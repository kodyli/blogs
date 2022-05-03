# Sliding Window
-----------------

- [1. Maximum Average Subarray I](#section-1)
- [2. Smallest Subarray with a given sum (easy)](#section-2)
- [3. Longest Substring with K Distinct Characters (medium)](#section-3)
- [4. Longest Substring Without Repeating Characters (medium)](#section-4)

~~~js
let start = 0;
let shrink = true;

for(let end = 0; end < size; end++ ){
    //Before shrinking the window
    shrink = true/false;
    //Shrinking the window
    while(shrink){
        shrink = true/false;
        start++;
    }
    //After shrinking the window.
    ...........
}
~~~

> {info} **Sliding Window v.s. Two Pointer**

> Sliding window algorithms can be implemented with a single pointer and a variable for window size. Typically we use all of the elements within the window for the problem (for eg - sum of all elements in the window).

> Two pointer technique is quite similar but we usually compare the value at the two pointers instead of all the elements between the pointers.
> Two pointers can also have variations like fast-slow pointer.

<a name="section-1"></a>
## [1. Maximum Average Subarray I](https://leetcode.com/problems/maximum-average-subarray-i/)
You are given an integer array nums consisting of n elements, and an integer k.

Find a contiguous subarray whose length is equal to k that has the maximum average value and return this value. Any answer with a calculation error less than 10-5 will be accepted.
~~~js
function findMaxAverage (k, numbers) {
    let max = Number.NEGATIVE_INFINITY;
    let start = 0,
         sum = 0.0;
    for(let i =0, l = numbers.length; i < l; i++){
        //Before shrinking the window
        sum += numbers[i];
                                                  
        /**
        * Do not need to shrink the window.
        */
                                                  
        //After shrinking the window
        if(i >= k - 1){
            max = Math.max(max, sum/k);
            sum -= numbers[start];
            start +=1;
        }
    }
    return max;
}
~~~

<a name="section-2"></a>
## 2. Smallest Subarray with a given sum (easy)
Given an array of positive numbers and a positive number 'S', find the length of the smallest contiguous subarray whose sum is greater than or equal to 'S'. Return 0, if no such subarray exists.
~~~js
function smallestSubarrayWithGivenSum(s, numbers){
    let minLength = Infinity,
        sum = 0,//shrink flag
        start = 0;
    for (let end = 0, l = numbers.length; end < l; end++) {
        //Before shrinking the window
        sum += numbers[end];
                                                           
        /**
        * Shrink the window as small as possible 
        * until the sum is smaller than 's'.
        * So using 'while' statement.
        */
        //Shrinking the window                                                   
        while (sum >= s) {
            sum -= numbers[start];
            start += 1;
        }
                                                           
        //After shrinking the window
        minLength = Math.min(minLength, end - start + 1);
    }
    return Number.isFinite(minLength) ? minLength : 0;
}
~~~

<a name="section-3"></a>
## 3. Longest Substring with K Distinct Characters (medium)
Given a string, find the length of the longest substring in it with no more than K distinct characters.
~~~js
function longestSubstringWithKDistinct(text, k){
    let maxLength = Number.NEGATIVE_INFINITY,
        start = 0,
        charFrequency = {};//shrink flag

    for (let i = 0, l = text.length; i < l; i++) {
        //Before shrinking the window
        const rightChar = text.charAt(i);
        if (charFrequency[rightChar] === undefined) {
            charFrequency[rightChar] = 0;
        }
        charFrequency[rightChar] += 1;
                                                  
        //Shrinking the window
        //Shrink the sliding window, until we are left 
        //with 'k' distinct characters in the charFrequency
        while (Object.keys(charFrequency).length > k) {
            const leftChar = text.charAt(start);
            charFrequency[leftChar] -= 1;
            if (charFrequency[leftChar] === 0) {
                delete charFrequency[leftChar];
            }
            start += 1; // Shrink the window
        }
                                                  
        //After shrinking the window
        maxLength = Math.max(maxLength, i - start + 1);
    }

    return maxLength;
}
~~~
<a name="section-4"></a>
## [4. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
Given a string s, find the length of the longest substring without repeating characters.
~~~js
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function(s) {
    let start = 0,
        positions ={};//shrink flag
    let max = 0;
    
    for(let end = 0; end < s.length; end++){
        let c = s[end];
        //If there is a repeating character in [start, end),
        //shrink the window.
        if(positions[c] != undefined && positions[c] >= start){
            start = positions[c]+1;
        }
        max = Math.max(max, end - start+1);
        positions[c] = end;
    }
    
    return max;
};
~~~