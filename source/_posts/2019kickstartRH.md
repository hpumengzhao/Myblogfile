---
title: google kickstart round H
tags: contest
mathjax: true
---
## 挺好的题目，就是不太会...
<!---more--->
## H-Index
题意：对于一个数组，对于每个$i( 1\leq i \leq n)$，找到一个数字$H$，使得$a_{1}...a_{i}$中大于等于$H$的数字的出现的次数也大于等于$H$。
solution:
可发现对于从$i$到$i+1$，答案最多增加$1$，可以用优先队列或者数据结构来实现查询操作，时间复杂度$O(nlog(n))$。
code(主席树):
```cpp
#include<bits/stdc++.h>

using namespace std;
const int N = 2e5+100;
int a[N];
vector<int> v[N<<2];
vector<int>::iterator it;
 
void build(int id,int l,int r){
    v[id].clear();
    for(int i = l; i <= r; i++)
        v[id].push_back(a[i]);
    sort(v[id].begin(),v[id].end());
    if(l == r)
        return;
    int mid = (l+r)>>1;
    build(id<<1,l,mid);
    build(id<<1|1,mid+1,r);
}
 
int query(int id,int L,int R,int l,int r,int h){
    if(l <= L && R <= r){
        it = upper_bound(v[id].begin(),v[id].end(),h);
        return it - v[id].begin();
    }
    int mid = (L+R)>>1;
    int ans = 0;
    if(l <= mid)
        ans += query(id<<1,L,mid,l,r,h);
    if(mid < r)
        ans += query(id<<1|1,mid+1,R,l,r,h);
    return ans;
}

int main(){
	int T,n;
	scanf("%d",&T);
	int cas=1;
	while(T--){
		scanf("%d",&n);
		for(int i=1;i<=n;i++) v[i].clear();
		vector<int> ans(n+1);
		for(int i=1;i<=n;i++){
			scanf("%d",&a[i]);
		}
		build(1,1,n);
		ans[1]=1;
		for(int i=2;i<=n;i++){
			int counts=i-query(1,1,n,1,i,ans[i-1]); 
			if(counts>=ans[i-1]+1){
				ans[i]=ans[i-1]+1;
			}else{
				ans[i]=ans[i-1];
			}
		}
		cout<<"Case #"<<cas++<<":";
		for(int i=1;i<=n;i++){
			cout<<' '<<ans[i];
		}
		cout<<endl;
	}
	return 0;
}

```
## Diagonal Puzzle
题意：
一个矩阵，每一个单元格是黑色或者白色，现在可以选取某一条对角线并反转该对角线的颜色，问最少反转多少条对角线才能使矩阵全为黑色。
sol：
将每个点抽象成边，每个对角线抽象成点，如果$maze_{i,j}$为白色，则必须要反转经过这个坐标的对角线中的其中一条，考虑建图，编号为偶数的点代表不反转，奇数的要反转，按照颜色建图。

假设该坐标为白色，经过的对角线编号为$u$和$v$，则
$add(2u,2v+1),add(2v,2u+1)$，表示$u,v$必须反转其中之一。如果为黑色则$add(2u,2v),add(2u+1,2v+1)$。

对于每个白色的坐标，枚举两种反转方式（奇偶染色），对于每个点进行$DFS$即可，时间复杂度$O(n^2)$。
code:
```cpp
#include<bits/stdc++.h>

using namespace std;
const int N = 100000;
string maze[N];
vector<int> G[N];
bool vis[N];
int counts=0;
void dfs(int u){//奇数是反转的
	if(vis[u]) return ;
	vis[u]=1;
	if(u&1){
		counts++;
	}
	for(int v:G[u]){
		dfs(v);
	}
}
void add(int u,int v){
	G[u].push_back(v);
	G[v].push_back(u);
}
void solve(){
	counts=0;
	memset(vis,0,sizeof(vis));
	for(int i=1;i<=N-1;i++) G[i].clear();
	int n;
	cin>>n;
	for(int i=0;i<n;i++){
		cin>>maze[i];
	}
	int all=4*n-2;
	for(int i=0;i<n;i++){
		for(int j=0;j<n;j++){
			int u=i-j+n;
			int v=i+j+2*n;
			// cout<<i<<' '<<j<<' '<<u<<' '<<v<<endl;
			if(maze[i][j]=='.'){
				add(2*u,2*v+1);//u反转
				add(2*v,2*u+1);//v反转
			}else{
					add(2*u,2*v);//都反转
					add(2*u+1,2*v+1);//都不反转
			}
		}
	}
	int ans=0;
	for(int i=0;i<n;i++){
		for(int j=0;j<n;j++){
			int x=i-j+n;
			int y=x*2+1;//不反转
			x*=2;//反转
			if(maze[i][j]=='.'&&!vis[x]){
				counts=0;
				dfs(x);
				int res=counts;
				counts=0;
				dfs(y);
				ans+=min(res,counts);
			}
		}
	}
	cout<<ans<<endl;
}
int main(){
	int T;
	cin>>T;
	int cas=1;
	while(T--){
		cout<<"Case #"<<cas++<<": ";
		solve();
	}
	return 0;
}
```
## Elevanagram
待补（dp）。
