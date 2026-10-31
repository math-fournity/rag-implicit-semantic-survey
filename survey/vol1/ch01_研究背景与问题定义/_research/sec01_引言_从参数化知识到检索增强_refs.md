# sec01_引言：从参数化知识到检索增强 - Research参考

**Research日期**: 2026-01-09  
**适用Schema节点**: 第六版Schema 第一节  
**用途**: 供Claude CLI并发写作时参考  

---

## 写作指令

本节需覆盖以下内容：
1. RAG的起源与动机（参数化知识的三重困境）
2. RAG奠基论文Lewis et al. (2020)的核心贡献
3. RAG的形式化定义 P(y|x) = Σ P(z|x) · P(y|x,z)
4. RAG的三大前驱工作：ORQA、REALM、DPR
5. 与其他知识增强方法的对比定位

**预计字数**: 3,000-4,000字  
**写作风格**: 学术综述，需引用具体论文和数据

---

## 核心文献

### 1. RAG奠基论文

**Lewis et al. (2020) - RAG正式提出**

| 属性 | 信息 |
|------|------|
| 完整引用 | Lewis, P., Perez, E., Piktus, A., Petroni, F., Karpukhin, V., Goyal, N., Küttler, H., Lewis, M., Yih, W., Rocktäschel, T., Riedel, S., & Kiela, D. (2020). Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks. *NeurIPS 2020*, 33, 9459-9474. |
| arXiv | https://arxiv.org/abs/2005.11401 |
| NeurIPS官方 | https://proceedings.neurips.cc/paper/2020/hash/6b493230205f780e1bc26945df7481e5-Abstract.html |
| PDF直链 | https://proceedings.neurips.cc/paper/2020/file/6b493230205f780e1bc26945df7481e5-Paper.pdf |
| 引用数 | ~10,130 (2025年1月) |
| 作者机构 | Facebook AI Research (FAIR) & UCL |

**核心贡献摘要**:
- 首次提出通用的检索增强生成框架
- 将参数化记忆（BART）与非参数化记忆（DPR + Wikipedia索引）结合
- 提出RAG-Sequence和RAG-Token两种变体
- 在开放域问答上实现SOTA

**关键引用段落**:
> "We explore a general-purpose fine-tuning recipe for retrieval-augmented generation (RAG) — models which combine pre-trained parametric and non-parametric memory for language generation."

**本节用途**: 作为RAG定义的权威来源，引入正式数学形式化

---

### 2. RAG前驱工作（按时间顺序）

#### 2.1 ORQA (Lee et al., 2019)

| 属性 | 信息 |
|------|------|
| 论文标题 | Latent Retrieval for Weakly Supervised Open Domain Question Answering |
| arXiv | https://arxiv.org/abs/1906.00300 |
| ACL Anthology | https://aclanthology.org/P19-1612/ |
| 会议 | ACL 2019 |
| 引用数 | ~1,107 |
| 作者机构 | Google Research |

**核心创新**: 
- 首个端到端可训练的开放域QA系统
- 提出逆向完形填空任务(ICT)进行检索器无监督预训练
- 将检索建模为潜变量

**本节用途**: 说明端到端检索学习的开创性工作

#### 2.2 REALM (Guu et al., 2020)

| 属性 | 信息 |
|------|------|
| 论文标题 | REALM: Retrieval-Augmented Language Model Pre-Training |
| arXiv | https://arxiv.org/abs/2002.08909 |
| ICML链接 | http://proceedings.mlr.press/v119/guu20a/guu20a.pdf |
| 会议 | ICML 2020 |
| 引用数 | ~2,548 |
| 作者机构 | Google Research |

**核心创新**:
- 首次将检索增强集成到预训练阶段
- 使用MLM作为学习信号
- 通过MIPS实现对百万文档的反向传播

**本节用途**: 展示检索增强可在预训练阶段学习

#### 2.3 DPR (Karpukhin et al., 2020)

| 属性 | 信息 |
|------|------|
| 论文标题 | Dense Passage Retrieval for Open-Domain Question Answering |
| arXiv | https://arxiv.org/abs/2004.04906 |
| ACL Anthology | https://aclanthology.org/2020.emnlp-main.550/ |
| 会议 | EMNLP 2020 |
| 引用数 | ~4,643 |
| GitHub | https://github.com/facebookresearch/DPR |
| 作者机构 | FAIR, Princeton, UW |

**核心创新**:
- 双编码器框架（独立BERT编码问题和段落）
- 证明稠密检索超越BM25达9-19%
- 成为RAG论文直接采用的检索组件

**本节用途**: 说明稠密检索相对稀疏检索的优势

---

### 3. LLM知识困境相关研究

#### 3.1 幻觉问题

**Huang et al. (2023) - 幻觉综述**

| 属性 | 信息 |
|------|------|
| 论文标题 | A Survey on Hallucination in Large Language Models: Principles, Taxonomy, Challenges, and Open Questions |
| arXiv | https://arxiv.org/abs/2311.05232 |
| 期刊 | ACM Transactions on Information Systems |
| 引用数 | ~1,868 |

**核心贡献**:
- 将幻觉分为事实性幻觉和忠实性幻觉
- 分析LLM全生命周期中导致幻觉的因素

**补充文献**:
- Ji et al. (2023): arXiv:2202.03629, ACM Computing Surveys
- Zhang et al. (2023): arXiv:2309.01219

#### 3.2 知识截止问题

**Cheng et al. (2024) - Dated Data**

| 属性 | 信息 |
|------|------|
| 论文标题 | Dated Data: Tracing Knowledge Cutoffs in Large Language Models |
| arXiv | https://arxiv.org/abs/2403.12958 |
| OpenReview | https://openreview.net/forum?id=wS7PxDjy6m |

**核心贡献**:
- 引入"有效截止日期"概念
- 证明实际知识边界与公布截止日期存在差异
- 指出重新训练成本（>10亿美元）使持续更新不现实

#### 3.3 参数化知识研究

**LAMA (Petroni et al., 2019)**
- arXiv: https://arxiv.org/abs/1909.01066
- ACL Anthology: https://aclanthology.org/D19-1250/
- 会议: EMNLP 2019
- 贡献: 揭示LM包含关系知识但存在显著缺口

**Roberts et al. (2020)**
- arXiv: https://arxiv.org/abs/2002.08910
- 会议: EMNLP 2020
- 贡献: T5闭卷问答随规模提升，但需极大模型

---

### 4. RAG综述论文

#### 4.1 主要参考综述

**Gao et al. (2023-2024)**

| 属性 | 信息 |
|------|------|
| 论文标题 | Retrieval-Augmented Generation for Large Language Models: A Survey |
| arXiv | https://arxiv.org/abs/2312.10997 |
| GitHub | https://github.com/Tongji-KGLLM/RAG-Survey |
| 引用数 | ~2,655 |
| 作者机构 | 同济大学, 复旦大学 |

**核心贡献**: 提出Naive/Advanced/Modular三范式分类法

#### 4.2 其他重要综述

| 综述 | arXiv | 年份 | 聚焦 |
|------|-------|------|------|
| Fan et al. | 2405.06211 | 2024 | RA-LLMs架构 |
| AIGC RAG | 2402.19473 | 2024 | 多模态RAG |
| Agentic RAG | 2501.09136 | 2025 | 自主代理式 |

---

## RAG形式化定义

**核心公式** (来源: Lewis et al., 2020):

$$P(y|x) \approx \sum_{z \in \text{top-}k} P(z|x) \cdot P(y|x,z)$$

**变量说明**:
- x = 输入查询/提示
- y = 生成的输出序列
- z = 检索到的文档（潜变量）
- P(z|x) = 检索器概率
- P(y|x,z) = 生成器概率

**两种变体**:
- RAG-Sequence: 序列级边缘化，同一文档生成完整输出
- RAG-Token: Token级边缘化，允许从多文档综合信息

---

## 学术脉络时间线

```
2019 ─── ORQA (ACL): 端到端检索训练
         │
         ├── LAMA (EMNLP): LM作为知识库探索
         │
2020 ─── REALM (ICML): 检索增强预训练
         │
         ├── DPR (EMNLP): 双编码器稠密检索
         │
         └── RAG (NeurIPS): 检索增强生成正式提出 ← 核心里程碑
              │
2023 ─── Hallucination Surveys
         │
         └── Gao et al. Survey: Naive/Advanced/Modular分类
              │
2024 ─── Advanced RAG, GraphRAG主流化
         │
2025 ─── Agentic RAG, Multimodal RAG
```

---

## 建议引用顺序

1. **开篇动机**: Huang et al. (2023) 幻觉 → Cheng et al. (2024) 知识截止
2. **历史脉络**: ORQA (2019) → REALM (2020) → DPR (2020) → RAG (2020)
3. **形式化定义**: Lewis et al. (2020) Section 2
4. **当前进展**: Gao et al. (2023-2024)

---

## 元信息

```yaml
research_date: 2026-01-09
schema_section: 第六版Schema 第一节
target_word_count: 3000-4000
core_references: 12篇
status: ready_for_writing
```
