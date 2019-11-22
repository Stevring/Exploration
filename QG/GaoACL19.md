[总目录](../toc_for_paper_notes.md)

# Interconnected Question Generation with Coreference Alignment and Conversation Flow Modeling

## 论文亮点

算是 conversation question generation 的开山之作。conversation QG 的两个特点：coreference resolution (需要处理不定代词)、conversation flow (对话需要连贯地变化)

目标是根据 passage（文章）、conversation history （由多轮 question-answer 组成）、answer （本轮的答案）生成 question （本轮的问题）。

将 passage、conversation history、answer 做 encoding 之后 再加了一个 decoder （copy + attention）

## Encoding 部分

### passage + answer

word embedding + answer position embedding (BIO) + **turn number embedding** + **Chunk embedding**

turn number embedding：当前的轮次。Chunk embedding：把 passage 分割为很多块，单词所属的块数的 embedding。 二者都是为了解决 Conversation Flow Modeling

然后把这个大的embedding 过一个 BiLSTM + self-attention + feature-fusion

### conversation history

假设有 i-1 轮历史。从两个粒度进行 encoding

**单个轮次**：有question和answer，用分隔符 "\<q\>" 和 "\<a\>" 将其分割，得到：$(<q>, q_{j,1}^w, \cdots, q_{j,m}^w; <a>, a_{j,1}^w, \cdots, a_{j,m}^w) $ ，$w$ 表示 word-level，$j$ 表示这是第 $j$ 轮历史，$1 \cdots m$ 是单词的下标，然后过一个BiLSTM，得到 token-level embedding。 $\color{red} ?$ 为什么这里的 answer 和 question 一定长度相同？

**轮次之间**：用 BiLSTM 得到每一个轮次的 context-level embedding $(h^c_1 ,\cdots, h^c_{i−1} )$

## Decoding 部分

attention + copy decoder

由于输入来源有二：passage、conversation history，因此 attention 需要特殊处理。

### attention

假设decoder 上一步的隐向量是 $h^d_{t}$

conversation history 的 word-level embedding 是 $h^w$ （一共有 $(i-1)*m$ 个）

conversation history 的 context-level embedding 是 $h^c$ （一共有 $(i-1)$ 个）

passage 的 encoding 是  $h^p$



1. 用 $h^d_{t}$与 passage 的 encoding $h^p$ 做 attention，得到一个 alignment score vec  $e^p$.

2. 用 $h^d_{t}$与 所有的 $h^w$  做 attention，得到一个 alignment score vec $e^w$.

3. 用 $h^d$与  $h^c$  做 attention，得到一个 alignment score vec $e^c$ , 用 $e^c$ 对 $e^w$ 加权。
4. 加权后的 $e^w$ 与 $e_p$ 拼接，做归一化，得到最终的 attention score



## tricks

beam search

copy

teacher-forcing

self-attention

feature-fusion

