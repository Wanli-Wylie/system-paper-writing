# Argument architecture

Read this when you have a candidate thesis and opponent (see `finding-the-contribution.md`) and must now build the path from that thesis down to evidence: outlining, restructuring a draft whose sections follow the code, or deciding where a mechanism, table, figure, or example goes. Everything here serves one object, the causal path spine → middle layer → mechanisms → capabilities → evidence. The devices are means. A paper can use all of them and still read as documentation if the path is missing, and it can skip most of them (Borg has no running example; Spanner's covers one subsection) if something else carries the path.

## Contents

1. Altitude, the two poles, and the adjacent-layers rule
2. Spine types and what each generates
3. The middle layer
4. Chain shapes: rendering design → mechanism → capability
5. Mechanisms: who earns space, demotion, the implementation section
6. Ordering
7. Indexes that must agree
8. Tables
9. Named vocabulary
10. Running examples
11. Condensed artifacts
12. Paragraph and sentence moves
13. Figures
14. The first page
15. Papers without a single spine

---

## 1. Altitude, the two poles, and the adjacent-layers rule

A large system can be described at five altitudes: **thesis**; **middle layer** (what any solution here must satisfy); **mechanisms**; **implementation** (language, size, kernels, substrates, defaults); **operations** (deployment practice, tuning, tooling). The catalog pole sits at implementation and operations with every component at the same depth (HDFS §II-III: one subsection per component, "The default heartbeat interval is three seconds", "Good practice is to create a daily checkpoint", no "why not X"). The vapor pole sits at the thesis with the mechanism missing: Pathways argues MoE, sparse routing and multi-tenancy at concept level and leaves them as future work, while its evidence is parity with multi-controller JAX.

**Locate your draft between the poles.** Tag every paragraph of the outline with its altitude. Catalog drafts have most paragraphs at implementation or operations and none at the middle layer. Vapor drafts have capabilities claimed at thesis altitude with no paragraph at mechanism altitude that a reader could re-implement, while the evaluation sits at implementation. Both can occur in one draft, in different sections.

Acceptable altitude depends on venue. JAX's three-page SysML paper (a concept plus four demos) suited an inaugural venue and would not pass at OSDI. A position paper or white paper may sit higher than a research-track paper; it then has to say so.

**Adjacent layers only.** Describe each layer through the layer directly above (why it exists) and the layer directly below (what it is made of), and nothing else. Bigtable describes GFS and Chubby only through the interface Bigtable uses. Spanner describes the spanserver stack only to the depth the timestamp argument needs. vLLM describes its kernel only to the depth the block table needs. To check a paragraph, name the layer it serves and the layer it describes. If they are two apart (a capability paragraph explaining a config flag; a thesis paragraph quoting kernel launch details), the paragraph has left the argument. Send it to an appendix, a companion document, or one sentence.

The rule holds inside a section too. A mechanism paragraph should mention the capability it enables and the structure it is made of, not the tuning parameter beneath that structure.

---

## 2. Spine types and what each generates

The top slot is often filled by something other than an "idea" in the mechanism sense, and each kind builds a different architecture below it. The type is a decision about where the novelty actually is, not a style. Most spine types are the concept kinds of `finding-the-contribution.md` §4 seen from the outline (a requirement spine comes from a "requirement no one meets" opponent, §1.1 there): that section says what each kind needs to be believed and how it fails as a claim; this table says what structure each generates and how that structure typically fails.

| Spine type | Cases | What it generates below | How the structure typically fails |
|---|---|---|---|
| Restriction on users | MapReduce, RDD | Restriction → guaranteed property (determinism, lineage) → runtime techniques the property makes legal | Restriction that buys nothing measurable; no "not suitable for" boundary |
| Small interface | Spanner (TrueTime), Bigtable (sorted map), AlphaEvolve (EVOLVE blocks + `evaluate`) | Features re-derived one at a time as consequences | Over-attribution: features credited to the interface that rest on other mechanisms (Spanner vs Brewer 2017) |
| Problem formulation | AlphaTensor (TensorGame), AlphaDev, FunSearch, JAX (PSC) | Components introduced as answers to the formulation's difficulties, each difficulty named before its component | The formulation itself can die (FunSearch's "function space", JAX's PSC) |
| Imported analogy | vLLM (blocks as pages, tokens as bytes, requests as processes) | Mechanisms named in the source field's terms; departures named | Hidden gaps make it a slogan; name lands on the wrong component |
| Cost metric | FlashAttention (HBM accesses), Borg (cell compaction) | The old design rewritten in the metric; mechanisms and alternatives judged on one axis | Metric stops predicting; the paper must show where (FlashAttention's block-size bend) |
| Premise register | GFS | Premise → consequence pairs, premises with numbers | A load-bearing premise left unstated (GFS: the team controlled all clients) |
| Requirement or contract | Dynamo (always-writeable, 300 ms at 99.9%), Ray | Techniques mapped to slices of the requirement, which needs a hard edge (a percentile, a guarantee) | The requirement that carries the structure is never tested (Ray's "stitching is untenable") |
| Criterion or priced principles | Raft (understandability), PyTorch | Each mechanism justified by the criterion, with a rejected alternative | Generic principles that exclude nothing; no exchange rate or instrument |
| Delta against a predecessor | TensorFlow OSDI (vs parameter server), AlphaEvolve (vs FunSearch) | Old limitations listed, each removed by one change | A rename (TF's "no parameter server" kept the topology); deltas that are scale, not concept |
| Taxonomy of gaps | Paxos Made Live | Sections by gap category, extracted abstractions | A list of anecdotes |
| Reader questions | Borg | Sections by concern, unified by a metric | See §15 |

**The spine need not be new; the chain below it must be.** FlashAttention calls its ingredients "two well-established techniques"; vLLM borrows paging wholesale; Dynamo states its contribution as the evaluation of how techniques combine. Novelty sits in what the spine makes possible here.

**Choose by which framing has the stronger chain beneath it.** Spark's code was framed three times; TensorFlow went from "interface and implementation" to "stateful dataflow that subsumes the parameter server"; TVM from "optimization stack" to "automated" optimization. In each pair the later framing generated more of the paper. Evidence from the cases:

- The TensorFlow whitepaper listed Variables as one feature and mentioned the parameter server in related work. OSDI made the PS the foil ("no such thing as a parameter server") and mutable state the answer to both "why not plain dataflow?" and "why not a PS?", which turned four features into case studies.
- Spark 2010 had three co-equal abstractions. NSDI 2012 mentions broadcast variables and accumulators zero times, because "restricted shared memory made fault-tolerant by lineage" could not generate them.
- vLLM v0.2.0: 273-line block manager, about 8.4K lines of model executor, about 3K lines of API and engine. The block manager carries §4 because only it follows from the paging analogy.

**Spine checks beyond SKILL.md's thesis tests.** *Generative:* delete the concept's name from the middle sections; if no section loses its reason to exist, the concept is a label (TVM v1's "stack"; Ray's RL framing, later dropped with no change to the architecture story). A candidate that explains one section is a mechanism, not a spine. *Priced:* it comes with a cost or a dial (Spanner's epsilon; PyTorch's exchange rate); without one it reads as marketing. *Contrasted:* a named alternative readers currently believe in. Strong papers often pair a spine with a middle layer of a different kind (vLLM: analogy plus a waste taxonomy; Borg: reader questions plus a metric).

---

## 3. The middle layer

The middle layer answers "what must any solution here satisfy?" so each mechanism reads as a forced move. Pick its form from the spine and the claim type.

| Form | Fits when | Case evidence |
|---|---|---|
| Premise register | The design is derived from workload or environment facts that may change | GFS §2.1: assumptions with numbers; when single master and record append aged, readers could see which premise broke |
| Requirement list | No component is new, or one demanding requirement is unmet | Dynamo §2.1 pre-excuses what the system does not do; vLLM §3 fixes three challenges and a waste taxonomy (reserved, internal, external) so the evaluation metric exists before the design |
| Priced principles | Tradeoff opponent, or a non-performance claim | PyTorch §3: each principle carries an exchange rate and is later cited; the version counter errors instead of copy-on-write "to avoid introducing subtle and hard-to-find performance cliffs" |
| Named invariants | Guarantee-type contribution | Spanner §4.1 (three invariants, two rules, four-line proof); Raft Fig. 3 (five properties with section pointers); MapReduce §3.3 failure-semantics contract and how non-determinism weakens it |
| One metric | "We optimized the right quantity", or a bundle of policies needing a common unit | FlashAttention §2.2 rewrites standard attention as HBM reads/writes (Algorithm 0); Borg's cell compaction turns four unrelated policies into "ways to need fewer machines" |

**The exclusion-and-citation test.** For each item, write two things: the design a reasonable engineer would otherwise choose that this item rules out, and the later paragraph that cites this item as the reason for a choice. An item with no exclusion is generic ("simple, scalable, flexible"; Dynamo's incremental scalability, symmetry, decentralization, heterogeneity are weaker than PyTorch's for this reason); sharpen it or cut it. An item never cited later is decoration; cut it. A principle that passes both is what reviewers notice: PyTorch's reviewers credited the mapping from decisions to principles with distinguishing the submission from a white paper.

**How many items.** The cases cluster at two to five. Too many: collapse to what the argument can carry (Ray's position paper had R1-R7; the OSDI paper derived three from RL pseudo-code). Too few, or too broad: a category every technique fits carries no information. The best cuts index *kinds* of mechanism: TVM v1 had four hardware-derived challenges mapped one-to-one onto features; OSDI re-cut to two (hardware features; search space), with the four techniques under the first. A good cut gives each item a different kind of mechanism and a different experiment.

If no middle layer can be written that passes the test, the problem is usually upstream: the thesis is a label (§2) or the paper has no organizing principle beyond modules (§15).

---

## 4. Chain shapes: rendering design → mechanism → capability

The shape is how sections and paragraphs carry the chain. Pick it from the spine type; if two fit, prefer the one the evaluation can mirror (§6).

| Shape | Fits spine | Paragraph pattern | Check before keeping it |
|---|---|---|---|
| Root and derived features | Restriction, interface | Root stated; each feature re-derived; abstract grammar subordinates features to the root (MapReduce: restriction → determinism → safe re-execution → fault tolerance, backup tasks, load balancing) | For each derived feature: would it disappear without the root? Brewer later attributed Spanner's serializability to two-phase locking and snapshot reads mostly to multiversioning |
| Analogy, then harder cases | Imported analogy | One mechanism applied to progressively harder cases (vLLM: parallel sampling, beam search, shared prefix) | Where the analogy breaks is named (vLLM's all-or-nothing eviction) |
| Problem, technique, advantage | Requirement, synthesis | Map table, then one subsection per row; each row "textbook version → what breaks here → our variant" (Dynamo Table 1, §4) | Each row cites a slice of the requirement |
| Premise and consequence | Premise register | Design question, choice, benefits "First... Second... Third...", cost, status note on why the cost has not bitten; napkin arithmetic (GFS in-memory metadata) | The boundary condition the derivation needs is stated |
| Limitation retraced | Delta | N limitations of the predecessor; one mechanism each (Pathways §2 → §4) | Each change is a real change, not a renaming |
| Difficulty and component | Problem formulation | Each difficulty gets a component and a justification (AlphaTensor: huge action space → sampled autoregressive policy; no known solutions → synthetic demonstrations, since multiplying out factors is "elementary"; low diversity → change of basis) | Each component answers exactly one difficulty |
| Principle, decision, alternative | Priced principles | Decision, the rejected alternative, the principle cited (PyTorch: refcount vs GC; error vs copy-on-write; one pool per stream) | The principle is cited, not just nearby |
| Metric and mechanism | Cost metric | Hierarchy, old algorithm in the metric, new mechanism, theorem (FlashAttention) | A measurement shows the metric predicting the outcome, and where it stops |

**Put the why in the sentence next to the what.** A separate "Design rationale" section detaches reasons from mechanisms and lets mechanisms appear unjustified. Ray §4.2.1 shows the paragraph form: prior lineage stores use one node because they are coarse-grained; that fails for fine-grained simulation; so lineage is sharded; scheduler-held metadata is a second coupling; so it moves to the GCS; payoff, "every component in the system to be stateless."

---

## 5. Mechanisms: who earns space, demotion, the implementation section

**A mechanism earns full treatment** if it is a link in the chain to a claimed capability (Spanner keeps directories and Paxos groups because the timestamp argument runs over them, and drops the SQL processor), has an alternative the reader expects (GFS: no client cache, 64 MB chunks), has a number attached (MapReduce backup tasks: 44% slower without), or separates you from a named rival (TensorFlow: "this used to require editing DistBelief's PS"). If it meets none, compress it to a clause.

Space does not track novelty. MapReduce's model, the named contribution, gets about 1.5 pages; the runtime and refinements get about 4.5 because the runtime is where the chain is shown. Spanner's TrueTime section is about 1.5 of 14 pages, and every other section points to it. To check dependence rather than size, count the back-references to the central concept from the other sections. A concept with many pages and few back-references is a self-contained module; one with few pages and many back-references is a spine.

**Demotion devices.** Choose by what the reader would otherwise wonder.

- *Refinements bucket.* A titled section of real, non-core optimizations keeps the core readable (Bigtable §6, MapReduce §4). MapReduce's nine refinements read as a feature list that the case file thinks only the authors' standing allowed; a group without that standing should keep the bucket short and give each item a reason.
- *Implementation quarantine.* An explicitly non-novel section of necessary facts (vLLM §5: 8.5K lines Python, 2K CUDA, three fused kernels). TVM reports "~50k LoC" once.
- *One-sentence dead end.* "An early Spanner incarnation supported multiple Paxos state machines per tablet ... led us to abandon it." Raft keeps exactly one abandoned design (ranking-based election) because it is the evidence for its principle. A dead end earns more than a sentence only when it is evidence.
- *One-sentence scope demotion* where the reader would ask (§12).

**What the implementation section may contain depends on the claim type.**

| Claim type | Role of the implementation section | Case |
|---|---|---|
| "This abstraction can be implemented at acceptable cost" | Part of the argument; its mechanisms need matching experiments | PyTorch §5 maps one-to-one onto §6.1-6.2 |
| "This abstraction enables these capabilities" | Quarantine of facts that argue nothing: size, language, substrate, lineage; short; after the mechanisms | vLLM §5; TensorFlow OSDI §5 |
| The runtime is where the chain is demonstrated | The argument itself, whatever its title | MapReduce §3 "Implementation" |
| Essential parts run on a closed substrate | Separate design from implementation so the idea does not depend on what readers cannot inspect | Pathways §6.1; §4.3 lists the properties PLAQUE must provide and argues Ray could host the design |

In every case, the implementation section must not introduce a new organizing idea. If it does, that idea is either the real spine or another paper.

---

## 6. Ordering

**Concept first or substrate first.** Ask: can the reader see what the idea removes before knowing the structures it acts on? If yes, concept first (Bigtable's data model in §2 before any architecture; vLLM's paging analogy in the introduction before any system description). If the idea's value is that it simplifies structures the reader has not met, substrate first, trimmed to exactly those structures (Spanner §2 before §3 TrueTime, then §4 reuses §2's objects). To check a substrate-first draft, list every object the substrate section introduces and confirm the idea section uses each one; unused objects are the start of a database-description paper. If the idea arrives after about a third of the paper, the substrate has swallowed it. A variant is **criterion before algorithm**: Raft §4 tells the reader how to judge the design before showing it; PyTorch §3 does the same with principles.

**Outside-in.** Show the system as users see it before internals: Borg §2 "The user perspective" before §3; Bigtable's API snippets before implementation; Ray's user code (Fig. 3), the task graph the system sees (Fig. 4), and a nine-step trace (Fig. 7). This fixes the capability vocabulary early, so "mechanism X enables capability Y" sentences are easy to write and to check. Ceph follows the data path (client, MDS, OSD) and its evaluation mirrors that order.

**Lower layers by contract, placed before they are used.** Bigtable §4 puts "Building Blocks" between the API and the implementation, so §5 can use "files in GFS" and "locks in Chubby" as primitives. What to say about a lower layer (the contract used, the one dependency quantified, when a held-fixed property is really a premise) is in `filtering-and-honesty.md` §7.

**Related work placement.** Early when the reader's existing systems define the gap (Dynamo §3 ends in four differentiators; TensorFlow OSDI §2.3; Ceph §1). Late when the difference lies in premises or an unfamiliar abstraction the reader must learn first (GFS §8, Bigtable §10). Absorbed when a "what's wrong with X" section does the positioning (Raft §3). Nature papers put it in Methods; do not copy that into a systems venue.

**Benefit, then cost.** Pair them in adjacent sections or paragraphs so the cost reads as part of the claim: Borg §5 (utilization from sharing) then §6 (isolation, the price), including a CPI analysis reported as "not clear-cut" with a control; vLLM §7 ablations after the headline.

**Evaluation mirrors the design.** vLLM §6.2-6.5 follow §4's capabilities; PyTorch §6.1 tests §5.2 and §6.2 tests §5.3; FlashAttention §4.1-4.3 follow the introduction's three bullets. TVM is the counter-example: claims organized as two challenges and three modules, evidence organized per platform, so no challenge can be checked against its evidence in one place. Organize the evaluation by the middle layer's items or the capabilities, and open it with the questions it answers (Ray's four numbered questions).

---

## 7. Indexes that must agree

Abstract, contributions list, overview figure, and evaluation questions are different cuts of one argument for different readers: the reviewer scanning for novelty, the systems reader wanting structure, the PC member checking claims against evidence. TVM's introduction holds two challenges, three modules mapped to them, four contribution bullets that are a different cut again, an abstract that adds a fourth, and Fig. 2 with section numbers on its boxes. The redundancy is useful only if the cuts agree.

Build the matrix: rows are contributions (from the abstract and introduction); columns are the sections that develop each, the figures or tables that show it, the experiment that tests it, and its claims-ledger status. Then read it three ways:

- An empty row is a contribution that is asserted.
- A section in no row is catalog material, or a contribution you have not named.
- An experiment in no row is evidence chosen because it was easy to measure.

Build it once from the outline and again from the draft. Industry classics often state the contribution in one sentence rather than a list (MapReduce, Dynamo); the list is optional, consistency is not.

---

## 8. Tables

Three jobs recur, and each has a characteristic check.

- **Map tables** show the whole before the parts: Dynamo Table 1 (problem, technique, advantage), placed between related work and architecture; Spanner Table 2 (operation types and concurrency control). Check: does the body follow the table's rows in order?
- **Positioning tables** place the proposal against alternatives: RDD Table 1 vs DSM; TVM Table 1; Omega's taxonomy; AlphaEvolve Table 1. The case files flag bias in three of these. RDD defines DSM so broadly that MapReduce plus caching is not a row, and writes "Poor performance (swapping?)". TVM's attributes are chosen so each baseline loses on one row and the proposal on none. Five of AlphaEvolve's eight rows are scale. Checks: Is the strongest practical alternative a row? Would a neutral party pick these axes? Which rows are conceptual and which are scale? Is there any row where you lose? If not, say which axes were left out.
- **Vocabulary tables** name a weakness or a dual abstraction: GFS Table 1 (consistent, defined, undefined) turns "weak consistency" into a precise middle ground; Ray Table 2 (tasks vs actors) justifies two abstractions at a glance. Use one when the honest description of your guarantee is "it depends"; the table says on what.

---

## 9. Named vocabulary

Coined terms compress mechanisms into handles: narrow vs wide dependencies, consistent vs defined, cell compaction, the waste taxonomy, stateful edges. RDD's "We found it both sufficient and useful to classify dependencies into two types" introduces one distinction that explains pipelining, recovery cost and scheduling boundaries. **A term earns its place when it explains more than one behavior.** List the behaviors each coined term explains; one behavior means it is a label.

A precise name invites precise attack, and a name can land on the wrong component ("PagedAttention" names a kernel that is slower in isolation). Naming the idea versus the artifact, and checking the name against the ablation, is covered in `finding-the-contribution.md` §7.

---

## 10. Running examples

Running examples play four roles in the cases: making an invisible abstraction visible (Bigtable's Webtable; RDD's log mining with its lineage graph as Fig. 1; AlphaTensor's Strassen 2×2 showing tensor, factors and algorithm are one object); making each mechanism fire once (vLLM); pairing user code with system behavior (Ray Figs. 3, 4, 7; MapReduce's word count from §2.1 to complete C++ in Appendix A); carrying the unflattering consequence (Dynamo's shopping cart, where deleted items can resurface).

**Choosing one.** List the on-chain mechanisms and the one cost you most need the reader to see. Draw candidates from real workloads, tests, or incident reports, not invented toys. Keep the candidate that makes every on-chain mechanism fire at least once and exposes at least one cost. Then trace it with concrete identifiers: vLLM's "Four score and seven years ago" prompt runs through Figs. 3 and 5-9 with block numbers (7, 1, 3) tracked through allocation, append, fork and copy-on-write. An example that shows only the happy path is decoration. If no single example covers every mechanism, the chain may have two spines, or a mechanism you thought was on-chain is not.

**Substitutes, when no example fits.** Invariants with a short proof (Spanner through §4). Distributions over many real instances (Borg's CDFs over 15 cells). A real configuration rather than a toy (FlashAttention's GPT-2 medium on an A100). A canonical small instance from the field (Strassen's 2×2). For a search or agentic loop, where the loop diagram is easy and the mechanism inside it is not, show a real result moving through the mechanism; AlphaEvolve omits this, and its program database is described in one sentence.

---

## 11. Condensed artifacts

Some papers give the reader one checkable object: Raft's one-page specification (Fig. 2) plus five invariants (Fig. 3); TrueTime's three-method table, whose shortness is itself the argument for simplicity; Bigtable's type signature; FunSearch's specification figure. For protocols and interfaces these are cheap to make. If your contribution is an interface, protocol, or contract and the draft has no such object, make one; if you cannot fit it on a page, the interface may not be as small as the thesis claims.

---

## 12. Paragraph and sentence moves

These moves carry structure at the sentence level. Each has a check.

- **The concept is the abstract's grammatical subject.** "a missing principle is making attention algorithms IO-aware" (FlashAttention); TrueTime "is critical to supporting external consistency" (Spanner). Compare "the architecture of HDFS". Check: count abstract sentences whose subject is the concept versus the system.
- **Claim-first paragraphs.** "Having a single master vastly simplifies our design" (GFS). A paragraph with one point declares its place on the chain in its first sentence. Check: read only first sentences; the argument should survive.
- **Contribution-type sentences** when no component is new. Dynamo: "The main contribution of this work for the research community is the evaluation of how different techniques can be combined." JAX: "as a systems research project, demonstrates the power of the PSC assumption." These tell reviewers where to look.
- **The differentia in two bullets.** "TensorFlow differs from batch dataflow systems in two respects."
- **Alternatives with one disqualifier each.** Ray §4.2.2 dismisses centralized schedulers, work stealing, Sparrow and Canary for one reason each and places its design in the remaining cell. A conjunction gap ("none difficult in isolation") is defined this way, and it then owes an experiment where composing the alternatives fails.
- **Novelty disclaimers.** RDD does "not claim novelty for the language-integrated interface"; FlashAttention's "two well-established techniques". Saying what is borrowed sharpens what is claimed.
- **One-sentence demotions placed where the reader would ask.** "We will not go into detail about the query language." "We focus here on the forward pass for ease of exposition." Dynamo §4 opens by listing production concerns it skips. Each turns an omission into a scope claim. Paxos Made Live's repeated "beyond the scope" shows the limit of the move: each use is a scope claim the reader has to accept on trust.
- **Name the reader** when it justifies the altitude: TVM writes for "how a system programmer can extend it."
- **The caveat inside the claim sentence.** TVM's Amdahl sentence right after the 40× FPGA number; AlphaEvolve's complex-valued caveat in the Table 2 caption. A caveat three pages away does not qualify the claim for a skimming reader.

---

## 13. Figures

Write the one sentence each figure argues before drawing it. If the sentence is "here are the components", the figure is inventory; allow at most one, and give it section pointers or a separation. The cases show six jobs:

1. **Roadmap.** TVM Fig. 2 labels boxes with section numbers; MapReduce Fig. 1, its only architecture figure, carries the whole data flow.
2. **Separation.** GFS Fig. 2 draws control and data flow apart, which shows the central claim that clients never move data through the master. Ray Fig. 4 distinguishes data, control and stateful edges.
3. **Thesis in one picture.** TensorFlow OSDI Fig. 2 puts input, preprocessing, training and checkpointing with state and queues in one graph; the whitepaper's counterpart was a toy `Relu(Wx+b)` fragment.
4. **Bridge: proxy against outcome, or problem before solution.** FlashAttention Fig. 2: more FLOPs (75.2 vs 66.6 GFLOPs), less HBM traffic (4.4 vs 40.3 GB), faster (7.3 vs 41.7 ms). vLLM Figs. 1-2 measure existing systems' waste (20-38% useful) before vLLM appears. Dynamo Fig. 4 (average vs 99.9th percentile) justifies percentile SLAs. Look for this figure first; it is often the paper's best evidence for the principle.
5. **Specification or interface.** FunSearch Fig. 2 (specifications for two problems) explains more than Fig. 1 (a four-box loop). RDD Fig. 1 makes lineage visible.
6. **Robustness built into the plot.** Borg's CDFs across 15 cells answer "most cells or one?"; GFS Fig. 3 plots throughput against the hardware limit when no fair baseline exists.

**Placement.** Put each figure where the claim it argues is made; GFS's figures sit where the claim needs to be seen. A bridge figure belongs before the design, since it motivates it. Low information can be deliberate: AlphaTensor Fig. 2 reduces its actor-learner system to two boxes because the system is demoted to an instrument. Read the figure claim sentences in order; they should form a compressed version of the argument.

---

## 14. The first page

Write it last, against five jobs: name the belief being displaced; state the thesis (GFS by sentences 3-4; MapReduce about 15 lines in; FlashAttention's abstract sentence 3; Ray's "we propose Ray" in paragraph 7, about 1.5 pages in, which is slow); disclose reality status (Levin & Redell's failure is fifteen pages of present tense before revealing the system is hypothetical; industry classics put scale on page 1); define the gap by a missing property (RDD gives each competitor class one missing property and offers the union); give a map when the order is unusual (Bigtable's roadmap; Borg's "organized around these topics").

**Match the opening register to the spine.** A belief-type spine opens with the belief (FlashAttention, PyTorch, Raft). A premise-type spine opens with observations (GFS). A practice-type spine opens with operating reality (MapReduce's "hundreds of special-purpose computations"; Dynamo's shopping cart that must stay writeable while "data centers are being destroyed by tornados"). An opening history of workload classes, as in Ray, delays the thesis without doing any of the five jobs.

**The stated gap must be one the mechanisms answer.** The structure follows the stated gap, so check every mechanism chosen in §5 against it. TVM motivates with portability and justifies its end-to-end design with library coverage; the same modules answer both, so it holds. Ray's "stitching together specialists is untenable" carries the structure and is never measured.

---

## 15. Papers without a single spine

Ask whether there is any organizing principle other than the code's module boundaries. Reader questions, gap categories, a requirement, or a metric qualify. Module boundaries do not, because they are the implementation's decomposition, not the argument's.

- **Borg** organizes by reader questions and works because its novelty was partly disclosure; one metric (cell compaction) unifies unrelated policies; benefit and cost sections are paired; each lesson has a fixed shape (claim, Borg evidence, Kubernetes consequence, bad before good); and EuroSys accepts system descriptions with a quantitative backbone. Omega, by overlapping authors, shows that a forward-looking design paper can pick a taxonomy and an idea, while a backward-looking system paper has to accept that the system's value is distributed. Do not borrow this form for a young system: it depends on maturity and on readers wanting to see the system at all.
- **Dynamo** has no novel component; the requirement plus the synthesis table is the spine, and a contribution-type sentence tells reviewers what to judge.
- **Paxos Made Live** organizes by gap category and extracts reusable abstractions (MultiOp, epoch numbers), which makes it more than anecdotes.
- **HDFS** organizes by component and fails. Its best content (0.8% of nodes lost per month, about 20 bad replicas per fortnight, "our assumption that applications will create a small number of large files was flawed") sits in §IV-V. The thesis it could have had was in its own data: an open implementation of the GFS design point meets a diverse user base, and here is where the inherited assumptions broke.

If your system fits none of these and no spine passes §2's checks, the honest move is a different genre (experience, operational, data) rather than an invented spine. A spine that does not generate the sections will be visible as a label within a page.

---

## Assumptions and limits

The spine types, chain shapes and figure jobs are read off influential papers; they describe what worked, not everything that could. A new system may fit no listed type, and forcing one produces a slogan. A strong architecture also makes a wrong cut more persuasive (TensorFlow's static graph, Bigtable's single-row transactions, Ray's GCS were all argued well), so treat a clean index matrix and a generative spine as evidence about the paper's coherence, not about whether the chosen cut is the one that will last.
