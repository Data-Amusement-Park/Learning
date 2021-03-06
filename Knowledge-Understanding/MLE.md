# 极大似然估计
MLE(Maximum Likelihood Estimation) 


模型已知，通过实验观测，估计参数。即使得得到这样的实验观测的概率最大的参数最有可能为真实的参数，$max(p(D|\theta))$。

给定样本集合求解随机变量的分布密度函数的参数估计方法可用MLE求解。例如估计正态分布的参数（$\mu , \sigma^{2} $）。

#### 步骤：
1. 写出似然函数；

    > 假设样本集中的样本都是独立同分布的（联合概率为其相乘）：

$$
l(\theta) = p(D|\theta) = p(x_{1}, x_{2}, ..., x_{n}|\theta) = \prod_{i=1}^N p(x_{i}|\theta)
$$

* 对于求解某种概率分布$f(x, \theta)$, 似然函数直接等于$L(\theta)=\prod_{i=1}^N f(x_{i}, \theta)$

2. 对似然函数取对数，并整理；（这个主要是因为将连乘变为连加，求解时求导方便）

$$
{\rm ln} \ l(\theta) = {\rm ln} \prod_{i=1}^N p(x_{i}|\theta) = \sum_{i=1}^N {\rm ln} \ p(x_{i}|\theta)
$$


3. 求解极大似然函数，求导，导数等于0的地方即为解；
$$
\hat{\theta} = {\rm arg} \ {\mathop{\rm max}\limits_{\theta}} \ {\rm ln}\theta
$$

$$
\frac{{\rm d} l(\theta) }{{\rm d}\theta}=0 \quad <=> \quad \frac{{\rm d} {\rm ln} l(\theta) }{{\rm d}\theta}=0
$$

* 若$\theta$为多维，则分别求其偏导=0

4. 解似然方程，得到解即为$\hat{\theta}$。

#### 最大似然估计的特点：
1. 比其他估计方法更加简单；
2. 收敛性：无偏或者渐近无偏，当样本数目增加时，收敛性质会更好；
3. 对于贝叶斯决策，利用MLE估计概率，如果假设的似然概率模型正确，则通常能获得较好的结果。但如果假设模型出现偏差，将导致非常差的估计结果。

---
#### 参考
[极大似然估计详解，写的太好了！](https://blog.csdn.net/qq_39355550/article/details/81809467)