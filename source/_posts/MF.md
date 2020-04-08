---
title: 矩阵分解推荐算法（LMF）
tags: Machine Learning
mathjax: true
---
# 矩阵分解推荐算法（LMF）
<!---more--->
首先我们现在有一个矩阵$R_{mn}$，其中$R_{ij}$代表第$i$个用户对第$j$个商品的喜爱程度。

$LMF$算法认为每个商品上面都有一些隐因子，而顾客的喜爱程度是由这些隐因子来决定的。因此便可以将$R_{mn}$分解成$P_{mF} \times Q_{Fn}$的形式。
矩阵$P_{mF}$代表了这$m$个用户对$F$个隐因子的喜爱程度，$Q_{Fn}$代表这$F$个隐因子在这$n$个商品上的分布概率。
<center>$R'_{ij}=\sum_{f=1}^F {P_{if}Q_{fj}}$ </center>
我们最终的目的是使得$R_{ij}$和$R'_ {ij}$尽可能的相近。因此，损失函数为:
<center>$f(P,Q)=\sum{(R_{ij}-R'_{ij})^2}$ </center>

为了防止过拟合，需要加上一个正则项来防止$P_{if},Q_{fj}$过小或过大。

<center>$f(P,Q)=\sum{(R_{ij}-R'_{ij})^2}+\lambda(\sum{(P_{if}^2}+\sum{Q_{fj}^2})$ </center>

接下来就是对这个函数用梯度下降进行拟合，递推式为：
<center>$P_{k+1}=P_{k}-\alpha\frac{\partial f(P,Q)}{\partial P_k}$ </center>
<center>$Q_{k+1}=Q_{k}-\alpha\frac{\partial f(P,Q)}{\partial Q_k}$ </center>

这样我们采用梯度下降算法即可获得$R'$矩阵
```python
import matplotlib.pyplot as plt
import numpy
##将R_nm分解成P_nk*Q_km
def MF(R,P,Q,K,times=100000,alp=0.0001,lb=0.01):
  #  Q=Q.T
    for steps in range(times):# 迭代次数
        for u in range(len(R)):
            for i in range(len(R[u])):
                if R[u][i]>0:
                    delta=R[u][i]-numpy.dot(P[u,:],Q[:,i])
                for f in range(K):
                    P[u][f]=P[u][f]+2*alp*(delta*Q[f][i]-lb*P[u][f])#递推运算
                    Q[f][i]=Q[f][i]+2*alp*(delta*P[u][f]-lb*Q[f][i])
    return P,Q
if __name__ == "__main__":
    R=[
        [5,3,0,1],
        [4,0,0,1],
        [1,1,0,5],
        [1,0,0,4],
        [0,1,5,4]
    ]
    K=2
    n=len(R)
    m=len(R[0])
    ##随机生成P,Q矩阵
    P=numpy.random.rand(n,K)
    Q=numpy.random.rand(K,m)
    ##矩阵分解
    ansp,ansq=MF(R,P,Q,K)
    ansR=numpy.dot(ansp,ansq)
    print(ansR)
```