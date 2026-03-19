# [PROJECT NAME]: Intelligence Layer Architecture

> **Parent**: [ARCHITECTURE_BLUEPRINT.md](../ARCHITECTURE_BLUEPRINT.md)
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]

Retrieval architecture, embedding design, evaluation framework, and data pipeline.
Extends the model strategy and RAG assessment defined in ARCHITECTURE_BLUEPRINT.

---

## 1. Intelligence Assessment

### 1.1 Knowledge Requirements

What knowledge and data do agents need access to beyond their model's training data?

| Agent | Knowledge Domain | Data Source | Format | Volume |
|-------|-----------------|-------------|--------|--------|
| `[agent_1]` | [domain] | [source] | [structured/unstructured] | [size] |
| `[agent_2]` | [domain] | [source] | [structured/unstructured] | [size] |

### 1.2 Intelligence Gaps

What makes agents "dumb" without the intelligence layer?

- [Gap 1: e.g., "No access to product documentation — agents hallucinate feature details"]
- [Gap 2: e.g., "No customer history — agents can't personalize responses"]
- [Gap 3: e.g., "No real-time data — agents use stale pricing information"]

### 1.3 Requirements Matrix

| Dimension | Requirement | Rationale |
|-----------|-------------|-----------|
| **Accuracy** | [acceptable error rate] | [why this threshold] |
| **Latency** | [time budget for retrieval] | [user experience impact] |
| **Cost** | [$/query or $/month budget] | [business constraint] |
| **Freshness** | [how current data must be] | [staleness impact] |
| **Scale** | [document count, query volume] | [growth projection] |

---

## 2. Retrieval Architecture

### 2.1 RAG Pipeline Design

> Extends RAG assessment from [ARCHITECTURE_BLUEPRINT.md](../ARCHITECTURE_BLUEPRINT.md#15-model-strategy--rag-assessment)

**Pattern**: [Naive | Advanced | Modular | Agentic RAG]

**Rationale**: [Why this pattern fits the requirements from Section 1.3]

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        RAG PIPELINE                                      │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   Data Sources          Ingestion           Index                       │
│   ────────────          ─────────           ─────                       │
│   [source 1]  ──►  [preprocessing]  ──►  [vector store]               │
│   [source 2]  ──►  [chunking]       ──►  [index type]                 │
│   [source 3]  ──►  [embedding]      ──►  [metadata]                   │
│                                                                         │
│   Query                 Retrieval          Generation                   │
│   ─────                 ─────────          ──────────                   │
│   [user query]  ──►  [search strategy]  ──►  [context assembly]       │
│                  ──►  [reranking]        ──►  [model + prompt]         │
│                  ──►  [filtering]        ──►  [output validation]     │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 2.2 Chunking Strategy

| Content Type | Strategy | Chunk Size | Overlap | Rationale |
|--------------|----------|------------|---------|-----------|
| [type] | [fixed/semantic/recursive/document] | [tokens] | [tokens] | [why] |

### 2.3 Knowledge Store Design

| Decision | Choice | Rationale |
|----------|--------|-----------|
| **Vector DB** | [provider] | [why — scale, cost, features] |
| **Similarity Metric** | [cosine/dot/euclidean] | [why — data characteristics] |
| **Index Type** | [HNSW/IVF/flat] | [why — speed vs accuracy] |
| **Hybrid Search** | [yes/no] | [why — keyword + semantic?] |
| **Reranking** | [model or N/A] | [why — precision improvement vs latency cost] |

---

## 3. Model Routing & Fallback

### 3.1 Routing Logic

> Extends model strategy from [ARCHITECTURE_BLUEPRINT.md](../ARCHITECTURE_BLUEPRINT.md#15-model-strategy--rag-assessment)

**Routing approach**: [complexity-based | cost-based | task-based | static]

**Complexity detection**: [How to determine query complexity — e.g., token count, keyword triggers, classifier]

### 3.2 Fallback Chains

| Agent | Primary Model | Fallback | Trigger |
|-------|--------------|----------|---------|
| `[agent_1]` | [model] | [fallback model] | [timeout/error/cost threshold] |
| `[agent_2]` | [model] | [fallback model] | [timeout/error/cost threshold] |

### 3.3 Fine-tuning vs Prompting vs RAG

| Capability | Approach | Rationale |
|------------|----------|-----------|
| [capability 1] | [prompting/RAG/fine-tune] | [why this over alternatives] |
| [capability 2] | [prompting/RAG/fine-tune] | [why this over alternatives] |

---

## 4. Embedding Design

### 4.1 Embedding Model Selection

| Criterion | Choice | Rationale |
|-----------|--------|-----------|
| **Model** | [model name] | [why — benchmark performance, domain fit, cost] |
| **Dimensions** | [N] | [why — storage vs accuracy tradeoff] |
| **Max Tokens** | [N] | [input length limit] |
| **Domain Adaptation** | [yes/no] | [approach if yes — fine-tuning, prefix, etc.] |

### 4.2 Embedding Pipeline

```
[Data Source]
    │
    ▼
[Preprocessing] ── Clean, normalize, extract metadata
    │
    ▼
[Chunking] ── Split by [strategy] with [overlap] overlap
    │
    ▼
[Embedding] ── [model] → [dimensions]-dim vectors
    │
    ▼
[Storage] ── [vector DB] with [index type] index
    │
    ▼
[Metadata] ── Store: source, timestamp, chunk_id, [custom fields]
```

### 4.3 Embedding Refresh Strategy

| Trigger | Action | Frequency |
|---------|--------|-----------|
| New documents | [incremental embed + index] | [on ingest] |
| Model update | [full re-embed or skip?] | [on model release] |
| Schema change | [rebuild index?] | [on schema migration] |
| Quality drift | [re-embed subset?] | [on drift detection] |

---

## 5. Evaluation Framework

### 5.1 Evaluation Pyramid

```
            ┌─────────────┐
            │ Agent Eval   │  ← E2E with real LLM, structured output validation
           ─┴─────────────┴─
          ┌─────────────────┐
          │   Tool Eval     │  ← Domain-specific evaluators, seeded data
         ─┴─────────────────┴─
        ┌─────────────────────┐
        │   Integration       │  ← Pipeline with test models, dependency injection
       ─┴─────────────────────┴─
      ┌─────────────────────────┐
      │      Unit               │  ← Deterministic tests, no LLM calls
     ─┴─────────────────────────┴─
```

### 5.2 Evaluation Methods

| Method | Purpose | When to Use | Implementation |
|--------|---------|-------------|----------------|
| **Custom evaluators** | Domain-specific validation | [scenario] | [approach] |
| **LLM-as-judge** | Faithfulness, relevance, coherence | [scenario] | [approach] |
| **Golden datasets** | Ground truth comparison | [scenario] | [approach — seeded data] |
| **Boundary testing** | Off-topic rejection, edge cases | [scenario] | [approach] |
| **Schema validation** | Structured output correctness | [scenario] | [approach] |
| **Human-in-the-loop** | Sampled review, expert scoring | [scenario] | [approach — sampling rate] |

### 5.3 Quality Metrics

| Metric | Target | Measurement Method | Alert Threshold |
|--------|--------|-------------------|-----------------|
| Retrieval precision | [target] | [how] | < [threshold] |
| Answer faithfulness | [target] | [how] | < [threshold] |
| Context relevance | [target] | [how] | < [threshold] |
| Cost per query | $[target] | [how] | > $[threshold] |
| Latency P95 | [target]ms | [how] | > [threshold]ms |
| Off-topic rejection rate | [target]% | [how] | < [threshold]% |

---

## 6. Data Pipeline

### 6.1 Ingestion Flows

| Data Source | Format | Ingestion Method | Frequency |
|-------------|--------|-----------------|-----------|
| [source 1] | [format] | [API/webhook/batch] | [schedule] |
| [source 2] | [format] | [API/webhook/batch] | [schedule] |

### 6.2 Freshness Guarantees

| Data Type | Update Cadence | Strategy | SLA |
|-----------|---------------|----------|-----|
| [type 1] | [cadence] | [batch/streaming/webhook] | [max staleness] |
| [type 2] | [cadence] | [batch/streaming/webhook] | [max staleness] |

### 6.3 Context Assembly

How retrieved context is assembled before passing to the model:

1. **Retrieval**: [Top-K results from vector search]
2. **Reranking**: [Reorder by relevance score]
3. **Deduplication**: [Remove near-duplicate chunks]
4. **Truncation**: [Fit within context window — [N] tokens budget]
5. **Ordering**: [Chronological / relevance / source-grouped]
6. **Injection**: [Template for combining context with user query]

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Assess knowledge requirements for each agent
2. Decide RAG applicability (consistent with ARCHITECTURE_BLUEPRINT Section 1.5)
3. Design retrieval pipeline if RAG is needed
4. Select embedding model and vector store
5. Define evaluation framework with tiered pyramid
6. Document data pipeline and freshness guarantees
7. Ensure model routing aligns with agent topology

**Key questions to answer:**

- Does each agent need external knowledge, or is model knowledge sufficient?
- What's your latency budget for retrieval?
- How often does your knowledge base change?
- What evaluation methods fit your domain?
- What's your cost budget per query?

---

## Validation Schema (For AI Generation)

```yaml
# INTELLIGENCE_LAYER validation gate
inputs_required:
  - arch.agent_topology[]: from_architecture_blueprint
  - arch.tech_stack: from_architecture_blueprint
  - arch.model_strategy: from_architecture_blueprint
  - arch.rag_assessment: from_architecture_blueprint
  - prompts[].system_prompt: from_agent_prompts

outputs_produced:
  - intelligence.retrieval_architecture: used_by_implementation_scaffold
  - intelligence.embedding_design: used_by_implementation_scaffold
  - intelligence.evaluation_framework: used_by_testing_strategy
  - intelligence.data_pipeline: used_by_post_deployment
  - intelligence.model_routing: used_by_observability
  - intelligence.quality_metrics: used_by_observability

validation_gate:
  required_sections:
    - "Intelligence Assessment"
    - "Retrieval Architecture"
    - "Evaluation Framework"

  minimum_content:
    retrieval_decisions: 1
    evaluation_methods: 2
    quality_metrics: 3

  placeholders_filled:
    - "[PROJECT NAME]"

cross_references:
  - retrieval: must_align_with: arch.rag_assessment
  - model_routing: must_cover: arch.agent_topology
  - quality_metrics: must_include: latency_and_cost

quality_checks:
  - rag_pipeline: has_all_stages_if_rag_yes
  - evaluation: covers_quality_cost_and_latency
  - embedding: has_model_and_dimensions_if_rag_yes

decision_prompts:
  - "Does each agent need external knowledge beyond model training data?"
  - "What's your retrieval latency budget?"
  - "How often does your knowledge base change?"
  - "What evaluation methods fit your domain?"
  - "What's your cost budget per query?"
```

**After generation, verify:**
- [ ] RAG decision aligns with ARCHITECTURE_BLUEPRINT Section 1.5
- [ ] Model routing covers all agents in topology
- [ ] Evaluation framework has at least 2 methods
- [ ] Quality metrics include cost and latency
- [ ] Embedding model specified if RAG=yes
- [ ] Data pipeline freshness matches requirements

---



*Document generated by North Star Advisor*
