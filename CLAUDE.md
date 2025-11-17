# DEEP TECH DISCOVERY SYSTEM - PROJECT SPECIFICATION

You are tasked with building a complete system for discovering obscure technical knowledge through multi-agent exploration. This document contains ALL specifications, requirements, and tasks.

## 🎯 PROJECT OVERVIEW

**Goal**: Create an agentic system that takes a technical seed topic (e.g., "Anycast BGP routing") and discovers 15-20 non-obvious, obscure related topics through systematic exploration.

**Key Principle**: Discover the "iceberg below" - hidden knowledge that most developers don't know exists.

**Example Flow**:
```
Input: "Cloudflare unlimited bandwidth"
Output:
- BGP Hijacking (obscurity: 0.78)
- Internet Exchange Points (obscurity: 0.72)
- AS_PATH Prepending (obscurity: 0.81)
- BGP Zombie Routes (obscurity: 0.89)
... (15 total discoveries with sources, reasoning chains, rabbit holes)
```

---

## 🏗️ SYSTEM ARCHITECTURE

### High-Level Design
```
ORCHESTRATOR (Main Session)
├── DECOMPOSER (Sub-agent) - Breaks down seed topic
├── EXPLORERS (5 parallel sub-agents) - Find related tech
│   ├── Component Explorer
│   ├── Layer Traverser
│   ├── Analogical Reasoner
│   ├── Temporal Analyzer
│   └── Constraint Explorer
├── MINERS (4 parallel sub-agents) - Find obscure details
│   ├── Security Miner
│   ├── Economic Analyzer
│   ├── Inversion Thinker
│   └── Implementation Detective
├── VALIDATOR (Sub-agent) - Verifies quality
└── SYNTHESIZER (Sub-agent) - Creates final output
```

### Model Selection Strategy

**Use Anthropic's Claude models:**
- **Claude Sonnet 4.5** for: Orchestrator, Decomposer, Validator, Synthesizer
- **Claude Haiku 4.5** for: All Explorer and Miner sub-agents

**Why**: Sonnet for complex reasoning, Haiku for focused parallel tasks.

---

## 📋 PHASE 1: PROJECT SETUP

### Task 1.1: Read Official Documentation

**CRITICAL**: Before writing ANY code, research the following official documentation:

1. **Claude Code Documentation**
   - Search: "site:docs.anthropic.com claude code sub-agents"
   - Read: Sub-agents, tools, model configuration
   - Focus: How to create and invoke sub-agents

2. **Claude Code Best Practices**
   - Search: "site:anthropic.com claude code best practices"
   - Read: Agent patterns, orchestration, context management

3. **Model Selection**
   - Search: "site:anthropic.com claude sonnet haiku differences"
   - Read: When to use each model

4. **Tools Available**
   - Search: "site:docs.anthropic.com claude code tools"
   - Read: Read, Grep, Bash, Task tools

**Output**: Create `docs/RESEARCH_NOTES.md` with key findings

### Task 1.2: Create Project Structure

Create the following directory structure:
```
tech-discovery-system/
├── .claude/
│   ├── agents/           # Sub-agent definitions (create this)
│   └── commands/         # Slash commands (create this)
├── data/
│   └── discoveries/      # Output storage (create this)
├── docs/
│   └── RESEARCH_NOTES.md
├── CLAUDE.md            # This file
└── README.md            # Project documentation
```

### Task 1.3: Document Research

In `docs/RESEARCH_NOTES.md`, document:
- How sub-agents work in Claude Code
- Model configuration syntax
- Tools available to agents
- Best practices learned

---

## 📋 PHASE 2: CREATE SUB-AGENTS

### Task 2.1: Decomposer Agent

**Purpose**: Break down seed topic into components, constraints, and fundamental principles.

**File**: `.claude/agents/decomposer.md`

**Model**: claude-sonnet-4-5-20250929

**Required Output Format**:
```json
{
  "components": {
    "entities": ["..."],
    "actions": ["..."],
    "properties": ["..."]
  },
  "constraint_violated": {
    "normal_rule": "...",
    "violation": "..."
  },
  "five_whys": [
    {"q": "...", "a": "..."}
  ],
  "fundamental_principle": "..."
}
```

**Instructions for Agent**:
```markdown
---
name: decomposer
description: Deep comprehension specialist. Breaks topics into fundamental components.
tools: [Read, Grep, Bash]
model: claude-sonnet-4-5-20250929
---

You are a technical analyst specializing in deep comprehension.

INPUT: A technical seed topic (string)

YOUR TASK:
1. DECOMPOSE into atomic parts:
   - Entities (nouns)
   - Actions (verbs)
   - Properties (adjectives)

2. IDENTIFY the constraint being violated:
   - What rule is being broken/optimized?
   - What's normally expensive/limited?
   - What surprise factor exists?

3. RUN 5 WHYS:
   - Ask "why" 5 times minimum
   - Trace to fundamental principles
   - Hit economics/physics/human nature

4. OUTPUT structured JSON (format specified above)

CRITICAL: Base analysis on technical knowledge. Use logical reasoning.
```

**Your Task**: Create this file with complete specifications.

---

### Task 2.2: Explorer Agents (5 agents)

Create FIVE explorer sub-agents in `.claude/agents/`:

#### 2.2.1: component-explorer.md
- Model: claude-haiku-4-5-20250429
- Purpose: Find related technologies through component analysis
- Input: Components from decomposer
- Output: 3-5 discoveries with reasoning chains

#### 2.2.2: layer-traverser.md
- Model: claude-haiku-4-5-20250429
- Purpose: Go up/down abstraction layers
- Input: Seed topic + constraints
- Output: Lower-level primitives + higher-level patterns

#### 2.2.3: analogical-reasoner.md
- Model: claude-haiku-4-5-20250429
- Purpose: Find cross-domain patterns
- Input: Abstract pattern from seed
- Output: Similar structures in other domains

#### 2.2.4: temporal-analyzer.md
- Model: claude-haiku-4-5-20250429
- Purpose: Trace evolution and future
- Input: Seed topic
- Output: Historical context + future trends

#### 2.2.5: constraint-explorer.md
- Model: claude-haiku-4-5-20250429
- Purpose: Find other situations with same constraint
- Input: Constraint from decomposer
- Output: Different solutions to same problem

**Specification for Each Explorer**:
```markdown
---
name: [agent-name]
description: [purpose]
tools: [Read, Grep]
model: claude-haiku-4-5-20250429
---

INPUT: [specify input format]

PROCESS:
1. [Step 1]
2. [Step 2]
3. [Step 3]

OUTPUT FORMAT:
{
  "discoveries": [
    {
      "topic": "...",
      "reasoning_chain": ["step1", "step2", "step3"],
      "obscurity_score": 0.0-1.0,
      "sources": ["..."],
      "next_rabbit_holes": ["..."]
    }
  ]
}

RULES:
- Only obscure findings (not mainstream)
- Must be 2+ degrees from seed
- Provide reasoning chain
```

**Your Task**: Create all 5 agent files with complete specifications.

---

### Task 2.3: Miner Agents (4 agents)

Create FOUR miner sub-agents in `.claude/agents/`:

#### 2.3.1: security-miner.md
- Purpose: Find attack vectors, vulnerabilities
- Model: claude-haiku-4-5-20250429

#### 2.3.2: economic-analyzer.md
- Purpose: Business models, incentives, cost structures
- Model: claude-haiku-4-5-20250429

#### 2.3.3: inversion-thinker.md
- Purpose: Opposites, failures, anti-patterns
- Model: claude-haiku-4-5-20250429

#### 2.3.4: implementation-detective.md
- Purpose: How it's actually built, technical details
- Model: claude-haiku-4-5-20250429

**Your Task**: Create all 4 agent files with specifications.

---

### Task 2.4: Validator Agent

**File**: `.claude/agents/validator.md`

**Model**: claude-sonnet-4-5-20250929

**Purpose**: Validate discoveries for quality, obscurity, relevance

**Scoring Criteria**:
```
Obscurity Score (0-1):
- 0.0-0.3: Common knowledge (REJECT)
- 0.4-0.6: Somewhat known
- 0.7-0.8: Obscure (GOOD)
- 0.9-1.0: Very obscure (EXCELLENT)

Relevance Score (0-1):
- 0.0-0.4: Weakly related (REJECT)
- 0.5-0.7: Related
- 0.8-1.0: Strongly related

Source Quality:
- RFCs, academic papers: 10/10
- Official docs: 8/10
- Tech blogs: 5/10
- Forums: 2/10
```

**Your Task**: Create validator agent that scores each discovery.

---

### Task 2.5: Synthesizer Agent

**File**: `.claude/agents/synthesizer.md`

**Model**: claude-sonnet-4-5-20250929

**Purpose**: Combine all discoveries into final structured output

**Output Format**:
```markdown
# Discovery Report: [Seed Topic]

## Metadata
- Date: [timestamp]
- Discoveries: [count]
- Verified: [count]

---

## Discovery 1: [Topic Name]

**Obscurity**: 0.XX
**Relevance**: 0.XX
**Verified**: ✅/❌

**Summary**: [1-2 sentence summary]

**Reasoning Chain**:
1. [Seed] → [Step 1]
2. [Step 1] → [Step 2]
3. [Step 2] → [Discovery]

**Key Insight**: [Non-obvious fact]

**Sources**:
- [URL 1]
- [URL 2]

**Next Rabbit Holes**:
- [Related topic 1]
- [Related topic 2]

---

[Repeat for all discoveries]

---

## Knowledge Graph

### Connections
- [Discovery 1] → ENABLES → [Discovery 2]
- [Discovery 3] → VULNERABLE_TO → [Discovery 1]

### Recommended Next Exploration
1. [High-value topic 1]
2. [High-value topic 2]
```

**Your Task**: Create synthesizer agent with complete output format.

---

## 📋 PHASE 3: CREATE ORCHESTRATOR

### Task 3.1: Main CLAUDE.md Orchestration Logic

Update THIS file to include orchestration logic:
```markdown
## ORCHESTRATION WORKFLOW

When user runs: `/discover [seed-topic]`

### Stage 1: Decomposition (Sequential)
1. Invoke @decomposer with seed topic
2. Wait for completion
3. Parse JSON output
4. Store in context

### Stage 2: Parallel Exploration
Launch ALL 5 explorer agents in parallel using Task tool:
- @component-explorer (pass decomposer.components)
- @layer-traverser (pass decomposer.constraints)
- @analogical-reasoner (pass decomposer.fundamental_principle)
- @temporal-analyzer (pass seed topic)
- @constraint-explorer (pass decomposer.constraint_violated)

Wait for all to complete. Aggregate results.

### Stage 3: Parallel Mining
Launch ALL 4 miner agents in parallel:
- @security-miner (pass seed + explorer results)
- @economic-analyzer (pass seed + explorer results)
- @inversion-thinker (pass seed + explorer results)
- @implementation-detective (pass seed + explorer results)

Wait for all to complete. Aggregate results.

### Stage 4: Validation (Sequential)
1. Collect all discoveries (explorers + miners)
2. Invoke @validator with complete list
3. Filter: Keep only obscurity > 0.6 AND relevance > 0.5
4. Store validated list

### Stage 5: Synthesis (Sequential)
1. Invoke @synthesizer with validated discoveries
2. Generate markdown report
3. Save to data/discoveries/[seed]-[timestamp].md
4. Display summary to user

### Error Handling
- If any agent fails, log error and continue
- If <5 valid discoveries, warn user
- If decomposer fails, abort (critical)
```

**Your Task**: Implement this orchestration logic in main context.

---

### Task 3.2: Create Slash Command

**File**: `.claude/commands/discover.md`
```markdown
---
name: discover
description: Run full discovery pipeline on a technical topic
---

USAGE: /discover <seed-topic>

EXAMPLE: /discover "Cloudflare unlimited bandwidth"

This command will:
1. Decompose the topic (1 agent)
2. Explore in parallel (5 agents)
3. Mine obscure knowledge (4 agents)
4. Validate findings (1 agent)
5. Synthesize report (1 agent)

Expected time: 3-5 minutes
Expected discoveries: 15-20 topics

Output saved to: data/discoveries/[seed]-[timestamp].md
```

**Your Task**: Create this slash command file.

---

## 📋 PHASE 4: TESTING & VALIDATION

### Task 4.1: Create Test Cases

**File**: `docs/TEST_CASES.md`

Create 3 test cases:

**Test 1: Technical Infrastructure**
- Seed: "Anycast BGP routing"
- Expected: 15+ discoveries about BGP, routing, networking
- Validate: Obscurity scores reasonable, sources credible

**Test 2: Software Concept**
- Seed: "Process Hollowing"
- Expected: Security topics, Windows internals, evasion techniques
- Validate: Technical depth, implementation details

**Test 3: Abstract Concept**
- Seed: "Eventual Consistency"
- Expected: Distributed systems, CAP theorem, databases
- Validate: Cross-domain connections

### Task 4.2: Run Tests

Execute each test case:
1. Run `/discover [seed]`
2. Review output markdown
3. Check quality metrics
4. Document results

### Task 4.3: Iteration

If tests fail:
- Refine agent prompts
- Adjust scoring thresholds
- Improve orchestration logic

---

## 📋 PHASE 5: DOCUMENTATION

### Task 5.1: README.md

Create comprehensive README with:
- Project overview
- Installation (none needed - uses Claude Code)
- Usage instructions
- Example outputs
- Architecture diagram
- Model selection rationale

### Task 5.2: ARCHITECTURE.md

Document:
- System design decisions
- Agent responsibilities
- Data flow
- Model selection strategy
- Why 10 agents and not more/less

### Task 5.3: Example Output

Create `docs/EXAMPLE_OUTPUT.md` with:
- Full discovery run on "CDN edge computing"
- Show all 15 discoveries
- Demonstrate quality of output

---

## 🔧 TECHNICAL REQUIREMENTS

### Agent Creation Guidelines

1. **Model Configuration**:
   - Use exact model strings: `claude-sonnet-4-5-20250929`, `claude-haiku-4-5-20250429`
   - Specify in agent frontmatter

2. **Tool Access**:
   - All agents can use: Read, Grep, Bash
   - Be explicit about which tools needed

3. **Output Format**:
   - ALWAYS JSON for structured data
   - Include reasoning chains
   - No hallucinations - acknowledge uncertainty

4. **Context Management**:
   - Agents should be stateless
   - All context passed explicitly
   - No assumptions about parent context

### Orchestrator Requirements

1. **Parallel Execution**:
   - Use Task tool for parallel sub-agent invocation
   - Document in Claude Code official docs how this works

2. **Error Handling**:
   - Graceful degradation if agent fails
   - Log errors to stderr
   - Continue with partial results

3. **Progress Reporting**:
   - Show user which stage is running
   - Display agent completion
   - Final summary statistics

---

## ⚠️ CRITICAL RULES

### Research Requirements

1. **Official Docs Only**:
   - Search: `site:docs.anthropic.com` OR `site:anthropic.com`
   - DO NOT use third-party blogs, Medium, etc.
   - If uncertain, check official docs again

2. **No Assumptions**:
   - If you don't know how something works, research it
   - Document all findings in RESEARCH_NOTES.md
   - Ask user if documentation unclear

3. **Verification**:
   - Test each component before moving forward
   - Use `/status` to check sub-agent availability
   - Validate JSON outputs parse correctly

### Quality Standards

1. **Agent Prompts**:
   - Clear, unambiguous instructions
   - Specific output formats
   - Examples where helpful
   - No vague language

2. **Code Quality**:
   - Markdown formatting correct
   - YAML frontmatter valid
   - JSON schemas specified

3. **Documentation**:
   - Every decision explained
   - Trade-offs documented
   - Future improvements noted

---

## 🎯 ACCEPTANCE CRITERIA

The system is complete when:

✅ All 10 sub-agent files created and valid
✅ Orchestrator workflow implemented
✅ Slash command `/discover` works
✅ 3 test cases pass successfully
✅ Documentation complete (README, ARCHITECTURE, EXAMPLES)
✅ Output format matches specification
✅ Quality metrics reasonable (obscurity, relevance)

---

## 📝 EXECUTION PLAN

### Step-by-Step Implementation

1. **First**: Read ALL official documentation (Task 1.1)
2. **Second**: Create project structure (Task 1.2)
3. **Third**: Document research findings (Task 1.3)
4. **Fourth**: Create decomposer agent (Task 2.1)
5. **Fifth**: Create 5 explorer agents (Task 2.2)
6. **Sixth**: Create 4 miner agents (Task 2.3)
7. **Seventh**: Create validator agent (Task 2.4)
8. **Eighth**: Create synthesizer agent (Task 2.5)
9. **Ninth**: Implement orchestration (Task 3.1)
10. **Tenth**: Create slash command (Task 3.2)
11. **Eleventh**: Run tests (Task 4.1-4.3)
12. **Twelfth**: Write documentation (Task 5.1-5.3)

### Time Estimate

- Research: 30 minutes
- Agent creation: 2 hours
- Orchestration: 1 hour
- Testing: 1 hour
- Documentation: 30 minutes
- **Total**: ~5 hours

---

## 🚀 START HERE

### Your First Actions

1. Say: "Starting Deep Tech Discovery System build"
2. Create project structure
3. Begin Task 1.1: Research Claude Code documentation
4. Document findings in RESEARCH_NOTES.md
5. Proceed through phases sequentially

### Progress Tracking

After each major task:
- Commit files
- Update this CLAUDE.md with checkboxes
- Test the component
- Document any issues

---

## 💡 TIPS FOR SUCCESS

1. **Don't Rush**: Research thoroughly before coding
2. **Test Incrementally**: Validate each agent individually
3. **Use Examples**: Show concrete examples in prompts
4. **Stay Organized**: Keep files structured and documented
5. **Ask Questions**: If specification unclear, ask user

---

## 🎓 LEARNING GOALS

By building this system, you'll learn:
- Multi-agent orchestration patterns
- Prompt engineering for specific tasks
- Model selection strategy
- Parallel execution in Claude Code
- Structured output generation
- Quality validation techniques

---

**BEGIN IMPLEMENTATION NOW**

Start with Phase 1, Task 1.1: Research official Claude Code documentation about sub-agents.

Good luck! 🚀
