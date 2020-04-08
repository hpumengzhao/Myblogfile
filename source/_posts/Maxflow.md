---
title: 网络流24题
tags: 网络流
mathjax: true
---
# 网络流24题
<!---more--->
## [搭配飞行员](https://loj.ac/problem/6000)
## 题解：

二分图最大匹配，从$S$往每个正驾驶连接一条流量为$1$的边，从每个副驾驶往$T$连接一条流量为$1$的边，两个可以配合的飞行员之间连接一条流量为$1$的边,跑最大流即可。

## 代码:

```cpp
#include<bits/stdc++.h>

using namespace std;
const int N = 3000;
const int INF = 0x3f3f3f3f;
int S,T,n,m,w[N],dep[N],head[N],to[N],num=1,sum=0,x,nxt[N];
bool vis[N];
void add(int u,int v,int ww){
    num++;
    to[num]=v;nxt[num]=head[u];w[num]=ww;head[u]=num;
    num++;
    to[num]=u;nxt[num]=head[v];w[num]=0;head[v]=num;
}
queue<int> q;
bool bfs(){
    while(!q.empty()) q.pop();
    memset(vis,0,sizeof(vis));
    while(!q.empty()) q.pop();
    vis[S]=1;q.push(S);dep[S]=1;
    while(!q.empty()){
        int u=q.front();q.pop();
        for(int i=head[u];i;i=nxt[i]){
            int v=to[i];
            if(vis[v]||w[i]<=0) continue;
            dep[v]=dep[u]+1;
            vis[v]=1;q.push(v);
        }
    }
    return vis[T];
}
int dfs(int u,int d){
    if(u==T||d==0){
        return d;
    }
    int ret=0;
    for(int i=head[u];i;i=nxt[i]){
        int v=to[i];
        if(dep[v]!=dep[u]+1||w[i]<=0) continue;
        int flow=dfs(v,min(d,w[i]));
        d-=flow;ret+=flow;
        w[i]-=flow;w[i^1]+=flow;
        if(d==0) break;
    }
    if(ret==0) dep[u]=-1;
    return ret;
}
int main(){
    scanf("%d %d",&n,&m);
    S=0;T=n+1;
    int u,v;
    for(int i=1;i<=m;i++) add(S,i,1);
    for(int i=m+1;i<=n;i++) add(i,T,1);
    while(~scanf("%d %d",&u,&v)){
        add(u,v,1);
    }
    while(bfs()) sum+=dfs(S,INF);
    printf("%d\n",sum);
    return 0;
}
```

## [太空飞行计划](https://loj.ac/problem/6001)

## 题解：
最大权闭合子图，设$V'$为$G(V,E)$的一个点集，如果$V$中对于每个点的所有的出边所到达的点也$\in V'$，那么$V'$即为一个闭合子图，最大权闭合子图即为所有的闭合子图中权值和最大的。

定理：从$S$向所有权值为正数的点增加一条等于该点点权的流量的边，从所有权值为负数的点向$T$增加一条等于该点权绝对值流量的边，对于$u(w_{u}>0)$，假设完成$u$需要集合$I_{u}$，则对$v \in I_{u}$连接一条权值流量为$inf$的边，跑一边最小割即可。

意义：割掉$S->u$代表不进行任务$u$，割掉$v->T$代表需要使用$v$，最后$S$可达的即为要选择的。

## 代码：
```cpp
#include<bits/stdc++.h>

using namespace std;
const int N = 3000;
const int INF = 0x3f3f3f3f;
int S,T,n,m,w[N],dep[N],head[N],to[N],num=1,sum=0,x,nxt[N];
bool vis[N];
void add(int u,int v,int ww){
    num++;
    to[num]=v;nxt[num]=head[u];w[num]=ww;head[u]=num;
    num++;
    to[num]=u;nxt[num]=head[v];w[num]=0;head[v]=num;
}
queue<int> q;
bool bfs(){
    while(!q.empty()) q.pop();
    memset(vis,0,sizeof(vis));
    while(!q.empty()) q.pop();
    vis[S]=1;q.push(S);dep[S]=1;
    while(!q.empty()){
        int u=q.front();q.pop();
        for(int i=head[u];i;i=nxt[i]){
            int v=to[i];
            if(vis[v]||w[i]<=0) continue;
            dep[v]=dep[u]+1;
            vis[v]=1;q.push(v);
        }
    }
    return vis[T];
}
int dfs(int u,int d){
    if(u==T||d==0){
        return d;
    }
    int ret=0;
    for(int i=head[u];i;i=nxt[i]){
        int v=to[i];
        if(dep[v]!=dep[u]+1||w[i]<=0) continue;
        int flow=dfs(v,min(d,w[i]));
        d-=flow;ret+=flow;
        w[i]-=flow;w[i^1]+=flow;
        if(d==0) break;
    }
    if(ret==0) dep[u]=-1;
    return ret;
}
int c[1000],p[1000];
int main(){
    scanf("%d %d",&n,&m);
    S=n+m+1;T=n+m+2;
    for(int i=1;i<=n;i++){
        scanf("%d",&p[i]);
        sum+=p[i];
        add(S,i,p[i]);
        while(getchar()==' '){
            scanf("%d",&x);
            add(i,n+x,INF);
        }
    }
    for(int i=1;i<=m;i++){
         scanf("%d",&c[i]);
         add(i+n,T,c[i]);
    }
    while(bfs()) sum-=dfs(S,INF);
    vector<int> task,exp;
    for(int i=1;i<=n;i++){
        if(vis[i]){
             task.push_back(i);
        }
    }
    for(int i=n+1;i<=n+m;i++){
        if(vis[i]) exp.push_back(i-n);
    }
    printf("%d",task[0]);
    for(int i=1;i<(int)task.size();i++){
        printf(" %d",task[i]);
    }
    puts("");
    printf("%d",exp[0]);
    for(int i=1;i<(int)exp.size();i++){
        printf(" %d",exp[i]);
    }
    puts("");
    printf("%d\n",sum);
    return 0;
}
```

## [最小路径覆盖](https://loj.ac/problem/6002)

## 题解：
假设$x,y$之间有一条边，则把$x$和$y$分别拆成$x_1,x_2,y_1,y_2$。在$x_1$和$y_2$之间连接一条流量为$1$的边，构造好二分图后答案即为$n-maxmatch$。对于方案的输出，可以利用并查集，对于$x$和$y$，如果流量流经$x->y$，则$x->y$在一条路径上，最后$O(n^2)$输出即可。

## 代码：
```cpp
#include<bits/stdc++.h>

using namespace std;
const int N = 100000;
const int INF = 0x3f3f3f3f;
int S,T,n,m,w[N],to[N],dep[N],head[N],nxt[N],num=1,u,v,pre[N];
void add(int u,int v,int ww){
    num++;
    to[num]=v;nxt[num]=head[u];w[num]=ww;head[u]=num;
    num++;
    to[num]=u;nxt[num]=head[v];w[num]=0;head[v]=num;
}
queue<int> q;
bool vis[N];
bool bfs(){
    while(!q.empty()) q.pop();
    memset(vis,0,sizeof(vis));
    dep[S]=0;
    vis[S]=1;
    q.push(S);
    while(!q.empty()){
        int u=q.front();q.pop();
        for(int i=head[u];i;i=nxt[i]){
            int v=to[i];
            if(vis[v]||w[i]<=0) continue;
            vis[v]=1;
            dep[v]=dep[u]+1;
            q.push(v);
        }
    }
    return vis[T];
}
int dfs(int u,int d){
    if(u==T||d==0){
        return d;
    }
    int ret=0;
    for(int i=head[u];i;i=nxt[i]){
        int v=to[i];
        if(dep[v]!=dep[u]+1||w[i]<=0) continue;
        int flow=dfs(v,min(w[i],d));//增广的流量
        d-=flow;ret+=flow;
        w[i]-=flow;w[i^1]+=flow;
        if(d==0) break;
    }
    if(ret==0){//无法增广
        dep[u]=-1;
    }
    return ret;
}
int par[N];
void init(){
    for(int i=0;i<=2*n+1;i++) par[i]=i;
}
int find(int x){
    return x==par[x]?x:par[x]=find(par[x]);
}
void unite(int x,int y){
    x=find(x);y=find(y);
    if(x==y) return ;
    par[x]=y;
}
int main(){
    scanf("%d %d",&n,&m);
    S=0;T=2*n+1;
    int ans=n;
    for(int i=1;i<=m;i++){
        scanf("%d %d",&u,&v);
        add(u,v+n,1);
    }
    for(int i=1;i<=n;i++) add(S,i,1);
    for(int i=n+1;i<=2*n;i++) add(i,T,1);
    while(bfs()) ans-=dfs(S,INF);
    init();
    for(int i=1;i<=n;i++){
        for(int j=head[i];j;j=nxt[j]){
            if(to[j]&&!w[j]&&to[j]<=2*n){//如果i->to[j]还有流量
                unite(i,to[j]-n);
            }
        }
    }
    for(int i=n;i;i--){
        bool check=0;
        for(int j=1;j<=n;j++){
            if(find(j)==i){
                check=1;
                printf("%d ",j);
            }
        }
        if(check) puts("");
    }
    printf("%d\n",ans);
    return 0;
}
```