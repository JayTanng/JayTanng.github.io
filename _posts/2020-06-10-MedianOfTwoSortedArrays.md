---
layout: default
title: "寻找两个正序数组的中位数"
tag: Leetcode
---

# <a name="top"></a>寻找两个正序数组的中位数

* [问题描述](#anchor1)
* [合并排序法](#anchor2)
* [双指针法](#anchor3)
* [二分法](#anchor4)

***

## <a name="anchor1"></a>问题描述

> 难度：<font color="red">困难</font>  
> 链接：[寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)
> 
> 给定两个大小为 m 和 n 的正序（从小到大）数组 ```nums1``` 和 ```nums2```。
> 请你找出这两个正序数组的中位数，并且要求算法的时间复杂度为 **O(log(m + n))**。
> 你可以假设 ```nums1``` 和 ```nums2``` 不会同时为空。

[[Top]](#top)

## <a name="anchor2"></a>合并排序法

加入该题没有要求时间复杂度为 **O(log(m + n))**，我们可以采取一下两种时间性能稍高的算法：

1. 将两个数组合并成一个数组，再用[快速排序](https://www.jianshu.com/p/a68f72278f8f)处理合并后数组，最后直接通过$ c = \sqrt{a^{2}+b_{xy}^{2}+e^{x}} $。
   * 时间复杂度**O((m+n)log(m+n))**
   * 空间复杂度**O(m+n)**
2. 与上面的方法相似，但是合并后不排序，而是通过找第K大数的方法找中位数。具体的题目见[数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)。
   * 时间复杂度**O(m+n)**
   * 空间复杂度**O(m+n)**

[[Top]](#top)

## <a name="anchor3"></a>双指针法



## <a name="anchor4"></a>二分法

``` java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
	if (nums2.length > nums1.length) {
		int[] temp = nums1;
		nums1 = nums2;
		nums2 = temp;
	}

		int m = nums1.length;
		int n = nums2.length;

		int totalLeft = m + (n - m + 1) / 2;

		int left = 0;
		int right = m;
		while (left < right) {
			int i = left + (right - left + 1) / 2;
			int j = totalLeft - i;
			if (nums1[i - 1] > nums2[j]) {
				right = i - 1;
			} else {
				left = i;
			}
	   }

		int i = left;
		int j = totalLeft - left;

		int nums1LeftMax = i == 0 ? Integer.MIN_VALUE : nums1[i - 1];
		int nums1RightMin = i == m ? Integer.MAX_VALUE : nums1[i];
		int nums2LeftMax = j == 0 ? Integer.MIN_VALUE : nums2[j - 1];
		int nums2RightMin = j == n ? Integer.MAX_VALUE : nums2[j];

		if ((m % 2 + n % 2) % 2 == 1) {
			return Math.max(nums1LeftMax, nums2LeftMax);
		} else {
			return (double)(Math.max(nums1LeftMax, nums2LeftMax) 
						 + Math.min(nums1RightMin, nums2RightMin)) / 2;
		}
	}
```

