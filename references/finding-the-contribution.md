# Finding the contribution

Read this at stage 2 (and when stage 3 frames compete): you have read the system for decisions and now must pick what the paper argues against, which concept it lifts into the literature, at which layer, under what name, with how much generality, and whether it is one paper or several. It goes deeper than SKILL.md on each of these; it assumes you already have a list of fan-out decisions, departures, removals, and surprising measurements from reading the system.

Opponent choice and concept choice are one decision seen from two sides, and the filter follows from both. The sections below separate them only to make each checkable.

**Contents**
1. Choosing the opponent
2. Stated gap, real gap, and legitimate elevation
3. Generating and testing thesis candidates
4. Kinds of elevated concept: a vocabulary of needs and failure modes
5. When the system fits none of the kinds
6. WHAT vs HOW: what survives, and cutting the layers so it can be lifted
7. Naming
8. Generality claims and scope statements
9. Choosing among competing frames
10. Splitting one system into several papers

---

## 1. Choosing the opponent

### 1.1 Kinds of opponent and what each obliges you to supply

The kind of opponent decides which kind of concept can be elevated and hands you most of the evaluation design. Identify the kind before drafting anything, because each one creates a debt the paper must pay.

| Opponent | Cases | The paper then owes |
|---|---|---|
| A believed tradeoff | PyTorch (usability vs speed); FlashAttention (exact vs fast, under FLOPs accounting); Spanner (2PC too expensive, clocks untrustworthy); TF OSDI (async replication required); Pathways (single- vs multi-controller) | One decision that dissolves the tradeoff, and a measurement of the side that was supposed to lose (PyTorch within 17%; Spanner's commit-wait cost; Pathways' parity) |
| A named incumbent design | TF vs parameter server; Raft vs Paxos; TVM vs Halide; vLLM vs contiguous KV allocation; AlphaEvolve vs FunSearch | A one-sentence delta, ideally with a structural reason the incumbent falls short (Raft: Paxos's single-decree foundation) |
| Inherited assumptions | GFS; Ceph ("shedding design assumptions like allocation lists") | A premise register, each premise paired with its design consequence, with numbers |
| A requirement no one meets | Dynamo (300 ms at 99.9%); Ray (RL's simulate-train-serve loop); Borg | The requirement made measurable before any design appears |
| A missing abstraction | RDD | A definition, a design-space comparison, a stated boundary |
| A wrong metric | FlashAttention (FLOPs vs memory traffic); Dynamo (mean vs tail); vLLM (waste taxonomy) | One result where proxy and outcome come apart |
| A belief about what a method can do | FunSearch (LLMs confabulate); AlphaTensor (human heuristics) | Externally checkable outputs on problems the audience already values |
| A theory-practice gap | Paxos Made Live | A taxonomy of the gap and abstractions extracted from it (MultiOp, epoch numbers) |
| None: a disclosure gap | Borg; Bigtable in part | Access as the novelty, organization by reader concern, quantification throughout |

Two regularities to use when choosing:

- **Tradeoff opponents produce the cleanest papers**, because the frame fixes both the thesis ("X without sacrificing Y") and the decisive experiment (measure Y). If your system has a defended choice that the field thinks costs something, check first whether it can be framed this way.
- **Conjunction gaps are the weakest.** "No existing system has A, B and C" (Ray's three RL legs; Pathways' 2x2) lets the authors pick the legs, and it hides a claim that composing existing systems fails. Ray asserted that stitching specialists was "untenable due to the tight coupling" and never tested it; its building-block experiments showed Ray matching each specialist alone, which is a different claim. If you keep a conjunction frame, the experiment you owe is the composition that fails. If you cannot run it, demote the conjunction to motivation and make what you measured the thesis.

A disclosure gap is legitimate only for a mature system few outsiders have seen, and only with deployment data. A young system that claims "no one has described a system like ours" is borrowing Borg's form without Borg's access.

### 1.2 Where to find the opponent in the material

The opponent is rarely written down as such. Look in places where the team had to argue:

- **Design-doc rationale and rejected alternatives.** What the doc argues against is what a reader believes. Spanner's abandoned early design with multiple Paxos state machines per tablet and GFS's failed attempt to persist chunk locations come from this kind of material.
- **Predecessors the team owns.** Criticizing your own earlier system is cheap and believable: TensorFlow's §2.1 on DistBelief, Ownership on Ray v0.7.
- **Places the docs expect surprise.** PyTorch's docs say "usability over performance" "may be surprising". The surprise marks the belief under attack.
- **The objection the team keeps answering** in issues, reviews, mailing lists, and talks Q&A. A question asked ten times is a belief held by many.
- **Rejected or reviewed earlier submissions.** Reviews state the opponent the community actually holds, sometimes more precisely than the authors do (Raft's 2012 reviewers on understandability).
- **The decision an outsider would call a mistake.** Its defense names the belief it contradicts.

Write the result as one sentence: a belief a competent reader holds that this work shows false or incomplete. If the only sentence available is "system X is slower than ours", you have a benchmark result. Either find the belief that makes X's slowness surprising or interesting, or accept that the paper is a different genre (§9 of this file; stage 3 of SKILL.md).

### 1.3 The straw-believer check

An opponent nobody holds wins every comparison and persuades no one. Run four checks on the sentence you wrote:

1. **Can you cite a believer?** Spanner writes "Some authors have claimed that general two-phase commit is too expensive" and then measures 2PC up to 200 participants (Table 4). TF OSDI states the "commonly held belief that asynchronous replication is required" and runs synchronous training with backup workers. If you cannot point to a paper, a system's documented design, or a widely used default that embodies the belief, treat the opponent as unconfirmed.
2. **Would the believer recognize their view in your sentence?** Rephrasing an incumbent's position into its weakest form is the usual way straw men enter. RDD defines "DSM" as any shared state with fine-grained writes, a category that loses its comparison table cleanly while the live alternative (MapReduce plus caching) goes undiscussed.
3. **Is it the opponent practitioners act on?** The strongest operational alternative, what a skeptical practitioner would use instead, must appear even if only to be scoped out. MapReduce never discussed relational databases, and DeWitt and Stonebraker supplied the comparison three years later. vLLM's gain depends on a baseline that reserves the maximum length per request; a contiguous allocator that grows dynamically is dismissed rather than measured.
4. **Is a weakened version labeled as such?** Using your own old design as a baseline is fine when labeled (Ownership's baselines are named Ray v0.7 variants). Presenting it as the field's state of the art is not.

If a check fails, the fix is usually to strengthen the opponent, not the prose: state the strongest version, measure against it, and let the thesis shrink to what survives. A smaller claim against a real believer beats a larger claim against a category you defined.

---

## 2. Stated gap, real gap, and legitimate elevation

In almost every case the stated gap sits one abstraction level above the real one:

| Case | Real gap (retrospectives, earlier versions, code) | Stated gap |
|---|---|---|
| RDD | MapReduce-on-HDFS reloads data between iterations | Clusters lack a distributed-memory abstraction |
| TVM | Graph rewriting produces fused operators no library has | Performance portability |
| Spanner | Belief that wide-area strong transactions need coordination and that clocks can't be trusted | Bigtable is awkward, Megastore slow |
| Pathways | Single-controller dispatch can match multi-controller SPMD | Future sparse, multi-task workloads |
| Raft | No complete, specified, implementable multi-Paxos | Paxos is hard to understand |
| AlphaTensor | Scale of search over exact discrete decompositions | Search "beyond human intuition" |

Elevation is how an engineering fact becomes a claim others can use. RDD climbed four rungs: performance on two workloads, then data sharing, then a missing abstraction, then the common cause behind the specialized frameworks. Each rung up widens the readership and raises the evidence bill.

**When elevation is legitimate.** Two conditions together: the mechanism actually addresses the higher gap (not just the lower one that motivated it), and the evidence reaches the higher level. RDD's 20x comes mostly from avoiding HDFS reads, parsing, and deserialization, not from lineage; the paper says so in §6.1, and that decomposition is what keeps its elevation credible. Pathways motivated with future workloads and evaluated only current ones, so its real thesis was the one it could measure: parity.

**Procedure.** Write both gaps in one sentence each. Then mark which one each planned experiment reaches. Three outcomes:

- The evaluation reaches the higher gap: keep the elevation.
- It reaches only the lower gap: either lower the stated gap, or add the experiment that reaches the higher one.
- The higher gap is a vision no experiment can reach yet: label it as a vision in its own sentence, and make the measured result the thesis.

Also check the direction of causation in the gap story (GFS's "driven by" workload observations; see `filtering-and-honesty.md` §8). The real gap often contains a load-bearing condition (GFS: the team controlled every client) that belongs in the paper as a scope statement.

Over-elevation and under-elevation are both failures. Over: the concept is bigger than the evidence ("beyond human intuition", "first to unify" untested). Under: the paper never leaves the real gap's level and reads as a catalog of fixes to one deployment (HDFS).

---

## 3. Generating and testing thesis candidates

### 3.1 Routes that generate candidates

Before running the routes, tag your inventory: for each subsystem, the capability it supports, whether it is WHAT (interface, contract, formulation) or HOW (a particular realization), whether it is contribution or environment, and what evidence exists or could be produced. Lines of code are a fact, not a ranking (vLLM: a 273-line block manager carries §4; about 8.4K lines of model executor get one sentence).

Run the routes independently, then look for agreement. Each one has a characteristic blind spot, which is why one route alone is weak.

| Route | What it finds | Blind spot |
|---|---|---|
| Trace the headline capability back to the decision it depends on (Spanner: three features → meaningful timestamps → TrueTime; MapReduce: fault tolerance → safe re-execution → determinism → restriction) | The dependency root, the concept whose removal takes down the most capabilities | Favors the mechanism the team is proudest of; can over-attribute (Spanner credited TrueTime with snapshot reads that rest mainly on multiversioning) |
| Find the decision an outsider would call a mistake, which the team defends (GFS's single master, PyTorch's eager execution, Spanner waiting deliberately) | The reader belief, plus a defense already half-written in design reviews | Can surface contrarian choices that were expedient, not principled |
| Find the distinction engineers use to explain behavior (narrow vs wide dependencies; consistent vs defined; three kinds of KV waste) | A middle-layer vocabulary that explains several mechanisms at once | The distinction may be internal jargon with no reader belief behind it |
| Watch what gets re-cut across versions (TVM v1's four challenges regrouped into two when the learned cost model arrived; Spark's broadcast variables and accumulators gone by 2012) | What keeps being promoted; what keeps being demoted is also a signal | Recent promotions are often the least tested part (TVM's comparison table was built so the proposal loses on no row) |
| Check what the evidence can carry (TVM's clean ablation, Fig. 12; Pathways' parity; RDD's Fig. 11 as the only lineage-isolating result) | The thesis you can actually defend | Can shrink the paper to its most measurable, least interesting claim |
| Tell it aloud to someone without context (Fox: a short talk before writing) and note what they repeat back | What a reader finds new, which authors often misjudge | Listeners repeat what is vivid, not what is load-bearing |

A candidate that turns up through dependency tracing, a contested choice, and team vocabulary is strong. One that turns up only through evidence availability is a fallback thesis; one that turns up only through the talk is a hypothesis to check against the other routes.

### 3.2 Tests, with pass and fail illustrations

| Test | Passes | Fails | What to do on failure |
|---|---|---|---|
| Name removal | "Expose clock uncertainty as an interval and wait it out"; "manage the KV cache as virtual memory"; CRUSH (Ceph appears once, in future work) | "HDFS's architecture"; Borg's thesis needs "Google" | Restate at the WHAT layer; if impossible, the paper is concern-organized or experience-type, and needs a unifying metric or gap taxonomy instead |
| Reimplementation | Hadoop, HDFS, HBase rebuilt from Google papers; OpenEvolve rebuilt AlphaEvolve from a figure and one-liners | Undisclosed parameters (Borg's score function); a closed substrate with no stated contract | Add the one mechanism a rebuilder would need; state the contract the substrate must provide (Pathways' list of PLAQUE's required properties) |
| Refutability | PyTorch's "10% slower acceptable, 100% not"; "HBM accesses determine runtime"; Spanner's commit-wait cost | TF's "flexibility" (case studies only); TVM's "production use inside several major companies" | Put a price, bound, or condition on the claim; a non-performance property needs an exchange rate or a proxy |
| Ancestry | RDD disclaims DryadLINQ's interface; TVM's one-sentence delta over Halide; Dynamo's explicit combination claim | AlphaTensor with no fixed-compute search baseline; HDFS never names GFS as its origin | Name the nearest respected predecessor, credit it, give the delta in one sentence; disclaim what you borrowed |
| Explanatory reach | TrueTime yields three features; paging yields fragmentation, sharing, and swap | PagedAttention-the-kernel explains nothing the block manager doesn't | It is a mechanism, not a thesis; look one step upstream on the dependency chain |
| Isolation | MapReduce: backup tasks off → 44% slower; FlashAttention Fig. 2 | Ray's unification; Pathways' motivating workloads | Design the toggle or decomposition; if none is possible, lower the claim to what the whole-system number supports |
| Layer | Ray's task/actor/futures API | Ray's GCS on the critical path | See §6 |

Weight name removal and reimplementation above the others. A statement without the system's name is what lets a reader who has never seen the codebase grasp the idea; reimplementability is the practical evidence that the idea was transmitted. Refutability and isolation decide whether the claim survives review. Passing all seven shows the candidate is well-formed, not that it is the right one; that is settled by comparing frames (§9).

---

## 4. Kinds of elevated concept: a vocabulary of needs and failure modes

The cases lift at least ten different kinds of thing into the literature. Use this section to ask "how does my concept earn attention, what must I supply for it to be believed, and how do concepts like it get attacked?" Do not use it to pick a box and fill it. Each entry gives why the kind earns attention, what it needs, and how it fails.

**K1. A restriction or removal that buys properties.** MapReduce; RDD; Ceph's CRUSH (placement by function instead of table); FunSearch's fixed skeleton.
- *Earns attention* because the restriction makes a runtime mechanism legal: determinism makes re-execution safe; coarse-grained writes make lineage sufficient for recovery. The system is the existence proof.
- *Needs* an argument that the restriction costs little for a named workload class, and a written boundary (RDD §2.4, "Applications Not Suitable for RDDs").
- *Fails* when the limits are left for others to find (MapReduce never characterized what it could not do; Spark's introduction did), when the strongest alternative inside the restricted space is skipped (relational databases), or when a higher layer later pushes the interface down to a substrate (DataFrames over RDDs).

**K2. An interface that exposes what was hidden.** TrueTime; Bigtable's client-visible locality controls; Dynamo's (N, R, W).
- *Earns attention* by reification: a nuisance (clock skew) becomes a return value that carries rules (start, commit wait), a short proof, and a dial (epsilon) that is also a research agenda. A three-method table can be quoted without the system.
- *Needs* one decision the rest depends on, so features can be rederived from it section by section.
- *Fails* by over-attribution. Spanner credits TrueTime with three features; snapshot reads rest mainly on multiversioning, and Brewer (2017) rescoped TrueTime's role in availability. Memorable interfaces also invite over-reading (TrueTime read as beating CAP).

**K3. An analogy imported from another field.** PagedAttention (OS paging); TensorFlow's Switch/Merge (dynamic dataflow); Omega (optimistic concurrency from databases).
- *Earns attention* because the reader can predict the design and the source field's trust transfers. vLLM publishes the mapping (blocks as pages, tokens as bytes, requests as processes) and reuses OS terms for each mechanism, which saves pages.
- *Needs* a near-isomorphism, an audience that knows the source concept, and named departures (vLLM's all-or-nothing eviction).
- *Fails* when the name sticks to the wrong part (the PagedAttention kernel is 20-26% slower in isolation; the benefit comes from the block manager), or when the analogy distracts from a weak baseline.

**K4. A swapped metric or accounting unit.** FlashAttention (HBM accesses); Borg (cell compaction); Dynamo (99.9th percentile); vLLM (reserved, internal, external waste).
- *Earns attention* because it recasts incumbents as having optimized the wrong quantity, and lets you admit the ingredients are old (FlashAttention: "two well-established techniques"; same blocks as Rabe and Staats, different objective).
- *Needs* one result where proxy and outcome come apart (more FLOPs, less traffic, faster), and a cost model that tracks reality over the claimed range.
- *Fails* where the model stops predicting (past block size ~256 other bottlenecks dominate). The usual outcome is that the metric survives and the algorithm is replaced (FlashAttention-2 changed the loop order).

**K5. A problem formulation.** TensorGame, AssemblyGame; FunSearch's "function space"; Raft's replicated log rather than single value; JAX's PSC observation.
- *Earns attention* because it makes a hard problem tractable for known machinery; the best formulations make correctness structural (a TensorGame that reaches zero is a correct algorithm), which also lets authors keep the generator closed and release results with checkers.
- *Needs* a cheap checker, and an objective that can be swapped to show breadth.
- *Fails* when the formulation survives but the claim that it needed your instrument doesn't (flip graphs, a laptop-scale random walk over the same representation, matched most of AlphaTensor's table within two months), or when a successor demotes it (AlphaEvolve made function space one of four levels; JAX's project abandoned PSC).

**K6. A non-performance property made the design criterion.** Raft (understandability); PyTorch (usability, priced).
- *Earns attention* only if turned into machinery a reader can check: Raft breaks understandability into two techniques, measures it with a proxy (quizzes) and a bias table; PyTorch gives usability an exchange rate and cites its principles as the reason for each mechanism.
- *Needs* a famous incumbent, and conventional evidence that holds the paper up if the soft evidence is discounted (Raft's TLA+ spec, proof, implementation).
- *Fails* when the property is claimed by definition and adoption substitutes for evidence (PyTorch was accepted as "an exposition"; Raft's 2012 version had "essentially no objective evidence").

**K7. A premise register.** GFS (observations, then numbered assumptions); Ceph; Dynamo §2.1.
- *Earns attention* because a messy design reads as a derivation, and readers can decide whether it applies to them. GFS's method aged better than its conclusions because the assumptions were explicit: a reader can see which premise broke.
- *Needs* measured, true premises and the boundary condition everything rests on (GFS never wrote that the team controlled every client).
- *Fails* when premises change (honest but dated), or when premises were written afterward to cover a different cause.

**K8. A synthesis with a requirements vocabulary.** Dynamo ("the main contribution ... is the evaluation of how different techniques can be combined"); PyTorch §2's converging trends.
- *Earns attention* by saying plainly what kind of contribution it is. A problem-technique-advantage table maps the whole before any mechanism; paragraphs follow "textbook version, what breaks here, our variant".
- *Needs* production credibility or a famous gap.
- *Fails* when the mechanisms don't persist (DynamoDB kept little of the architecture), leaving vocabulary as the durable content. Expect the "no one big idea" review and answer it in the contribution-type sentence.

**K9. Organization by reader concern, with a unifying metric.** Borg; much of Bigtable. HDFS is the failed version.
- *Earns attention* for a mature, revealed system whose value is spread across many choices: the reader's questions give the spine; one cross-cutting metric (cell compaction) turns unrelated policies into one cost; benefit sections are paired with cost sections; lessons map to successor designs.
- *Needs* access as part of the novelty, and deployment data.
- *Fails* as HDFS did: organized by component, defaults instead of reasons, the best material buried in the last sections.

**K10. A taxonomy of a gap.** Paxos Made Live; Bigtable §9 and Borg §8 in part.
- *Earns attention* by organizing around why theory missed practice, not around components.
- *Needs* extracted reusable abstractions (MultiOp, epoch numbers) and quantified failures.
- *Fails* into an incident list when nothing is extracted.

Two forms cut across the kinds:

- **Subsumption ("X subsumes Y")**: TensorFlow and parameter servers, RDD and Pregel/HaLoop, Ray and tasks/actors. It is strong when "express" is defined (RDD: capturing the subsumed systems' optimizations, not just their outputs) and built, weak when it reduces to renaming (TensorFlow's "no such thing as a parameter server" still runs PS tasks on the same topology).
- **A delta against a predecessor, ideally one you own**: TensorFlow OSDI against DistBelief's parameter server; AlphaEvolve against FunSearch; Pathways retracing TF v1's limitations. It *needs* a named predecessor and each listed limitation removed by one real change; owning the predecessor makes the criticism cheap and believable (§1.2). It *fails* when the change is a renaming or when most of the delta is scale rather than concept (five of AlphaEvolve's eight comparison rows are scale). `argument-architecture.md` §2 shows the structure it generates.

### 4.1 Combining kinds

Strong papers usually combine two kinds, and the combination is rarely symmetric: one kind supplies the contrast that earns attention, the other supplies the credential that earns belief.

| Paper | Attention | Credential |
|---|---|---|
| Spanner | Exposed interface (TrueTime) | Proof of external consistency, plus commit-wait cost |
| FlashAttention | Swapped metric (IO) | IO-complexity theorem, plus the proxy/outcome split |
| RDD | Restriction | Built subsumption, plus a stated boundary |
| vLLM | Imported analogy (paging) | Waste metric that makes the analogy's benefit measurable |
| Raft | Non-performance criterion | Spec, proof, implementation, user study |

When combining, check that both kinds generate the same spine. If one kind wants sections organized by the features it rederives and the other by reader concerns, you have two papers, or one kind should be demoted to a section. Each kind added also adds its failure modes: an analogy plus a metric can fail on the name (K3) and on the cost model's range (K4) independently.

---

## 5. When the system fits none of the kinds

The kinds are a record of what worked in the case studies behind this skill, not a closed list. If nothing fits, do not stretch the closest kind; derive its needs and failure modes directly by answering these:

1. **What object does a reader carry away and use without your system?** A rule, contract, quantity, formulation, boundary, invariant, vocabulary, design space, or discovery. If you cannot name the object, there is no elevated concept yet; go back to the routes in §3.1.
2. **What does that object change for the reader?** What they can predict, what they may rely on, what they count, what they assume, or what they believe a method can do. (This grouping of the kinds is my representation; it is a prompt, not a taxonomy.) The answer tells you what belief you are writing against.
3. **What would a skeptic need to believe it?** Name the evidence type: a toggle inside the system, a proxy/outcome split, a proof, a checker, telemetry against a stated contract, reimplementation by others. This is the kind's "needs".
4. **What is the cheapest way it could be false, and what is the cheapest alternative that would get the same result?** For a discovery, a simpler search with equal budget; for a mechanism, the design a competent engineer would build in a week without the idea. This is the kind's main failure mode.
5. **Which replaceable part is it tied to?** If the object only makes sense with one implementation detail, it is at the HOW layer (§6).
6. **What is its boundary?** Write the "not suitable for" paragraph now. If you cannot, the concept is not yet understood well enough to claim.

If question 1 has no answer after honest effort, the right outcome may be a concern-organized or experience paper with a unifying metric or gap taxonomy, a documentation-type report that states its lineage and where its assumptions broke (the paper HDFS could have written from its own §IV-V), or a blog and docs rather than a paper. Inventing a concept to fill the slot produces a slogan the evidence cannot carry.

---

## 6. WHAT vs HOW: what survives, and cutting the layers so it can be lifted

Authors often guess wrong about which part of their system will last:

| Case | Elevated | Survived |
|---|---|---|
| Ceph | MDS subtree partitioning | RADOS and CRUSH |
| Spark | RDD as universal, in-memory interface | Lineage; RDD as substrate under DataFrames/SQL |
| Ray | GCS on the critical path | Tasks/actors/futures API |
| Dynamo | Vector clocks, sloppy quorum | SLA framing, predictability, incremental scalability |
| Bigtable | "Single-row transactions suffice" | Data model, SSTable/LSM, lessons |
| TensorFlow | Deferred static graph | Dataflow as IR; eager became default |
| FlashAttention | Algorithm 1's loop order | "IO-aware" as a principle |
| AlphaTensor | "Beyond human intuition" | TensorGame; the diversity result |
| GFS | Single master, record append | The method of stating assumptions |

Specification-layer claims (interfaces, formulations, metrics, requirements, invariants) tend to be robust; implementation-layer commitments (a control plane, a loop order, a quantitative premise) get replaced. Three qualifications keep this from becoming a slogan:

- **A WHAT-only paper is a position paper.** Every surviving concept was believed because a working HOW demonstrated it. Present the HOW as one realization of the WHAT; do not omit it.
- **WHAT claims can be wrong.** TensorFlow's static graph was a WHAT-level choice; the OSDI paper was locally excellent and mispositioned against what drove adoption.
- **You cannot reliably predict the survivor.** You can only make it liftable.

**How to cut the layers so the durable part can be lifted:**

- **State the concept as a contract before describing its realization.** TrueTime's three-method table, Raft's Figure 2 specification, Bigtable's data model. A reader should be able to reimplement the WHAT against a different HOW.
- **Describe your dependencies by the contract you use, and ask the same of your own concept.** Megastore and Spanner reused Bigtable through its interface as Bigtable used GFS. Pathways listed what its closed substrate must provide and argued Ray could host the design; untested, but it detached the idea from the substrate.
- **Keep the thesis off components on the roadmap for replacement.** Read the roadmap and open issues. If a component is about to be rewritten, either do not make it the thesis or label it as a snapshot. Ray tied a principle ("centralize control state") to the GCS, which its own later whitepaper inverted.
- **Give secondary WHAT-level results their own standing.** AlphaTensor's 14,236 nonequivalent factorizations survive a lower rank found elsewhere; Ceph gave RADOS its own paper.
- **Tie each strong claim to the layer its evidence supports.** Spanner's proof supports "TrueTime enables external consistency"; nothing in its evaluation supports a role in availability, which is the part that later needed correcting.
- **Run the rewrite test** on the abstract: which sentences would survive the system's next major rewrite? If the thesis is not among them, move it up a layer or label it as a snapshot.

This is conditional on maturity. A young system can and should state its WHAT and rest evidence on invariants, ablations, and parity, because its HOW numbers will change. A mature system can carry HOW-level telemetry and lessons. A follow-up paper should move the thesis to whatever layer survived, using the old design as a labeled baseline (Ownership on Ray).

---

## 7. Naming

Names are where a concept detaches from its system, and in the cases naming is one of the main mechanisms of elevation, not decoration.

- **Use two names: one for the idea, one for the artifact.** IO-aware / FlashAttention; the sorted map / Bigtable. The idea's name must pass name removal; the artifact's need not. When only the artifact has a name, readers cite the artifact and the idea stays attached to one implementation.
- **Put the name on the component that produces the benefit.** PagedAttention names the kernel, which is slower in isolation; the benefit comes from the block manager. Check the name against your ablation: if the ablation credits a different component, rename or re-cut.
- **The name should predict the mechanism and the layer.** TrueTime names the API, and the API is the thesis. TVM's added "Automated" named the novelty it could evaluate. A name that predicts the wrong mechanism misleads every later citation.
- **Name a weakness to turn it into a design space.** GFS's consistent / defined / undefined table converts "weak consistency" into a precise middle ground; Dynamo's (N, R, W) turns a relaxation into a dial users can set. If your system gives up a guarantee, a named vocabulary for exactly what it gives up is often the most transferable thing in the paper.
- **Know the risks of each name type.** Slogans ("no such thing as a parameter server", "bottom-up scheduler") are memorable and can reduce to renaming. A coined workload term is a bet about workloads (JAX's PSC; the project dropped it). Naming micro-discoveries (AlphaDev's "swap move") gives critics a precise object to check, which cuts both ways.
- **If you do not name and frame your work, someone else will.** XLA published no framing paper; TVM categorized it as a "graph-level DSL", MLIR as one fragment, and a 2023 third-party paper's first contribution was describing its fusion, which "has not yet been well described in public papers or documents". Competitors also chose the configuration in which it was benchmarked.

Diagnostics: can the named concept be explained in one paragraph to someone who has never heard of the system? Does the name attach to the component that produces the benefit? Would it still be accurate after the implementation is rewritten?

---

## 8. Generality claims and scope statements

### 8.1 Forms of generality, ordered by the evidence they carry

| Form | Example | Typical overreach |
|---|---|---|
| Built subsumption with "express" defined | RDD rebuilds Pregel and HaLoop as ~200-line libraries | Of six models listed, two built, two argued by operator correspondence, one conjecture |
| Mathematical generality | Tensors represent any bilinear operation (AlphaTensor); FlashAttention's bound over a stated range of SRAM sizes | Bound stated beyond the range where the model tracks hardware |
| Domains that differ in kind under one fixed template | FunSearch: cap sets and bin packing; TVM: one exemplar per hardware class | Selection of domains described only as curated |
| Applicability conditions as a list | FunSearch's three conditions; vLLM §8; GFS §9; RDD §2.4 | (This form is the remedy, not the risk) |
| A taxonomy of configurations | Dynamo's three usage patterns | Patterns drawn from one company's tenants |
| Adoption | MapReduce's 395 unique map implementations; Bigtable's 60+ products | Adoption shows usefulness, not the idea's causal role; depends on author authority |
| Design separated from implementation | Pathways: Ray could host the design | Cheap and untested |
| A swappable objective | AlphaTensor's runtime reward; AlphaDev's latency head | One swap shown, breadth implied |
| A successor mapping | Borg's lessons mapped to Kubernetes designs | Holds only if the successor works |

Two instances that differ in kind persuade more than many of the same kind. No source says how many are enough; let the claim name its class and show at least one member from each region of the class that behaves differently.

Author position changes what is available. Industry papers used adoption as generality evidence (MapReduce, Bigtable, Dynamo). An academic group without that authority needs an explanatory account of why the idea generalizes (RDD §7.1) and built instances. Adoption also does not rebut critics: Bigtable's adoption did not answer the transaction complaints Spanner later cited.

### 8.2 Tagging generality sentences

Find every sentence that claims breadth: "general", "unified", "any", "a wide range", lists of workloads or domains, "first". Tag each one:

- **built and measured**: implemented and evaluated;
- **built**: implemented, not evaluated head to head;
- **argued by correspondence**: shown to map onto your operators, not built;
- **conjecture**: plausible, neither built nor argued in detail.

Only the first two belong in the abstract and contributions list. The other two go in discussion, in the voice of argument or conjecture ("we believe"). Overclaims in the cases are almost all sentences that would have been tagged conjecture: the Spark dissertation's "emulate any distributed system", Ray's "first to unify", AlphaDev's hashing paragraph ("in theory"), AlphaEvolve's "~75% matched, ~20% improved" over a curated set with the matched cases left out of the public notebook, Ceph's "uniquely scalable" with MDS recovery unimplemented.

### 8.3 Scope statements

A generality claim is credible in proportion to the boundary drawn around it. Stating where the idea does not apply raises trust: RDD §2.4 hands asynchronous fine-grained updates to RAMCloud, Percolator, and Piccolo, which converts "RDDs are general" into "RDDs cover batch, bulk-write workloads", a claim its evidence supports. vLLM §8 says why the paging analogy holds (dynamic, memory-capacity-bound workloads) and that it "may rather degrade the performance" for training.

Write the non-applicability paragraph before the generality paragraph. Generalize the WHAT over a class defined by its premises, not the system over a domain ("for workloads where X and Y hold" rather than "for distributed storage"). Include the load-bearing condition from the real gap (§2) if there is one. A scope statement written last tends to list harmless limitations; candor in the cases is common about costs that don't threaten the headline and rare about the gap that does. Name the limitation that would most reduce belief in the abstract's first claim, and scope against that one.

---

## 9. Choosing among competing frames

### 9.1 Generating frames

Draft two or three frames on purpose. For each, write at least the thesis sentence (without the system's name), the cited believer, and the decisive experiment; those are where frames usually differ. Add the kind of concept, the layer, the venue, or the genre (idea, synthesis, concern-organized, experience, instrument, measured system; see `reading-a-system.md` §9) when the frames differ on them, and skip any field that does not help you choose. Spark's three framings of the same code show that frames usually differ in evidence needs, not in truth. TensorFlow's two documents show that a venue can force the choice: OSDI needed a differentia and experiments, which moved the parameter server from related work to the central foil.

Keep at least one rejected frame alive until the trial below. Ask explicitly what drove the system's adoption. If the thesis and the adoption driver differ, accept that deliberately and plan where the adoption driver will be documented. TensorFlow-OSDI passed every local test against the frame it chose and was mispositioned against what drove adoption (eager execution, where PyTorch placed itself).

### 9.2 Trial by abstract

For each frame, write a full abstract using only claims from the claims ledger, and mark each sentence measured, argued, asserted, or future. Then:

- If the thesis sentence or the first claimed result is asserted, the frame is out, unless you can schedule the experiment that converts it. Raft's 2012 understandability claim became the 2014 user study this way.
- Count asserted sentences in the abstract. The frame with fewer, at equal interest, wins.
- Check which abstract survives the rewrite test (§6) and the straw-believer check (§1.3).
- Read each abstract as a non-specialist would: "algorithm", "first", "global", "linchpin" are read at full strength.

### 9.3 When to freeze, and what reopens a frame

Freeze a frame once it passes trial by abstract and its decisive experiment is in hand or scheduled. Endless reframing and premature freezing are both process failures; the freeze rule exists so that drafting can proceed with a stable claim set.

Reopen only on a named trigger. The five that reshaped the classic papers, each with the question to simulate before a reviewer does:

| Trigger | Case | Question |
|---|---|---|
| Venue or audience change | TF whitepaper → OSDI; MapReduce OSDI → CACM | What does this venue count as a contribution, and which evidence type carries it? |
| Arrival of an evaluable mechanism | TVM's learned cost model → "Automated" | Is the new mechanism better tested than the old thesis, or just newer? |
| Demand for evidence of the headline | Raft 2012 → 2014; PyTorch reviews | What experiment would a skeptic outside our community accept? |
| Attack on an omitted comparison | MapReduce vs DBMS; AlphaTensor vs flip graphs | What would an expert in each adjacent field use instead? |
| System outgrowing the paper | Ray → Ownership; Dynamo → DynamoDB | Which claims depend on a mechanism scheduled for replacement? |

Add one more simulation: if a competitor beats your headline record next month with a cheaper method, what remains? A record is a wasting asset as a headline; a structural finding or reusable formulation is not (AlphaTensor partly hedged with its nonequivalent factorizations and the formulation).

Promote late arrivals carefully. A mechanism that arrived last is often the least tested part of the system, and promoting it can lead to tables built so it loses on no row.

---

## 10. Splitting one system into several papers

### 10.1 When to split

| Case | Split | Logic |
|---|---|---|
| Ceph | OSDI (integrative); CRUSH at SC; RADOS at PDSW; dissertation | By research question and evaluation environment: traces, simulation and statistics, cluster runs with failure injection |
| Raft | ATC paper; ~200-page dissertation | Paper carries what an implementer needs; dissertation carries completeness and candor |
| Spanner | 2012 TrueTime → 2017 SQL → Brewer's note | Thesis first, catch-up later |
| Omega → Borg | Forward-looking architecture, then retrospective | A design idea can appear before its host can be named |
| Ray | Position → OSDI → Ownership → whitepapers | Each follow-up moved the thesis to the surviving layer |
| XLA | No paper | Others framed it and chose its baseline configuration |

Split when candidate theses need different evidence environments, not merely because there are many ideas. CRUSH needed simulation and statistical analysis that an architecture paper could not hold; each piece was then testable against its own environment. A useful test: if two candidates would share no experiments, they are probably two papers. If they share the decisive experiment, they are one paper with one of them demoted.

Hold one WHAT per paper. A second organizing idea gets demoted to a clause anyway (Spark 2012 dropped broadcast variables and accumulators), so decide the demotion yourself rather than letting the outline do it.

### 10.2 Order

Publish first the concept that is the dependency root and has the cleanest isolating evidence (TrueTime; CRUSH). The integrating document comes either first, as a compressed overview citing the parts (Ceph OSDI), or last, as a synthesis under one thesis (the dissertation). Choose first if the community needs the whole to understand any part; last if the parts can each stand alone.

Which document carries what (paper, docs, blog, dissertation), and keeping their numbers consistent, is covered in `filtering-and-honesty.md` §11.

### 10.3 Costs

- **The flagship cannot defend subsystem details.** Whatever it compresses is open to attack until the companion paper appears.
- **The durable abstraction may land in the least prestigious venue.** RADOS appeared at a workshop.
- **The first paper's omissions become the system's identity** until a later document corrects them (Spanner 2017); see `filtering-and-honesty.md` §11 for the defenses.
- **Skipping the paper is also a choice with costs.** Without your own framing and evaluation, competitors' baseline configurations become your published performance.

---

## Assumptions and limits

The kinds, tests, and survival table come from eleven case studies covering about two dozen influential papers, chosen because they mattered later. They show which moves were present in papers that succeeded and which omissions were later attacked; the second kind of evidence is stronger than the first. Several moves (adoption as generality, disclosure as novelty, telemetry-only support for a synthesis) depended on the authors' institutional standing, and transfer weakly to a group without it. The "what survived" column is hindsight; use it to cut layers so survival is possible, not to predict which part will survive.
