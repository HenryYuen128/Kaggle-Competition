## Summary

### 特征工程中的一些技巧：
	- 年龄是连续型的特征，但根据统计图可以得知小孩的存活率是很高的。因此，可以将年龄进行离散化（按区间分组，再设置成类目型变量）。
	- 年龄的缺失值较多，有两种方法填充缺失值：（1）可以根据其他特征（如票价，仓位等级等）进行拟合。（2）离散化后，缺失值作为一种类型。
	- 二值类型特征（如性别）使用独热编码。
	- 哑变量 (dummy varible) 如特征有三种取值（A,B,C），可以生成三种新特征（A, B, C），特征值为A的A属性取值为A，B,C属性取值为0.

### L1和L2正则化
#### basline model 使用 logistic regression，注意到sklearn中有penalty参数（惩罚项），L1和L2正则项的区别：
	- L1正则容易产生稀疏参数矩阵，起到简化模型的作用（即可能有大量参数为0），起到了特征筛选的作用。
	- L2正则中参数可能出现很接近0的情况，但不会等于0。

![image](https://github.com/HenryYuen128/Kaggle-Competition/blob/master/pic/l1andl2.png)

左图中是带L1正则化的解空间，右图是带L2正则化的解空间。彩色线是误差函数的等高线，左图正方形是L1正则的等值线，右图圆形是L2正则的等值线。两图形的切点（P1）是损失函数最小化的解。L1正则的切点容易出现在数轴上，因此比较容易出产生稀疏解。L2正则是两个圆，切点较小可能在数轴上。

关于L1为什么比L2正则更容易获得稀疏解的解释：
https://www.zhihu.com/question/37096933

假设损失函数 L 与某个参数 x 的关系如图所示：

![image](https://github.com/HenryYuen128/Kaggle-Competition/blob/master/pic/costFunction.jpeg)

则最优的 x 在绿点处，x 非零。
现在施加 L2 regularization，新的损失函数（L+Cx²）如图中蓝线所示：

![image](https://github.com/HenryYuen128/Kaggle-Competition/blob/master/pic/l2%20regularization.jpeg)

最优的 x 在黄点处，x 的绝对值减小了，但依然非零。
而如果施加 L1 regularization，则新的损失函数（L+C|x|）如图中粉线所示：

![image](https://github.com/HenryYuen128/Kaggle-Competition/blob/master/pic/l1%20regularization.jpeg)

最优的 x 就变成了 0。这里利用的就是绝对值函数的尖峰。
两种 regularization 能不能把最优的 x 变成 0，取决于原先的损失函数在 0 点处的导数。
如果本来导数不为 0，那么施加 L2 regularization 后导数依然不为 0，最优的 x 也不会变成 0。
而施加 L1 regularization 时，只要 regularization 项的系数 C 大于原先损失函数在 0 点处的导数的绝对值，x = 0 就会变成一个极小值点。
上面只分析了一个参数 x。事实上 L1 regularization 会使得许多参数的最优值变成 0，这样模型就稀疏了。

L1和L2正则化都可以解决过拟合的问题，但方式不同：
L1通过抛弃某些特征，L2通过控制所用权重的和的大小

### 集成学习
集成学习有三种常用的方式：Bagging, Boosting, Stacking

#### Bagging
给定一个大小为n的训练集 D，Bagging算法从中均匀、有放回地选出m个大小为n的子集Di，作为新的训练集。在这 m个训练集上使用分类、回归等算法，则可得到 m个模型，再通过取平均值、取多数票等方法综合产生预测结果，即可得到Bagging的结果。

![image](https://github.com/HenryYuen128/Kaggle-Competition/blob/master/pic/bagging.jpg)

#### Boosting
加入的过程中，通常根据它们的上一轮的分类准确率给予不同的权重。加和弱学习者之后，数据通常会被重新加权，来强化对之前分类错误数据点的分类，其中一个经典的提升算法例子是AdaBoost。

![image](https://github.com/HenryYuen128/Kaggle-Competition/blob/master/pic/boosting.jpg)

#### Stacking
用训练集中**部分数据**对j个baseline模型进行训练（使用全部训练集会造成过拟合的问题），第j个baseline模型对第i个训练样本的预测值将作为新的训练集中第i个样本的第j个特征值，最后基于新的训练集使用新模型进行训练。同理，预测的过程也要先经过所有baseline模型的预测形成新的测试集，最后再对测试集进行预测

![image](https://github.com/HenryYuen128/Kaggle-Competition/blob/master/pic/stacking.png)
