# 第六版第一章 Schema

**文档名称**: 01_研究背景与问题定义_v6.md  
**设计日期**: 2026-01-09  
**版本演进**: v3 → v4 → v5 → **v6**  
**设计依据**: 
- 法证式审计报告（一）：《Aurola过程》文档 vs v3（61项，覆盖率29.5%）
- 法证式审计报告（二）：《隐性语义提取》文档 vs v3（70项，覆盖率34.3%）
- v5 Research报告（2024-2025最新学术进展+中文隐性语义研究）
- v4/v5所有细节的完整继承

---

## 整体结构

```
第一章 研究背景与问题定义 (v6.0)
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
├── 二、知识嵌入的技术路线全景 [★ v5新增，v6扩展]
│   │
│   ├── 2.1 参数化知识 vs 检索增强：本质对比
│   │   ├── "闭卷考试" vs "开卷考试"比喻
│   │   ├── 各自适用场景
│   │   └── 混合策略的必然性
│   │
│   ├── 2.2 知识嵌入的四条技术路径
│   │   │
│   │   ├── 2.2.1 增量预训练 (Continued Pre-training / DAPT)
│   │   │   ├── 原理：在领域数据上继续预训练
│   │   │   ├── 灾难性遗忘 (Catastrophic Forgetting)
│   │   │   │   ├── 定义与机制
│   │   │   │   └── ACM Computing Surveys 2025综述
│   │   │   ├── 遗忘缓解方法
│   │   │   │   ├── **Replay/Rehearsal**：存储约5%历史数据混合训练
│   │   │   │   ├── **Self-Synthesized Rehearsal** (ACL 2024)：LLM自身生成合成数据
│   │   │   │   ├── **Importance-aware Soft-masking**：根据参数重要性调节梯度
│   │   │   │   └── **Memory Decoder**：模型无关的领域适应
│   │   │   ├── Replay Data混合策略
│   │   │   │   ├── GeoGalactica：8:1:1（领域:ArXiv:Code）
│   │   │   │   └── ChipNeMo (NVIDIA 2024)：工业级芯片设计DAPT
│   │   │   ├── 阅读理解增强方法 (ICLR 2024)
│   │   │   │   └── 直接CPT损害prompting能力，需阅读理解任务增强
│   │   │   └── 关键论文
│   │   │       ├── Don't Stop Pretraining (ACL 2020)：奠基性DAPT/TAPT
│   │   │       ├── Adapting LLMs via Reading Comprehension (ICLR 2024)
│   │   │       └── Mitigating Catastrophic Forgetting (ACL 2024)
│   │   │
│   │   ├── 2.2.2 有监督微调 (SFT)
│   │   │   ├── **关键洞察：SFT是教"规矩"而非"知识"**
│   │   │   ├── LIMA"表面对齐假设"验证 (NeurIPS 2023)
│   │   │   │   └── 模型知识几乎完全在预训练阶段学习
│   │   │   ├── EMNLP 2024验证：微调无法与RAG竞争
│   │   │   ├── 幻觉风险
│   │   │   │   └── 微调可能增加幻觉而非减少
│   │   │   ├── 最佳实践：激活而非灌输
│   │   │   │   └── SFT准备模型进行新知识的推理
│   │   │   ├── **Prompt Distillation (PD)**
│   │   │   │   └── 比SFT更数据高效，10倍以下训练问题即可匹配SFT
│   │   │   ├── Microsoft研究：fact-based vs token-based scaling
│   │   │   └── 关键论文
│   │   │       ├── LIMA: Less Is More for Alignment (NeurIPS 2023)
│   │   │       ├── Fine-Tuning or Retrieval? (EMNLP 2024)
│   │   │       └── Injecting New Knowledge via SFT (arXiv 2024)
│   │   │
│   │   ├── 2.2.3 参数高效微调 (PEFT/LoRA)
│   │   │   ├── 原理：冻结主参数，训练旁路矩阵
│   │   │   ├── 模块化知识：法律LoRA、编程LoRA
│   │   │   ├── 成本优势：内存降低10倍以上
│   │   │   ├── **LoRA变体演进表**
│   │   │   │   ├── LoRA：平衡各方面
│   │   │   │   ├── **QLoRA**：4-bit量化，单GPU训练65B
│   │   │   │   ├── **DoRA** (ICML 2024 Oral, 1.5%接受率)
│   │   │   │   │   ├── 将权重分解为magnitude和direction
│   │   │   │   │   └── LLaMA 7B/13B超越LoRA +3.7/+1.0分
│   │   │   │   ├── **QDoRA**："2025年PEFT新标准"
│   │   │   │   └── rsLoRA：Rank-Stabilized LoRA
│   │   │   └── 开源实现
│   │   │       ├── Hugging Face PEFT：github.com/huggingface/peft
│   │   │       └── LlamaFactory：github.com/hiyouga/LLaMA-Factory
│   │   │
│   │   └── 2.2.4 模型编辑 (Knowledge Editing)
│   │       ├── **ROME**：causal tracing定位知识存储位置
│   │       │   └── 局限：连续编辑10次后性能快速下降
│   │       ├── **MEMIT** (ICLR 2023)：跨层批量编辑
│   │       │   └── 40次编辑后仍保持稳定
│   │       ├── **WISE** (NeurIPS 2024)：持续知识编辑
│   │       │   └── 首次系统性解决终身模型编辑问题
│   │       ├── 其他方法：MEND, SERAC, GRACE, IKE
│   │       ├── **EasyEdit框架** (ACL 2024 System Demo)
│   │       │   ├── 最全面的知识编辑框架
│   │       │   ├── 支持Llama3-8B、Mistral-7B等
│   │       │   └── github.com/zjunlp/EasyEdit
│   │       └── 当前局限：知识衰减问题仍未完全解决
│   │
│   ├── 2.3 混合架构：工业界的终极方案
│   │   ├── 稳定知识 → 增量预训练（长期记忆）
│   │   ├── 时效知识 → RAG（短期工作记忆）
│   │   ├── Long Context + Context Caching趋势
│   │   │   ├── Gemini 1.5：10M tokens上下文
│   │   │   │   ├── MoE架构选择性激活专家网络
│   │   │   │   └── Needle In A Haystack测试99%准确率
│   │   │   └── 超长上下文正在模糊RAG边界
│   │   └── CPT + SFT + RAG组合策略优于单一方法
│   │
│   └── 2.4 微调 vs RAG：技术路线决策框架
│       ├── 微调≠知识内化（EMNLP 2024验证）
│       ├── 微调擅长 vs 不擅长
│       │   ├── 擅长：风格、格式、推理模式
│       │   └── 不擅长：事实知识、时效信息
│       ├── RAG + Long Context是正确解法
│       ├── KV Cache优化
│       └── **Prompt Caching商业实践对比**
│           ├── Anthropic：显式cache_control，读取-90%，写入+25%，TTL 5分钟
│           ├── OpenAI：自动缓存，-50%
│           └── Gemini：隐式+显式，-75%，TTL 1小时
│
├── 三、RAG技术演进：从Naive到Agentic（2020-2025）[保留v3，v6扩展]
│   │
│   ├── 3.1 RAG发展阶段划分（五阶段表）
│   │   ├── 2020-2021：Naive RAG
│   │   ├── 2022：Advanced RAG
│   │   ├── 2023：Modular RAG
│   │   ├── 2024：Graph RAG
│   │   └── 2025：Agentic RAG
│   │
│   ├── 3.2 Naive RAG：朴素范式的局限
│   │   ├── 简单的"检索-生成"管道
│   │   ├── 固定分块策略
│   │   ├── 无查询理解
│   │   └── 典型问题：上下文丢失、噪音干扰
│   │
│   ├── 3.3 Advanced RAG：技术优化的积累
│   │   ├── 查询优化
│   │   │   ├── **HyDE**：假设文档嵌入
│   │   │   │   └── 生成假设答案→嵌入→检索
│   │   │   ├── Query Rewriting
│   │   │   ├── Query Decomposition
│   │   │   └── **RAG Fusion**：RRF算法融合多路检索
│   │   ├── 分块策略
│   │   │   ├── 语义分块 vs 固定分块
│   │   │   ├── 重叠窗口（10-20%）
│   │   │   └── 最佳实践：1500-3000 tokens
│   │   ├── 检索增强
│   │   │   ├── Hybrid Search（向量+BM25）
│   │   │   ├── Re-ranking（Cross-encoder）
│   │   │   └── MMR多样性优化
│   │   ├── **Contextual Retrieval** (Anthropic 2024)
│   │   │   ├── 为每个chunk生成上下文说明
│   │   │   ├── 检索失败率从5.7%降至1.9%（降低67%）
│   │   │   └── Prompt模板（见第十一节）
│   │   └── **Late Chunking** (Jina AI, arXiv:2409.04701)
│   │       ├── 先全文嵌入再在token层面分块
│   │       ├── 保留跨chunk上下文
│   │       └── 相似度测试：从70-75%提升至82-84%
│   │
│   ├── 3.4 Modular RAG：可组合的系统架构
│   │   ├── 模块化设计理念
│   │   ├── 可插拔组件
│   │   └── 灵活编排
│   │
│   ├── 3.5 Graph RAG：知识图谱增强
│   │   ├── Microsoft GraphRAG (arXiv:2404.16130)
│   │   ├── **Leiden算法层级聚类**
│   │   │   └── 构建多层级社区结构
│   │   ├── 社区摘要预计算
│   │   │   └── Community Reports用于Global Search
│   │   ├── **信息密度压缩原理**
│   │   │   └── 从原始文本到社区摘要的信息提纯
│   │   ├── **Local vs Global Search**
│   │   │   ├── Local：特定实体查询
│   │   │   └── Global：综合性问题、主题概览
│   │   ├── 性能数据
│   │   │   ├── 对Naive RAG达70-80%胜率
│   │   │   └── Token成本降至2-70%（按层级）
│   │   └── **"只读最关键的0.1%"原理**
│   │
│   └── 3.6 Agentic RAG：智能体驱动的范式转变
│       ├── 迭代检索与推理
│       │   └── Multi-hop reasoning
│       ├── 自我反思（Self-RAG）
│       │   ├── 检索判断：是否需要检索
│       │   ├── 相关性判断：检索结果是否相关
│       │   └── 支持度判断：生成是否有依据
│       ├── 工具调用
│       │   ├── 搜索引擎
│       │   ├── 计算器
│       │   └── 代码执行器
│       ├── 多智能体协作
│       │   ├── 规划Agent
│       │   ├── 检索Agent
│       │   └── 生成Agent
│       └── **System 1/System 2认知架构**
│           ├── System 1：快速、直觉、低成本
│           └── System 2：慢速、推理、高成本
│
├── 四、超长文档与大规模知识库处理 [★ v5新增，v6扩展]
│   │
│   ├── 4.1 超长文档的挑战
│   │   ├── 上下文窗口的物理限制
│   │   │   └── 传统模型：4K-32K tokens
│   │   ├── 对话录的特殊性：上下文依赖极强
│   │   │   └── 话轮之间的指代、省略、隐含
│   │   ├── 亿级Token规模场景
│   │   │   └── 企业知识库、法律文档库
│   │   └── **超长上下文技术突破**
│   │       ├── Gemini 1.5：10M tokens
│   │       ├── Claude 3：200K tokens
│   │       └── GPT-4 Turbo：128K tokens
│   │
│   ├── 4.2 语义切分策略 (Semantic Segmentation)
│   │   ├── 按话题而非Token切分
│   │   │   └── 识别话题边界
│   │   ├── 识别转折点
│   │   │   └── 标志词、语气变化
│   │   ├── 滑动窗口保留上下文
│   │   │   └── 10-20%重叠
│   │   └── LlamaIndex SemanticSplitter
│   │
│   ├── 4.3 Map-Reduce递归摘要
│   │   ├── **Map阶段**：分块摘要
│   │   │   └── 每个chunk独立摘要
│   │   ├── **Reduce阶段**：全局汇总
│   │   │   └── 合并所有摘要
│   │   ├── 生成"文件级索引"
│   │   ├── **BooookScore** (ICLR 2024)
│   │   │   ├── 书籍级摘要系统评估
│   │   │   ├── 层级合并产生更连贯摘要（89.4 vs 82.1分）
│   │   │   └── 增量更新产生更详细但连贯性较低结果
│   │   └── **LangChain实现**
│   │       └── MapReduceDocumentsChain
│   │
│   ├── 4.4 父子索引架构 (Parent-Child Indexing)
│   │   ├── **父文档**：摘要/大块
│   │   │   └── 用于粗粒度匹配
│   │   ├── **子文档**：原始切片
│   │   │   └── 用于精确检索
│   │   ├── 检索流程：匹配父→取出子
│   │   ├── **LlamaIndex HierarchicalNodeParser**
│   │   │   ├── 多级层次支持（如2048→512→128 tokens）
│   │   │   └── 自动建立父子关系
│   │   ├── **AutoMergingRetriever**
│   │   │   └── 50%以上子节点被检索时自动返回父节点
│   │   └── **LlamaIndex SummaryIndex**
│   │       └── 为每个文档生成摘要索引
│   │
│   ├── 4.5 结构化导航：Top-Down Drill-Down
│   │   ├── **宏观层**：顶层摘要
│   │   │   └── 文档/社区级概览
│   │   ├── **路径选择**：图谱关联
│   │   │   └── 根据实体/概念关系导航
│   │   ├── **微观层**：精准定位原始片段
│   │   │   └── 具体证据支撑
│   │   ├── **"只读最关键的0.1%"原理**
│   │   │   └── 层级导航实现信息聚焦
│   │   └── GraphRAG对Naive RAG达70-80%胜率
│   │
│   └── 4.6 递归问答 (Iterative Refinement)
│       ├── 分治法处理超复杂问题
│       │   └── 将大问题拆解为子问题
│       ├── 中间状态累积
│       │   └── 保留推理链条
│       └── 迭代优化答案质量
│
├── 五、RAG的四大核心挑战 [保留v3]
│   │
│   ├── 5.1 幻觉问题
│   │   ├── 内在幻觉：与源矛盾
│   │   ├── 外在幻觉：无法验证
│   │   └── 缓解策略：引用追溯、事实核查
│   │
│   ├── 5.2 知识更新与时效性
│   │   ├── 索引更新成本
│   │   ├── 增量更新策略
│   │   └── 时效性标记
│   │
│   ├── 5.3 检索质量与相关性
│   │   ├── 语义鸿沟
│   │   ├── 长尾查询
│   │   └── 多跳推理
│   │
│   └── 5.4 隐性语义处理——被忽视的核心问题 ★
│       ├── **语用含义**
│       │   ├── Grice会话含义
│       │   ├── PUB基准 (Pragmatic Understanding Benchmark)
│       │   └── 会话蕴涵
│       ├── **篇章结构**
│       │   ├── RST修辞结构理论
│       │   └── 篇章关系（因果、对比、让步）
│       ├── **论证结构**
│       │   ├── 图尔敏论证模型
│       │   ├── 省略三段论（Enthymeme）
│       │   └── 隐性担保（Warrant）
│       └── **常识推理**
│           ├── ATOMIC 2020
│           └── COMET-ATOMIC
│
├── 六、认知增强RAG：15个理论框架 [保留v3完整细节]
│   │
│   ├── 6.1 理论框架的三层架构（总表）
│   │   ├── 第一层：语义与语用基础（3个）
│   │   ├── 第二层：篇章与论证结构（6个）
│   │   └── 第三层：认知科学视角（6个）
│   │
│   ├── 6.2 第一层：语义与语用基础（3个理论）
│   │   ├── **形式语义学**
│   │   │   ├── 真值条件语义
│   │   │   ├── 组合性原则
│   │   │   └── 在RAG中的应用：精确查询理解
│   │   ├── **分布式语义学**
│   │   │   ├── 词向量的语义空间
│   │   │   ├── 上下文决定意义
│   │   │   └── 在RAG中的应用：嵌入模型基础
│   │   └── **语用学三论**
│   │       ├── Grice会话含义
│   │       │   ├── 合作原则
│   │       │   └── 四大准则（量、质、关系、方式）
│   │       ├── Austin-Searle言语行为理论
│   │       │   ├── 言内之意（Locution）
│   │       │   ├── 言外之力（Illocution）
│   │       │   └── 言后之果（Perlocution）
│   │       └── 关联理论
│   │           ├── 最佳关联假设
│   │           └── 认知效果与处理努力
│   │
│   ├── 6.3 第二层：篇章与论证结构（6个理论）
│   │   ├── **修辞结构理论（RST）**
│   │   │   ├── 核心-卫星结构
│   │   │   ├── 23种篇章关系
│   │   │   ├── RST解析树
│   │   │   ├── LLM时代的RST (EACL 2024)
│   │   │   │   └── Llama 2 70B + QLoRA实现SOTA
│   │   │   └── DISRetrieval：RST增强长文档问答
│   │   ├── **图尔敏论证模型**
│   │   │   ├── 六要素
│   │   │   │   ├── Claim（主张）
│   │   │   │   ├── Data（数据）
│   │   │   │   ├── Warrant（担保）
│   │   │   │   ├── Backing（支撑）
│   │   │   │   ├── Qualifier（限定）
│   │   │   │   └── Rebuttal（反驳）
│   │   │   ├── 隐性担保的识别
│   │   │   └── ACL 2024：Toulmin理论产生最高零样本成功率
│   │   ├── **框架语义学（FrameNet）**
│   │   │   ├── 语义框架
│   │   │   ├── 框架元素
│   │   │   ├── FS-RAG (arXiv:2406.16167)
│   │   │   │   └── 利用FrameNet框架关系进行RAG检索
│   │   │   └── ACL 2025：LoRA注入框架语义
│   │   ├── **概念隐喻理论（CMT）**
│   │   │   ├── 源域→目标域映射
│   │   │   ├── 隐喻的认知功能
│   │   │   └── arXiv:2502.01901：CMT作为LLM提示框架
│   │   ├── **叙事结构理论**
│   │   │   ├── Labov叙事结构
│   │   │   │   ├── 摘要（Abstract）
│   │   │   │   ├── 定向（Orientation）
│   │   │   │   ├── 进展（Complicating Action）
│   │   │   │   ├── 评价（Evaluation）
│   │   │   │   ├── 结果（Resolution）
│   │   │   │   └── 结尾（Coda）
│   │   │   └── 脚本理论
│   │   │       └── 事件序列的预期模式
│   │   └── **会话分析（CA）**
│   │       ├── 话轮转换
│   │       ├── 相邻对
│   │       ├── 修复机制
│   │       └── 偏好组织
│   │
│   ├── 6.4 第三层：认知科学视角（6个理论）
│   │   ├── **认知负荷理论**
│   │   │   ├── 内在负荷
│   │   │   ├── 外在负荷
│   │   │   ├── 生成负荷
│   │   │   └── 在RAG中：信息呈现优化
│   │   ├── **信息觅食理论**
│   │   │   ├── 信息气味
│   │   │   ├── 信息补丁
│   │   │   └── 在RAG中：检索路径优化
│   │   ├── **情境模型理论**
│   │   │   ├── 心智模型构建
│   │   │   ├── 情境更新
│   │   │   └── 在RAG中：上下文理解
│   │   ├── **共同基础理论**
│   │   │   ├── 共享知识
│   │   │   ├── 接地过程
│   │   │   └── 在RAG中：对话状态管理
│   │   ├── **具身认知理论**
│   │   │   ├── 认知的身体基础
│   │   │   ├── 隐喻的具身性
│   │   │   └── 在RAG中：多模态理解
│   │   └── **认识警觉理论**
│   │       ├── 信息可信度评估
│   │       ├── 来源评估
│   │       └── 在RAG中：幻觉检测
│   │
│   ├── 6.5 补充理论：布鲁姆认知分类学
│   │   ├── 六级认知层次
│   │   │   ├── 记忆（Remember）
│   │   │   ├── 理解（Understand）
│   │   │   ├── 应用（Apply）
│   │   │   ├── 分析（Analyze）
│   │   │   ├── 评价（Evaluate）
│   │   │   └── 创造（Create）
│   │   └── 在RAG中：问题分类与回答策略
│   │
│   └── 6.6 理论框架的综合应用："Aurola过程"
│       ├── **核心思想："把书读厚"**
│       │   └── 通过理论框架扩展文档的语义维度
│       ├── **入库端扩展（Document Expansion）**
│       │   ├── 显性关键词提取
│       │   ├── 隐性语义标签生成
│       │   └── 认知分析元数据
│       ├── **检索端发散（Query Expansion via Essay）**
│       │   ├── 将查询扩展为小论文
│       │   ├── 多角度覆盖
│       │   └── 增加召回率
│       ├── **映射分析（Mapping Analysis）**
│       │   ├── Query-Document语义映射
│       │   └── 跨框架关联
│       └── **学术对应物表**
│           ├── HyDE → Query Expansion
│           ├── RAPTOR → Hierarchical Summarization
│           ├── Self-RAG → Retrieval Decision
│           └── Contextual Retrieval → Document Expansion
│
├── 七、开源生态全景图 [保留v4完整细节，v6扩展]
│   │
│   ├── 7.1 核心RAG框架代码级分析
│   │   │
│   │   ├── 7.1.1 LangChain生态（120k stars）
│   │   │   ├── **架构**
│   │   │   │   ├── LangChain Core：核心抽象
│   │   │   │   ├── LangChain Community：第三方集成
│   │   │   │   ├── LangGraph：多智能体编排
│   │   │   │   └── LangServe：部署服务
│   │   │   ├── **LCEL设计理念**
│   │   │   │   ├── LangChain Expression Language
│   │   │   │   ├── 声明式管道构建
│   │   │   │   └── 流式处理支持
│   │   │   ├── **内置RAG组件深度分析**
│   │   │   │   ├── Document Loaders
│   │   │   │   ├── Text Splitters
│   │   │   │   ├── Retrievers
│   │   │   │   └── Chains
│   │   │   ├── **默认提示词模板逆向**
│   │   │   │   └── 内置模板的设计模式
│   │   │   ├── **LangSmith商业边界**
│   │   │   │   ├── 可观测性平台
│   │   │   │   └── 开源vs商业功能划分
│   │   │   └── **社区争议与局限性**
│   │   │       ├── 过度抽象
│   │   │       ├── 版本不稳定
│   │   │       └── 调试困难
│   │   │
│   │   ├── 7.1.2 LlamaIndex生态（46k stars）
│   │   │   ├── **核心Index Types设计**
│   │   │   │   ├── VectorStoreIndex
│   │   │   │   ├── SummaryIndex
│   │   │   │   ├── TreeIndex
│   │   │   │   └── KnowledgeGraphIndex
│   │   │   ├── **Query Engine vs Chat Engine**
│   │   │   │   ├── Query Engine：单轮问答
│   │   │   │   └── Chat Engine：多轮对话
│   │   │   ├── **Ingestion Pipeline实现**
│   │   │   │   ├── 文档加载
│   │   │   │   ├── 节点解析
│   │   │   │   ├── 嵌入生成
│   │   │   │   └── 索引构建
│   │   │   ├── **默认参数配置**
│   │   │   │   ├── chunk_size: 1024
│   │   │   │   ├── chunk_overlap: 20
│   │   │   │   └── similarity_top_k: 2
│   │   │   ├── **LlamaCloud商业边界**
│   │   │   │   ├── 托管服务
│   │   │   │   └── 企业功能
│   │   │   └── **与LangChain定位对比**
│   │   │       ├── LlamaIndex：数据索引专精
│   │   │       └── LangChain：通用LLM编排
│   │   │
│   │   ├── 7.1.3 DSPy（31k stars）
│   │   │   ├── **声明式提示编程范式**
│   │   │   │   └── 将提示工程转化为编程问题
│   │   │   ├── **Signature/Module/Optimizer架构**
│   │   │   │   ├── Signature：输入输出定义
│   │   │   │   ├── Module：可复用组件
│   │   │   │   └── Optimizer：自动优化提示
│   │   │   └── **与传统框架定位差异**
│   │   │       └── 从"手写提示"到"编译提示"
│   │   │
│   │   ├── 7.1.4 Microsoft GraphRAG（29k stars）
│   │   │   ├── **完整技术架构**
│   │   │   │   ├── Indexing Pipeline
│   │   │   │   ├── Query Pipeline
│   │   │   │   └── Storage Layer
│   │   │   ├── **Leiden聚类算法**
│   │   │   │   └── 层级社区检测
│   │   │   ├── **源代码提示词模板**
│   │   │   │   ├── Entity Extraction Prompt
│   │   │   │   ├── Relationship Extraction Prompt
│   │   │   │   └── Community Summarization Prompt
│   │   │   └── **部署资源需求**
│   │   │       ├── 计算资源估算
│   │   │       └── API成本估算
│   │   │
│   │   ├── 7.1.5 Haystack（24k stars）
│   │   │   ├── **Pipeline架构**
│   │   │   │   └── 组件化管道设计
│   │   │   └── **生产就绪性**
│   │   │       ├── 企业级功能
│   │   │       └── 部署选项
│   │   │
│   │   └── 7.1.6 其他重要框架
│   │       ├── **RAGFlow**（68k stars）—— 深度文档理解
│   │       │   ├── 复杂文档解析
│   │       │   └── 表格/图片处理
│   │       ├── **Dify**（119k stars）—— 低代码平台
│   │       │   ├── 可视化构建
│   │       │   └── 应用模板
│   │       ├── **Flowise**（47k stars）—— 可视化构建
│   │       │   └── 拖拽式界面
│   │       ├── **Quivr**（39k stars）—— 个人知识库
│   │       │   └── 第二大脑概念
│   │       └── **PrivateGPT**（57k stars）—— 隐私本地RAG
│   │           └── 完全本地化部署
│   │
│   ├── 7.2 LlamaIndex GUI生态 [★ 审计重点]
│   │   │
│   │   ├── 7.2.1 **Kotaemon** —— "版本答案"
│   │   │   ├── **核心优势：真·LlamaIndex内核**
│   │   │   │   └── 非LangChain包装
│   │   │   ├── **Hybrid Search / Re-ranking支持**
│   │   │   │   ├── 向量+BM25混合检索
│   │   │   │   └── Cross-encoder重排序
│   │   │   ├── **引文溯源功能**
│   │   │   │   └── 答案与源文档关联
│   │   │   └── **M1 Max部署方案**
│   │   │       └── 本地运行配置
│   │   │
│   │   ├── 7.2.2 PrivateGPT v2.0
│   │   │   └── **底层从LangChain迁移到LlamaIndex**
│   │   │       └── 架构重构原因
│   │   │
│   │   ├── 7.2.3 FlowiseAI
│   │   │   └── **可视化LlamaIndex组件编排**
│   │   │       └── 拖拽式RAG构建
│   │   │
│   │   ├── 7.2.4 Verba (Golden RAGtriever)
│   │   │   └── **Weaviate官方高级RAG UI**
│   │   │       └── 向量数据库原生集成
│   │   │
│   │   └── 7.2.5 SEC Insights
│   │       └── **LlamaIndex官方全栈参考架构**
│   │           └── 金融文档分析示例
│   │
│   ├── 7.3 其他重要工具 [v5补充]
│   │   ├── **Coze/扣子**（字节跳动）
│   │   │   └── 中国市场主流平台
│   │   ├── **Google NotebookLM**
│   │   │   └── 消费级RAG产品
│   │   └── **ChatBase / FastGPT**
│   │       └── 快速部署方案
│   │
│   ├── 7.4 向量数据库生态
│   │   ├── **开源数据库**
│   │   │   ├── Milvus：分布式向量数据库
│   │   │   ├── Qdrant：Rust实现，高性能
│   │   │   ├── Chroma：轻量级，开发友好
│   │   │   ├── Weaviate：GraphQL接口
│   │   │   ├── pgvector：PostgreSQL扩展
│   │   │   └── FAISS：Meta开源，本地优先
│   │   ├── **性能基准对比（VDBBench）**
│   │   │   ├── QPS对比
│   │   │   ├── 延迟对比
│   │   │   └── 召回率对比
│   │   ├── **部署复杂度评估**
│   │   │   ├── 单机部署
│   │   │   └── 分布式部署
│   │   └── **成本模型对比**
│   │       ├── 自托管成本
│   │       └── 云服务成本
│   │
│   ├── 7.5 嵌入模型生态
│   │   ├── **开源模型MTEB排名**
│   │   │   ├── 英文榜单Top 10
│   │   │   └── 多语言榜单
│   │   ├── **中文嵌入模型推荐**
│   │   │   ├── **BGE-M3**：多语言、多粒度
│   │   │   ├── text2vec-chinese
│   │   │   └── m3e-base
│   │   ├── **商业API对比**
│   │   │   ├── OpenAI text-embedding-3
│   │   │   ├── Voyage AI
│   │   │   └── Cohere Embed
│   │   └── **本地部署方案**
│   │       ├── Ollama嵌入模型
│   │       └── MLX嵌入模型
│   │
│   ├── 7.6 垂直领域RAG
│   │   ├── **代码RAG**
│   │   │   ├── Continue：IDE集成
│   │   │   ├── Cursor：AI-first编辑器
│   │   │   └── Cody (Sourcegraph)：代码智能
│   │   ├── **法律RAG**
│   │   │   └── 法律文档检索与问答
│   │   ├── **医疗RAG**
│   │   │   └── MedGraphRAG：医学知识图谱RAG
│   │   └── **学术RAG**
│   │       └── PaperQA2：论文问答
│   │
│   ├── 7.7 评估与监控工具
│   │   ├── **RAGAS**（RAG评估框架）
│   │   │   ├── 忠实度（Faithfulness）
│   │   │   ├── 答案相关性（Answer Relevancy）
│   │   │   ├── 上下文精确率（Context Precision）
│   │   │   └── 上下文召回率（Context Recall）
│   │   ├── **Langfuse**（开源LLM工程平台）
│   │   │   ├── 追踪
│   │   │   ├── 评估
│   │   │   └── 监控
│   │   ├── **TruLens**（可观测性）
│   │   │   └── RAG三角评估
│   │   └── **Phoenix**（Arize AI）
│   │       └── LLM可观测性
│   │
│   └── 7.8 依赖关系与商业化趋势
│       ├── **底层共享组件依赖链**
│       │   ├── tiktoken
│       │   ├── transformers
│       │   └── sentence-transformers
│       └── **开源→商业化迁移风险评估**
│           ├── 许可证变更风险
│           ├── 功能分层风险
│           └── 供应商锁定风险
│
├── 八、Apple Silicon生态与边缘计算 [保留v4完整细节]
│   │
│   ├── 8.1 M1 Max硬件特性与RAG适配
│   │   ├── **统一内存架构优势**
│   │   │   ├── 64GB统一内存
│   │   │   ├── CPU/GPU共享内存
│   │   │   └── 无需显存搬运
│   │   ├── **vs NVIDIA显卡对比**
│   │   │   ├── 内存带宽对比
│   │   │   ├── 计算能力对比
│   │   │   └── 功耗对比
│   │   └── **适合的模型规模**
│   │       ├── 7B-32B甜点区
│   │       └── 量化模型推荐
│   │
│   ├── 8.2 MLX框架深度解析 [★ 审计P0]
│   │   ├── **Apple官方ML框架定位**
│   │   │   └── 专为Apple Silicon优化
│   │   ├── **核心设计理念**
│   │   │   ├── NumPy风格API
│   │   │   ├── 惰性计算
│   │   │   ├── 统一内存
│   │   │   └── 动态图
│   │   ├── **mlx-lm**：LLM训练/推理
│   │   │   ├── 支持模型列表
│   │   │   ├── 量化支持
│   │   │   └── LoRA微调
│   │   ├── **mlx-rag**：RAG示例项目
│   │   │   └── 本地RAG实现
│   │   └── **vs PyTorch MPS性能对比**
│   │       ├── 推理速度对比
│   │       └── 内存效率对比
│   │
│   ├── 8.3 本地模型选型指南
│   │   ├── **Qwen 3系列深度分析**
│   │   │   ├── 32B Dense vs 30B MoE
│   │   │   │   ├── Dense：全参数激活
│   │   │   │   └── MoE：稀疏激活，效率更高
│   │   │   ├── Thinking Mode
│   │   │   │   └── 深度推理模式
│   │   │   └── 中文token效率
│   │   │       └── 中文编码优化
│   │   ├── **Llama 3系列**
│   │   │   ├── 8B/70B模型
│   │   │   └── 量化版本
│   │   └── **其他候选模型**
│   │       ├── Mistral系列
│   │       └── Yi系列
│   │
│   ├── 8.4 MLX + LlamaIndex组合架构 [★ 审计遗漏]
│   │   ├── **MLX作为计算层**
│   │   │   └── 硬件效率最大化
│   │   ├── **LlamaIndex作为逻辑层**
│   │   │   └── 检索智能与管道编排
│   │   └── **本地API Server方案**
│   │       ├── FastAPI封装
│   │       └── OpenAI兼容接口
│   │
│   └── 8.5 大规模知识库处理
│       ├── **亿级Token规模挑战**
│       │   └── 索引构建时间
│       ├── **Batch Processing内存管理**
│       │   └── 分批处理策略
│       └── **渐进式索引构建**
│           └── 增量更新机制
│
├── 九、MCP协议与AI工具集成 [保留v4完整细节]
│   │
│   ├── 9.1 MCP协议概述 [★ 审计P0]
│   │   ├── **Model Context Protocol定义**
│   │   │   └── AI模型与外部工具的标准接口
│   │   ├── **Anthropic发布背景**
│   │   │   └── 2024年发布
│   │   └── **与传统API的区别**
│   │       ├── 双向通信
│   │       ├── 上下文感知
│   │       └── 工具发现
│   │
│   ├── 9.2 MCP信息流架构
│   │   ├── **Host AI（大脑）**
│   │   │   └── Claude/GPT等LLM
│   │   ├── **MCP Server（图书管理员）**
│   │   │   └── 中间层，暴露工具
│   │   ├── **LlamaIndex + ChromaDB（图书馆）**
│   │   │   └── 数据存储与检索
│   │   └── **完整信息流演示**
│   │       └── 请求→检索→返回→生成
│   │
│   ├── 9.3 "抓手"机制详解
│   │   ├── **层级摘要作为抓手**
│   │   │   └── 从摘要定位到详情
│   │   ├── **抓手消除噪音**
│   │   │   └── 过滤不相关信息
│   │   ├── **父子关系保留**
│   │   │   └── 保持文档结构
│   │   └── **共创感延续**
│   │       └── 对话历史的上下文
│   │
│   ├── 9.4 检索 vs 遍历：两种模式
│   │   ├── **问答场景：Vector Search**
│   │   │   └── 语义相似度检索
│   │   ├── **目录生成场景：Metadata Query**
│   │   │   └── 结构化遍历
│   │   └── **Context Caching策略**
│   │       └── 重复上下文复用
│   │
│   └── 9.5 MCP Server实现指南
│       ├── **Python SDK**
│       │   └── mcp-python包
│       ├── **工具定义示例**
│       │   └── 函数注册与描述
│       └── **与Claude Code集成**
│           └── 本地开发环境配置
│
├── 十、隐性语义提取：从理论到Prompt [★ v5大幅扩展，v6整合Research]
│   │
│   ├── 10.1 显性关键词 vs 隐性语义标签
│   │   ├── **显性关键词**
│   │   │   └── 文本中直接出现的词汇
│   │   ├── **隐性语义标签**
│   │   │   └── 需要推理才能获得的语义
│   │   ├── **"反证法"案例**
│   │   │   └── 文中未提"反证法"但逻辑上使用了反证法
│   │   ├── **GraphRAG的LLM深度阅读机制**
│   │   │   └── LLM作为阅读器提取隐性信息
│   │   └── **LLM生成元数据"挂"在片段上**
│   │       └── 为每个chunk生成隐性标签
│   │
│   ├── 10.2 双轨提取策略 [审计二P0]
│   │   │
│   │   ├── **轨道一：显性实体提取**
│   │   │   ├── 专有名词
│   │   │   │   └── 人名、地名、组织名
│   │   │   ├── 技术工件
│   │   │   │   └── 函数名、错误码、配置项
│   │   │   └── 具体数值
│   │   │       └── 日期、金额、版本号
│   │   │
│   │   └── **轨道二：隐性语义推断**
│   │       ├── 逻辑模式识别
│   │       │   └── 反证法、归纳、演绎、类比
│   │       ├── 代码/架构模式
│   │       │   └── 单例、工厂、观察者、MVC
│   │       └── 情感与意图识别
│   │           └── 正面/负面、建议/警告
│   │
│   ├── 10.3 学术术语激活专家权重 [审计二P0核心洞察]
│   │   ├── **原理：学术术语是"咒语"**
│   │   │   └── 特定术语触发特定神经元
│   │   ├── **激活LLM预训练中的专家神经元**
│   │   │   └── 学术文献训练形成的专家知识
│   │   ├── **避免同义词混乱，收敛到标准术语**
│   │   │   └── "论证结构"可能模糊，"Toulmin Model"精准
│   │   └── **示例对比**
│   │       ├── 模糊："分析一下这段话的论证"
│   │       └── 精准："使用Toulmin Model分析这段话的Claim、Data、Warrant"
│   │
│   ├── 10.4 四大探针设计 [保留v4]
│   │   ├── **图尔敏探针：抓"逻辑断层"**
│   │   │   ├── 识别隐含的Warrant
│   │   │   ├── 发现逻辑跳跃
│   │   │   └── 提示模板示例
│   │   ├── **Frame探针：抓"技术场景"**
│   │   │   ├── 识别语义框架
│   │   │   ├── 标注框架元素
│   │   │   └── 提示模板示例
│   │   ├── **Labov探针：抓"排错高潮"**
│   │   │   ├── 识别叙事结构
│   │   │   ├── 定位关键转折点
│   │   │   └── 提示模板示例
│   │   └── **CA探针：抓"认知纠偏"**
│   │       ├── 识别修复序列
│   │       ├── 发现理解偏差
│   │       └── 提示模板示例
│   │
│   ├── 10.5 八大框架分层处理策略 [审计二P0]
│   │   │
│   │   ├── **Layer 1（基础层）：实体与意图**
│   │   │   ├── FrameNet：语义框架与框架元素
│   │   │   ├── Illocution：言语行为类型
│   │   │   └── 输出：基础实体、意图标签
│   │   │
│   │   ├── **Layer 2（深度层）：逻辑与关系**
│   │   │   ├── RST：篇章关系
│   │   │   ├── Toulmin：论证结构
│   │   │   └── 输出：逻辑链条、节点关系
│   │   │
│   │   └── **Layer 3（元数据层）：评估与分类**
│   │       ├── Bloom：认知层次分类
│   │       ├── Narrative：叙事阶段标注
│   │       └── 输出：文档打分、分段标签
│   │
│   ├── 10.6 借用标注指南的降维打击 [审计二P0]
│   │   ├── **学术数据集的Annotation Guidelines**
│   │   │   └── 专业标注员使用的详细规则
│   │   ├── **技巧：把标注规则直接抄给LLM**
│   │   │   └── 将人类标注标准转化为LLM指令
│   │   ├── **推荐数据集**
│   │   │   ├── SNLI：自然语言推理标注指南
│   │   │   ├── MultiNLI：多领域NLI标注指南
│   │   │   └── ATOMIC：常识推理标注指南
│   │   └── **标注指南中的伪影规律**
│   │       ├── 否定词→预示contradiction
│   │       └── 修饰语→预示neutral
│   │
│   ├── 10.7 隐性语义的学术研究坐标
│   │   │
│   │   ├── **自然语言推理 (NLI)**
│   │   │   ├── 任务定义：前提→假设的关系
│   │   │   ├── 标签：entailment/contradiction/neutral
│   │   │   ├── **Implied NLI**：隐式蕴涵
│   │   │   │   └── 现有模型表现接近随机（关键瓶颈）
│   │   │   └── arXiv:2505.21011：LLM利用词频偏差
│   │   │
│   │   ├── **文本蕴涵 (RTE)**
│   │   │   ├── 任务定义：T是否蕴涵H
│   │   │   └── 与NLI的关系
│   │   │
│   │   ├── **隐性论元提取 (iSRL)**
│   │   │   ├── 任务定义：识别省略的语义角色
│   │   │   └── 挑战：跨句指代
│   │   │
│   │   ├── **论辩挖掘 (Argumentation Mining)**
│   │   │   ├── 任务定义：识别论证组件和关系
│   │   │   ├── 子任务：Claim Detection, Relation Classification
│   │   │   └── 工具与数据集
│   │   │
│   │   ├── **语用推理 (Pragmatic Inference)**
│   │   │   ├── 任务定义：理解言外之意
│   │   │   ├── 会话含义
│   │   │   └── 间接言语行为
│   │   │
│   │   └── **常识推理 (Commonsense Reasoning)**
│   │       ├── 任务定义：基于世界知识的推理
│   │       ├── **COMET-ATOMIC 2020**
│   │       │   ├── 1.33M常识推理元组
│   │       │   └── BART模型超越GPT-3达12个点
│   │       └── github.com/atcbosselut/comet-commonsense
│   │
│   ├── 10.8 中文隐性语义研究 [★ v6 Research新增]
│   │   │
│   │   ├── 10.8.1 中文NLI数据集
│   │   │   ├── **OCNLI** (EMNLP 2020 Findings)
│   │   │   │   ├── 首个非翻译的原生中文NLI数据集
│   │   │   │   ├── 约56,000个句对
│   │   │   │   ├── 语言学专业母语者标注
│   │   │   │   └── aclanthology.org/2020.findings-emnlp.314
│   │   │   ├── **CMNLI**（翻译版）
│   │   │   │   └── 从MNLI翻译
│   │   │   └── **CLUE Benchmark**
│   │   │       ├── 中文版GLUE
│   │   │       ├── 214G文本，约760亿中文词语
│   │   │       ├── 9种任务
│   │   │       └── github.com/CLUEbenchmark/CLUE
│   │   │
│   │   ├── 10.8.2 中文文本蕴涵的特殊挑战
│   │   │   ├── **意合型语言**
│   │   │   │   └── 依赖语境理解隐含意义
│   │   │   ├── **无分隔符**
│   │   │   │   └── 字词边界模糊，影响分词
│   │   │   └── **字形携带语义**
│   │   │       └── 象形文字特性
│   │   │
│   │   ├── 10.8.3 中文论辩挖掘
│   │   │   ├── **ORCHID** (EMNLP 2023)
│   │   │   │   ├── 首个中文论辩对话数据集
│   │   │   │   ├── 1,218场真实辩论
│   │   │   │   ├── 14,133条完整标注话语
│   │   │   │   ├── 支持目标无关立场检测
│   │   │   │   └── arXiv:2410.13667
│   │   │   ├── **C-MTCSD** (WWW 2025)
│   │   │   │   ├── 最大中文多轮对话立场检测数据集
│   │   │   │   ├── 24,264条标注实例
│   │   │   │   └── arXiv:2504.09958
│   │   │   └── **中文论辩特点**
│   │   │       ├── 意合性强：逻辑关系常隐含
│   │   │       ├── 修辞手法丰富：比喻、反问
│   │   │       └── 需处理隐式目标引用
│   │   │
│   │   ├── 10.8.4 中文语用推理
│   │   │   ├── **SwordsmanImp** (CCL 2024)
│   │   │   │   ├── 首个中文多轮对话会话含义数据集
│   │   │   │   ├── 数据来源：《武林外传》对话
│   │   │   │   ├── 包含违反格莱斯准则标注
│   │   │   │   └── github.com/sjtu-compling/llm-pragmatics
│   │   │   └── Springer研究：LLM理解中文会话含义
│   │   │
│   │   ├── 10.8.5 中文常识知识库
│   │   │   ├── **CN-DBpedia**（复旦大学）
│   │   │   │   ├── 1600万+实体
│   │   │   │   └── 2.2亿+关系
│   │   │   ├── **HowNet/OpenHowNet**（中科院/清华）
│   │   │   │   ├── 223,767个概念
│   │   │   │   ├── 义原标注
│   │   │   │   └── github.com/thunlp/OpenHowNet
│   │   │   ├── **XLore**（清华大学）
│   │   │   │   ├── 246万概念
│   │   │   │   └── 1600万实体
│   │   │   └── **CHARM**
│   │   │       └── 首个全面评估LLM中文常识推理的基准
│   │   │
│   │   └── 10.8.6 代表性研究团队
│   │       ├── **清华THUNLP**（孙茂松、刘知远）
│   │       │   └── OpenHowNet、THULAC
│   │       ├── **哈工大SCIR**
│   │       │   ├── LTP平台（CoNLL 2009第一）
│   │       │   └── github.com/HIT-SCIR/ltp
│   │       ├── **复旦知识工场**
│   │       │   └── CN-DBpedia
│   │       └── **上海交大计算语言学**
│   │           └── LLM语用能力评估
│   │
│   └── 10.9 学术资源库
│       │
│       ├── 10.9.1 Anthropic Cookbook
│       │   ├── **Contextual Retrieval提示词**
│       │   │   └── 完整模板（见第十一节）
│       │   └── 其他RAG最佳实践
│       │
│       ├── 10.9.2 Microsoft GraphRAG prompt_tune目录
│       │   ├── **Entity Extraction提示词**
│       │   │   └── 完整模板（见第十一节）
│       │   └── Relationship Extraction提示词
│       │
│       ├── 10.9.3 Fabric Prompt库 [★ 审计P0]
│       │   ├── **extract_wisdom模式**
│       │   │   ├── 提取IDEAS、INSIGHTS、QUOTES
│       │   │   └── 每条exactly 16 words
│       │   ├── **analyze_claims模式**
│       │   │   └── 分析主张的证据支撑
│       │   └── github.com/danielmiessler/Fabric
│       │
│       └── 10.9.4 关键论文GitHub仓库
│           ├── RAPTOR: github.com/parthsarthi03/raptor
│           ├── Self-RAG: github.com/AkariAsai/self-rag
│           ├── GraphRAG: github.com/microsoft/graphrag
│           ├── EasyEdit: github.com/zjunlp/EasyEdit
│           ├── OpenHowNet: github.com/thunlp/OpenHowNet
│           ├── 哈工大LTP: github.com/HIT-SCIR/ltp
│           ├── CLUE Benchmark: github.com/CLUEbenchmark/CLUE
│           └── COMET: github.com/atcbosselut/comet-commonsense
│
├── 十一、Aurola Protocol：完整Prompt规范 [★ v5新增]
│   │
│   ├── 11.1 System Prompt完整文本（中英双语）
│   │   │
│   │   ├── **角色定义**
│   │   │   └── 语言学与认知分析专家
│   │   │
│   │   ├── **四大基础框架指令**
│   │   │   ├── RST修辞结构分析
│   │   │   │   └── 识别核心-卫星结构，标注篇章关系
│   │   │   ├── Illocutionary Force言外之力
│   │   │   │   └── 识别言语行为类型
│   │   │   ├── Frame Semantics语义框架
│   │   │   │   └── 识别语义框架和框架元素
│   │   │   └── Toulmin Model隐性担保
│   │   │       └── 识别Claim/Data/Warrant
│   │   │
│   │   └── **四大进阶框架指令**
│   │       ├── CMT概念隐喻
│   │       │   └── 识别源域→目标域映射
│   │       ├── Labov叙事结构
│   │       │   └── 标注叙事六阶段
│   │       ├── CA会话分析
│   │       │   └── 识别话轮转换和修复
│   │       └── Bloom认知分类
│   │           └── 标注认知层次
│   │
│   ├── 11.2 JSON输出Schema规范
│   │   ├── **explicit_keywords数组**
│   │   │   └── 显性关键词列表
│   │   ├── **implicit_concepts数组**
│   │   │   └── 隐性概念列表
│   │   ├── **cognitive_analysis对象**
│   │   │   ├── type字段：框架类型
│   │   │   ├── content字段：分析内容
│   │   │   └── confidence字段：置信度
│   │   └── **aurola_essay字段**
│   │       └── 扩展小论文
│   │
│   ├── 11.3 GraphRAG Entity Extraction定制Prompt
│   │   ├── **默认Prompt的局限**
│   │   │   └── 只提取显性实体
│   │   ├── **定制指令示例**
│   │   │   └── 增加隐性语义提取
│   │   └── **输出格式：Nodes + Relationships**
│   │       ├── Entity: (name, type, description)
│   │       └── Relationship: (source, target, description, strength)
│   │
│   ├── 11.4 分场景Prompt变体
│   │   ├── **技术对话场景**
│   │   │   └── 强调代码模式、排错过程
│   │   ├── **学术论文场景**
│   │   │   └── 强调论证结构、引用关系
│   │   └── **商业文档场景**
│   │       └── 强调决策点、行动项
│   │
│   ├── 11.5 针对不同模型的适配说明
│   │   ├── **Qwen适配**
│   │   │   ├── 中文优化
│   │   │   └── 长上下文处理
│   │   ├── **Claude适配**
│   │   │   ├── XML标签结构
│   │   │   └── Constitutional AI兼容
│   │   └── **GPT适配**
│   │       ├── Function Calling集成
│   │       └── JSON模式
│   │
│   └── 11.6 完整Prompt模板示例
│       │
│       ├── **Contextual Retrieval Prompt** (Anthropic)
│       │   └── <document>{{WHOLE_DOCUMENT}}</document>
│       │       <chunk>{{CHUNK_CONTENT}}</chunk>
│       │       请给出简洁上下文以定位此chunk...
│       │
│       ├── **Fabric extract_wisdom核心结构**
│       │   └── IDENTITY → STEPS → OUTPUT SECTIONS
│       │
│       └── **GraphRAG Entity Extraction核心结构**
│           └── Goal → Steps → Output Format
│
├── 十二、本综述的学术定位与贡献 [保留v3]
│   │
│   ├── 12.1 与现有综述的关系
│   │   ├── 对比Gao et al. (2023)
│   │   ├── 对比Zhao et al. (2024)
│   │   └── 本综述的独特视角
│   │
│   ├── 12.2 六个核心研究问题
│   │   ├── RQ1：RAG如何处理隐性语义？
│   │   ├── RQ2：语用推理如何整合到RAG？
│   │   ├── RQ3：篇章结构如何增强检索？
│   │   ├── RQ4：论证挖掘如何改善生成？
│   │   ├── RQ5：常识推理的角色是什么？
│   │   └── RQ6：认知理论如何指导RAG设计？
│   │
│   └── 12.3 预期学术贡献（五大贡献）
│       ├── C1：隐性语义处理的系统框架
│       ├── C2：15个理论框架的RAG映射
│       ├── C3：Aurola Protocol方法论
│       ├── C4：开源生态的技术分析
│       └── C5：边缘计算RAG的实践指南
│
├── 附录A：全书结构概览（六卷100章）
│
└── 参考文献 [扩展至90+篇]
    │
    ├── RAG奠基与演进
    │   ├── Lewis et al. (2020) - RAG原始论文
    │   ├── Guu et al. (2020) - REALM
    │   ├── Karpukhin et al. (2020) - DPR
    │   └── Izacard & Grave (2021) - FiD
    │
    ├── RAG前沿范式
    │   ├── Asai et al. (2023) - Self-RAG
    │   ├── Sarthi et al. (2024) - RAPTOR
    │   ├── Microsoft (2024) - GraphRAG
    │   ├── Anthropic (2024) - Contextual Retrieval
    │   └── Jina AI (2024) - Late Chunking
    │
    ├── 隐性语义与语用推理
    │   ├── Grice (1975) - 会话含义
    │   ├── Searle (1969) - 言语行为
    │   ├── Sperber & Wilson (1986) - 关联理论
    │   └── Implied NLI相关论文
    │
    ├── 篇章分析与论证挖掘
    │   ├── Mann & Thompson (1988) - RST
    │   ├── Toulmin (1958) - 论证模型
    │   ├── Fillmore (1985) - FrameNet
    │   ├── EACL 2024 - RST with LLMs
    │   └── ACL 2024 - Toulmin零样本
    │
    ├── 常识推理与知识图谱
    │   ├── Hwang et al. (2021) - ATOMIC 2020
    │   ├── Bosselut et al. (2019) - COMET
    │   └── 常识推理综述
    │
    ├── 认知科学与RAG
    │   ├── Sweller (1988) - 认知负荷
    │   ├── Pirolli & Card (1999) - 信息觅食
    │   └── Bloom (1956) - 认知分类
    │
    ├── 神经-符号整合
    │   └── 相关综述与方法
    │
    ├── 中文NLP资源 [v6扩展]
    │   ├── OCNLI (EMNLP 2020)
    │   ├── ORCHID (EMNLP 2023)
    │   ├── SwordsmanImp (CCL 2024)
    │   ├── C-MTCSD (WWW 2025)
    │   ├── CN-DBpedia
    │   ├── HowNet/OpenHowNet
    │   └── CLUE Benchmark
    │
    ├── 评估与幻觉
    │   ├── RAGAS
    │   └── 幻觉检测相关论文
    │
    ├── 开源框架
    │   ├── LangChain
    │   ├── LlamaIndex
    │   ├── DSPy
    │   └── 其他框架
    │
    ├── Apple Silicon生态
    │   ├── MLX官方文档
    │   └── 相关技术报告
    │
    ├── MCP协议与工具集成
    │   └── Anthropic MCP文档
    │
    ├── 知识嵌入技术 [v6新增]
    │   ├── Don't Stop Pretraining (ACL 2020)
    │   ├── LIMA (NeurIPS 2023)
    │   ├── DoRA (ICML 2024)
    │   ├── Self-Synthesized Rehearsal (ACL 2024)
    │   ├── ROME
    │   ├── MEMIT (ICLR 2023)
    │   ├── WISE (NeurIPS 2024)
    │   ├── EasyEdit (ACL 2024)
    │   └── BooookScore (ICLR 2024)
    │
    └── 隐性语义提取学术文献 [v6扩展]
        ├── Implied NLI研究
        ├── FS-RAG (arXiv:2406.16167)
        ├── DISRetrieval
        └── CMT as LLM Prompt Framework
```

---

## v3 → v4 → v5 → v6 变更追溯

### v3 → v4 变更

| 板块 | 动作 | 预估字数 |
|------|------|----------|
| 五、开源生态全景图 | **新增** | ~8,000 |
| 六、Apple Silicon生态 | **新增** | ~4,000 |
| 七、MCP协议 | **新增** | ~3,000 |
| 八、隐性语义Prompt | **新增** | ~3,000 |
| 参考文献 | 扩展至60+ | ~1,000 |

### v4 → v5 变更

| 板块 | 动作 | 预估字数 |
|------|------|----------|
| 二、知识嵌入技术路线 | **新增** | ~4,000 |
| 四、超长文档处理 | **新增** | ~4,000 |
| 十、隐性语义（含中文研究） | **大幅扩展** | +5,000 |
| 十一、Aurola Protocol | **新增** | ~3,000 |
| 参考文献 | 扩展至70+ | +500 |

### v5 → v6 变更

| 板块 | 动作 | 预估字数 |
|------|------|----------|
| 二、知识嵌入技术路线 | **深化扩展** | +2,000 |
| │ | - 增加SSR、DoRA、QDoRA细节 | |
| │ | - 增加EasyEdit、WISE细节 | |
| │ | - 增加Prompt Caching商业对比 | |
| 三、RAG演进 | **扩展** | +1,000 |
| │ | - 增加Late Chunking | |
| │ | - 增加RAG Fusion | |
| │ | - 增加GraphRAG性能数据 | |
| 四、超长文档处理 | **扩展** | +500 |
| │ | - 增加BooookScore细节 | |
| │ | - 增加AutoMergingRetriever | |
| 六、15理论框架 | **确保完整** | 无变化 |
| │ | - 确认所有细节保留 | |
| 七、开源生态 | **确保完整** | 无变化 |
| │ | - 确认所有细节保留 | |
| 十、隐性语义 | **深化扩展** | +1,500 |
| │ | - 增加OCNLI、ORCHID、SwordsmanImp细节 | |
| │ | - 增加Implied NLI研究 | |
| │ | - 增加中文研究团队 | |
| 参考文献 | 扩展至90+ | +1,000 |

---

## 两份审计P0遗漏的覆盖情况

### 审计一（Aurola过程）P0遗漏

| 遗漏项 | v6覆盖位置 | 状态 |
|--------|-----------|------|
| MLX框架 | 8.2（完整细节） | ✅ |
| MCP协议 | 9.1-9.5（完整细节） | ✅ |
| Kotaemon | 7.2.1（完整细节） | ✅ |
| Fabric Prompt库 | 10.9.3（完整细节） | ✅ |

### 审计二（隐性语义提取）P0遗漏

| 遗漏项 | v6覆盖位置 | 状态 |
|--------|-----------|------|
| 双轨提取策略 | 10.2 | ✅ |
| 学术Prompt完整模板 | 11.1-11.6 | ✅ |
| 8框架分层处理策略 | 10.5 | ✅ |
| 学术术语激活专家权重原理 | 10.3 | ✅ |
| 借用标注指南技巧 | 10.6 | ✅ |

---

## 规模预估

| 指标 | v3 | v4 | v5 | v6预估 | v5→v6增幅 |
|------|-----|-----|-----|--------|----------|
| 总字数 | 18,000 | 36,000 | 48,000 | **60,000** | +25% |
| 章节数 | 6 | 10 | 12 | **12** | 0% |
| 参考文献 | 43 | 60 | 70 | **90+** | +29% |
| 树状节点 | ~80 | ~200 | ~280 | **~350** | +25% |

---

## 元信息

```yaml
schema_version: 6.0
design_date: 2026-01-09
version_lineage: v3 → v4 → v5 → v6
design_basis:
  - 法证式审计报告（一）：Aurola过程
  - 法证式审计报告（二）：隐性语义提取
  - v5 Research报告（2024-2025最新学术进展）
  - v4/v5所有细节的100%继承
target_document: 01_研究背景与问题定义_v6.md
estimated_word_count: 60000
estimated_sections: 12
estimated_tree_nodes: 350
estimated_references: 90+
detail_preservation:
  v3_details: 100%
  v4_details: 100%
  v5_details: 100%
p0_coverage:
  audit_1: 4/4 (100%)
  audit_2: 5/5 (100%)
key_features:
  - 完整继承所有前版细节
  - 知识嵌入技术深化（DoRA/QDoRA/EasyEdit/WISE）
  - 超长文档处理深化（BooookScore/Late Chunking）
  - 中文隐性语义研究全面覆盖（OCNLI/ORCHID/SwordsmanImp）
  - 学术资源GitHub仓库汇总
  - 90+篇参考文献
status: schema_approved_pending_execution
```
