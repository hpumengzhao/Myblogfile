---
title: E. Special Segments of Permutation
tags: 单调栈
mathjax: true
---
# Educational Codeforces Round 64 (Rated for Div. 2) Special Segments of Permutation（单调栈+暴力剪枝）
<!--more-->
$Problem$：

一个长度为$n$的数组$a$，计算有多少个区间$[l,r]$满足$a[l]+a[r]==max(a[l],a[l+1],...,a[r])$

$Thought$:

先用单调栈处理出每个位置它所能成为最大值的连续区间的端点位置$L[i]$和$R[i]$，然后暴力计算每个区间内的数目即可。但是如果单纯的暴力会超时，这时我们利用启发式的思想，选择
$[L[i],i]$和$[i,R[i]]$中小的那个来计算即可。

```cpp
#include<bits/stdc++.h>

using namespace std;
const int N = 1e6+100;
int pos[N],a[N];
int L[N],R[N];
int main(){
	int n;
	cin>>n;
	stack<int> sta;
	for(int i=1;i<=n;i++) scanf("%d",&a[i]),pos[a[i]]=i;
	for(int i=1;i<=n;i++){
		while(sta.size()&&a[sta.top()]<=a[i]) sta.pop();
		if(sta.empty()) L[i]=1;
		else L[i]=sta.top()+1;
		sta.push(i);
	}
	while(sta.size()) sta.pop();
	for(int i=n;i>=1;i--){
		while(sta.size()&&a[sta.top()]<=a[i]) sta.pop();
		if(sta.empty()) R[i]=n;
		else R[i]=sta.top()-1;
		sta.push(i);
	}
	long long ans=0;
	for(int i=1;i<=n;i++){
		if(i-L[i]<R[i]-i){
			for(int j=L[i];j<i;j++){
				if(pos[a[i]-a[j]]>i&&pos[a[i]-a[j]]<=R[i]) ans++;
			}
		}
		else{
			for(int j=i+1;j<=R[i];j++){
				if(pos[a[i]-a[j]]<i&&pos[a[i]-a[j]]>=L[i]) ans++;
			}			
		}
	}
	cout<<ans<<endl;
	return 0;
}
```
