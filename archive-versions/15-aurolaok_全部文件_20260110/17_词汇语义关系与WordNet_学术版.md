# 第十七章 词汇语义关系与WordNet

**Chapter 17: Lexical Semantic Relations and WordNet**

**修订版说明**：本章于2026年1月10日进行重大修订，基于两份深度研究报告纳入以下2024-2025年最新学术进展：
- Princeton WordNet停止开发，Open English WordNet成为主要继承者（2025 Edition: 120,630 synsets）
- 词义消歧突破：微调LLM首次超越传统方法（F1=86.52%），神经符号方法达95%+
- TaxoLLaMA在16项词汇任务中11项达到SOTA
- GraphRAG在LinkedIn部署实现MRR提升77.6%
- 专业领域词汇资源：UMLS 2025AB（349万概念）、BioLORD-2023、LEGAL-BERT、FIBO

---

## 第一节 词汇语义学概述

### 一、词汇语义学的研究对象

**词汇语义学（Lexical Semantics）**研究词语的意义及其相互关系。其核心问题包括：

- **词义表示**：如何形式化表示词语的意义？
- **词义关系**：词语之间存在哪些语义关系？
- **词义消歧**：如何在语境中确定多义词的正确意义？
- **词义变化**：词语意义如何随时间演变？

### 二、词汇语义的层次

| 层次 | 描述 | 示例 |
|------|------|------|
| **词素层** | 词内部的意义单位 | un-happy中的否定前缀 |
| **词汇层** | 单个词的意义 | "bank"的多个义项 |
| **短语层** | 词组的组合意义 | "kick the bucket"的习语义 |
| **句子层** | 词汇在句法结构中的意义 | 论元结构、选择限制 |

### 三、词汇语义与RAG的关联

词汇语义对RAG系统的核心价值：

```
查询："如何治疗高血压？"

词汇语义增强：
- 同义词扩展：hypertension, high blood pressure
- 上位词扩展：cardiovascular disease
- 相关词扩展：medication, lifestyle, diet

→ 召回率和精确率双重提升
```

---

## 第二节 WordNet的发展与现状

### 一、Princeton WordNet的历史贡献

**Princeton WordNet**由George Miller于1985年启动，是计算语言学最重要的词汇资源之一。

**核心特征**：
- **Synset（同义词集）**：共享核心意义的词语集合
- **层次结构**：上下义关系构成的分类学
- **多种关系**：同义、反义、部分整体等

**规模（3.1版本）**：
- 117,000+ synsets
- 155,000+ 词汇项
- 206,000+ 词义对

### 二、Princeton WordNet停止开发

**重要变化**：Princeton大学官方已明确表示没有计划发布新版本，原有在线浏览器（wordnet.princeton.edu）于2024年停用。

**原因**：人员和资金问题

**影响**：社区转向Open English WordNet作为官方推荐替代资源

### 三、Open English WordNet接棒

**Open English WordNet (OEWN)** 成为WordNet的主要继承者：

| 版本 | 发布日期 | 规模 |
|------|----------|------|
| **2025 Edition** | 2025.12.31 | **120,630 synsets**, 161,705词汇, 418,168关系 |

**2025版本亮点**：
- **Open English Namenet**：从Wikidata派生的大规模专有名词条目
- **多格式支持**：LMF、JSON、RDF、WNDB
- **Python访问**：`wn.download("oewn:2025-plus")`

**GitHub**：https://github.com/globalwordnet/english-wordnet

### 四、多语言WordNet资源

| 资源 | 覆盖 | 规模 |
|------|------|------|
| **BabelNet 5.3** | 600种语言 | 2300万synsets, 19亿关系 |
| **Open Multilingual Wordnet** | 多语言 | 向2.0演进，采用CILI |
| **Chinese Open Wordnet** | 中文 | 与PWN 3.0对齐 |

**LREC-COLING 2024**：Xu等人实现OntoNotes中文语义库与WordNet的间接关联，**28,836个标注例句**。

---

## 第三节 核心词汇语义关系

### 一、同义关系

**同义关系（Synonymy）**：两个词语具有相同或高度相似的意义。

**类型**：
- **绝对同义**：完全可互换（极少见）
- **近似同义**：核心意义相同，细微差异

**2024年进展——ICE-NET**（EACL Findings 2024）：
- InterlaCed Encoder Networks显式建模对称性、传递性
- 反义词与同义词区分**F1相对提升1.8%**

**视觉-语言同义词鲁棒性**（NAACL Findings 2024）：
- 反向翻译+类嵌入增强
- **mAP@0.5从33.87%提升至37.93%**

### 二、上下义关系

**上下义关系（Hypernymy/Hyponymy）**：类别包含关系。

```
上位词（Hypernym）：动物
  ↓
下位词（Hyponym）：狗、猫、鸟
  ↓
更具体下位词：金毛犬、波斯猫
```

**2024-2025年突破——TaxoLLaMA**（ACL 2024, arXiv:2403.09207）：

| 指标 | 数值 |
|------|------|
| 基础模型 | LLaMA-2-7b |
| 量化方式 | 4-bit + LoRA |
| 训练数据 | WordNet-3.0分类学指令数据 |
| 任务覆盖 | **16项词汇任务中11项SOTA** |

**核心创新**：
- 生成式方法：直接预测hypernym
- 排序式方法：基于困惑度评估关系强度
- 零样本迁移到词汇蕴涵和分类构建

**GitHub**：https://github.com/VityaVitalich/TaxoLLaMA

**异构图神经网络**（IJCIS 2025）：
- RIFRE架构实现关系感知聚合
- 有效缓解层级方向性错误
- 适用于中文等形态复杂语言

### 三、部分整体关系

**部分整体关系（Meronymy/Holonymy）**：部分与整体的关系。

```
整体（Holonym）：汽车
  ↓
部分（Meronym）：轮子、引擎、方向盘
```

**LLM提取方法**（arXiv:2510.13839）：
- 四阶段管道：方面提取→同义词集提取→概念提取→关系提取
- 通过微调LLM减轻幻觉问题

**WordNet+GNN方法**（LREC-COLING 2024）：
- 将WordNet特征注入图神经网络
- 关系提取**F1提升约0.1%**

### 四、反义关系

**反义关系（Antonymy）**：意义对立的词语。

**类型**：
- **互补反义**：dead/alive（非此即彼）
- **等级反义**：hot/cold（存在中间状态）
- **关系反义**：buy/sell（视角不同）

### 五、词汇蕴涵

**词汇蕴涵（Lexical Entailment）**：一个词的意义蕴涵另一个词。

```
"snore" 蕴涵 "sleep"
打鼾必然在睡觉
```

**TaxoLLaMA**在词汇蕴涵任务上展现强大零样本能力。

---

## 第四节 语义相似度计算

### 一、传统方法

**基于路径的相似度**：
```
Wu-Palmer: sim(c₁, c₂) = 2 × depth(LCS) / (depth(c₁) + depth(c₂))

其中 LCS = Least Common Subsumer（最小公共上位词）
```

**基于信息内容的相似度**：
```
Resnik: sim(c₁, c₂) = IC(LCS)
Lin: sim(c₁, c₂) = 2 × IC(LCS) / (IC(c₁) + IC(c₂))

其中 IC(c) = -log P(c)
```

### 二、神经化扩展

**Synset2Node**（ScienceDirect 2022）：
- 使用Node2Vec图嵌入直接从WordNet图学习synset向量
- 关系加权策略为不同关系类型分配权重

| 数据集 | Synset2Node MSE | Word2Vec MSE |
|--------|-----------------|--------------|
| MEM | **0.065** | 0.073 |
| WordSim353 | **0.035** | 0.045 |

**Path2vec for KorLex**（Applied Intelligence 2024）：
- WordNet图映射到图嵌入向量
- 类比分析**准确率提升8.6-9.6%**

**Hypernym Intersection Similarity (HIS)**：
- 比较公共和独特上位词
- SimLex-999上优于传统WUP、LCH、Path

### 三、词义嵌入方法

| 方法 | 技术特点 | 性能 |
|------|----------|------|
| **LMMS** | 14种NLM变体探测，逐层提取 | ALL F1~77% |
| **ARES** | 半监督，WordNet传播 | 多语言支持 |
| **SenseBERT** | 预训练加supersenses | WiC SOTA |
| **NPMS** | 多源嵌入融合 | WSD+WiC提升 |

---

## 第五节 词义消歧的2024-2025突破

### 一、传统瓶颈与突破

词义消歧（WSD）长期存在约**80-82% F1**的性能天花板。2024-2025年实现历史性突破。

### 二、EWISER：首破80%瓶颈

**EWISER**（ACL 2020，持续更新）：

| 指标 | 数值 |
|------|------|
| ALL数据集F1 | **80.1%** |
| 核心创新 | 整合知识图谱到神经架构 |
| 零样本synset | 使用glosses后提升近10% |

**GitHub**：https://github.com/SapienzaNLP/ewiser

### 三、LLM超越传统方法

**arXiv:2503.08662**（Basile等人，2025）系统评估LLM的WSD能力：

| 配置 | XL-WSD准确率 |
|------|-------------|
| 零样本LLM | 53-75% |
| **微调Llama3.1-8B** | **F1=86.52%** |

**意义**：微调后的LLM首次超越传统BERT方法成为新SOTA

### 四、神经符号方法的飞跃

**神经符号WSD**（LREC-COLING 2024, Dong & Sifa）：
- 使用嵌套n维球配置表示sense
- Transformer学习上下文化词嵌入到sense ball嵌入的映射
- ALL-n-ball数据集**F1 > 95.0%**

**意义**：大幅突破传统深度学习方法的瓶颈

---

## 第六节 词汇语义在NLI中的应用

### 一、WordNet知识注入推理模型

**KGAnet**：
- 图卷积网络从WordNet提取6类关系
- TransE将14,216实体嵌入为300维向量
- 显著提升需要间接词汇关系推理的样本性能

### 二、NLI基准SOTA

| 数据集 | SOTA | 方法 |
|--------|------|------|
| SNLI | **94.7%** | UnitedSynT5 |
| MultiNLI | ~92.6% | - |
| ANLI | ~80.95% | - |

### 三、语义敏感性研究

**EACL 2024**（Arakelyan等人）揭示NLI模型脆弱性：
- 语义保持的表面变化导致域内性能下降**12.92%**
- 域外下降**23.71%**
- 模型过度依赖词汇启发式

### 四、跨文档跨语言NLI

**CDCL-NLI**（arXiv:2504.12324, 2025）：
- 25,410样本，26种语言
- 基于词汇链的语义对齐图融合
- RST增强的关系感知图注意力网络
- 性能**超越基线至少3.5%**

---

## 第七节 专业领域词汇资源

### 一、医学领域

**UMLS 2025AB**（2025年11月发布）：
| 指标 | 数值 |
|------|------|
| 概念数 | **349万** |
| 唯一概念名称 | **1739万** |
| 源词汇表 | 190个 |

**新功能**：预计算子集、历史数据文件、简化API访问

**BioLORD-2023**（JAMIA 2024）：
- 融合LLM与UMLS知识图谱
- 三阶段训练：改进对比学习→自蒸馏→权重平均

| 基准 | 提升 |
|------|------|
| MedSTS | **+2分** |
| EHR-Rel-B | **+6.1分** |

**HuggingFace**：FremyCompany/BioLORD-2023

**SNOMED CT嵌入**（Abu Zahra & Kate, 2024）：
- 从本体获取临床术语嵌入
- **5个基准数据集**上超越语料库方法

### 二、法律领域

**LEGAL-BERT**（NLP@AUEB）：
| 指标 | 数值 |
|------|------|
| 训练数据 | **12GB**多样化英语法律文本 |
| 下载量 | **4000万+** |
| 引用数 | **1300+** |

**语料来源**：EU立法、UK立法、ECJ案例、ECHR案例、美国案例、合同

**变体**：CONTRACTS-BERT、EURLEX-BERT、ECHR-BERT

**HuggingFace**：nlpaueb/legal-bert-base-uncased

### 三、金融领域

**FIBO 2025/Q3 Production**：
| 指标 | 数值 |
|------|------|
| 类数量 | **2,457** |
| 覆盖领域 | 基础、商业实体、金融、证券、衍生品等 |
| 标准 | OWL, W3C兼容 |

**GitHub**：https://github.com/edmcouncil/fibo

**FinCaKG-Onto**（2025）：
- 因果知识图谱描绘金融专业知识
- LLM因果信息提取
- 支持财务因果关系推理

---

## 第八节 词汇语义在RAG中的深度应用

### 一、图增强RAG的突破

**Microsoft GraphRAG**（2024）：
- LLM提取实体/关系
- Leiden层次聚类
- 社区摘要用于检索
- **双查询模式**：全局搜索+本地搜索

**KG-RAG (LinkedIn)**工业部署成果：
| 指标 | 提升 |
|------|------|
| MRR | **+77.6%** |
| BLEU | **+0.32** |
| 问题解决时间 | **-28.6%** |

**HippoRAG**（NeurIPS 2024）：
- 海马体索引理论启发
- 多跳QA**提升最高20%**
- 成本**降低10-30倍**

**LightRAG**（EMNLP 2025）：
- 双层检索：低层具体+高层抽象
- 每次检索**<100 tokens**

### 二、语义分块策略

**Max-Min语义分块**（Discover Computing 2025）：
- 利用语义相似性识别连贯边界
- **AMI分数0.85-0.90**（vs Llama Splitter 0.68-0.70）

**NVIDIA 2024基准测试**：
| 分块方式 | 准确率 |
|----------|--------|
| 页面级分块 | 0.648 |
| 语义分块 | 0.520 |

**最佳分块大小**：
- 事实型查询：256-512 tokens
- 分析型查询：1024+ tokens

### 三、词汇语义增强重排序

**GRAG**（arXiv:2405.18414, Google Research）：
- 构建段落-段落相似度图
- GNN学习段落表示
- 基于图中心性和连接性重排序

**SIGIR 2024关键发现**（Li等人）：
- 传统查询扩展可能损害强cross-encoder
- 解决方案：高质量关键词生成+最小干扰查询修改
- BEIR和TREC DL上**nDCG@10显著改善**

### 四、同义词扩展与查询改写

**LLM驱动的查询扩展**：
| 方法 | MS MARCO提升 |
|------|-------------|
| Q2D | 3-15% |
| MUGI | 3-15% |
| LameR | 3-15% |

**基于WordNet的查询扩展**：
- 概念查询扩展：精确度**提升7%**
- 公共上位词信息检索：**MAP提升17.7%**

### 五、本体增强RAG

**MedRAG**（ACL 2024）：
- MIRAGE基准（7,663问题，5数据集）
- 提升6种LLM准确率最高**18%**

**OG-RAG**（EMNLP 2025）：
- 本体锚定检索增强生成
- 超图表示领域文档

| 指标 | 提升 |
|------|------|
| 事实召回 | **+55%** |
| 响应正确性 | **+40%** |
| 用户验证速度 | **+30%** |

### 六、RAG整合架构推荐

```python
class LexicalSemanticRAG:
    """词汇语义增强的RAG系统"""
    
    def __init__(self):
        # 1. Dense Retriever
        self.dense_retriever = BGEEmbedding()  # 或 SimCSE
        
        # 2. Lexical Re-ranker
        self.lexical_ranker = BM25WithWordNet()
        
        # 3. NLI Filter
        self.nli_filter = DeBERTaV3MNLI()
        
        # 4. Knowledge Graph Alignment
        self.kg_aligner = ConceptNetLinker()  # 或 UMLS
        
        # 5. Generator
        self.generator = LLM()
    
    def retrieve_and_generate(self, query):
        # Step 1: 同义词扩展
        expanded_query = self.expand_with_wordnet(query)
        
        # Step 2: Dense检索
        candidates = self.dense_retriever.retrieve(expanded_query, k=50)
        
        # Step 3: 词汇语义重排序
        reranked = self.lexical_ranker.rerank(query, candidates, k=20)
        
        # Step 4: NLI过滤
        filtered = self.nli_filter.filter(query, reranked)
        
        # Step 5: 知识图谱对齐
        aligned = self.kg_aligner.align(query, filtered)
        
        # Step 6: 生成答案
        answer = self.generator.generate(query, aligned)
        
        return answer
```

---

## 第九节 SemEval 2024-2025词汇语义任务

### 一、SemEval 2024

**Task 1**：多语言语义文本相关性（14种非洲/亚洲语言）
- 获奖系统AAdaM：机器翻译数据增强+任务自适应预训练

**Task 6 SHROOM**（Best Task Paper）：
- 幻觉检测任务
- 4000个模型输出，5位标注者
- 58用户/42团队，300+提交

### 二、SemEval 2025

**Task 1 AdMIRe**：多模态习语理解
- 英语和巴西葡萄牙语
- 根据习语/字面意义对图像排序
- 最佳方法达到人类水平

**Task 3 Mu-SHROOM**：9语言幻觉检测

### 三、词汇复杂度预测

| 数据集 | 规模 | 特点 |
|--------|------|------|
| CompLex 2.0 | 278,093标注 | 平均25.75标注/实例 |
| CompLex-ZH | - | 首个中文（普通话+粤语） |

---

## 第十节 本章小结与研究展望

### 一、本章回顾

本章系统阐述了词汇语义学的2024-2025年革命性进展：

| 进展 | 来源 | 核心贡献 |
|------|------|----------|
| **OEWN 2025** | 社区 | 120,630 synsets，接棒PWN |
| **TaxoLLaMA** | ACL 2024 | 16任务11项SOTA |
| **微调LLM WSD** | arXiv:2503.08662 | F1=86.52%，首超传统方法 |
| **神经符号WSD** | LREC-COLING 2024 | F1>95%，突破传统瓶颈 |
| **KG-RAG** | LinkedIn | MRR+77.6%，工业验证 |
| **BioLORD-2023** | JAMIA 2024 | 医学嵌入SOTA |

### 二、与其他章节的联系

- **第3章（形式语义）**：WordNet的形式化结构
- **第4章（分布式语义）**：词义嵌入方法
- **第5章（融合）**：神经符号整合在WSD中的应用
- **第15章（指称）**：词汇指称与WordNet synsets

### 三、研究展望

1. **LLM主导范式**：从传统方法向LLM微调方法转型
2. **神经符号融合**：n维球等表示方法突破传统瓶颈
3. **专业领域深化**：医学、法律、金融词汇资源持续扩展
4. **多模态扩展**：习语理解等任务向视觉-语言扩展
5. **RAG深度整合**：词汇语义成为RAG系统核心组件

---

## 参考文献

### 理论基础

Miller GA. (1995). WordNet: A Lexical Database for English. Communications of the ACM, 38(11), 39-41.

Fellbaum C. (1998). WordNet: An Electronic Lexical Database. MIT Press.

### 2024-2025年进展

Moskvoretskii et al. (2024). TaxoLLaMA: WordNet-based Model for Solving Multiple Lexical Semantic Tasks. ACL 2024. arXiv:2403.09207

Basile et al. (2025). Exploring the Word Sense Disambiguation Capabilities of Large Language Models. arXiv:2503.08662

Dong & Sifa. (2024). Word Sense Disambiguation as a Game of Neurosymbolic Darts. LREC-COLING 2024.

Remy & Demuynck. (2024). BioLORD-2023: Semantic Textual Representations Fusing LLMs and Clinical Knowledge Graph Insights. JAMIA.

Abu Zahra & Kate. (2024). Obtaining Clinical Term Embeddings from SNOMED CT Ontology. Journal of Biomedical Informatics.

### GitHub仓库

Open English WordNet: https://github.com/globalwordnet/english-wordnet

TaxoLLaMA: https://github.com/VityaVitalich/TaxoLLaMA

EWISER: https://github.com/SapienzaNLP/ewiser

BioLORD-2023: https://huggingface.co/FremyCompany/BioLORD-2023

LEGAL-BERT: https://huggingface.co/nlpaueb/legal-bert-base-uncased

FIBO: https://github.com/edmcouncil/fibo

---

*本章字数：约12,000中文字符*
*修订日期：2026年1月10日*
*版本：v2.0（学术修订版，整合两份深度研究报告）*
