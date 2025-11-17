# Quick Start Guide

## System is Ready!

The Deep Tech Discovery System is now fully operational with orchestration logic implemented.

## How to Test

### Test 1: Simple Discovery

```bash
/discover "Consistent hashing"
```

**What will happen:**
1. System will invoke decomposer agent
2. Launch 5 explorer agents in parallel
3. Launch 4 miner agents in parallel
4. Validate all discoveries
5. Generate final report
6. Save to `data/discoveries/consistent-hashing-[timestamp].md`

**Expected time:** 4-6 minutes

---

### Test 2: Complex Topic

```bash
/discover "Cloudflare unlimited bandwidth"
```

**This should discover:**
- BGP routing mechanisms
- Peering strategies
- Load balancing algorithms
- Security implications
- Economic models
- Historical context
- Implementation details

---

### Test 3: Abstract Concept

```bash
/discover "Eventual Consistency"
```

**This should discover:**
- Consensus algorithms
- CRDT data structures
- CAP theorem implications
- Database implementations
- Cross-domain analogies

---

## How It Works

When you run `/discover "topic"`, the system:

1. **Reads the slash command**: `.claude/commands/discover.md`
2. **Receives orchestration instructions**: The command file contains detailed orchestration logic
3. **Executes 5 stages sequentially**:
   - Stage 1: Decomposition (1 agent)
   - Stage 2: Exploration (5 agents in parallel)
   - Stage 3: Mining (4 agents in parallel)
   - Stage 4: Validation (1 agent)
   - Stage 5: Synthesis (1 agent)
4. **Generates report**: Saves to `data/discoveries/`

---

## Agent Invocation

The orchestrator uses the **Task tool** to invoke sub-agents:

```
Task(
  subagent_type="general-purpose",
  description="Decompose technical topic",
  prompt="Use the decomposer agent to analyze: 'Consistent hashing'",
  model="sonnet"
)
```

Agents are invoked by name (e.g., "decomposer", "component-explorer") and Claude Code routes to the appropriate agent file in `.claude/agents/`.

---

## Progress Tracking

You'll see real-time updates like:

```
🔍 STAGE 1: DECOMPOSITION - Analyzing seed topic...
✅ Stage 1 complete. Components identified: 12 entities, 8 actions, 6 properties.

🔎 STAGE 2: EXPLORATION - Launching 5 explorer agents in parallel...
✅ Stage 2 complete. 21 discoveries from exploration.

⛏️ STAGE 3: MINING - Launching 4 miner agents in parallel...
✅ Stage 3 complete. 16 additional discoveries from mining. Total: 37.

✔️ STAGE 4: VALIDATION - Assessing quality of all discoveries...
✅ Stage 4 complete. Validated 18/37 discoveries. Average obscurity: 0.74

📝 STAGE 5: SYNTHESIS - Generating comprehensive report...
✅ Stage 5 complete. Report saved to: data/discoveries/[filename].md
```

---

## Expected Output

### Console Output

Final summary showing:
- Discovery statistics
- Quality metrics
- Agent performance
- Report location
- Recommended next explorations

### File Output

Comprehensive markdown report at:
```
data/discoveries/[topic-slug]-[timestamp].md
```

Containing:
- Executive summary
- All validated discoveries
- Reasoning chains
- Sources and references
- Knowledge graph
- Recommended explorations

---

## Troubleshooting

### Issue: Agent not found

**Error**: "Agent 'decomposer' not found"

**Fix**: Ensure agent files exist in `.claude/agents/` directory

**Verify**:
```bash
ls -la .claude/agents/
```

Should show:
- decomposer.md
- component-explorer.md
- layer-traverser.md
- (etc., 10 total agents)

---

### Issue: JSON parsing error

**Error**: "Failed to parse JSON from agent output"

**Cause**: Agent returned malformed JSON

**Fix**: Check agent prompt output format requirements. Each agent must return valid JSON.

---

### Issue: No discoveries passed validation

**Error**: "Validated 0/25 discoveries"

**Cause**: Topic may be too broad/narrow, or validation too strict

**Fix**:
- Try a different, more specific topic
- Or modify validator thresholds in `.claude/agents/validator.md`

---

### Issue: Timeout

**Error**: "Agent timeout after 2 minutes"

**Cause**: Agent took too long, possibly due to network issues

**Fix**: Retry the command. If persistent, check internet connection.

---

## Validation Criteria

Discoveries must meet these thresholds to pass validation:

- **Obscurity**: ≥ 0.6 (preferably 0.7+)
- **Relevance**: ≥ 0.5 (preferably 0.65+)
- **Source Quality**: ≥ 4/10 (preferably 6+)

Expected acceptance rate: **60-75%**

---

## Performance Benchmarks

### Stage Duration

| Stage | Expected Time | Model | Parallel? |
|-------|---------------|-------|-----------|
| 1. Decomposition | 30-60s | Sonnet | No |
| 2. Exploration | 90-120s | Haiku | Yes (5 agents) |
| 3. Mining | 90-120s | Haiku | Yes (4 agents) |
| 4. Validation | 30-45s | Sonnet | No |
| 5. Synthesis | 30-45s | Sonnet | No |
| **Total** | **4-6 minutes** | Mixed | Hybrid |

### Quality Metrics

**Target Scores:**
- Average Obscurity: 0.70+
- Average Relevance: 0.65+
- Average Source Quality: 6.0+
- Final Discovery Count: 15-20

---

## Cost Estimation

**Per Discovery Run:**
- Sonnet agents: ~50,000 tokens
- Haiku agents: ~150,000 tokens
- **Total cost**: ~$0.10-0.30 (depending on usage)

**Optimizations:**
- Haiku used for bulk work (9 agents)
- Sonnet used for critical reasoning (3 agents)
- Parallel execution reduces wall-clock time by 4-5x

---

## Next Steps After First Run

1. **Review Output**: Check `data/discoveries/[filename].md`
2. **Assess Quality**: Verify obscurity and relevance scores
3. **Follow Rabbit Holes**: Pick topics from "Recommended Next Exploration"
4. **Iterate**: Run `/discover` on related topics to build knowledge graph
5. **Refine Agents**: If quality issues, adjust agent prompts in `.claude/agents/`

---

## Example Workflow

```bash
# Initial discovery
/discover "BGP routing"

# Review output
cat data/discoveries/bgp-routing-*.md

# Follow a rabbit hole from the report
/discover "BGP Communities"

# Go deeper on security aspect
/discover "BGP Hijacking"

# Explore related topic
/discover "RPKI"

# Build comprehensive knowledge graph across 4 discovery runs
```

---

## Advanced Usage

### Customizing Agents

Edit agent files in `.claude/agents/` to:
- Adjust output formats
- Change quality targets
- Add new discovery dimensions
- Modify reasoning approaches

### Adjusting Validation

Edit `.claude/agents/validator.md` to:
- Change obscurity thresholds
- Adjust relevance criteria
- Modify source quality standards

### Adding New Agents

1. Create new agent file in `.claude/agents/`
2. Define YAML frontmatter (name, model, tools)
3. Write detailed prompt
4. Update orchestration in `.claude/commands/discover.md`
5. Test with sample topic

---

## System Architecture Recap

```
┌──────────────────────────────────────┐
│         ORCHESTRATOR                 │
│      (Main Session)                  │
└──────────────────────────────────────┘
              │
              ▼
   ┌─────────────────┐
   │   DECOMPOSER    │ (Sonnet)
   └─────────────────┘
              │
              ▼
   ┌─────────────────────────────────┐
   │   5 EXPLORERS (Parallel)        │ (Haiku)
   ├─────────────────────────────────┤
   │ • Component Explorer            │
   │ • Layer Traverser               │
   │ • Analogical Reasoner           │
   │ • Temporal Analyzer             │
   │ • Constraint Explorer           │
   └─────────────────────────────────┘
              │
              ▼
   ┌─────────────────────────────────┐
   │   4 MINERS (Parallel)           │ (Haiku)
   ├─────────────────────────────────┤
   │ • Security Miner                │
   │ • Economic Analyzer             │
   │ • Inversion Thinker             │
   │ • Implementation Detective      │
   └─────────────────────────────────┘
              │
              ▼
   ┌─────────────────┐
   │   VALIDATOR     │ (Sonnet)
   └─────────────────┘
              │
              ▼
   ┌─────────────────┐
   │  SYNTHESIZER    │ (Sonnet)
   └─────────────────┘
              │
              ▼
   ┌─────────────────────────────────┐
   │   FINAL REPORT                  │
   │   data/discoveries/[file].md    │
   └─────────────────────────────────┘
```

---

**You're ready! Try running:**

```bash
/discover "Consistent hashing"
```

**And watch the magic happen!** ✨
