---
title : The Preliminary Contest for ICPC Asia Nanjing 2019
tags : contest
mathjax : true
---

# The Preliminary Contest for ICPC Asia Nanjing 2019
<!---more--->
## [A.The beautiful values of the palace](https://nanti.jisuanke.com/t/41298)

首先对于每个$(x,y)$，我们可以$O(1)$的查询出这个坐标的值。接下来就将问题转化为了一个$10^6 \cdot 10^6$的矩阵，每次查询子矩阵内的点的和。

考虑将所有的$y$离散化，计$mp_{i,j}$表示$(1,1)-(i,j)$的和，那么对于$(x_1,y_1)->(x_2,y_2)$，答案即为
$mp_{x_2,y_2}-mp_{x_1-1,y_2}-mp_{x_2,y1-1}+mp_{x_1-1,y_1-1}$

考虑离线做法：
把所有的点按照$x$从小到达排序，每加入一个点，利用树状数组插入，每次查询，利用树状数组查询四个前缀和即可。时间复杂度$(m+4p) \cdot log(m+4p)$。

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N = 1e6+100;
const int mod = 1e9+7;
typedef long long ll;
const int INF = 0x3f3f3f3f;
const ll llINF = 0x3f3f3f3f3f3f3f3f;
#define rep(i,a,b) for(int i=(a);i<=(b);i++)
#define fep(i,a,b) for(int i=(a);i>=(b);i--)
inline bool read(ll &num) {
    char in;bool IsN=false;
    in=getchar();
    if(in==EOF) return false;
    while(in!='-'&&(in<'0'||in>'9')) in=getchar();
    if(in=='-'){ IsN=true;num=0;}
    else num=in-'0';
    while(in=getchar(),in>='0'&&in<='9'){
            num*=10,num+=in-'0';
    } 
    if(IsN) num=-num;
    return true;
}
ll T,n,p,m,c[N];
int lowbit(ll x){
    return (x&(-x));
}
void add(ll x,ll v){
    for(;x<N;x+=lowbit(x)){
         c[x]+=v;
         //cout<<x<<' '<<v<<endl;
    }
}
ll query(ll x){
    ll ans=0;
    for(;x;x-=lowbit(x)){
        ans+=c[x];
    }
    return ans;
}
//BIT
struct point{
    ll x,y;
    int flag;
}pp[600000];
bool cmp(point a,point b){
    if(a.x==b.x){
        if(a.y==b.y){
           return a.flag<b.flag;
        }
        return a.y<b.y;
    }
    return a.x<b.x;
}

ll dig(ll x){
    ll ans=0;
    while(x){
        ans+=x%10;
        x/=10;
    }
    return ans;
}
ll cal(ll x,ll y){//计算(x,y)处的值
    x=x-n/2-1;
    y=y-n/2-1;
    ll t=max(abs(x),abs(y));
    if(x>=y) return n*n-4*t*t-2*t-x-y;
    else return n*n-4*t*t+2*t+x+y;
}
map<pair<ll,ll>,ll> mmp;
ll yy[N],id[N];
ll x__1[100001],y__1[100001],x__2[100001],y__2[100001];
int main(){
    read(T);
    while(T--){
        mmp.clear();
        read(n);read(m);read(p);
        memset(c,0,sizeof(c));
        ll x,y,x_1,y_1,x_2,y_2;
        rep(i,1,m){
            read(x);read(y);
            pp[i]={x,y,0};
        }
        rep(i,1,p){
            read(x_1);read(y_1);read(x_2);read(y_2);
            x__1[i]=x_1;y__1[i]=y_1;x__2[i]=x_2;y__2[i]=y_2;
            pp[++m]={x_1-1,y_1-1,1};
            pp[++m]={x_2,y_2,1};
            pp[++m]={x_1-1,y_2,1};
            pp[++m]={x_2,y_1-1,1};
        }
        rep(i,1,m) yy[i]=pp[i].y;
        sort(yy+1,yy+m+1);
        int siz=unique(yy+1,yy+m+1)-yy-1;
        sort(pp+1,pp+m+1,cmp);
        rep(i,1,m){
            id[i]=lower_bound(yy+1,yy+siz+1,pp[i].y)-yy;
        }
        //离散化
        rep(i,1,m){
            if(pp[i].flag==0){
                add(id[i],dig(cal(pp[i].x,pp[i].y)));
                //cout<<pp[i].x<<' '<<pp[i].y<<' '<<id[i]<<' '<<dig(cal(pp[i].x,pp[i].y))<<endl;
            }
            else{
                mmp[{pp[i].x,pp[i].y}]=query(id[i]);
                //cout<<pp[i].x<<' '<<pp[i].y<<' '<<query(id[i])<<endl;
            }
        }
        rep(i,1,p){
            //cout<<x__2[i]<<' '<<y__2[i]<<endl;
            printf("%lld\n",mmp[{x__2[i],y__2[i]}]-mmp[{x__1[i]-1,y__2[i]}]-mmp[{x__2[i],y__1[i]-1}]+mmp[{x__1[i]-1,y__1[i]-1}]);
        }
    }
    return 0;
}
```

## [B.super_log](https://nanti.jisuanke.com/t/41299)

计算$a^{a^{a^{a...}}} \% m$，总共有$b$个$a$。

直接递归欧拉降幂即可。
降幂公式：
$$
a^b \% p=
\begin{cases}
a^{b \% \phi(p)} \%p & gcd(a,p)=1\\
a^b \% p & gcd(a,p) \neq1 ,b < \phi(p)\\
a^{b \% \phi(p)+\phi(p)} \% p & gcd(a,p)\neq 1, \phi(p) \leq b
\end{cases}
$$

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N = 2e6+100;
const int mod = 1e9+7;
typedef long long ll;
const int INF = 0x3f3f3f3f;
const ll llINF = 0x3f3f3f3f3f3f3f3f;
#define rep(i,a,b) for(int i=(a);i<=(b);i++)
#define fep(i,a,b) for(int i=(a);i>=(b);i--)
inline bool read(ll &num) {
	char in;bool IsN=false;
	in=getchar();
	if(in==EOF) return false;
	while(in!='-'&&(in<'0'||in>'9')) in=getchar();
	if(in=='-'){ IsN=true;num=0;}
	else num=in-'0';
	while(in=getchar(),in>='0'&&in<='9'){
			num*=10,num+=in-'0';
	} 
	if(IsN) num=-num;
	return true;
}
ll ph[N];
void init(){
	rep(i,1,N-10){
		ph[i]=i;
	}
	rep(i,2,N-10){
		if(ph[i]==i){
			for(int j=i;j<=N-10;j+=i){
				ph[j]=ph[j]/i*(i-1);
			}
		}
	}
}
ll qpow(ll a,ll b,ll mod){
	ll ans=1;
	while(b){
		if(b&1) ans=(ans%mod*a%mod)%mod;
		a=(a%mod*a%mod)%mod;
		b>>=1;
	}
	return ans%mod;
}
bool check(ll a,ll b,ll m){
	if(b==0) return 1>=ph[m];
	if(b==1) return a>=ph[m];
	ll ans=1;
	if(ans>=ph[m]) return 1;
	rep(i,1,b-1){
		rep(j,1,a){
			ans*=a;
			if(ans>=ph[m]) return 1;
		}
	}
	return 0;
}
ll  solve(ll a,ll b,ll m){
	if(m==1) return 0;
	if(b==0) return 1%m;
	if(b==1) return a%m;
	if(__gcd(a,m)==1){
		return qpow(a,solve(a,b-1,ph[m]),m);
	}
	else{
		if(check(a,b-1,m)){
			return qpow(a,solve(a,b-1,ph[m])+ph[m],m);
		}
		else return qpow(a,solve(a,b-1,m),m);
	}
}
ll T,a,b,m;
int main(){
	//freopen("1.in", "r", stdin);
	read(T);
	init();
	//cout<<ph[1000000]<<endl;
	while(T--){
		read(a);read(b);read(m);
		printf("%lld\n",solve(a,b,m)%m);
	}
	return 0;
}
```

## [D.Robots](https://nanti.jisuanke.com/t/41301)
一个有向图，机器人从$u$可以不接着往下走，也可以随便选一个邻接点走下去，所有的情况都是等概率的，一天只能执行一次上面的操作。第$i$天执行任意操作的的花费为$i$，计算从$1$到$n$的期望花费。

计$day_{u}$为从$u$到$n$的期望天数,$cost_{u}$代表$u$到$n$的期望花费。
那么有：
$$
day_{u}=(day_{u}+1) \cdot \frac{1}{d_{u}+1}+\sum{(day_{v}+1)\cdot  \frac{1}{d_{u}+1}}
$$

那么我们就可以得到花费期望的转移：
$$
cost_{u}=(cost_{u}+day_{u}+1) \cdot \frac{1}{d_{u}+1}+ \sum{(cost_{v}+day_{v}+1)\cdot  \frac{1}{d_{u}+1}}
$$
移项后DFS转移即可。

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N = 1e6+100;
const int mod = 1e9+7;
typedef long long ll;
const int INF = 0x3f3f3f3f;
const ll llINF = 0x3f3f3f3f3f3f3f3f;
#define rep(i,a,b) for(int i=(a);i<=(b);i++)
#define fep(i,a,b) for(int i=(a);i>=(b);i--)
inline bool read(ll &num) {
    char in;bool IsN=false;
    in=getchar();
    if(in==EOF) return false;
    while(in!='-'&&(in<'0'||in>'9')) in=getchar();
    if(in=='-'){ IsN=true;num=0;}
    else num=in-'0';
    while(in=getchar(),in>='0'&&in<='9'){
            num*=10,num+=in-'0';
    } 
    if(IsN) num=-num;
    return true;
}
int T,n,m,u,v;
double day[N],cost[N],d[N];
bool vis[N];
vector<int> G[N];
double dfsa(int u){
    if(day[u]) return day[u];
    if(u==n) return day[u];
    double x=0;
    for(int v:G[u]){
        dfsa(v);
        x+=(day[v]+1)/(d[u]+1);
    }
    x+=1.0/(d[u]+1);
    return day[u]=x*(d[u]+1)/(d[u]);
}
double dfsb(int u){
    if(cost[u]) return cost[u];
    if(u==n) return cost[u];
    double y=0;
    for(int v:G[u]){
        dfsb(v);
        y+=(cost[v]+day[v]+1)/(d[u]+1);
    }
    y+=(day[u]+1)/(d[u]+1);
    return cost[u]=(y*(d[u]+1))/(d[u]);
}
int main(){
    //freopen("1.in", "r", stdin);
    scanf("%d",&T);
    while(T--){
        scanf("%d %d",&n,&m);
        rep(i,1,n){
            d[i]=0,G[i].clear();
            day[i]=cost[i]=0;
        }
        rep(i,1,m){
            scanf("%d %d",&u,&v);
            G[u].push_back(v);
            d[u]+=1.0;
        }
        dfsa(1);
        printf("%.2lf\n",dfsb(1));
    }
    return 0;
}

```

## [ F. Greedy Sequence](https://nanti.jisuanke.com/t/41303)

会发现每个点的前驱都是唯一的，我们用主席树求出对于每个点的左右$k$远的区间内最大的小于这个数字的数，然后$O(n)$的递推即可。

```cpp
#include<bits/stdc++.h>

using namespace std;
const int maxn = 1e5+100;
typedef long long ll;
struct node
{
	ll sum,l,r;
}t[maxn*32];
int cnt;
void update(ll l,ll r,ll &x,ll y,ll pos){
	t[++cnt]=t[y];t[cnt].sum++;x=cnt;//复制节点并且更新
	if(l==r) return ;
	int mid=(l+r)>>1;
	if(mid>=pos) update(l,mid,t[x].l,t[y].l,pos);
	else update(mid+1,r,t[x].r,t[y].r,pos);
}
int query(int a,int b,int x,int l,int r)
{
	if(l==r)
	{
		if(l==x)
			return 0;
		else
			return l;
	}
	int mid=(l+r)>>1;
	int xx=t[t[b].l].sum-t[t[a].l].sum;
	int yy=t[t[b].r].sum-t[t[a].r].sum;
	int res=0;
	if(yy&&x>mid)
		res=query(t[a].r,t[b].r,x,mid+1,r);
	if(xx&&!res)
		res=query(t[a].l,t[b].l,x,l,mid);
	return res;
}
int a[maxn],pos[maxn],ans[maxn];
ll roots[maxn];
int main(){
    //freopen("1.in","r",stdin);
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int T;
    cin>>T;
    while(T--){
        cnt=0;
        int n,k;
        cin>>n>>k;
        for(int i=1;i<=n;i++){
            cin>>a[i];pos[a[i]]=i;
        }
        for(int i=1;i<=n;i++) update(1,n,roots[i],roots[i-1],a[i]);
        for(int i=1;i<=n;i++){
            int L=max(pos[i]-k,1);
            int R=min(pos[i]+k,n);
            int now=query(roots[L-1],roots[R],i,1,n);
            if(now==0) ans[i]=1;
            else ans[i]=ans[now]+1;
        }
        for(int i=1;i<=n-1;i++) cout<<ans[i]<<' ';
        cout<<ans[n]<<endl;
    }
    return 0;
}
```
## [ H. Holy Grail](https://nanti.jisuanke.com/t/41305)

跑6次$SPFA$即可。

```cpp
#include<bits/stdc++.h>
using namespace std;
const int mod = 1e9+7;
typedef long long ll;
const int N = 401;
const int INF = 0x3f3f3f3f;
const ll llINF = 0x3f3f3f3f3f3f3f3f;
#define rep(i,a,b) for(int i=(a);i<=(b);i++)
#define fep(i,a,b) for(int i=(a);i>=(b);i--)
inline bool read(ll &num) {
    char in;bool IsN=false;
    in=getchar();
    if(in==EOF) return false;
    while(in!='-'&&(in<'0'||in>'9')) in=getchar();
    if(in=='-'){ IsN=true;num=0;}
    else num=in-'0';
    while(in=getchar(),in>='0'&&in<='9'){
            num*=10,num+=in-'0';
    } 
    if(IsN) num=-num;
    return true;
}
ll dis[N];
int s,t,n,m,u,v;
ll w;
vector<pair<int,ll> > G[N];
bool vis[N];
void SPFA(int s)//若存在负环返回false
{
    queue<int> q;
    memset(vis,0,sizeof(vis));
    rep(i,0,n) dis[i]=llINF;
    dis[s]=0;
    q.push(s);
    while(!q.empty())
    {
        int u=q.front();q.pop();
        vis[u]=0;
        for(auto V:G[u]){
            int v=V.first;ll w=V.second;
            if(dis[v]>dis[u]+w){
                dis[v]=dis[u]+w;
                if(!vis[v]){
                    q.push(v);
                    vis[v]=1;
                }
            }
        }
    }
    return ;
}
int main(){
   //freopen("1.in", "r", stdin);
    int T;
    scanf("%d",&T);
    while(T--){
        rep(i,0,n) G[i].clear();
        scanf("%d %d",&n,&m);
        rep(i,1,m){
            scanf("%d %d %lld",&u,&v,&w);
            G[u].push_back({v,w});
        }
        rep(i,1,6){
            scanf("%d %d",&s,&t);
            SPFA(t);
            printf("%lld\n",-dis[s]);
            G[s].push_back({t,-dis[s]});
        }
    }
    return 0;
}
```