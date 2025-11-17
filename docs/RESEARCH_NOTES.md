# RESEARCH NOTES - Deep Tech Discovery System

## Date: 2025-11-17

## Objective
Discover 500+ obscure technical topics across interview prep domains using systematic multi-agent exploration.

## Research Strategy

### Approach: Parallel Batch Discovery
Instead of building complete 10-agent system first (would delay discoveries), using Claude Code's Task tool with Explore agents directly.

### Discovery Methodology

For each seed topic, explore through:

1. **Decomposition**: Break into atomic components
2. **Layer Traversal**: Implementation details + higher abstractions
3. **Cross-Domain Patterns**: Similar structures elsewhere
4. **Constraint Analysis**: What problem solved, what's violated
5. **Security Implications**: Attack vectors, vulnerabilities
6. **Economic Rationale**: Why exists, trade-offs
7. **Failure Modes**: Anti-patterns, edge cases
8. **Implementation Details**: How actually built

### Quality Thresholds

- **Obscurity ≥ 0.7**: Only topics experts know
- **Relevance ≥ 0.5**: Must relate to seed topic
- **Reasoning Chain**: Clear path from seed to discovery
- **No Hallucination**: Based on real CS concepts

### Domain Allocation

| Domain | % | Target Discoveries | Seed Topics |
|--------|---|-------------------|-------------|
| DSA | 40% | 200 | Arrays, Strings, Trees, Graphs, DP |
| DBMS | 15% | 75 | Indexing, Transactions, Query optimization |
| OS | 10% | 50 | Concurrency, Memory, Scheduling |
| Networks | 10% | 50 | TCP/IP, HTTP, Security |
| LLD | 20% | 100 | Design patterns, Architecture |
| **Total** | | **575** | |

### Execution Batches

**Batch 1 - DSA Arrays/Strings (200 discoveries)**
Seeds: Two Pointers, Kadane, Dutch National Flag, KMP, Sliding Window, Prefix Sum, Boyer-Moore, Rolling Hash, Manacher, Z-algorithm

**Batch 2 - DSA Trees/Graphs (100 discoveries)**
Seeds: Morris Traversal, Monotonic Stack, Dijkstra, Union-Find, Topological Sort, Tarjan's SCC

**Batch 3 - DBMS (75 discoveries)**
Seeds: B-Tree, MVCC, WAL, Query Planner, Index-Only Scans

**Batch 4 - OS (50 discoveries)**
Seeds: Copy-on-Write, futex, CFS, Page Cache

**Batch 5 - Networks (50 discoveries)**
Seeds: TCP Fast Open, QUIC, BGP, SYN Cookies

**Batch 6 - LLD (100 discoveries)**
Seeds: CQRS, Event Sourcing, Saga, Circuit Breaker, Bulkhead

## Tools Used

- **Task tool** with `subagent_type=Explore` for parallel discovery
- **Grep/Glob** for codebase pattern searches (if applicable)
- **WebSearch** for verification (when needed)

## Output Format

Each discovery includes:
- Topic name
- Obscurity score (0-1)
- Category
- Seed topic that led to it
- 2-sentence summary
- Reasoning chain (how we got here)
- Key non-obvious insight
- Related rabbit holes

## Notes

- Focusing on interview-relevant obscure knowledge
- Prioritizing "iceberg below" - what experts know but isn't obvious
- Balancing breadth (many topics) with depth (useful insights)

---

**Status**: Ready to begin Batch 1 discoveries
