# sec03_RAG技术演进：从Naive到Agentic - Research参考

**Research完成时间**: 2026-01-09
**用途**: 供Claude CLI编写第三节正文

---

## 写作指令

本节需覆盖RAG五阶段演进：
1. Naive RAG (2020-2022): 基础检索-拼接-生成
2. Advanced RAG (2022-2023): 预检索/后检索优化
3. Modular RAG (2023-2024): 模块化可组合架构
4. Graph RAG (2024): 知识图谱增强
5. Agentic RAG (2024-2025): 自主代理式RAG

预计字数：5,000-6,000字

---

## 核心文献

### 1. RAG综述（范式分类来源）

**Gao et al. (2023-2024)** [核心参考]
- **标题**: Retrieval-Augmented Generation for Large Language Models: A Survey
- **arXiv**: https://arxiv.org/abs/2312.10997
- **GitHub**: https://github.com/Tongji-KGLLM/RAG-Survey
- **引用数**: ~2,655
- **版本演进**: v1 (2023.12) → v5 (2024.03)
- **核心贡献**: 提出 **Naive RAG → Advanced RAG → Modular RAG** 三范式分类
- **本节用途**: 范式分类的权威来源

---

### 2. Naive RAG (2020-2022)

**特征**: "检索-拼接-生成"的简单流水线

**典型流程**:
```
用户提问 → 直接检索 → 拼接到Prompt → 一次性生成
```

**技术栈**:
- 检索: BM25 / TF-IDF (稀疏检索)
- 向量化: 基础BERT嵌入
- 生成: 直接拼接top-k文档

**局限性**:
- 低精度 (misaligned chunks)
- 低召回 (failure to retrieve all relevant)
- 无法处理复杂查询
- 检索与生成脱节

---

### 3. Advanced RAG (2022-2023)

**核心改进**: 预检索(Pre-retrieval) + 后检索(Post-retrieval) 优化

**预检索优化**:
| 技术 | 说明 |
|------|------|
| 查询改写 (Query Rewriting) | 扩展/精化用户查询 |
| HyDE | 假设性文档嵌入 |
| 混合检索 | 稀疏+稠密结合 |
| 元数据增强 | 添加时间/来源等信息 |

**HyDE (Gao et al., 2022)**
- **标题**: Precise Zero-Shot Dense Retrieval without Relevance Labels
- **arXiv**: https://arxiv.org/abs/2212.10496
- **核心思想**: 先让LLM生成假设性答案，用答案的嵌入去检索
- **本节用途**: 查询增强的代表性技术

**后检索优化**:
| 技术 | 说明 |
|------|------|
| 重排序 (Re-ranking) | 对检索结果二次排序 |
| 上下文压缩 | 去除冗余信息 |
| 融合检索 | 多路召回结果融合 |

**Contextual Retrieval (Anthropic, 2024)**
- **来源**: Anthropic官方博客
- **链接**: https://www.anthropic.com/news/contextual-retrieval
- **核心思想**: 为每个chunk添加上下文前缀
- **效果**: 检索失败率降低49%（结合BM25可降67%）
- **本节用途**: 工业界最佳实践

---

### 4. Modular RAG (2023-2024)

**核心特征**: 可组合的模块化架构

**模块类型** (Gao et al.):
- **检索模块**: 多种检索策略可替换
- **增强模块**: 查询改写、知识融合
- **生成模块**: 不同LLM可插拔
- **编排模块**: 控制流程逻辑

**代表性框架**:
- LangChain LCEL
- LlamaIndex Query Pipeline
- DSPy Modules

**本节用途**: 说明RAG从单一流水线到可组合架构的演进

---

### 5. Graph RAG (2024)

**Microsoft GraphRAG**
- **论文**: From Local to Global: A Graph RAG Approach to Query-Focused Summarization
- **arXiv**: https://arxiv.org/abs/2404.16130
- **GitHub**: https://github.com/microsoft/graphrag
- **发布时间**: 2024.04

**核心技术**:
- **实体提取**: LLM从文档中提取实体和关系
- **社区检测**: Leiden算法构建层次化社区
- **社区摘要**: 为每个社区生成摘要
- **全局查询**: 基于社区摘要回答全局性问题

**对比Naive RAG**:
| 维度 | Naive RAG | Graph RAG |
|------|-----------|-----------|
| 索引单位 | 文本块 | 实体+关系+社区 |
| 查询类型 | 局部事实 | 全局摘要 |
| 知识表示 | 向量 | 知识图谱 |

**本节用途**: 结构化知识增强RAG的代表

---

### 6. Agentic RAG (2024-2025)

**Singh et al. (2025)** [最新综述]
- **标题**: Agentic Retrieval-Augmented Generation: A Survey on Agentic RAG
- **arXiv**: https://arxiv.org/abs/2501.09136
- **发布时间**: 2025.01

**核心特征**:
1. **自主决策**: Agent独立评估和管理检索策略
2. **迭代精化**: 反馈循环持续改进
3. **工作流编排**: 动态任务调度
4. **多Agent协作**: 专业化分工

**代理设计模式** (Andrew Ng):
- Reflection (反思)
- Planning (规划)
- Tool Use (工具使用)
- Multi-agent Collaboration (多Agent协作)

**代表性系统**:

**Self-RAG (Asai et al., 2023)**
- **标题**: Self-RAG: Learning to Retrieve, Generate, and Critique through Self-Reflection
- **arXiv**: https://arxiv.org/abs/2310.11511
- **核心**: 自我反思决定是否检索、检索内容是否相关

**CRAG (Yan et al., 2024)**
- **标题**: Corrective Retrieval Augmented Generation
- **arXiv**: https://arxiv.org/abs/2401.15884
- **核心**: 评估检索质量，必要时触发网络搜索

**Adaptive-RAG (Jeong et al., 2024)**
- **标题**: Adaptive-RAG: Learning to Adapt Retrieval-Augmented Large Language Models through Question Complexity
- **arXiv**: https://arxiv.org/abs/2403.14403
- **核心**: 根据问题复杂度动态选择RAG策略

**System 1 vs System 2类比**:
- **Predefined RAG** ≈ System 1: 快速、结构化、规则驱动
- **Agentic RAG** ≈ System 2: 慢速、深思熟虑、自适应

---

### 7. 演进时间线

```
2020 ─── Lewis et al.: RAG正式提出 (NeurIPS)
         │
2020-22 ─ Naive RAG: 基础检索-生成流水线
         │
2022 ─── HyDE: 假设性文档嵌入
         │
2022-23 ─ Advanced RAG: 预/后检索优化
         │
2023 ─── Self-RAG: 自我反思机制
         │
         ├── Modular RAG: 可组合架构
         │
2024.01 ─ CRAG: 纠错检索
         │
2024.03 ─ Adaptive-RAG: 自适应策略
         │
2024.04 ─ GraphRAG: 知识图谱增强
         │
2024.09 ─ Contextual Retrieval (Anthropic)
         │
2025.01 ─ Agentic RAG Survey: 系统性综述
```

---

## 写作建议

### 结构建议
1. 开篇：RAG演进的驱动力（解决什么问题）
2. Naive RAG：基础架构与局限
3. Advanced RAG：预/后检索优化技术
4. Modular RAG：模块化设计原则
5. Graph RAG：结构化知识增强
6. Agentic RAG：自主代理与多步推理
7. 对比总结：五阶段特征表

### 关键数据点
- Gao et al. 综述引用数: ~2,655
- GraphRAG GitHub stars: 25k+
- Contextual Retrieval效果: 检索失败率↓49-67%
- Self-RAG: 在多个基准上超越ChatGPT

---

## 元信息

```yaml
section: 03_RAG技术演进
schema_position: 第三节
estimated_words: 5000-6000
core_references: 12篇
research_date: 2026-01-09
status: research_complete
```
