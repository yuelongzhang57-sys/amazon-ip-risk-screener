# Amazon IP Risk Screener

一个用于筛查 Amazon 直接竞品第三方知识产权线索的 Codex Skill。它会先从 H10 自然关键词表中提取自然排名 1-10 的全部关键词，再结合准确的 Amazon 商品页面进行有限网页研究和视觉对比。

本工具用于前置风险筛查，不构成法律意见，也不替代商标、版权、专利、外观设计及授权数据库的完整核验。

## 必填输入

每次运行只要求两个必填输入：

1. 直接竞品的 H10 自然关键词表，支持 `.xlsx`、`.xlsm`、`.csv`、`.tsv`；
2. 与该表对应的准确 Amazon 商品详情页链接，必须指向需要检查的站点和实际商品/变体。

文件名中的 ASIN、单独的 ASIN、搜索结果页或近似商品链接不能替代准确商品链接。

## 安装

将整个仓库克隆到个人 Codex skills 目录：

```powershell
git clone <仓库地址> "$env:USERPROFILE\.codex\skills\amazon-ip-risk-screener"
```

安装后重新打开 Codex 会话，即可使用 `$amazon-ip-risk-screener`。

## 使用示例

```text
使用 $amazon-ip-risk-screener 检查这个商品：
H10自然关键词表：<文件路径>
Amazon商品链接：https://www.amazon.com/dp/B0XXXXXXXX?th=1
```

## 目录

- `SKILL.md`：筛查流程、判断规则和输出要求；
- `scripts/filter_h10_top10.py`：确定性提取自然排名 1-10 的关键词；
- `agents/openai.yaml`：Codex 中的技能展示和默认调用提示。
