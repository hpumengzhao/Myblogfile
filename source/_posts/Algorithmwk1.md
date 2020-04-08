---
title: Algorithm Weekly 1
tags: Problems
mathjax: true
---

## A happy week

<!---more---->

* **牛客小白月赛23:Problem G.**
有一棵包含$n$个节点和$n-1$条边的树，规定树链$(u,v)$为树上从$u$到$v$的简单路径。
树的每条边上都有一个正整数，这个正整数被称作这条边的颜色，规定一条树链的权值$w(u,v)$为这条树链上所有边的颜色的代数和。
而整棵树的权值为所有不同的树链的权值的代数和。
已知所有边的颜色集合恰好为$1$到$n-1$这$n-1$个不同的正整数，请你为每条边安排一种颜色，使得这棵树的权值尽量小，你不需要给出具体方案，只需要求出这个最小的权值即可。
**思路:**
计算每条边的贡献:边$(u,v)$的贡献为$(n-siz_{u}) \cdot siz_{u}$，其中$siz_{i}$为$i$节点为根的子树的大小。按贡献从大到小排序后分配给$1,2..,n-1$即可。时间复杂度$O(n\cdot log(n))$
* **Leetcode181 T3**
给定$6$种道路，按照数据所给的$n \cdot m$矩阵排列能否从$(0,0)$到达$(n-1,m-1)$。
**思路:**
BFS即可，对于每一块的四周判断可不可以走。
* **KickStart RoundA T2**
一个$n \cdot m$的方格阵，每个方格都有一个值，对于每一行来说，你只能从第一块开始拿，不能跳过某一块，整个方阵最多可以拿$k$块，使这$k$块的值的和最大。
**思路**
dp，令$dp_{i,j,k}$代表拿到第$i$行，第$i$行拿了$j$个，总共拿了$k$个的最大值。$(nm)^2$转移即可。
**代码**
```cpp
/*
* @author:  codancer
* @createTime:  time
*/
#include<bits/stdc++.h>

using namespace std;
typedef long long ll;
const ll mod = 1e9+7;
#define pb push_back
#define fi first
#define se second
#define SZ(x) ((int)(x).size())
#define rep(i,a,b) for(int i=(a);i<=(b);i++)
#define fep(i,a,b) for(int i=(a);i>=(b);i--)
typedef vector<int> VI;
typedef vector<ll> VII;
typedef pair<int,int> pii;
const int N = 200;
long long dp[60][40][2000];//dp[i][j][k]表示第i行拿j个总共k个的最大值
long long a[N][N];
long long pre[N][N];
int main(){
	int T;
	cin>>T;
	int cas=1;
	while(T--){
		int n,k,p;
		cin>>n>>k>>p;
		rep(i,1,n){
			pre[i][0]=0;
			rep(j,1,k){
				cin>>a[i][j];
				pre[i][j]=pre[i][j-1]+a[i][j];
			}
		}
		memset(dp,0,sizeof(dp));
		rep(i,1,n){//第i行
			rep(j,0,k){
				rep(u,0,k){
					rep(last,0,p){
						dp[i][j][last+j]=max(dp[i-1][u][last]+pre[i][j],dp[i][j][last+j]);
					}
				}
			}
		}
		ll ans=0;
		rep(i,1,n){
			rep(j,0,k){
				ans=max(ans,dp[i][j][p]);
			}
		}
		printf("Case #%d: %lld\n",cas++,ans);
	}
	return 0;
}
```

* **KickStart RoundA T3**
$n$个木棒，可以切$k$次，随便切，问切割后最长的木棒最短为多少
**思路：**
二分最大值$x$，假设第$i$个木棒的长度为$l_{i}$，则可计算出该木棒至少切割几次才能让最大值小于等于$x$，判断总次数是否小于等于$k$即可。

* **ABC159 E**
一个$n \cdot m$的矩阵，如果$(i,j)$处为$1$则是白色，为$0$则是黑色，现在你可以对这个矩阵横竖切割，最终要使得切割后的每一块的黑色面积小于等于$k$，计算至少需要多少次。其中$1 \leq n \leq 10, 1\leq m \leq 1000$。
**思路:**
$2^{n-1}$枚举横着切的所有情况，贪心的计算竖着的刀数即可。时间复杂度$O(2^{n-1} \cdot n \cdot m)$
**代码**
```cpp
/*
* @author:  codancer
*/
#include<bits/stdc++.h>

using namespace std;
typedef long long ll;
const ll mod = 1e9+7;
#define pb push_back
#define SZ(x) ((int)(x).size())
#define fi first
#define se second
#define rep(i,a,b) for(int i=(a);i<=(b);i++)
#define fep(i,a,b) for(int i=(a);i>=(b);i--)
typedef vector<int> VI;
typedef pair<int,int> pii;
char maze[20][3000];
int c[3000][20];//第j列的前i行的前缀和
int main(){
	int n,m,k;
	cin>>n>>m>>k;
	rep(i,1,n){
		rep(j,1,m){
			cin>>maze[i][j];
		}
	}
	rep(j,1,m){
		c[j][0]=0;
		rep(i,1,n){
			c[j][i]=c[j][i-1]+(maze[i][j]-'0');
		}
	}
	
	int ans=1e9;
	for(int i=0;i<(1<<(n-1));i++){
		vector<int> lc;
		int now=0;
		for(int j=0;j<(n-1);j++){
			if((i>>j)&1){
				lc.pb(j+1);
			}
		}
		int siz=(int)lc.size();
		now+=siz;
		
		vector<pair<int,int> > sq;//第i块的区间
				
		if(siz==0){//不切 
			sq.pb({1,n});
		}else{
			sq.pb({1,lc[0]});
			rep(j,1,siz-1){
				sq.pb({lc[j-1]+1,lc[j]});
			} 
			sq.pb({lc[siz-1]+1,n});
		}
		
		//先检验横着划分的可行性 
		bool flag=1;
		rep(j,1,m){
			rep(d,0,siz){
				int up=sq[d].first;
				int down=sq[d].second;
				if(c[j][down]-c[j][up-1]>k){
					flag=0;
					break;
				}
			}
		}
		if(flag==0) continue;
		
//		cout<<sq.size()<<' '<<now<<endl;
//		for(auto v:sq){
//			cout<<v.first<<"---"<<v.second<<endl;
//		}
		
		vector<int> tmp(siz+1,0);//第i块当前白色的数量 
		rep(j,1,m){
			bool ok=1;
			rep(d,0,siz){
				int up=sq[d].first;
				int down=sq[d].second;
				if(tmp[d]+c[j][down]-c[j][up-1]>k){
					ok=0;
					break;
				}
				tmp[d]+=(c[j][down]-c[j][up-1]);
			}
			if(ok==0){
				++now;
				rep(d,0,siz){
					int up=sq[d].first;
					int down=sq[d].second;
					tmp[d]=c[j][down]-c[j][up-1];
				}
			}
		}
		ans=min(ans,now);
	}
	
	cout<<ans<<endl;
	return 0;
}

```
