# Daily Challange
-----------------

## 5/1/2022
[844. Backspace String Compare](https://leetcode.com/problems/backspace-string-compare/)

Given two strings s and t, return true if they are equal when both are typed into empty text editors. '#' means a backspace character.

Note that after backspacing an empty text, the text will continue empty.

~~~js
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
var backspaceCompare = function(s, t) {
    if(!s && !t){return true;}
    let cache = [];
    for(let i = 0; i<s.length; i++){
        let c = s[i];
        if(c == '#'){
            cache.pop();
        }else{
            cache.push(c);
        }
    }
    let result1 = cache.join('');
    cache = [];
    for(let i = 0; i<t.length; i++){
        let c = t[i];
        if(c == '#'){
            cache.pop();
        }else{
            cache.push(c);
        }
    }
    
    return result1 == cache.join('');
};
~~~
|Time Submitted|Status|Runtime|Memory|Language|
|--------------|------|-------|------|--------|
|05/01/2022 19:46|	Accepted|	81 ms|	42.8 MB|	javascript|

## 5/2/2022
[905. Sort Array By Parity](https://leetcode.com/problems/sort-array-by-parity/)

Given an integer array nums, move all the even integers at the beginning of the array followed by all the odd integers.

Return any array that satisfies this condition.
~~~js
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var sortArrayByParity = function(nums) {
    if(!nums){return nums}
    let left = 0,
        right = nums.length -1;
    
    while(left<=right){
        if(nums[left]%2 == 1 && nums[right]%2==0){
            [nums[left], nums[right]] = [nums[right], nums[left]];
            left++;
            right--;
        }
        if(nums[left]%2 == 0){
            left++;
        }
        
        if(nums[right]%2==1){
            right--;
        }
    }
    
    return nums;
};
~~~
|Time Submitted|Status|Runtime|Memory|Language|
|--------------|------|-------|------|--------|
|05/01/2022 19:46|	Accepted|	177 ms|	45 MB|	javascript|

## 5/3/2022
[581. Shortest Unsorted Continuous Subarray](https://leetcode.com/problems/shortest-unsorted-continuous-subarray/)

Given an integer array nums, you need to find one continuous subarray that if you only sort this subarray in ascending order, then the whole array will be sorted in ascending order.

Return the shortest such subarray and output its length.
~~~js
/**
 * @param {number[]} nums
 * @return {number}
 */
var findUnsortedSubarray = function(nums) {
    let start = nums.length,
        end = -1;
    for(let i = 0; i< nums.length; i++){
        let min = i;
        for(let j = i+1; j<nums.length;j++){
            if(nums[j]<nums[i]){
                min = j;
            }
        }
        
        if(min!=i){
            [nums[i], nums[min]] = [nums[min], nums[i]];
            start = Math.min(start, i);
            end = Math.max(end, min);
        }
    }
    
    return start == nums.length && end == -1? 0:end - start +1;
};
~~~
|Time Submitted|Status|Runtime|Memory|Language|
|--------------|------|-------|------|--------|
|05/02/2022 20:50|	Accepted|	1448 ms|	45 MB|	javascript|
~~~js
/**
 * @param {number[]} nums
 * @return {number}
 */
var findUnsortedSubarray = function(nums) {
    //1. Sort a copy fo the given array nums
    let cache = nums.slice(0, nums.length);
    cache.sort((a,b)=>a-b);
    
    let start = nums.length,
        end = -1;
    //2. Find the leftmost and rightmost elements which mismath
    for(let i = 0; i<nums.length;i++){
        if(nums[i]!=cache[i]){
            start = Math.min(start, i);
            end = Math.max(end, i);
        }
    }
    return start == nums.length && end == -1?0:end-start+1;
};
~~~
|Time Submitted|Status|Runtime|Memory|Language|
|--------------|------|-------|------|--------|
|05/02/2022 21:27|	Accepted|	125 ms|	46.8 MB|	javascript|

## 5/4/2022
[1679. Max Number of K-Sum Pairs](https://leetcode.com/problems/max-number-of-k-sum-pairs/)

You are given an integer array nums and an integer k.

In one operation, you can pick two numbers from the array whose sum equals k and remove them from the array.

Return the maximum number of operations you can perform on the array.
~~~js
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number}
 */
var maxOperations = function(nums, k) {
    nums.sort((a,b)=>a-b);
    let start = 0,
        end = nums.length -1;
    let count = 0,
        sum = 0;
    
    while(start < end){
        sum = nums[start] + nums[end];
        if(sum < k){
            start++;
        }else if(sum > k){
            end--;
        }else{
            count++;
            start++;
            end--;
        }
    }
    return count;
};
~~~
|Time Submitted|Status|Runtime|Memory|Language|
|--------------|------|-------|------|--------|
|05/03/2022 21:45|	Accepted|	258 ms|	53.4 MB|	javascript|
~~~js
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number}
 */
var maxOperations = function(nums, k) {
    let cache = {},
        remaining = 0;
    
    let count = 0;
    for(let i = 0; i < nums.length; i++){
        remaining = k - nums[i];
        if(cache[remaining]){
            count++;
            cache[remaining]--;
            if(!cache[remaining]){
               delete cache[remaining];
           }
        }else{
            cache[nums[i]] = cache[nums[i]] || 0;
            cache[nums[i]]++;
        }
    }
    
    return count;
};
~~~
|Time Submitted|Status|Runtime|Memory|Language|
|--------------|------|-------|------|--------|
|05/03/2022 21:45|	Accepted|	342 ms|	59.1 MB|	javascript|

## 5/4/2022
[225. Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues/)

Implement a last-in-first-out (LIFO) stack using only two queues. The implemented stack should support all the functions of a normal stack (push, top, pop, and empty).

[Queue](https://github.com/datastructures-js/queue)
~~~js

var MyStack = function() {
    this.queue = new Queue();
    this.cache = new Queue();
};

/** 
 * @param {number} x
 * @return {void}
 */
MyStack.prototype.push = function(x) {
    this.queue.enqueue(x);
};

/**
 * @return {number}
 */
MyStack.prototype.pop = function() {
    if(this.queue.isEmpty()){
        return undefined;
    }
    while(this.queue.size()>1){
        this.cache.enqueue(this.queue.dequeue());
    }
    let last = this.queue.dequeue();
    let temp = this.queue;
    this.queue = this.cache;
    this.cache = temp;
    return last;
};

/**
 * @return {number}
 */
MyStack.prototype.top = function() {
    return this.queue.back();
};


/**
 * @return {boolean}
 */
MyStack.prototype.empty = function() {
    return this.queue.isEmpty();
};

/** 
 * Your MyStack object will be instantiated and called as such:
 * var obj = new MyStack()
 * obj.push(x)
 * var param_2 = obj.pop()
 * var param_3 = obj.top()
 * var param_4 = obj.empty()
 */
~~~
|Time Submitted|Status|Runtime|Memory|Language|
|--------------|------|-------|------|--------|
|05/04/2022 21:08	|	Accepted|	78 ms|	41.9 MB|	javascript|