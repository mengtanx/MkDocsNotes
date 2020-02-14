# 线性回归与 Softmax 回归

## 1. 基础概念

### 1.1 线性回归

线性回归输出是一个连续值，因此适用于回归问题。

### 1.2 线性回归的基本要素

* **模型函数**：$\hat{y}=x_{1} w_{1}+x_{2} w_{2}+b$，其中 $w_1$ 和 $w_2$ 是权重 weight 和偏差 bias，且均为标量。模型输出 $\hat{y}$ 是线性回归对真实值 $y$ 的预测或估计。
* **模型训练**：通过数据来寻找特定的模型参数值，使模型在数据上的误差尽可能小，这个过程称为模型训练（model training）。
* **训练数据**：通常是指收集到的一系列真实数据，该数据集被称为训练数据集（training data set）或训练集（training set）。
  * 其中的一条数据称之为一个样本（sample）；
  * 训练集中的真实值 y 称之为标签（label）；
  * 用 来预测标签的因子叫做特征（feature）。
* **损失函数**：在模型训练中，需要衡量预测值和真实值之间的误差。
  * 通常我们会选取一个非负数作为误差，且数值越小表示误差越小。
  * 一个常用的选择是平方函数，单个样本误差的表达式为：$l^{(i)}(\mathbf{w}, b) = \frac{1}{2} \left(\hat{y}^{(i)} - y^{(i)}\right)^2$。
  * 通常，我们用训练集中所有样本误差的平均来衡量模型预测的质量，即：$
L(\mathbf{w}, b) =\frac{1}{n}\sum_{i=1}^n l^{(i)}(\mathbf{w}, b) =\frac{1}{n} \sum_{i=1}^n \frac{1}{2}\left(\mathbf{w}^\top \mathbf{x}^{(i)} + b - y^{(i)}\right)^2$。
* **优化算法**：小批量随机梯度下降（mini-batch stochastic gradient descent）
    1. 先选取一组模型参数的初始值，如随机选取；
    2. 接下来对参数进行多次迭代，使每次迭代都可能降低损失函数的值；
    3. 在每次迭代中，先随机均匀采样一个由固定数目训练样本组成的小批量（mini-batch）数据集；
    4. 然后再求小批量数据集中数据样本的平均损失有关模型参数的导数（梯度）；
    5. 最后用此结果与预先设定的一个正数的乘积作为模型参数在本次迭代的减小量。
* **模型预测**：模型训练完成后，我们得到的不一定是最小化损失函数的最优解，而是对最优解的一个近似估算，也称之为模型预测、模型推断或模型测试。

### 1.3 softmax 回归

> 和线性回归不同的是，softmax 回归的输出单元从一个变成了多个，且引入了 softmax 运算使输出更适合离散值的预测和训练。

softmax 回归是一个离散值，因此适用于分类问题。

### 1.4 softmax 回归的基本要素

* 模型：softmax 回归跟线性回归一样将输入特征与权重做线性叠加。
  * 与线性回归不同的是，softmax 回归的输出值个数等于标签里的类别数。
  * 与线性回归相同的是，softmax 回归也是一个单层神经网络，并且输出层也是一个全连接层。
* softmax 运算
  * 直接使用输出层的两个问题
    1. 由于输出层的输出值的范围不确定，我们难以直观上判断这些值的意义；
    2. 由于真实标签是离散值，这些离散值与不确定范围的输出值之间的误差难以衡量。
  * softmax 运算符（softmax operator）解决了以上两个问题，并通过下述公式将输出值变换成值为正切和为 1 的概率分布：$\hat{y_1},\hat{y_2},\hat{y_3} = softmax(o_1,o_2,o_3)$。
  * 因为 $\operatorname{argmax} o_{i}=\operatorname{argmax} \hat{y}_{i}$，softmax 运算不改变预测类别输出。
* 单样本分类的矢量计算表达式
  * $\boldsymbol{o}^{(i)}=\boldsymbol{x}^{(i)} \boldsymbol{W}+\boldsymbol{b}$
  * $\hat{\boldsymbol{y}}^{(i)}=\operatorname{softmax}\left(\boldsymbol{o}^{(i)}\right)$
* 小批量样本分类的矢量计算表达式
  * $O = XW + b$
  * $\hat{Y} = softmax(O)$
* 交叉熵损失函数
  * 假设训练数据集的样本数为 n，交叉熵损失函数定义为：$\ell(\Theta)=\frac{1}{n} \sum_{i=1}^{n} H\left(\boldsymbol{y}^{(i)}, \hat{\boldsymbol{y}}^{(i)}\right)$。
* 模型预测及评价
  * 在训练好 softmax 回归模型后，给定任一样本，就可以预测每个输出类别的概率。
  * 通常，我们把预测概率最大的类别作为输出类别。
  * 我们可以使用准确率（accuracy）来评价模型的表现，等于正确预测数量与总预测数量之比。

## 2. 代码实现

### 2.1 线性回归（Python）

```python
# import packages and modules
%matplotlib inline
import torch
from IPython import display
from matplotlib import pyplot as plt
import numpy as np
import random

print(torch.__version__)

# set input feature number
num_inputs = 2
# set example number
num_examples = 1000

# set true weight and bias in order to generate corresponded label
true_w = [2, -3.4]
true_b = 4.2

features = torch.randn(num_examples, num_inputs,
                      dtype=torch.float32)
labels = true_w[0] * features[:, 0] + true_w[1] *features[:, 1] + true_b
labels += torch.tensor(np.random.normal(0, 0.01, size=labels.size()),
                      dtype=torch.float32)

def data_iter(batch_size, features, labels):
    num_examples = len(features)
    indices = list(range(num_examples))
    random.shuffle(indices)  # random read 10 samples
    for i in range(0, num_examples, batch_size):
        j = torch.LongTensor(indices[i: min(i + batch_size, num_examples)])
        yield features.index_select(0, j), labels.index_select(0, j)

batch_size = 10

for X, y in data_iter(batch_size, features, labels):
    print(X, '\n', y)
    break

# 初始化模型参数
w = torch.tensor(np.random.normal(0, 0.01, (num_inputs, 1)), dtype=torch.float32)
b = torch.zeros(1, dtype=torch.float32)

w.requires_grad_(requires_grad=True)
b.requires_grad_(requires_grad=True)

# 定义模型
def linreg(X, w, b):
    return torch.mm(X, w) + b

# 定义损失函数
def squared_loss(y_hat, y):
    return (y_hat - y.view(y_hat.size())) **2 / 2

# 定义优化函数
def sgd(params, lr, batch_size):
    for param in params:
        param.data -= lr * param.grad / batch_size  # use .data to operate param without gradient track

# 训练
# 超参数初始化
lr = 0.03
num_epochs = 5

net = linreg
loss = squared_loss

# 训练
for epoch in range(num_epochs):  # training repeats num_epochs times
    # in each epoch, all the sample in dataset will be used once

    # X is the feature and y is the label of a batch sample
    for X, y in data_iter(batch_size, features, labels):
        l = loss(net(X, w, b), y).sum()
        # calculate the gradient of batch sample loss
        l.backward()
        # using small batch random gradient descent to iter model parameters
        sgd([w, b], lr, batch_size)
        # reset parameter gradient
        w.grad.data.zero_()
        b.grad.data.zero_()
    train_l = loss(net(features, w, b), labels)
    print('epoch %d, loss %f' % (epoch + 1, train_l.mean().item()))

```

### 2.2 线性回归（PyTorch）

```python
import torch
from torch import nn
torch.manual_seed(1)

print(torch.__version__)
torch.set_default_tensor_type('torch.FloatTensor')

# 生成数据集
num_inputs = 2
num_examples = 1000

true_w = [2, -3.4]
true_b = 4.2

features = torch.tensor(np.random.normal(0, 1, (num_examples, num_inputs)),                                 dtype=torch.float)
labels = true_w[0] * features[:, 0] + true_w[1] * features[:, 1] + true_b
labels += torch.tensor(np.random.normal(0, 0.01, size=labels.size()), dtype=torch.float)

# 读取数据集
import torch.utils.data as Data

batch_size = 10

# combine features and labels of dataset
dataset = Data.TensorDataset(features, labels)

# put dataset into DataLoader
data_iter = Data.DataLoader(
    dataset=dataset,            # torch TensorDataset format
    batch_size=batch_size,      # mini batch_szie
    shuffle=True,               # whether shuffle the data or not
    num_workers=2,              # read data in multithreading
    )

# 定义模型
class LinearNet(nn.Module):
    def __init__(self, n_feature):
        super(LinearNet, self).__init__()       # call father function to init
        self.linear = nn.Linear(n_feature, 1)   # function prototype: `torch.nn.Linear(in_features, out_features, bias=True)`
    def forward(self, x):
        y = self.linear(x)
        return y

net = LinearNet(num_inputs)
print(net)

# 初始化网络的方法
# ways to init a multilayer network
# method one
net = nn.Sequential(
    nn.Linear(num_inputs, 1)
    # other layers can be added here
    )

# method two
net = nn.Sequential()
net.add_module('linear', nn.Linear(num_inputs, 1))
# net.add_module() ...

# method three
from collections import OrderedDict
net = nn.Sequential(OrderedDict([
    ('linear', nn.Linear(num_inputs, 1))
    # ......
    ]))

print(net)
print(net[0])

# 初始化模型参数
from torch.nn import init

init.normal_(net[0].weight, mean=0.0, std=0.01)
init.constant_(net[0].bias, val=0.0)  # or you can use `net[0].biads.data.fill_(0)` to modify it directly

for param in net.parameters():
    print(param)
loss = nn.MSELoss()     # nn built-in squared loss function
                        # function prototype: `torch.nn.MSELoss(size_average=None, reduce=None, reduction='mean')`
# 定义优化函数
import torch.optim as optim

optimizer = optim.SGD(net.parameters(), lr=0.03)    # built-in random gradient descent function
print(optimizer)    # function prototype: `torch.optim.SGD(params, lr=, momentum=0, dampening=0, weight_decay=0, nesterov=False)`

# 训练
num_epochs = 3
for epoch in range(1, num_epochs + 1):
    for X, y in data_iter:
        output = net(X)
        l = loss(output, y.view(-1, 1))
        optimizer.zero_grad()   # reset gradient, equal to net.zero_grad()
        l.backward()
        optimizer.step()
    print('epoch %d, loss: %f' % (epoch, l.item()))
```

### 2.3 softmax 回归（Python）

* [ ] TODO

### 2.4 softmax 回归（PyTorch）

* [ ] TODO
