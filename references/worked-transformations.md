# Worked transformations: raw system material → paper material

This file teaches the core skill by example: how material that existed in a system, its docs, or an earlier version was re-represented as paper material, why that worked, and what it cost. Entries are organized by **transformation move**, not by paper. Read it when you hold a pile of material and need to see what a successful re-cut looks like, or when a draft reads like a catalog and you want to know which move it is missing. Find the move whose recognition cue matches your system; borrow its decision logic; then run its failure mode against your draft.

## Contents

| # | Move | Raw material that signals it | Illustrated by |
|---|---|---|---|
| 1 | Feature list → delta against a predecessor you own | A known predecessor; features described as additions, not as limits removed | TensorFlow whitepaper → OSDI |
| 2 | Many challenges → fewer, evaluable mechanism promoted into the title | Challenge list as long as the feature list; one late mechanism with a clean ablation | TVM arXiv v1 → OSDI |
| 3 | Co-equal abstractions → one; the others dropped | Early docs list N primitives at equal weight; one explains the rest | Spark 2010 → RDD 2012 |
| 4 | Big system → one exposed interface with invariants and a proof | A guarantee many features rely on; a nuisance handled explicitly | Spanner / TrueTime |
| 5 | Memory manager → imported analogy with a published term mapping | Engineers explain the design in another field's words | vLLM / PagedAttention |
| 6 | Known-ingredients kernel → metric swap with a bridging figure | A measurement where the believed proxy and the outcome come apart | FlashAttention |
| 7 | Huge search system → named game, correctness by construction, system to Methods | Outputs are formal objects with a cheap checker | AlphaTensor, AlphaDev |
| 8 | Pipeline → a name for the representation shift | The distinctive choice is *what* is searched or produced, not the loop | FunSearch |
| 9 | Workload facts → premise register (and the causation problem) | Design docs justify choices by workload numbers | GFS |
| 10 | SLA → requirements + synthesis table + contribution-type sentence | No novel component; a hard quantified requirement met | Dynamo |
| 11 | A decade of policies → reader questions + one metric + lessons | Mature system, many policies, no fan-out decision | Borg |
| 12 | Soft property → method + proxy + bias table + conventional evidence | Your best selling point is an adjective | Raft |
| 13 | Closed system → parity + substrate contract | Nothing can be released; best result is "no worse than X" | Pathways |
| 14 | One big system → several papers split by evidence environment | Candidate theses need different experimental setups | Ceph, CRUSH, RADOS |
| C1–C5 | Counterfactual rewrites | Material that was present but unused | HDFS, Ray, Spark, AlphaTensor, a hypothetical |
| — | How to use these | | |

Each entry has five parts: **Raw** (what existed), **Paper** (what the paper made of it), **Decision logic** (why it worked, including its preconditions), **Cost and aging** (what it invited or what broke later), and **Cue** ("you are probably looking at this move in your own system when…").

---

## Moves that choose what the paper is about

### 1. Feature list → delta against a predecessor you own

**Raw.** The 2015 TensorFlow whitepaper called itself "an interface for expressing machine learning algorithms, and an implementation for executing such algorithms." Its outline read like a manual plus an implementation tour (Programming Model, Implementation, Extensions, Optimizations, Experience, Idioms, Tools). Variables were one feature in the programming model. The parameter server appeared only in related work (§11). DistBelief was told as lineage ("has served us well"). There was no evaluation, only a placeholder promising one.

**Paper.** OSDI 2016 §2.1 criticizes DistBelief as three missing user flexibilities (new training algorithms, new layers, refined optimizers). §2.2 makes the parameter server the foil ("no such thing as a parameter server"), and mutable state as a dataflow vertex becomes the two-bullet differentia against batch dataflow and the answer to "why not a PS?". Whitepaper features return as §4 case studies of one shape: this used to require editing DistBelief's PS; now it is user code. Four experiments appear. TensorBoard (7 mentions → 0), the EEG tracer, the placement algorithm and the porting lessons are cut; the layered Implementation section (§5) holds the rest.

**Decision logic.** The venue needed a named alternative and experiments. Owning the predecessor made criticism cheap and believable, and criticism of others' PS work was deflected to related work with a hedge. Recasting features as before/after capabilities put every survivor on one chain (state in the graph → user-level policies). The stay/go rule was: differentiates from the named alternative, lies on the chain to an abstract capability, or can be measured.

**Cost and aging.** "No parameter server" was architectural, not operational: deployments still ran PS tasks, so the slogan invites a deflating "it's a renaming" reading. "Flexibility", the main differentiator, had case studies but no instrument; the evaluation showed only that generality is not slow. The frame ranked deferred execution above researcher usability; TensorFlow Eager followed in 2019 and PyTorch took the other side. The cut tooling was part of what drove adoption. The paper was locally excellent and globally mispositioned.

**Cue.** Your team has a predecessor readers know; your docs describe features as additions rather than as limits removed; something your system makes unnecessary sits in related work; you keep writing "X used to require modifying the runtime." Before using it, check that the removal is real (not a renaming) and ask whether the property you are elevating is the one users actually choose you for.

### 2. Many challenges → fewer, with the evaluable mechanism promoted into the title

**Raw.** TVM arXiv v1 (Feb 2018), "End-to-End Optimization Stack", listed four hardware-derived challenges (dataflow rewriting, memory reuse across threads, tensorized intrinsics, latency hiding) mapped one-to-one onto four features. It had no cost model at all. Between versions a learned cost model arrived. Around it sat a graph optimizer with several passes, seven frontends, five backends, about 50k lines of C++.

**Paper.** OSDI / v3 has two challenges, "leveraging hardware features" (expressiveness) and "large search space" (search). The four techniques become subsections under the first. The cost model becomes §5, and the title becomes "An *Automated* End-to-End Optimizing Compiler." Graph optimization shrinks to fusion and layout, with the other passes a clause each; frontends become a parenthetical. Fig. 2 labels each box with its section number. Halide is credited with a one-sentence delta ("also separating target hardware intrinsics").

**Decision logic.** A challenge list that maps one-to-one onto features carries no information; a challenge should index a *kind* of technique. The cost model was the one novelty with a clean comparison (Fig. 12) and it answered a different challenge from the scheduling primitives, so it could carry the title's adjective. "Stack" names a box; "Automated" names an idea a reviewer can test. Baselines a reviewer would ask for (XLA, TFLite, Tensor Comprehensions, ARM Compute Library) were added; the case infers this was review-driven from the diff.

**Cost and aging.** Late arrivals are often the least tested part: Table 1 (three automation methods × four attributes) is built so each alternative loses on one row and TVM on none. The evaluation is organized per platform with a different baseline on each, so "1.2× to 3.8×" mixes unlike comparisons and no challenge can be checked against its evidence in one place. The burden of writing schedule templates is muted. The module list was later replaced in the repo; the decomposition and argument outlived it.

**Cue.** Your challenge list is as long as your feature list; one mechanism arrived late and is the only thing with an on/off experiment; your title names the architecture ("stack", "platform", "framework"). After recutting, check that the evaluation follows the new challenges, and audit any comparison table you built for the new mechanism.

### 3. Co-equal abstractions → one, the others dropped

**Raw.** Spark at HotCloud 2010 offered "three simple data abstractions": RDDs, broadcast variables and accumulators. It was framed as a framework for working sets, conceded a family relationship to DSM in one related-work paragraph, and had measurements on iterative and interactive jobs.

**Paper.** NSDI 2012 is "a fault-tolerant distributed memory abstraction." Broadcast variables and accumulators get zero mentions. The DSM paragraph is promoted to a main-body comparison with Table 1. Lineage and the narrow/wide dependency distinction become the intellectual core. §2.4 "Applications Not Suitable for RDDs" bounds the claim, and Pregel and HaLoop rebuilt as roughly 200-line libraries carry generality. In the case writer's words, the story "needed a new framing for the same measurements."

**Decision logic.** "Restricted shared memory made fault-tolerant by lineage" could not generate broadcast or accumulators; keeping them would require a second organizing idea. One abstraction could be defined, positioned in a design space, and bounded. An academic group without a production fleet needed a conceptual contribution where Google could cite deployment.

**Cost and aging.** The dropped features remained core APIs, and the system is still judged on them; the paper covers the engine core only. The DSM category was defined as any fine-grained-write shared state, so the practical alternative (MapReduce plus caching, Piccolo) was never a row, and the table prints "Poor performance (swapping?)". "RDD as universal interface" yielded to DataFrames and SQL, under which RDDs became the substrate. Dropping a co-equal abstraction is a cut of scope only if no remaining claim depended on it; record it in the filter ledger with its destination.

**Cue.** Your earliest paper or README lists N primitives at equal weight; one of them explains the others' guarantees or the headline capability; the rest are conveniences that would need their own thesis. A related-work paragraph that concedes kinship with an older line of work may be promotable into the positioning device; make the strongest live alternative a row when you do.

### 4. Big system → one exposed interface with invariants and a proof

**Raw.** Spanner in 2012: Paxos groups, lock tables, transaction managers, directories, a SQL-like query processor (a decade-scale investment by the 2017 account), zonemaster, placement driver, storage format, schema language; features including external consistency, lock-free snapshot reads and atomic schema change; a GPS and atomic-clock time service. One major customer; several features unimplemented.

**Paper.** TrueTime is three methods in Table 1, with `TT.now()` returning an interval. §3 is about 1.5 of 14 pages, but every other section points to it. §4 states three invariants, two rules (Start, Commit Wait) and a four-line proof, then re-derives the features one by one. The abstract makes the API the grammatical subject. §2 describes the substrate first, trimmed to the objects §4 needs. SQL gets one sentence ("We will not go into detail about the query language"); "for space reasons, we will only describe the spanserver"; an abandoned design costs one sentence. The "1D" row (commit wait disabled: 9.4 ms vs 14.4 ms) prices the mechanism, and epsilon becomes a dial and a research agenda.

**Decision logic.** Reification turns a nuisance (clock skew) into a return value that can carry rules and a proof, which does not depend on the system's maturity. The interface passes name removal ("now is an interval"). One decision fans out to three features. The real opponent was a belief (general 2PC is too expensive; clocks can't be trusted), not the stated gap (Bigtable awkward, Megastore slow).

**Cost and aging.** Over-attribution: snapshot reads rest mostly on multiversioning plus safe time, and Brewer (2017) rescoped TrueTime's role in availability ("it is really Google's wide-area network"); "linchpin" invited a CAP over-reading. Scope gap: microbenchmark zones sat less than 1 ms apart. Users adopted Spanner for SQL; the 2017 paper says "today's Spanner is very different from what was described there" and mentions TrueTime once in its body. The proof and interface lasted; the numbers did not.

**Cue.** The docs state a guarantee with conditions; one internal API is called by many features; your system handles explicitly a nuisance that competitors hide; you can write the core contract as a table of a handful of operations. For each feature you credit to the interface, name the mechanism that fails without it, and attribute the rest elsewhere.

### 5. A memory manager → an imported analogy with a published term mapping

**Raw.** vLLM v0.2.0: a 273-line block manager, a ~440-line scheduler, a ~8.4K-line model executor across 14 model families, ~3K lines of API server, engine and tokenizer code, quantization kernels, an attention kernel adapted from FasterTransformer, a launch blog claiming 24× over HuggingFace, and two months serving Chatbot Arena.

**Paper.** The introduction states the mapping before any system description: "blocks as pages, tokens as bytes, and requests as processes." Every mechanism then uses OS terms (block table, copy-on-write "when forking a process", swapping, shared libraries). §3 defines a waste taxonomy (reserved, internal, external) and Fig. 2 measures that only 20.4–38.2% of KV memory holds tokens, before the system appears. §4 applies one mechanism to progressively harder decoding cases, with one prompt ("Four score and seven years ago…") traced through six figures. The departure is named (all-or-nothing eviction). Models get one sentence. §8 scopes the idea ("may rather degrade the performance" for training).

**Decision logic.** SOSP readers know paging, so the analogy compresses explanation and imports trust. It also generates the filter: keep what is a consequence of the analogy, drop what would need a second organizing idea. Defining the waste metric before the design fixes what the evaluation must show. Preconditions: a near-isomorphism, an audience that knows the source concept, and listed departures.

**Cost and aging.** The name sits on the wrong layer: "PagedAttention" names a kernel that is 20–26% slower in isolation, while the block manager carries the benefit. The analogy's appeal hid the baseline question: Orca-Max reserves 2048 tokens per request, the Orca variants are the authors' reimplementations, and a growing contiguous allocator is dismissed in two sentences. Prefix sharing was evaluated but absent from the v0.2.0 tag. Blog (24×) and paper (2–4×) answer different baselines. The model zoo, cut from the paper, drove adoption.

**Cue.** Engineers explain your design in another field's words ("it's basically paging", "like a write-ahead log"); the mapping covers most mechanisms with a short list of departures. Write the mapping table and the departures before drafting, attach the name to the component the ablation credits, and list the strongest non-analogy alternative an engineer would try first.

### 6. Known-ingredients kernel → metric swap with a bridging figure

**Raw.** FlashAttention's first release: about 10.8K lines of CUDA built from Apex FMHA (files still named `fmha_*`), A100 and fp16 only. Tiling, the softmax decomposition and recomputation were all known. Heavy per-architecture engineering. A contemporaneous paper (Rabe and Staats) used the same blocks.

**Paper.** "A missing principle is making attention algorithms IO-aware" (abstract, sentence 3). Standard attention is rewritten as Algorithm 0 in HBM reads and writes, so it reads as a bad I/O algorithm before the new one appears. The ingredients are disclaimed ("two well-established techniques"). Theorem 2 and a lower bound are scoped ("for all M in [d, Nd]"). Fig. 2 shows more FLOPs (75.2 vs 66.6 GFLOPs), far less HBM traffic (4.4 vs 40.3 GB), and less time (7.3 vs 41.7 ms). Kernel engineering is one sentence; the FMHA lineage sits in acknowledgments and Appendix E; against Rabe and Staats the delta is "same blocks, different objective." Two names: IO-aware (idea), FlashAttention (artifact).

**Decision logic.** When ingredients are old, novelty moves to the objective. A swapped metric recasts incumbents as having optimized the wrong quantity, and one proxy-versus-outcome figure is the evidence for the principle. The theorem works as a legitimacy device at an ML venue. Precondition: a cost model that tracks reality over the claimed range.

**Cost and aging.** The block-size sweep shows runtime following HBM accesses only "up to a point" (above about 256 it is "bottlenecked by other factors"); the paper shows the bend, which is what makes the rest credible. FlashAttention-2 and -3 superseded Algorithm 1 while "IO-aware" survived. Lineage is nearly invisible in the main text. Promised extensions were not demonstrated.

**Cue.** You have a surprising measurement where the field's proxy and the outcome disagree; your implementation does more of the "expensive" thing and is still faster; a reviewer would say "these techniques are known." Plan the figure that shows where your model stops predicting.

### 7. Huge search system → a named game with correctness by construction; the system to Methods

**Raw.** AlphaTensor: an AlphaZero fork with a custom torso, autoregressive policy and quantile value head; 5 million synthetic demonstrations; random change of basis; 64 TPU cores, 1,600 actors, a week to converge; a recombination pipeline; nonequivalence certification; a hardware-in-the-loop reward. AlphaDev adds a benchmarking harness taking the fifth percentile over 100 machines.

**Paper.** TensorGame (AssemblyGame) is named in the first paragraphs; the agent arrives as the thing that plays it. The system overview is one sentence of "advances", each answering a difficulty of the game (huge action space → sampled autoregressive policy; no known solutions → synthetic demonstrations, justified because factoring is hard and multiplying out is "elementary"). Fig. 2 reduces the actor-learner system to "Acting" and "Learning" boxes. Compute is in Methods; the ablation showing synthetic demonstrations are worth 15 ranks (64 vs 49) is in the SI. Strassen's 2×2 is the Rosetta stone. The reward is swappable ("Aside from the different reward, the exact same formulation"). The repo holds factorizations and verifiers, no training code. Main text: what a scientist needs to believe the result; Methods: what a practitioner needs to re-implement; SI: what an expert needs to audit.

**Decision logic.** If reaching the terminal state implies a correct algorithm, no reader needs to trust the network. Output claims rest on a checker, so the generator can stay closed. This works only because the outputs are formal objects; for claims about system behavior, withholding the system withholds the evidence.

**Cost and aging.** Method claims got no method evidence. With no equal-budget search baseline, flip graphs (Dec 2022) matched the best known schemes for all formats up to 5 and found 95 for 5×5 mod 2 (AlphaTensor: 96) on a laptop-scale budget. "Beyond human intuition" fell; TensorGame and the 14,236 nonequivalent factorizations survived. Records are a wasting headline. Results from the recombination pipeline were credited to the agent. AlphaDev's "integrated into LLVM" omitted the patch's restriction to arithmetic types and simple comparators (a cut of qualification), "new sorting algorithm" was read at full strength, and the named swap move turned out to save one instruction. Its main-text comparison with a stochastic superoptimizer earned reviewer credibility but not public acceptance, because critics attacked significance, not method.

**Cue.** Your outputs are formal objects with a cheap checker; most of the engineering is something a domain reader doesn't need; you can define the search space so that a terminal state implies correctness. Sort the claims into output claims and process claims before deciding what to withhold, and promote a structural result alongside any record.

### 8. A pipeline → a name for the representation shift

**Raw.** FunSearch: asynchronous programs database, samplers and evaluators (15 samplers, 150 CPU evaluators, about 10⁶ LLM samples); islands with periodic reset, clustering by score signature, Boltzmann selection, length bias, two-program prompts; a sandbox; a robustness record in which 4 of 140 cap-set runs found the size-512 set.

**Paper.** The name is the thesis: "searching in the function space", programs "that describe how to solve a problem, rather than what the solution is." The shift gets three justifications (concision: a set of over 200,000 vectors from a few-line program; interpretability: a hand-simplified construction; deployability). Four "ingredients" close the introduction. Fig. 2, a specification figure showing the evaluate function, solve skeleton and priority stub for two problems, explains more than the loop in Fig. 1. The distributed system gets one subsection; database equations go to Methods; three applicability conditions (efficient evaluator, rich scoring, available skeleton) bound the claim; two domains that differ in kind carry generality.

**Decision logic.** Naming the representational choice makes the loop, islands and prompting into the "how." Choosing open problems turns the contamination worry into evidence of novelty. The conditions list makes the remaining generality hard to attack.

**Cost and aging.** The successor demoted the thesis: AlphaEvolve's "flexibility in choosing the abstraction" makes function space one of four levels, and Tao et al. report search mode as the default. Interpretability faded in follow-up use; the Kolmogorov-complexity explanation was a "we believe", never tested. The methodological delta over prior LLM-guided evolution was modest, and the hit rate sits in Methods.

**Cue.** Your system's distinctive move is a change of what is searched or produced (generators instead of outputs, plans instead of actions); you find yourself defending the representation rather than the loop. Ask whether the representation is a principle or a workaround for a weak component that the next generation will remove.

---

## Moves that build a middle layer when there is no single mechanism

### 9. Workload facts → a premise register, and the problem of rewritten causation

**Raw.** GFS: about three years of production, two measured clusters (342 and 227 chunkservers), workload traces, operational anecdotes, and a single master chosen as "one of the very first decisions, mostly just to simplify the overall design problem" (Quinlan, 2009). The file-system team also controlled the applications.

**Paper.** The introduction gives four observations, each paired with its consequence (failures are the norm → monitoring is integral; huge files → block sizes revisited; append-mostly → client caching "loses its appeal"; co-design → relaxed consistency plus atomic append). §2.1 lists assumptions with numbers, and every subsection of §§2–5 serves one of them. The recurring paragraph is choice → benefits "First… Second…" → cost → why the cost hasn't bitten, often with an anecdote. The consistent/defined table turns "weak consistency" into a precise middle ground. §9 bounds transfer to "data processing tasks of a similar magnitude and cost consciousness."

**Decision logic.** A premise register makes a messy system read as a derivation and lets readers decide whether it applies to them. Each mechanism becomes a forced move from a stated fact.

**The honesty problem.** The paper says its design "has been driven by" the observations. The facts were true; the causation was reversed. The real drivers were shipping fast and controlling every client (Gobioff: "we controlled both the horizontal and the vertical"), and the second was load-bearing for relaxed consistency. Argument order is fine; asserting false history is not. The fix costs almost nothing: write "is justified by", and state the boundary condition ("this works because the file-system team controls the clients"). Test: would a reader deciding whether the design applies to *their* system be misled by the omission?

**Cost and aging.** Single master, record append ("more painful than it was worth"), 64 MB chunks and the "few million files" assumption were all revised. The method aged better than the conclusions, because readers could see which premise broke. The paper kept small failures that motivated choices and omitted none that challenged the premises (those came later).

**Cue.** Your design docs justify choices by workload numbers; several mechanisms depart from the textbook and each traces to a workload or environment fact; your team controls the callers. Write the register with numbers, add a row for organizational conditions (time-to-market, client control), and mark in the alternatives register which reasons are documented history and which are reconstructed.

### 10. An SLA → requirements + synthesis table + contribution-type sentence

**Raw.** Dynamo: a production store for "hundreds of services", instances of a couple hundred nodes, every core technique from the literature (consistent hashing, vector clocks, sloppy quorum, hinted handoff, Merkle trees, gossip), a latency SLA, business numbers that could not be published, three partitioning strategies over time, and a long list of production concerns.

**Paper.** It opens on operating reality (carts must work while "data centers are being destroyed by tornados"). §2.1 states four-part requirements that pre-excuse everything the system doesn't do; §2.2 states the contract (300 ms at the 99.9th percentile at 500 requests/s). Related work comes before design and ends in four differentiators. Table 1 (problem, technique, advantage) maps the whole before §4 follows its rows, each written as textbook version → what breaks here → our variant. The contribution sentence tells reviewers where to look: "the evaluation of how different techniques can be combined." §4 opens by listing what it will not cover; §6.2 promotes the three partitioning strategies because the lesson lives in the evolution; the shopping cart shows deleted items resurfacing; (N, R, W) settings form a small taxonomy of uses; withheld numbers are declared as withheld.

**Decision logic.** With no novel component, the requirement turns technique selection into forced moves, the table supplies the map, and telemetry against a stated contract is the evidence. Naming the contribution type stops reviewers searching for a novelty that isn't claimed.

**Cost and aging.** DynamoDB later "shared most of the name … but little of its architecture" (Multi-Paxos, leader-based strong reads). What lasted was the SLA framing, the vocabulary and the trade-off space. The "clearly defined consistency window" was never defined or measured. The principles (symmetry, decentralization) exclude nothing. Telemetry with no baseline was accepted on Amazon's credibility; an unknown group would need more.

**Cue.** You cannot point to a novel component, but the system meets a hard, quantified requirement others did not; your design rationale reads "we took X from A and Y from B and adapted them"; you have dead-end versions. Make the requirement measurable before any design appears, and measure its hardest property.

### 11. A decade of policies → reader questions + one metric + lessons

**Raw.** Borg: a decade of accreted features (BorgMaster, scheduler, Borglet, a configuration language with about 230 parameters, quota, priority bands, allocs, resource reclamation, kernel patches), a simulator that replays production checkpoints, a public trace, a successor (Kubernetes) already open-sourced, and no single idea; an earlier paper by overlapping authors (Omega) had already published the architectural idea without naming Borg.

**Paper.** The introduction names three benefits and says the paper is organized around them. §2 gives the user's view before §3's architecture. §5 (utilization, the benefit) is paired with §6 (isolation, the cost). Cell compaction, the smallest cell that still fits the workload, makes sharing, cell size, bucketing and reclamation comparable as ways to need fewer machines (segregation: 20–30% more machines in the median cell; bucketed requests: 30–50% more), shown as CDFs over 15 cells. A CPI comparison carries its own control (Borglet 1.43 vs 1.20 beside the workload's 1.58 vs 1.53) and is called "not clear-cut." Lessons come bad first, each as claim → Borg evidence → Kubernetes consequence. The score function is not disclosed.

**Decision logic.** The value is spread across choices, so forcing a single idea would produce a slogan. One cost unit turns unrelated policies into a quantitative backbone; the filter is the peer operator's checklist rather than a causal chain; access is part of the novelty.

**Cost and aging.** The lessons carry no data and hold on author trust plus Kubernetes' later success. The form depends on readers having no access to the system, a one-time advantage. Withheld parameters limit reimplementation. A young system should not borrow this form; Omega shows the forward-looking alternative (one idea, a taxonomy, simulation).

**Cue.** The system is mature, has many policies and no fan-out decision; you can replay production state; your readers are peers who ask "how do you handle X?"; you have reversed decisions or a successor to anchor lessons. Find the cross-cutting cost unit first. Without one, organizing by concern degrades into the component catalog of C1.

---

## Moves that make the evidence fit the claim

### 12. A soft property → design method + proxy + bias table + conventional evidence

**Raw.** Raft: a conviction that Paxos is hard to understand, and a 2012 submission that claimed understandability with "essentially no objective evidence" (the dissertation's account; reviewers suggested the study design). Also LogCabin (about 2,000 lines of C++), a TLA+ spec, and an abandoned ranking-based election.

**Paper.** Understandability is named in the abstract. §3 explains *why* Paxos is opaque (its single-decree foundation), which makes Raft different in kind. §4 "Designing for understandability" comes before the algorithm and breaks the property into two techniques (decomposition, state-space reduction). Fig. 2 is a one-page spec and Fig. 3 lists five properties. The abandoned election is kept as the evidence for the principle. A study of 43 students at two schools (33 of 43 scored higher on Raft; 25.7 vs 20.8 of 60) comes with a bias-mitigation table and a disclosed confound (15 of 43 had prior Paxos experience). A TLA+ spec of about 400 lines, a proof, and an election benchmark (median downtime 287 ms with 5 ms of timeout randomness) sit beside it.

**Decision logic.** A soft property becomes defensible when it is turned into a method that can be argued case by case, measured with one proxy whose bias is disclosed, and paired with conventional evidence so the paper stands if the proxy is discounted. PyTorch reaches the same place by pricing the property ("10% acceptable, 100% not"), which turns a parity number into the principle's evaluation.

**Cost and aging.** Reviewers dismissed the study ("lacks representativeness"); the paper was accepted "after several attempts … despite the reviewers' generally negative opinions of the study." The pairing carried it. The study tests "the start of the learning curve." Joint consensus was superseded by the dissertation's single-server membership change. The study design depended on a famous incumbent and the authors' own course populations.

**Cue.** Your strongest selling point is an adjective (simpler, safer, more debuggable); competitors are not slower; you can imagine a reviewer writing "no objective evidence." Build the proxy, write its bias table, narrow the claim to what the proxy measures, and make sure a second kind of evidence holds the paper up.

### 13. A closed system → parity + substrate contract

**Raw.** Pathways: a Google-internal orchestration layer on PLAQUE, a closed-source dataflow system; runs on up to 2048 TPUs; motivating workloads (mixture-of-experts, sparse routing, multi-tenancy) mostly unbuilt; no code release; a vision post by one of the authors as the cited motivation.

**Paper.** Table 1 reports T5 throughput identical to JAX. Parameterized microbenchmarks yield a rule of thumb (parity at 2.3 ms per computation on 16 hosts, 35 ms at 2048 TPUs). Public model configurations (T5, GPipe-style, GShard-like) bound the numbers by known quantities. Traces sit in appendices, and the worst case is admitted (JAX wins the op-by-op case). The paper lists the properties PLAQUE must provide and argues the design could be rebuilt on Ray; §6.1 separates design from implementation. Each TF v1 limitation from §2 is retraced to one mechanism in §4.

**Decision logic.** When readers cannot inspect the system, the paper is the artifact. Parity against the best public baseline lets them sanity-check with quantities they know; the substrate contract detaches the idea from layers they cannot see.

**Cost and aging.** The stated gap (future workloads) sits above the evidence (parity on current ones). The honest headline is "flexibility costs nothing for current workloads", and the motivating capabilities sit in future work without being labeled unevaluated. Authority carried part of the framing and does not transfer. Parity holds partly because real computations are large. The single-controller claim aged best; the 2022 workload promises are not externally visible.

**Cue.** You cannot release the system; a layer beneath you is proprietary; your best result is "no worse than X"; your motivation concerns workloads that do not yet exist. Say which side of the parity claim is evidenced, and label the motivating capabilities as bets.

### 14. One big system → several papers split by evidence environment

**Raw.** Ceph at UCSC in 2006: a prototype of about 40,000 semicolon-lines of C++ comprising client, metadata cluster, RADOS, CRUSH and a local object store, with MDS recovery unimplemented. Its questions needed different evidence: metadata balancing (traces, many-MDS runs), placement (simulation and statistics), replication and recovery (cluster runs with failure injection), local storage (microbenchmarks).

**Paper(s).** OSDI '06 is the integrative architecture paper, treating CRUSH and RADOS as subsections that cite elsewhere. CRUSH (SC '06) is self-contained; Ceph appears once, in future work. RADOS (PDSW '07) is reframed as a general "reliable object storage service." The dissertation reintegrates the parts under one thesis ("device intelligence").

**Decision logic.** Each piece is tested against its own environment and reviewed by the audience that can judge it; one WHAT per paper. Lead with the piece that is the dependency root and has the cleanest isolating evidence.

**Cost and aging.** The flagship cannot defend subsystem correctness. The most durable abstraction (RADOS) landed at a workshop. The OSDI paper headlined the metadata cluster (over a quarter million metadata ops/s; "uniquely scalable"), while the product reorganized around RADOS, and block and S3 interfaces absent from the 2006 papers drove adoption.

**Cue.** Your candidate theses need different experimental setups; one subsystem's problem can be stated without mentioning the host system; your evaluation is splitting into halves that share no figure. "Lots of ideas" is not by itself a reason to split; different evidence environments are.

---

## Counterfactual rewrites

These are **not what the authors did**. Each uses material that was present, according to the case studies, to show a move that was available and unused. Use them to see a move from the inside, not as claims about what would have been accepted.

### C1. HDFS: the thesis it had and did not use (counterfactual)

**Present.** The MSST 2010 paper is organized by component, with defaults in the prose ("The default heartbeat interval is three seconds") and no "why not X" anywhere. Its best material sits in §IV–V: about 0.8% of nodes lost per month, about 20 bad replicas found per fortnight, 0.5–1% of nodes not surviving a power-on restart, a three-tier benchmark framing, and the admission that "our assumption that applications will create a small number of large files was flawed." Its single-writer model is what the GFS team later said it would prefer to record append. GFS is never named as the origin.

**Rewrite.** Thesis: *an open implementation of the GFS design point survives contact with a diverse public user base; here is where the inherited assumptions broke.* The opponent becomes the GFS premise set, stated as a premise register and checked one by one against Yahoo!'s operational data (move 9 run in reverse). Places where HDFS diverged from GFS (single writer instead of record append) become alternatives-register entries with reasons. The component descriptions shrink into an Implementation section.

**Cost.** It requires naming the lineage and conceding that the design is inherited; the novelty becomes data and candor, not mechanism.

### C2. Ray: a composition test for the conjunction claim (counterfactual)

**Present.** Ray's OSDI paper rejects each incumbent for lacking one of three RL legs (simulation, training, serving) and asserts that stitching specialist systems together is "untenable due to the tight coupling." No experiment composes them. The building-block results show Ray matching each specialist alone (Horovod within 10%; Clipper in one co-located setting), which is a different claim. Its later documents dropped RL entirely and kept the task/actor/futures interface.

**Rewrite.** Build the system a skeptic would assemble in a week: a simulator plus a Horovod trainer plus a Clipper server, wired together, running the same RL loop as Ray. Measure end-to-end iteration time and the data movement between systems. If the composition fails, the conjunction thesis is evidenced. If it doesn't, move the thesis down to what the evidence supports, the interface (fine-grained dynamic tasks and actors with futures), which is where Ray's own later papers moved it.

**What it buys.** The paper's load-bearing sentence changes from asserted to measured, or the thesis moves early to the layer that survived.

### C3. Spark: lineage evidence in proportion to its place in the title (counterfactual)

**Present.** The NSDI paper leads with 20× over Hadoop and decomposes that gap itself (HDFS reads, text parsing, deserialization), so the headline number doesn't test lineage. Lineage, the named idea, gets one failure run (k-means, 58 s → 80 s) and a "<10 KB" size claim, with no comparison against checkpointing.

**Rewrite.** Report the 20× as a property of the in-memory runtime. Rest the lineage claim on recovery cost against a checkpointing variant of the same system, across several failure rates and checkpoint intervals. This is the null-idea test applied: the speedup survives removing lineage, the recovery-cost curve does not.

**What it buys.** Each number attaches to the mechanism that produced it, and the title's idea gets evidence proportional to its weight.

### C4. AlphaTensor: an equal-budget search baseline (counterfactual)

**Present.** AlphaTensor compares against published records, not against a non-learned search over the same representation given the same compute. Two months after publication, flip graphs (a random walk over the same kind of discrete decompositions, on a laptop-scale budget against a week on 64 TPU cores and 1,600 actors) matched most of the table.

**Rewrite.** Run a local or random-walk search over TensorGame's representation with an equal compute budget, report hit rates for both methods, and let the result decide the abstract's wording. If search matches, the claim becomes the formulation plus the diversity result, which flip graphs did not undercut. If the agent wins, the method claim ("learned pattern recognition helps") is evidenced instead of asserted.

**What it buys.** The paper meets its competitor class before a competitor does, and output claims and method claims each get their own evidence.

### C5. Batching versus protocol (hypothetical, for a new system)

**Present.** Design docs report "3× lower p99 after adopting our new cache-coherence protocol." The same rollout also introduced request batching.

**Rewrite.** The null-idea test fails: batching alone could explain the drop. Find a rollout stage where only one change shipped, or rerun with batching held fixed. If neither is possible, write the claim as "protocol plus batching cut p99 3×; the protocol's share is X", with X measured or explicitly marked unmeasured in the claims ledger.

**Recognition.** Any headline number taken from a release, migration or A/B rollout that bundled several changes. Production docs report outcomes per rollout, not per mechanism, so this situation is the default rather than the exception.

---

## How to use these

**Match the cue, not the surface.** Look for the entry whose *raw material* resembles yours, not the one whose domain does. A storage system can need move 6 (a swapped metric), and an ML system can need move 9 (a premise register). Several cues may match; strong papers combine two moves (Spanner: interface plus proof; FlashAttention: metric plus theorem; vLLM: analogy plus a waste metric defined before the design).

**Borrow the decision logic and check its preconditions.** Each move worked under conditions: an analogy needs a near-isomorphism and an audience that knows the source; a game formulation needs a cheap checker; a premise register needs true, measured premises plus their organizational conditions; organizing by reader questions needs maturity, access and a unifying metric; a parity paper must say which side is evidenced. If your system lacks the precondition, the move will produce the failure listed under its cost.

**Run the move's failure mode against your draft.** The "cost and aging" part of each entry is the attack the move invites. For an analogy: is the name on the component the ablation credits, and is the strongest non-analogy baseline present? For a metric swap: where does the model stop predicting? For a formulation: what does equal-budget search achieve? For a premise register: which unstated condition is load-bearing? For a dropped abstraction: did any claim depend on it?

**Do not copy the paper's outline.** Spanner's substrate-first order worked because §2 was trimmed to the objects §4 needed. Borg's concern-organized form worked because of a decade of operation and readers with no other access. Nature's three tiers worked because outputs were checkable without the system. An outline is the product of a move, a venue and the evidence at hand; copy the reasoning that produced it and let your own material produce the outline.

**If no move matches, treat that as information.** Either your system's organizing principle is a kind not seen here (these moves are a vocabulary, not a closed list; see `finding-the-contribution.md` §5), or the material has not yet been read for decisions (go back to `reading-a-system.md`). Inventing a spine to fit a move is how vapor papers get written.

**Prefer moves that place the thesis at the WHAT layer.** Across the entries, what survived was an interface (TrueTime, tasks and actors), a metric (IO-awareness, cell compaction), a formulation (TensorGame), a vocabulary (Dynamo's requirements, GFS's consistent/defined) or a stated premise set. What aged was a particular control plane, loop order, module list or quantitative premise. If your chosen move puts the thesis on a component the roadmap will replace, label it as a snapshot or move the thesis up a layer.

## Assumptions and limits

The decision logic in each entry is inferred from differences between versions, from retrospectives and from critiques; only a few cases document the authors' reasoning directly (the Raft dissertation, the GFS interview, Brewer's Spanner note). Treat "why it worked" as a reconstruction that fits the evidence, not as the authors' account. The counterfactuals are untested by construction.
