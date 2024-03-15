# [Ring Buffer](https://www.baeldung.com/java-ring-buffer)

Ring Buffer (or Circular Buffer) is a bounded circular data structure that is used for buffering data between two or more threads. As we keep writing to a ring buffer, it wraps around as it reaches the end.

~~~java
public class CircularBuffer<E>{
    private static final int DEFAULT_CAPACITY = 8;
    
    private final int capacity;
    private final E[] data;
    private int writeSequence, readSequence;
    
    public CircularBuffer(int capacity){
        this.capacity = capacity<1?DEFAULT_CAPACITY:capacity;
        this.data = (E()) new Object[this.capacity];
        this.readSequence = 0;
        this.writeSequence = -1;
    }
    
    public boolean offer(E element){
        if(!isFull()){
            int nextWriteSeq = writeSequence + 1;
            data[nextWriteSeq % capacity] = element;
            writeSequence = nextWriteSeq;
            //data[++nextWriteSeq % capacity] = element;
            return true;
        }
        return false;
    }
    
    public E poll(){
        if(!isEmpty()){
            E nextValue = data[readSequence % capacity];
            readSequence++;
            return nextValue;
            //return data[readSequence++ % capacity];
        }
        return null;
    }
    
    public int capacity(){
        return capacity;
    }
    
    public int size(){
        return writeSequence - readSequence +1;
    }
    
    public boolean isEmpty(){
        return writeSequence < readSequence
    }
    
    public boolean isFull(){
        return size() >= capacity();
    }
}
~~~

Implementation for single producer and consumer
~~~java
public class CircularBuffer<E>{
    private static final int DEFAULT_CAPACITY = 8;
    
    private final int capacity;
    private final E[] data;
    private volatile int writeSequence, readSequence;
    
    public CircularBuffer(int capacity){
        this.capacity = capacity<1?DEFAULT_CAPACITY:capacity;
        this.data = (E()) new Object[this.capacity];
        this.readSequence = 0;
        this.writeSequence = -1;
    }
    
    public boolean offer(E element){
        if(!isFull()){
            int nextWriteSeq = writeSequence + 1;
            data[nextWriteSeq % capacity] = element;
            writeSequence = nextWriteSeq;
            //data[++nextWriteSeq % capacity] = element;
            return true;
        }
        return false;
    }
    
    public E poll(){
        if(!isEmpty()){
            E nextValue = data[readSequence % capacity];
            readSequence++;
            return nextValue;
            //return data[readSequence++ % capacity];
        }
        return null;
    }
    
    public int capacity(){
        return capacity;
    }
    
    public int size(){
        return writeSequence - readSequence +1;
    }
    
    public boolean isEmpty(){
        return writeSequence < readSequence
    }
    
    public boolean isFull(){
        return size() >= capacity();
    }
}
~~~

Implementation for multiple producers and consumers

1. Synchronized
~~~java
class CircularBuffer<T> {
    private final int capacity;
    private final Object[] buffer;
    private int size = 0;
    private int writeIndex = 0;
    private int readIndex = 0;

    public CircularBuffer(int capacity) {
        this.capacity = capacity;
        this.buffer = new Object[capacity];
    }

    public synchronized void put(T item) throws InterruptedException {
        while (size == capacity) {
            wait();
        }
        buffer[writeIndex] = item;
        writeIndex = (writeIndex + 1) % capacity;
        size++;
        notifyAll();
    }

    public synchronized T get() throws InterruptedException {
        while (size == 0) {
            wait();
        }
        T item = (T) buffer[readIndex];
        readIndex = (readIndex + 1) % capacity;
        size--;
        notifyAll();
        return item;
    }
}
~~~

2. ReentrantLock

~~~java
//import java.util.concurrent.locks.*;

class CircularBuffer<T> {
    private final int capacity;
    private final Object[] buffer;
    private int size = 0;
    private int writeIndex = 0;
    private int readIndex = 0;
                         
    private final Lock lock = new ReentrantLock();
    private final Condition notFull = lock.newCondition();
    private final Condition notEmpty = lock.newCondition();

    public CircularBuffer(int capacity) {
        this.capacity = capacity;
        this.buffer = new Object[capacity];
    }

    public void put(T item) throws InterruptedException {
        lock.lock();
        try {
            while (size == capacity) {
                notFull.await();
            }
            buffer[writeIndex] = item;
            writeIndex = (writeIndex + 1) % capacity;
            size++;
            notEmpty.signal();
        } finally {
            lock.unlock();
        }
    }

    public T get() throws InterruptedException {
        lock.lock();
        try {
            while (size == 0) {
                notEmpty.await();
            }
            T item = (T) buffer[readIndex];
            readIndex = (readIndex + 1) % capacity;
            size--;
            notFull.signal();
            return item;
        } finally {
            lock.unlock();
        }
    }
}
~~~

3. Lock-free CircularBuffer

~~~java
import java.util.concurrent.atomic.AtomicIntegerArray;

class CircularBuffer<T> {
    private final int capacity;
    private final T[] buffer;
    private final AtomicIntegerArray counter;
    private int writeIndex = 0;
    private int readIndex = 0;

    public CircularBuffer(int capacity) {
        this.capacity = capacity;
        this.buffer = (T[]) new Object[capacity];
        this.counter = new AtomicIntegerArray(capacity);
    }

    public boolean put(T item) {
        int index;
        do {
            index = writeIndex;
            if (counter.get(index) == 0) {
                buffer[index] = item;
                writeIndex = (index + 1) % capacity;
                counter.lazySet(index, 1);
                return true;
            }
        } while (true);
    }

    public T get() {
        int index;
        do {
            index = readIndex;
            if (counter.get(index) == 1) {
                T item = buffer[index];
                readIndex = (index + 1) % capacity;
                counter.lazySet(index, 0);
                return item;
            }
        } while (true);
    }
}


~~~