# Deep Tech Discovery System - Architecture

This document provides a comprehensive technical overview of the Deep Tech Discovery System's architecture, design decisions, and rationale.

---

## Table of Contents

1. [System Overview](#system-overview)
2. [Architectural Principles](#architectural-principles)
3. [Agent Design](#agent-design)
4. [Data Flow](#data-flow)
5. [Model Selection Strategy](#model-selection-strategy)
6. [Parallelization Strategy](#parallelization-strategy)
7. [Quality Control](#quality-control)
8. [Error Handling](#error-handling)
9. [Design Trade-offs](#design-trade-offs)
10. [Alternative Architectures Considered](#alternative-architectures-considered)

---

## System Overview

### Core Concept

The system implements a **multi-stage, multi-agent pipeline** for knowledge discovery:

```
INPUT: Seed Topic (e.g., "Cloudflare unlimited bandwidth")
       ↓
STAGE 1: Decomposition (1 agent, sequential)
       ↓
STAGE 2: Exploration (5 agents, parallel)
       ↓
STAGE 3: Mining (4 agents, parallel)
       ↓
STAGE 4: Validation (1 agent, sequential)
       ↓
STAGE 5: Synthesis (1 agent, sequential)
       ↓
OUTPUT: Comprehensive Discovery Report (15-20 discoveries)
```

### Key Characteristics

- **Multi-Agent**: 10 specialized agents + 1 orchestrator
- **Hybrid Execution**: Sequential stages with parallel execution within stages
- **Quality-Gated**: Validation layer filters low-quality discoveries
- **Model-Optimized**: Sonnet for reasoning, Haiku for focused tasks
- **Fault-Tolerant**: Graceful degradation on agent failures

---

## Architectural Principles

### 1. Separation of Concerns

**Principle**: Each agent has a single, well-defined responsibility.

**Rationale**:
- Simpler agent prompts → higher quality outputs
- Easier to debug and improve individual agents
- Agents can be developed and tested independently
- Avoids "do everything" agents that perform poorly

**Example**:
- ❌ Bad: "Find related technologies and their security implications"
- ✅ Good: Separate agents for "Component Explorer" and "Security Miner"

### 2. Composition Over Monoliths

**Principle**: Complex reasoning emerges from composing simple agents.

**Rationale**:
- A single mega-agent would require enormous context and complex prompts
- Smaller agents with focused tasks produce higher-quality outputs
- Composition allows flexibility (add/remove/replace agents)
- Matches how human research teams work (specialists collaborating)

### 3. Explicit Data Flow

**Principle**: All data passed explicitly between agents via structured formats (JSON).

**Rationale**:
- No hidden state or implicit context
- Easy to trace data lineage
- Agents are stateless and composable
- Enables debugging and auditing

### 4. Fail-Safe Design

**Principle**: System continues with partial results if non-critical agents fail.

**Rationale**:
- 15 discoveries from 8 agents is better than 0 from system crash
- Decomposer failure aborts (critical dependency)
- Explorer/Miner failures logged but don't block pipeline
- Graceful degradation maintains user value

### 5. Quality Over Quantity

**Principle**: Filter aggressively for high-quality, obscure discoveries.

**Rationale**:
- 15 excellent discoveries > 30 mediocre ones
- Obscurity target (0.7+) ensures expert-level knowledge
- Validation gate maintains quality standards
- Better user experience from curated results

---

## Agent Design

### Agent Categories

We use **three categories** of agents:

1. **Analysis Agents** (Sonnet 4.5): Deep reasoning required
   - Decomposer
   - Validator
   - Synthesizer

2. **Discovery Agents** (Haiku 4.5): Focused exploration
   - 5 Explorers (component, layer, analogical, temporal, constraint)
   - 4 Miners (security, economic, inversion, implementation)

3. **Orchestrator** (Sonnet 4.5): Coordination and flow control
   - Main session managing entire pipeline

### Why 10 Agents (Not More or Fewer)?

#### Why Not Fewer?

**Option: 3-5 Agents**
- ❌ Each agent would need to cover multiple domains
- ❌ Prompts become complex and diluted
- ❌ Quality degrades (trying to do too much)
- ❌ Less parallel execution opportunity

**Example Problem**: A single "Explorer" agent trying to handle component analysis, layer traversal, AND cross-domain patterns would produce superficial results in each area.

#### Why Not More?

**Option: 15-20 Agents**
- ❌ Diminishing returns on discovery diversity
- ❌ Increased orchestration complexity
- ❌ Higher cost and latency
- ❌ Overlap between agent outputs

**Example Problem**: Adding "Performance Miner" separate from "Implementation Detective" would create significant overlap with little added value.

#### The Goldilocks Number: 10

**Coverage**: 10 agents provide comprehensive coverage across:
- Technical dimensions (components, layers, implementations)
- Analytical dimensions (security, economics, inversions)
- Temporal dimension (history, present, future)
- Abstract dimensions (cross-domain patterns, constraints)

**Efficiency**: Enough parallelization (9 agents in 2 stages) without excessive overhead.

**Quality**: Each agent can maintain sharp focus on its domain.

### Agent Prompt Design

Each agent prompt includes:

1. **Identity**: "You are a [specialist type]..."
2. **Mission**: Clear statement of purpose
3. **Input Format**: Exact JSON schema expected
4. **Process**: Step-by-step reasoning instructions
5. **Output Format**: Exact JSON schema required
6. **Examples**: Concrete examples of inputs/outputs
7. **Guidelines**: Critical rules and quality standards
8. **Goal**: Motivational framing of success

**Example (Compressed)**:
```markdown
---
name: security-miner
model: haiku
---

You are a security researcher...

## Your Mission
Find 3-5 obscure security implications...

## Input Format
```json
{"seed_topic": "...", "explorer_discoveries": [...]}
```

## Process
1. Threat surface analysis
2. Historical incident research
3. Obscurity mining
4. Discovery generation

## Output Format
```json
{"discoveries": [{"topic": "...", "category": "...", ...}]}
```

## Example
[Detailed example]

## Guidelines
[Critical rules]
```

**Design Rationale**:
- **Explicit**: No ambiguity about expectations
- **Structured**: Easy for LLM to parse and follow
- **Example-Driven**: Concrete targets improve output quality
- **Constrained**: JSON output enforces structure

---

## Data Flow

### Stage 1: Decomposition

```
User Input: "Cloudflare unlimited bandwidth"
     ↓
Decomposer Agent (Sonnet)
     ↓
Output: {
  components: {entities, actions, properties},
  constraint_violated: {normal_rule, violation},
  five_whys: [{q, a}, ...],
  fundamental_principle: "..."
}
```

**Why First?**: All downstream agents depend on decomposition results.

**Why Sequential?**: Cannot parallelize - must complete before Stage 2.

**Why Sonnet?**: Requires deep analytical reasoning and "5 Whys" analysis.

### Stage 2: Exploration (Parallel)

```
Decomposition Output
     ↓
     ├──→ Component Explorer (Haiku) ──→ Discoveries [3-5]
     ├──→ Layer Traverser (Haiku) ──→ Discoveries [3-5]
     ├──→ Analogical Reasoner (Haiku) ──→ Discoveries [3-5]
     ├──→ Temporal Analyzer (Haiku) ──→ Discoveries [3-5]
     └──→ Constraint Explorer (Haiku) ──→ Discoveries [3-5]
     ↓
Aggregate: 15-25 discoveries
```

**Why Parallel?**: Agents are independent - no data dependencies.

**Why Haiku?**: Focused tasks with clear outputs, speed matters.

**Data Passed**:
- Component Explorer ← `{seed_topic, components}`
- Layer Traverser ← `{seed_topic, constraint_violated}`
- Analogical Reasoner ← `{seed_topic, fundamental_principle}`
- Temporal Analyzer ← `{seed_topic}`
- Constraint Explorer ← `{seed_topic, constraint_violated}`

### Stage 3: Mining (Parallel)

```
Exploration Output (15-25 discoveries)
     ↓
     ├──→ Security Miner (Haiku) ──→ Discoveries [3-5]
     ├──→ Economic Analyzer (Haiku) ──→ Discoveries [3-5]
     ├──→ Inversion Thinker (Haiku) ──→ Discoveries [3-5]
     └──→ Implementation Detective (Haiku) ──→ Discoveries [3-5]
     ↓
Aggregate: 12-20 additional discoveries
```

**Why After Exploration?**: Miners can reference explorer discoveries for context.

**Why Parallel?**: Miners are independent of each other.

**Why Haiku?**: Focused domain analysis, cost/speed optimization.

**Data Passed**: Each miner receives `{seed_topic, explorer_discoveries}`

### Stage 4: Validation (Sequential)

```
All Discoveries (27-45 total)
     ↓
Validator Agent (Sonnet)
     ↓
Output: {
  validated_discoveries: [filtered, scored discoveries],
  rejected_discoveries: [rejected ones],
  quality_metrics: {...}
}
```

**Why Sequential?**: Must wait for all discoveries before validation.

**Why Sonnet?**: Nuanced quality assessment requires strong reasoning.

**Filtering**: Rejects discoveries with:
- Obscurity < 0.6 (too common)
- Relevance < 0.5 (too weakly related)
- Source Quality < 4 (insufficient credibility)

**Expected Output**: 15-20 validated discoveries (~60-70% acceptance rate)

### Stage 5: Synthesis (Sequential)

```
Validated Discoveries (15-20)
     ↓
Synthesizer Agent (Sonnet)
     ↓
Output: Markdown Report
     ↓
Save to: data/discoveries/[seed]-[timestamp].md
```

**Why Sequential?**: Must wait for validation.

**Why Sonnet?**: Complex report generation, pattern identification, professional writing.

---

## Model Selection Strategy

### Decision Matrix

| Task Type | Complexity | Model | Rationale |
|-----------|------------|-------|-----------|
| Orchestration | High | Sonnet 4.5 | Multi-agent coordination, error handling |
| Deep Analysis | High | Sonnet 4.5 | "5 Whys", decomposition, principle extraction |
| Quality Assessment | High | Sonnet 4.5 | Nuanced scoring, subjective judgments |
| Report Writing | High | Sonnet 4.5 | Professional writing, synthesis |
| Focused Discovery | Medium | Haiku 4.5 | Clear inputs/outputs, well-defined tasks |
| Domain Analysis | Medium | Haiku 4.5 | Structured exploration, JSON output |

### Cost-Benefit Analysis

#### Using Haiku for Explorers/Miners

**Benefits**:
- **Speed**: 4-5x faster than Sonnet
  - Stage 2 (5 agents): ~90-120s vs. ~400-600s
  - Stage 3 (4 agents): ~90-120s vs. ~350-500s
- **Cost**: ~5-10x cheaper
  - Haiku: $1/$5 per million tokens
  - Sonnet: Higher pricing
  - Savings: ~$0.15-0.25 per discovery run

**Costs**:
- **Quality**: ~5-10% reduction in per-discovery quality
- **Depth**: Slightly less nuanced reasoning

**Net Result**: **Strongly Positive**
- 4-5x faster execution (critical for UX)
- Major cost savings (enables scale)
- Minor quality loss (acceptable given validation layer)

#### Validation as Quality Gate

By using a Sonnet validator after Haiku explorers/miners:
- Catch and filter any low-quality Haiku outputs
- Maintain overall system quality standards
- Best of both worlds: speed + quality

### Alternative Considered: All Sonnet

**Pros**:
- Highest quality from each agent
- Most nuanced reasoning

**Cons**:
- 8-12 minute execution time (poor UX)
- 5-10x higher cost
- Marginal quality improvement (~5-10%)

**Decision**: Not worth the trade-off for production use.

---

## Parallelization Strategy

### Why Parallel Execution?

**Without Parallelization**:
- 10 agents × 30-60s each = 5-10 minutes
- Poor user experience (too slow)

**With Parallelization**:
- Stage 2: 5 agents × 30-60s = 30-60s (all parallel)
- Stage 3: 4 agents × 30-60s = 30-60s (all parallel)
- Total: ~4-6 minutes (acceptable UX)

### Parallelization Boundaries

**Can Parallelize**:
- ✅ All Stage 2 agents (Explorers) - no data dependencies
- ✅ All Stage 3 agents (Miners) - independent analyses

**Cannot Parallelize**:
- ❌ Stage 1 (Decomposer) → Stage 2 - downstream depends on decomposition
- ❌ Stage 2 → Stage 3 - miners reference explorer results
- ❌ Stage 3 → Stage 4 - validator needs all discoveries
- ❌ Stage 4 → Stage 5 - synthesizer needs validated discoveries

### Implementation

Using Claude Code's Task tool:
```markdown
# Pseudo-code for orchestrator

# Stage 2: Launch all explorers in parallel
results_2 = await Task.parallel([
  Task(agent="component-explorer", input=decomposition.components),
  Task(agent="layer-traverser", input=decomposition.constraints),
  Task(agent="analogical-reasoner", input=decomposition.principle),
  Task(agent="temporal-analyzer", input=seed_topic),
  Task(agent="constraint-explorer", input=decomposition.constraint)
])

# Stage 3: Launch all miners in parallel
results_3 = await Task.parallel([
  Task(agent="security-miner", input={seed, results_2}),
  Task(agent="economic-analyzer", input={seed, results_2}),
  Task(agent="inversion-thinker", input={seed, results_2}),
  Task(agent="implementation-detective", input={seed, results_2})
])
```

---

## Quality Control

### Three-Layer Quality Strategy

**Layer 1: Agent Prompts (Prevention)**
- Explicit obscurity targets (0.7+)
- Example-driven outputs
- Quality guidelines in prompts

**Layer 2: Validation (Detection)**
- Dedicated Validator agent
- Objective scoring criteria
- Multi-dimensional assessment (obscurity, relevance, sources)

**Layer 3: Synthesis (Curation)**
- Human-quality report writing
- Pattern identification
- Coherent narrative

### Validation Criteria

**Obscurity Score Calibration**:
```
0.0-0.3: Everyone knows (undergrad CS) → REJECT
0.4-0.6: Specialists know (certifications) → BORDERLINE
0.7-0.8: Experts know (RFCs, papers) → TARGET
0.9-1.0: Researchers only (cutting edge) → EXCELLENT
```

**Relevance Score Calibration**:
```
0.0-0.4: Tangential connection → REJECT
0.5-0.7: Clear connection → ACCEPTABLE
0.8-1.0: Direct relationship → EXCELLENT
```

**Source Quality Calibration**:
```
10/10: RFCs, peer-reviewed papers → PRIMARY
8/10: Official docs, tech blogs → STRONG
5-6/10: Technical media, Wikipedia → ACCEPTABLE
2-3/10: Forums, uncited blogs → WEAK
0-1/10: No sources, rumors → REJECT
```

### Expected Pass Rates

- **Stage 2+3**: 27-45 total discoveries
- **Validator**: Reject ~25-40% (acceptance rate 60-75%)
- **Final Output**: 15-20 validated discoveries

**Rationale**: Better to have fewer high-quality discoveries than many mediocre ones.

---

## Error Handling

### Failure Modes and Responses

**Agent Returns Invalid JSON**:
```
Response: Log error, skip agent's discoveries, continue
Rationale: Partial results better than system crash
```

**Agent Returns Empty/No Discoveries**:
```
Response: Log warning, continue with other agents
Rationale: Other agents can compensate
```

**Decomposer Fails**:
```
Response: ABORT - cannot proceed without decomposition
Rationale: All downstream agents depend on this
User Message: "Decomposition failed, please retry with different topic"
```

**Validator Fails**:
```
Response: Use unvalidated discoveries with warning
Rationale: Better than no output
User Message: "Warning: Discoveries not fully validated"
```

**Synthesizer Fails**:
```
Response: Output raw validated discoveries as JSON
Rationale: User still gets data, just not formatted
```

### Retry Strategy

- **No Automatic Retries**: Avoid long waits
- **User-Initiated Retry**: User can re-run `/discover`
- **Logged Errors**: All failures logged for debugging

---

## Design Trade-offs

### 1. Breadth vs. Depth

**Decision**: Optimize for breadth (15-20 discoveries across domains)

**Trade-off**:
- ✅ Gain: Comprehensive coverage, multiple perspectives
- ❌ Lose: Deep drill-down on specific sub-topics

**Rationale**: User can follow "rabbit holes" for depth in subsequent runs.

### 2. Speed vs. Quality

**Decision**: Hybrid approach (Haiku for bulk, Sonnet for critical)

**Trade-off**:
- ✅ Gain: 4-5x faster, major cost savings
- ❌ Lose: ~5-10% quality from Haiku agents

**Rationale**: Validator compensates, speed critical for UX.

### 3. Automation vs. Interaction

**Decision**: Fully automated pipeline (no mid-process user input)

**Trade-off**:
- ✅ Gain: Simple UX, runs unattended
- ❌ Lose: Cannot steer discovery mid-process

**Rationale**: Reduces complexity, user can run multiple times.

### 4. Structured vs. Freeform Output

**Decision**: Strictly structured JSON between agents, markdown for final output

**Trade-off**:
- ✅ Gain: Reliable parsing, clear data flow, composability
- ❌ Lose: Some expressiveness in agent outputs

**Rationale**: Reliability and debuggability outweigh expressiveness.

---

## Alternative Architectures Considered

### Alternative 1: Single Mega-Agent

**Architecture**:
```
User Input → Mega-Agent (Opus) → Final Report
```

**Pros**:
- Simplest architecture
- No orchestration complexity
- Potentially most coherent output

**Cons**:
- ❌ Requires massive prompt (10K+ tokens)
- ❌ Single point of failure
- ❌ Cannot parallelize
- ❌ Hard to debug/improve
- ❌ Lower quality (trying to do everything)

**Why Rejected**: Quality and debuggability concerns.

### Alternative 2: Iterative Refinement

**Architecture**:
```
Input → Agent 1 → Agent 2 → Agent 3 → ... → Output
(each agent refines previous output)
```

**Pros**:
- Emergent complexity through iteration
- Each agent adds value

**Cons**:
- ❌ Strictly sequential (slow)
- ❌ Error propagation (early mistakes compound)
- ❌ Context accumulation (final agent sees 100K+ tokens)

**Why Rejected**: Speed concerns and error propagation risk.

### Alternative 3: Hierarchical Agents

**Architecture**:
```
Input → Coordinator
         ├→ Category A Coordinator
         │   ├→ Agent A1
         │   └→ Agent A2
         └→ Category B Coordinator
             ├→ Agent B1
             └→ Agent B2
```

**Pros**:
- Clean hierarchy
- Modular organization

**Cons**:
- ❌ Added complexity (intermediate coordinators)
- ❌ More overhead (coordinator agents)
- ❌ Marginal benefit over flat parallelization

**Why Rejected**: Complexity not justified by benefits.

### Alternative 4: Chosen Architecture (Pipeline + Parallelization)

**Architecture**: See [System Overview](#system-overview)

**Why Chosen**:
- ✅ Optimal balance of speed, quality, cost
- ✅ Parallelization where possible
- ✅ Clear data flow and dependencies
- ✅ Modular and extensible
- ✅ Fault-tolerant with graceful degradation

---

## Future Architecture Considerations

### Potential Enhancements

**1. Caching Layer**
- Cache decompositions for related topics
- Cache common discoveries
- Reduces cost and latency for similar queries

**2. Incremental Discovery**
- Build on previous discovery runs
- "Discover more about [previous finding]"
- Enables depth-first exploration

**3. Feedback Loops**
- Agents learn from validator rejections
- Adaptive prompts based on quality metrics
- Self-improving system

**4. Dynamic Agent Selection**
- Not all 10 agents for every topic
- Topic classification → subset of agents
- Optimization for speed and relevance

**5. Human-in-the-Loop**
- Pause after Stage 2 for user feedback
- User directs Stage 3 focus areas
- Interactive discovery mode

---

## Conclusion

The Deep Tech Discovery System architecture balances:
- **Quality**: Through specialized agents and validation
- **Speed**: Through parallelization and model selection
- **Cost**: Through strategic use of Haiku vs. Sonnet
- **Reliability**: Through error handling and graceful degradation
- **Extensibility**: Through modular design

The result: A production-ready system for discovering obscure technical knowledge at scale.
