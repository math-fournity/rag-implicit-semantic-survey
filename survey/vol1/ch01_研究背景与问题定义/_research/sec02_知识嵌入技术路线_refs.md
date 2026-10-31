# sec02_知识嵌入技术路线全景 - Research参考

**Research完成时间**: 2026-01-09
**用途**: 供Claude CLI编写第二节正文

---

## 写作指令

本节需覆盖四条知识嵌入路径：
1. 增量预训练（Continued Pre-training / DAPT / TAPT）
2. 有监督微调（SFT）与表面对齐假设
3. 参数高效微调（PEFT）：LoRA → DoRA → QDoRA
4. 模型编辑（Model Editing）：ROME → MEMIT → WISE → EasyEdit
5. 与RAG的对比：何时选择哪种方案

预计字数：6,000-8,000字

---

## 核心文献

### 1. 增量预训练

**Gururangan et al. (2020) - Don't Stop Pretraining**
- **标题**: Don't Stop Pretraining: Adapt Language Models to Domains and Tasks
- **会议**: ACL 2020
- **arXiv**: https://arxiv.org/abs/2004.10964
- **引用数**: ~3,000+
- **核心概念**: 
  - DAPT (Domain-Adaptive Pre-Training): 在领域语料上继续预训练
  - TAPT (Task-Adaptive Pre-Training): 在任务相关语料上继续预训练
- **本节用途**: 增量预训练的奠基性论文

**灾难性遗忘相关**：
- Kirkpatrick et al. (2017) "Overcoming catastrophic forgetting" - EWC方法
- 知识回放(Replay)策略

---

### 2. 有监督微调（SFT）

**表面对齐假设 (Surface Alignment Hypothesis)**
- **核心观点**: SFT教的是"规矩"（格式、风格）而非"知识"
- **证据**: 微调后模型的知识边界并未扩展
- **本节用途**: 阐明SFT的局限性，为RAG提供理论依据

**InstructGPT (Ouyang et al., 2022)**
- **标题**: Training language models to follow instructions with human feedback
- **arXiv**: https://arxiv.org/abs/2203.02155
- **本节用途**: SFT+RLHF的代表性工作

---

### 3. 参数高效微调（PEFT）

**LoRA (Hu et al., 2022)**
- **标题**: LoRA: Low-Rank Adaptation of Large Language Models
- **会议**: ICLR 2022
- **arXiv**: https://arxiv.org/abs/2106.09685
- **GitHub**: https://github.com/microsoft/LoRA
- **引用数**: ~8,000+
- **核心公式**: W' = W + BA (B∈R^{d×r}, A∈R^{r×k}, r << min(d,k))
- **本节用途**: PEFT的奠基性工作

**DoRA (Liu et al., 2024)** [ICML 2024 Oral, 接受率1.5%]
- **标题**: DoRA: Weight-Decomposed Low-Rank Adaptation
- **会议**: ICML 2024 (Oral)
- **arXiv**: https://arxiv.org/abs/2402.09353
- **GitHub**: https://github.com/NVlabs/DoRA
- **ICML官方**: https://proceedings.mlr.press/v235/liu24bn.html
- **作者机构**: NVIDIA Research
- **核心创新**: 
  - 将预训练权重分解为幅度(magnitude)和方向(direction)两个分量
  - 使用LoRA仅更新方向分量
  - 学习模式更接近全量微调(FT)
- **性能**: 在LLaMA、LLaVA、VL-BART上一致超越LoRA
- **本节用途**: LoRA的最新改进

**QDoRA (Answer.AI, 2024)**
- **标题**: Efficient finetuning of LLaMA 3 with FSDP QDoRA
- **博客**: https://www.answer.ai/posts/2024-04-26-fsdp-qdora-llama3.html
- **核心观点**: QDoRA显著超越QLoRA，甚至接近全量微调
- **本节用途**: 量化+DoRA的实践验证

---

### 4. 模型编辑（Model Editing）

**ROME (Meng et al., 2022)** [NeurIPS 2022]
- **标题**: Locating and Editing Factual Associations in GPT
- **会议**: NeurIPS 2022
- **arXiv**: https://arxiv.org/abs/2202.05262
- **项目页面**: https://rome.baulab.info/
- **引用数**: ~800+
- **核心方法**:
  - Causal Tracing: 因果追踪定位知识存储位置
  - Rank-One Model Editing: 秩一更新修改MLP层权重
- **关键发现**: 事实性知识主要存储在中间层的MLP模块
- **本节用途**: 模型编辑的开创性工作

**MEMIT (Meng et al., 2023)** [ICLR 2023]
- **标题**: Mass-Editing Memory in a Transformer
- **会议**: ICLR 2023
- **arXiv**: https://arxiv.org/abs/2210.07229
- **项目页面**: https://memit.baulab.info/
- **核心创新**: 
  - 扩展ROME到多层编辑
  - 支持批量编辑（thousands of edits）
  - 在GPT-J (6B)和GPT-NeoX (20B)上验证
- **本节用途**: 大规模模型编辑

**WISE (2024)** [NeurIPS 2024]
- **标题**: WISE: Rethinking the Knowledge Memory for Lifelong Model Editing of Large Language Models
- **会议**: NeurIPS 2024
- **来源**: EasyEdit GitHub更新日志 (2024-09-26)
- **本节用途**: 终身学习场景下的模型编辑

**EasyEdit (Wang et al., 2024)** [ACL 2024]
- **标题**: EasyEdit: An Easy-to-use Knowledge Editing Framework for Large Language Models
- **会议**: ACL 2024 (System Demonstrations)
- **arXiv**: https://arxiv.org/abs/2308.07269
- **ACL Anthology**: https://aclanthology.org/2024.acl-demos.9/
- **GitHub**: https://github.com/zjunlp/EasyEdit
- **作者机构**: 浙江大学
- **支持方法**: ROME, MEMIT, MEND, SERAC, IKE, KN等
- **支持模型**: T5, GPT-J, LLaMA, LLaMA-2, Mistral, Qwen (1B-65B)
- **本节用途**: 统一的模型编辑工具框架

---

### 5. 知识嵌入综述

**Yao et al. (2023)**
- **标题**: Editing Large Language Models: Problems, Methods, and Opportunities
- **会议**: EMNLP 2023
- **arXiv**: https://arxiv.org/abs/2305.13172
- **ACL Anthology**: https://aclanthology.org/2023.emnlp-main.632/

**Zhang et al. (2024)**
- **标题**: A Comprehensive Study of Knowledge Editing for Large Language Models
- **arXiv**: https://arxiv.org/abs/2401.01286
- **本节用途**: 知识编辑的全面综述

**知识编辑ACM综述**
- **标题**: Knowledge Editing for Large Language Models: A Survey
- **期刊**: ACM Computing Surveys
- **链接**: https://dl.acm.org/doi/10.1145/3698590

---

### 6. RAG vs 微调选择

**关键对比维度**：

| 维度 | RAG | 微调/编辑 |
|------|-----|----------|
| 知识更新 | 实时，更换文档即可 | 需要重新训练/编辑 |
| 可追溯性 | 高，可引用来源 | 低，知识融入参数 |
| 计算成本 | 推理时增加检索开销 | 训练时高，推理时无 |
| 幻觉控制 | 有外部依据约束 | 仍可能产生幻觉 |
| 适用场景 | 动态知识、需引用 | 静态知识、需内化 |

---

## 写作建议

### 结构建议
1. 开篇：知识嵌入的四条技术路径概览
2. 增量预训练：DAPT/TAPT原理、灾难性遗忘问题
3. SFT：表面对齐假设、局限性分析
4. PEFT：LoRA→DoRA→QDoRA演进
5. 模型编辑：ROME→MEMIT→WISE→EasyEdit
6. 对比总结：何时选择RAG vs 微调 vs 编辑

### 关键数据点
- DoRA: ICML 2024 Oral, 接受率1.5%
- LoRA引用数: ~8,000+
- ROME引用数: ~800+
- EasyEdit支持模型: 1B-65B
- MEMIT: 可处理thousands of edits

---

## 元信息

```yaml
section: 02_知识嵌入技术路线
schema_position: 第二节
estimated_words: 6000-8000
core_references: 10篇
research_date: 2026-01-09
status: research_complete
```
