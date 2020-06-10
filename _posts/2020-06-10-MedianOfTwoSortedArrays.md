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
> 给定两个大小为 m 和 n 的正序（从小到大）数组<span class="inline_code_block">nums1</span>和<span class="inline_code_block">nums2</span>。
> 请你找出这两个正序数组的中位数，并且要求算法的时间复杂度为 **O(log(m + n))**。
> 你可以假设<span class="inline_code_block">nums1</span>和<span class="inline_code_block">nums2</span>不会同时为空。

[[Top]](#top)

## <a name="anchor2"></a>合并排序法

加入该题没有要求时间复杂度为 **O(log(m + n))**，我们可以采取一下两种时间性能稍高的算法：  

1. 将两个数组合并成一个数组，再用[快速排序](https://www.jianshu.com/p/a68f72278f8f)处理合并后数组，最后直接求中位数。
   * 时间复杂度为**O((m+n)log(m+n))**
   * 空间复杂度为**O(m+n)**
2. 与上面的方法相似，但是合并后不排序，而是通过找第K大数的方法找中位数。具体的题目见[数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)。
   * 时间复杂度为**O(m+n)**
   * 空间复杂度为**O(m+n)**

[[Top]](#top)

## <a name="anchor3"></a>双指针法

每个指针相当于一条分割线，双指针一共将两个数组分层4部分。比如，    

| nums1 | nums2 |
| :---- | :---- |
| 1 2 3 | 7 8 9 |
| 4 5 6 | 10 11 12 |    

元素总个数可以为奇数也可以为偶数，关于这点算法中做了两个约定：  
* 指针所指的数组下标表示分割线右边第一个数，所以当总数为奇数时直接取分割线左边的最大值，当总数为偶数时需要求左边最大值和右边最小值的平均值。
* 中位数左边的总元素$ totalLeft = \frac{nums1.length + nums2.length + 1}{2} $     

算法性能如下：
* 时间复杂度为**O(m+n)**
* 空间复杂度为**O(m+n)**    

算法代码如下：

``` java
	public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        double res = 0.0;
        /* 交换数组，确保nums1指向短数组 */
        if (nums1.length > nums2.length) {
            int[] temp = nums1;
            nums1 = nums2;
            nums2 = temp;
        }
        if (nums1.length == 0) {
            int len = nums2.length;
            res = len % 2 == 0 ? (double) (nums2[len / 2] + nums2[len / 2 - 1]) / 2 : nums2[len / 2];
            return res;
        }
        int len1 = nums1.length;
        int len2 = nums2.length;
        /**
         * i，j分别表示nums1和nums2的指针
         */
        int i = 0;
        int j = 0;
        /* totalLeft表示两个数组合起来后，中位数左边的元素个数 */
        int totalLeft = len1 + (len2 - len1 + 1) / 2;
        /**
         * 算法核心部分
         * 初始时，用i，j两个指针分别指向nums1和nums2的0号位置
         * 一共循环totalLeft次，每次循环时比较nums1[i]和nums2[j]的值，当<时，说明该数排在合并后数组的前置位置所以i++
         * 需要注意i有可能会移到nums1的最后一位，所以需要判断i<nums1.length，当不符合条件则需要将j向右移动
         * 显然，当时nums1[i]>nums2[j],j也需要向右移动
         *
         * 又因为按照我的写法i，j都有可能等于数组长度所以需要每次数组取值时需要判断是否越界，越界了就下标减一
         */
        for (; totalLeft > 0; totalLeft--) {
            if (nums1[i == len1 ? i - 1 : i] < nums2[j == len2 ? j - 1 : j]
                    && i < len1) {
                i++;
            } else if (j < len2) {
                j++;
            }
        }

        /**
         * 分别求两个数组分割线左边的最大值和右边的最小值
         *
         * 当分割线在下标为0的位置时，令最大值为Integer.MIN_VALUE，因为在求totalLeft的时候需要比较两个数组中左边区间的最大值，
         * 而0位置的左边没有值，所以要定义整数最小值来确保一定能取另一个数组中分割线左边的值
         *
         * 当分割线在下标为数组长度的位置时，令最小值为Integer.MAX_VALUE，因为在求totalRight的时候需要比较两个数组中右边区间的最小值
         * 数组长度位置是不能取的，为了确保取到另一个数组中分割线右边的值，所以定义为整数最大值
         */
        int nums1LeftMax = i == 0 ? Integer.MIN_VALUE : nums1[i - 1];
        int nums1RightMin = i == len1 ? Integer.MAX_VALUE : nums1[i];
        int nums2LeftMax = j == 0 ? Integer.MIN_VALUE : nums2[j - 1];
        int nums2RightMin = j == len2 ? Integer.MAX_VALUE : nums2[j];

        /**
         * 按照约定，指针所指的数组下标表示分割线右边第一个数，
         * 所以当总数为奇数时直接取分割线左边的最大值
         * 例如nums1=[1],nums2=[2,3]，最终指针分别指向1，1，所以中位数等于Math.max(nums1LeftMax, nums2LeftMax)=2
         * 当总数为偶数时需要求左边最大值和右边最小值的平均值
         */
        if ((len1 % 2 + len2 % 2) % 2 == 1) {
            res = Math.max(nums1LeftMax, nums2LeftMax);
        } else {
            res =  (double)(Math.max(nums1LeftMax, nums2LeftMax) + Math.min(nums1RightMin, nums2RightMin)) / 2;
        }
        return res;
    }
```   
这里想跟大家分享一道双指针法经典问题[Leetcode88.合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)    
[[Top]](#top)

## <a name="anchor4"></a>二分法
该方法大体思路和上面的二分法相似，但是划分数组时对单个数组使用了二分法，先确定一个数组中left的元素个数，
再用totalLeft减去left得到另一个数组的left个数。因为二分的缘故所以时间复杂度降低到了**O(log(m+n))**

``` java
	public double findMedianSortedArrays(int[] nums1, int[] nums2) {
		/* sdfsadfs */
		if (nums1.length > nums2.length) {
			int[] temp = nums1;
			nums1 = nums2;
			nums2 = temp;
		}
		
		int m = nums1.length;
		int n = nums2.length;

		/**
		 * 因为除法是向下取整的，所以无论m+n是奇数还是偶数，中位数都可以用(m+n+1)/2表示
		 * m + (n - m + 1) / 2是防止m，n相加大于2^16-1
		 */
		int totalLeft = m + (n - m + 1) / 2;

		/**
		 * 先确定nums1的分割线位置，nums2的分割线位置可以由totalLeft-i得到
		 * 分割线需要保证nums1[i - 1] <= nums2[j] && nums1[i] >= nums[j - 1]
		 */
		int left = 0;
		int right = m;
		while (left < right) {
			/* +1是为了防止[0,1]这样的数组在二分时进入死循环，需要注意这样写会导致到不了数组的0号位置 */
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

		/* 防止相加溢出 */
		if ((m % 2 + n % 2) % 2 == 1) {
			return Math.max(nums1LeftMax, nums2LeftMax);
		} else {
			return (double)(Math.max(nums1LeftMax, nums2LeftMax) 
							+ Math.min(nums1RightMin, nums2RightMin)) / 2;
		}
	}
```
[[Top]](#top)
