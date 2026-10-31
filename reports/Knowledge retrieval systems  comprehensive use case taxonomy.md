# Knowledge retrieval systems: comprehensive use case taxonomy

Modern knowledge retrieval systems built on RAG (Retrieval-Augmented Generation) architectures now span **eight distinct capability domains**: document Q&A, agentic reasoning, structured extraction, multi-modal processing, knowledge graph construction, conversational exploration, fact verification, and automated pipeline orchestration. LlamaIndex, LangChain, and GraphRAG each occupy different positions in this landscape—LlamaIndex excels at data connectors and retrieval patterns, LangChain dominates in agent orchestration and integrations, while GraphRAG uniquely addresses global sensemaking queries that vector RAG fundamentally cannot answer. For a new architecture design, the critical gaps exist in **real-time streaming updates**, **cross-framework interoperability**, **multi-agent debugging**, and **human-in-the-loop workflows** with feedback incorporation.

---

## LlamaIndex provides the deepest retrieval pattern library

LlamaIndex processes over **150 million pages monthly** through LlamaParse and offers the most sophisticated retrieval patterns in the ecosystem. Its core strength lies in flexible document processing: auto-retrieval with metadata filtering, recursive retrieval for hierarchical documents, hybrid search combining BM25 and vector similarity, and auto-merging retrievers that provide sentence-level precision with paragraph-level context.

The framework's agent capabilities center on **FunctionAgent** and **ReActAgent** implementations, where agents decide when to retrieve, which indices to query, and how to decompose complex queries into sub-questions. A particularly powerful pattern is the sub-question query engine, which breaks complex queries into atomic questions, queries multiple sources in parallel, and synthesizes unified responses.

For production deployments, LlamaIndex provides **Workflows**—an event-driven, async-first orchestration layer supporting branching, looping, parallel execution, and human-in-the-loop pauses. LlamaCloud adds enterprise data connectors for SharePoint, Google Drive, Confluence, and S3, with SOC 2 Type 2 certification. Notable deployments include Salesforce Agentforce and Boeing's Jeppesen, which reports **~2,000 engineering hours saved** using the framework.

Key capabilities that distinguish LlamaIndex include **PropertyGraphIndex** for knowledge graph RAG, **LlamaExtract** for schema-based structured extraction, and multi-modal retrieval supporting GPT-4V, Gemini Pro Vision, and Claude 3 Vision for visual Q&A over charts, diagrams, and tables.

---

## LangChain dominates agent orchestration and integrations

LangChain's primary value proposition is its **1,000+ integrations** and the maturity of its agent abstractions. The framework offers 150+ document loaders, 60+ vector store integrations, and 40+ retriever types. Its LCEL (LangChain Expression Language) provides declarative composition of LLM operations using pipe operators: `prompt | model | parser`, enabling optimized parallel execution with automatic streaming support.

The significant evolution in the LangChain ecosystem is **LangGraph**, which enables graph-based stateful workflows with cycles and branches—something traditional DAG frameworks cannot support. LangGraph introduces conditional edges, automatic checkpointing, and time-travel debugging (rewind and take different actions). Adoption statistics show **43% of LangSmith organizations** send LangGraph traces, with average workflow steps increasing from **2.8 in 2023 to 7.7 in 2024**.

Enterprise deployments include LinkedIn's SQL Bot multi-agent system, Uber's agentic applications, Elastic's AI Assistant, and Replit's code generation agents. LangSmith provides production observability with step-by-step execution visualization, token consumption tracking, and LLM-as-judge evaluation patterns.

Memory patterns in LangChain support multiple strategies: ConversationBufferMemory for full history, ConversationBufferWindowMemory for recent K messages, ConversationSummaryMemory for LLM-generated summaries, and VectorStoreRetrieverMemory for semantic search over history. The structured output capabilities include provider-native (ProviderStrategy), tool-based (ToolStrategy), and various output parsers with automatic error recovery.

---

## GraphRAG solves the global question problem

Microsoft's GraphRAG addresses a fundamental limitation of vector-based RAG: **inability to answer global questions** like "What are the main themes in this dataset?" These are query-focused summarization (QFS) tasks, not retrieval tasks, and vector RAG retrieves semantically similar chunks but cannot reason across an entire corpus.

**Community-led summarization** works through a multi-stage process. First, an LLM extracts entities and relationships from documents to create a knowledge graph. The Leiden algorithm then partitions this graph into hierarchical communities—densely connected clusters of entities. For each community, summaries are generated: leaf-level communities summarize entity/relationship descriptions ordered by node prominence, while higher-level communities recursively incorporate sub-community summaries. The result is a hierarchy from root-level themes (**34-55 summaries**) to granular topics (**1,300-2,100 summaries** at level 3).

**Query-focused summarization** uses a map-reduce approach over these community summaries. Community summaries are shuffled, divided into chunks, and processed in parallel by the LLM to generate intermediate answers with helpfulness scores (0-100). The reduce step sorts by score and synthesizes a final answer. On global sensemaking questions, GraphRAG achieves **72-83% win rate** on comprehensiveness and **62-82%** on diversity compared to vector RAG.

The critical insight: root-level GraphRAG achieves competitive performance at **9-43x lower token cost per query**, but requires expensive upfront indexing (**281 minutes for 1M tokens** with GPT-4-turbo). GraphRAG excels for private document collections, intelligence analysis, multi-hop reasoning, and domains requiring explainability. It fails for simple fact retrieval, highly dynamic data, and cost-sensitive applications.

---

## Industry applications reveal distinct requirements

**Financial services** shows the most mature RAG deployments. Morgan Stanley's AI @ Morgan Stanley Assistant serves **16,000+ financial advisors** with 98% adoption, increasing document retrieval efficiency from 20% to 80%. Their AskResearchGPT synthesizes **70,000+ annual research reports**. HSBC's "Amy" processed over **3 million reports in Q1 2024** alone.

**Customer support automation** demonstrates measurable ROI. DoorDash's Dasher Support Chatbot combines RAG retrieval with LLM judges assessing five metrics. LinkedIn's Knowledge Graph RAG reduced **median resolution time by 28.6%** by capturing intra-issue structure. Salesforce Agentforce deployments handle **66% of external queries and 84% of internal ones** for clients like Fisher & Paykel.

**Healthcare applications** face unique regulatory requirements. Apollo 24|7's Clinical Intelligence Engine uses Google's MedPaLM with RAG for real-time access to de-identified patient data. Zebra-Llama embeds **30 AASLD hepatology guidelines** in a PHI-compliant system. The nephrology-specific GPT-4 RAG system aligned with KDIGO guidelines outperforms standard GPT-4 by reducing hallucinations and enhancing clinical relevance.

**Legal and compliance** requires document structure preservation. Harvey AI processes documents in **45+ countries and multiple languages**. Neo4j's Agentic GraphRAG for contracts structures the CUAD dataset (**500+ contracts**) into knowledge graphs for precise queries, overcoming naive RAG's tendency to pull terms from irrelevant contracts.

**Academic research** tools show impressive scale. Elicit searches **138 million papers + 545,000 clinical trials**, with users reporting **5+ hours saved weekly**. VDI/VDE achieved **99.4% accuracy** extracting 1,502 of 1,511 data points in a systematic review pilot.

---

## Advanced use cases define the frontier

**Multi-agent collaboration** has emerged through frameworks with distinct philosophies. CrewAI excels at role-based orchestration for deterministic business workflows. AutoGen (Microsoft) enables conversational agent-to-agent interactions for dynamic collaboration. LangGraph provides graph-based DAG workflows for complex decision trees. Production deployments exist in compliance automation, supply chain optimization, and software engineering teams.

**Autonomous research agents** represent a major capability advancement. Perplexity Deep Research uses test-time compute expansion mimicking human cognition, achieving **21.1% accuracy** on Humanity's Last Exam benchmark and completing most tasks within 3 minutes. The open-source GPT Researcher generates comprehensive reports by consulting dozens of sources with citations.

**Knowledge graph construction** is becoming automated. Neo4j's LLM Knowledge Graph Builder provides end-to-end KG construction with schema inference. LightRAG achieves **10x token reduction** versus GraphRAG through dual-level retrieval. AutoSchemaKG (ATLAS) built a zero-schema graph with **900M+ nodes from 50M documents**. Organizations report **300-320% ROI** from automated KG construction.

**Real-time streaming updates** remain technically challenging. The Streaming RAG architecture (arXiv 2508.05662) achieves **Recall@10 gains up to 3 points** with **<15ms latency** and **>900 docs/sec** throughput using heavy-hitter filters and mini-batch clustering. Frameworks like Pathway enable real-time sync with SharePoint and Google Drive.

**Fact verification** systems have matured significantly. The FIRE framework achieves **7.6x LLM cost reduction** and **16.5x search cost reduction** through iterative retrieval with confidence-based decision making. VERIFAID provides RAG-based fake news detection with auto-growing datasets.

---

## Workflow patterns determine architecture requirements

**Single-turn versus multi-turn** interactions require fundamentally different architectures. Single-turn queries enable stateless scaling where any node handles any request. Multi-turn conversations require session tracking, memory hierarchies (short-term conversation buffer plus long-term vector store), and context window management through sliding windows, summarization, or selective forgetting. DH-RAG's Dynamic Historical Context Database with Query Reconstruction shows **58% improvement** in response quality for conversational scenarios.

**Batch versus real-time processing** involves explicit tradeoffs. Batch processing enables full corpus indexing (daily/weekly) with lower per-document cost but stale indices. Real-time patterns require streaming ingestion (Kafka, Kinesis), incremental index updates, and cache layers, achieving sub-100ms retrieval at higher per-query cost. The Lambda Architecture for RAG combines both: batch layer for full indexing, speed layer for streaming updates, serving layer for merged queries.

**Human-in-the-loop workflows** support three patterns: user confirmation (boolean yes/no), return of control (full parameter modification), and feedback tagging (structured critique). Amazon Bedrock Agents, LlamaIndex Workflows, and LangGraph all support input-required events for workflow pauses. Pistis-RAG implements online learning from human feedback through alignment and querying phases.

**Automated pipelines** require RAGOps practices: automated testing of retrieval relevance, canary deployments for model updates, knowledge base versioning, and component-level tracing. Observability tools include Langfuse, LangSmith, Arize Phoenix, and RAGAS for automated faithfulness scoring. Production deployments implement graceful degradation, automatic retries with exponential backoff, and circuit breakers.

---

## Capability matrix reveals architecture gaps

| Capability Domain | LlamaIndex | LangChain | GraphRAG | Gap Severity |
|-------------------|------------|-----------|----------|--------------|
| Document Q&A | ★★★★★ | ★★★★☆ | ★★★☆☆ | Low |
| Agent orchestration | ★★★★☆ | ★★★★★ | ★★☆☆☆ | Low |
| Global sensemaking | ★★★☆☆ | ★★☆☆☆ | ★★★★★ | Medium |
| Streaming updates | ★★★☆☆ | ★★★☆☆ | ★★☆☆☆ | High |
| Multi-modal | ★★★★☆ | ★★★☆☆ | ★★☆☆☆ | Medium |
| Human-in-loop | ★★★★☆ | ★★★★☆ | ★★☆☆☆ | Medium |
| Cross-lingual | ★★★☆☆ | ★★★☆☆ | ★★☆☆☆ | High |
| Multi-agent debugging | ★★★☆☆ | ★★★★☆ | ★★☆☆☆ | High |
| Structured extraction | ★★★★★ | ★★★★☆ | ★★★☆☆ | Low |

---

## Conclusion: strategic gaps for new architectures

Three capability clusters remain underserved across existing frameworks. First, **unified global-local retrieval** that seamlessly combines GraphRAG's community summarization for thematic questions with vector RAG's precise fact retrieval—current hybrid approaches require manual routing. Second, **native streaming architectures** supporting sub-second index updates without query interruption, where Pathway and custom Kafka solutions exist but aren't first-class framework features. Third, **cross-framework interoperability** allowing LlamaIndex's data connectors to feed LangGraph's orchestration with GraphRAG's reasoning—currently requiring substantial integration code.

Emerging capabilities approaching production readiness include autonomous research agents (Perplexity at **21% accuracy** on hardest benchmarks), automated knowledge graph construction (**300%+ ROI** reported), and multi-agent collaboration (LinkedIn, Uber deploying LangGraph). The critical enablers for 2025-2026 will be **feedback loop integration** (Pistis-RAG patterns), **cost-optimized indexing** (LightRAG's 10x reduction), and **conversational context management** (CID-GraphRAG's intent transition graphs).

For a new architecture design, the highest-value capabilities to prioritize are: unified retrieval modes (local + global), first-class streaming support, multi-agent observability beyond traces, and declarative human-in-the-loop primitives with feedback incorporation.