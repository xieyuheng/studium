---
title: The Little Learner
---

# Foreword by Guy L. Steele Jr.

> This book is _exactly_ right.

> Dan Friedman, with his able and expert co-authors, has
> been writing books in his unique “Little” style for over
> four decades. Every one is a gem, explaining deep and
> important ideas in computer science in bite-sized chunks.
> Dan and his co-authors have raised the “programmed
> learning” question-and-answer format to an art form, to a
> conversational style that seems almost breezy.  This very
> volume introduces two innovations, _nuggets_ and _revision
> charts_, that further streamline the presentation of
> chunks of program code and their behavior.

nuggets 就是代码块。

> The authors themselves remark: “Little” books are all
> about packaging ideas neatly into little boxes.

可否让 AI agent 为某个领域的知识生成 little books？

# Preface

> Deep learning, an emerging area of artificial
> intelligence, has revolutionized the way problems are
> solved, be it winning at Go, recognizing cats in pictures,
> or asking a smart speaker to order pizza. The most
> beautiful thing about deep learning is how simple pieces
> come together to solve large, complex problems. How can we
> understand what makes these deep learning tools work? Our
> approach is to build them, a little bit at a time, and
> watch them work.

> This book builds up a collection of concepts, with nearly
> all of them being functions.

因此「文学式编程」也应该以用函数表达的概念为元素，
而不是以任意的代码片段为元素。

> We also categorize key properties as either _rules_ or
> _laws_. Our rules are about the structure of entities,
> like their sizes. Our laws, on the other hand, are about
> the behavior of entities, like their equalities and
> invariants.

structure knowledge and behavior knowledge of entities。

# 0: Are You Schemish?

复习一下 Scheme。

# 1: The Lines Sleep Tonight

介绍 line 这个简单的函数。

```
y = w x + b
```

```scheme
(define line
  (lambda (x)
    (lambda (w b)
      (+ (* w x) b))))
```

> Here, though, we deal with a different kind of problem,
> where `x` is known, but `w` and `b` must be figured out
> from a bunch of given values of `x` and `y`.

从这个反向的参数顺序，
已经可以看到机器学习的特点了。

形式参数的分类：

- w，b -- parameters
- x -- argument

翻译建议：

- w, b 是「模型参数」，或「可学习参数」
- x 是「自变量」，或「输入」

TODO

# 2: The More We Learn, the Tenser We Become

介绍 Tenser 的概念。

# Interlude I: The More We Extend, the Less Tensor We Get

将作用于标量的函数扩展成可以作用于张量的函数。

# 3: Running Down a Slippery Slope

介绍梯度。

# 4: Slip-slidin’ Away

递归下降法。

# Interlude II: Too Many Toys Make Us Hyperactive

介绍 dynamic scope，可以用来处理递归下降法中的参数。

我们在练习实现的时候可以不用 dynamic scope，
因为这是一个程序语言级别的功能，
而一般的程序语言没有 dynamic scope 这个功能。

在一般的程序语言中，我们可以使用的备选方案是，
直接多带一个参数 `options`，
把需要的所有参数都传到函数里。

# 5: Target Practice

之前用 line 测试了递归下降法，
line 被称为 target，
这章找更多的 target 函数来练习一下。

练习了一个非线性函数 -- 二次函数 quad，
还有一个多元函数 -- 平面 plane。

> **The Rule of Parameters**
>     (Final Version)
> Every parameter is a tensor.

注意 `Parameters` 不是 `Tensor` 而是 `Array<Tensor>`，
因为每个 parameter 的 shape 可能不一样。

> **The Rule of θ**
> θ is a list of parameters
> that can have different shapes.

# Interlude III: The Shape of Things to Come

介绍新的 Tensor 的缩写。

把 rank 为 2 的 tensor 写成矩阵，
然后在旁边标注上 shape。

比如 shape 为 `(3 1)` 的是 column matrix，
而 shape 为 `(1 3)` 的是 row matrix。

# 6: An Apple a Day

用 Sampling 来避免在每次调用 loss 函数的时候都使用整个数据集。

> Using a small random sample of a few points
> from the data set produces a good enough
> approximation of loss, which can be used to
> revise θ. We refer to this sample as a batch
> and its generation as sampling a batch from
> the data set.

注意这里函数复合式的设计，
实现 `samplingObjective` 之后，
并不用修改 `gradientDescent` 的实现，
只需要在使用 `gradientDescent` 的时候用 `samplingObjective` 做复合。
在 Sussman 关于如何灵活地编程的新书中，也有提到类似的技巧。

> This kind of gradient descent where the
> objective function uses sampling is known as
> _stochastic gradient descent_

> **The Law of Batch Sizes**
> Each revision in stochastic gradient descent uses only a batch of
> size batch-size from the data set and the ranks of the tensors in
> the batch are the same as the ranks of the tensors in the data set.

# 7: The Crazy “ates”

抽出来一个 interface，为下一步做准备。

```js
interface Representation {
  inflate,
  deflate,
  update,
}
```

# 8: The Nearer Your Destination, the Slower You Become

上一章和这一章，完美的展示了，如何先把代码打开，然后在修改代码。

> For each desired change,
> make the change easy (warning: this may be hard),
> then make the easy change.
> -- Kent Beck

由于接近极值点的时候梯度太小了，
这里的方案是去积累整个梯度下降路径上的梯度，
每次都乘上一个因子来减少积累的程度。

这种梯度下降称为 momentum gradient descent。

# Interlude IV: Smooth Operator

> So, smooth seems to be a way of
> incorporating historical information that
> is less relevant as we move forward.

# 9: Be Adamant

> Because α (learning rate) represents the fraction of the
> gradient we're going to use as our velocity,
> another approach to addressing this
> problem is to make this fraction _adaptive_.

> Adaptive here means that the fraction is
> decided based on the gradient and its
> historical values.

不用在纠结 `learningRate` 这个常数了，
因为我们讲用历史数据类计算 `adaptiveLearningRate`。

并且可以把这里修改 `learningRate` 的技巧，
和前一章的 momentum gradient descent 结合起来用，
得到 ADMA 算法。

> NOTE ADAM is short for adaptive moment estimation.
> The accompaniment `v` is known as
> the gradient's 1st moment and `r` its 2nd moment.

注意，
这里两个算法的复合并没有那么优雅了，
必须要修改算法定义的细节才能做到。

# Interlude V: Extensio Magnifico!

这里学习如何实现 extended functions。

我不理解为什么不用函数复合，
而是重新从头定义 `of-rank?` 之类的函数。
是为了优化？难道经过测试这里的函数是瓶颈？

我在实现这里的 `extend1` 和 `extend2` 时，
没有先看这章，但是我只是实现了对 Scalar 函数的 extend，
这里实现了对任意 base rank 的函数 extend，
技巧就是把 base rank 作为参数传进来。

```typescript
const mul21 = extend2(mul, 2, 1)
```

`mul21` 与 `mul` 作用于两个 rank 为 2 的 Tensor 的效果并不一样，
`mul21` 可以用来实现 matrix-vector multiplication。

注意，对于 `extend2` 之类的高阶 tensor 函数，
只有当考虑 tensor 的 shape 的时候，
其类型才能有效地约束函数的范围。

比如：

```cicada
mul21: ([m, n], [n]) -> [m, n]
mul21: ([a, m, n], [n]) -> [a, m, n]
mul21: ([m, n], [b, n]) -> [b, m, n]

// 由于 extend2 的定义中两个参数 rank 相等时，
// 被特殊处理为 zip + map 了，
// 这也许是不合理的，因为下面两个作用返回的 shape 一样了。
mul21: ([a, m, n], [a, b, n]) -> [a, b, m, n]
mul21: ([a, m, n], [b, n]) -> [a, b, m, n]
// 也许应该要求相对于两个 base rank 的 extra rank 相等时，
// 才使用 zip + map，从而达到下面的效果：
mul21: ([a, m, n], [a, n]) -> [a, m, n]
// 而按照当前的定义，我们将得到：
mul21: ([a, m, n], [a, n]) -> [a, a, m, n]
```

TODO 用 dependent type 描述 `extend2` 的类型将会是很有趣的挑战。

```cicada
claim extend2: (
  fn: (
    Tensor(firstShape),
    Tensor(secondShape),
  ) -> Tensor(resultShape)
) -> (
  Tensor(???),
  Tensor(???),
) -> Tensor(???)
```

# 10: Doing the Neuron Dance

如何构造更复杂的，模仿人类神经网络的函数，作为 target 函数呢？

用函数复合就可以吗？

称 activation functions 为 deciders，
activation 来自神经网络的比喻，
但是 decider 这样的命名更接地气。

> Functions like relu are known
> as artificial neurons. Each
> neuron has a linear part, like
> linear, and a nonlinear
> decider like rectify.

引入 relu 函数。

> **The Rule of Artificial Neurons**
> An artificial neuron is a parameterized linear function
> composed with a nonlinear decider function.

> Let's see an illustration of
> how multiple uses of relus
> can be combined to do more interesting things.

获得用 relu 的复合模拟更复杂的函数的直觉。

用 `halfStrip` 和 `fullStrip` 为例子，
因为根据勒贝格（Lebesgue）积分的方案，
任何函数都可以被分解为 `halfStrip` 和 `fullStrip`，
即 piecewise-linear approximation。

这类方案叫做 universal approximation。

# 11: In Love with the Shape of Relu

这一章学习神经网络的 layer，
layer function 是特殊的 target function，
有一样的类型声明。

有不同类型的 layer：

- dense layers (fully-connected layers)

  In general, the layer function of a dense
  layer with `m` neurons takes one input tensor of shape `[n]`,
  and parameters weight of shape `[m, n]` and bias of shape `[m]`,
  and invokes each of those `m` neurons on input,
  to produce output tensor of shape `[m]`.

  We say that the width of this layer is `m`.

> **The Law of Dense Layers**
>       (Initial Version)
> A dense layer function invokes m neurons on an n-element
> input tensor-1 and produces an m-element output tensor-1.

> **The Law of Dense Layers**
>       (Final Version)
> A dense layer function invokes m neurons on an n-element
> input tensor-1 that produces an m-element output tensor-1
> in a single invocation of *-2-1.

注意，这里对 `linear` 的定义要用 `matrixVactorMul`，而不能用 `dot`。

观察这里 `matrixVactorMul` 和 `dot` 的定义，
也许我们在处理 tensor 的函数的时候，
应该放弃传统线性代数中，只有行向量与列向量才能 `dot` 的限制，
也许行列向量的发明只是为了书写矩阵乘向量的时候方便。

> The list of shapes of the tensor2 and
> tensor1 parameters necessary for a layer is
> known as the _shape list_ of the layer.

在带有 dependent type 的语言中，
shape list 就是 layer 的类型参数。

这里很多篇幅用来处理 `k-relu` 的 currying，
看来带有自动 currying 的语言是必要的。

> The problem is that network functions, as
> in 1-relu, 2-relu, and 3-relu above, must
> take their arguments t and θ one at a time.

# 12: Rock Around the Block

前一章定义的 `k-relu`，
给出了 `k` 之后，
对所接受的 parameters 完全没有约束。

parameters 的 shape list 必须作为分离的 metadata 给出，
这一章我们改进这一点。

> We begin by introducing blocks.
> (A block is short for network building block.)
> A block associates a layer function with its shape list.

可以说是带有一些运行时的类型信息了，
也许可以在 compose block 的时候用来检查 shape 是否符合？

- 其实不是，在做 composition 的时候，
  shape list 只是简单地被 append 到一起，
  shape list 之间并没有什么相互的约束（constraints）。

> Now let's see the real magic in these blocks.
> A neural network is fully described by a
> network function and a shape list.

> Oh, that means a neural
> network can also be a block!
> So stacking blocks together
> also produces another block.

找到了！什么？
范畴的态射。
那是带有 shape list 的 layer functions。

有 composition 的地方，就有范畴与它的态射，
而上一章的 `k-relu` 显然是在做 composition，
因此要找范畴与态射。

但是，如果真的是范畴的话，
单位元是什么？
就是对 输入 tensor 而言的 identity function，
并且忽略 parameters。

想要成为范畴，
还需要检查复合是满足结合律的，
这是显然的。

> **The Law of Blocks**
> Blocks can be stacked to form bigger blocks
> and complete networks.

# 13: An Eye for an Iris

> Degrees of belief is the confidence we have
> about a certain statement.

> In general, the layers closer to the input are
> wider than the layers closer to the output.

> The layers closer to the input are responsible
> for learning some of the more primitive
> characteristics of the data set, and the layers
> closer to the output learn more advanced
> characteristics based on the output produced
> by the earlier layers.

Breaking the symmetry between neurons
-- 使用随即的初始 weights。

He initialization for networks that use rectify:

> ... that formula makes sure that in deep
> networks, the weights stay in a very tight
> cluster around 0.0 so that when a scalar from
> the input tensor is multiplied with weights in
> each layer, the result neither explodes nor
> vanishes.

> **The Rule of Layer Initialization**
>          (Initial Version)
> The bias tensor-1 of a layer is initialized to contain only 0.0
> The weight tensor-2 of a layer is initialized to random scalars
> with a central value of 0.0 and a variance of 2/n where n is the
> length of the input to the layer.

用部分数据来训练，用另一部分数据来测试。

> On larger data sets, the general guideline
> is to use 20% of the data set for testing.
> Since our data set here is quite small, we
> limit the test set to 10%.

> A model is an approximation of an
> _idealized_ function represented by the data
> set. This idealized function yields, for
> every x in the xs of the data set, the
> corresponding y from ys, but also
> produces a y for any given x, even if it is
> not in xs.

这一章的 `randomTensor` 需要用到正态分布。
要写一个能生成符合正态分布的随机数的函数。

引入 model 的概念，
每次 training 都会获得一个 model，
但是只是用函数来实现 model 可能是不合适的，
因为好的 model 的 parameters 应该被保存下来。

# Interlude VI: How the Model Trains

> [for classifiers]
> We say that the model is accurate for
> those inputs where the two classes are
> the same.

> When the classes are not the same,
> we call them _classification errors_.

> The ratio of the number of accurate
> classifications to the total number of
> test inputs we have is known as the
> _accuracy_ of the model.

当决定算法的合适参数（options）时，
可以尝试不同的参数组合，
然后用 accurate 来判断参数是否合适。

> This way of testing different
> combinations for the best one is known as
>     _grid search_.

尽管这里把 `grid-search` 定义为了一个语法关键词，
但是也有用函数实现 `grid-search` 的方案，
并且不依赖 dynamic scope。

这里像是 `grid-search` 这种基于实验的方案，
让我感到很不舒服，也许是因为与（可以被机器学习的） parameters 相比，
人们对这些 hyperparameters 的理解并没有那么透彻，
也许科学的探索就是需要这些实验。

# Interlude VII: Are Your Signals Crossed?

利用神经网络，来解码带有噪声的信号。

介绍莫尔斯码，以及用 tensor 表示莫尔斯码的方式。

为了在书中给出 signal 例子时方便，
还设计了一种代表 signal 的 tensor 的缩写，
这也是一种简单的压缩数据的方案。

`tenserZip` -- `[d, n] -> [n, d]` -- 与矩阵转置类似。

> **The Law of Zipped Signals**
> A signal-2 is formed by zipping signal-1s, and the signal-2 as well
> as its constituent signal-1s all have the same number of segments.

由于信息开始的时间可能是任意的，
所以需要使用卷积来消除信号中开始时间的差异。

# 14: It's Really Not That Convoluted

> To decode Alice and Bob's messages,
> we need to now learn about _correlation_
> between two 1-dimensional signals.

> Correlation is a way to detect the
> occurrence of a pattern anywhere within
> a signal.

> We detect a pattern by _scanning_ the
> signal from beginning to end and
> measuring the similarity between a
> portion of the source signal and the
> pattern.

假设有一个已知的信号模式（pattern signal），
将它在需要被识别的对象信号（target signal）上扫过（scan），
然后逐个度量当前信号和信号模式的耦合程度（correlation），
就能在对象信号上找到最符合信号模式的位置。

可以想像，在带有卷积网络中，
开始的时候 pattern signal 是未知的，
需要在学习的过程中找到，因此它们是 parameters。

target signal 在书中被称做 source signal，
pattern single 也叫做 filter 或者 kernel。

注意在 scan 的时候，
要用一半 pattern 从边界开始对比，
这样能够使得结果信号（result signal）
与 target signal 的长度相等，
也能够囊括边界上的 partial match。

> **The Law of Correlation**
>   (Single Filter Version)
> The correlation of a filter of length m with a source signal1 of
> length n, where m is odd (given by 2p + 1), is a signal1 of
> length n obtained by sliding the filter from overlap position −p
> to overlap position n − p − 1, where each segment of the result
> signal1 is obtained by taking the dot product of the filter and the
> overlap in the source at each overlap position.

> What are features?
> Features are specific kinds of patterns
> that we look for in the source.

刚好我最近的工作在处理属性图（attributed graph）
中的特征识别（feature recognition），
很巧在这里遇到 feature 一词。
也许属性图中的特征也可以用卷积神经网络来识别。

> **The Rule of Filters**
> Filters are tensor parameters in a θ.

> In a typical neural network classifier,
> we're simultaneously trying to detect
> multiple features in a single source.

多个 shape 相同的 filter
可以组成一个 filter bank tensor (or just bank)。

> **The Law of Correlation**
>    (Filter Bank Version)
> The correlation of a filter bank of shape (list b m d) with a
> source signal2 of shape (list n d) is a signal2 of shape (list n b)
> resulting from zipping the b signals1 resulting from correlating
> the b filters2 in the bank with the source.

为了保持多个卷积 layer 可以复合，
对结果做了一次 sum。

TODO 以图片识别为例，从直觉上解释高维度 single 的 correlation。

# 15: …But It Is Correlated!

这两章的标题很棒。

> It's Really Not That Convoluted.
> But It Is Correlated!

因为 CNN 的 C 在原始的论文中代表 convolution（mirrored correlation），
但是在现代的实现中，没有必要做 mirror。

> Since filters are learned during training of
> the network, it does not matter if we mirror
> them or not -- they are learned in the
> appropriate direction. So we can avoid the
> mirroring, leaving just the correlation.

TODO

# Epilogue - We've Only Just Begun

## 1 Mathematical foundations

The most relevant mathematical fields:

- Linear Algebra
- Probability and Statistics
- Vector Calculus

Condensed presentation of the necessary mathematics:

- Deep Learning [^1]

Learning from first principles:

- Coding the Matrix [^2]
- Bayesian Statistics the Fun Way [^3]

## 2 Data-generating distributions

> Data sets are a curious entity. They appear like there's nothing
> particularly interesting about them. They just contain a large
> number of points. But in reality, data sets provide us with a
> sampling of the outputs of a _data-generating process_.

> These data-generating processes can be understood in probabilistic
> terms. We associate them with an ideal _probability distribution_,
> which maps each point being generated by the process to the
> probability of its occurrence. We call this the _data generating
> distribution_.

这里对 probability distribution 的定义很棒，

> [it] maps each point being generated by the process
> to the probability of its occurrence.

> The process of learning the θ of a neural network constructs an
> internal approximation of this data generating distribution, and
> applies it to the specific task at hand.

有了所学习到的参数 θ，
就确定了所学习到的函数，
就能重新用来生成数据了。

## 3 Tasks

> Deep learning is all about doing interesting things. The cognoscenti
> have given a somewhat boring name to these interesting things. They
> call them _tasks_.

给机器学习所能处理的问题简单分类：

- Regression（通过函数拟合来做预测）
- Classification（比如鸢尾花的分类问题）
- Translation（比如莫尔斯电码到拉丁字母的的翻译）
  - Style Transfer（比如图片美化，比如自动生成梵高风格的画）
- Generation（比如现在最火的 ChatGPT，生成图片，自动谱曲，等等）

> Neural networks are always designed for a specific task that drives
> the choice of underlying functions and structures. Here we discuss
> some of the ideas that influence the design space.

## 4 Other loss functions

> Cross-entropy loss is usually used in conjunction with a softmax
> decider (we'll see this one soon). In combination, these two
> functions enable training a network with more accuracy and using
> fewer revisions.

> This function [l1-loss] can be used when we don't want outliers in
> the dataset to disproportionately influence the loss.

## 5 More deciders

> The only decider in previous chapters is _rectify_. While it has a
> number of very useful properties and is extremely simple, _rectify_
> is a rather recent development. Earlier neural networks relied on
> nonlinear functions, for example, the _logistic sigmoid_ and the
> hyperbolic _tanh_.

leaky rectify 对于避免某些训练时出现大量 0 的情况。

有方案[^6]可以通过学习获得 leaky rectify 中的反向倾斜角的角度，
而不是作为一个在训练过程中不变的 hyperparameter。

> This function [softmax] always produces a tensor1 that meets the
> criterion of a probability distribution -- each element in it is
> between 0.0 and 1.0, and the elements sum to 1.0. Because of this
> property, a softmax decider is used in conjunction with the
> cross-entropy loss function (which expects a probability
> distribution), making it ideal for the last layer of a classifier
> neural network.

## 6 Higher-dimensional signals

一维的 signal 包括：

- sounds
- seismic vibrations
- variability of temperature

高维的 signal 包括：

- images
- videos

> Higher-dimensional signals give rise to networks that have much
> larger θs since we're now dealing with a much larger amount of
> data. It is not uncommon to see θs containing hundreds of millions
> of scalar parameters in them.

Examples of these networks that make for
extremely interesting architectural explorations:

- AlexNet [^7]
- ResNet [^8]
- VGG [^9]

## 7 Natural language systems

> Text-based tasks such as translation and text classification require
> the processing of natural languages. A neural network designed for
> natural language processing usually relies on very specific layer
> structures that allow the network to learn the relationship between
> words in different parts of a piece of text.

> These networks can learn how ideas are threaded through a sequence
> of sentences and reproduce them as necessary in tasks like
> translation and summarization. Networks such as GPT-3 [^10] carry
> billions of scalars in their θs and are able to handle very complex
> tasks.

> There are two main kinds of architectures used for text processing.
> There is a _recurrent_ neural network architecture that uses an
> output associated with a prior token for processing a future token.
> This allows the network to develop information about the context in
> which a given token appears.

> Recurrent networks are rapidly being replaced by a newer form of
> network based on the idea of _attention_ [^11]. The difference in
> attention-based networks is that they work on whole sequences rather
> than one token at a time. They include a processing block known as a
> _transformer_ that uses attention to provide a mechanism with which
> the network can learn how each token in the input sequence
> influences other tokens.

构造一个简单的使用 attention 和 transformer 的网络，
可以看作是 The Little Learner 的有趣练习，
毕竟现在 ChatGPT 非常火。

## 8 Generative networks

有两种主要的 generative network architectures：

- variational autoencoder [^12]
- generative adversarial network, or GAN [^13]

variational autoencoder 有 encoder 和 decoder 两部分：

- encoder 压缩数据成 code，
- decoder 可以反过来把随机的 code
  还原成类似原数据的数据。

这听起来比 ChatGPT 还有趣，感觉类似于人类的想象力，
比如发现新的图片，乐曲，字体等等。

要知道，人们发现新的数学也在于想象力，
可否用这种 network 来发现新的数学呢？

generative adversarial network 也有两部分：

- generator -- 把随机数变为带有想要属性的数据，
- discriminator -- 用来筛选所生成的数据。

> The clever bit about GANs is that their training is designed in a
> way that both penalizes the discriminator if it is wrong in making a
> judgment and penalizes the generator if it generates the wrong kind
> of output.

generative adversarial network（生成对抗网络），
中的 adversarial（对抗） 就在于此。

这个 clever bit 听起来也是有趣的 idea，具体如何实现呢？

> Training the discriminator and generator together ensures that the
> generator learns how to produce samples that the discriminator will
> accept and the discriminator learns to accept only samples that
> satisfy the application requirements.

## 9 Practical things

> One of the most important elements of the design of a neural network
> is its _capacity_, which can roughly be thought of as the number of
> scalars in its θ.

> If the network has too few, then it won't learn enough about the
> training set, a condition known as _underfitting_.  If it has too
> many, then it will learn to be very specific to the training set,
> a condition known as _overfitting_.

> Even if a network has the appropriate capacity, training it with too
> few revs can lead to underfitting, while training it with too many
> can lead to overfitting.

> Either underfitting or overfitting or both might cause the network
> to not perform optimally. The network will fail to _generalize_ to
> points that lie outside the training set. Selecting networks with
> the right capacity and choosing the hyperparameters carefully is a
> very critical part of the network training process.

这里，可能是因为我们还欠缺足够强大的数学理论来理解 hyperparameters，
因此实验就很重要，最好不要满足于找到合适的 hyperparameters，
而是要勇于做关于 hyperparameters 的假设，
然后用实验数据来验证假设。

这样就和科学研究非常类似了。

实用的技巧：

> First, it always helps to properly track the metrics of a network as
> it trains.  Tracking the training loss, i.e., the value produced by
> the loss function at each of the revs, helps follow how well the
> network is training.

设计结构化的 log 来记录实验过程。

> Second, using a proper validation set to track the relevant
> performance metric associated with the network (such as accuracy of
> predictions) helps determine if the training is proceeding
> correctly.

比如之前用到的 grid-search。

> Third, techniques like _regularization_ are used to manage the
> effectiveness of training. Regularization is the name given to a
> technique of adding an additional term to the loss function that
> influences the direction the gradient descent takes. See Good-fellow
> et al. [^1] for examples of regularization.  This helps train the
> network without under- or over-training it, which improves the
> overall performance of the network for its designated task.

> Fourth, initialization of weights is a significant part of
> training. He et al. [^6] proposed the initialization scheme used in
> this book. This scheme is useful with rectifier-based networks.
> Other schemes such as the one proposed by Glorot et al. [^14] help
> with other types of networks.

## 10 Onwards, little learners!

> This epilogue is a whirlwind tour of the topics and sources for
> curious readers to pursue. With the understanding of neural networks
> gained from this book, we hope these topics are less formidable than
> they would otherwise have been.

> À bientôt

# Appendix A: Ghost in the Machine

介绍了利用 Dual Number 进行自动微分的方法，
不用对函数复合而成的表达式做符号处理，
只需要扩展 Number 的定义，
并且再扩展 primitive function，
不需要改变 compound function 的定义，
就能完成自动微分。

据说这个方法是 Clifford 发明的，
Sussman 在讲经典力学的 Scheme 书中也用了这种方法。

在无类型语言中，
可以自由地扩展 primitive function
（需要小心避免递归定义）。
在带有类型检查的语言中，
可能需要重新定义一套 primitive function
（正如我们在 JS/TS 实现中做的那样）。

我们在什么地方可以用类似的技巧呢？

注意不用修改已有的定义就能做扩展，
也是 propagator 的主题。

# Appendix B: I Could Have Raced All Day

TODO

# References

[^1]: I. Goodfellow, Y. Bengio, and A. Courville, Deep Learning. MIT Press, 2016.

[^2]: P. N. Klein, Coding the Matrix: Linear Algebra Through Applications to Computer Science. Newtonian Press, 2013.

[^3]: W. Kurt, Bayesian Statistics the Fun Way. No Starch Press, Inc., 2019.

[^4]: L. Hui and M. Belkin, “Evaluation of neural architectures trained with square loss vs cross-entropy in classification tasks,” in 9th International Conference on Learning Representations, ICLR 2021, OpenReview.net, 2021.

[^5]: A. L. Maas, “Rectifier nonlinearities improve neural network acoustic models,” 2013.

[^6]: K. He, X. Zhang, S. Ren, and J. Sun, “Delving deep into rectifiers: Surpassing human-level performance on imagenet classification,” in Proceedings of the IEEE International Conference on Computer Vision (ICCV), December 2015.

[^7]: A. Krizhevsky, I. Sutskever, and G. E. Hinton, “Imagenet classification with deep convolutional neural networks,” in Advances in neural information processing systems, 2012.

[^8]: K. He, X. Zhang, S. Ren, and J. Sun, “Deep residual learning for image recognition,” in IEEE Conference on Computer Vision and Pattern Recognition (CVPR), 2016.

[^9]: K. Simonyan and A. Zisserman, “Very deep convolutional networks for large-scale image recognition,” in International Conference on Learning Representations, 2015.

[^10]: T. Brown, B. Mann, N. Ryder, et al., “Language models are few-shot learners,” in Advances in Neural Information Processing Systems (H. Larochelle et al., eds.), vol. 33, Curran Associates, Inc., 2020.

[^11]: A. Vaswani, N. Shazeer, N. Parmar, et al., “Attention is all you need,” in Advances in Neural Information Processing Systems (I. Guyon et al., eds.), vol. 30, Curran Associates, Inc., 2017.

[^12]: D. P. Kingma and M. Welling, “Auto-Encoding Variational Bayes,” in 2nd International Conference on Learning Representations, ICLR 2014, Banff, AB, Canada, April 14-16, 2014, Conference Track Proceedings, 2014.

[^13]: I. Goodfellow, J. Pouget-Abadie, et al., “Generative adversarial nets,” in Advances in Neural Information Processing Systems (Z. Ghahramani, M. Welling, et al., eds.), vol. 27, Curran Associates, Inc., 2014.

[^14]: X. Glorot and Y. Bengio, “Understanding the difficulty of training deep feedforward neural networks,” in Proceedings of the Thirteenth International Conference on Artificial Intelligence and Statistics, PMLR, 2010.
