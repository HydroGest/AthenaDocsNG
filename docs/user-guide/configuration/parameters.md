# 推理时参数

## OpenAI API 支持的参数

### 1. `Parameters.Temperature`

采样器的温度。数值越大，回复越随机；数值越小，回复越确定。

具体而言，这个参数对词符 logit 的 softmax 分布进行缩放。在实践中，较大的温度值会导致更多的随机性，而较小的温度值会导致更多的确定性。

相关公式为：

$$
P_i = \frac{\exp(\text{logit}_i / T)}{\sum_{j} \exp(\text{logit}_j / T)}
$$

其中 $P_i$ 是词符 $i$ 的概率，$\text{logit}_i$ 是词符 $i$ 的 logit，$T$ 是温度。

### 2. `Parameters.MaxTokens`

一次生成的最大词符数量。更大的词符数量可能会导致生成更长的回复。对于基于 llama.cpp 的加载器，这个参数还可以设置为 `-1` 或 `-2`，分别表示 `无限制` 和 `生成到上下文窗口被填满`。

### 3. `Parameters.TopP`

核心采样。模型生成的所有候选词符按照其概率从高到低排序后，依次累加这些概率，直到达到或超过此预设的阈值，剩余的词符会被丢弃。值为1时相当于关闭此采样器。

### 4. `Parameters.FrequencyPenalty`

数值为正时，会根据词符在前文出现的频率进行惩罚，降低模型重复使用同一个词的概率。这是一个乘数。

相关公式为：

$$
\text{logit}_i^\prime = \text{logit}_i - \text{FrequencyPenalty} \times f_i
$$

其中 $f_i$ 是词符 $i$ 在前文中出现的次数。

### 5. `Parameters.PresencePenalty`

数值为正时，如果词符在前文出现过，就对其进行惩罚，降低它再次出现的概率，提高模型谈论新话题的可能性。这是一个加数。

相关公式为：

$$
\text{logit}_i^\prime = \text{logit}_i - \text{PresencePenalty} \times \mathbf{1}_{(f_i > 0)}
$$

其中 $\mathbf{1}_{(f_i > 0)}$ 表示当词符 $i$ 在前文中出现过时取值 1，否则为 0。

### 6. `Parameters.Stop`

自定义停止词。对于 OpenAI 官方的 API，最多可以设置4个自定义停止词。生成会在遇到这些停止词时停止。

## 其他非通用参数

你可以在 `Parameters.OtherParameters` 中尝试设置这些参数，但并非所有 API 都支持它们：

### 1. `logit_bias`

通过 JSON 对象调整特定词符的生成概率。偏置值范围在 -100 到 100 之间，会在采样前加到模型生成的 logit 上。-1 到 1 的值会轻微调整概率，而 -100 或 100 则几乎能阻止或强制选中相关词符。

在某些 API 中，还可以使用 `false` 偏置值来禁用特定词符。

相关公式为：

$$
\text{logit}_i^\prime = \text{logit}_i + \text{bias}_i
$$

### 2. `max_completion_tokens`

适用于带思考功能的模型（如 `o1`）的最大词符数限制，会计入思考消耗的词符数。

### 3. `seed`

用于生成相对固定回复的随机种子。相同的种子值会尽可能生成相似的回复，但不保证完全相同。主要用于调试和固定输出。

### 4. 动态温度参数

- `dynatemp_range`：动态温度变化范围，最终温度在 \([temperature - dynatemp\_range, temperature + dynatemp\_range]\) 之间。默认值 `0.0` 表示禁用此功能。
- `dynatemp_exponent`：动态温度计算指数，默认值为 `1.0`。

温度计算公式：

$$
T = T_{\text{base}} + \text{dynatemp\_range} \cdot (2 \cdot \text{entropy}^{\text{dynatemp\_exponent}} - 1)
$$

其中 $T$ 为最终温度，$T_{\text{base}}$ 为基础温度，$\text{entropy}$ 为信息熵（具体计算方式取决于 API 实现）。

### 5. `top_k`

仅保留概率最高的前 `top_k` 个词符，其余词符将被排除。

### 6. `min_p`

词符被考虑的最低概率阈值，相对于最可能词符的概率计算。

相关公式为：

$$
\text{logit}_i^\prime = \text{logit}_i \cdot \mathbf{1}_{(\text{logit}_i > \text{logit}_{\text{max}} \cdot \text{min\_p})}
$$

### 7. `typical_p`

局部典型性采样参数，用于控制生成文本的典型程度。它通过计算词符条件概率与随机词符期望条件概率的相似度来工作。

设置为小于 1 的值时，仅保留最典型的词符集合，使其累积概率不低于 typical_p。这有助于生成更自然连贯的文本。

相关公式：

$$
H(p) = -\sum_j p(x_j)\log(p(x_j))
$$

$$
\text{Typicality}(x_i) = |\log(p(x_i)) + H(p)|
$$

$$
\text{Selected tokens} = \{x_i : \sum_{x_j \in S, \text{Typicality}(x_j) \leq \text{Typicality}(x_i)} p(x_j) \leq \text{typical\_p}\}
$$

其中 $p(x_i)$ 是词符 $i$ 的条件概率，$H(p)$ 是概率分布的熵。典型度较小的词符更可能被保留。默认值 1.0 表示禁用此功能。

### 8. DRY（重复控制）参数

- `dry_multiplier`：DRY 惩罚系数。默认值 0.0 表示禁用。
- `dry_base`：DRY 惩罚基数，默认值为 1.75。
- `dry_allowed_length`：允许的重复长度。超过此长度的重复序列将受到惩罚：$\text{multiplier} \times \text{base}^{(\text{重复序列长度} - \text{allowed\_length})}$。默认值为 2。
- `dry_penalty_last_n`：检查重复的词符范围。0 表示禁用，-1 表示使用整个上下文长度。默认值为 -1。
- `dry_sequence_breakers`：序列打断器配置，接受 JSON 字符串数组。默认值：`['\n', ':', '"', '*']`。

### 9. XTC 采样器参数

XTC (eXtreme Truncation Coring) 是一种通过概率性移除低概率词符来优化采样的技术。

- `xtc_probability`：触发 XTC 采样器移除词符的概率。默认值为 0.0（禁用）。
- `xtc_threshold`：通过 XTC 采样器移除词符的最低概率阈值。默认值为 0.1（大于 0.5 时将禁用 XTC）。

相关公式：

$$
P(\text{token removal}) = \begin{cases}
1 & \text{if } p(x_i) < \text{xtc\_threshold} \text{ and } r < \text{xtc\_probability} \\
0 & \text{otherwise}
\end{cases}
$$

其中：
- $p(x_i)$ 是词符 $x_i$ 的概率
- $r$ 是一个 [0,1] 之间的随机数
- $\text{xtc\_threshold}$ 是概率阈值
- $\text{xtc\_probability}$ 是触发概率

### 10. `mirostat`

Mirostat 是一种基于信息论的采样器，用于控制生成文本的多样性。目前有两个版本。

- `mirostat`：Mirostat 采样器版本。默认值为 0（禁用）。
- `mirostat_tau`：Mirostat 采样器的 $\tau$ 参数。
- `mirostat_eta`：Mirostat 采样器的 $\eta$ 参数。

### 11. `grammar`

通过控制采样器来强制生成符合语法规则的文本。关于如何编写GBNF语法，请参考 [GBNF 语法](https://github.com/ggerganov/llama.cpp/blob/master/grammars/README.md)。