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