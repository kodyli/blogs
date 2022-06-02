# Concurrency
----------------
[Non-blocking Algorithms](https://jenkov.com/tutorials/java-concurrency/non-blocking-algorithms.html): an algorithm is said to be non-blocking if the suspension of one thread cannot lead to the suspension of other threads involved in the algorithm.

###### Blocking algorithms:
![Blocking algorithms](https://jenkov.com/images/java-concurrency/non-blocking-algorithms-1.png)

###### Non-blocking algorithms:
![Non-blocking algorithms](https://jenkov.com/images/java-concurrency/non-blocking-algorithms-2.png)

Blocking algorithms block the thread until the requested action can be performed. Non-blocking algorithms notify the thread requesting the action that the action cannot be performed.

