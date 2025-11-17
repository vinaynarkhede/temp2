# Deep Tech Discovery System - Test Cases

This document defines test cases for validating the Deep Tech Discovery System's functionality, quality, and reliability.

---

## Table of Contents

1. [Test Methodology](#test-methodology)
2. [Test Case 1: Technical Infrastructure](#test-case-1-technical-infrastructure)
3. [Test Case 2: Software Concept](#test-case-2-software-concept)
4. [Test Case 3: Abstract Concept](#test-case-3-abstract-concept)
5. [Edge Cases](#edge-cases)
6. [Performance Tests](#performance-tests)
7. [Quality Validation](#quality-validation)
8. [Regression Tests](#regression-tests)

---

## Test Methodology

### Success Criteria

For each test case, validate:

**Quantitative Metrics**:
- ✅ 15+ discoveries in final report
- ✅ Average obscurity ≥ 0.70
- ✅ Average relevance ≥ 0.65
- ✅ Average source quality ≥ 6.0
- ✅ Execution time ≤ 8 minutes
- ✅ Zero critical failures

**Qualitative Assessment**:
- ✅ Discoveries are non-obvious (not in top Google results)
- ✅ Technical accuracy verified
- ✅ Sources are credible and accessible
- ✅ Reasoning chains are logical
- ✅ Report is well-formatted and readable

### Test Execution Process

1. **Run Discovery**: `/discover "<seed-topic>"`
2. **Monitor Progress**: Observe stage completion
3. **Review Output**: Examine generated report
4. **Validate Metrics**: Check quality scores
5. **Spot-Check Content**: Verify 3-5 discoveries manually
6. **Document Results**: Record findings in this file

---

## Test Case 1: Technical Infrastructure

### Seed Topic

```
"Anycast BGP routing"
```

### Expected Behavior

**Decomposition Should Identify**:
- **Entities**: BGP, routers, Anycast, IP addresses, ASes, routing tables
- **Actions**: advertise, route, select, propagate, converge
- **Properties**: distributed, topology-aware, redundant, policy-based
- **Constraint**: Routing scalability and optimal path selection
- **Principle**: Distributed decision-making with local knowledge converges to global optimum

**Expected Discovery Categories**:
- **Component**: BGP Communities, AS_PATH, Route Reflectors
- **Layer Down**: TCP session management, EBGP vs IBGP, routing algorithms (Dijkstra)
- **Layer Up**: CDN routing strategies, DDoS mitigation patterns
- **Analogical**: Gossip protocols, distributed consensus, swarm routing
- **Temporal**: EGP and BGP history, NSFNET, future (SCION architecture)
- **Constraint**: OSPF/IS-IS as alternatives, centralized SDN routing
- **Security**: BGP hijacking, RPKI, route leaks
- **Economic**: Transit vs peering economics, IX business models
- **Inversion**: BGP route flapping, Great Firewall censorship, Facebook outage 2021
- **Implementation**: Zebra/Quagga/FRRouting, Linux kernel routing, hardware TCAM

**Quality Expectations**:
- Obscurity: 0.75+ (BGP is specialist knowledge)
- Relevance: 0.70+ (strong technical relationships)
- Sources: RFCs (1771, 4271, 7454), academic papers, vendor docs
- Technical Depth: Should include specific protocol details, algorithms

**Success Criteria**:
- ✅ 15+ discoveries
- ✅ Mix of infrastructure (protocols), security (attacks), and economics (peering)
- ✅ At least 3 RFCs referenced
- ✅ Obscurity 0.75+ average

### Test Execution

```bash
/discover "Anycast BGP routing"
```

### Expected Output Sample

```markdown
### Discovery: BGP Route Dampening
**Obscurity**: 0.82 | **Relevance**: 0.78

BGP route dampening suppresses flapping routes to maintain routing stability.
When a route changes state repeatedly (flaps), routers assign penalties.
Routes exceeding a threshold are suppressed (not advertised) until they
stabilize. This prevents global routing table churn.

**Key Insight**: While designed to improve stability, aggressive dampening
can cause reachability issues. RFC 7196 recommends disabling it due to
unintended consequences in modern Internet...
```

---

## Test Case 2: Software Concept

### Seed Topic

```
"Process Hollowing"
```

### Expected Behavior

**Decomposition Should Identify**:
- **Entities**: Process, memory, executable, loader, Windows API, malware
- **Actions**: create, suspend, unmap, allocate, write, resume
- **Properties**: stealthy, legitimate-looking, evasive, injection-based
- **Constraint**: Evading detection while executing malicious code
- **Principle**: Legitimate containers can be repurposed for malicious payloads by replacing internal state

**Expected Discovery Categories**:
- **Component**: Process memory structures, PE format, Windows loader, PEB
- **Layer Down**: Virtual memory management, page tables, memory protection (NX bit)
- **Layer Up**: APT attack chains, multi-stage malware, fileless malware
- **Analogical**: Container escapes, DLL hijacking, parasitic biology
- **Temporal**: Code injection history, modern EDR detection, future (eBPF monitoring)
- **Constraint**: Alternative injection techniques (DLL injection, APC injection, thread hijacking)
- **Security**: Defense techniques (memory scanning, API hooking, behavioral analysis)
- **Economic**: Cost of detection vs evasion, underground malware market
- **Inversion**: Failed evasion attempts, defender case studies
- **Implementation**: Actual API calls (CreateProcess, NtUnmapViewOfSection, VirtualAllocEx, WriteProcessMemory)

**Quality Expectations**:
- Obscurity: 0.70+ (security research level)
- Relevance: 0.75+ (focused on evasion/injection techniques)
- Sources: Security blogs, malware analysis reports, Windows internals docs
- Technical Depth: API call sequences, memory layouts, detection heuristics

**Success Criteria**:
- ✅ 15+ discoveries
- ✅ Strong security focus (attack + defense)
- ✅ Windows internals details
- ✅ Mix of historical context and modern defenses

### Test Execution

```bash
/discover "Process Hollowing"
```

### Expected Output Sample

```markdown
### Discovery: PEB (Process Environment Block) Manipulation
**Obscurity**: 0.87 | **Relevance**: 0.83

After hollowing, attackers often modify the PEB to hide artifacts.
The PEB contains process metadata visible to debuggers and monitoring tools.
Attackers overwrite PEB.ImageBaseAddress and PEB.ProcessParameters to
match the original legitimate executable, evading basic detection...

**Key Insight**: While the main executable is replaced, the PEB still
points to the original path. Sophisticated malware modifies these pointers,
but kernel-level inspection of the actual memory mappings reveals the
discrepancy...
```

---

## Test Case 3: Abstract Concept

### Seed Topic

```
"Eventual Consistency"
```

### Expected Behavior

**Decomposition Should Identify**:
- **Entities**: distributed system, replicas, updates, state, clients
- **Actions**: replicate, propagate, converge, reconcile, resolve conflicts
- **Properties**: available, partition-tolerant, asynchronous, weakly consistent
- **Constraint**: CAP theorem trade-off (availability vs consistency)
- **Principle**: Temporary divergence with guaranteed convergence provides practical balance between availability and consistency

**Expected Discovery Categories**:
- **Component**: Vector clocks, conflict resolution, anti-entropy
- **Layer Down**: Consensus algorithms (Paxos, Raft), gossip protocols, CRDTs
- **Layer Up**: Database architectures (Dynamo, Cassandra), caching strategies
- **Analogical**: Biological immune systems, social consensus, git merges
- **Temporal**: BASE vs ACID history, modern multi-region databases
- **Constraint**: Strong consistency alternatives (Spanner, Calvin), tunable consistency
- **Security**: Byzantine fault tolerance, trust assumptions
- **Economic**: Cost of consistency (latency, availability), business trade-offs
- **Inversion**: Consistency bugs, split-brain scenarios, data loss incidents
- **Implementation**: Vector clocks, Merkle trees, read-repair, hinted handoff

**Quality Expectations**:
- Obscurity: 0.68+ (distributed systems is specialized but well-documented)
- Relevance: 0.70+ (consistency models, distributed systems)
- Sources: Academic papers (Dynamo, Spanner), database documentation
- Technical Depth: Consistency models, conflict resolution algorithms

**Success Criteria**:
- ✅ 15+ discoveries
- ✅ Strong theoretical foundations (CAP, consistency models)
- ✅ Practical implementations (Cassandra, DynamoDB)
- ✅ Cross-domain analogies (biology, social systems)

### Test Execution

```bash
/discover "Eventual Consistency"
```

### Expected Output Sample

```markdown
### Discovery: CRDTs (Conflict-Free Replicated Data Types)
**Obscurity**: 0.79 | **Relevance**: 0.85

CRDTs are data structures that guarantee eventual consistency without
coordination. By designing operations to be commutative, associative,
and idempotent, replicas can merge updates deterministically without
conflicts. Categories include state-based (CvRDT) and operation-based (CmRDT)...

**Key Insight**: CRDTs prove that eventual consistency doesn't require
conflict resolution if you design data structures correctly. They're
used in Redis, Riak, and collaborative editing (Google Docs, Figma)...
```

---

## Edge Cases

### Edge Case 1: Very Narrow Topic

**Seed**: `"Maglev hashing algorithm"`

**Expected Behavior**:
- Should still find 15+ discoveries by going up/down layers, across domains
- Layer down: Hash functions, consistent hashing variants
- Layer up: Load balancer architectures, Anycast routing
- Analogical: Rendezvous hashing, jump hash, ketama
- May struggle with some categories (economics, inversions)

**Success Criteria**:
- ✅ 12+ discoveries (acceptable for very narrow topic)
- ✅ Explores related hashing algorithms and load balancing techniques

### Edge Case 2: Very Broad Topic

**Seed**: `"Machine Learning"`

**Expected Behavior**:
- Decomposer should focus on specific aspects (e.g., gradient descent, training)
- Risk: Discoveries may be too diverse, less coherent
- Agents should find niche subtopics within ML

**Success Criteria**:
- ✅ 15+ discoveries
- ✅ Obscurity 0.65+ (harder to avoid common knowledge for broad topics)
- ✅ Focused on non-obvious techniques (not "neural networks 101")

### Edge Case 3: Ambiguous Topic

**Seed**: `"Consensus"`

**Expected Behavior**:
- Could mean distributed systems consensus OR social consensus
- Decomposer should disambiguate based on technical context
- Likely interprets as distributed systems (technical bias)

**Success Criteria**:
- ✅ 15+ discoveries
- ✅ Primarily technical interpretations (Paxos, Raft, Byzantine fault tolerance)

### Edge Case 4: Non-English Topic

**Seed**: `"Paxos consensus"` (uses Greek mythology name)

**Expected Behavior**:
- Should work fine (English-based sources)
- May find historical context (Leslie Lamport's Greek island metaphor)

**Success Criteria**:
- ✅ 15+ discoveries
- ✅ Technical accuracy maintained

---

## Performance Tests

### Performance Test 1: Execution Time

**Objective**: Verify system completes within acceptable time.

**Test**:
```bash
time /discover "CDN edge computing"
```

**Expected**:
- ✅ Stage 1 (Decomposition): 30-90s
- ✅ Stage 2 (Exploration): 90-150s
- ✅ Stage 3 (Mining): 90-150s
- ✅ Stage 4 (Validation): 30-60s
- ✅ Stage 5 (Synthesis): 30-60s
- ✅ **Total**: 4-8 minutes

**Failure Criteria**:
- ❌ Any stage > 5 minutes
- ❌ Total > 10 minutes

### Performance Test 2: Parallel Efficiency

**Objective**: Verify parallel execution provides speedup.

**Test**: Compare sequential vs parallel agent execution.

**Theoretical**:
- Sequential: 9 agents × 60s = 540s = 9 minutes (for stages 2+3)
- Parallel: max(5 agents) + max(4 agents) = 120s = 2 minutes

**Expected Speedup**: ~4-5x

**Measurement**: Monitor Stage 2 and Stage 3 durations.

---

## Quality Validation

### Quality Test 1: Obscurity Validation

**Objective**: Verify discoveries are genuinely obscure.

**Test**:
1. Run `/discover "Cloudflare unlimited bandwidth"`
2. Select 5 random discoveries
3. For each, Google `"discovery topic" basics`
4. Count how many are in top 10 Google results

**Success Criteria**:
- ✅ ≤1 out of 5 in top 10 results (indicates obscurity)
- ✅ Average obscurity score matches manual assessment

### Quality Test 2: Source Verification

**Objective**: Verify sources are credible and accessible.

**Test**:
1. Run `/discover "Anycast BGP routing"`
2. Check all sources in final report
3. Verify HTTP status (200 OK)
4. Assess source quality

**Success Criteria**:
- ✅ ≥90% of sources return 200 OK (not dead links)
- ✅ ≥70% of sources are official docs, RFCs, or papers (high quality)

### Quality Test 3: Technical Accuracy

**Objective**: Verify technical claims are correct.

**Test**:
1. Run `/discover "Process Hollowing"`
2. Select 3 implementation-focused discoveries
3. Cross-reference claims with official documentation
4. Check for technical errors

**Success Criteria**:
- ✅ Zero major technical errors
- ✅ Minor inaccuracies (if any) are flagged by validator

### Quality Test 4: Relevance Validation

**Objective**: Verify discoveries are meaningfully related to seed.

**Test**:
1. Run `/discover "Eventual Consistency"`
2. Review reasoning chains for all discoveries
3. Assess logical flow from seed → discovery

**Success Criteria**:
- ✅ 100% of discoveries have clear connection
- ✅ Reasoning chains are logical (no gaps)
- ✅ Average relevance ≥ 0.65

---

## Regression Tests

### Regression Test 1: Agent Output Format

**Objective**: Ensure all agents return valid JSON.

**Test**:
```bash
/discover "test topic" && verify all agent outputs parse as JSON
```

**Success Criteria**:
- ✅ All agents return valid JSON
- ✅ No parsing errors in orchestrator logs

### Regression Test 2: Validator Filtering

**Objective**: Ensure validator properly filters low-quality discoveries.

**Test**:
1. Run `/discover` on any topic
2. Check validation summary
3. Verify rejection reasons

**Success Criteria**:
- ✅ 20-40% rejection rate
- ✅ All rejected discoveries have valid reasons
- ✅ Rejected discoveries have obscurity <0.6 OR relevance <0.5

### Regression Test 3: Error Handling

**Objective**: Verify graceful degradation on agent failures.

**Test**: Manually inject agent failure (modify agent to return invalid JSON).

**Expected Behavior**:
- ✅ System logs error
- ✅ System continues with remaining agents
- ✅ Final report includes discoveries from successful agents
- ✅ User is informed of partial results

---

## Test Results Log

### Test Run 1: [Date]

**Test Case**: Technical Infrastructure - "Anycast BGP routing"

**Results**:
- Discoveries: [count]
- Avg Obscurity: [score]
- Avg Relevance: [score]
- Execution Time: [time]
- Pass/Fail: [status]

**Notes**: [Any observations]

---

### Test Run 2: [Date]

**Test Case**: Software Concept - "Process Hollowing"

**Results**:
- Discoveries: [count]
- Avg Obscurity: [score]
- Avg Relevance: [score]
- Execution Time: [time]
- Pass/Fail: [status]

**Notes**: [Any observations]

---

### Test Run 3: [Date]

**Test Case**: Abstract Concept - "Eventual Consistency"

**Results**:
- Discoveries: [count]
- Avg Obscurity: [score]
- Avg Relevance: [score]
- Execution Time: [time]
- Pass/Fail: [status]

**Notes**: [Any observations]

---

## Continuous Testing

### Recommended Test Schedule

**Before Each Release**:
- Run all 3 primary test cases
- Run all quality validation tests
- Run regression tests

**Weekly**:
- Run 2-3 edge case tests
- Performance benchmarking

**Monthly**:
- Full test suite execution
- Source link validation (check for dead links)
- Obscurity recalibration (as knowledge spreads)

---

## Test Automation (Future)

### Potential Automation

```python
# Pseudocode for automated testing

def run_test_suite():
    test_cases = [
        ("Anycast BGP routing", "infrastructure"),
        ("Process Hollowing", "security"),
        ("Eventual Consistency", "distributed_systems")
    ]

    for seed, category in test_cases:
        result = run_discovery(seed)

        assert result.discovery_count >= 15
        assert result.avg_obscurity >= 0.70
        assert result.avg_relevance >= 0.65
        assert result.execution_time <= 480  # 8 minutes

        validate_sources(result.sources)
        validate_technical_accuracy(result.discoveries)

    print("All tests passed!")
```

---

## Conclusion

This test suite ensures the Deep Tech Discovery System maintains high quality, reliability, and performance across diverse topics and conditions.

**Next Steps**:
1. Execute Test Cases 1-3
2. Document results in Test Results Log
3. Address any failures
4. Iterate on agent prompts if quality issues found
5. Establish regular testing cadence
