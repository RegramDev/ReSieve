# ReSieve

ReSieve 是 Regram 使用的中文广告识别模型。消息在本机处理，不需要把聊天内容发到服务器。

它基于 MiniRBT-H256 微调，判断文字是否带有推广意图。遇到正文夹带广告的消息，Regram 会分段判断，保留正常内容。混合消息的裁剪、链接和媒体处理由客户端负责，模型本身只输出分类分数。

当前版本 **1.3.0**（内部模型版本 `2026-11-18.2`，第 39e 阶段第 1 个 epoch）。模型体积、推理流程与单模型端侧架构保持不变，基于新一批人工核对导出样本完成了增量微调。

## 模型文件

| 文件 | 格式 | 大小 |
| --- | --- | --- |
| [coreml/ReSieve.mlmodel](coreml/ReSieve.mlmodel) | Core ML，FP16 权重 | 20,571,001 字节 |
| [pytorch/model.safetensors](pytorch/model.safetensors) | PyTorch / Transformers，FP32 权重 | 41,394,928 字节 |

两种格式来自同一份训练权重。配置和词表都在仓库里，加载时不需要再下载基础模型。可以用 `shasum -a 256 -c SHA256SUMS` 检查文件是否完整。

## 使用

输出分为 `content`（正常内容）和 `promotion`（推广）。输入处理、长文本分窗和离线加载方法见[推理说明](docs/inference.md)，参数见 [model.json](model.json)。本版阈值为 `0.9835`，更偏向保留不确定内容。

目前主要处理中文文字，不能直接识别图片和视频里的广告。模型仍会漏判和误判，接入时应保留原文，并允许用户纠正。已完成的检查及其范围记在[评估记录](docs/evaluation.md)。

## 来源

感谢 HIT、科大讯飞及相关开源项目。基础模型说明、数据来源记录和许可说明已放在 [references/](references/README.md)，原作者和原始地址均有保留。

模型及本文档使用 [Apache-2.0](LICENSE)，上游声明见 [NOTICE](NOTICE)。仓库不包含手机消息、账号数据或原始训练语料，因此不能只靠这里的文件完整重现训练。
