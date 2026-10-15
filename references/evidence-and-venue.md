# Evidence and venue

Read this when you are designing the evaluation, choosing baselines, placing limitations, or deciding which venue and contribution type the evidence can carry. It goes deeper on stages 4 and 7 of SKILL.md. The organizing claim: the paper's real claim is whatever its evidence tests, and context (venue, contribution type, maturity, openness, authority) sets the exchange rate between kinds of evidence.

The ledgers, maps, and tests here are the writer's analysis. Their output is the work: an experiment run, a claim resized, a baseline strengthened, a scope stated once. They do not appear in the paper as such, and the paper does not narrate them; it states right-sized claims with their evidence.

Contents
1. Type the claims before designing experiments
2. The three gap shapes and how to detect them
3. The null-idea test and isolation designs
4. Kinds of evidence, and stacking them
5. Baselines
6. Limitations, scope, lessons, and unfinished evaluations
7. Venue currencies
8. Contribution types and the evidential debt each owes
9. Maturity, openness, and authority
10. From a pile of docs and logs to an evidence plan
11. Assumptions and limits

---

## 1. Type the claims before designing experiments

Pull every sentence from the draft abstract and introduction that a skeptic could deny, plus the claims a reader will infer from the title ("global", "first", "general", "unified", "algorithm"). Type each one. The type decides what can test it, and, just as useful, what cannot.

| Type | Can be tested by | Cannot be tested by | Typical over-reach |
|---|---|---|---|
| Existence / feasibility | Running at stated scale; percent of a hardware limit; telemetry | Comparison is optional | Wording that slides into "better than" |
| Mechanism attribution | Toggle, decomposition, proxy/outcome decoupling, idea-removed variant | End-to-end numbers alone | Spark's 20× read as lineage's win |
| Magnitude in a regime | End-to-end against the strongest fair baseline in that regime | A baseline in its worst regime | vLLM's Orca-Max reserving 2048 tokens per request |
| Parity / no-regression | Matching the best public baseline on public configurations | Case studies (they test the benefit side) | Implying the benefit side was also shown |
| Generality | Built subsumption with "express" defined; instances differing in kind; stated preconditions | Many instances of the same kind | "Emulate any distributed system" |
| Non-performance property | A proxy with disclosed bias, paired with conventional evidence | Line counts or adoption alone | TensorFlow's "flexibility" |
| Guarantee | Invariant plus proof or model checking; the mechanism's measured cost | Benchmarks | Crediting the guarantee with properties it doesn't produce |
| Use / impact | Adoption counts, specific behavioral anecdotes | Why it is used; whether the idea is the reason | Adoption offered as a rebuttal to critics |

Then mark the one claim the paper cannot lose. Evidence effort should be distributed by claim weight, not by what is easy to measure. A claim may also carry two types at once: "PagedAttention cuts KV-cache waste" is both attribution (to which component?) and magnitude (in which regime?), and each type needs its own test.

For generality sentences, also tag the evidence status (built and measured / built / argued by correspondence / conjecture); `finding-the-contribution.md` §8 covers the tags and the forms of generality evidence.

## 2. The three gap shapes and how to detect them

The structural cause is the same for all three: the idea is a cause inside the system, the convenient measurement is an end-to-end effect with many causes, and reviewers and headlines both want end-to-end numbers. So authors measure the effect and assert the cause. Expect this in your own draft; it is the default outcome, not a lapse.

**Attribution gap.** The number is real; the cause is other engineering.
- Cases: Spark's 20× over Hadoop decomposes (by the paper's own Fig. 9) into avoided HDFS reads (2 s), text parsing (7 s) and deserialization (3 s); lineage gets one failure run (k-means, one machine killed, 58 s to 80 s). vLLM's brand is the kernel, which is 20–26% slower in microbenchmarks; the benefit comes from the block manager. AlphaTensor's "improves over 70 tensors" came from the agent plus a separate recombination pipeline. TVM's "1.2× to 3.8×" spans four platforms with a different baseline on each.
- Detect: run the null-idea test (§3) on the experiment behind each abstract number. Also check the name: for each feature credited to the concept, name the mechanism that fails without it (Spanner's abstract credits external consistency, snapshot reads and schema changes to TrueTime; Brewer later narrowed that, since snapshot reads rest mostly on multiversioning plus safe time).
- Repair: decompose and move the claim onto the idea's share; rename so the brand sits on the component that does the work; or run an isolation design.

**Scope gap.** The claim covers a regime the evaluation does not reach.
- Cases: Spanner's "global" microbenchmarks put clients and zones under 1 ms apart. Pathways is motivated by MoE, sparse routing and multi-tenancy and evaluated on SPMD parity and pipelining. Ray's "stitching specialists is untenable" is asserted twice; the building-block comparisons show Ray matches each specialist alone, which is a different claim.
- Detect: put the title's and abstract's nouns next to the evaluation's setup paragraph. A conjunction claim ("nothing has A, B and C together") needs an experiment where composing existing pieces fails; without one it is a scope gap.
- Repair: run in the missing regime, or write the regime into the claim.

**Property gap.** The claimed adjective has no instrument.
- Cases: TensorFlow's "flexibility" rests on case studies while §6 measures only that generality is not slow. Dynamo's "clearly defined consistency window" is never defined or measured. MapReduce's "simplified" rests on 3,800 to 700 lines and an anecdote. AlphaEvolve's database, cascade and islands sit in its system figure and get no ablation.
- Detect: list the adjectives in the abstract and point at the figure for each. Compare the system figure's boxes with the ablation's rows; boxes without rows are advertised but unevidenced.
- Repair: build a proxy with disclosed bias (§4.5), price the property as a bound (§4.2), or demote the adjective.

Which gaps hurt: papers survived untested claims that were framing, and aged badly when the untested claim was the one practice turned on (Ray's coupling, Dynamo's window). Rank your gaps by that criterion, not by count.

## 3. The null-idea test and isolation designs

**The test.** For each experiment, imagine the central idea false and every other piece of engineering unchanged. Would the result change? If not, the experiment tests the system. Spark's logistic-regression speedup survives removing lineage (the job would just need checkpointing). MapReduce's backup-task run changes (44% slower without them). Spanner's "1D" row, with commit wait disabled (9.4 ms vs 14.4 ms), changes, so it tests the mechanism's cost, though not its benefit. FlashAttention's Fig. 2 changes and is the cleanest idea test in the corpus.

Run it in writing: for each abstract number, write one sentence naming what else in the build could have produced the number. If you can write that sentence easily, a reviewer can too.

**Isolation designs, ordered by how directly they test attribution.**

1. **Toggle the mechanism inside the same system.** MapReduce backup tasks off (+44%) and 200 workers killed (+5%); TVM fusion and latency hiding on/off; Pathways' parallel-dispatch ablation; Borg's scheduler with and without caching, equivalence classes and relaxed randomization ("a few hundred seconds" vs "did not finish after more than 3 days"). Cheap and decisive about whether the mechanism matters *here*; silent about whether the idea beats a different design.
2. **Decompose the headline.** Spark Fig. 9. Doing it yourself buys credibility, but only if you then move the idea's claim onto its share. Spark decomposed and still led with 20×.
3. **Decouple the proxy from the outcome.** FlashAttention: more FLOPs (75.2 vs 66.6 GFLOPs), far less HBM traffic (4.4 vs 40.3 GB), faster (7.3 vs 41.7 ms). One table refutes the field's proxy and supports the paper's. The block-size sweep showing runtime tracks HBM accesses "up to a point" marks where the model stops predicting. A "we optimized the right metric" paper needs both halves: the split, and the place where the split stops holding.
4. **Measure the mechanism in isolation, then the whole.** TensorFlow's null-model benchmark isolates queue-based coordination; PyTorch traces show the host running about 3× ahead of the GPU, which neutralizes "Python is slow".
5. **Compare against the idea-removed design**: what a competent engineer builds in a week without the idea. vLLM with a growing contiguous allocator; Hadoop plus caching for Spark; equal-budget combinatorial search for AlphaTensor. The classics mostly lacked this, and it is where critics struck: flip graphs reproduced most of AlphaTensor's table on a small budget within two months; DeWitt and Stonebraker attacked MapReduce on the omitted DBMS comparison.

Designs 1–4 are internal and cheap; design 5 answers the question reviewers and critics actually ask. When budget allows only one expensive experiment, it usually belongs to design 5 on the claim the paper cannot lose.

**When the work dissolves a tradeoff.** When what the work changes is a believed tradeoff, the decisive measurement is on the side that was supposed to lose: PyTorch's "within 17% of the fastest framework", Spanner's commit-wait cost, Pathways' single controller at parity. Measuring only the side you were expected to win confirms nothing the reader doubted.

For the common production case, a headline number from a rollout that bundled several changes, see the worked example in `worked-transformations.md` C5.

## 4. Kinds of evidence, and stacking them

Each kind establishes one class of claim and is blind to another.

| Kind | Establishes | Blind spot | Use when | Cases |
|---|---|---|---|---|
| Microbenchmark vs hardware limit | The design wastes little | Whether another design wastes less | No honest competitor exists | GFS Fig. 3 (reads at 75–80% of link limit; writes about half, with the reason) |
| End-to-end vs baselines | Magnitude in a regime | Attribution; baseline construction | Comparable systems can be run fairly | FlashAttention vs MLPerf record; TensorFlow vs Caffe/Neon/Torch |
| Production telemetry | Works at real scale; real workload shape | Counterfactuals; attribution | Authors operate it; claim is existence or behavior under load | Dynamo Fig. 4; Bigtable and MapReduce fleet tables |
| Simulation / trace replay | Counterfactual policies at scale | Whatever the model omits; a model evaluated against itself | Live experiments impossible; workload can be held fixed | Borg's Fauxmaster (real checkpoints, production code); Omega |
| User study | A human-facing property | Representativeness; experimenter bias | The property is the contribution | Raft (43 students, two schools, bias table) |
| Theory / proof | Guarantees; asymptotic cost | Model-to-machine gap | A guarantee is the product, or a clean cost model exists | Spanner's four-line proof; FlashAttention's bound; Raft's TLA+ |
| Verifiable artifact | Correctness and novelty of outputs | Efficiency and necessity of the method | Outputs are formal objects with a cheap checker | AlphaTensor factorizations; FunSearch programs |
| Adoption | A tool or model is useful | Why; whether the idea is the reason | Adoption exists and the venue credits it | PyTorch's arXiv share; MapReduce's 395 map implementations |
| Parity | The generality is free | The benefit the generality buys | Contribution is an abstraction whose benefit is flexibility | Pathways Table 1; PyTorch within 17% |
| Effort / size proxy | Simplicity, expressiveness | Real cost to real users | Supporting color only | MapReduce 3,800→700 lines; Raft 4 message types vs 10 |
| Failure stories, incident counts | The theory/practice gap | Generality | Experience papers | Paxos Made Live (categorized incidents over 100 machine-years) |
| Design evolution | Choices were earned | Alternatives never tried | Dead-end records exist and can be disclosed | Dynamo's three partitioning strategies; Raft's ranking election |

### 4.1 Stack kinds so one can fail

When a reviewer discounts one kind, a second should still hold the claim. GFS defends its single master with a benefit argument, guards, memory arithmetic (under 64 bytes of metadata per 64 MB chunk), a measurement (200–500 ops/s) and related work. Raft's reviewers disliked its user study; the TLA+ spec, proofs and election benchmark carried the paper anyway. Ask of the claim the paper cannot lose: if the strongest evidence kind is rejected outright, what is left? If nothing, add a second kind before submission.

### 4.2 Parity evidence and priced principles

Parity is a genre the advice literature does not name. Pathways' main result is that nothing was lost: a single-controller runtime matches multi-controller JAX. A parity result proves the cost side and leaves the benefit side to case studies (TensorFlow §4), adoption (PyTorch) or future work (Pathways). Say in the text which side is evidenced; Pathways would have read more honestly as "flexibility costs nothing for current workloads" with its motivating workloads labeled as unevaluated.

A design philosophy becomes evidence once it is priced. PyTorch wrote "trading 10% of speed for a significantly simpler model is acceptable; 100% is not", so "within 17% of the fastest" reads as the principle's evaluation rather than as a loss. To price a principle: state the threshold before the measurement, in the middle layer where the principle lives, then measure against it. An unpriced principle ("simple", "flexible") cannot be refuted and therefore cannot be supported.

### 4.3 Making production telemetry experiment-shaped

Telemetry alone shows that something happened, not why. Borg shows how to give it experimental shape, and each move transfers:

- **One metric that makes heterogeneous policies comparable.** Cell compaction (the smallest cell that still fits the workload) turns sharing, cell size, bucketing and reclamation into "fewer machines".
- **Distributions across many units with a stated selection rule.** CDFs over 15 cells; the selection rule is what stops the reader suspecting cherry-picking.
- **A counterfactual by replay** where live experiments are impossible, with fidelity designed in (real checkpoints, production scheduler code) so the simulator is not evaluating a model against itself.
- **A control.** Borg reports the Borglet's own CPI (1.43 shared vs 1.20 dedicated) beside the workload's (1.58 vs 1.53), and calls the result "not clear-cut".
- **Stated biases.**

Dynamo is the contrast: 30 days of 99.9th-percentile latency at (3,2,2) holds nothing fixed, and its one comparative datum (client-driven coordination, 99.9th read 30.4 ms vs 68.9 ms) is its most convincing. In internal material, the experiment-shaped telemetry often already exists as staged rollouts, canaries and A/B launches; these are natural experiments if exactly one thing changed between arms (§10).

### 4.4 Theory where a claim rests on it, with its limit shown

A theorem or formal model belongs in the paper when a claim rests on it. Definitions that restate prose, notation introduced to look rigorous, and a model from which no result follows are artificial formalization, and reviewers read them as such. Venues that pay in theory (NeurIPS especially) value a theorem that carries a claim, and it is honest only if the paper shows where the model stops predicting. FlashAttention scoped its lower bound ("for all M in [d, Nd]") and its Fig. 2 middle panel shows the bend; FlashAttention-2 later won on factors outside that model, so a claim that the model fully explains runtime would have aged badly. Spanner proves external consistency in four lines but argues the clock bound socially ("bad CPUs are 6 times more likely than bad clocks"). Write the proof's assumptions next to the measurement that tests whether the hardware meets them, and put the place where model and machine diverge in a figure, not a footnote.

### 4.5 Non-performance properties

Raft's 2012 submission claimed understandability with what reviewers called "essentially no objective evidence". The 2014 version built a proxy (a two-school study, paired quizzes, 33 of 43 scored higher on Raft), disclosed its bias in a table and its confound (15 of 43 had prior Paxos experience), and backed it with conventional evidence. Raft's dissertation also says the study tests "the start of the learning curve", which is the NSDI Frontiers move: say what can and cannot be evaluated. The recipe is four parts: proxy, disclosed bias, a claim narrowed to what the proxy measures, and a second evidence kind so the paper stands if the proxy is discounted. Even then, venue matters: Raft went to ATC after several attempts, and reviewers did not value understandability "per se" (§7).

## 5. Baselines

### 5.1 Taxonomy and what each did to the paper

| Situation | Cases | Consequence |
|---|---|---|
| None | GFS, Bigtable, Dynamo, Borg, HDFS | Accepted when the claim was existence or disclosure and the institution supplied credibility |
| Self only (variants, ablations) | Spanner "1D"; Dynamo strategies 1–3; Borg via Fauxmaster | Tests mechanism cost or policy choice, not superiority |
| Reimplemented competitor | vLLM's Orca Max/Pow2/Oracle; Pregel and HaLoop as RDD libraries | Necessary when the competitor is closed, but the reimplementer controls the regime |
| Old or weak | Spark vs Hadoop 0.20; FunSearch vs first-fit/best-fit; Ray vs BSP-style MPI | Works while it is the field's reference point; ages fast |
| Different hardware or a published record | MapReduce vs TeraSort record (1057 s vs 891 s, different machines); Pathways vs Ray v1.3 | Context, not comparison |
| Strong and standard | FlashAttention vs MLPerf 1.1 BERT record (15% faster); TensorFlow vs Caffe, Neon, Torch; Paxos Made Live vs the same service on 3DB | Most persuasive, rarest |

### 5.2 Apply strict standards per claim, not per paper

Heiser's benchmarking crimes (selective benchmarking, microbenchmarks as overall performance, no real baseline, only evaluating against yourself, a simulator evaluating a model against itself) are violated by most of the canon. The classics still held, for three reasons you can check against your own claims: the claim was not comparative (GFS: "our design wastes little", with the hardware limit as the reference point); no baseline could exist at that scale (nobody could run another cluster manager on Google's cells); or the value was disclosure and vocabulary, not ranking (Dynamo's contribution sentence is "the evaluation of how different techniques can be combined").

Where weak baselines did cost a paper in these cases, it was on a comparative claim: Spark's baseline aged with Hadoop 0.20; vLLM left the growing-allocator question open; AlphaTensor's missing equal-budget search let flip graphs reframe "beyond human intuition". So go through the claims ledger and set the baseline bar per row: full Heiser standard on magnitude and attribution rows, a hardware limit or self-variant on existence rows, a proof on guarantee rows.

Heiser's *conservative* criterion (no hidden degradation) is the half the best classics honored, and it is cheap. TensorFlow admits Neon wins on three models and explains why; vLLM reports its kernel slowdown; FlashAttention its 4% loss to Apex FMHA at N=128; Pathways that JAX wins the OpByOp case. Reporting where you lose is what lets the headline stand.

### 5.3 Disclose unfairness in the sentence that uses the baseline

vLLM explains that Orca is closed and that Oracle is an infeasible upper bound. Ray's footnote concedes experts would use asynchronous MPI primitives, and its Clipper comparison notes the setting is "often the case for RL applications but not for the general web serving workloads". TVM notes Tensor Comprehensions "is not yet meant" for the compared use. Disclosure does not fix the baseline; it moves the judgment to the reader with the facts in hand. The test for each baseline: who configured it, and would its own designers accept the configuration?

### 5.4 The strongest simple alternative

For each comparative claim, write down the alternative a skeptical engineer would build in a week without the idea, then measure it or argue explicitly why it fails. Ask once per adjacent community (database, compiler, OR, theory, practitioner) what they would use instead. In the cases: a growing contiguous allocator (vLLM), Hadoop plus caching (Spark), equal-budget local search (AlphaTensor), a stitched Horovod plus Clipper pipeline (Ray), a parallel DBMS (MapReduce), warm-started stochastic superoptimization (AlphaDev).

AlphaDev is the instructive positive case. It put that comparison in the main text and conceded where the simpler method (AlphaDev-S) is more efficient. That bought reviewer credibility and did not stop the public critique, which attacked significance ("one MOV"), not method. Baselines answer method objections; they do not answer significance objections. Simulate both critics: the one asking "would something simpler do this?" and the one asking "does this matter?" The second is answered by the problem and what the work changes in the field (SKILL.md stages 2 and 3), not by more baselines.

### 5.5 Constructed comparison tables

A design-space table whose axes the authors chose is evidence only if you could lose on some row; the checks are in `argument-architecture.md` §8 (positioning tables).

## 6. Limitations, scope, lessons, and unfinished evaluations

### 6.1 Placement

| Placement | Examples | Effect |
|---|---|---|
| Inline, attached to the claim | GFS "writes are slower than we would like"; TVM's Amdahl's-law sentence after the 40× FPGA result; Spanner "out of expediency" | Credible and cheap; skimmers miss them because nothing aggregates them |
| Scope section | RDD §2.4 "Applications Not Suitable for RDDs"; vLLM §8 ("may rather degrade" for training); FunSearch's three conditions | Strengthens generality by stating preconditions |
| Lessons / experience | Bigtable §9; Borg §8; Paxos Made Live | Converts mistakes into transferable content |
| Future work | HDFS's "our assumption that applications will create a small number of large files was flawed" | Honest but buried |
| Absent | GFS failover (an hour in practice, per a later interview); AlphaDev's LLVM patch restrictions; AlphaEvolve's evaluator exploitation | Supplied later by critics |

State the decisive limitation at the claim and in the scope paragraph, for the reader of that claim and for the skimmer; minor caveats once, or not at all. Do not repeat a qualification across sections. Before adding any caveat, ask whether resizing the claim would make it unnecessary; a right-sized claim needs little qualification, and a caveat repeated in several places reads as defense rather than candor (`constructive-narrative.md` §5).

### 6.2 Candor about everything except the decisive gap

The recurring pattern: papers are candid about costs that do not threaten the headline and quiet about the gap that does. Spanner is candid about double logging and silent about the missing wide-area microbenchmark; vLLM is candid about kernel cost and quiet about the baseline regime. The draft test: name the limitation that, if a reviewer raised it, would most reduce belief in the abstract's first claim. If it holds, shrink the claim until it no longer threatens it, or check that the paper states it once, plainly, near that claim. A paper full of small admissions and missing the large one reads as candid only until someone finds the large one. Honesty about the decisive limitation is not defensive qualification: one plain statement where it changes belief is information.

### 6.3 Scope statements

A scope statement is a generality claim with its domain written down. Work out the non-applicability conditions before writing the generality claim, because they size it; in the paper, state the conditions once, positively where possible, as the conditions under which the idea works. `finding-the-contribution.md` §8.3 covers how (RDD §2.4, vLLM §8).

### 6.4 Assumption-bound versus universal claims

Claims that aged badly fall into two groups. Assumption-bound claims that stated their assumptions aged legibly: GFS's single master, record append and 64 MB chunks were all revised, but because §2.1 listed premises with numbers, a reader can see which one broke. Claims stated as universal, or attributed beyond the evidence, needed correction by others: Brewer re-scoped TrueTime's role in availability ("it is really Google's wide-area network"); Bigtable's "single-row transactions suffice" was reversed by Megastore and Spanner; "beyond human intuition" fell to flip graphs. The practical move: tie each strong claim to the layer its evidence supports. Spanner's proof supports "TrueTime enables external consistency"; nothing in its evaluation supports a role in availability, which is the part that needed correcting. For each strong claim, ask whether the next version could change the supporting mechanism without changing the interface; if so, report it as an implementation choice with its measured effect (Ray's GCS).

### 6.5 What makes a lesson worth printing

A lesson earns space when it is tied to a decision that was reversed or kept for a stated reason, written as claim → system-specific evidence → consequence: Bigtable's membership protocol, rewritten until it depended "solely on widely-used Chubby features"; Paxos Made Live's categorized incidents; Borg's lessons, bad ones first, each mapped to a Kubernetes design. Borg's lessons carry no data and hold because the authors are trusted and Kubernetes later worked. A lesson with neither data nor a successor reads as opinion, and the operational tracks reject deployments that "do not reflect deeply". Prefer lessons that challenge a premise over lessons that only motivated a choice; the cases keep the latter and omit the former, so ask the authors directly.

### 6.6 Unfinished evaluation

When experiments have not been run, write the evaluation as the questions it will answer and the experiments that will answer them: the claim each tests, the baseline, the regime, and what result would refute it. Do not write numbers, trends, or outcomes that have not been measured, even as placeholders in result-shaped prose, and do not substitute a walkthrough of the design for evidence. The abstract and introduction then claim only what is built or argued. Mark planned experiments as planned in the claims ledger (status "future"), so that trial by abstract (`finding-the-contribution.md` §9.2) does not count them as evidence. An open evaluation stated this way is part of an honest research account; it is not a gap to be papered over (`constructive-narrative.md` §10).

## 7. Venue currencies

| Venue | Currency | What the cases show |
|---|---|---|
| SOSP / OSDI research | A transferable abstraction or design point, made real by a system, with evaluation | TVM's arXiv "stack" became the OSDI "Automated" compiler, adding XLA, TFLite, TC and ACL baselines after review; vLLM's OS analogy is native currency |
| SOSP / OSDI, industry experience | Deployment credibility plus a teachable design point | GFS, MapReduce, Bigtable, Dynamo, Spanner: fleet numbers stood in for baselines |
| NSDI | Clear mechanism plus strong evaluation | RDD: definition, mechanism, evaluation, generality argument |
| EuroSys | System descriptions with a quantitative backbone; experience | Omega (taxonomy plus simulation) and Borg (retrospective) two years apart |
| USENIX ATC | Practice-oriented work | Raft, after "several attempts" |
| MLSys / SysML | Design bets; closed systems tolerated; parity as a result | JAX in three pages with four demos; Pathways with no code release |
| NeurIPS | Method plus theory plus quality results | FlashAttention's theorem and Path-X result; PyTorch accepted on adoption by analogy to a software track |
| Nature | Significance for non-specialists; verifiable results; replication via Methods and SI | AlphaTensor, AlphaDev, FunSearch push the instrument to Methods, ablations to SI |
| White paper / tech report | Capability announcement, breadth | AlphaEvolve: system catalog first, one ablation figure; TensorFlow 2015 had no evaluation |
| Operational tracks (OSDI '26, NSDI '27) | Deployment setting, scale, and lessons; novelty not required | Formalizes what Dynamo, Bigtable and Borg did informally |
| Workshop (HotCloud, PDSW and similar) | An early idea with a first signal | Spark's HotCloud paper needed a new framing more than new experiments to reach NSDI; RADOS, the layer that lasted, landed at PDSW |

Treat this as a starting hypothesis. Read three recently accepted papers at the target venue and track (a) what counts as a contribution, (b) where related work sits, (c) which evidence kind carries the main claim. Note the CFP's track language too: the operational tracks say papers "need not present new ideas", and NSDI's Frontiers track asks authors to say what can and cannot be evaluated.

**Evidence moves more than prose across venues.** TensorFlow's whitepaper ("a future version... will have" an evaluation) gained four experiments and one foil for OSDI. vLLM's launch blog reported 24× over HuggingFace and 3.5× over TGI, what practitioners ran; the paper reported 2–4× over FasterTransformer and reimplemented Orca, what researchers cite. Both are defensible, but the blog set expectations the paper doesn't support. Venue norms also decide where evidence sits physically: Nature's tiers put AlphaTensor's strongest evidence that the engineering mattered (rank 64 without synthetic demonstrations vs 49 with) in the SI behind a prose "substantially outperforms". That tiering works only because outputs are checkable without the system; in a systems venue the mechanism and its evidence are the main text. When translating between venues, re-sort evidence by claim weight, not by the source venue's convention.

**Choose the venue by the evidence you hold.** Raft held a user study and proofs and met systems reviewers who wanted novelty "in some other dimension". PyTorch held adoption and went where reviewers accepted a software-track analogy. Pathways held parity on a closed system, and MLSys accepted it. If the frame you prefer needs a currency you don't hold, the options are to produce that evidence, change the frame, or change the venue. Pick deliberately rather than submitting and learning it from reviews.

## 8. Contribution types and the evidential debt each owes

| Type | Debt | Typical unpaid part | Cases |
|---|---|---|---|
| New abstraction / programming model | Ablation showing the restriction makes X possible; expressiveness by reimplementation with a rule for "express"; parity on performance | The benefit to programmers, asserted | MapReduce, RDD, TensorFlow OSDI, Ray, JAX |
| Design point under new assumptions | Measured premises; percent-of-limit; arithmetic | Premises are one scale regime's snapshot | GFS, Ceph |
| Synthesis plus experience | Telemetry against a stated contract; design evolution; lessons | No attribution; the contract's hardest property unmeasured | Dynamo, Bigtable, Borg, Paxos Made Live |
| Non-performance property | Proxy with disclosed bias plus conventional evidence | Proxy contested; early learning curve only | Raft, PyTorch |
| Algorithm-in-system | Invariant and proof; the mechanism's cost; proxy validation | Model-to-hardware gap; the regime the title promises | Spanner, FlashAttention, CRUSH |
| System as instrument for discovery | Verifiable outputs; hit rates over runs; equal-budget alternative search | Method claims backed only by output claims | AlphaTensor, AlphaDev, FunSearch, AlphaEvolve |
| Design space / taxonomy | Sweeps across the space, checked by a high-fidelity replay | Axes chosen so the proposal wins | Omega |
| Parity / architecture | Parity with the best public baseline; public configurations; worst cases | The motivating capability left as future work | Pathways, TensorFlow §6, Ray building blocks |
| Reimplementation / documentation | Operational data; candid divergence from the origin | No thesis; lineage unstated | HDFS |

Discovery papers owe two debts. Output claims ("a rank-47 scheme exists") are paid by the artifact. Method claims ("RL beats search", "every component matters") need equal-budget comparisons, complete ablations, hit rates (FunSearch reports 4 of 140 cap-set runs) and published negatives, as Tao et al. later supplied by listing all 67 problems including failures.

A paper often mixes types. Name the type of the claim the paper cannot lose, and pay that debt first. Dynamo states its type outright, once ("the evaluation of how different techniques can be combined"), which makes clear which debt applies. If claims need different evaluation environments (simulation vs failure injection vs trace runs), that is a signal to split papers, as Ceph did with CRUSH, RADOS and the integrating OSDI paper.

## 9. Maturity, openness, and authority

### 9.1 Forward-looking versus retrospective

Omega (2013) and Borg (2015), by overlapping authors at the same venue, show the asymmetry. A forward-looking paper picks one idea, lays out a design space and simulates across it; it may advocate because its evidence is about the space. A retrospective holds production data and policies whose value is distributed, so it unifies by a metric or by the reader's questions, and its distinctive evidence is the lesson. Forward-looking claims are predictions and should be worded as such: Omega's next-generation architecture was folded into Borg, which reports "scalability has not yet been a problem".

The younger the system, the more evidence should rest on the WHAT layer (interfaces, invariants, cost models, requirements, mechanism ablations, parity) and the less on HOW-specific numbers the next version will change. Spanner launched with one customer and chose a guarantee with a proof, which does not depend on maturity, plus the mechanism's measured cost. Ray committed early to an implementation (the GCS on the critical path) that its own later whitepaper inverted. For an early system, label each claim measured or bet; for a mature one, telemetry and lessons become available currencies.

### 9.2 Openness

**Closed system: the paper is the artifact.** Nobody can check what the paper doesn't show. Pathways' compensation kit, all of which transfers: parity against the best public baseline (JAX); public model configurations (T5, GPipe-style, GShard-like) so numbers are bounded by known quantities; parameterized microbenchmarks that yield a rule of thumb (parity at 2.3 ms per computation on 16 hosts, 35 ms at 2048 TPUs); traces; worst-case disclosure; the list of properties the closed substrate must provide; and a claim that another system (Ray) could host the design, which detaches the idea from the substrate. Its remaining ingredient, the authority of its authors, does not transfer. Dynamo adds one more: say what was withheld and why (business interests forced aggregate-only numbers).

**Open system: paper and release must agree.** Code supplies credibility and creates an obligation. vLLM's shared-prefix results (1.67×, 3.58×) were not in the v0.2.0 tag two weeks after the arXiv posting. Pin the release the paper describes; check each evaluated feature against it. Moving adoption-driving material (model zoo, support matrix) to docs is a reasonable division of labor if the paper's claims stay within the release.

**Checker released, generator withheld.** AlphaTensor released factorizations and recombination code but no training code or network; FunSearch released the evolutionary core and discovered programs but not the LLM or distributed layer; AlphaEvolve's Colab "does not contain the code to run AlphaEvolve" and omitted matched cases "to avoid clutter". This is coherent for output claims: a rank-47 decomposition is correct or not, whoever produced it. It fails for process claims: method necessity (flip graphs matched most of AlphaTensor's table without a network), component contribution (AlphaEvolve's under-ablated parts had to be guessed by OpenEvolve), breadth ("about 75% matched, about 20% improved" from a curated set with no public denominator), and evaluator integrity (Tao et al. found evaluator exploitation neither paper discussed). Before withholding the process, sort the ledger into output claims and process claims. Process claims need the process, or in its place equal-budget comparisons, a complete ablation grid, hit rates and published negatives. The split rarely applies to ordinary systems papers, whose claims are about system behavior: there, withholding the system withholds the evidence.

**No paper at all.** XLA appears in the literature mainly as a bar in competitors' figures, configured by those competitors, and a 2023 third-party paper's main contribution was describing its fusion from source. Without your own evaluation, someone else's baseline configuration becomes your published performance. If the authors are weighing "blog only", say this cost out loud.

### 9.3 Institutional credibility versus conceptual evidence

Dean and Ghemawat could write "hundreds of programs" and show a usage table, and nobody asked for external validation. Zaharia's group had a prototype and a few collaborating users, so the RDD paper bought generality with conceptual evidence: the DSM contrast, narrow/wide dependencies, Pregel and HaLoop rebuilt as 200-line libraries, a stated boundary, and an explanation of why restricted transformations suffice. TVM's "in production use inside several major companies", with no detail, carries no weight. Ask honestly whether this venue will believe these authors on deployment claims. If not, the paper needs evidence that stands without that belief: ablations, honest baselines, decomposed speedups, scope statements. Every deployment claim needs either the restriction under which it shipped (AlphaDev's LLVM patch was limited to arithmetic types with simple comparators) or one concrete deployment fact. Even with credibility, adoption does not rebut critics: Bigtable's adoption did not answer the transaction complaints Spanner later cited, nor MapReduce's the DBMS critique.

Specific behavioral anecdotes are the strongest form of use evidence (Levin and Redell's users who "hang their wet shirts over the tape drives"): MapReduce's indexing change going from months to days; F1's last manual MySQL resharding having taken "over two years"; Borg users encoding topology in job names. Mine tickets and migration notes for these.

## 10. From a pile of docs and logs to an evidence plan

This assumes a candidate thesis from earlier stages. Context comes before experiments because it decides which evidence is worth gathering.

**1. Fix the context and write it as an overturnable assumption.** Venue and track; contribution type of the claim the paper cannot lose (§8); maturity (design-stage, launch, retrospective); openness (closed, code released, checker only); authority for deployment claims. From these, write the currency the context accepts (§7). The authors may overrule it; that is the point of writing it down.

**2. Build the claims ledger with types** (§1), including inferred title-word claims, and mark the one claim the paper cannot lose. Claims whose experiments are only planned get status "future" (§6.6).

**3. Inventory evidence that already exists.** Internal material holds more than benchmark logs, and the most persuasive items are often not labeled as evidence:

| Source | Can carry | Watch for |
|---|---|---|
| Staged rollouts, canaries, A/B launches | Attribution, if one change shipped per stage | Confounded rollouts (two changes at once) |
| Config history | Which knobs users actually set; a configuration taxonomy (Dynamo's (3,2,2) as common setting) | Defaults mistaken for choices |
| Postmortems, incident counts | Theory/practice gaps; failure-mode categories; failures that challenge premises | Only incidents that motivated a choice survive in docs |
| Design reviews, RFCs, reverts | Alternatives register entries, documented history | Mixing reconstructed alternatives into history |
| Dashboards, fleet distributions | Existence at scale; workload premises with numbers | No selection rule; nothing held fixed |
| Migration notes | Before/after effort for use claims | Effort counts presented as the only evidence for simplicity |
| User tickets, internal Q&A | Specific behavioral use evidence | Anecdote without a count |
| Specs, model-checking runs | Guarantee claims | The gap between spec and code |
| Benchmark notebooks | Magnitude; sometimes a surprising proxy/outcome split | Baseline configured by whoever wanted to win |

Map each item to a ledger row. Unmapped evidence is a cut candidate or a sign of a missed claim; unmapped claims are the gaps. The mapping is private: the paper presents each piece of evidence where its claim is made, not as a table of correspondences.

**4. For each gap, choose one of three moves.** (a) Run the isolating experiment (§3), with the idea-removed baseline and the strongest simple alternative. (b) Rescope: write the condition into the claim's own sentence. (c) Move the claim to Discussion as a stated bet with the reason it is plausible. Rescoping is usually cheaper than an experiment and better than an assertion; most of the claims that later needed correction (§6.4) needed move (b), not more data. Move (c) is what Pathways should have done with its motivating workloads.

**5. Spend experiments on the decisive uncertainty.** Rank open gaps by how much belief in the abstract's first claim would drop if a reviewer raised them. Attribution on that claim and the strongest simple alternative come first; breadth comes last. One decoupling table outweighs ten end-to-end bars. Don't spend a week tightening confidence intervals on a number no one doubts.

**6. Add the conservative side and place limitations by threat** (§5.2, §6). Every benefit gets its measured cost. The decisive gap goes next to the claim it qualifies (and in the scope paragraph); preconditions go once, in the scope paragraph; minor caveats appear once, inline, or not at all. Resize first: a claim sized to its evidence needs few caveats.

**7. Reconcile numbers across artifacts.** List every public number for the system (blog, README, talks, earlier preprints, the paper) with the baseline and regime each answers. Where two differ, either make them agree or state in the paper which baseline each answers (vLLM's 24× vs 2–4×). Check each evaluated feature against the cited release and each deployment claim against the record. An inconsistent number found by a reviewer costs more than the gap it was meant to hide.

**8. Check the whole.** A fully mapped ledger can still describe the wrong problem: TensorFlow's OSDI evaluation was correct and its bet on deferred execution was not what drove adoption. Ask: if every test here passes, what could I still be wrong about? Typical answers are the problem definition, which property users actually care about, and which premise is about to change. Put the answer in the discussion, and if it touches the problem or the thesis, take it back to SKILL.md stages 2–4 rather than patching the evaluation.

For worked examples of this plan applied to real cases, see `worked-transformations.md` move 11 (Borg: telemetry made comparable by one metric) and C3 (Spark: lineage evidence in proportion to its place in the title).

## 11. Assumptions and limits

The venue table and the per-venue norms rest partly on CFP text and partly on secondhand readings (EuroSys and SOSP calls were seen only as snippets or summaries); verify against the current call. Several classics succeeded despite weak evidence because of authority, adoption or timing, so "they got away with it" is weak license for a new group. The rules above about per-claim baseline strictness and checker-only release are inferences from where critics struck, which is stronger evidence than where papers succeeded but still covers a small set of cases.
