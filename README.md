# Deep Tech Discovery System

> **Uncover the iceberg below**: An agentic system for discovering obscure technical knowledge through multi-agent exploration.

## Overview

The Deep Tech Discovery System is a sophisticated multi-agent pipeline built with Claude Code that takes a technical seed topic (e.g., "Cloudflare unlimited bandwidth") and systematically discovers 15-20 non-obvious, obscure related technologies through intelligent exploration.

### What Makes This Special?

**The Problem**: When learning about complex technologies, we typically only scratch the surface. Marketing materials highlight features, documentation explains usage, but the deep technical knowledge - the algorithms, trade-offs, historical context, security implications, and economic forces - remains hidden.

**The Solution**: This system employs 10 specialized AI agents that systematically explore a topic from multiple angles:
- Component analysis
- Abstraction layer traversal
- Cross-domain pattern recognition
- Temporal analysis (history → future)
- Constraint-based exploration
- Security implications
- Economic forces
- Failure modes and anti-patterns
- Implementation details

**The Result**: A comprehensive discovery report revealing expert-level knowledge that most developers don't know exists.

---

## Quick Start

### Installation

No installation needed! This system runs entirely within Claude Code.

### Prerequisites

- [Claude Code](https://claude.com/claude-code) installed
- This project checked out locally
- Internet connection (for web searches and source verification)

### Basic Usage

```bash
# Navigate to project directory
cd /path/to/tech-discovery-system

# Run discovery on a topic
/discover "Cloudflare unlimited bandwidth"

# View the output
cat data/discoveries/cloudflare-unlimited-bandwidth-*.md
```

### That's It!

The system will:
1. Decompose your topic (30-60s)
2. Explore in parallel with 5 agents (90-120s)
3. Mine obscure details with 4 agents (90-120s)
4. Validate findings (30-45s)
5. Synthesize comprehensive report (30-45s)

**Total time**: 4-6 minutes
**Output**: 15-20 validated discoveries with sources

---

## Example Topics

### Infrastructure & Networking
```bash
/discover "Anycast BGP routing"
/discover "QUIC protocol"
/discover "DNS over HTTPS"
/discover "Internet Exchange Points"
```

### Security & Privacy
```bash
/discover "Process Hollowing"
/discover "Zero-knowledge proofs"
/discover "Differential privacy"
/discover "Side-channel attacks"
```

### Distributed Systems
```bash
/discover "Eventual Consistency"
/discover "Raft consensus"
/discover "CRDT data structures"
/discover "Distributed transactions"
```

### Algorithms & Data Structures
```bash
/discover "Consistent hashing"
/discover "Bloom filters"
/discover "LSM trees"
/discover "Skip lists"
```

---

## System Architecture

### High-Level Design

```
┌─────────────────────────────────────────────────────────────┐
│                     ORCHESTRATOR                            │
│                   (Main Session - Sonnet 4.5)               │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
        ┌──────────────────────────────────────┐
        │    STAGE 1: DECOMPOSITION            │
        │    Decomposer Agent (Sonnet 4.5)     │
        │    → Components, Constraints, Why's   │
        └──────────────────────────────────────┘
                              │
                              ▼
        ┌──────────────────────────────────────┐
        │    STAGE 2: PARALLEL EXPLORATION     │
        │    5 Agents (Haiku 4.5 each)         │
        ├──────────────────────────────────────┤
        │  • Component Explorer                │
        │  • Layer Traverser                   │
        │  • Analogical Reasoner               │
        │  • Temporal Analyzer                 │
        │  • Constraint Explorer               │
        └──────────────────────────────────────┘
                              │
                              ▼
        ┌──────────────────────────────────────┐
        │    STAGE 3: PARALLEL MINING          │
        │    4 Agents (Haiku 4.5 each)         │
        ├──────────────────────────────────────┤
        │  • Security Miner                    │
        │  • Economic Analyzer                 │
        │  • Inversion Thinker                 │
        │  • Implementation Detective          │
        └──────────────────────────────────────┘
                              │
                              ▼
        ┌──────────────────────────────────────┐
        │    STAGE 4: VALIDATION               │
        │    Validator Agent (Sonnet 4.5)      │
        │    → Quality, Obscurity, Relevance   │
        └──────────────────────────────────────┘
                              │
                              ▼
        ┌──────────────────────────────────────┐
        │    STAGE 5: SYNTHESIS                │
        │    Synthesizer Agent (Sonnet 4.5)    │
        │    → Final Report (Markdown)         │
        └──────────────────────────────────────┘
```

### Agent Responsibilities

| Agent | Model | Purpose | Output |
|-------|-------|---------|--------|
| **Decomposer** | Sonnet 4.5 | Break down seed topic into components, constraints, principles | Structured JSON |
| **Component Explorer** | Haiku 4.5 | Find related tech through component analysis | 3-5 discoveries |
| **Layer Traverser** | Haiku 4.5 | Navigate abstraction layers (up/down stack) | 3-5 discoveries |
| **Analogical Reasoner** | Haiku 4.5 | Find cross-domain patterns (biology, physics, economics) | 3-5 discoveries |
| **Temporal Analyzer** | Haiku 4.5 | Trace evolution (history → present → future) | 3-5 discoveries |
| **Constraint Explorer** | Haiku 4.5 | Find alternative solutions to same constraint | 3-5 discoveries |
| **Security Miner** | Haiku 4.5 | Uncover attack vectors, vulnerabilities, defenses | 3-5 discoveries |
| **Economic Analyzer** | Haiku 4.5 | Reveal business models, incentives, market dynamics | 3-5 discoveries |
| **Inversion Thinker** | Haiku 4.5 | Discover through opposites, failures, anti-patterns | 3-5 discoveries |
| **Implementation Detective** | Haiku 4.5 | Find actual algorithms, architectures, tricks | 3-5 discoveries |
| **Validator** | Sonnet 4.5 | Score and filter discoveries for quality | Validated subset |
| **Synthesizer** | Sonnet 4.5 | Create comprehensive markdown report | Final report |

---

## Model Selection Rationale

### Why Sonnet 4.5 for Orchestrator & Key Agents?

**Orchestrator** (Main Session):
- Complex multi-agent coordination
- Data aggregation and flow control
- Error handling and recovery

**Decomposer**:
- Deep analytical reasoning required
- "5 Whys" analysis needs sophisticated thinking
- Foundation for all downstream agents

**Validator**:
- Nuanced quality assessment
- Subjective scoring of obscurity and relevance
- Technical accuracy verification

**Synthesizer**:
- Complex report generation
- Pattern identification across discoveries
- Professional writing quality

### Why Haiku 4.5 for Explorers & Miners?

**Speed**: 4-5x faster than Sonnet
- Parallel execution of 9 agents completes in ~2-3 minutes
- With Sonnet: Would take 8-12 minutes

**Cost**: $1/$5 per million tokens vs higher Sonnet pricing
- Significant savings on bulk operations
- Each discovery run uses ~100K-200K tokens across all agents

**Quality**: Sufficient for focused, well-defined tasks
- Each agent has clear instructions and output format
- Tasks don't require frontier-level reasoning
- Haiku 4.5 handles structured output excellently

**Trade-off Analysis**:
- Lost: ~5-10% quality vs Sonnet for each agent
- Gained: 4-5x speed, major cost savings
- Net Result: More discoveries in less time for less cost

---

## Quality Metrics

### Obscurity Scoring

**0.0-0.3: Common Knowledge (REJECTED)**
- Undergraduate CS curriculum
- Mainstream tech media
- Known by typical developers

**0.4-0.6: Somewhat Known (BORDERLINE)**
- Known by specialists
- Advanced courses/certifications
- Technical documentation

**0.7-0.8: Obscure (TARGET - GOOD)**
- Known by experts only
- RFCs, papers, source code
- Specialist circles only

**0.9-1.0: Very Obscure (EXCELLENT)**
- Researchers/practitioners only
- Academic papers, internal systems
- Deep domain expertise required

### Relevance Scoring

**0.0-0.4: Weakly Related (REJECTED)**
- Tangential connection
- Logical gaps in reasoning chain
- Forced relationship

**0.5-0.7: Related (ACCEPTABLE)**
- Clear connection to seed
- Logical reasoning chain
- Meaningful context

**0.8-1.0: Strongly Related (EXCELLENT)**
- Direct technical relationship
- Compelling reasoning chain
- Deep insights provided

### Source Quality

| Score | Type | Examples |
|-------|------|----------|
| 10/10 | Primary Research | RFCs, peer-reviewed papers, specs |
| 8/10 | Official Docs | Vendor docs, official blogs, conference talks |
| 5-6/10 | Technical Media | Tech blogs, Wikipedia (cited), Stack Overflow |
| 2-3/10 | Low Quality | Forums, uncited blogs, marketing |
| 0-1/10 | Unacceptable | No sources, dead links, rumors |

---

## Project Structure

```
tech-discovery-system/
├── .claude/
│   ├── agents/                    # Sub-agent definitions
│   │   ├── decomposer.md          # Sonnet 4.5 - Deep analysis
│   │   ├── component-explorer.md  # Haiku 4.5 - Component analysis
│   │   ├── layer-traverser.md     # Haiku 4.5 - Layer traversal
│   │   ├── analogical-reasoner.md # Haiku 4.5 - Cross-domain patterns
│   │   ├── temporal-analyzer.md   # Haiku 4.5 - Historical/future
│   │   ├── constraint-explorer.md # Haiku 4.5 - Alternative solutions
│   │   ├── security-miner.md      # Haiku 4.5 - Security implications
│   │   ├── economic-analyzer.md   # Haiku 4.5 - Business/economics
│   │   ├── inversion-thinker.md   # Haiku 4.5 - Inversions/failures
│   │   ├── implementation-detective.md  # Haiku 4.5 - Implementation
│   │   ├── validator.md           # Sonnet 4.5 - Quality validation
│   │   └── synthesizer.md         # Sonnet 4.5 - Report generation
│   └── commands/
│       └── discover.md            # Slash command definition
├── data/
│   └── discoveries/               # Output reports
│       └── [seed]-[timestamp].md  # Generated reports
├── docs/
│   ├── RESEARCH_NOTES.md          # Documentation research
│   ├── ARCHITECTURE.md            # System design
│   ├── TEST_CASES.md              # Test scenarios
│   └── EXAMPLE_OUTPUT.md          # Sample report
├── CLAUDE.md                      # Project specification
└── README.md                      # This file
```

---

## Example Output

### Discovery Entry Format

```markdown
### Discovery 3: Maglev Consistent Hashing Algorithm

**Obscurity**: 0.84 | **Relevance**: 0.78 | **Quality**: 9/10

**Summary**: Google's production load balancing algorithm achieving optimal
distribution with minimal disruption during backend changes.

**Connection to Seed**:
Cloudflare's unlimited bandwidth relies on efficient load distribution. Within
each Point of Presence (POP), Anycast brings traffic in, but Maglev distributes
it across backend servers with near-perfect load balancing.

**Reasoning Chain**:
1. CDN edge servers use Anycast to route traffic to nearest POP
2. Within a POP, traffic must be distributed across backend servers
3. Standard consistent hashing causes poor load distribution
4. Google developed Maglev for even distribution with minimal disruption
5. Maglev is the algorithm actually used in production load balancers

**Key Insight**:
The algorithm uses a clever lookup table construction where each backend claims
positions in a shuffled order. This achieves three properties simultaneously:
even distribution, minimal disruption (only ~1/N connections remapped), and
O(1) lookup time. This is provably optimal for consistent hashing.

**Sources**:
- [Maglev: A Fast and Reliable Software Network Load Balancer](https://research.google/pubs/pub44824/)
- [Maglev Open Source Implementation](https://github.com/google/maglev)

**Further Reading**:
- Jump hash (even simpler consistent hashing)
- Rendezvous hashing (HRW) alternative
- ECMP and flow-based load balancing
```

---

## Development

### Adding New Agents

1. Create agent file in `.claude/agents/[name].md`
2. Define YAML frontmatter (name, description, tools, model)
3. Write detailed prompt with input/output formats
4. Update orchestration logic to invoke new agent
5. Test with sample topics

### Modifying Existing Agents

1. Edit agent file in `.claude/agents/`
2. Adjust prompt, output format, or guidelines
3. Test changes with `/discover`
4. Verify output quality

### Customizing Workflows

Edit `.claude/commands/discover.md` to:
- Change agent invocation order
- Add/remove stages
- Modify error handling
- Adjust output format

---

## Performance & Cost

### Typical Run

**Time**: 4-6 minutes
- Decomposition: 30-60s
- Exploration (5 parallel): 90-120s
- Mining (4 parallel): 90-120s
- Validation: 30-45s
- Synthesis: 30-45s

**Cost**: ~$0.10-0.30 per discovery run
- Sonnet agents: ~50K tokens total
- Haiku agents: ~150K tokens total
- Token costs vary by model pricing

**Output**: 15-20 validated discoveries

### Optimization Tips

1. **Use Haiku for Parallelizable Work**: Already optimized
2. **Cache Common Decompositions**: Reuse for related topics
3. **Adjust Agent Counts**: Reduce from 5+4 to 3+2 for faster runs
4. **Filter Early**: Stricter validation criteria = fewer final discoveries but faster

---

## Troubleshooting

### Common Issues

**Issue**: "Agent failed to return valid JSON"
- **Cause**: Malformed JSON in agent output
- **Fix**: Check agent prompt output format requirements

**Issue**: "No discoveries passed validation"
- **Cause**: Topic too niche or agents found only common knowledge
- **Fix**: Try broader/different topic, or lower validation thresholds

**Issue**: "Timeout during parallel execution"
- **Cause**: Network issues or overloaded API
- **Fix**: Retry command, check internet connection

**Issue**: "Sources return 404"
- **Cause**: Links changed or removed
- **Fix**: Web content changes over time, use alternative sources

---

## Limitations

### Current Limitations

1. **Knowledge Cutoff**: January 2025 - no real-time information
2. **Source Availability**: Some sources require subscriptions/access
3. **Domain Dependent**: Works best for technical topics with rich literature
4. **Language**: English sources primarily
5. **Execution Time**: 4-6 minutes per run (not real-time)

### Known Issues

1. Some agents may occasionally return incomplete JSON
2. Validation can be conservative (false negatives)
3. Web searches may return outdated pages
4. Synthesis quality depends on discovery quality

---

## Roadmap

### Planned Enhancements

- [ ] **Caching Layer**: Cache decompositions and common discoveries
- [ ] **Incremental Discovery**: Build on previous discoveries
- [ ] **Interactive Mode**: Ask questions during discovery process
- [ ] **Multi-Language**: Support for non-English sources
- [ ] **Visual Output**: Generate knowledge graphs and diagrams
- [ ] **Export Formats**: PDF, JSON, HTML outputs
- [ ] **Collaborative Discovery**: Share and merge discoveries across users

---

## Contributing

This project is a demonstration of multi-agent orchestration with Claude Code. Contributions welcome!

### How to Contribute

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with `/discover` command
5. Submit pull request with examples

### Ideas for Contributions

- New agent types (e.g., "Legal Implications", "Environmental Impact")
- Alternative validation strategies
- Output format improvements
- Performance optimizations
- Documentation improvements

---

## License

[Your License Here - e.g., MIT, Apache 2.0]

---

## Acknowledgments

- Built with [Claude Code](https://claude.com/claude-code) by Anthropic
- Inspired by the principle: "Invert, always invert" (Carl Jacobi)
- Designed to reveal the "iceberg below" - hidden technical knowledge

---

## Contact

[Your contact information]

---

**Ready to discover the hidden depths of technology? Run `/discover <your-topic>` now!**
