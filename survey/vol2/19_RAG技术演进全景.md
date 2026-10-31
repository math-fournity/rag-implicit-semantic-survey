# 第二卷 RAG技术演进

## 回复19：RAG技术演进全景——从Naive到Modular

---

### 概述

2020年，Lewis等人在NeurIPS发表了开创性论文《Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks》，正式提出了RAG（Retrieval-Augmented Generation）框架。这一范式将检索与生成相结合，为解决大语言模型的知识局限性开辟了新路径。

从2020年到2025年，RAG技术经历了从**Naive RAG**到**Advanced RAG**再到**Modular RAG**的演进，并在2024-2025年进一步发展为**Agentic RAG**。这一演进过程不是简单的技术叠加，而是对"如何有效整合外部知识与生成能力"这一核心问题的持续探索。

本回复作为第二卷的开篇，将建立RAG技术演进的整体框架，为后续各章节的深入讨论奠定基础。

---

### 一、RAG的起源与动机

#### 1.1 参数化知识的三重困境

大语言模型将世界知识编码在模型参数中，这种**参数化知识（Parametric Knowledge）**面临根本性局限：

**知识截止（Knowledge Cutoff）**

模型的知识停留在训练数据的时间节点：
- GPT-4的训练截止于2023年12月
- Claude的知识截止于特定日期
- 无法获取最新信息

对于时效性场景（新闻、金融、医疗），这一局限尤为致命。

**幻觉（Hallucination）**

当模型被要求回答训练数据未覆盖的问题时：
- "自信地"生成虚假信息
- 编造不存在的引用
- 胡编乱造细节

Huang et al. (2023)的综述指出，幻觉是LLM落地的最大障碍。

**知识更新成本**

更新参数化知识意味着：
- 重新训练或微调（高计算成本）
- 可能破坏已有能力
- 难以精确控制更新内容

#### 1.2 检索增强的核心思想

RAG的核心洞见是：**将知识存储从模型参数外部化到可检索的文档库**。

```
传统LLM:
Query → [LLM(Parametric Knowledge)] → Answer

RAG:
Query → [Retriever] → Relevant Docs → [LLM(Query + Docs)] → Answer
```

**优势**：
- 知识可实时更新（无需重新训练）
- 生成有据可查（可追溯来源）
- 支持领域定制（更换文档库即可）
- 降低幻觉（有外部依据约束）

#### 1.3 Lewis et al. (2020)的原始框架

**RAG-Sequence**：
整个生成过程使用相同的检索文档。

$$P(y|x) = \sum_{z \in \text{top-k}} P(z|x) \prod_i P(y_i|x, z, y_{1:i-1})$$

**RAG-Token**：
每个token的生成可以参考不同文档。

$$P(y|x) = \prod_i \sum_{z \in \text{top-k}} P(z|x) P(y_i|x, z_i, y_{1:i-1})$$

两种变体反映了不同的知识利用粒度。

---

### 二、RAG范式的三阶段演进

#### 2.1 Naive RAG（2020-2022）

**特征**："检索-拼接-生成"的简单流水线。

**典型流程**：

```
1. 用户提问
2. 直接用问题检索文档
3. 将检索结果拼接到提示词
4. 模型一次性生成答案
```

**优点**：
- 实现简单
- 部署容易
- 立即可用

**问题**：

| 问题 | 表现 |
|-----|-----|
| 检索质量不稳定 | 用户问题往往不是好的检索query |
| 上下文利用不充分 | 简单拼接，缺乏筛选 |
| Lost in the Middle | 模型忽视中间位置的信息 |
| 上下文窗口限制 | 无法容纳足够多的检索结果 |
| 缺乏质量控制 | 无论检索是否成功都生成 |

#### 2.2 Advanced RAG（2022-2024）

**特征**：针对Naive RAG的各个环节进行优化。

**优化维度**：

**检索前优化（Pre-retrieval）**：

| 技术 | 目的 |
|-----|-----|
| 查询重写（Query Rewriting） | 改善检索query的质量 |
| 查询扩展（Query Expansion） | 增加检索召回 |
| 查询分解（Query Decomposition） | 处理复杂问题 |
| HyDE（假设文档嵌入） | 用假设答案指导检索 |

**检索中优化（Retrieval）**：

| 技术 | 目的 |
|-----|-----|
| 混合检索（Hybrid Search） | 结合稀疏和稠密检索 |
| 元数据过滤 | 利用结构化信息 |
| 多路召回 | 多种检索策略并行 |

**检索后优化（Post-retrieval）**：

| 技术 | 目的 |
|-----|-----|
| 重排序（Re-ranking） | 提升相关性排序 |
| 上下文压缩 | 压缩或筛选检索内容 |
| 文档融合 | 合并多文档信息 |

**生成优化**：

| 技术 | 目的 |
|-----|-----|
| 引用生成 | 生成可追溯的答案 |
| 忠实性约束 | 确保答案基于检索内容 |
| 拒绝回答 | 检索不充分时承认不知道 |

#### 2.3 Modular RAG（2024-）

**特征**：将RAG分解为可独立优化、灵活组合的模块。

**模块化架构**：

```
┌─────────────────────────────────────────────┐
│                  RAG系统                    │
├─────────────────────────────────────────────┤
│  ┌─────────┐ ┌─────────┐ ┌─────────┐       │
│  │ 查询处理 │ │ 检索器  │ │ 重排器  │       │
│  │ 模块    │→│ 模块    │→│ 模块    │       │
│  └─────────┘ └─────────┘ └─────────┘       │
│       │           │           │            │
│       ▼           ▼           ▼            │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐       │
│  │ 上下文  │ │ 生成器  │ │ 验证器  │       │
│  │ 构建器  │→│ 模块    │→│ 模块    │       │
│  └─────────┘ └─────────┘ └─────────┘       │
├─────────────────────────────────────────────┤
│  可插拔、可配置、可独立更新                 │
└─────────────────────────────────────────────┘
```

**模块化的优势**：

- **灵活性**：根据场景组合模块
- **可维护性**：独立更新各模块
- **可复用性**：模块跨项目复用
- **可测试性**：独立测试各组件

**典型模块**：

| 模块类别 | 具体模块 |
|---------|---------|
| 查询模块 | 查询理解、查询改写、查询路由 |
| 检索模块 | 向量检索、稀疏检索、图检索 |
| 处理模块 | 重排序、压缩、融合 |
| 生成模块 | 答案生成、引用生成、摘要 |
| 控制模块 | 路由、编排、质量检查 |

---

### 三、RAG的核心技术组件

#### 3.1 索引构建

**文档处理流程**：

```
原始文档
    ↓
[文档加载] → 支持多格式(PDF, Word, HTML, ...)
    ↓
[文本提取] → 提取文本内容
    ↓
[分块(Chunking)] → 切分为适当粒度
    ↓
[嵌入(Embedding)] → 转换为向量表示
    ↓
[索引存储] → 存入向量数据库
```

**分块策略（详见回复21）**：

| 策略 | 特点 |
|-----|-----|
| 固定大小 | 简单但可能切断语义 |
| 句子边界 | 保持句子完整 |
| 段落边界 | 保持段落完整 |
| 语义分块 | 基于语义相似度 |
| 递归分块 | 层次化处理 |

**嵌入模型（详见回复20）**：

从通用到专用：
- 通用嵌入：OpenAI text-embedding, BERT
- 检索优化嵌入：E5, BGE, GTE
- 领域专用嵌入：BioBERT, LegalBERT

#### 3.2 检索机制

**稀疏检索**：

基于词频统计的传统方法：
- BM25：业界标准，无需训练
- TF-IDF：简单有效

优点：精确匹配、可解释、无需GPU
缺点：无法处理语义相似

**稠密检索**：

基于神经网络的语义检索：
- 双塔模型：查询和文档独立编码
- 交叉编码器：联合编码（更精确但更慢）

优点：语义理解、处理同义词
缺点：需要训练、需要GPU

**混合检索**：

结合两者优势：
$$Score_{hybrid} = \alpha \cdot Score_{sparse} + (1-\alpha) \cdot Score_{dense}$$

实践中混合检索往往效果最佳。

#### 3.3 重排序

检索后的二次排序，提升精度：

**交叉编码器重排序**：

将(query, document)对联合编码，判断相关性。比双塔模型更精确，但计算成本更高。

**常用重排序模型**：
- Cohere Rerank
- BGE-Reranker
- cross-encoder系列

**重排序的必要性**：

检索召回大量候选后，重排序：
- 提升top-k的质量
- 支持更细粒度的相关性判断
- 可引入额外特征

#### 3.4 生成整合

**上下文构建**：

如何组织检索结果送入生成模型：

```
System: 你是一个有帮助的助手。请基于以下信息回答问题。

Context:
[Document 1]: {content1}
[Document 2]: {content2}
...

Question: {user_query}

Answer:
```

**关键考量**：
- 检索结果的排列顺序
- 上下文长度控制
- 来源标注方式
- 指令的设计

---

### 四、评估框架

#### 4.1 RAG评估的挑战

**传统评估的局限**：
- 端到端评估无法定位问题
- 缺乏标准化的评估框架
- 人工评估成本高

**组件级评估的必要性**：
需要分别评估检索质量和生成质量。

#### 4.2 检索评估指标

| 指标 | 定义 | 用途 |
|-----|------|-----|
| Recall@k | top-k中包含相关文档的比例 | 召回能力 |
| Precision@k | top-k中相关文档的比例 | 精确度 |
| MRR | 第一个相关文档的排名倒数 | 排序质量 |
| NDCG | 归一化折损累积增益 | 排序质量 |
| Hit Rate | 是否检索到任何相关文档 | 基本能力 |

#### 4.3 生成评估指标

**忠实性（Faithfulness）**：
答案是否忠于检索内容，不添加虚假信息。

**相关性（Relevance）**：
答案是否回应了用户问题。

**完整性（Completeness）**：
答案是否覆盖了问题的各个方面。

**引用准确性（Citation Accuracy）**：
引用是否准确对应来源。

#### 4.4 评估框架

**RAGAS（Retrieval Augmented Generation Assessment）**：

自动化评估框架，评估维度：
- Context Precision：检索精度
- Context Recall：检索召回
- Faithfulness：生成忠实性
- Answer Relevancy：答案相关性

**ARES（Automated RAG Evaluation System）**：

基于LLM的自动评估，减少人工标注需求。

**RAGBench**：

可解释的RAG基准测试，提供细粒度诊断。

---

### 五、主要技术挑战

#### 5.1 检索相关性挑战

**语义鸿沟**：
用户问题与文档表述方式的差异。

> 用户："感冒了吃什么药？"
> 文档："上呼吸道感染的药物治疗..."

**解决方向**：
- 查询改写
- 假设文档嵌入(HyDE)
- 混合检索

#### 5.2 上下文利用挑战

**Lost in the Middle**：
模型倾向于关注上下文的开头和结尾，忽视中间。

**上下文窗口限制**：
即使是长上下文模型，利用效率也会下降。

**解决方向**：
- 上下文压缩
- 重要内容前置
- 分层检索

#### 5.3 生成忠实性挑战

**幻觉仍会发生**：
即使提供了正确信息，模型可能仍然生成虚假内容。

**过度依赖检索**：
有时检索内容本身有误。

**解决方向**：
- 忠实性约束训练
- 引用生成
- 答案验证

#### 5.4 效率与质量权衡

**延迟**：
增加检索环节带来延迟。

**成本**：
向量数据库、嵌入计算的成本。

**解决方向**：
- 缓存策略
- 模型蒸馏
- 选择性检索

---

### 六、演进趋势：走向Agentic RAG

#### 6.1 从被动到主动

传统RAG是**被动的**：收到查询→执行检索→生成回答。

Agentic RAG是**主动的**：规划→执行→验证→修正。

**关键转变**：
- 检索成为智能体的可调用工具
- 引入迭代推理循环
- 支持自我反思和修正

#### 6.2 第三卷预告

第三卷将深入探讨Agentic RAG：
- 智能体架构设计
- 迭代检索机制
- Self-RAG与CRAG
- GraphRAG
- 多智能体协作

---

### 参考文献

1. Lewis, P., Perez, E., Piktus, A., Petroni, F., Karpukhin, V., Goyal, N., ... & Kiela, D. (2020). Retrieval-augmented generation for knowledge-intensive NLP tasks. *NeurIPS 2020*.

2. Gao, Y., Xiong, Y., Gao, X., Jia, K., Pan, J., Bi, Y., ... & Wang, H. (2023). Retrieval-augmented generation for large language models: A survey. *arXiv:2312.10997*.

3. Huang, L., et al. (2023). A survey on hallucination in large language models. *arXiv:2311.05232*.

4. Es, S., James, J., Espinosa-Anke, L., & Schockaert, S. (2024). RAGAS: Automated evaluation of retrieval augmented generation. *EACL 2024*.

5. Saad-Falcon, J., et al. (2024). ARES: An automated evaluation framework for RAG systems. *NAACL 2024*.

6. Liu, N., et al. (2024). Lost in the middle: How language models use long contexts. *TACL 2024*.

7. Asai, A., Wu, Z., Wang, Y., Sil, A., & Hajishirzi, H. (2024). Self-RAG: Learning to retrieve, generate, and critique. *ICLR 2024*.

8. Yan, S., et al. (2024). Corrective retrieval augmented generation. *arXiv:2401.15884*.

9. Khattab, O., & Zaharia, M. (2020). ColBERT: Efficient and effective passage search via contextualized late interaction. *SIGIR 2020*.

10. Karpukhin, V., et al. (2020). Dense passage retrieval for open-domain question answering. *EMNLP 2020*.

---

### 下一节预告

回复20《嵌入技术与向量空间》将深入探讨RAG的基础设施——向量嵌入。从Word2Vec到BERT到现代检索专用嵌入模型，我们将系统梳理嵌入技术的演进，分析不同模型的特点和选型考量。

---

**元信息**
```yaml
writer: human_claude_collaboration
volume: 2
response: 19
title: RAG技术演进全景：从Naive到Modular
completed_at: 2026-01-09T23:30:00+08:00
word_count: 约7500字
status: completed
```
