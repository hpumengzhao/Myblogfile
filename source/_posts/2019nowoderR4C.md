---
title : 2019牛客暑期多校训练营（第四场）C.sequence
tags : 数据结构
mathjax: true
---
# 2019牛客暑期多校训练营（第四场）C.sequence(单调栈+线段树)

<!---more--->

## [题面](https://ac.nowcoder.com/acm/contest/884/C)

## 题意:
找到一个区间$[l,r]$使得$min(a_{l...r}) \cdot sum(b_{l...r})$最大。

## 思路
先用单调栈预处理出$a_i$作为最小值的区间，然后对于每一个$a_i$，如果是$a_i<0$,就要查询$b$在$[l,r]$内并且包含$i$的最小连续子段和,反之查询最大子段和。我们对$b$数组的前缀数组建一棵线段树，如果要查最小子段和，就是$min(pre_{i...r})-max(pre_{l...i-1})$反之即为$max(pre_{i...r})-min(pre_{l...i-1})$。时间复杂度$O(nlog(n))$

## 代码
```cpp
#include<bits/stdc++.h>

using namespace std;
const int N = 3e6+100;
long long maxx[N*5],minn[N*5];
long long a[N],b[N],sum[N],pre[N];
void pushup(int rt){
	maxx[rt]=max(maxx[rt<<1],maxx[rt<<1|1]);
	minn[rt]=min(minn[rt<<1],minn[rt<<1|1]);
}
void bt(int rt,int l,int r){
	if(l==r){
		maxx[rt]=minn[rt]=pre[l];
		return ;
	}
	int mid=(l+r)>>1;
	bt(rt<<1,l,mid);
	bt(rt<<1|1,mid+1,r);
	pushup(rt);
}
long long querymaxx(int rt,int l,int r,int nl,int nr){
	if(nl<=l&&r<=nr){
		return maxx[rt];
	}
	long long ans=0;
	int mid=(l+r)>>1;
	if(nl<=mid) ans=max(ans,querymaxx(rt<<1,l,mid,nl,nr));
	if(nr>mid) ans=max(ans,querymaxx(rt<<1|1,mid+1,r,nl,nr));
	return ans;
}
long long queryminn(int rt,int l,int r,int nl,int nr){
	if(nl<=l&&r<=nr){
		return minn[rt];
	}
	long long ans=99999999999999;
	int mid=(l+r)>>1;
	if(nl<=mid) ans=min(ans,queryminn(rt<<1,l,mid,nl,nr));
	if(nr>mid) ans=min(ans,queryminn(rt<<1|1,mid+1,r,nl,nr));
	return ans;
}
int L[N],R[N];
int main(){
	int n;
	scanf("%d",&n);
	for(int i=1;i<=n;i++) scanf("%lld",&a[i]);
	for(int i=1;i<=n;i++) scanf("%lld",&b[i]),pre[i]=pre[i-1]+b[i];
	bt(1,1,n);
	stack<long long> sta;
	for(int i=1;i<=n;i++){
		while(sta.size()&&a[sta.top()]>=a[i]) sta.pop();
		if(sta.empty()) L[i]=1;
		else L[i]=sta.top()+1;
		sta.push(i);
	}
	while(sta.size()) sta.pop();
	for(int i=n;i>=1;i--){
		while(sta.size()&&a[sta.top()]>=a[i]) sta.pop();
		if(sta.empty()) R[i]=n;
		else R[i]=sta.top()-1;
		sta.push(i);
	}
	long long ans=-99999999999999999;
	for(int i=1;i<=n;i++){
		if(a[i]<0){
			if(i>L[i]) ans=max(ans,a[i]*(queryminn(1,1,n,i,R[i])-querymaxx(1,1,n,L[i],i-1)));
			else ans=max(ans,a[i]*(queryminn(1,1,n,i,R[i])-pre[i-1]));
		}
		else{
			if(i>L[i]) ans=max(ans,a[i]*(querymaxx(1,1,n,i,R[i])-queryminn(1,1,n,L[i],i-1)));
			else ans=max(ans,a[i]*(querymaxx(1,1,n,i,R[i])-pre[i-1]));
		}
	}
	printf("%lld\n",ans);
	return 0;
}
```