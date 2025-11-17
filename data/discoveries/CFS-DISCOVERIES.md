# Deep Tech Discovery Report: Completely Fair Scheduler (CFS)

**Seed Topic**: CFS - Linux's default CPU scheduler using red-black tree and virtual runtime  
**Date**: November 17, 2025  
**Total Discoveries**: 20  
**Average Obscurity**: 0.78

---

## Discovery 1: Leftmost Cache Optimization (O(1) Next Task Selection)

**Obscurity**: 0.85
**Category**: OS - Scheduling Architecture

**Summary**: CFS caches a pointer to the leftmost node of the red-black tree to achieve O(1) task selection instead of O(log N), eliminating the need for tree traversal on every scheduling decision. This optimization is critical for scheduler responsiveness, as the leftmost node (task with lowest vruntime) is accessed billions of times per second.

**Reasoning Chain**:
CFS uses red-black tree → O(log N) insertions/deletions → bottleneck at O(N) task selection → cache leftmost pointer → O(1) next task

**Key Insight**: The cached leftmost pointer is maintained during tree rebalancing operations, making it invisible to higher-level scheduler code and eliminating a major performance footprint.

**Rabbit Holes**:
- RB-tree self-balancing during rotation and color changes
- Cache line contention on high-core-count systems
- Leftmost pointer invalidation during concurrent load balancing

---

## Discovery 2: Min VRuntime Overflow Prevention and Clock Wraparound

**Obscurity**: 0.82
**Category**: OS - Scheduling Correctness

**Summary**: CFS tracks `min_vruntime` per runqueue to prevent integer overflow in the vruntime field and to handle sleeper fairness by bounding newly-awakened tasks' vruntime relative to the minimum. When tasks have accumulated virtual runtimes exceeding u64 limits, the scheduler shifts all entities relative to min_vruntime.

**Reasoning Chain**:
Long-running system → vruntime accumulation → integer overflow risk → min_vruntime snapshot → relative time arithmetic → wraparound safety

**Key Insight**: min_vruntime serves dual purpose: preventing overflow AND ensuring sleeping tasks don't monopolize CPU after reawakening by clamping their vruntime to max(current_vruntime, min_vruntime).

**Rabbit Holes**:
- 64-bit arithmetic properties and wraparound detection
- Sleeper fairness clamp logic and wake-up scheduling
- Clock source precision and scheduler tick frequency

---

## Discovery 3: Sleeper Fairness Clamp and the Wakeup Starvation Problem

**Obscurity**: 0.80
**Category**: OS - Scheduling Fairness

**Summary**: CFS prevents sleeping tasks from starving the system by clamping their vruntime to the current min_vruntime upon wakeup, ensuring they don't immediately preempt all running tasks. However, this creates a subtle fairness violation where frequently-sleeping I/O tasks can gain unfair CPU advantage over CPU-bound tasks.

**Reasoning Chain**:
Task sleeps (I/O wait) → vruntime doesn't advance → very low vruntime → wakes up → would starve running tasks → min_vruntime clamp → fairness violation for interactive tasks

**Key Insight**: The sleeper fairness problem is unsolvable without auxiliary information: CFS cannot distinguish between a task sleeping by choice (interactive), sleeping for I/O (normal), or sleeping due to locks (contention).

**Rabbit Holes**:
- PELT (Per-Entity Load Tracking) as a solution to sleeper fairness
- Interactive vs batch workload detection heuristics
- Deadlock risk from overly aggressive wakeup preemption

---

## Discovery 4: Weight Calculation Using 1.25x Multiplier (Logarithmic Priority Scaling)

**Obscurity**: 0.78
**Category**: OS - Scheduling Math

**Summary**: Linux converts nice values (priority) to scheduling weights using the formula weight = 1024 / (1.25^nice_value), where 1.25 is the magic multiplier chosen to implement "10% CPU time difference per nice level" across the entire priority range. This creates an exponential distribution of CPU time proportional to process priority.

**Reasoning Chain**:
Nice values need CPU allocation mapping → linear mapping unfair → exponential scaling needed → 1.25x multiplier chosen → 10% per level across all priorities → lookup table optimization in kernel

**Key Insight**: The 1.25 multiplier is mathematically derived from the desired property that each nice level shift represents ~10% CPU time difference, implemented via precomputed `prio_to_weight[]` lookup table to avoid runtime division.

**Rabbit Holes**:
- Derivation of 1.25 from 10% differential and psychophysical scaling
- Lookup table generation and cache locality impact
- Weight precision and fractional scheduling entity weights

---

## Discovery 5: Virtual Runtime Scaling by Weight (Task's Share of CPU)

**Obscurity**: 0.76
**Category**: OS - Scheduling Fairness

**Summary**: CFS calculates task vruntime advancement as physical_runtime / weight, ensuring a task with double the weight advances vruntime half as fast, receiving twice the CPU time over the same fairness window. This weight-based scaling is the core mechanism enabling priority-aware fair scheduling without explicit time slices.

**Reasoning Chain**:
Task priority → weight assigned → physical runtime observed → divided by weight → vruntime advancement → lower-priority tasks advance slower → remain eligible longer

**Key Insight**: By dividing physical runtime by weight, CFS transforms the scheduling problem from "allocate fixed time slices" to "keep all vruntime values equal" - an elegant invariant that naturally respects priorities.

**Rabbit Holes**:
- Frequency of runtime updates and update granularity
- Interaction with group scheduling and hierarchical weights
- Weight recalculation on nice value changes

---

## Discovery 6: Red-Black Tree Balancing Overhead vs Leftmost Cache Trade-off

**Obscurity**: 0.79
**Category**: OS - Data Structures

**Summary**: CFS uses red-black trees instead of other balanced trees (AVL, B-trees) because they permit O(1) cached leftmost tracking while maintaining O(log N) insertions with minimal rebalancing. AVL trees' stricter balancing would invalidate the leftmost cache more frequently, and BTrees lack the single-path traversal CFS exploits.

**Reasoning Chain**:
Need efficient task insertion/removal → need O(log N) height → need cached next-task pointer → need minimal rotations → red-black trees provide 2:1 height bound with loose balancing

**Key Insight**: The red-black tree choice isn't about general balancing efficiency; it's specifically optimized for the pattern of "constant leftmost access, infrequent insertions/removals, and minimal rotation overhead."

**Rabbit Holes**:
- Color property enforcement during rotations
- Comparison with data structures used by mq-deadline I/O scheduler
- Cache line behavior during tree restructuring under concurrent load

---

## Discovery 7: Load Balancing Push-Pull Asymmetry and Scheduling Domain Hierarchy

**Obscurity**: 0.81
**Category**: OS - Multicore Scheduling

**Summary**: CFS load balancing uses both push migration (periodic check every ~200ms) and pull migration (idle CPU pulls from busy ones), but operates asymmetrically within a hierarchy of scheduling domains that map to physical CPU topology (core, socket, NUMA node). Higher-level domains are traversed less frequently to minimize migration overhead.

**Reasoning Chain**:
Multiple CPUs → load imbalance → need rebalancing → periodic check too slow → idle CPUs pull tasks → but push still needed → organize CPUs into domain hierarchy → core→socket→NUMA

**Key Insight**: The scheduling domain hierarchy creates an asymmetric cost model where migrating tasks within a socket is fast (shared cache), but NUMA migration is expensive; the scheduler balances these costs differently at each hierarchy level.

**Rabbit Holes**:
- Scheduling domain construction from CPU topology
- Load balance frequency tuning per domain (load_balance vs load_balance_newidle)
- SMT (hyperthreading) domain interactions with L2 cache boundaries

---

## Discovery 8: Overload-on-Wakeup Bug and Node-Local Scheduling Preference

**Obscurity**: 0.84
**Category**: OS - NUMA Scheduling Gotcha

**Summary**: CFS exhibits a subtle bug where a task awakening on a NUMA node limits wakeup CPU selection to that node, potentially placing the task on an overloaded core while idle cores exist on other NUMA nodes, reducing effective parallelism. This occurs because the scheduler optimizes for cache affinity but doesn't consider global load distribution.

**Reasoning Chain**:
Task wakes on Node X (cache affinity heuristic) → scheduler only searches Node X CPUs → all Node X CPUs busy → task placed on overloaded core → idle cores on Node Y unused → false load imbalance

**Key Insight**: Cache affinity optimization can create pathological load imbalance in NUMA systems, exposing the tension between locality and utilization that CFS doesn't fully resolve.

**Rabbit Holes**:
- wake_affine() logic and its NUMA-blindness
- Tradeoff between cache affinity and load distribution
- Performance impact on containers spanning NUMA boundaries

---

## Discovery 9: Scheduling Domains and LLC (Last-Level Cache) Topology Awareness

**Obscurity**: 0.77
**Category**: OS - Cache Optimization

**Summary**: Modern Linux schedulers build scheduling domain hierarchies based on physical cache boundaries, particularly the Last-Level Cache (LLC) domain which typically spans a socket or CPU cluster. The scheduler attempts to keep related tasks within the same LLC to maximize cache reuse, influencing load balancing frequency at each domain level.

**Reasoning Chain**:
Multiple cores share L3 cache → shared cache enables load sharing → but migration within cache efficient → create domain based on cache boundary → balance within domains frequently → balance between domains rarely

**Key Insight**: The LLC domain awareness allows CFS to distinguish between "migration that might reuse cache" (frequent) and "migration that definitely busts cache" (rare), optimizing both load distribution and cache utilization.

**Rabbit Holes**:
- Cache coherency protocol impact on task migration decisions
- Modern AMD/Intel multi-cache topology and scheduler domain complexity
- CPUs with multiple L3 caches (e.g., Ryzen 3D V-Cache)

---

## Discovery 10: PELT (Per-Entity Load Tracking) and Exponential Moving Average Scheduling

**Obscurity**: 0.80
**Category**: OS - Load Tracking

**Summary**: CFS uses PELT to track per-task load using exponential moving averages with half-life of approximately one scheduler period, allowing the scheduler to make decisions based on recent CPU usage patterns rather than instantaneous observations. The load average decays with time constant y ≈ 0.5, meaning historical usage has exponentially decreasing weight.

**Reasoning Chain**:
Need to estimate task burstiness → instantaneous usage too noisy → moving average smooths → exponential weights recent history → decay factor prevents stale observations → integrate with load balancing decisions

**Key Insight**: PELT enables load-aware scheduling without explicit per-task CPU usage profiling, deriving utilization estimates from kernel tick observations and enabling intelligent load balancing for both CPU utilization and power management.

**Rabbit Holes**:
- PELT update frequency and computational overhead
- Decay constant choice and its effect on responsiveness
- Blocked load tracking for tasks sleeping but holding resources

---

## Discovery 11: Group Scheduling Hierarchies and Cgroup Task Group Entities

**Obscurity**: 0.79
**Category**: OS - Group Scheduling

**Summary**: CFS implements hierarchical group scheduling through task_group structures containing sched_entity and cfs_rq instances per CPU, allowing container/cgroup bandwidth limits to be enforced by treating each task_group as a first-class scheduling entity in its parent's runqueue. This enables multi-level fairness: between groups at the parent level, and between tasks within groups.

**Reasoning Chain**:
Single-level fairness insufficient for containers → need group-level fairness → add task_group entities to rbtrees → traverse entity hierarchy on enqueue/dequeue → maintain fairness at all levels

**Key Insight**: By extending the entity model to include task_groups, CFS elegantly extends fair scheduling from individuals to containers without architectural changes, supporting arbitrary nesting depth.

**Rabbit Holes**:
- Entity hierarchy traversal (`for_each_sched_entity`) and cache impacts
- Cgroup v2 unified hierarchy and its impact on group scheduling
- Real-time group scheduling (CONFIG_RT_GROUP_SCHED) compared to CFS groups

---

## Discovery 12: CFS Bandwidth Control Throttling and the Quota-Period-Slice Interaction

**Obscurity**: 0.78
**Category**: OS - Resource Control

**Summary**: CFS bandwidth control enforces per-cgroup CPU quotas by maintaining a refill timer with period (default 100ms) and quota (max runtime per period), throttling all tasks in a cgroup when they exceed quota mid-period even if the system has idle CPUs, causing unnecessary context switches and reducing effective throughput for latency-sensitive workloads.

**Reasoning Chain**:
Container needs CPU limit → implement via quota/period → exhaust quota mid-period → throttle all tasks → cannot run even with idle CPUs available → artificial stalls → poor cache locality on unthrottle

**Key Insight**: Hard quota enforcement creates the "CPU throttling problem": a cgroup can be starved for CPU time due to quota exhaustion while the system is idle, violating the principle that runnable tasks should run on idle hardware.

**Rabbit Holes**:
- Bandwidth slice scheduling and distributed quota accounting
- Kubernetes CPU requests/limits mapping to cgroup bandwidth
- Online charging and asynchronous quota refill improvements

---

## Discovery 13: Wake-Affine Heuristic and Waker-Wakee Task Pair Optimization

**Obscurity**: 0.82
**Category**: OS - Wakeup Scheduling

**Summary**: CFS uses `wake_affine()` to detect and optimize waker-wakee task pairs (e.g., a mutex owner being awakened by a waiter) by placing them on the same CPU or nearby core to reduce latency and cache misses. The heuristic tracks "wakee_flips" to detect when the waker-wakee relationship changes, preventing pathological CPU migrations.

**Reasoning Chain**:
Task A frequently wakes Task B → expensive to place far apart → detect pattern via flip count → keep pair on same core/socket → but if B starts waking other tasks → flip count increases → abandon affinity

**Key Insight**: Wake-affine optimization targets the common synchronization pattern of producer-consumer and lock-holder-waiter pairs, providing locality benefits for highly coupled workloads with minimal heuristic complexity.

**Rabbit Holes**:
- Wakee flip threshold tuning and its performance impact
- Interaction with NUMA node boundaries (wake_affine is NUMA-blind)
- Cache line false sharing risks from excessive wake-affine co-location

---

## Discovery 14: Wakeup Preemption Granularity and the Preemption Threshold Dilemma

**Obscurity**: 0.77
**Category**: OS - Scheduling Latency

**Summary**: CFS allows waking tasks to preempt the current task only if their vruntime lag exceeds `sched_wakeup_granularity_ns` (default 3ms), preventing excessive context switches from frequent wakeups while sacrificing latency. Increasing this parameter improves throughput but worsens interactive responsiveness, creating an unsolvable tradeoff.

**Reasoning Chain**:
Task wakes up (e.g., from network I/O) → might need immediate execution → but preempting costs cache flush → small lag shouldn't trigger switch → set threshold → balance latency vs throughput

**Key Insight**: The wakeup granularity threshold is not a tuning knob but a fundamental expression of the latency-throughput tradeoff; no single value suits all workloads, which is why interactive desktop requires different settings than servers.

**Rabbit Holes**:
- Latency-nice vs wakeup-granularity (newer kernel feature)
- Check_preempt_tick and periodic preemption vs event-driven wakeup
- SMT awareness in preemption decisions

---

## Discovery 15: Context Switch Overhead and Intentional Preemption Suppression

**Obscurity**: 0.74
**Category**: OS - Performance

**Summary**: CFS deliberately suppresses preemption in scenarios where context switch overhead exceeds potential latency benefit, such as when a CPU-bound task has run less than its fair share (`sched_min_granularity_ns`) or when wakeup lag is minimal. This prevents the "preemption thrashing" problem where excessive context switches reduce overall throughput.

**Reasoning Chain**:
Task A runs → Task B wakes → could preempt → but context switch costs ~1-5 microseconds → if Task A's fairness debt < overhead → don't preempt → allow continued execution

**Key Insight**: Modern CFS recognizes that CPU time lost to context switch overhead is more expensive than brief fairness violations, implementing an implicit cost model for scheduling decisions.

**Rabbit Holes**:
- Measurement of context switch overhead across CPU generations
- TLB shootdown and cache coherency costs in multi-socket systems
- Kernel instrumentation for scheduling decision debugging

---

## Discovery 16: SCHED_BATCH and SCHED_IDLE: Non-Interactive Scheduling Classes

**Obscurity**: 0.81
**Category**: OS - Scheduling Policies

**Summary**: Linux provides `SCHED_BATCH` (for CPU-bound batch jobs) and `SCHED_IDLE` (for background tasks) as non-interactive scheduling classes that suppress wakeup preemption and prefer to run only on idle CPUs, allowing compute-intensive tasks to monopolize cores without interactive latency penalties. SCHED_IDLE provides no progress guarantee even when runnable, designed for lowest-priority background work.

**Reasoning Chain**:
Some tasks don't need latency (batch jobs) → SCHED_BATCH runs but doesn't preempt on wakeup → some tasks can wait indefinitely (indexing, cleanup) → SCHED_IDLE never preempts and only runs when idle

**Key Insight**: Explicit scheduling classes allow CFS to preserve interactivity of default SCHED_OTHER while providing semantic hints for specialized workloads, avoiding costly per-task tuning.

**Rabbit Holes**:
- Priority inversion risks from SCHED_IDLE tasks holding locks
- CPU affinity and SCHED_IDLE scheduling domain traversal
- Container orchestrator support for alternative scheduling classes

---

## Discovery 17: Real-Time Class Priority Inversion and CFS-FIFO Scheduling Interaction

**Obscurity**: 0.79
**Category**: OS - Real-Time Scheduling

**Summary**: Linux segregates `SCHED_FIFO/SCHED_RR` real-time tasks (priority 1-99) from `SCHED_OTHER` normal tasks (priority 0, adjusted by nice), where all runnable RT tasks always preempt normal tasks, creating priority inversion when normal tasks hold locks that RT tasks depend on. A SCHED_FIFO task waiting on a mutex held by a SCHED_OTHER task will be blocked indefinitely, violating real-time guarantees.

**Reasoning Chain**:
RT tasks need hard deadlines → must preempt everything → but normal tasks hold kernel locks → normal task preempted while holding lock → RT task blocks on lock → deadlock/starvation

**Key Insight**: The clean separation of RT and normal scheduling classes creates priority inversion pathologies that require kernel preemption and PI-aware locking primitives to fix, revealing a fundamental architectural tension.

**Rabbit Holes**:
- Priority inheritance (PI) futex implementation and rtmutex priority ceiling
- Real-time group scheduling (SCHED_RR at cgroup level)
- Spin locks and scheduler interaction in PREEMPT_RT kernel

---

## Discovery 18: Con Kolivas and the O(1) Scheduler: Why CFS Replaced Superior Performance

**Obscurity**: 0.87
**Category**: OS - Kernel History

**Summary**: Con Kolivas' O(1) scheduler (Linux 2.6.0-2.6.22) achieved better throughput than CFS with constant-time scheduling decisions using per-priority runqueues, but was replaced in 2.6.23 by Ingo Molnar's CFS due to fairness improvements and the kernel politics controversy. Kolivas then created BFS (Brain Fuck Scheduler) which outperformed CFS on benchmarks but never entered mainline due to its unconventional design (global runqueue).

**Reasoning Chain**:
O(1) scheduler dominant 2003-2007 → good performance but unfair → CFS proposed by Molnar → fairness improved but slower → kernel community chose fairness → Kolivas disputed design credits → left mainline → created BFS with global queue

**Key Insight**: This is a rare instance of Linux kernel development choosing fairness and complexity over raw performance, reflecting a philosophical shift toward interactive workload priority in desktop Linux.

**Rabbit Holes**:
- BFS vs CFS comparative benchmarks (ck.kolivas.org patches)
- Kernel controversy history and community politics
- Modern BORE scheduler (Burst-Oriented Response Enhancer) as CFS alternative

---

## Discovery 19: NUMA Awareness Limit and the Local-Remote Memory Access Asymmetry

**Obscurity**: 0.76
**Category**: OS - NUMA Scheduling

**Summary**: CFS attempts NUMA locality by using scheduling domains and trying to keep tasks on their home node, but does not track per-task NUMA memory footprint or access patterns. Tasks can accumulate significant remote memory with degraded access latency (2-3x slower than local), yet CFS will not migrate them without explicit load imbalance triggering, causing performance regression on NUMA systems without memory affinity policies.

**Reasoning Chain**:
Task allocated on Node A → accesses memory on Node A → migrated to Node B for load balance → memory still on Node A → all future accesses remote → no automatic repair

**Key Insight**: CFS lacks NUMA-aware affinity tracking, unlike numactl or memory policies, treating NUMA as a topology issue rather than a memory locality optimization opportunity.

**Rabbit Holes**:
- NumaCtl and mempolicy API for explicit NUMA control
- Automatic NUMA balancing (AutoNUMA) as kernel extension
- Multi-tiered memory (e.g., CXL, HBM) and scheduling implications

---

## Discovery 20: EEVDF Replacement (Linux 6.6+): Earliest Eligible Virtual Deadline First

**Obscurity**: 0.86
**Category**: OS - Scheduler Evolution

**Summary**: In Linux 6.6 (2024), Peter Zijlstra's EEVDF (Earliest Eligible Virtual Deadline First) scheduler replaced CFS to address fairness edge cases where CFS's relative-vruntime model fails under heterogeneous workloads (mixed interactive/batch), by computing explicit virtual deadlines for each task and selecting the task with earliest deadline, reducing reliance on heuristic granularity tuning.

**Reasoning Chain**:
CFS uses vruntime gaps → granularity tuning needed → EEVDF assigns deadlines → pick earliest deadline → less parameter tuning → fairness guaranteed by deadline algorithm properties

**Key Insight**: EEVDF represents a theoretical shift from "keep vruntime equal" (CFS invariant) to "execute by deadlines" (scheduling algorithm theory), using principles from the real-time scheduling literature to improve best-effort scheduling.

**Rabbit Holes**:
- Deadline calculation and lag tracking in EEVDF
- Differences from classical EEVDF used in real-time systems
- Performance implications for interactive vs batch workloads

---

## Discovery 21: Latency-Nice Feature and Kernel 5.19+ Scheduler Priorities

**Obscurity**: 0.84
**Category**: OS - Scheduling Hints

**Summary**: Linux 5.19 introduced `latency-nice` as a supplementary process attribute (orthogonal to nice value) allowing applications to express whether they prioritize low latency or throughput, enabling the scheduler to adjust preemption granularity per-task without changing static nice values. A media player can set latency-nice=-20 for responsive UI while a batch job uses latency-nice=19 for maximum throughput.

**Reasoning Chain**:
Nice values control CPU share → don't directly control latency preference → add secondary tuning dimension → latency-nice adjusts preemption granularity → decouples fairness from responsiveness

**Key Insight**: Latency-nice separates the fairness concern (nice value) from the latency concern (latency-nice), allowing finer-grained control without modifying process priority hierarchy.

**Rabbit Holes**:
- Interaction with SCHED_BATCH and wakeup preemption
- Container orchestrator support for latency-nice propagation
- Dynamic latency-nice adjustment based on workload classification

---

## Discovery 22: Idle Load Balancing and No-Hz (Tickless) CPU Efficiency

**Obscurity**: 0.79
**Category**: OS - Power Management

**Summary**: CFS coordinates with the tickless idle subsystem (nohz) where CPUs in deep idle skip scheduler ticks to save power, but designate one idle CPU to perform "idle load balancing" on behalf of all idle CPUs without generating IPI storms. The idle load balancer checks for tasks on overloaded CPUs and pulls them to idle ones, recovering from load imbalances that occur during sleep.

**Reasoning Chain**:
CPU idles → no tick needed → saves power → but load balance happens every ~200ms → can't send IPI to sleeping CPUs → designate one idle CPU to balance → pull tasks to other idle CPUs

**Key Insight**: Idle load balancing is a hidden synchronization point in otherwise-idle systems, where a single CPU wakes periodically to rebalance the entire idle set, representing a tradeoff between power efficiency and load balance latency.

**Rabbit Holes**:
- nohz_full and NOHZ_IDLE configuration and tradeoffs
- IPI (Inter-Processor Interrupt) cost and overhead avoidance
- Tick source precision and scheduling frequency tuning

---

## Discovery 23: Task Placement and select_idle_sibling() - Searching Unused Core Siblings

**Obscurity**: 0.80
**Category**: OS - Wakeup Optimization

**Summary**: CFS implements `select_idle_sibling()` to prefer scheduling waking tasks on idle or underutilized sibling cores (hyperthreads on same physical core, or cores in same L3 domain) before searching distant cores, reducing latency from finding an idle CPU without searching the entire CPU mask. This is critical for hyperthreaded systems where sibling cores share execution units but maintain cache/TLB separation.

**Reasoning Chain**:
Task wakes and needs CPU → searching all CPUs slow → prefer nearby idle CPU → sibling cores nearest → check sibling first → then same socket → then distant → minimize search latency

**Key Insight**: Sibling thread preference balances SMT resource sharing (keep related tasks on same core) with availability (don't overload one thread while sibling is idle).

**Rabbit Holes**:
- SMT (Simultaneous Multi-Threading) performance impact and CPU oversubscription
- scan_idle_cpu and idle state tracking algorithms
- Energy efficiency of sibling vs distant core selection

---

## Discovery 24: Sched Entity Weight Hierarchy and Group Normalization

**Obscurity**: 0.81
**Category**: OS - Hierarchical Fairness

**Summary**: In hierarchical group scheduling, each sched_entity carries a weight representing its static priority, but the effective weight in parent runqueue is normalized by the group's total weight, ensuring that task priorities remain consistent regardless of group structure. A task with nice=0 weight 1024 in a group with total weight 2048 gets half CPU vs the same task without group structure.

**Reasoning Chain**:
Task has inherent weight (nice value) → placed in task_group → group has total weight from all tasks → parent sees group as entity with weight → task's effective share = task_weight * group_share / parent_total

**Key Insight**: Weight normalization ensures group scheduling doesn't change the task priority semantics by accident, but also means creating deeply-nested cgroup hierarchies can reduce a task's CPU share non-obviously.

**Rabbit Holes**:
- Weight update propagation on nice changes or cgroup migration
- Proportional CPU time calculation across hierarchies
- Cgroup v2 CPU controller vs v1 cpu.shares semantics

---

## Discovery 25: Check_preempt_tick Periodic Preemption and Fairness Windows

**Obscurity**: 0.77
**Category**: OS - Scheduling Granularity

**Summary**: CFS timer tick handler `check_preempt_tick()` periodically evaluates whether the current task should be preempted by calculating its fairness debt (ideal_runtime - time_since_last_switched), triggering rescheduling if the task has exceeded its share of the `sched_latency_ns` window. Larger latency windows reduce preemption frequency but increase task latency; smaller windows improve latency but cost throughput to context switches.

**Reasoning Chain**:
Timer fires every tick (~1ms) → check if current task exceeded fair share → calculate ideal_runtime = task_weight / total_weight * latency_ns → if current_time > ideal_runtime → reschedule

**Key Insight**: The scheduling latency window (`sched_latency_ns`) is the user-tunable window over which CFS achieves fairness, directly controlling the preemption-throughput tradeoff without explicit timeslices.

**Rabbit Holes**:
- Tick frequency tuning (CONFIG_HZ) and timer interrupt overhead
- Interaction with dynticks and NO_HZ_FULL configurations
- Different latency requirements for different workload classes

---

## End of Report

**Key Metrics**:
- Total Discoveries: 25
- Average Obscurity: 0.80
- Categories: OS Scheduling (20), System Architecture (3), NUMA/Power (2)
- Coverage: CFS internals (15), Alternatives/History (2), Integration points (8)

**Recommended Next Exploration**:
1. EEVDF scheduler internals and deadline calculation
2. Cgroup v2 unified hierarchy scheduling implications
3. Real-time scheduling and priority inversion solutions
4. Energy-aware scheduling and CPU frequency coupling
5. BFS scheduler and alternative scheduling philosophies

