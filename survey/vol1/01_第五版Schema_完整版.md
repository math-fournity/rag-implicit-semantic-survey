# 第五版第一章 Schema（完整版）

**文档名称**: 01_研究背景与问题定义_v5.md  
**设计日期**: 2026-01-09  
**设计依据**: 
- 法证式审计报告（一）：《Aurola过程》文档 vs v3（61项，覆盖率29.5%）
- 法证式审计报告（二）：《隐性语义提取》文档 vs v3（70项，覆盖率34.3%）
- v5 Research报告（2024-2025最新学术进展+中文隐性语义研究）

---

## 设计原则

### 两份审计的核心发现

| 审计来源 | 主要缺失类型 | 性质 |
|----------|-------------|------|
| 《Aurola过程》 | 工具链、MLX、MCP、GUI生态 | **工程基础设施缺失** |
| 《隐性语义提取》 | Prompt模板、分层策略、实操指南 | **方法论实操缺失** |

### v5设计目标

1. **完整继承v4所有细节**：不因结构调整而丢失任何信息
2. **补齐工程基础设施**：MLX、MCP、工具链生态
3. **补齐方法论实操**：Prompt模板、分层处理、双轨提取
4. **强化理论→实践桥梁**：学术术语激活原理、标注指南借用技巧
5. **加强中文隐性语义研究**：OCNLI、ORCHID、SwordsmanImp等中文资源

---

## 第五版结构（12节）

```
第一章 研究背景与问题定义 (v5.0)
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
├── 二、知识嵌入的技术路线全景 [★ 新增——审计二Q1]
│   │
│   ├── 2.1 参数化知识 vs 检索增强：本质对比
│   │   ├── "闭卷考试" vs "开卷考试"比喻
│   │   └── 各自适用场景
│   │
│   ├── 2.2 知识嵌入的四条技术路径
│   │   ├── 2.2.1 增量预训练 (Continued Pre-training / DAPT)
│   │   │   ├── 原理：在领域数据上继续预训练
│   │   │   ├── 灾难性遗忘 (Catastrophic Forgetting)
│   │   │   ├── Replay Data混合策略（如8:1:1比例）
│   │   │   ├── Self-Synthesized Rehearsal (ACL 2024)
│   │   │   └── 阅读理解增强方法 (ICLR 2024)
│   │   │
│   │   ├── 2.2.2 有监督微调 (SFT)
│   │   │   ├── **关键洞察：SFT是教"规矩"而非"知识"**
│   │   │   ├── LIMA"表面对齐假设"验证 (NeurIPS 2023)
│   │   │   ├── 幻觉风险
│   │   │   ├── 最佳实践：激活而非灌输
│   │   │   └── Prompt Distillation新方法
│   │   │
│   │   ├── 2.2.3 参数高效微调 (PEFT/LoRA)
│   │   │   ├── 原理：冻结主参数，训练旁路矩阵
│   │   │   ├── 模块化知识：法律LoRA、编程LoRA
│   │   │   ├── 成本优势
│   │   │   ├── **DoRA (ICML 2024 Oral)**：magnitude/direction分解
│   │   │   ├── QLoRA：4-bit量化，单GPU训练65B
│   │   │   └── QDoRA："2025年PEFT新标准"
│   │   │
│   │   └── 2.2.4 模型编辑 (Knowledge Editing)
│   │       ├── ROME：causal tracing定位知识
│   │       ├── MEMIT：跨层批量编辑
│   │       ├── WISE (NeurIPS 2024)：持续知识编辑
│   │       ├── **EasyEdit框架** (ACL 2024)
│   │       └── 当前局限：连续编辑10次后性能下降
│   │
│   ├── 2.3 混合架构：工业界的终极方案
│   │   ├── 稳定知识 → 增量预训练（长期记忆）
│   │   ├── 时效知识 → RAG（短期工作记忆）
│   │   └── Long Context + Context Caching趋势
│   │
│   └── 2.4 微调 vs RAG：技术路线决策框架 [审计一Q4]
│       ├── 微调≠知识内化（EMNLP 2024验证）
│       ├── 微调擅长 vs 不擅长
│       ├── RAG + Long Context是正确解法
│       ├── KV Cache优化
│       └── Prompt Caching商业实践对比
│           ├── Anthropic：显式cache_control，读取-90%
│           ├── OpenAI：自动缓存，-50%
│           └── Gemini：隐式+显式，-75%
│
├── 三、RAG技术演进：从Naive到Agentic（2020-2025）[保留v3，微调]
│   ├── 3.1 RAG发展阶段划分（五阶段表）
│   ├── 3.2 Naive RAG：朴素范式的局限
│   ├── 3.3 Advanced RAG：技术优化的积累
│   │   ├── 查询优化（HyDE等）
│   │   ├── 分块策略
│   │   ├── 检索增强
│   │   ├── Contextual Retrieval (Anthropic)
│   │   └── **Late Chunking** (Jina AI, arXiv:2409.04701)
│   ├── 3.4 Modular RAG：可组合的系统架构
│   ├── 3.5 Graph RAG：知识图谱增强
│   │   ├── Leiden算法层级聚类
│   │   ├── 社区摘要预计算
│   │   ├── **信息密度压缩原理** [审计二Q5补充]
│   │   └── Global vs Local Search对比
│   └── 3.6 Agentic RAG：智能体驱动的范式转变
│       ├── 迭代检索与推理
│       ├── 自我反思（Self-RAG）
│       ├── 工具调用
│       ├── 多智能体协作
│       └── System 1/System 2认知架构
│
├── 四、超长文档与大规模知识库处理 [★ 新增——审计二Q4-Q5]
│   │
│   ├── 4.1 超长文档的挑战
│   │   ├── 上下文窗口的物理限制
│   │   ├── 对话录的特殊性：上下文依赖极强
│   │   ├── 亿级Token规模场景
│   │   └── Gemini 1.5的10M tokens突破
│   │
│   ├── 4.2 语义切分策略 (Semantic Segmentation)
│   │   ├── 按话题而非Token切分
│   │   ├── 识别转折点
│   │   └── 滑动窗口保留上下文（10-20%重叠）
│   │
│   ├── 4.3 Map-Reduce递归摘要 [审计二Q4核心]
│   │   ├── Map阶段：分块摘要
│   │   ├── Reduce阶段：全局汇总
│   │   ├── 生成"文件级索引"
│   │   ├── **BooookScore** (ICLR 2024)：书籍级摘要评估
│   │   └── LangChain MapReduceDocumentsChain实现
│   │
│   ├── 4.4 父子索引架构 (Parent-Child Indexing) [审计二Q4核心]
│   │   ├── 父文档：摘要/大块
│   │   ├── 子文档：原始切片
│   │   ├── 检索流程：匹配父→取出子
│   │   ├── LlamaIndex HierarchicalNodeParser
│   │   │   └── 多级层次（2048→512→128 tokens）
│   │   ├── AutoMergingRetriever
│   │   │   └── 50%以上子节点被检索时自动返回父节点
│   │   └── LlamaIndex SummaryIndex
│   │
│   ├── 4.5 结构化导航：Top-Down Drill-Down [审计二Q5]
│   │   ├── 宏观层：顶层摘要
│   │   ├── 路径选择：图谱关联
│   │   ├── 微观层：精准定位原始片段
│   │   ├── **"只读最关键的0.1%"原理**
│   │   └── GraphRAG对Naive RAG达70-80%胜率
│   │
│   └── 4.6 递归问答 (Iterative Refinement)
│       ├── 分治法处理超复杂问题
│       └── 中间状态累积
│
├── 五、RAG的四大核心挑战 [保留v3]
│   ├── 5.1 幻觉问题
│   ├── 5.2 知识更新与时效性
│   ├── 5.3 检索质量与相关性
│   └── 5.4 隐性语义处理——被忽视的核心问题 ★
│       ├── 语用含义（Grice会话含义、PUB基准）
│       ├── 篇章结构（RST）
│       ├── 论证结构（图尔敏、省略三段论）
│       └── 常识推理（ATOMIC 2020）
│
├── 六、认知增强RAG：15个理论框架 [保留v3]
│   ├── 6.1 理论框架的三层架构（总表）
│   ├── 6.2 第一层：语义与语用基础（3个理论）
│   │   ├── 形式语义学
│   │   ├── 分布式语义学
│   │   └── 语用学三论（Grice/Austin-Searle/关联理论）
│   ├── 6.3 第二层：篇章与论证结构（6个理论）
│   │   ├── 修辞结构理论（RST）
│   │   ├── 图尔敏论证模型
│   │   ├── 框架语义学（FrameNet）
│   │   ├── 概念隐喻理论
│   │   ├── 叙事结构理论（Labov/脚本理论）
│   │   └── 会话分析
│   ├── 6.4 第三层：认知科学视角（6个理论）
│   │   ├── 认知负荷理论
│   │   ├── 信息觅食理论
│   │   ├── 情境模型理论
│   │   ├── 共同基础理论
│   │   ├── 具身认知理论
│   │   └── 认识警觉理论
│   ├── 6.5 补充理论：布鲁姆认知分类学
│   └── 6.6 理论框架的综合应用："Aurola过程"
│       ├── 核心思想："把书读厚"
│       ├── 入库端扩展（Document Expansion）
│       ├── 检索端发散（Query Expansion via Essay）
│       ├── 映射分析（Mapping Analysis）
│       └── 学术对应物表（HyDE/RAPTOR/Self-RAG/Contextual Retrieval）
│
├── 七、开源生态全景图 [保留v4完整细节，扩展]
│   │
│   ├── 7.1 核心RAG框架代码级分析
│   │   ├── 7.1.1 LangChain生态（120k stars）
│   │   │   ├── 架构：Core/Community/LangGraph/LangServe
│   │   │   ├── LCEL设计理念
│   │   │   ├── 内置RAG组件深度分析
│   │   │   ├── 默认提示词模板逆向
│   │   │   ├── LangSmith商业边界
│   │   │   └── 社区争议与局限性
│   │   │
│   │   ├── 7.1.2 LlamaIndex生态（46k stars）
│   │   │   ├── 核心Index Types设计
│   │   │   ├── Query Engine vs Chat Engine
│   │   │   ├── Ingestion Pipeline实现
│   │   │   ├── 默认参数配置
│   │   │   ├── LlamaCloud商业边界
│   │   │   └── 与LangChain定位对比
│   │   │
│   │   ├── 7.1.3 DSPy（31k stars）
│   │   │   ├── 声明式提示编程范式
│   │   │   ├── Signature/Module/Optimizer架构
│   │   │   └── 与传统框架定位差异
│   │   │
│   │   ├── 7.1.4 Microsoft GraphRAG（29k stars）
│   │   │   ├── 完整技术架构
│   │   │   ├── Leiden聚类算法
│   │   │   ├── 源代码提示词模板
│   │   │   └── 部署资源需求
│   │   │
│   │   ├── 7.1.5 Haystack（24k stars）
│   │   │   └── Pipeline架构与生产就绪性
│   │   │
│   │   └── 7.1.6 其他重要框架
│   │       ├── RAGFlow（68k stars）—— 深度文档理解
│   │       ├── Dify（119k stars）—— 低代码平台
│   │       ├── Flowise（47k stars）—— 可视化构建
│   │       ├── Quivr（39k stars）—— 个人知识库
│   │       └── PrivateGPT（57k stars）—— 隐私本地RAG
│   │
│   ├── 7.2 LlamaIndex GUI生态 [★ 审计遗漏重点]
│   │   ├── 7.2.1 Kotaemon —— "版本答案"
│   │   │   ├── 核心优势：真·LlamaIndex内核
│   │   │   ├── Hybrid Search / Re-ranking支持
│   │   │   ├── 引文溯源功能
│   │   │   └── M1 Max部署方案
│   │   │
│   │   ├── 7.2.2 PrivateGPT v2.0
│   │   │   └── 底层从LangChain迁移到LlamaIndex
│   │   │
│   │   ├── 7.2.3 FlowiseAI
│   │   │   └── 可视化LlamaIndex组件编排
│   │   │
│   │   ├── 7.2.4 Verba (Golden RAGtriever)
│   │   │   └── Weaviate官方高级RAG UI
│   │   │
│   │   └── 7.2.5 SEC Insights
│   │       └── LlamaIndex官方全栈参考架构
│   │
│   ├── 7.3 其他重要工具 [审计二Q3补充]
│   │   ├── Coze/扣子（字节跳动）
│   │   ├── Google NotebookLM
│   │   └── ChatBase / FastGPT
│   │
│   ├── 7.4 向量数据库生态
│   │   ├── 开源：Milvus/Qdrant/Chroma/Weaviate/pgvector/FAISS
│   │   ├── 性能基准对比（VDBBench）
│   │   ├── 部署复杂度评估
│   │   └── 成本模型对比
│   │
│   ├── 7.5 嵌入模型生态
│   │   ├── 开源模型MTEB排名
│   │   ├── 中文嵌入模型推荐（BGE-M3）
│   │   ├── 商业API对比（OpenAI/Voyage/Cohere）
│   │   └── 本地部署方案（Ollama/MLX）
│   │
│   ├── 7.6 垂直领域RAG
│   │   ├── 代码RAG（Continue/Cursor/Cody）
│   │   ├── 法律RAG
│   │   ├── 医疗RAG（MedGraphRAG）
│   │   └── 学术RAG（PaperQA2）
│   │
│   ├── 7.7 评估与监控工具
│   │   ├── RAGAS（RAG评估框架）
│   │   ├── Langfuse（开源LLM工程平台）
│   │   ├── TruLens（可观测性）
│   │   └── Phoenix（Arize AI）
│   │
│   └── 7.8 依赖关系与商业化趋势
│       ├── 底层共享组件依赖链
│       └── 开源→商业化迁移风险评估
│
├── 八、Apple Silicon生态与边缘计算 [保留v4完整细节]
│   │
│   ├── 8.1 M1 Max硬件特性与RAG适配
│   │   ├── 统一内存架构优势（64GB显存）
│   │   ├── vs NVIDIA显卡对比
│   │   └── 适合的模型规模（7B-32B甜点区）
│   │
│   ├── 8.2 MLX框架深度解析 [★ 审计P0遗漏]
│   │   ├── Apple官方ML框架定位
│   │   ├── 核心设计理念
│   │   ├── mlx-lm：LLM训练/推理
│   │   ├── mlx-rag：RAG示例项目
│   │   └── vs PyTorch MPS性能对比
│   │
│   ├── 8.3 本地模型选型指南
│   │   ├── Qwen 3系列深度分析
│   │   │   ├── 32B Dense vs 30B MoE
│   │   │   ├── Thinking Mode
│   │   │   └── 中文token效率
│   │   ├── Llama 3系列
│   │   └── 其他候选模型
│   │
│   ├── 8.4 MLX + LlamaIndex组合架构 [★ 审计遗漏]
│   │   ├── MLX作为计算层（硬件效率）
│   │   ├── LlamaIndex作为逻辑层（检索智能）
│   │   └── 本地API Server方案
│   │
│   └── 8.5 大规模知识库处理
│       ├── 亿级Token规模挑战
│       ├── Batch Processing内存管理
│       └── 渐进式索引构建
│
├── 九、MCP协议与AI工具集成 [保留v4完整细节]
│   │
│   ├── 9.1 MCP协议概述 [★ 审计P0遗漏]
│   │   ├── Model Context Protocol定义
│   │   ├── Anthropic发布背景
│   │   └── 与传统API的区别
│   │
│   ├── 9.2 MCP信息流架构
│   │   ├── Host AI（大脑）
│   │   ├── MCP Server（图书管理员）
│   │   ├── LlamaIndex + ChromaDB（图书馆）
│   │   └── 完整信息流演示
│   │
│   ├── 9.3 "抓手"机制详解
│   │   ├── 层级摘要作为抓手
│   │   ├── 抓手消除噪音
│   │   ├── 父子关系保留
│   │   └── 共创感延续
│   │
│   ├── 9.4 检索 vs 遍历：两种模式
│   │   ├── 问答场景：Vector Search
│   │   ├── 目录生成场景：Metadata Query
│   │   └── Context Caching策略
│   │
│   └── 9.5 MCP Server实现指南
│       ├── Python SDK
│       ├── 工具定义示例
│       └── 与Claude Code集成
│
├── 十、隐性语义提取：从理论到Prompt [★ 大幅扩展——审计二核心]
│   │
│   ├── 10.1 显性关键词 vs 隐性语义标签 [审计二Q6-Q7核心]
│   │   ├── "反证法"案例：文中未提但逻辑存在
│   │   ├── GraphRAG的LLM深度阅读机制
│   │   └── LLM生成元数据"挂"在片段上
│   │
│   ├── 10.2 双轨提取策略 [审计二P0]
│   │   ├── **轨道一：显性实体提取**
│   │   │   ├── 专有名词
│   │   │   ├── 技术工件（函数名、错误码）
│   │   │   └── 具体数值
│   │   │
│   │   └── **轨道二：隐性语义推断**
│   │       ├── 逻辑模式识别（反证法、自顶向下）
│   │       ├── 代码/架构模式（单例、工厂）
│   │       └── 情感与意图识别
│   │
│   ├── 10.3 学术术语激活专家权重 [审计二P0核心洞察]
│   │   ├── **原理：学术术语是"咒语"**
│   │   ├── 激活LLM预训练中的专家神经元
│   │   ├── 避免同义词混乱，收敛到标准术语
│   │   └── 示例：提"Toulmin Model"比提"论证结构"更精准
│   │
│   ├── 10.4 四大探针设计 [保留v4]
│   │   ├── 图尔敏探针：抓"逻辑断层"
│   │   ├── Frame探针：抓"技术场景"
│   │   ├── Labov探针：抓"排错高潮"
│   │   └── CA探针：抓"认知纠偏"
│   │
│   ├── 10.5 八大框架分层处理策略 [审计二P0]
│   │   ├── **Layer 1（基础层）**：FrameNet + Illocution
│   │   │   └── 提取基础实体和意图
│   │   ├── **Layer 2（深度层）**：RST + Toulmin
│   │   │   └── 提取逻辑链条，连接节点关系
│   │   └── **Layer 3（元数据层）**：Bloom + Narrative
│   │       └── 文档打分、分段，用于搜索排序
│   │
│   ├── 10.6 借用标注指南的降维打击 [审计二P0]
│   │   ├── 学术数据集的Annotation Guidelines
│   │   ├── **技巧：把标注规则直接抄给LLM**
│   │   └── 推荐数据集：SNLI, MultiNLI, Atomic
│   │
│   ├── 10.7 隐性语义的学术研究坐标 [审计二Q11]
│   │   ├── 自然语言推理 (NLI)
│   │   │   └── Implied NLI：现有模型表现接近随机
│   │   ├── 文本蕴涵 (RTE)
│   │   ├── 隐性论元提取 (iSRL)
│   │   ├── 论辩挖掘 (Argumentation Mining)
│   │   ├── 语用推理 (Pragmatic Inference)
│   │   └── 常识推理 (Commonsense Reasoning)
│   │       └── COMET-ATOMIC 2020：1.33M常识推理元组
│   │
│   ├── 10.8 中文隐性语义研究 [★ 新增——Research报告]
│   │   ├── 10.8.1 中文NLI数据集
│   │   │   ├── **OCNLI** (EMNLP 2020)：首个原生中文NLI
│   │   │   ├── CMNLI（翻译版）
│   │   │   └── CLUE Benchmark
│   │   │
│   │   ├── 10.8.2 中文论辩挖掘
│   │   │   ├── **ORCHID** (EMNLP 2023)：首个中文论辩对话数据集
│   │   │   ├── C-MTCSD (WWW 2025)：多轮对话立场检测
│   │   │   └── 中文论辩特点：意合性强、修辞丰富
│   │   │
│   │   ├── 10.8.3 中文语用推理
│   │   │   ├── **SwordsmanImp** (CCL 2024)：首个中文多轮对话会话含义数据集
│   │   │   └── 基于《武林外传》对话，含格莱斯准则标注
│   │   │
│   │   ├── 10.8.4 中文常识知识库
│   │   │   ├── CN-DBpedia（复旦）：1600万+实体
│   │   │   ├── HowNet/OpenHowNet（中科院/清华）：义原标注
│   │   │   ├── XLore（清华）：246万概念
│   │   │   └── CHARM：中文常识推理基准
│   │   │
│   │   └── 10.8.5 代表性研究团队
│   │       ├── 清华THUNLP（孙茂松、刘知远）
│   │       ├── 哈工大SCIR：LTP平台
│   │       ├── 复旦知识工场：CN-DBpedia
│   │       └── 上海交大计算语言学
│   │
│   └── 10.9 学术资源库
│       ├── 10.9.1 Anthropic Cookbook
│       │   └── Contextual Retrieval提示词
│       │
│       ├── 10.9.2 Microsoft GraphRAG prompt_tune目录
│       │   └── Entity Extraction提示词
│       │
│       ├── 10.9.3 Fabric Prompt库 [★ 审计P0遗漏]
│       │   ├── extract_wisdom模式
│       │   └── analyze_claims模式
│       │
│       └── 10.9.4 关键论文GitHub仓库
│           ├── RAPTOR: parthsarthi03/raptor
│           ├── Self-RAG: AkariAsai/self-rag
│           ├── GraphRAG: microsoft/graphrag
│           ├── EasyEdit: zjunlp/EasyEdit
│           ├── OpenHowNet: thunlp/OpenHowNet
│           ├── 哈工大LTP: HIT-SCIR/ltp
│           └── CLUE Benchmark: CLUEbenchmark/CLUE
│
├── 十一、Aurola Protocol：完整Prompt规范 [★ 新增——审计二Q8-Q9]
│   │
│   ├── 11.1 System Prompt完整文本（中英双语）
│   │   ├── 角色定义：语言学与认知分析专家
│   │   ├── 四大理论框架指令
│   │   │   ├── RST修辞结构分析
│   │   │   ├── Illocutionary Force言外之力
│   │   │   ├── Frame Semantics语义框架
│   │   │   └── Toulmin Model隐性担保
│   │   └── 四大进阶框架指令
│   │       ├── CMT概念隐喻
│   │       ├── Labov叙事结构
│   │       ├── CA会话分析
│   │       └── Bloom认知分类
│   │
│   ├── 11.2 JSON输出Schema规范
│   │   ├── explicit_keywords数组
│   │   ├── implicit_concepts数组
│   │   ├── cognitive_analysis对象
│   │   │   ├── type字段
│   │   │   ├── content字段
│   │   │   └── confidence字段
│   │   └── aurola_essay字段
│   │
│   ├── 11.3 GraphRAG Entity Extraction定制Prompt [审计二Q6]
│   │   ├── 默认Prompt的局限
│   │   ├── 定制指令示例
│   │   └── 输出格式：Nodes + Relationships
│   │
│   ├── 11.4 分场景Prompt变体
│   │   ├── 技术对话场景
│   │   ├── 学术论文场景
│   │   └── 商业文档场景
│   │
│   └── 11.5 针对不同模型的适配说明 [保留v4]
│       ├── Qwen适配
│       ├── Claude适配
│       └── GPT适配
│
├── 十二、本综述的学术定位与贡献 [保留v3]
│   ├── 12.1 与现有综述的关系
│   ├── 12.2 六个核心研究问题
│   └── 12.3 预期学术贡献（五大贡献）
│
├── 附录A：全书结构概览（六卷100章）
│
└── 参考文献 [扩展至80+篇]
    ├── RAG奠基与演进
    ├── RAG前沿范式
    ├── 隐性语义与语用推理
    ├── 篇章分析与论证挖掘
    ├── 常识推理与知识图谱
    ├── 认知科学与RAG
    ├── 神经-符号整合
    ├── 中文NLP资源 [扩展]
    │   ├── OCNLI、ORCHID、SwordsmanImp
    │   ├── CN-DBpedia、HowNet
    │   └── CLUE Benchmark
    ├── 评估与幻觉
    ├── 开源框架
    ├── Apple Silicon生态
    ├── MCP协议与工具集成
    ├── 知识嵌入技术 [新增]
    │   ├── DAPT、LIMA、DoRA
    │   └── ROME、MEMIT、EasyEdit
    └── 隐性语义提取学术文献 [新增]
```

---

## v3 → v4 → v5 变更追溯

### v3 → v4 变更（保留）

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

### v4 → v5 变更（新增）

| 变更类型 | 内容 | 来源 | 预估字数 |
|----------|------|------|----------|
| **新增节** | 二、知识嵌入的技术路线全景 | 审计二Q1 + Research | **~5,000** |
| **新增节** | 四、超长文档与大规模知识库处理 | 审计二Q4-Q5 + Research | **~4,000** |
| **新增节** | 十一、Aurola Protocol完整Prompt规范 | 审计二Q8-Q9 | **~3,000** |
| **大幅扩展** | 十、隐性语义提取（含中文研究） | 审计二Q6-Q11 + Research | **~6,000** |
| **扩展** | 三、RAG演进（Late Chunking等） | Research | **+500** |
| **扩展** | 七、开源生态（工具补充） | 审计二Q3 | **+500** |
| **扩展** | 参考文献（中文+知识嵌入） | Research | **+1,000** |

---

## 新增板块的数据来源

### 第二节：知识嵌入技术路线
- **主要来源**: 审计二《隐性语义提取》Q1
- **补充调研**: 
  - ICLR 2024: Adapting LLMs via Reading Comprehension
  - ACL 2024: Self-Synthesized Rehearsal
  - ICML 2024: DoRA
  - ACL 2024: EasyEdit
  - NeurIPS 2024: WISE

### 第四节：超长文档处理
- **主要来源**: 审计二《隐性语义提取》Q4-Q5
- **补充调研**:
  - ICLR 2024: BooookScore
  - LlamaIndex官方文档: HierarchicalNodeParser
  - arXiv:2409.04701: Late Chunking

### 第十节：隐性语义提取（中文部分）
- **主要来源**: v5 Research报告
- **学术来源**:
  - EMNLP 2020: OCNLI
  - EMNLP 2023: ORCHID
  - CCL 2024: SwordsmanImp
  - 清华THUNLP、哈工大SCIR、复旦知识工场

### 第十一节：Aurola Protocol
- **主要来源**: 审计二《隐性语义提取》Q8-Q9
- **补充调研**: Fabric项目、GraphRAG prompt_tune目录

---

## 两份审计P0遗漏的覆盖情况

### 审计一（Aurola过程）P0遗漏

| 遗漏项 | v5覆盖位置 | 状态 |
|--------|-----------|------|
| MLX框架 | 8.2 | ✅ |
| MCP协议 | 9.1-9.5 | ✅ |
| Kotaemon | 7.2.1 | ✅ |
| Fabric Prompt库 | 10.9.3 | ✅ |

### 审计二（隐性语义提取）P0遗漏

| 遗漏项 | v5覆盖位置 | 状态 |
|--------|-----------|------|
| 双轨提取策略 | 10.2 | ✅ |
| 学术Prompt完整模板 | 11.1 | ✅ |
| 8框架分层处理策略 | 10.5 | ✅ |
| 学术术语激活专家权重原理 | 10.3 | ✅ |
| 借用标注指南技巧 | 10.6 | ✅ |

---

## 规模预估

| 指标 | v3 | v4 | v5预估 | v4→v5增幅 |
|------|-----|-----|--------|----------|
| 总字数 | 18,000 | 36,000 | **55,000** | +53% |
| 章节数 | 6 | 10 | **12** | +20% |
| 参考文献 | 43 | 60 | **80+** | +33% |
| 新增内容 | - | 50% | **35%** | - |

---

## 执行计划

| 步骤 | 内容 | 依赖 | 预计产出 |
|------|------|------|----------|
| 1 | 整合v3基础内容 | 无 | 第一/五/六/十二节 |
| 2 | 整合v4新增内容 | 步骤1 | 第七/八/九节（完整细节） |
| 3 | 撰写第二节：知识嵌入技术路线 | Research报告 | ~5,000字 |
| 4 | 撰写第四节：超长文档处理 | Research报告 | ~4,000字 |
| 5 | 扩展第三节：RAG演进（Late Chunking等） | Research报告 | +500字 |
| 6 | 扩展第十节：隐性语义提取（含中文研究） | Research报告 | ~6,000字 |
| 7 | 撰写第十一节：Aurola Protocol | 审计二 | ~3,000字 |
| 8 | 参考文献扩展（中文+知识嵌入） | 步骤3-7 | 80+篇 |
| 9 | 质量审核与格式统一 | 步骤1-8 | 最终v5 |

---

## 元信息

```yaml
schema_version: 5.0
design_date: 2026-01-09
design_basis:
  - 法证式审计报告（一）：Aurola过程
  - 法证式审计报告（二）：隐性语义提取
  - v5 Research报告（2024-2025最新学术进展）
target_document: 01_研究背景与问题定义_v5.md
estimated_word_count: 55000
estimated_sections: 12
estimated_references: 80+
v4_detail_preservation: 100%  # 完整保留v4所有细节
p0_coverage:
  audit_1: 4/4 (100%)
  audit_2: 5/5 (100%)
key_additions:
  - 知识嵌入技术路线全景（含DoRA、EasyEdit）
  - 超长文档与大规模知识库处理（含BooookScore、Late Chunking）
  - 双轨提取策略
  - 学术术语激活专家权重原理
  - 八框架分层处理策略
  - 中文隐性语义研究（OCNLI、ORCHID、SwordsmanImp）
  - Aurola Protocol完整Prompt规范
status: schema_approved_pending_execution
```
