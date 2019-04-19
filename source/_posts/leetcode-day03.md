---
title: leetCode-day03
date: 2019-04-19 20:30:21
tags: array
categories: leetcode
---

### 1 数组 双指针 问题
给定 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。
//
// 说明：你不能倾斜容器，且 n 的值至少为 2。
图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
//
//
//
// 示例:
//
// 输入: [1,8,6,2,5,4,8,3,7]
//输出: 49

<!--more-->

```
我的常规解法： 循环遍历数组， 计算最大值：耗时207ms
 public int maxArea(int[] height) {
        int maxArea = 0;
        int tempArea = 0;

        for (int i = 0; i < height.length; i++) {
            for (int j = i+1; j < height.length; j++) {
                tempArea = (j-i)*Math.min(height[i], height[j]);
                if(tempArea > maxArea){
                    maxArea = tempArea;
                }
            }
        }
        return maxArea;
    }

```

```
大牛的解法：3ms
利用数组的首尾两个指针
public int maxArea(int[] height) {
    //首尾两个指针
    int L = height.length, lo = 0, hi = L-1;
    int max = 0;
    while(lo<hi) {	//外部循环，条件是首指针的值，小于尾指针的值  
        int loMax = height[lo], hiMax = height[hi];      

    	int candidate = (hi-lo) * (loMax<hiMax ? loMax : hiMax);
    	max = candidate > max ? candidate : max;

    	if(height[lo]<=height[hi]) //判断条件 哪边的值小，哪边的指针移动
    	    while(lo<hi && height[lo]<=loMax) ++lo; //内部循环，条件依然还是首指针的值小于尾指针，同时，当前值小于记录的最大值，则指针移动
    	else 
    	    while(hi>lo && height[hi]<=hiMax) --hi;
    }
    return max;
}

```

### 2 数组中 三个数之和的问题 暂时求解不出来

```
看看大神们的解法
//给定一个包括 n 个整数的数组 nums 和 一个目标值 target。找出 nums 中的三个整数，使得它们的和与 target 最接近。返回这三个数的和。假定每组输入只存在唯一答案。
//
// 例如，给定数组 nums = [-1，2，1，-4], 和 target = 1.
//
//与 target 最接近的三个数的和为 2. (-1 + 2 + 1 = 2).
//
//

public int threeSumClosest(int[] nums, int target) {
    Arrays.sort(nums);
    int len = nums.length;
    if(nums[len-3]+nums[len-2]+nums[len-1]<=target) return nums[len-3]+nums[len-2]+nums[len-1];//short  cut
    if(nums[0] + nums[1] + nums[2]>=target) return nums[0] + nums[1] + nums[2];//short cut
    int closest = nums[0] + nums[1] + nums[len - 1];
    for(int s = 0;s<len - 2;s++){
        if(s!=0&&nums[s-1]==nums[s]) continue;
        if(nums[s]+nums[len-1]+nums[len-2]<target&&s<len-3){//short cut
            closest = Math.abs(closest-target)>target - (nums[s]+nums[len-1]+nums[len-2])?nums[s]+nums[len-1]+nums[len-2]:closest;
            continue;
        }
        if(nums[s]+nums[s+1]+nums[s+2]>target){//short cut
            closest = Math.abs(closest-target)>(nums[s]+nums[s+1]+nums[s+2])-target?nums[s]+nums[s+1]+nums[s+2]:closest;
            break;
        }
        int newTarget = target - nums[s];
        int m = s+1;
        int b = len - 1;
        while(m<b){
            while(m!=s+1&&nums[m-1]==nums[m]&&m+1<b)m++; //skip duplicates
            while(b!=len-1&&nums[b+1]==nums[b]&&b-1>m)b--; //skip duplicates
            if(nums[m]+nums[b]<newTarget){
                closest = Math.abs(closest-target)>newTarget - nums[m] - nums[b]?nums[s]+nums[m]+nums[b]:closest;
                m++;
            }
            else if(nums[m]+nums[b]>newTarget){
                closest = Math.abs(closest-target)>nums[m] + nums[b] - newTarget?nums[s]+nums[m]+nums[b]:closest;
                b--;
            }
            else{        
                return target;
            }
            
        }
    }
    return closest;
}


```