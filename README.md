# RAG 与隐性语义处理综述项目档案

2026-01-08 ~ 2026-02-11 的百万字综述研究项目：以《隐性语义提取》与《Aurola 过程》两个基础文档为起点，系统综述 RAG 技术与隐性语义处理。

## 项目状态（截至 2026-02-11）

- **Vol1 语言学与认知基础（Ch1-18）**：学术版完成——形式语义学、分布式语义学、言语行为、修辞结构、框架语义学、概念隐喻、词汇语义/WordNet 等 18 章，含各章研究资料（`survey/vol1/`）
- **Vol2 RAG 技术演进（Ch19-28）**：完成（`survey/vol2/`）
- **Vol3 Agentic RAG（Ch29-43, 44-53）/ Vol4 框架深度剖析 / Vol5 工程实践**：研究资料与部分章节（`survey/vol3*`、`vol4/`、`vol5/`）
- **Schema 迭代**：第四版 → 第五版 → 第六版（2 卷 28 章架构）全程保留（`survey/vol1/01_第*版Schema*.md`）
- **法证式审计**：v3 / v6Schema 等多轮审计报告（`survey/vol1/01_法证式审计报告*.md`）

## 目录

| 目录 | 内容 |
|---|---|
| `survey/` | 综述正文与章节研究资料、项目进展总览、supplement |
| `reports/` | 独立 RAG 研究报告 9 篇（RAG 前沿、Agentic RAG、KG²RAG 与 SemRAG、MCP 互操作、文档语义增强等） |
| `planning/` | 项目文件清单、Vol3 规划方案 |
| `conversation-exports/` | Claude-01 对话完整导出《隐性语义提取与 Aurola 过程的系统评价》（两次快照：原版 + (1) 完整版） |
| `transcripts/` | 126 段对话 transcript + `journal.txt` 索引 |
| `rescue-20260211/` | 2026-02-11 抢救归档：说明 README + 上传给 AI 的 17 份源文档 |
| `archive-versions/` | 历史版本（v3/v4/v5/FINAL 交付快照、早期草稿） |

## 整理说明

- 逐文件来源映射见 [PROVENANCE.md](PROVENANCE.md)。
- `transcripts/` 中同一对话存在数秒间隔的两次保存时，已按内容去重保留较早一份；同对话不同时点的导出内容不同，均保留。
- 文件修改时间保留自原始资料；zip 不含创建时间，故创建时间与修改时间一致。
