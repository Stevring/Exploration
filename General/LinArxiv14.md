# Network in Network

idea: 做卷积的时候，用 Multilayer Perceptron 结构 (fc layer) 代替普通的卷积核。

----

**普通的卷积核有什么问题?**

convolution 可看成从 $\mathbb{R}^n$ 到 $\mathbb{R}$ 的线性变换，然后对变换的结果加一个 non-linear activation 操作。对于一些可以线性分割的特征，convolution works pretty well. 但是实际上很多特征是线性不可分的。 因此作者考虑用 MLP 结构进行特征提取。

----

**输入和输出**

MLP 结构在神经网络中的地位和卷积核完全相同。输入是一张图片，输出是一个 feature map。

-----

**用于 classification problem**

Supppose we have 10 classes, then in the last layer of the nerwork, 10 MLP structures are used to extract 10 feature maps, and an average pooling operation is applied on each of the feature maps. The result is feed into a Softmax layer and we'll get the classification probability for the 10 classes.
$$
f1 = MLP_1(input)\\
f2 = MLP_2(input)\\
...\\
f_{10} = MLP_{10}(input) \\
f_{1, \cdots,10} \in \mathbb{R}^{h\times w}\\
e_i = AveragePooling(f_i)\, , \, i\in(1, \cdots, 10) \\
P = Softmax(\mathbf{e})
$$
**Why average pooling?**

1. no additional parameters, less prone to overfitting
2. feature maps and classification probability of classes are in one-to-one correspondence, easier to interprete.

**<font color='red'>For NLP classification problems</font>**

Maybe Max pooling works better, because NLP features can be really discrete sometimes.

