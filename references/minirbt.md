# MiniRBT-H256

作者：哈尔滨工业大学（HIT）与科大讯飞研究院（iFLYTEK Research）。MiniRBT 使用知识蒸馏训练较小的中文预训练模型。ReSieve 在 H256 编码器上增加了推广意图分类头并做监督微调。

- 模型来源：[hfl/minirbt-h256，固定版本](https://huggingface.co/hfl/minirbt-h256/tree/446e1b4012b10946f479563005700c54c9db3f7c)
- 上游项目：[iflytek/MiniRBT，文档固定版本](https://github.com/iflytek/MiniRBT/tree/baef787a1cee4fdc4051083943a460807e7b8a86)
- 原始权重 SHA-256：`6728ca573fd5197f28198eb7e6119a76e4d5dc626f953fca8c0dc5ba2798f9fc`
- 原始[模型卡](minirbt-model-card.md)和 [Apache-2.0 许可证](MiniRBT-LICENSE)保存在本目录。

仓库分发的是微调后的完整权重，不是增量适配器。推理不需要另外下载 MiniRBT 权重。基础模型卡中的其他项目链接属于上游的延伸阅读，不是 ReSieve 的依赖。
