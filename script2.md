>>> 前向传播总览 #B08
@enter: fade-up
@exit: fade
@visual: image(./generated_images/image8.png)

--- visual ---
（文档参考：实际使用 `./generated_images/image8.png`，构建时复制该文件，不参与文生图。）
前向传播总览：嵌入 → 注意力 → MLP → 输出 logits。

--- narration ---
上一集我们说，模型就是 4000 多个随机数字。
这一集，我们把一段文字喂进去，看看这些数字是怎么被用起来的。
这个过程叫 **前向传播**，forward pass。
它把「输入字符」和「模型参数」组合起来。
最终输出一个预测：下一个字符是什么。
整个流程分四步：**嵌入**、**注意力**、**前馈网络**、输出。
我们一步一步来。


>>> 嵌入：把字符变成向量 #B09
@enter: fade-up
@exit: fade
@visual: image(./generated_images/image9.png)

--- visual ---
（文档参考：实际使用 `./generated_images/image9.png`，构建时复制该文件，不参与文生图。）
嵌入：token 表 wte + 位置表 wpe，相加得到输入向量。

--- narration ---
你给模型一个字符，比如 e，它的 token id 是 4。
模型不能直接用这个 4。
4 和 5 之间没有天然的意义关系，它们只是查找表的下标。
模型需要把这个 id 翻译成一个 **向量**，一组数字。
代码里就两行：从 **wte** 这张 27 行 16 列的表里，取出第 4 行。
那是字符 e 的 16 维含义向量。
但只有「是哪个字符」还不够。
**abc** 和 **cba** 用的字符一样，意思完全不同。
所以我们再查第二张表 **wpe**，告诉模型这个字符在第几个位置。
位置 0 有它的 16 维向量，位置 1 有另一组，依此类推。
把「字符向量」和「位置向量」加起来，就是这一步要送进 Transformer 的输入。
这一个向量同时编码了 **是什么字符** 和 **在哪个位置**。


>>> Transformer Block 的整体结构 #B10
@enter: fade-up
@exit: fade
@visual: image(./generated_images/image10.png)

--- visual ---
（文档参考：实际使用 `./generated_images/image10.png`，构建时复制该文件，不参与文生图。）
单层 Transformer Block：RMSNorm、注意力、残差、MLP、残差与输出。

--- narration ---
Transformer 由若干个相同的 block 堆叠而成。
GPT-4 有几十层。**microgpt** 只用了一层，但结构是一样的。
每一个 block 干两件事。
先做 **注意力**，再过一个小的 **前馈网络（MLP）**。
这两步前面各做一次归一化。
每一步之后，结果都会和这一步的输入相加。
这叫 **残差连接**，等会儿讲。
注意力是 Transformer 最关键的发明。
我们重点讲它。


>>> Q、K、V：注意力的三个角色 #B11
@enter: fade-up
@exit: fade
@visual: image(./generated_images/image11.png)

--- visual ---
（文档参考：实际使用 `./generated_images/image11.png`，构建时复制该文件，不参与文生图。）
Q、K、V：由 x 经 wq、wk、wv 得到三组向量。

--- narration ---
注意力机制要解决的问题是：
**当前位置该从前面哪些字符里取信息？**
举个例子。模型看到 j-o-h-n，正在预测第 5 个字符。
它「回头看」前面的 j、o、h、n。
这四个字符里，哪个对预测最有帮助？
为此，模型给 **每个字符** 都算三个向量。
**Query**（查询）：我要找什么。
**Key**（键）：我能提供什么。
**Value**（值）：如果你看上我，我给你的内容。
这三个向量都从同一个输入向量出发。
分别乘以三个不同的矩阵 **wq**、**wk**、**wv**，得到三个向量。
这三个矩阵就是模型要学的参数。
训练完毕后，wq 学会了该提什么样的问题。
wk 学会了该亮什么样的旗子，wv 学会了该传什么样的消息。


>>> 打分与加权：注意力的核心运算 #B12
@enter: fade-up
@exit: fade
@visual: image(./generated_images/image12.png)

--- visual ---
（文档参考：实际使用 `./generated_images/image12.png`，构建时复制该文件，不参与文生图。）
注意力：Q 与 K 打分 → Softmax → 对 V 加权求和。

--- narration ---
有了 Q、K、V，注意力做这样一件事：
**当前位置的 Q，去和前面所有位置的 K 做点积。**
点积值高，说明这两个位置很匹配。低，说明不太相关。
代码里这一步算出来的叫 **attention logits**。
它是一组分数，每个位置一个。
算分时会除以一个常数，也就是根号 head_dim。
这样做是为了让数值不要爆炸，保持训练稳定。
得到一串分数后，用 **softmax** 把它变成一个概率分布。
这些概率加起来正好是 1。
然后用这个概率，对所有位置的 V 做 **加权求和**。
这就是注意力的输出。
当前位置融合了所有相关位置的信息，融合权重由相似度决定。
一句话总结：**注意力** 等于我先看谁和我有关。
然后按关系紧密程度，把他们的信息加权拿过来。


>>> 多头：让模型从多个角度看 #B13
@enter: fade-up
@exit: fade
@visual: image(./generated_images/image13.png)

--- visual ---
（文档参考：实际使用 `./generated_images/image13.png`，构建时复制该文件，不参与文生图。）
多头注意力：多路并行 → concat → wo → 输出。

--- narration ---
刚才讲的是「一头」注意力。
实际中，我们让模型同时跑 **多头**——microgpt 是 4 头。
为什么要多头？
一头注意力可能学到「看前一个字符是什么」。
另一头可能学到「看名字开头是什么」。
再一头可能学到「看辅音元音的搭配」。
多头让模型在不同的子空间各看各的，最后把它们的结果 **拼接** 起来。
每一头处理的维度更小。
16 维分给 4 头，每头 4 维。
但关注的角度不同，加起来比单头更丰富。
最后再过一个矩阵 **wo**。
把多头的拼接结果再混合一次，作为整个注意力模块的输出。


>>> 残差连接与 RMSNorm #B14
@enter: fade-up
@exit: fade
@visual: image(./generated_images/image14.png)

--- visual ---
（文档参考：实际使用 `./generated_images/image14.png`，构建时复制该文件，不参与文生图。）
残差连接与 RMSNorm：稳定深层训练的两项技巧。

--- narration ---
注意力之后有两个细节，不讲清楚就会看不懂。
第一个是 **残差连接**：注意力的输出要加回它的输入。
代码里就是 x 等于对应元素相加的那种写法。
为什么？模型很深的时候，信号一层层往下传容易丢失。
反向传播时，梯度也容易衰减消失。
残差连接让原始信号有一条「短路」通道，可以直接传到后面。
这是深层网络能训得动的关键技巧。
第二个是 **RMSNorm**。
在每一步注意力和 MLP **之前**，先把向量归一化。
保持方向不变，但把幅度规整到一个稳定的范围。
它防止训练过程中数值越变越大或越变越小。
这两个技巧本身不增加模型表达能力。
但没它们，深层模型几乎训不出来。


>>> MLP：非线性与维度变换 #B15
@enter: fade-up
@exit: fade
@visual: image(./generated_images/image15.png)

--- visual ---
（文档参考：实际使用 `./generated_images/image15.png`，构建时复制该文件，不参与文生图。）
MLP：16 → 64 → ReLU → 压回 16，引入非线性。

--- narration ---
注意力之后，每个 block 还有一个小的前馈网络。
也就是 **MLP**。
先把 16 维放大到 64 维。
过一个 **ReLU** 激活函数，再压回 16 维。
它的作用是给模型增加 **非线性** 表达能力。
光做线性变换是学不到复杂规律的。


>>> 输出：lm_head 与 logits #B16
@enter: fade-up
@exit: fade
@visual: image(./generated_images/image16.png)

--- visual ---
（文档参考：实际使用 `./generated_images/image16.png`，构建时复制该文件，不参与文生图。）
lm_head：16 维隐状态 → 27 维 logits，下一字符打分。

--- narration ---
走完所有 block，我们得到一个 16 维的向量。
最后一步，乘上 **lm_head** 矩阵，把它映射成 **27 个数**。
对应 27 个候选字符（26 个字母加上 BOS）。
这 27 个数叫 **logits**。
谁的值最高，模型就觉得下一个字符最可能是它。


>>> Loss：交叉熵告诉模型错了多少 #B17
@enter: fade-up
@exit: fade
@visual: image(./generated_images/image17.png)

--- visual ---
（文档参考：实际使用 `./generated_images/image17.png`，构建时复制该文件，不参与文生图。）
交叉熵损失：对正确答案概率取负对数，得到标量 loss。

--- narration ---
但模型刚开始是随机的，它的预测大概率是错的。
我们怎么用一个数字告诉它错了多少？
答案是 **cross entropy loss**，交叉熵损失。
具体做法：把 logits 过一次 **softmax** 变成概率分布。
然后取出 **正确答案那个字符的概率**，对它取负对数。
如果模型给正确答案分配了 0.9 的概率，loss 很小。
如果它给正确答案分配了 0.001 的概率，loss 就非常大。
整个序列的 loss 是每个位置 loss 的平均。
这个数字，就是模型本次考试的量化成绩。
**loss** 越小，模型越接近真相。


>>> 收束与下集预告 #B18
@enter: fade
@exit: fade
@visual: image(./generated_images/image18.png)

--- visual ---
（文档参考：实际使用 `./generated_images/image18.png`，构建时复制该文件，不参与文生图。）
收束：全流程嵌入 → 注意力 → MLP → logits → loss；下集反向传播与 Adam。

--- narration ---
我们走完了一次完整的前向传播。
从一个字符开始，经过嵌入、注意力、MLP、输出，最终算出一个 **loss**。
它告诉模型：这次考得有多差。
但 loss 只是一个数字。
模型怎么根据这个数字，去 **调整** 自己的 4000 多个参数？
下一集，我们讲 **反向传播**。
以及让 **Adam** 这个优化器，把 loss 真正变成模型能力的提升。
