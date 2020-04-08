---
title: 计蒜之道初赛1B
tags: DP
mathjax: true
---
# 商汤AI园区的n个路口（DP）
<!--more-->
## [题面](https://nanti.jisuanke.com/t/39261)

题意：给你一个长度为$n$的链，让你为每个点分配一个点权使得相邻两个点之间的$gcd$不等于边权，求方案数。要求是点权的范围是$[1,m]$。

## 思路：

令$dp_{ij}$表示第$i$个点选择权值为$j$的方案数，那么状态转移方程为
<center>$dp_{i,j}=\sum_{k=1}^{m}dp_{i-1,k}[gcd(j,k)\neq w_{i}]$ </center>

复杂度$O(nm^2)$

## code
```cpp
#include<bits/stdc++.h>

using namespace std;
const int mod = 1e9+7;
const int N = 100;
int w[N];
long long dp[N][N];
int main(){
	int n,m;
	cin>>n>>m;
	int a,b,c;
	for(int i=1;i<=n-1;i++){
		cin>>a>>b>>c;
		w[b]=c;
	}
	memset(dp,0,sizeof(dp));
	for(int i=1;i<=m;i++) dp[1][i]=1;
	for(int i=2;i<=n;i++){
		for(int j=1;j<=m;j++){
			for(int k=1;k<=m;k++){
				if(__gcd(j,k)!=w[i]){
					dp[i][j]=(dp[i][j]%mod+dp[i-1][k]%mod)%mod;
				}
			}
		}
	}
	long long ans=0;
	for(int i=1;i<=m;i++) ans=(ans%mod+dp[n][i]%mod)%mod;
	cout<<ans<<endl;
	return 0;
}
```

