# Claude Code Research Notes

**Date**: 2025-11-06
**Purpose**: Document findings from official Claude Code documentation for Deep Tech Discovery System

---

## 1. Sub-Agents in Claude Code

### What Are Sub-Agents?

Sub-agents are specialized AI assistants that handle specific types of tasks with:
- Custom system prompts
- Dedicated tool access
- Separate context windows
- Specialized domain expertise

### File Structure

**Location**: `.claude/agents/` (project-level) or `~/.claude/agents` (user-level)

**Format**: Markdown files with YAML frontmatter

**Example Structure**:
```markdown
---
name: your-sub-agent-name
description: When to invoke this subagent
tools: tool1, tool2, tool3
model: sonnet
---

System prompt content here.
```

**Required Fields**:
- `name`: Must use lowercase letters and hyphens only
- `description`: Describes when to invoke the subagent

**Optional Fields**:
- `tools`: Comma-separated list of tools (inherits all if omitted)
- `model`: Model configuration (defaults to Sonnet if omitted)

### Invocation Methods

1. **Explicit Invocation** (via natural language):
   - "Use the test-runner subagent to fix failing tests"
   - "Have the code-reviewer subagent look at my recent changes"

2. **Automatic Delegation**:
   - Claude proactively selects appropriate subagents based on task descriptions

3. **Programmatic Invocation** (via Task tool):
   - Task tool "Runs a sub-agent to handle complex, multi-step tasks"
   - Can launch multiple sub-agents for parallel execution

### Best Practices

1. **Single Responsibility**: Create subagents with single, clear responsibilities
2. **Detailed Prompts**: Write specific instructions for domain-specific tasks
3. **Tool Limitation**: Only grant necessary tools to each subagent
4. **Version Control**: Keep project subagents in version control
5. **Iterative Design**: Generate initial subagent with Claude, then iterate

---

## 2. Model Configuration

### Available Models

**Model Aliases**:
- `default` - Recommended based on account type
- `sonnet` - Latest Sonnet model (currently Claude Sonnet 4.5)
- `opus` - Opus 4.1 for complex reasoning
- `haiku` - Fast, efficient option (currently Claude Haiku 4.5)
- `sonnet[1m]` - Sonnet with expanded context window
- `opusplan` - Hybrid approach using Opus then Sonnet
- `inherit` - Uses the main conversation's model

**Full Model Identifiers**:
- `claude-sonnet-4-5-20250929` (Sonnet 4.5)
- `claude-haiku-4-5-20250429` (Haiku 4.5)
- Full model names and Bedrock ARNs also supported

### When to Use Each Model

**Sonnet 4.5** (Recommended for):
- Complex reasoning tasks
- Orchestration and planning
- Decomposition and synthesis
- Validation and quality checks
- Main conversation/orchestrator
- Daily coding tasks with strong performance

**Haiku 4.5** (Recommended for):
- Simple, focused tasks
- Parallel execution of sub-tasks
- High-volume operations (4-5x faster than Sonnet)
- Cost-sensitive workloads ($1/$5 per million tokens vs Sonnet's higher cost)
- Background functionality
- Straightforward operations with clear instructions

**Opus 4.1** (Recommended for):
- Extremely complex reasoning
- Architecture decisions
- Tasks requiring highest capability

### Model Configuration Priority

1. During session: `/model <alias|name>`
2. At startup: `claude --model <alias|name>`
3. Environment variable: `ANTHROPIC_MODEL=<alias|name>`
4. Settings file: `"model": "alias"` in configuration JSON

**For Subagents**: Use `CLAUDE_CODE_SUBAGENT_MODEL` environment variable

### Model Selection Strategy for This Project

**Sonnet 4.5** for:
- Orchestrator (main session) - Complex multi-step coordination
- Decomposer - Deep analysis and breakdown
- Validator - Quality assessment and scoring
- Synthesizer - Complex report generation

**Haiku 4.5** for:
- All 5 Explorer agents - Focused search tasks
- All 4 Miner agents - Specific analysis tasks
- Parallel execution for speed and cost efficiency

---

## 3. Tools Available to Agents

### Core File Operations

**Read**:
- View file contents
- No permission required
- Can read any accessible file

**Write**:
- Create or overwrite files
- Requires permission
- Use for new file creation

**Edit**:
- Make targeted edits to specific files
- Requires permission
- Preferred for modifying existing files

### Search and Discovery

**Glob**:
- Find files based on pattern matching
- No permission required
- Fast pattern-based file discovery

**Grep**:
- Search for patterns in file contents
- No permission required
- Support for regex patterns

### Execution

**Bash**:
- Execute shell commands
- Requires permission
- Access to full system environment

### Web Access

**WebFetch**:
- Fetch content from specified URLs
- Requires permission
- Can retrieve documentation, articles, etc.

**WebSearch**:
- Perform web searches with domain filtering
- Requires permission
- Access to current information

### Agent Orchestration

**Task**:
- Runs sub-agents for complex, multi-step tasks
- No permission required
- Key tool for invoking sub-agents
- Supports parallel execution

**SlashCommand**:
- Run custom slash commands
- Requires permission
- Enables workflow automation

### Organization

**TodoWrite**:
- Create and organize structured task lists
- No permission required
- Track progress and manage workflows

---

## 4. Context Management

### Separate Contexts

- Each subagent operates in its own context window
- Helps preserve main conversation context
- Prevents context pollution from sub-tasks
- All necessary context must be passed explicitly

### Stateless Design

- Subagents should be stateless
- No assumptions about parent context
- All inputs passed explicitly through invocation
- Results returned in structured format

---

## 5. Parallel Execution Pattern

### Multi-Agent Orchestration

Based on model documentation:
- **Orchestrator (Sonnet)** breaks down complex problems into multi-step plans
- **Worker Agents (Haiku)** complete subtasks in parallel
- Orchestrator aggregates results from parallel workers

### Implementation Approach

1. Decompose problem (Sequential - Sonnet)
2. Launch multiple explorers (Parallel - Haiku)
3. Launch multiple miners (Parallel - Haiku)
4. Validate results (Sequential - Sonnet)
5. Synthesize output (Sequential - Sonnet)

### Efficiency Benefits

- 4-5x faster execution with Haiku for parallel tasks
- Significant cost savings (Haiku: $1/$5 vs Sonnet's higher pricing)
- Better resource utilization through parallelization

---

## 6. Output Format Requirements

### Structured Data

- Use JSON for structured outputs from sub-agents
- Include reasoning chains to explain discoveries
- Acknowledge uncertainty rather than hallucinate
- Provide sources and verification

### Communication Protocol

While not explicitly documented, best practice:
- Sub-agents return structured JSON
- Orchestrator parses and validates
- Type-safe data structures
- Clear schema definitions

---

## 7. Error Handling

### Graceful Degradation

- If a sub-agent fails, log error and continue
- Don't block entire workflow on single failure
- Partial results better than complete failure
- Log errors to stderr for debugging

### Progress Reporting

- Show user which stage is running
- Display sub-agent completion status
- Provide final summary statistics
- Track success/failure of each component

---

## 8. Key Insights for Implementation

### Agent Design

1. Each agent should have a single, clear purpose
2. Explicitly define input and output formats (JSON schemas)
3. Include examples in prompts for clarity
4. Limit tools to only what's necessary
5. Use descriptive names with hyphens (e.g., `component-explorer`)

### Orchestration Pattern

1. Sequential stages for dependencies (decompose → explore → mine → validate → synthesize)
2. Parallel execution within stages (5 explorers simultaneously, 4 miners simultaneously)
3. Explicit context passing between stages
4. Structured data formats (JSON) for inter-agent communication

### Model Selection Rationale

- **10 total sub-agents**: 1 Decomposer + 5 Explorers + 4 Miners + 1 Validator + 1 Synthesizer
- **4 Sonnet agents**: Complex reasoning tasks (decompose, validate, synthesize) + orchestrator
- **9 Haiku agents**: Focused parallel tasks (all explorers and miners)
- **Cost/performance balance**: Haiku for parallelizable work, Sonnet for critical reasoning

---

## 9. Technical Implementation Notes

### File Naming Convention

- Use lowercase with hyphens: `component-explorer.md`
- Store in `.claude/agents/` directory
- One agent per file

### YAML Frontmatter Syntax

```yaml
---
name: agent-name          # lowercase-with-hyphens
description: "When to use this agent"
tools: Read, Grep, Bash   # Comma-separated
model: haiku              # alias or full identifier
---
```

### Slash Commands

- Store in `.claude/commands/` directory
- Similar YAML frontmatter structure
- Invoked with `/command-name` syntax

---

## 10. Documentation Sources

All research from official Anthropic/Claude documentation:

1. **Sub-agents**: https://code.claude.com/docs/en/sub-agents
2. **Model Configuration**: https://code.claude.com/docs/en/model-config.md
3. **Settings/Tools**: https://code.claude.com/docs/en/settings.md
4. **Model Comparison**: https://www.anthropic.com/news/claude-haiku-4-5
5. **Claude 3 Family**: https://www-cdn.anthropic.com/de8ba9b01c9ab7cbabf5c33b80b7bbc618857627/Model_Card_Claude_3.pdf

---

## Next Steps

1. ✅ Research complete
2. ✅ Documentation written
3. **Next**: Create decomposer agent (Task 2.1)
4. Then: Create explorer agents (Task 2.2)
5. Then: Create miner agents (Task 2.3)
6. Then: Create validator and synthesizer (Tasks 2.4-2.5)
7. Then: Implement orchestration logic (Task 3.1)
8. Then: Create /discover slash command (Task 3.2)
9. Then: Test and validate (Phase 4)
10. Then: Write final documentation (Phase 5)

---

**Research Phase Complete** ✅
