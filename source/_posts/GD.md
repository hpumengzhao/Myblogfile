---
title: 梯度下降法
tags: Machine Learning
mathjax: true
---
# 梯度下降法求解线性回归
<!---more--->
## 梯度下降法

梯度下降法（英语：Gradient descent）是一个一阶最优化算法，通常也称为最速下降法。 要使用梯度下降法找到一个函数的局部极小值，必须向函数上当前点对应梯度（或者是近似梯度）的反方向的规定步长距离点进行迭代搜索。如果相反地向梯度正方向迭代进行搜索，则会接近函数的局部极大值点；这个过程则被称为梯度上升法。

## 梯度下降的形象解释

现在有一个山谷，你想要到达山谷的最低端，你此时在A点，那么此时就可以利用梯度下降来找到最低点。你每次以你当前的方向为基准。选择一个最陡峭的方向，朝着山下降的方向向下走，每次走一段距离，重复执行该步骤，你总能够到达山顶。

![](https://a-1257883661.cos.ap-beijing.myqcloud.com/codancer/20190517111338915.png)

 ## 梯度下降算法原理
原理介绍：
 ### 微分
 
 微分其实就可以看作是函数图像在某点的斜率。有单变量微分和多变量微分
 
<center>$\frac{d(x^2)}{x}=2x$ </center>
<center>$\frac{\partial}{\partial x} (x^2y)=2xy$ </center>
<center>$\frac{\partial}{\partial y}(x^2y)=x^2$ </center>

### 梯度
梯度的本意是一个向量（矢量），表示某一函数在该点处的方向导数沿着该方向取得最大值，即函数在该点处沿着该方向（此梯度的方向）变化最快，变化率最大（为该梯度的模）。

梯度是一个向量。对于某个点的梯度其实就是对每个变量求偏导构成的向量。

<center>$J(\Theta)=1+2\Theta_1-3\Theta_2+4\Theta_3$ </center>
<center>$\Delta J(\Theta)= <\frac{\partial J}{\partial\Theta_1},\frac{\partial J}{\partial\Theta_2},\frac{\partial J}{\partial\Theta_3}> = <2,-3,4>$ </center>

### 梯度下降算法的数学原理
<center>$\Theta_1$=$\Theta_0$ -$\alpha \Delta J(\Theta)$ </center>
公式解释:$\Theta_0$ 表示当前所在的位置,$\Theta_1$表示下一个位置，$\alpha$表示步长，$J$函数就是当前的梯度。减号表示步长的反向，即下坡。

在机器学习中$\alpha$表示学习率或者步长，我们需要通过$\alpha$来控制每一步所走的距离，既不能太快，也不能太慢。

## 梯度下降应用实例

现在我们有一个单变量的函数：

<center>$J(\Theta)=\Theta^2$ </center>

对函数求微分：
<center>$J'(\Theta)=2\Theta$ </center>

设定$\Theta_0=1$，学习率$\alpha=0.4$

根据梯度下降的公式
<center>$\Theta_1=\Theta_0-\alpha*J'(\Theta)$ </center>
我们不断迭代：
<center>$\Theta_0=1$ </center>
<center>$\Theta_1=0.2$ </center>
<center>$\Theta_2=0.04$ </center>
<center>$\Theta_3=0.008$ </center>
<center>$\Theta_4=0.0016$ </center>

经过$4$次迭代，最终结果也接近了函数的最小值。

多变量函数的求解过程和单变量的求解如出一辙。

## 梯度下降求解线性回归

房屋价格与面积（数据在下面表格中）

| 序号 | 面积 | 价格 |
| ------ | ------ | ------ |
| 1 | 150 | 6450 |
| 2 | 200 | 7450 |
| 3 | 250 | 8450 |
| 4 | 300 | 9450 |
| 5 | 350 | 11450 |
| 6 | 400 | 15450 |
| 7 | 600 | 18450 |

使用梯度下降求解线性回归（求$\Theta_0,\Theta_1$）
<center>$h_\Theta(x)=\Theta_0+\Theta_1x$ </center>

我们的目的是使得我们的估计值和实际值相差最小，因此我们定义一个代价函数，这里我们使用均方误差代价函数：
<center>$J(\Theta)=\frac{1}{2m}\sum_{i=1}^m(h_\Theta(x_i)-y_i)^2$ </center>
即：
<center>$J(\Theta)=\frac{1}{2m}\sum_{i=1}^m(\Theta_0+\Theta_1x_i-y_i)^2$ </center>

而其中$h_\Theta(x)=\Theta_0+\Theta_1x$
让函数分别对$\Theta_0,\Theta_1$求偏导。
<center>$\Delta J(\Theta)= <\frac{\partial J}{\partial \Theta_0}, \frac{\partial J}{\partial \Theta_1}>$ </center> 
其中：
<center>$\frac{\partial J}{\partial \Theta_0}=\frac{1}{m}\sum_{i=1}^m(h_\Theta(x_i)-y_i)$ </center>

<center>$\frac{\partial J}{\partial \Theta_1}=\frac{1}{m}\sum_{i=1}^m(h_\Theta(x_i)-y_i)x_i$ </center>

接下来就是代码时间了
```python
import math
m=7 #数据集大小
Theta0=300
Theta1=100
#初始坐标

alpha=0.000000001#学习率
area=[150,200,250,300,350,400,600];#数据集
price=[6450,7450,8450,9450,11450,15450,18450];
def gradientx(Theta0,Theta1):#对Theta0的偏导
    ans=0
    for i in range(0,7):
        ans=ans+Theta0+Theta1*area[i]-price[i]
    ans=ans/m
    return ans
def gradienty(Theta0,Theta1):#对Theta1的偏导
    ans=0
    for i in range(0,7):
        ans=ans+(Theta0+Theta1*area[i]-price[i])*area[i]
    ans=ans/m
    return ans


nowTheta0 = Theta0-alpha*gradientx(Theta0, Theta1)#下一个点的坐标
nowTheta1 = Theta1-alpha*gradienty(Theta0, Theta1)
#print(nowTheta0,nowTheta1)
while math.fabs(nowTheta1-Theta1)>0.000000001:#梯度下降
    nowa = nowTheta0-alpha*gradientx(nowTheta0,nowTheta1)
    nowb = nowTheta1-alpha*gradienty(nowTheta0, nowTheta1)
    nowTheta0=nowa
    nowTheta1=nowb
    nowa = Theta0-alpha*gradientx(Theta0, Theta1)
    nowb = Theta1-alpha*gradienty(Theta0, Theta1)
    Theta0=nowa
    Theta1=nowb
print(nowTheta0,nowTheta1 )
#299.85496413867725 32.638872688242515
```
绘图
```python
import numpy as np
import matplotlib.pyplot as plt

from matplotlib import pyplot
area=[150,200,250,300,350,400,600]#数据集
price=[6450,7450,8450,9450,11450,15450,18450]
pyplot.scatter(area,price)
x=np.arange(100,700,100)
y=32.37648991481203*x+299.85496413867725
pyplot.plot(x,y)
pyplot.xlabel('area')
pyplot.ylabel('price')
pyplot.show()
```
结果：
![](https://a-1257883661.cos.ap-beijing.myqcloud.com/codancer/20190518010738491.png)

我们可以看到梯度下降求解出的线性回归很好的与结果吻合了。

拟合过程（每次的$\Theta_0$和$\Theta_1$）:
![\myplot.png](https://a-1257883661.cos.ap-beijing.myqcloud.com/codancer/myplot.png)