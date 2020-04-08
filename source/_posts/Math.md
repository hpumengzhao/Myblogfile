---
title: 数论知识点总结
tags: 数论
mathjax: true
---
# 数论知识点总结
<!--more-->
## $1.gcd$（最大公约数）
对于给出的两个数$a,b$，我们可以用欧几里得算法来计算最大公约数。欧几里得算法的精髓就在于下面这个公式：
$gcd(a,b)=gcd(b,a$%$b)$

证明：
已知：$gcd(a,b)|a$并且$gcd(a,b)|b$,设$a$%$b=r$,则$a=r+kb$,故$r=a-kb$,根据同余关系可得：$r$%$gcd(a,b)=0$,因此$gcd(a,b)=gcd(b,a$%$b)$

code：
```c++
int gcd(int a,int b){
    return b?gcd(b,a%b):a
}
```
## $2.exgcd$（扩展欧几里得算法）
扩展欧几里得算法是用于求解$ax+by=gcd(a,b)$的一组解的算法。
根据欧几里得算法我们可知：$gcd(a,b)=gcd(b,a$%$b)$
我们假设$x1,y1$是满足条件的一组解

那么$ax1+by1=gcd(a,b)$

而$gcd(a,b)=gcd(b,a$%$b)$

故$ax1+by1=bx2+a$%$by2$

而$a$%$b=a-a/b\ast b$

因而$ax1+by1=bx2+ay2-a/b\ast by2=ay2+b*(x2-
a/b\ast y2)$

那么我们就得到了一组合法的$x1,y1$的解：

$x1=y2,y1=x2-a/b\ast y2$

也就是我们递归下去即可。当$b=0$的时候我们就可以发现$x=1,y=0$是合法的

这是我们再返回$x=1,y=0$。最后就一直会回溯下去，得到我们的$x1,y1$
```cpp
void exgcd(int a,int b){
    if(!b){
        x=1,y=0;
        return ;
    }
    exgcd(b,a%b)
    int temp=x;
    x=y;y=temp-a/b*x;
}
```
但是如果要求$ax+by=gcd(a,b)$的最小整数解的时候，我们就要对$x$批量的加上$b$的倍数，但是这不会影响最终的结果。

因为$ax+by+kab-kab=a(x+kb)+b*(y-ka)$，这样依旧是合法的。

因此我们直接让$x=(x$%$b+b)$%$b$即为最终的答案。

## $3.$逆元
对于$a$和$m$，如果$ax\equiv1(modm)$,那么称$x$是$a$在$m$下的逆元。

那么我们该怎么求解逆元呢？我们将逆元的等式转化一下:
$ax+my=1$

由于$ax+my=k$有解当且仅当$k$%$gcd(a,m)=0$的时候有解，说明$gcd(a,m)=1$

那么我们直接用扩展欧几里得求解即可。
```cpp
int x,y;
void exgcd(int a,int b){
    if(!b){
        x=1,y=0;
        return ;
    }
    exgcd(b,a%b)
    int temp=x;
    x=y;y=temp-a/b*x;
}
int inv(int a,int m){//a在m下的逆元
    exgcd(a,m);
    return (x%m+m)%m;
}
```
逆元一般是用在除法取模上面，如$(a/b)$%$m$即为$a$%$m\ast inv(b,m)$

## $4.$埃拉托斯特尼筛法
埃拉托斯特尼筛法是一个复杂度为$nlnnlnn$的筛法。
当选中一个数为素数的时候，就把以这个数为因子的数全部筛掉即可。
```cpp
const int N=1e6+100;
vector<int> pr;
bool vis[N];
void seive(){
	vis[0]=vis[1]=1;
	for(int i=2;i<=N-10;i++){
		if(!vis[i]){
			pr.push_back(i);
			for(int j=2*i;j<=N-10;j+=i) vis[j]=1;
		}
	}
} 
```
## $5.$费马小定理
假设$a$是一个整数，$p$是一个质数，那么$a^p-a$是$p$的倍数

即$a^p\equiv a(modp)$,如果$a$不是$p$的倍数，这个定理也可以写成:

$a^{p-1}\equiv1(modp)$

## $6.$线性同余方程求解
形如$ax\equiv b(modm)$即为线性同余方程。
将线性同余方程变形后即可得到：

$ax+my=b$
只有当$b$%$gcd(a,m)=0$时该方程才有解。
我们先利用扩展欧几里得算法求出

$ax+my=gcd(a,m)$的一组解$(x0,y0)$,那么$x=x0*(b/gcd(a,m))$%$m$
即为原方程的一组解。
## $7.$欧拉函数
欧拉函数即为小于$n$的数中与$n$互质的数的个数
比如$\varphi(8)=4$
欧拉函数的通式为:

$\varphi(x)=x(1-\frac{1}{p1})(1-\frac{1}{p2})...(1-\frac{1}{pn})$

其中$p1,p2,...pn$为$x$的质因数。























