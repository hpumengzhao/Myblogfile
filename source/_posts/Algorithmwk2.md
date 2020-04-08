---
title: Algorithm Weekly 2
tags: Probems
mathjax: true
---

## A slow week

<!---more--->

* **牛客练习赛60 C**

对于一个长度为$n$的字符串$s$,计算有多少个长度为$k$的本质不同的子序列。

**思路**
$dp_{i,j}$为前$i$个字符组成的字符串中长度为$j$的本质不同的子序列。则有:
$$dp_{i,j}=dp_{i-1,j-1}+dp_{i-1,j}-dp[last_{s_{i}}-1][j-1]$$
其中 $last_{s_{i}}$ 表示 $s_{i}$ 前一次出现的位置。时间复杂度$O(n \cdot k)$