# 第四版第一章 Schema

**文档名称**: 01_研究背景与问题定义_v4.md  
**设计日期**: 2026-01-09  
**设计依据**: 法证式审计报告（对比用户18问18答文档）  

---

## 整体结构

```
第一章 研究背景与问题定义
│
├── 摘要
│
├── 一、引言：从参数化知识到检索增强 [保留v3]
│   ├── 1.1 大语言模型的知识困境
│   │   ├── 知识截止问题
│   │   ├── 幻觉问题
│   │   └── 知识更新成本
│   ├── 1.2 RAG范式的诞生：Lewis et al. (2020)
│   └── 1.3 RAG之前的探索：REALM、DPR、ORQA
│
├── 二、RAG技术演进：从Naive到Agentic（2020-2025）[保留v3]
│   ├── 2.1 RAG发展阶段划分（五阶段表）
│   ├── 2.2 Naive RAG：朴素范式的局限
│   ├── 2.3 Advanced RAG：技术优化的积累
│   │   ├── 查询优化（HyDE等）
│   │   ├── 分块策略
│   │   ├── 检索增强
│   │   └── Contextual Retrieval
│   ├── 2.4 Modular RAG：可组合的系统架构
│   ├── 2.5 Graph RAG：知识图谱增强
│   └── 2.6 Agentic RAG：智能体驱动的范式转变
│       ├── 迭代检索与推理
│       ├── 自我反思（Self-RAG）
│       ├── 工具调用
│       ├── 多智能体协作
│       └── System 1/System 2认知架构
│
├── 三、RAG的四大核心挑战 [保留v3]
│   ├── 3.1 幻觉问题
│   ├── 3.2 知识更新与时效性
│   ├── 3.3 检索质量与相关性
│   └── 3.4 隐性语义处理——被忽视的核心问题 ★
│       ├── 语用含义（Grice会话含义、PUB基准）
│       ├── 篇章结构（RST）
│       ├── 论证结构（图尔敏、省略三段论）
│       └── 常识推理（ATOMIC 2020）
│
├── 四、认知增强RAG：15个理论框架 [保留v3]
│   ├── 4.1 理论框架的三层架构（总表）
│   ├── 4.2 第一层：语义与语用基础（3个理论）
│   │   ├── 形式语义学
│   │   ├── 分布式语义学
│   │   └── 语用学三论（Grice/Austin-Searle/关联理论）
│   ├── 4.3 第二层：篇章与论证结构（6个理论）
│   │   ├── 修辞结构理论（RST）
│   │   ├── 图尔敏论证模型
│   │   ├── 框架语义学（FrameNet）
│   │   ├── 概念隐喻理论
│   │   ├── 叙事结构理论（Labov/脚本理论）
│   │   └── 会话分析
│   ├── 4.4 第三层：认知科学视角（6个理论）
│   │   ├── 认知负荷理论
│   │   ├── 信息觅食理论
│   │   ├── 情境模型理论
│   │   ├── 共同基础理论
│   │   ├── 具身认知理论
│   │   └── 认识警觉理论
│   ├── 4.5 补充理论：布鲁姆认知分类学
│   └── 4.6 理论框架的综合应用："Aurola过程"
│       ├── 核心思想："把书读厚"
│       ├── 入库端扩展（Document Expansion）
│       ├── 检索端发散（Query Expansion via Essay）
│       ├── 映射分析（Mapping Analysis）
│       └── 学术对应物表（HyDE/RAPTOR/Self-RAG/Contextual Retrieval）
│
├── 五、开源生态全景图 [★ 新增板块]
│   │
│   ├── 5.1 核心RAG框架代码级分析
│   │   ├── 5.1.1 LangChain生态（120k stars）
│   │   │   ├── 架构：Core/Community/LangGraph/LangServe
│   │   │   ├── LCEL设计理念
│   │   │   ├── 内置RAG组件深度分析
│   │   │   ├── 默认提示词模板逆向
│   │   │   ├── LangSmith商业边界
│   │   │   └── 社区争议与局限性
│   │   │
│   │   ├── 5.1.2 LlamaIndex生态（46k stars）
│   │   │   ├── 核心Index Types设计
│   │   │   ├── Query Engine vs Chat Engine
│   │   │   ├── Ingestion Pipeline实现
│   │   │   ├── 默认参数配置
│   │   │   ├── LlamaCloud商业边界
│   │   │   └── 与LangChain定位对比
│   │   │
│   │   ├── 5.1.3 DSPy（31k stars）
│   │   │   ├── 声明式提示编程范式
│   │   │   ├── Signature/Module/Optimizer架构
│   │   │   └── 与传统框架定位差异
│   │   │
│   │   ├── 5.1.4 Microsoft GraphRAG（29k stars）
│   │   │   ├── 完整技术架构
│   │   │   ├── Leiden聚类算法
│   │   │   ├── 源代码提示词模板
│   │   │   └── 部署资源需求
│   │   │
│   │   ├── 5.1.5 Haystack（24k stars）
│   │   │   └── Pipeline架构与生产就绪性
│   │   │
│   │   └── 5.1.6 其他重要框架
│   │       ├── RAGFlow（68k stars）—— 深度文档理解
│   │       ├── Dify（119k stars）—— 低代码平台
│   │       ├── Flowise（47k stars）—— 可视化构建
│   │       ├── Quivr（39k stars）—— 个人知识库
│   │       └── PrivateGPT（57k stars）—— 隐私本地RAG
│   │
│   ├── 5.2 LlamaIndex GUI生态 [★ 审计遗漏重点]
│   │   ├── 5.2.1 Kotaemon —— "版本答案"
│   │   │   ├── 核心优势：真·LlamaIndex内核
│   │   │   ├── Hybrid Search / Re-ranking支持
│   │   │   ├── 引文溯源功能
│   │   │   └── M1 Max部署方案
│   │   │
│   │   ├── 5.2.2 PrivateGPT v2.0
│   │   │   └── 底层从LangChain迁移到LlamaIndex
│   │   │
│   │   ├── 5.2.3 FlowiseAI
│   │   │   └── 可视化LlamaIndex组件编排
│   │   │
│   │   ├── 5.2.4 Verba (Golden RAGtriever)
│   │   │   └── Weaviate官方高级RAG UI
│   │   │
│   │   └── 5.2.5 SEC Insights
│   │       └── LlamaIndex官方全栈参考架构
│   │
│   ├── 5.3 向量数据库生态
│   │   ├── 开源：Milvus/Qdrant/Chroma/Weaviate/pgvector/FAISS
│   │   ├── 性能基准对比（VDBBench）
│   │   ├── 部署复杂度评估
│   │   └── 成本模型对比
│   │
│   ├── 5.4 嵌入模型生态
│   │   ├── 开源模型MTEB排名
│   │   ├── 中文嵌入模型推荐（BGE-M3）
│   │   ├── 商业API对比（OpenAI/Voyage/Cohere）
│   │   └── 本地部署方案（Ollama/MLX）
│   │
│   ├── 5.5 垂直领域RAG
│   │   ├── 代码RAG（Continue/Cursor/Cody）
│   │   ├── 法律RAG
│   │   ├── 医疗RAG（MedGraphRAG）
│   │   └── 学术RAG（PaperQA2）
│   │
│   ├── 5.6 评估与监控工具
│   │   ├── RAGAS（RAG评估框架）
│   │   ├── Langfuse（开源LLM工程平台）
│   │   ├── TruLens（可观测性）
│   │   └── Phoenix（Arize AI）
│   │
│   └── 5.7 依赖关系与商业化趋势
│       ├── 底层共享组件依赖链
│       └── 开源→商业化迁移风险评估
│
├── 六、Apple Silicon生态与边缘计算 [★ 新增板块]
│   │
│   ├── 6.1 M1 Max硬件特性与RAG适配
│   │   ├── 统一内存架构优势（64GB显存）
│   │   ├── vs NVIDIA显卡对比
│   │   └── 适合的模型规模（7B-32B甜点区）
│   │
│   ├── 6.2 MLX框架深度解析 [★ 审计P0遗漏]
│   │   ├── Apple官方ML框架定位
│   │   ├── 核心设计理念
│   │   ├── mlx-lm：LLM训练/推理
│   │   ├── mlx-rag：RAG示例项目
│   │   └── vs PyTorch MPS性能对比
│   │
│   ├── 6.3 本地模型选型指南
│   │   ├── Qwen 3系列深度分析
│   │   │   ├── 32B Dense vs 30B MoE
│   │   │   ├── Thinking Mode
│   │   │   └── 中文token效率
│   │   ├── Llama 3系列
│   │   └── 其他候选模型
│   │
│   ├── 6.4 微调 vs RAG：技术路线决策 [★ 审计遗漏]
│   │   ├── 微调≠知识内化
│   │   ├── 微调擅长 vs 不擅长
│   │   ├── RAG + Long Context是正确解法
│   │   └── KV Cache / Prompt Caching
│   │
│   ├── 6.5 MLX + LlamaIndex组合架构 [★ 审计遗漏]
│   │   ├── MLX作为计算层（硬件效率）
│   │   ├── LlamaIndex作为逻辑层（检索智能）
│   │   └── 本地API Server方案
│   │
│   └── 6.6 大规模知识库处理
│       ├── 亿级Token规模挑战
│       ├── Batch Processing内存管理
│       └── 渐进式索引构建
│
├── 七、MCP协议与AI工具集成 [★ 新增板块]
│   │
│   ├── 7.1 MCP协议概述 [★ 审计P0遗漏]
│   │   ├── Model Context Protocol定义
│   │   ├── Anthropic发布背景
│   │   └── 与传统API的区别
│   │
│   ├── 7.2 MCP信息流架构
│   │   ├── Host AI（大脑）
│   │   ├── MCP Server（图书管理员）
│   │   ├── LlamaIndex + ChromaDB（图书馆）
│   │   └── 完整信息流演示
│   │
│   ├── 7.3 "抓手"机制详解
│   │   ├── 层级摘要作为抓手
│   │   ├── 抓手消除噪音
│   │   ├── 父子关系保留
│   │   └── 共创感延续
│   │
│   ├── 7.4 检索 vs 遍历：两种模式
│   │   ├── 问答场景：Vector Search
│   │   ├── 目录生成场景：Metadata Query
│   │   └── Context Caching策略
│   │
│   └── 7.5 MCP Server实现指南
│       ├── Python SDK
│       ├── 工具定义示例
│       └── 与Claude Code集成
│
├── 八、隐性语义提取：从理论到Prompt [★ 新增板块]
│   │
│   ├── 8.1 学术资源库
│   │   ├── 8.1.1 Anthropic Cookbook
│   │   │   └── Contextual Retrieval提示词
│   │   │
│   │   ├── 8.1.2 Microsoft GraphRAG prompt_tune目录
│   │   │   └── Entity Extraction提示词
│   │   │
│   │   ├── 8.1.3 Fabric Prompt库 [★ 审计P0遗漏]
│   │   │   ├── extract_wisdom模式
│   │   │   └── analyze_claims模式
│   │   │
│   │   └── 8.1.4 关键论文GitHub仓库
│   │       ├── RAPTOR: parthsarthi03/raptor
│   │       ├── Self-RAG: AkariAsai/self-rag
│   │       └── GraphRAG: microsoft/graphrag
│   │
│   ├── 8.2 语言学驱动的Prompt工程
│   │   ├── 8.2.1 为什么学术术语能激活专家权重
│   │   ├── 8.2.2 四大探针设计
│   │   │   ├── 图尔敏探针：抓"逻辑断层"
│   │   │   ├── Frame探针：抓"技术场景"
│   │   │   ├── Labov探针：抓"排错高潮"
│   │   │   └── CA探针：抓"认知纠偏"
│   │   └── 8.2.3 JSON Schema设计
│   │
│   └── 8.3 Aurola Protocol完整Prompt模板 [★ 审计遗漏]
│       ├── System Prompt完整文本
│       ├── 输出格式规范
│       └── 针对Qwen/Claude/GPT的适配说明
│
├── 九、本综述的学术定位与贡献 [保留v3，微调]
│   ├── 9.1 与现有综述的关系
│   ├── 9.2 六个核心研究问题
│   └── 9.3 预期学术贡献（五大贡献）
│
├── 十、全书结构概览 [保留v3]
│   └── 六卷100章结构
│
└── 参考文献 [扩展]
    ├── RAG奠基与演进
    ├── RAG前沿范式
    ├── 隐性语义与语用推理
    ├── 篇章分析与论证挖掘
    ├── 常识推理与知识图谱
    ├── 认知科学与RAG
    ├── 神经-符号整合
    ├── 中文NLP资源
    ├── 评估与幻觉
    ├── 开源框架
    ├── Apple Silicon生态 [新增]
    └── MCP协议与工具集成 [新增]
```

---

## v3 → v4 变更摘要

| 板块 | v3状态 | v4动作 | 预估字数 |
|------|--------|--------|----------|
| 一、引言 | ✅ 完整 | 保留 | ~2,000 |
| 二、RAG演进 | ✅ 完整 | 保留 | ~4,000 |
| 三、四大挑战 | ✅ 完整 | 保留 | ~2,500 |
| 四、15理论框架 | ✅ 完整 | 保留 | ~6,500 |
| **五、开源生态全景图** | ❌ 缺失 | **新增** | **~8,000** |
| **六、Apple Silicon生态** | ❌ 缺失 | **新增** | **~4,000** |
| **七、MCP协议** | ❌ 缺失 | **新增** | **~3,000** |
| **八、隐性语义Prompt** | 🟡 理论有/实操无 | **新增** | **~3,000** |
| 九、学术定位 | ✅ 完整 | 微调 | ~1,500 |
| 十、全书结构 | ✅ 完整 | 保留 | ~500 |
| 参考文献 | ✅ 43篇 | 扩展至60+ | ~1,000 |

---

## 预估规模对比

| 指标 | v3 | v4预估 | 增幅 |
|------|-----|--------|------|
| 总字数 | ~18,000字 | **~36,000字** | +100% |
| 章节数 | 6节 | **10节** | +67% |
| 参考文献 | 43篇 | **60+篇** | +40% |
| 新增内容占比 | - | **~50%** | - |

---

## 新增板块的数据来源

### 第五节：开源生态全景图
- **主要来源**: 本次Research报告（RAG开源生态系统全景深度调研）
- **补充调研**: Kotaemon、Verba、SEC Insights

### 第六节：Apple Silicon生态
- **主要来源**: 用户提供的18问18答文档（Q1-Q3, Q8）
- **补充调研**: MLX框架官方文档、mlx-rag项目

### 第七节：MCP协议
- **主要来源**: 用户提供的18问18答文档（Q10-Q13）
- **补充调研**: Anthropic MCP官方文档

### 第八节：隐性语义Prompt
- **主要来源**: 用户提供的18问18答文档（Q14-Q18）
- **补充调研**: Fabric项目、GraphRAG prompt_tune目录

---

## 执行计划

| 步骤 | 内容 | 依赖 | 预计产出 |
|------|------|------|----------|
| 1 | 补充Research：MLX/MCP/Kotaemon/Fabric | 无 | 补充调研报告 |
| 2 | 撰写第五节：开源生态全景图 | 已有Research + 步骤1 | ~8,000字 |
| 3 | 撰写第六节：Apple Silicon生态 | 步骤1 | ~4,000字 |
| 4 | 撰写第七节：MCP协议 | 步骤1 | ~3,000字 |
| 5 | 撰写第八节：隐性语义Prompt | 步骤1 | ~3,000字 |
| 6 | 整合v3内容 + 新增内容 | 步骤2-5 | v4完整文件 |
| 7 | 参考文献扩展 | 步骤6 | 60+篇 |
| 8 | 质量审核 | 步骤7 | 最终v4 |

---

## 元信息

```yaml
schema_version: 4.0
design_date: 2026-01-09
design_basis: 法证式审计报告
target_document: 01_研究背景与问题定义_v4.md
estimated_word_count: 36000
estimated_sections: 10
estimated_references: 60+
new_content_ratio: 50%
critical_additions:
  - 开源生态全景图
  - Apple Silicon生态
  - MCP协议
  - 隐性语义Prompt模板
status: schema_approved_pending_execution
```
