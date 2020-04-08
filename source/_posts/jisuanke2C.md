---
title: 利用二分图和DP解决的差值最小问题
tags: DP
mathjax: true
---
# 利用二分图和DP解决的分配差值最小问题
<!--more--->
这类题目的大致样子就是，对于一个可重集合，在满足一些不兼容的关系的情况下，将其分成两个可重集合，使得这两个集合的大小差值最小。

解决方法:

我们对这多组不兼容关系连边构成一个图，如果这个图是二分图的话，那么就表明可以分成两个部分，接下来对每个联通块的两个子集和$dp$,令$dp_{ij}$表示到第$i$个联通块的时候第一个集合大小是否可能为$j$，到最后遍历找差值最小即可。这样我们就可以在$O(n^2)$的时间内解决问题。

## $Problem_1:$

### [2019 计蒜之道 初赛 第二场C](https://nanti.jisuanke.com/t/39266)

### 题意：

一个长度为$n$的数组$a$，把他拆分成两个严格递增的数组，使得这两个数组的长度差值最小。无解输出$-1$.

### 思路：

对于$i<j$并且$a_i>=a_j$那么说明$a_i$和$a_j$一定不能在同一个数组中，我们对于不能在同一组的连接一条无向边，构成一个无向图，如果这个图是二分图则说明有解，否则无解。

对于有解的情况，我们对于图中的连通块进行$DP$,由于这个图是二分图，因此每个连通块都可以划分成最多两个不在同一阵营的子集，假设第$i$个连通块的两个子集大小分别为$w_{i1}$和$w_{i2}，$由于确定好一个数组之后另一个也确定了，因此我们让$DP_{ij}$代表到第$i$个连通块时第一个数组的大小为$j$是否可行，最后我们遍历$DP_{num,k}$，其中$num$是连通块的个数，$k$代表第一个数组的个数，找到$|k-(n-k)|$最小的可行$k$即为答案。复杂度$O(n^2)$。

```cpp
#include<bits/stdc++.h>

using namespace std;
const int N = 1e3+100;
typedef long long ll;
vector<ll> G[N];
ll color[N],a[N];
bool vis[N];
ll w[N][3];ll cnt;
ll num1,num2;
bool dfs(ll u,ll c){
	color[u]=c;
	for(auto v:G[u]){
		if(color[v]==c) return 0;
		if(color[v]==0&&!dfs(v,-c)) return 0;
	}
	return 1;
}
void dfs2(ll x,bool ok){
	if(ok) num1++;
	else num2++;
	vis[x]=1;
	for(auto v:G[x]){
		if(!vis[v]) dfs2(v,!ok);
	}
}
ll dp[N][N];
int main(){
	ll T;
	cin>>T;
	while(T--){
		cnt=0;
		ll n;
		cin>>n;
		memset(vis,0,sizeof(vis)); 
		memset(color,0,sizeof(color));
		memset(dp,0,sizeof(dp));
		memset(w,0,sizeof(w));
		for(ll i=1;i<=n;i++){
			cin>>a[i];
			G[i].clear();
		}
		for(ll i=2;i<=n;i++){
			for(ll j=1;j<i;j++){
				if(a[j]>=a[i]){
					G[j].push_back(i);
					G[i].push_back(j);
				}
			}
		}
		bool flag=0;
		for(ll i=1;i<=n;i++){
			if(!color[i]){
				if(!dfs(i,1)){
					flag=1;break;
				}
			}
		}
		if(flag){
			cout<<-1<<endl;
		}
		else{
			for(ll i=1;i<=n;i++){
				if(!vis[i]){
					num1=num2=0;
					dfs2(i,1);
					w[++cnt][1]=num1;
					w[cnt][2]=num2;
				}
			}
		dp[0][0]=1;
		for(int i=1;i<=cnt;i++){
			for(int j=1;j<=n;j++){
				if(j>=w[i][1]){
					if(dp[i-1][j-w[i][1]]) dp[i][j]=1;
				}
				if(j>=w[i][2]){
					if(dp[i-1][j-w[i][2]]) dp[i][j]=1;
				}
			}
		}
		ll ans=0x7f7f7f7f;
		for(int i=0;i<=N-50;i++){
			if(dp[cnt][i]) ans=min((ll)ans,abs((i)-(n-i)));
		}
		cout<<ans<<endl;
		}
	}
	return 0;
}
```
## $Problem_2:$

### [2019ICPC西安邀请赛D: Miku and Generals](https://nanti.jisuanke.com/t/39271)

我们把每个联通快当作物品，价格为两部分的差值，然后01背包即可。

```cpp
#include<bits/stdc++.h>

using namespace std;
typedef long long ll;
const ll N = 200000;
vector<ll> G[N];
ll dp[N];
ll WW[N]; 
ll w[N];
bool vis[N];
ll num1,num2,cnt;
void dfs(ll u,bool check){
	if(check) num1+=w[u];
	else num2+=w[u];
	vis[u]=1;
	for(auto v:G[u]){
		if(!vis[v]) dfs(v,!check);
	}
}
int WWW[N];
int main(){
	ll T;
	cin>>T;
	while(T--){
		memset(vis,0,sizeof(vis));
		memset(dp,0,sizeof(dp));
		ll sum=0;
		num1=num2=cnt=0;
		ll n,m;
		cin>>n>>m;
		for(ll i=1;i<=n;i++){
			cin>>w[i];
			w[i]/=100;
			sum+=w[i];
			G[i].clear();
		}
		ll u,v;
		for(ll i=1;i<=m;i++){
			cin>>u>>v;
			G[u].push_back(v);
			G[v].push_back(u);
		}
		int sum1=0;
		for(ll i=1;i<=n;i++){
			if(!vis[i]){
				num1=num2=0;
				dfs(i,1);
				WW[++cnt]=abs(num1-num2);
				sum1+=abs(num1-num2);
			}
		}
		for(int i=1;i<=cnt;i++){
			for(int j=sum1/2;j>=WW[i];j--){
				dp[j]=max(dp[j],dp[j-WW[i]]+WW[i]);
			}
		}
		ll ans=sum1-2*dp[sum1/2];
		cout<<(sum+ans)*50<<endl;
	}
	return 0;
} 
//1
//4 1
//100 200 300 400
//1 4
//0
//500
//sum1-2*
```
