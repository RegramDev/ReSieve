# bayes_spam_sniper

来源作者：ramsayleung。项目利用贝叶斯分类处理 Telegram 群组垃圾信息。

- [上游项目](https://github.com/ramsayleung/bayes_spam_sniper/tree/f685165f4cf92deac34c7904f552639b805e29da)
- [使用的数据文件](https://github.com/ramsayleung/bayes_spam_sniper/blob/f685165f4cf92deac34c7904f552639b805e29da/db/data/db_migration_data.csv)：`db/data/db_migration_data.csv`

ReSieve 仅使用由 `admin_dashboard` 或 `group_admin` 标记的 `message_content` 与 ham/spam 标签。用户、群组等标识不作为模型输入。文件摘要见 [provenance.json](../metadata/provenance.json)。

这类样本有助于发现垃圾信息的写法，但不能直接决定应用中每条内容该删还是该留。正常求助、反诈讨论、软件分享和官方福利仍需单独审阅。

在上述固定版本未找到明确的数据集分发许可。本仓库保留署名和使用记录，不分发原始数据库或项目源码。
