---
title: 2019江西省程序设计竞赛
tags: 竞赛
mathjax: true
---
# 2019江西省程序设计竞赛
<!--more-->
## [A.Cotree](http://acm.hdu.edu.cn/showproblem.php?pid=6567)
## 题意:
两棵树，你需要连接两个点使得$\sum_{i=1}^{i=n}{\sum_{j=i+1}^{j=n}}{dis(i,j)}$最小。
## 思路：
对于每棵树进行换根dp，对每棵子树找到一个点使得所有点到这个点的距离和最小，连接这两个点，然后进行一次DFS统计每条边的贡献，累加即可。
## code
```cpp
#include<bits/stdc++.h>

using namespace std;
const int N = 1e5+100;
vector<long long> G[N];
int n,n1,n2;
bool vis[N];
long long ans;
void dfs1(int x){
    if(vis[x]) return ;
    vis[x]=1;
    for(auto v:G[x]){
        dfs1(v);
    }
}
int ta,tb,now;
long long dp[N];long long siz[N];
void dfs2(long long u,long long fa){
    siz[u]=1;
    for(auto v:G[u]){
        if(v==fa) continue;
        dfs2(v,u);
        siz[u]+=siz[v];
        dp[u]+=dp[v];
    }
    dp[u]+=siz[u];
}
void dfs3(long long u,long long fa){
    if(ans>dp[u]){
        ans=dp[u];
        now=u;
    }
    for(auto v:G[u]){
        if(v==fa) continue;
        long long res=dp[u]-dp[v]-siz[v];
        dp[v]=(dp[v]+res+n1-siz[v]);
        dfs3(v,u);
    }
}
void dfs4(long long u,long long fa){
    if(ans>dp[u]){
        ans=dp[u];
        now=u;
    }
    for(auto v:G[u]){
        if(v==fa) continue;
        long long res=dp[u]-dp[v]-siz[v];
        dp[v]=(dp[v]+res+n2-siz[v]);
        dfs4(v,u);
    }
}

long long final=0;
void dfs(int now,int fa){
    final+=siz[now]*(n-siz[now]);
    for(auto v:G[now]){
        if(v==fa) continue;
        dfs(v,now);
    }
}
int main(){
    //freopen("1.in","r",stdin);
    cin>>n;
    int u,v;
    for(int i=1;i<=n-2;i++){
        cin>>u>>v;
        G[u].push_back(v);
        G[v].push_back(u);
    }
    int a,b;//找到左子树的一个点和右子树的一个点
    a=1;dfs1(1);
    for(int i=2;i<=n;i++){
        if(!vis[i]){
            b=i;n2++;
        }
    }
    n1=n-n2;
    ans=99999999999999;
    now=a;
    dfs2(a,0);
    dfs3(a,0);
    ta=now;
    memset(dp,0,sizeof(dp));
    memset(siz,0,sizeof(siz));
    ans=99999999999999;
    now=b;
    dfs2(b,0);
    dfs4(b,0);
    tb=now;
    G[ta].push_back(tb);
    G[tb].push_back(ta);
    memset(dp,0,sizeof(dp));
    memset(siz,0,sizeof(siz));
    dfs2(1,0);
    dfs(1,0);
    cout<<final<<endl;
    return 0;
}
```
## [D.Wave](http://acm.hdu.edu.cn/showproblem.php?pid=6570)
## 题意：
给一个数组，找到最长的一个子序列使得这个子序列的所有奇数位的数字相同，所有偶数位的数字相同，奇数位和偶数位的数字不同。
## 思路：
利用一个前缀和可以快速地查询$[l,r]$区间内$c$的数量，记录下每个数字出现的位置，暴力枚举子序列的那两个不同的数字，更新最大值。
## code
```cpp
#include<bits/stdc++.h>

using namespace std;
const int N = 1e6+100;
vector<int> pos[200];
int num[N][201];
int a[N];
int main(){
    int n,c;
    cin>>n>>c;
    for(int i=1;i<=100;i++){
        pos[i].push_back(0);
    }
    for(int i=1;i<=n;i++){
        cin>>a[i];
        pos[a[i]].push_back(i);
        for(int j=1;j<=100;j++) num[i][j]=num[i-1][j];
        num[i][a[i]]++;
    }
    int ans=0;
    for(int i=1;i<=c;i++){
        for(int j=1;j<=c;j++){
            if(i!=j){
                int now=0;
                for(int k=2;k<(int)pos[i].size();k++){
                    if(num[pos[i][k]][j]-num[pos[i][k-1]][j]>0) now++;
                }
                now=now*2+1;
                if(num[n][j]-num[pos[i][(int)pos[i].size()-1]][j]>0){
                     now++;
                }
                ans=max(ans,now);
            }
        }
    }
    cout<<ans<<endl;
    return 0;
}
```
## [F.String](http://acm.hdu.edu.cn/showproblem.php?pid=6572)
## 题意：
给定一个字符串，随机拿出四个字母，求这四个字母组成"avin"的概率。
## 思路：
答案即为$\frac{num['a']*num['v']*num['i']*num['n']}{|s|^4}$
## 代码
```cpp
#include<bits/stdc++.h>

using namespace std;
int main(){
    string s;int n;
    while(cin>>n>>s){
        long long a,b,c,d;
        a=b=c=d=0;
        for(int i=0;i<n;i++){
            if(s[i]=='a') a++;
            if(s[i]=='v') b++;
            if(s[i]=='i') c++;
            if(s[i]=='n') d++;
        }
        if(a*b*c*d==0){
            cout<<"0/1"<<endl;
        }
        else{
            long long x=a*b*c*d;
            long long y=(n*n*n*n);
            long long gg=__gcd(x,y);
            x/=gg;
            y/=gg;
            cout<<x<<'/'<<y<<endl;
        }
    }
    return 0;
}
```
## [G.Traffic](http://acm.hdu.edu.cn/showproblem.php?pid=6573)
## 题意：
一个十字路口，东西和南北各有车辆经过，每个车到达十字路口的时间为$t_i$,但是如果东西走向的汽车和南北走向的汽车同时到达十字路口，那么南北走向的就得等待。问最少要等多长时间。
## 思路：
暴力枚举等待时间，知道不会再有车发生时间冲撞。
## 代码:
```cpp
#include<bits/stdc++.h>

using namespace std;
const int N = 3000;
long long a[N],b[N];
bool ta[N],tb[N];
int main(){
    int n,m;
    while(cin>>n>>m){
        memset(ta,0,sizeof(ta));
        memset(ta,0,sizeof(tb));
        for(int i=1;i<=n;i++) cin>>a[i],ta[a[i]]=1;
        for(int i=1;i<=m;i++) cin>>b[i];
        int res=999999999;
        for(int i=0;i<=1001;i++){
            int ans=0;
            bool flag=0;
            memset(tb,0,sizeof(tb));
            for(int j=1;j<=m;j++){
                tb[b[j]+i]=1;
                if(ta[b[j]+i]==1){
                    flag=1;break;
                }
            }
            if(!flag){
                res=i;break;
            }
        }
        cout<<res<<endl;
    }
    return 0;
}
```
## [H.Rng](http://acm.hdu.edu.cn/showproblem.php?pid=6574)
## 题意：
一个$[1,n]$的区间，执行下列操作：
1.任意在$[1,n]$中选择一个$r$
2.在$[1,r]$中选择一个$l$
计算两次线段相交的概率
## 思路:
手动计算2的情况为$\frac{3}{4}$,暴力打表发现接近$\frac{1}{2}$，猜测答案为$\frac{n+1}{2*n}$
## 代码:
```cpp
#include<bits/stdc++.h>

using namespace std;
const int mod = 1e9+7;
long long qp(long long a,long long b){
    long long ans=1;
    while(b){
        if(b&1){
            ans=(ans%mod*a%mod)%mod;
        }
        a=(a%mod*a%mod)%mod;
        b>>=1;
    }
    return ans;
}
int main(){
    int n;
    while(cin>>n){
        cout<<((n+1)%mod*qp(2*n,mod-2)%mod)%mod<<endl;
    }
    return 0;
}
```
## [I.Budget](http://acm.hdu.edu.cn/contests/contest_show.php?cid=868)
## 题意：
$n$个保留三位的小数，计算他们四舍五入变为2位后的差值
## 思路：
以string读入，判断最后一位即可。
## 代码：
```cpp
#include<bits/stdc++.h>

using namespace std;
const int N = 2000000;
string a[N];
int main(){
    int n;
    while(cin>>n){
        double ans=0;
        for(int i=1;i<=n;i++){
            cin>>a[i];
            int l=(int)a[i].length();
            if(a[i][l-1]<='4'){
                ans-=((a[i][l-1]-'0')*1.0)/(1000);
            }
            else{
                ans+=((10-(a[i][l-1]-'0'))*1.0)/1000;
            }
        }
        printf("%.3lf\n",ans);
    }
    return 0;
```
## [J.Worker](http://acm.hdu.edu.cn/showproblem.php?pid=6576)
## 题意：
$n$个工作间，$m$个人，第$i$个工作间每个人每天的产量位$a_i$，计算一种分配方案使的每个工作间的产量相同，如果没有，输出"No"。
## 思路：
$lcm$分配即可。
## 代码：
 ```cpp
 #include<bits/stdc++.h>

using namespace std;
const int N = 3000;
long long lcm(long long a,long long b){
    return a*b/__gcd(a,b);
}
int a[N],b[N];
int main(){
    long long n,m;
    while(cin>>n>>m){
        long long all=1;
        for(int i=1;i<=n;i++) cin>>a[i],all=lcm(all,a[i]);
        long long zz=0;
        for(int i=1;i<=n;i++){
            b[i]=all/(a[i]);
            zz+=b[i];
        }
        if(m%zz){
            cout<<"No"<<endl;
        }
        else{
            cout<<"Yes"<<endl;
            for(int i=1;i<=n-1;i++) cout<<m/(zz)*b[i]<<' ';
            cout<<(m/zz)*b[n]<<endl;
        }
    }
}
 ```
 
 ## [K.Class](http://acm.hdu.edu.cn/contests/contest_showproblem.php?cid=868&pid=1011)
 ## 题意：
 $x=a+b,y=a-b$
 计算$a*b$
 ## 思路：
 答案为$\frac{(x+y)*(x-y)}{4}$
 ## 代码
 ```cpp
 #include<bits/stdc++.h>

using namespace std;
int main(){
    long long x,y;
    cin>>x>>y;
    cout<<(x+y)*(x-y)/4<<endl;
    return 0;
}
 ```