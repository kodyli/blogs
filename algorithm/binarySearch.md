# Binary Search
---------------
- [1. Basic](#section-1)
- [2. Left Most](#section-2)
- [3. Right Most](#section-3)

<a name="section-1"></a>
#### 1. Basic

[704. Binary Search](https://leetcode.com/problems/binary-search/)

Given an array of integers nums which is sorted in ascending order, and an integer target, write a function to search target in nums. If target exists, then return its index. Otherwise, return -1.

You must write an algorithm with O(log n) runtime complexity.

~~~js

/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var search = function(nums, target) {
    var left = 0,
        right = nums.length -1;
    var mid;
    //Search area [left, right]
    while(left<=right){
        //Do not use (left+right)/2, because left+right
        //may be greater than the largest integer
        mid = Math.floor(left + (right - left)*0.5);
        if(nums[mid] > target){
            right = mid -1;
        }else if(nums[mid]<target){
            left = mid +1;
        }else{
            return mid;
        }
    }
    return -1;
};

~~~

<a name="section-2"></a>
If the array of integers is [1,2,2,2,3]，target is 2，the Basic will return 2, how about 1 instead of 2？

#### 2. [Left Most](https://labuladong.github.io/algo/1/10/#二寻找左侧边界的二分搜索)
When nums[mid] == target, don't return mid, narrow the right index.
~~~js
var leftBound = function(nums, target){
    var left = 0,
        right = nums.length-1;
    var mid;
    //Search area [left, right]
    while(left <= right){
        mid = Math.floor(left+(right-left)*0.5);
        if(nums[mid]< target){
            left = mid + 1;
        }else if(nums[mid]> target){
            right = mid-1;
        }else{
            right = mid-1;
        }
    }
    return left<nums.length&&nums[left]==target?left:-1;
};
~~~

<a name="section-3"></a>
If the array of integers is [1,2,2,2,3]，target is 2，the Basic will return 2, how about 3 instead of 2？

#### 3. [Right Most](https://labuladong.github.io/algo/1/10/#三寻找右侧边界的二分查找)
When nums[mid] == target, don't return mid, narrow the left index.
~~~js
var rightBound = function(nums, target){
    var left = 0,
        right = nums.length-1;
    var mid;
    //Search area [left, right]
    while(left <= right){
        mid = Math.floor(left+(right-left)*0.5);
        if(nums[mid]< target){
            left = mid + 1;
        }else if(nums[mid]> target){
            right = mid - 1;
        }else{
            left = mid+1;
        }
    }
    return right>-1&&nums[right]==target?right:-1;
};
~~~