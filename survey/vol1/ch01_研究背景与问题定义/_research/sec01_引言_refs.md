# sec01_引言：从参数化知识到检索增强 - Research参考

**Research完成时间**: 2026-01-09
**用途**: 供Claude CLI编写第一节正文

---

## 写作指令

本节需覆盖：
1. RAG的起源动机（参数化知识的三重困境）
2. RAG的正式定义与数学形式化
3. RAG的前驱工作（ORQA→REALM→DPR）
4. RAG与参数化知识的对比框架
5. 本章的结构预览

预计字数：3,000-4,000字

---

## 核心文献

### 1. RAG奠基论文 [必引]

**Lewis et al. (2020)**
- **标题**: Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks
- **会议**: NeurIPS 2020
- **arXiv**: https://arxiv.org/abs/2005.11401
- **NeurIPS官方**: https://proceedings.neurips.cc/paper/2020/hash/6b493230205f780e1bc26945df7481e5-Abstract.html
- **PDF直链**: https://proceedings.neurips.cc/paper/2020/file/6b493230205f780e1bc26945df7481e5-Paper.pdf
- **引用数**: ~10,130 (2025.01)
- **作者机构**: Facebook AI Research (FAIR) & UCL

**核心贡献摘要**:
> 首次提出通用的检索增强生成框架，将参数化记忆（BART）与非参数化记忆（DPR+Wikipedia索引）结合。提出RAG-Sequence和RAG-Token两种变体，在开放域问答任务上实现SOTA。

**关键引用段落**:
> "We endow pre-trained, parametric-memory generation models with a non-parametric memory through a general-purpose fine-tuning approach which we refer to as retrieval-augmented generation (RAG)."

**本节用途**: 作为RAG定义的权威来源，引入正式数学形式化

---

### 2. RAG形式化定义 [必引]

**数学公式** (源自Lewis et al., 2020 Section 2):

```
P(y|x) ≈ Σ_{z∈top-k} P(z|x) · P(y|x,z)
```

**符号说明**:
- x = 输入查询/提示
- y = 生成的输出序列
- z = 检索到的文档（潜变量）
- P(z|x) = 检索器概率（DPR双编码器）
- P(y|x,z) = 生成器概率（BART seq2seq）

**两种变体**:
- **RAG-Sequence**: 整个序列使用同一文档，P(y|x) = Σ_z P(z|x) Π_i P(y_i|x,z,y_{1:i-1})
- **RAG-Token**: 每个token可参考不同文档，P(y_i|x,y_{1:i-1}) = Σ_z P(z|x) P(y_i|x,z,y_{1:i-1})

---

### 3. 前驱工作一：ORQA [推荐引用]

**Lee et al. (2019)**
- **标题**: Latent Retrieval for Weakly Supervised Open Domain Question Answering
- **会议**: ACL 2019
- **arXiv**: https://arxiv.org/abs/1906.00300
- **ACL Anthology**: https://aclanthology.org/P19-1612/
- **引用数**: ~1,107
- **作者机构**: Google Research

**核心创新**:
> 首个端到端可训练的开放域QA系统。关键创新是**逆向完形填空任务(ICT)**——将句子作为伪查询，其上下文作为伪证据，用于检索器的无监督预训练。

**本节用途**: 说明检索器端到端学习的开创性工作

---

### 4. 前驱工作二：REALM [推荐引用]

**Guu et al. (2020)**
- **标题**: REALM: Retrieval-Augmented Language Model Pre-Training
- **会议**: ICML 2020
- **arXiv**: https://arxiv.org/abs/2002.08909
- **ICML官方PDF**: http://proceedings.mlr.press/v119/guu20a/guu20a.pdf
- **引用数**: ~2,548
- **作者机构**: Google Research

**核心创新**:
> 首次将检索器集成到语言模型**预训练**阶段，使用掩码语言建模作为学习信号。关键突破是通过MIPS实现对数百万文档的检索步骤进行反向传播。

**本节用途**: 展示检索增强可在预训练阶段学习

---

### 5. 前驱工作三：DPR [必引]

**Karpukhin et al. (2020)**
- **标题**: Dense Passage Retrieval for Open-Domain Question Answering
- **会议**: EMNLP 2020
- **arXiv**: https://arxiv.org/abs/2004.04906
- **ACL Anthology**: https://aclanthology.org/2020.emnlp-main.550/
- **GitHub**: https://github.com/facebookresearch/DPR
- **引用数**: ~4,643
- **作者机构**: Facebook AI, Princeton, UW

**核心创新**:
> 提出**双编码器框架**，使用独立BERT分别编码问题和段落。证明稠密检索在top-20准确率上超越BM25达9-19%。

**本节用途**: 作为RAG论文直接采用的检索组件

---

### 6. 幻觉问题综述 [必引]

**Huang et al. (2023)**
- **标题**: A Survey on Hallucination in Large Language Models: Principles, Taxonomy, Challenges, and Open Questions
- **期刊**: ACM Transactions on Information Systems
- **arXiv**: https://arxiv.org/abs/2311.05232
- **引用数**: ~1,868

**核心分类**:
- **事实性幻觉**: 与可验证事实不一致
- **忠实性幻觉**: 偏离用户指令或上下文

**本节用途**: 说明LLM参数化知识的局限性，论证RAG必要性

---

### 7. 知识截止问题 [推荐引用]

**Cheng et al. (2024) - Dated Data**
- **标题**: Dated Data: Tracing Knowledge Cutoffs in Large Language Models
- **arXiv**: https://arxiv.org/abs/2403.12958
- **OpenReview**: https://openreview.net/forum?id=wS7PxDjy6m

**核心观点**:
> LLM的"有效截止日期"往往与公布的训练数据截止日期存在显著差异。重新训练成本（前沿模型>10亿美元）使持续知识更新不切实际。

**本节用途**: 论证RAG作为解决静态知识问题的方案

---

### 8. 参数化知识研究 [可选引用]

**Petroni et al. (2019) - LAMA**
- **标题**: Language Models as Knowledge Bases?
- **会议**: EMNLP 2019
- **arXiv**: https://arxiv.org/abs/1909.01066
- **ACL Anthology**: https://aclanthology.org/D19-1250/

**核心观点**:
> LM包含可竞争传统NLP方法的关系知识，但存在显著缺口。

---

### 9. RAG综述 [必引]

**Gao et al. (2023-2024)**
- **标题**: Retrieval-Augmented Generation for Large Language Models: A Survey
- **arXiv**: https://arxiv.org/abs/2312.10997
- **GitHub**: https://github.com/Tongji-KGLLM/RAG-Survey
- **引用数**: ~2,655
- **作者机构**: 同济大学, 复旦大学

**核心贡献**:
> 提出三范式分类法：Naive RAG → Advanced RAG → Modular RAG

**本节用途**: 引用该综述的分类体系

---

## 写作建议

### 开篇结构
1. 以LLM的知识困境开篇（幻觉、截止、更新成本）
2. 引出RAG作为解决方案
3. 给出正式定义和数学形式化
4. 梳理学术脉络（ORQA→REALM→DPR→RAG）
5. 预览本章结构

### 关键数据点
- Lewis et al. (2020) 引用数：~10,130
- RAG在NQ上的EM得分：44.5（超越当时SOTA）
- DPR相比BM25：top-20准确率提升9-19%
- 前沿模型重训成本：>$1B

### 学术脉络时间线
```
2019.06 ── ORQA (ACL): 端到端检索训练
2020.02 ── REALM (ICML): 检索增强预训练  
2020.04 ── DPR (EMNLP): 双编码器稠密检索
2020.05 ── RAG (NeurIPS): 检索增强生成正式提出 ← 里程碑
```

---

## 元信息

```yaml
section: 01_引言
schema_position: 第一节
estimated_words: 3000-4000
core_references: 5篇必引
optional_references: 4篇可选
research_date: 2026-01-09
status: research_complete
```
