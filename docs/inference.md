# 加载和推理

模型文件、配置和词表已包含在本仓库。Python 环境安装所需版本见 [requirements-pytorch.txt](../requirements-pytorch.txt)；检查 Core ML 时另用 [requirements-coreml.txt](../requirements-coreml.txt)。这些是验证过的运行库版本，仓库不内嵌 Python 包或系统 Core ML 框架。

## PyTorch

在仓库根目录运行，以下加载过程不会访问模型托管服务：

```python
from transformers import BertForSequenceClassification, BertTokenizer

tokenizer = BertTokenizer.from_pretrained("pytorch", local_files_only=True)
model = BertForSequenceClassification.from_pretrained(
    "pytorch", local_files_only=True, attn_implementation="eager"
).eval()
```

模型是 6 层 BERT，隐藏维度 256，共 10,345,730 个参数。模型输出 logits，需要做 softmax；类别顺序为 `content`、`promotion`。

## 文本处理

顺序不能随意调整，否则同一条消息的分数会不同。精确规则保存在 [model.json](../model.json) 的 `normalization` 字段：

1. Unicode NFKC 规范化并转小写。
2. 删除 Unicode 分类为 `Cf`、`Mn`、`Me`、`So`、`Sk` 的字符。
3. 按配置中的正则依次替换 URL、用户名和五位以上数字，删除汉字间空白并合并其他空白。
4. 去除首尾空白，使用仓库自带的 WordPiece 词表。

这些操作只供分类使用。裁剪消息时仍需使用原文的 UTF-16 范围，不能直接套用规范化后的字符位置。原生客户端还会检查语言、实体和上下文。

## 长文本

每个窗口为 128 token，其中 `[CLS]` 和 `[SEP]` 占两个位置，不足的部分补零。超过 126 个正文 token 时，窗口前进 110 个 token，保留 16 个重叠 token；最后一个窗口从文本末尾向前取满 126 个正文 token，并去掉重复的起点。

完整输入最多 1,024 个正文 token，同时最多 8 个窗口。任一上限超出就不作自动判定，不能只截取前面一段。每个窗口分别计算推广概率，整段取所有窗口的最小值。没有足够文字、超长或推理失败都应视为“未判定”。

阈值见 [model.json](../model.json)。这是片段候选的决策阈值，不是准确率；超过阈值也不表示可以直接删除整条消息。Regram 另有正文保留、引用保护、推广段落和链接归属等判断。

## Core ML

加载 [ReSieve.mlmodel](../coreml/ReSieve.mlmodel)，使用 `CPU_ONLY`。其他计算单元尚未通过本模型的概率一致性验收。

| 名称 | 类型 | 形状 |
| --- | --- | --- |
| `input_ids` | Int32 | `[1, 128]` |
| `attention_mask` | Int32 | `[1, 128]` |
| `probabilities` | Float32 | `[1, 2]` |

输出已经包含 softmax，无须再算一次。`attention_mask` 对非零 token 取 1，对补齐位置取 0。手机端应在后台串行执行，并缓存重复文本的结果。
