---
layout: default
title: "盛最多水的容器"
tag: Leetcode
---

# <a name="top"></a>盛最多水的容器

* [问题描述](#anchor1)
* [双指针法](#anchor2)

***

## <a name="anchor1"></a>问题描述

> 难度：<font color="red">中等</font>  
> 链接：[盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)   
> 描述：给你$ n $个非负整数$ a_{1}，a_{2}，...，a_{n} $，每个数代表坐标中的一个点$ (i, a_{i}) $ 。在坐标内画$ n $条垂直线，垂直线$ i $的两个端点分别为$ (i, a_{i}) $和$ (i, 0) $。找出其中的两条线，使得它们与$ x $轴共同构成的容器可以容纳最多的水。     
> **说明：**你不能倾斜容器，且 n 的值至少为 2。    
> <div align="center"><img src="../assets/img/diagram/20200615_example.jpg" alt="示例1" style="width:50%; height:50%;" /></div>   
> <div class="img_title">图中垂直线代表输入数组$ [1,8,6,2,5,4,8,3,7] $。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。</div>    

[[Top]](#top)

## <a name="anchor2"></a>双指针法

我们使用一条分割线把两个数组分别分割成两部分：   
1. 红线左边和右边的元素个数相等，或者左边元素的个数比右边元素的个数多1个；
2. 红线左边的所有元素的数组<=红线右边的所有元素的数值；  
那么中位数就一定只与红线两侧的元素有关。

<br>
算法思想为：
> 定义两个指针<span class="inline_code_block">nums1Index</span>，<span class="inline_code_block">nums2Index</span>；    
> 定义两个变量<span class="inline_code_block">leftVal</span>，<span class="inline_code_block">rightVal</span>用来表示中位数。如果数组为<span class="inline_code_block">[1,2][3]</span>，中位数就是<span class="inline_code_block">rightVal</span>，如果数组为<span class="inline_code_block">[1,3][2,4]</span>，则<span class="inline_code_block">leftVal=2、rightVal=3</span>     
> 循环$ totalLeft = \frac{nums1.length + nums2.length}{2} + 1 $次，每次比较<span class="inline_code_block">nums1[nums1Index]</span>和<span class="inline_code_block">nums2[nums2Index]</span>，如果小于则<span class="inline_code_block">nums1Index+1</span>否则<span class="inline_code_block">nums2Index+1</span>     
> 同时改变<span class="inline_code_block">leftVal</span>和<span class="inline_code_block">rightVal</span>    
> 最后根据奇偶的情况输入相应值

算法性能如下：
* 时间复杂度为**O(n)**
* 空间复杂度为**O(1)**    

算法代码如下：

``` java   
public int maxArea(int[] height) {
	int left = 0;
	int right = height.length - 1;
	int maxArea = 0;
	while (left < right) {
		int curArea = Math.min(height[left], height[right]) * (right - left);
		maxArea = maxArea > curArea ? maxArea : curArea;
		if (height[left] < height[right]) {
			left++;
		} else {
			right--;
		}
	}
	return maxArea;
}
```   
[[Top]](#top) 