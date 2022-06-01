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


## 5/5/2022 (need review again)
[1209. Remove All Adjacent Duplicates in String II](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string-ii/)

You are given a string s and an integer k, a k duplicate removal consists of choosing k adjacent and equal letters from s and removing them, causing the left and the right side of the deleted substring to concatenate together.

We repeatedly make k duplicate removals on s until we no longer can.

Return the final string after all such duplicate removals have been made. It is guaranteed that the answer is unique.

~~~js
/**
 * @param {string} s
 * @param {number} k
 * @return {string}
 */
var removeDuplicates = function(s, k) {
    let stack = [];
    
    for(let i=0; i<s.length; i+=1) {
        if (stack.length && stack[stack.length - 1][0] === s[i]) {
            stack[stack.length - 1][1] += 1;
            if (stack[stack.length - 1][1] >= k) {
                stack.pop();
            }
        } else {
            stack.push([s[i], 1]);
        }
    }
    
    let result = '';
    for(let i=0; i<stack.length; i+=1) {
        result += stack[i][0].repeat(stack[i][1]);
    }
    return result;
};
~~~
~~~js
/**
 * @param {string} s
 * @param {number} k
 * @return {string}
 */
var removeDuplicates = function(s, k) {
    if(s.length<k){
        return s;
    }
    
    let result = [];
    
    let start = 0;
    
    for(let end = 0; end<s.length;end++){
        while(s[start]!=s[end]){
            result.push(s[start]);
            start++;
        }
        if(end-start+1==k){
            start +=k;
        }
    }
    while(start<s.length){
        result.push(s[start]);
        start++;
    }
    return result.length == s.length? s : removeDuplicates(result.join(''), k);
};

~~~


## 5/11/2022 (need review again)

[121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

You are given an array prices where prices[i] is the price of a given stock on the ith day.

You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock.

Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return 0.


Selection Sort Style:
~~~js
/**
 * @param {number[]} prices
 * @return {number}
 */
var maxProfit = function(prices) {
    let profit = 0;
    
    for(let buy = 0; buy<prices.length; buy++){
        for(let sell = buy+1; sell<prices.length; sell++){
            profit = Math.max(profit, prices[sell]-prices[buy]);
        }
    }
    
    return profit;
};
~~~
~~~js
var maxProfit = function(prices) {
    let profit = 0;
    let hightest = 0;
    let getSell = function(startIndex) {
        if (startIndex < hightest) {
            return hightest;
        }
        let result = startIndex;
        for (let sell = startIndex + 1; sell < prices.length; sell++) {
            if (prices[sell] > prices[result]) {
                result = sell;
            }
        }
        return result;
    };
    let lowest = 0;
    for (let buy = 0; buy < prices.length; buy++) {
        if (prices[buy] <= prices[lowest]) {
            profit = Math.max(profit, prices[getSell(buy)] - prices[buy]);
            lowest = buy;
        }
    }
    return profit;
};
~~~
Insertion Sort Style:
~~~js
var maxProfit = function(prices) {
    var min = 0; 
    var profit = 0;
    for (var i = 0; i < prices.length; i++) {
        min = Math.min(min, prices[i]);
        profit = Math.max(profit, prices[i] - min);
    }
    return profit;
};
~~~
|Time Submitted|Status|Runtime|Memory|Language|
|--------------|------|-------|------|--------|
|05/04/2022 21:08|Time Limit Exceeded|N/A|N/A|javascript|
|05/11/2022 23:15|Accepted|95 ms|51.3 MB|javascript|


## 5/30/2022
[139. Word Break](https://leetcode.com/problems/word-break/)
Given a string s and a dictionary of strings wordDict, return true if s can be segmented into a space-separated sequence of one or more dictionary words.

Note that the same word in the dictionary may be reused multiple times in the segmentation.

~~~js
/**
 * @param {string} s
 * @param {string[]} wordDict
 * @return {boolean}
 */
var wordBreak = function(s, wordDict) {
    var cache ={};
    var found = function(start){
        if(cache[start]!=undefined){
            return cache[start];
        }
        for(var w of wordDict){
            var l = w.length;
            var sub = s.substring(start, start+l);
            if(sub == w){
                if(start+l==s.length){
                    cache[start]= true;
                    return true;
                }else if(start+l<s.length && found(start+l)){
                    cache[start]= true;
                    return true;
                }
            }
        }
        cache[start]= false;
        return false;
    };
    return found(0);
};


// y = f(x), x stands for the relationship bewteen state and choices[i].

/**
 * @param {string} s
 * @param {string[]} wordDict
 * @return {boolean}
 */
var wordBreak = function(s, wordDict) {
    //wordDict.sort(function(a,b){return a.length-b.length;});
    var cache ={};
    var found = function(start){
        if(start == s.length){
            return true;
        }
        if(cache[start]!=undefined){
            return cache[start];
        }
        for(var w of wordDict){
            var l = w.length;
            var sub = s.substring(start, start+l);
            if(sub == w && found(start+l)){
                cache[start]= true;
                return true;
            }
        }
        cache[start]= false;
        return false;
    };
    return found(0);
};
~~~
## 5/31/2022
[162. Find Peak Element](https://leetcode.com/problems/find-peak-element/)
A peak element is an element that is strictly greater than its neighbors.

Given an integer array nums, find a peak element, and return its index. If the array contains multiple peaks, return the index to any of the peaks.

You may imagine that nums[-1] = nums[n] = -∞.

You must write an algorithm that runs in O(log n) time.

~~~js
//convert to find a Max in subarray
/**
 * @param {number[]} nums
 * @return {number}
 */
var findPeakElement = function(nums) {
    var l = 0,
        r = nums.length;
    while(l<r){
        var m = l + Math.floor((r-l)/2);
        if(nums[m]<nums[m+1]){
            l = m+1;
        }else{
            r = m;
        }
    }
    return l;
};

~~~
