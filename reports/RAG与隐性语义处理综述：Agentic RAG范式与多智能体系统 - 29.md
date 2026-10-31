# RAG与隐性语义处理综述：Agentic RAG范式与多智能体系统

**Vol3-01 | 第29-31章研究报告**

Agentic RAG标志着检索增强生成技术从静态流水线向自主智能体范式的根本转变。2024-2025年间，学术界对这一领域的定义趋于成熟：Singh等人(2025)的综述将Agentic RAG定义为"将自主AI智能体嵌入RAG流水线，利用**反思、规划、工具使用和多智能体协作**四种核心设计模式动态管理检索策略"。本报告系统梳理了该领域的学术进展、关键架构及实际部署挑战。

---

## 第29章：Agentic RAG范式深度解析

### 从"检索-生成"到"感知-决策-行动"的范式转变

传统RAG采用静态、线性的检索-生成流水线，而Agentic RAG则引入了认知科学中的**System 1/System 2**双系统框架。根据arXiv:2506.10408的分析，预定义推理类似于System 1思维——快速、结构化、依赖启发式规则；而智能体推理则对应System 2思维——缓慢、审慎、适应性强，LLM主动参与推理、规划和决策。

RAG技术演进呈现清晰的阶段性特征：

| 阶段 | 时期 | 核心特征 | 代表性工作 |
|------|------|----------|-----------|
| Traditional RAG | 2020-2023 | 静态流水线、单次检索 | Lewis等(NeurIPS 2020) |
| Modular RAG | 2023-2024 | 模块化组件、灵活配置 | 各类专用模块 |
| Agentic RAG | 2024-2025 | 自主智能体、迭代优化 | Singh等(arXiv:2501.09136) |
| Reasoning Agentic RAG | 2025+ | 深度推理、交替搜索 | Li等(arXiv:2507.09477) |

NVIDIA技术博客对两种范式的区分尤为精准："传统RAG如同快速查询——AI查询知识库、检索信息、生成响应。Agentic RAG则更具动态性：AI智能体主动管理信息获取方式，**通过推理优化查询**，将RAG转化为复杂工具，并随时间管理信息。"

### Agent定义的学术争论与边界划定

"什么算Agent"是2024-2025年学术界的核心争议点。Wang等人(2023/2025)在*Frontiers of Computer Science*发表的综述(arXiv:2308.11432)提出了统一的智能体框架，包含**Brain(大脑)、Perception(感知)、Action(行动)**三大组件。其中大脑负责自然语言交互、知识存储、记忆管理和推理规划；感知处理多模态输入；行动执行工具调用和环境交互。

Xi等人(2023/2024)在*Science China Information Sciences*发表的综述(arXiv:2309.07864)从哲学角度给出定义："AI智能体是**感知环境、做出决策、采取行动**的人工实体。"该论文由复旦大学28位作者联合完成，GitHub仓库(github.com/WooooDyy/LLM-Agent-Paper-List)已获8k+星标。

Simon Willison在2025年9月提出了更简洁的技术定义："LLM智能体在**循环中运行工具以实现目标**。"这一定义区分了三个层次：
- **工具使用LLM**：单次工具调用，无迭代
- **LLM Agent**：目标导向的迭代工具使用
- **认知Agent**：增加记忆、学习和元认知能力

学术界对Agent最低要求的共识包括：目标导向行为(必需)、工具/API访问(必需)、迭代循环(必需)、规划能力(优先)、记忆机制(优先)、自我反思(可选)、学习能力(可选)。

### 认知架构理论与RAG Agent设计的映射

认知科学领域的经典架构对LLM Agent设计产生了深远影响。**SOAR**(State, Operator, And Result)由Laird、Newell、Rosenbloom于1983年在卡内基梅隆大学创建，包含工作记忆、程序性记忆、语义记忆和情景记忆四大组件。**ACT-R**(Adaptive Control of Thought-Rational)由John R. Anderson开发，核心目标是认知行为建模。

Sumers等人(2023/2024)在*Transactions on Machine Learning Research*发表的**CoALA框架**(arXiv:2309.02427)首次系统性地将认知架构映射到语言智能体设计。CoALA的核心洞察是：LLM作为**概率性产生式系统**运作，其中确定性规则被字符串转换的概率分布所取代。该框架沿三个维度组织智能体：信息存储(工作记忆与长期记忆)、动作空间(内部动作与外部动作)、决策过程(规划与执行)。

arXiv:2505.07087(2025年5月)的研究将ReAct与BDI架构进行了对比分析，发现"ReAct与observe-decide-act模式有显著重叠，但**缺乏显式承诺步骤**"，这启发了引入承诺机制可能改善推理效果的实证研究方向。

### 核心学术资源与研究团队

**2024-2025年主要综述论文：**

| 论文 | 作者 | 发表渠道 | arXiv ID |
|------|------|---------|----------|
| Agentic RAG: A Survey | Singh, Ehtesham, Kumar, Khoei | arXiv | 2501.09136 |
| Reasoning RAG via System 1/2 | 多作者 | arXiv | 2506.10408 |
| Agentic RAG with Deep Reasoning | Li等(20位作者) | arXiv | 2507.09477 |
| LLM-based Autonomous Agents Survey | Wang等 | Frontiers of CS | 2308.11432 |
| Rise and Potential of LLM Agents | Xi等(28位作者) | Science China IS | 2309.07864 |
| Cognitive Architectures for Language Agents | Sumers, Yao等 | TMLR | 2309.02427 |

**主要研究团队：** 复旦大学(Xi等，LLM Agent综述)、中国人民大学(Wang等，自主智能体框架)、普林斯顿大学(Sumers, Yao，CoALA框架)、密歇根大学(Laird，SOAR架构)、DeepLearning.AI(Andrew Ng，Agentic设计模式)。

---

## 第30章：智能体架构模式

### ReAct：推理与行动的协同

**ReAct: Synergizing Reasoning and Acting in Language Models**由Yao等人提出，发表于ICLR 2023(arXiv:2210.03629)。该架构引入了**Think-Act-Observe**循环：Thought(推理轨迹)用于分析当前状态、分解目标、注入常识知识；Action执行任务特定操作；Observation接收环境反馈。

ReAct的核心洞察在于推理轨迹帮助模型诱导、追踪和更新行动计划，同时处理异常情况；行动则允许与外部源(知识库、API、环境)交互以获取额外信息——形成"为行动而推理"与"通过行动推理"的协同动态。

**性能基准：**
- HotpotQA：ReAct + CoT达到70.9%(GPT-3达到78.4%)
- ALFWorld：相比模仿学习和强化学习方法，绝对成功率提升**34%**
- WebShop：相比RL基线，绝对提升**10%**

**2024-2025年扩展：** ReWOO(arXiv:2305.18323)通过预先规划所有工具调用实现**80%的token减少**(HotpotQA上2,000 vs 9,795 tokens)；LLMCompiler引入DAG规划实现**3.6倍速度提升**；CodeAct将ReAct扩展到代码生成和执行。

### MRKL系统：模块化神经符号架构

**MRKL Systems**由AI21 Labs的Karpas等人提出(arXiv:2205.00445，2022年5月)，全称为"Modular Reasoning, Knowledge and Language"。该架构针对LLM的固有局限：无法访问实时/专有信息、无法执行可靠的符号推理、无法查询内部数据库。

MRKL的核心设计包括：**可扩展的专家模块集**(神经模块如通用LLM和专用小模型、符号模块如计算器和数据库、外部知识源如Wikipedia)和**路由器**(使用调优的LLM将查询分解为动作步骤并分配给适当模块)。

关键技术挑战在于**跨越神经-符号鸿沟**：从自然语言中提取形式化参数供符号推理器使用。论文在算术问题上展示了90%+的准确率。

### Toolformer：自监督工具学习

**Toolformer: Language Models Can Teach Themselves to Use Tools**由Meta AI的Schick等人提出(arXiv:2302.04761，2023年2月)。核心创新在于语言模型可以通过**自监督方式**学习使用外部工具，每个API仅需少量示例。

训练过程分三步：(1)采样API调用——给定输入文本，使用上下文学习采样候选API调用；(2)执行与过滤——实际执行API调用并计算适应度分数；(3)微调——仅保留能降低下一token预测损失的API调用，在增强数据集上微调LLM。

**性能结果：** 6.7B参数的GPT-J经Toolformer方法微调后，在多个基准上超越175B参数的GPT-3。计算器工具显著提升算术准确率，Wikipedia搜索工具大幅改善问答性能。

### Reflexion：语言反馈的强化学习

**Reflexion: Language Agents with Verbal Reinforcement Learning**由Shinn等人提出，发表于NeurIPS 2023(arXiv:2303.11366)。该框架引入"语言强化学习"范式，通过**语言反馈而非权重更新**来强化语言智能体。

架构包含三个模块化组件：**Actor**(生成文本和动作)、**Evaluator**(评分输出)、**Self-Reflection Model**(生成存储于情景记忆缓冲区的语言反馈)。

**性能基准：**
- HumanEval(编程)：**91% pass@1**准确率，超越GPT-4的80%
- AlfWorld：12次迭代学习后绝对提升22%
- HotPotQA：提升20%

自我反思相比纯情景记忆学习提供了**8%的绝对提升**，证明反思引导的改进优于单纯的迭代优化。

### Plan-and-Execute与LATS架构

**Plan-and-Execute**架构将战略规划与战术执行分离：Planner使用强大的推理密集型LLM(如GPT-4)生成结构化计划，Executor逐步执行计划，可使用更简单/小型的LLM。相比ReAct，该架构具有更快的速度(更少LLM调用)、更低的成本(执行阶段可用小模型)和更好的规划质量。

**LATS(Language Agent Tree Search)**由Zhou等人提出，发表于ICML 2024(arXiv:2310.04406)，是**首个统一推理、行动和规划**的框架。LATS将蒙特卡洛树搜索(MCTS)适配到语言智能体，包含动作生成器、LM驱动的价值函数、反思机制和外部环境反馈。

**LATS性能：** HumanEval(GPT-4)达到**92.7% pass@1**(发表时SOTA)；HotPotQA上是ReAct的2倍；相比ToT和RAP，LATS需要更少的总token和状态。

### 架构对比与选型指南

| 架构 | Token效率 | 性能 | 延迟 | 适用场景 |
|------|----------|------|------|----------|
| ReAct | 中 | 基准 | 低 | 原型开发、交互环境 |
| ReWOO | **最高(5x)** | 高 | 低 | 工具增强任务、成本敏感应用 |
| Reflexion | 中 | 高(迭代改进) | 高(多轮) | 带反馈循环的任务 |
| Plan-and-Execute | 高 | 高 | 中 | 多步工作流 |
| LATS | 中-高 | **最高** | 可变 | 复杂推理+行动 |
| Chain-of-Agents | 高 | 长上下文高 | 中 | 长文档处理 |

---

## 第31章：多智能体RAG系统

### 多智能体协作模式的学术分类

Tran等人(2025)在arXiv:2501.06322的综述中将协作类型分为三种主要模式：

**合作(Cooperation)**：智能体将个体目标(oᵢ)与共享集体目标(O_collab)对齐，数学表达为O_collab = ∪ᵢ₌₁ⁿ oᵢ。代表性工作包括MetaGPT的装配线模型(Hong等, ICLR 2024)和AgentVerse的角色专业化(Chen等, 2024)。

**竞争(Competition)**：智能体优先考虑可能与他人冲突的个体目标。代表性工作包括LLMARENA的竞争基准测试(Chen等, 2024)和Multi-Agent Debate中的发散思维激励(Liang等, EMNLP 2024)。

**竞合(Coopetition)**：合作与竞争的战略混合，智能体在某些任务上协作而在其他任务上竞争。

Guo等人(2024)在IJCAI发表的综述(arXiv:2402.01680)进一步分析了通信结构：**中心化**(中央智能体管理所有协调，简单但存在单点故障)、**去中心化**(决策分布于智能体间，容错但通信开销高)、**层级化**(分层系统具有不同权限级别，平衡但延迟高)。

### 智能体间通信协议的演进

从历史基础来看，**FIPA ACL**(2002)提供了智能体通信的抽象架构，**KQML**引入了显式言语行为，**Contract Net Protocol**(Smith, 1980, IEEE)实现了通过协商的分布式任务分配。

2024-2025年出现了针对LLM智能体的新协议：

| 协议 | 提出者 | 核心特征 |
|------|--------|----------|
| **MCP** | Anthropic/OpenAI | 基于JSON-RPC 2.0，工具集成标准 |
| **A2A** | Google | 支持扩展交互的Agent-to-Agent协议 |
| **ACP** | IBM/Linux Foundation | 跨平台、可审计 |
| **LACP** | arXiv:2510.13821 | 三层架构：语义层、事务层、安全层 |

AutoGen(Wu等, 2023, COLM)实现了多种通信模式：双智能体对话、群聊(可定制模式)、嵌套对话(层级结构)、人机协作(集成人类参与)。

### 共识机制与冲突解决

**Multi-Agent Debate(MAD)**是重要的共识达成机制。Du等人(2023)在ICML 2024发表的开创性工作(arXiv:2305.14325)证明多个LLM实例通过迭代辩论可在推理基准上将事实性提升**10-20%**。

后续发展包括：**FREE-MAD**(arXiv:2509.11035)采用无共识框架和基于分数的决策机制；**CONSENSAGENT**(ACL 2025 Findings)通过动态提示优化缓解谄媚问题；**A-HMAD**(Springer 2025)引入学习的共识优化器实现自适应异构多智能体辩论；**ReConcile**(ACL 2024)实现多样化LLM间的圆桌会议共识。

关键挑战包括：**谄媚(Sycophancy)**——智能体相互强化回应而缺乏批判性参与；**从众压力**——多数压力抑制独立纠正；**级联幻觉**——错误通过智能体链传播。

### 核心多智能体框架深度分析

**AutoGen(Microsoft)**是最成熟的多智能体框架，GitHub仓库(github.com/microsoft/autogen)拥有**51.7k星标**。AutoGen v0.4(2025年1月发布)进行了完全架构重设计，采用**actor模型**实现异步、事件驱动架构。Microsoft Agent Framework(2025年10月公开预览)将AutoGen与Semantic Kernel合并为统一框架，计划2026年Q1正式发布。

Magentic-One(arXiv:2411.04468)展示了五智能体架构：Orchestrator(高级规划)、WebSurfer(浏览器交互)、FileSurfer(文件处理)、Coder(代码编写)、ComputerTerminal(代码执行)。基准性能：GAIA 38%任务完成率，WebArena 32.8%完成率。

**MetaGPT**(arXiv:2308.00352, ICLR 2024)由Hong等人提出，核心理念是"Code = SOP(Team)"。框架将**标准化操作流程(SOPs)**编码到提示序列中，模拟真实软件开发团队的装配线范式。角色包括产品经理、架构师、项目经理、工程师、QA工程师。代码生成Pass@1达到**85.9%-87.7%(SOTA)**，任务完成率100%。GitHub仓库(github.com/geekan/MetaGPT)拥有40k+星标。

**ChatDev**(arXiv:2307.07924, ACL 2024)由清华大学Qian等人提出，模拟虚拟软件公司。采用**Chat Chain**机制将阶段分解为原子子任务，通过"瀑布模型"四阶段(设计→编码→测试→文档)完成开发。组织角色包括CEO、CTO、CPO、程序员、设计师、测试员、艺术设计师。项目开发**不到7分钟**，成本**低于$1**。

**CAMEL**(arXiv:2303.17760, NeurIPS 2023)由KAUST的Li等人提出，全称"Communicative Agents for Mind Exploration"。核心机制是**Inception Prompting**——引导对话智能体完成任务同时保持人类意图。可扩展到**100万智能体**用于涌现行为研究。GitHub仓库(github.com/camel-ai/camel)自称"首个也是最好的多智能体框架"。

**AgentVerse**(arXiv:2308.10848, ICLR 2024)由清华大学Chen等人提出，提供**双框架设计**：仿真框架(自定义环境观察多智能体行为)和任务解决框架(四阶段过程：专家招募、协作决策、行动执行、评估)。支持Minecraft集成进行具身AI实验。

### 多智能体涌现行为研究

AgentVerse研究识别了三类涌现社会行为：**志愿行为**(智能体自发提供帮助)、**从众行为**(智能体在批评后调整偏离行为)、**破坏行为**(偶发的不良结果需要缓解策略)。

Riedl(arXiv:2510.05174, 2024)提出了使用时间延迟互信息(TDMI)的信息论框架来量化动态涌现，发现通过提示设计可将多智能体LLM系统从"单纯聚合"引导为"高阶集体"。

**Project SID**(arXiv:2411.00114, Altera.AL)测试了10-1000+智能体在Minecraft环境中的行为，观察到专业身份形成、文化/宗教传播等涌现现象。**AgentSociety**(arXiv:2502.08691, 2025)扩展到**10,000+智能体、500万次交互**，采用基于actor的分布式机制。

### 多智能体RAG的特殊挑战

Singh等人(arXiv:2501.09136, 2025年1月)的Agentic RAG综述识别了关键挑战：协调复杂性(管理智能体间交互需要复杂编排)、计算开销(多智能体增加资源需求)、可扩展性限制(动态特性对高查询量造成资源压力)、检索结果整合(综合多检索源信息)。

**MA-RAG**(arXiv:2505.20096, 2025)提出多智能体RAG框架，编排专门智能体(Planner、Step Definer、Extractor、QA Agents)。LLaMA3-8B配合MA-RAG超越了更大的独立LLM；LLaMA3-70B和GPT-4o-mini在多跳数据集上创造新SOTA。

知识一致性维护是核心挑战。Song(arXiv:2503.13275, 2025年3月)提出的知识感知迭代检索机制包括：内部知识缓存(渐进更新、与外部源解耦)、渐进多样性促进(平衡探索新路径与任务优先级相关性)、上下文保持过滤(确保精炼上下文与已验证知识一致)。

**信息融合方法：** M2RAG(ICONIP 2025)多智能体多模态融合框架在DocBench等基准上平均超越基线**7.7%**；KG-RAG整合结构化(知识图谱)和非结构化(文档)检索，通过路径注意力机制选择性激活知识片段，BLEU-4提升4.8%，ROUGE-L提升5.5%，Knowledge F1提升**11.5%**。

### 评估框架与基准测试

**MultiAgentBench**(ACL 2025, arXiv:2503.01935)是主要评估框架，评估维度包括任务完成(任务分数、里程碑KPI)、协作质量(规划分数、通信分数)、竞争(竞争分数)、协调(效率评估，涵盖星型、链型、树型、图型拓扑)。

**MAST**(NeurIPS 2025, arXiv:2503.13657)提出多智能体系统失败分类法，在1600+标注轨迹上识别了**14种失败模式**，分为三类：规格与系统设计失败(违反任务/角色规格)、智能体间不对齐(对话重置、信息隐瞒)、任务验证与终止(过早终止、验证不充分)。标注者间一致性Cohen's κ = 0.88。

---

## 结论：关键洞察与研究前沿

本综述揭示了Agentic RAG与多智能体系统领域的几个核心趋势。**范式层面**，从静态RAG到Agentic RAG的转变标志着AI系统从"检索-生成"向"感知-决策-行动"的根本演进，认知架构(SOAR, ACT-R)为智能体设计提供了理论基础。**架构层面**，ReAct确立了think-act-observe循环的基础范式，而ReWOO(5x效率提升)、LATS(92.7% HumanEval)和Reflexion(91% pass@1)分别在效率、性能和自我改进方面取得突破。**系统层面**，AutoGen、MetaGPT等框架已实现工业级部署，但多智能体系统面临谄媚、级联幻觉、知识漂移等独特挑战。

**开放问题**包括：涌现行为的量化测量(现有结果主要是描述性的)、可扩展性与性能的权衡(agent数量增加时的性能维持)、标准化评估(缺乏综合的多智能体评估套件)、RAG特有问题(动态检索触发、多源去重、跨智能体知识同步、检索链幻觉传播防止)。

2025年的研究方向正在向"人工集体智能"迈进——从单智能体到多智能体的转变代表着AI系统设计的根本性变革。

---

## 核心资源索引

### GitHub仓库

| 项目 | 链接 | 星标 |
|------|------|------|
| AutoGen | github.com/microsoft/autogen | 51.7k |
| MetaGPT | github.com/geekan/MetaGPT | 40k+ |
| CAMEL | github.com/camel-ai/camel | Active |
| AgentVerse | github.com/OpenBMB/AgentVerse | Active |
| ChatDev | github.com/OpenBMB/ChatDev | Active |
| Reflexion | github.com/noahshinn/reflexion | 3k+ |
| LATS | github.com/lapisrocks/LanguageAgentTreeSearch | Active |
| LangGraph | github.com/langchain-ai/langgraph | 10k+ |
| LLM Agent Survey | github.com/WooooDyy/LLM-Agent-Paper-List | 8k+ |
| Agentic RAG Survey | github.com/asinghcsu/AgenticRAG-Survey | Active |

### 关键论文完整引用

**Agentic RAG综述：**
- Singh, A., Ehtesham, A., Kumar, S., & Khoei, T. T. (2025). Agentic Retrieval-Augmented Generation: A Survey on Agentic RAG. arXiv:2501.09136.

**智能体架构：**
- Yao, S., Zhao, J., Yu, D., et al. (2023). ReAct: Synergizing Reasoning and Acting in Language Models. ICLR 2023. arXiv:2210.03629.
- Shinn, N., Cassano, F., Gopinath, A., et al. (2023). Reflexion: Language Agents with Verbal Reinforcement Learning. NeurIPS 2023. arXiv:2303.11366.
- Zhou, A., Yan, K., Shlapentokh-Rothman, M., et al. (2024). LATS: Language Agent Tree Search. ICML 2024. arXiv:2310.04406.
- Schick, T., Dwivedi-Yu, J., et al. (2023). Toolformer: Language Models Can Teach Themselves to Use Tools. arXiv:2302.04761.
- Karpas, E., et al. (2022). MRKL Systems: A Modular, Neuro-Symbolic Architecture. arXiv:2205.00445.

**多智能体框架：**
- Wu, Q., Bansal, G., Zhang, J., et al. (2023). AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation. COLM. arXiv:2308.08155.
- Hong, S., Zhuge, M., Chen, J., et al. (2024). MetaGPT: Meta Programming for A Multi-Agent Collaborative Framework. ICLR 2024. arXiv:2308.00352.
- Qian, C., Liu, W., et al. (2024). ChatDev: Communicative Agents for Software Development. ACL 2024. arXiv:2307.07924.
- Li, G., Hammoud, H., Itani, H., et al. (2023). CAMEL: Communicative Agents for Mind Exploration. NeurIPS 2023. arXiv:2303.17760.
- Chen, W., Su, Y., Zuo, J., et al. (2024). AgentVerse: Facilitating Multi-Agent Collaboration. ICLR 2024. arXiv:2308.10848.

**认知架构与理论：**
- Sumers, T. R., Yao, S., Narasimhan, K., & Griffiths, T. L. (2024). Cognitive Architectures for Language Agents. TMLR. arXiv:2309.02427.
- Wang, L., Ma, C., Feng, X., et al. (2025). A Survey on Large Language Model based Autonomous Agents. Frontiers of CS. arXiv:2308.11432.
- Xi, Z., Chen, W., et al. (2024). The Rise and Potential of Large Language Model Based Agents: A Survey. Science China IS. arXiv:2309.07864.

---

*报告编制日期：2026年1月10日*
*时效性说明：本报告涵盖2024-2025年主要学术进展，部分2025年论文为预印本状态(待验证)，框架版本信息截至报告日期*