---
title: AGC40.Two Contests
tags: Atcoder
mathjax: true
---
# AGC40.Two Contests
<!---more--->
## 题意

$n$段区间，要把他们分到两个不同的集合$S,T$中，不能有剩余，每个区间只能在一个集合里，令$S$中所有区间的交的长度为$ls$，$T$中所有区间的交为$lt$，求$max\{ls+lt\}$。

## 题解

找到 $L$ 最大的区间 $p$ 和 $r$ 最小的区间 $q$，那么只有两种情况:

$1.$ $p,q$在同一个集合内，那么即使把剩下的所有的区间都放到这个集合，最大值也不变，我们一定是把最长的放到另一个区间内，此时答案为$maxlen+minR-maxL+1$。

$2.$ $p,q$不在同一个集合里，那么对于$p$所在的集合，交的长度为$min\{max\{R_i-maxxL+1,0\}\}$，对于$q$所在的集合，交的长度为$min\{max\{minR-L_i+1,0\}\}$，这个问题可以转化为：一个数组，每个元素包含$a_i,b_i$l两个参数，把这个数组分成两部分，使得$min\{a_i\}_{i\in{s}}+min\{b_j\}_{j\in{t}}$最大。

考虑把$a_i$从大到小排序，同时维护$b_i$后缀最小值，每次枚举$i$即可。时间复杂度$O(nlog(n))$。

## 代码：
```cpp
#include<bits/stdc++.h>

using namespace std;
const int N = 2e5+100;
struct node{
    long long a,b;
}s[N];
bool cmp(node a,node b){
    return a.a>b.a;
}
long long L[N],R[N];
long long minnore[N];//后缀最小的B
int main(){
    int n;
    cin>>n;
    for(int i=1;i<=n;i++) cin>>L[i]>>R[i];
    long long maxx=0;long long minn=1e18;
    int p,q;
    long long maxxlength=0;
    for(int i=1;i<=n;i++){
        if(L[i]>maxx){
            maxx=L[i];
            p=i;
        }
        if(R[i]<minn){
            q=i;
            minn=R[i];
        }
        maxxlength=max(maxxlength,R[i]-L[i]+1);
    }
    long long ans1=maxxlength+(minn>=maxx?minn-maxx+1:0);
    for(int i=1;i<=n;i++){
        s[i].a=max(R[i]-maxx+1,0LL);
        s[i].b=max(minn-L[i]+1,0LL);
    }
  //  for(int i=1;i<=n;i++) cout<<i<<' '<<s[i].a<<' '<<s[i].b<<endl;
    sort(s+1,s+n+1,cmp);
    minnore[n+1]=(1e18);
    for(int i=n;i>=1;i--) minnore[i]=min(minnore[i+1],s[i].b);
    long long ans2=0;
    for(int i=1;i<=n-1;i++){
        ans2=max(ans2,s[i].a+minnore[i+1]);
    }
    cout<<max(ans1,ans2)<<endl;
    return 0;
}
```