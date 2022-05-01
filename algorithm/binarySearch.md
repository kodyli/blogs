# Binary Search
---------------
- [1. 基本二分搜索](#section-1)
- [2. 左侧边界二分搜索](#section-2)
- [3. 右侧边界二分搜索](#section-3)

<a name="section-1"></a>
#### 1. 基本二分搜索

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
    //搜索区间为[left, right]
    while(left<=right){
        //有效防止了 left 和 right 太大，直接相加导致溢出的情况。
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

有序数组 nums = [1,2,2,2,3]，target 为 2，此算法返回的索引是2,如果要左侧边界呢？

<a name="section-2"></a>
#### 2. [左侧边界二分搜索](https://labuladong.github.io/algo/1/10/#二寻找左侧边界的二分搜索)
当 nums[mid] == target 时不要立即返回而要收紧右侧边界
~~~js
var leftBound = function(nums, target){
    var left = 0,
        right = nums.length-1;
    var mid;
   //搜索区间为[left, right]
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

同样的数据，如果需要右侧边界呢？
<a name="section-3"></a>
#### 3. [右侧边界二分搜索](https://labuladong.github.io/algo/1/10/#三寻找右侧边界的二分查找)
当 nums[mid] == target 时不要立即返回而要收紧左侧边界
~~~js
var rightBound = function(nums, target){
    var left = 0,
        right = nums.length-1;
    var mid;
   //搜索区间为[left, right]
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