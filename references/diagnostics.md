# Diagnostics: reviewing and revising a draft

Read this when you have a draft, yours or someone else's, and must decide what is wrong with it and what to fix first. Symptoms are grouped by where they show up, but most of them are produced upstream: a structure symptom is often a framing defect, and an evidence symptom is often a stated gap pitched above what was measured. Diagnose broadly, trace upstream, then fix from the top down. Precedents are cases where the symptom was later attacked, corrected, or avoided.

## Contents

1. How to run a diagnosis
2. Meta-tests
3. Symptom catalog
   3.1 Framing and thesis · 3.2 Structure · 3.3 Mechanism description · 3.4 Evidence · 3.5 Claims and wording · 3.6 Honesty and scope · 3.7 Cross-artifact consistency
4. Reviewer and critic simulation
5. Prioritizing fixes
6. Failure modes of the revision process

---

## 1. How to run a diagnosis

1. **Read the whole draft once without marking anything.** Then write one sentence: what the paper claims, against which belief, held by whom. If you cannot write it, that is the first finding, and it outranks everything you will mark later.
2. **Build the working artifacts from the draft, not from the system.** Contributions list, section list, figure list, and a claims ledger with each claim's evidence and status (measured, argued, asserted, future). Reconstructing them from the draft shows what a reviewer will see; reconstructing them from your knowledge of the system hides the gaps.
3. **Run the meta-tests (§2)** before collecting local symptoms. They locate the defects that make local fixes pointless.
4. **Collect symptoms (§3) without fixing them.** Fixing as you go anchors you to the current frame.
5. **Trace each symptom upstream and group symptoms by shared cause.** Then prioritize (§5).

**When the draft is someone else's.** Separate what the draft alone can show (index agreement, wording, figure-to-claim mapping, null-idea results on reported experiments) from what needs the system or the authors: the real decision history, whether a load-bearing reason was left out, what the release contains, which experiments are feasible, which failures challenged the premises. Report the second kind as questions, not defects. A reviewer who asserts revisionist causation without the history is guessing.

---

## 2. Meta-tests

**Three-lists test.** Put contributions, sections, and figures side by side; draw each contribution's pointers to sections and figures and each figure's pointer back to one contribution. Read the orphans:

| Orphan | What it usually indicates |
|---|---|
| Contribution with no figure or experiment | An asserted claim (§3.4) |
| Section serving no contribution | Catalog material, or a contribution that exists but is unnamed |
| Figure serving no contribution | Evidence chosen because it was easy to measure |
| Contribution pointing to every section | The contribution is the system, not an idea |

Extend it to the full contributions × sections × experiments matrix when the paper has a contributions list and an evaluation with numbered questions. TVM's lists (two challenges, three modules, four contribution bullets, per-platform evaluation) are each reasonable and do not line up.

**Rewrite test.** Mark the sentences that would still be true after the system's next major rewrite. If the thesis is not among them, it sits at the HOW layer. Ray's task/actor/futures API survived; its GCS-on-the-critical-path design was inverted by its own v2 whitepaper. Ask the authors which components are scheduled for replacement; a thesis on one of them has a known expiry date.

**Null-idea test.** For each experiment behind a number in the abstract, ask whether the result would change if the central idea were false and all other engineering stayed the same. If not, the experiment tests the system (Spark's speedup would survive removing lineage). The test, its precedents, and the isolation designs that repair a failure are in `evidence-and-venue.md` §3.

**Trace upstream before fixing.** A symptom is seen at one layer and produced at another. Before fixing, ask which upstream decision would make this symptom disappear if it were made differently.

| Seen at | Often produced by | Example |
|---|---|---|
| Wording (adjectives, "first", "unified") | A stated gap above the evidence | Ray's "first to unify" over building-block parity results |
| Evidence (figure measures the runtime, not the idea) | A thesis credited with the whole system's effect | Spark's 20× attributed to the abstraction |
| Mechanism description (defaults, no alternatives) | No opponent, so nothing to argue against | HDFS |
| Structure (evaluation per platform) | A middle layer cut that the evaluation could not follow | TVM |
| Naming (brand on a slower component) | A name attached to the novel-looking component rather than the one that carries the benefit | PagedAttention |

If three or more symptoms trace to one decision, revisit that decision and re-diagnose. Patching them one by one is filling a wrong form well.

---

## 3. Symptom catalog

Each entry: symptom; what it usually means; a quick check; the fix; a precedent. Groups run roughly from upstream to downstream, which is also the order of repair.

### 3.1 Framing and thesis

Symptoms here mean the paper's top object is missing, ungrounded, or pitched at the wrong layer. Every other group inherits from this one.

- **The thesis cannot be stated without the system's name.** *Means:* no separable idea has been found, or there is none. *Check:* rewrite the abstract's key sentence without the name. *Fix:* go back to reading for decisions (fan-out decisions, removals, invariants, surprising measurements; see `reading-a-system.md`). If nothing turns up, choose an honest genre: experience, operational, data, synthesis. *Precedent:* HDFS's unused thesis was in its own data: an open implementation of the GFS design point meets a diverse user base, and here is where its assumptions broke.
- **No opponent.** No sentence of the form "it is commonly believed" or "prior systems assume". *Means:* nothing is contested, so reviewers will not know what to update. *Check:* can you cite someone who holds the belief? *Fix:* name the belief; if the only opponent is "X is slower", you have a benchmark result and need a different frame. *Precedent:* HDFS.
- **The stated gap sits above the evidence.** *Means:* the elevation from engineering fact to claim went one level further than the evaluation. *Check:* which gap does the evaluation actually reach? *Fix:* lower the stated gap to that level or add the experiment at the stated level. *Precedent:* Pathways is motivated by MoE and multi-tenancy and evaluated on SPMD parity; the case writer's honest headline is "flexibility costs nothing for current workloads". RDD stated its gap above its 20× and then decomposed the number honestly.
- **An untested conjunction gap.** "Nothing has A, B and C together." *Check:* is there an experiment where composing existing A, B and C fails? *Fix:* run it (for Ray, a stitched Horovod + Clipper pipeline) or narrow the claim to parity with each specialist. *Precedent:* Ray's "stitching is untenable" carried the structure, was never tested, and the RL framing was later dropped.
- **The spine is named but generates nothing.** *Check:* delete the concept's name from the middle sections; does any section lose its reason to exist? *Fix:* find the concept the sections actually depend on, or reorganize under one that generates them. *Precedent:* TVM v1's "optimization stack"; TVM OSDI re-cut to "Automated" when the cost model arrived.
- **The thesis is tied to a replaceable HOW.** *Check:* could the next version change this without changing the interface? *Fix:* restate the thesis at the interface or specification layer; report the HOW as an implementation choice with its measured effect. *Precedent:* Ray's GCS.
- **A record is the only headline.** *Check:* if a cheaper method beats the number next month, what claim remains? *Fix:* promote the structural finding or the reusable formulation. *Precedent:* flip graphs reproduced most of AlphaTensor's table within two months; the 14,236 nonequivalent factorizations and the formulation were the partial hedge.
- **The paper argues for something users did not adopt it for.** *Check:* compare the thesis with what drove adoption (README traffic, issues, talks). *Fix:* either reframe or accept the gap deliberately and say why. *Precedent:* TensorFlow's OSDI paper was locally excellent and argued the stateful-dataflow design rather than what drove adoption.

### 3.2 Structure

Symptoms here usually mean the outline was taken from the codebase or from effort, not from the argument. Check §3.1 first: a module-shaped outline often exists because no spine was found.

- **Section headings are module names** (NameNode, Scheduler, Object Store). *Check:* does each section open with a claim a non-user could dispute? *Fix:* reorganize by premise, capability, or challenge (GFS's premises; vLLM's capabilities applied to harder cases; TVM's two challenges) and collect layers into one implementation section. *Exception:* a mature system with no single idea may organize by reader questions if the questions are stated in the introduction and a metric unifies them (Borg). See `argument-architecture.md` §15.
- **Uniform depth; section length tracks subsystem size.** *Means:* effort is presented as contribution. *Fix:* budget pages per claim, by argumentative load. *Precedent:* MapReduce gives the model about 1.5 pages and the runtime and refinements about 4.5 because the runtime is where the claim is shown; vLLM gives its roughly 8.4K-line model executor one sentence.
- **One-sentence-per-feature paragraphs or bullet lists of features.** *Fix:* if they serve no claim, gather them into a named refinements bucket (Bigtable §6) or cut them. If breadth is the thesis (extensibility, generality), recast four or five as before/after capability case studies (TensorFlow OSDI §4: "used to require editing the parameter server, now user code"). *Precedent:* MapReduce's nine refinements read as a feature list that only the authors' standing carried.
- **The substrate swallows the idea.** The idea arrives after about a third of the paper. *Check:* list objects the substrate section introduces; are they all used by the idea section? *Fix:* trim to those objects. *Precedent:* Spanner §2 avoids it by introducing only what §4's argument uses.
- **A lower layer is explained by its internals.** *Fix:* describe it by the contract used and quantify the one dependency that can take the system down (Bigtable on Chubby: 0.0047% unavailability).
- **The implementation section introduces a new organizing idea.** *Means:* that idea is the real spine or another paper. *Fix:* promote it or move it out.
- **The best content sits in the last two sections.** *Check:* where are the paper's unique data and its candor? *Fix:* consider whether that material is the thesis. *Precedent:* HDFS §IV-V (0.8% of nodes lost per month; "our assumption that applications will create a small number of large files was flawed").
- **The evaluation is organized by dataset or platform.** *Fix:* organize by claim or capability and open with the questions it answers. *Precedent:* TVM's evidence per platform vs claims per challenge; vLLM and PyTorch mirror their designs.
- **The introduction reads like a README** (lineage, feature list, platforms, "X is a framework for..."). *Check:* time to thesis in paragraphs (GFS: sentences 3-4; MapReduce: about 15 lines; PyTorch: paragraph 3; Ray: paragraph 7, the weak part of an accepted paper). *Fix:* get the displaced belief and the thesis onto the first page, and cut lineage to one sentence crediting the nearest predecessor. The opening register depends on the spine (a belief, observations, or operating reality); see `argument-architecture.md` §14.
- **The abstract is a table of contents** ("we describe the design, implementation and evaluation of X"). *Fix:* make the concept the grammatical subject and give the reader what they need to decide whether to believe it: which limitation it removes, by what mechanism, with which number, within what scope. The order and emphasis follow the frame: Raft's abstract carries its user-study result; Dynamo's carries its tradeoff.

### 3.3 Mechanism description

Symptoms here are the two poles at paragraph scale: description without argument (catalog) or argument without mechanism (vapor). Both often mean the middle layer is missing, so nothing tells the reader why each mechanism is a forced move.

- **A mechanism appears with no alternative and no cost.** *Check:* can you name the alternative a reader would expect? *Fix:* use GFS's pattern (choice, benefits "First... Second...", cost, why the cost has not bitten) or Dynamo's (textbook version, what breaks here, our variant). If there is no plausible alternative, compress the mechanism to a clause. *Precedent:* HDFS's defaults in prose.
- **Reasons sit in a separate rationale section.** *Fix:* move each why into the sentence next to its what (Ray §4.2.1).
- **No mechanism is concrete enough to re-implement the core idea.** *Check:* could a competent reader rebuild the idea, not the system, from the paper? *Fix:* trace a running example through the mechanism (vLLM's prompt across six figures; Ray's nine-step trace; GFS's seven-step write), give a one-page specification (Raft), or show the user-facing contract plus a real result moving through it. *Precedent:* AlphaEvolve's program database, called the component that "resurfaces previously explored ideas", is described in one sentence; open reimplementations had to guess.
- **Generic principles.** *Check:* does each exclude something a reasonable designer would do, and is it cited later as a reason? *Fix:* sharpen into priced principles or cut. *Precedent:* Dynamo's principle list vs PyTorch's.
- **The named concept is a renaming or names the wrong part.** *Check:* can a careful reader reduce the slogan to a rename, or find that the named part is not where the benefit comes from? *Fix:* name the part that carries the benefit, or say which it is. *Precedent:* TensorFlow's "no parameter server" kept the topology; "PagedAttention" names a kernel 20-26% slower in isolation while the block manager delivers the gain.
- **Over-attribution.** Features credited to the root concept that rest on other mechanisms. *Check:* for each, name the mechanism that fails without the root. *Precedent:* Brewer (2017) attributes Spanner's serializability to two-phase locking and its snapshot reads mostly to multiversioning; AlphaTensor's "improves over 70 tensors" includes a separate recombination pipeline credited to the agent.
- **An analogy with hidden gaps.** *Fix:* list where it breaks (vLLM names all-or-nothing eviction as an LLM-specific departure).
- **Happy-path running example; more than one inventory figure.** *Fix:* choose the example that fires every on-chain mechanism and exposes a cost; write a claim sentence for each figure and cut those whose sentence is "here are the components". *Precedent:* Dynamo's shopping cart shows deleted items resurfacing; AlphaEvolve Figs. 1-2 are inventory.

### 3.4 Evidence

Symptoms here mean the paper's real claim (what its evidence tests) differs from its stated claim. The three shapes are attribution, scope, and property gaps; see `evidence-and-venue.md` for designing the replacement experiments.

- **A figure maps to no claim, or to a different claim than it is cited for.** *Check:* read the ledger's evidence column backwards, figure by figure. *Fix:* cut it, re-attach it honestly ("this measures the runtime, not the abstraction"), or run the experiment that ties one mechanism to one number. *Precedent:* Ray's 1.8M tasks/s are empty tasks.
- **A claim has no figure.** *Fix:* measure it, weaken the wording to what is shown, or move it to Discussion as a stated belief with the reason it is plausible (Pathways on GPUs). Keep it out of the abstract.
- **Attribution gap: the abstract number fails the null-idea test.** *Fix:* decompose your own headline before a reviewer does (RDD Fig. 9 splits Spark's gap into HDFS, parsing, deserialization) and give the idea evidence in proportion to its weight in the title.
- **Scope gap.** *Check:* compare the title's nouns with the evaluation's setup paragraph. *Precedent:* Spanner promises global distribution; its microbenchmark zones were less than 1 ms apart.
- **Property gap: a differentiating adjective has no instrument.** *Check:* for each adjective in the abstract (flexible, simple, consistent, usable), find the figure. *Fix:* build a proxy and disclose its bias, price it as a bound, or demote the adjective; pair any soft claim with conventional evidence. *Precedent:* TensorFlow's "flexibility" rests on case studies; Dynamo's "clearly defined consistency window" is never defined; Raft's 2014 study, TLA+ spec and implementation answered 2012's "essentially no objective evidence".
- **Baseline in its worst regime, or rebuilt by the authors.** *Check:* who configured it, and would its designers accept the configuration? *Fix:* disclose the construction in the sentence that uses it; report where the competitor wins. *Precedent:* vLLM's Orca variants are reconstructions, Orca-Max reserving 2048 tokens; Ray's BSP-style MPI; TensorFlow admits Neon wins on three models; AlphaDev concedes warm-started AlphaDev-S is more efficient on branchless sorts.
- **The strongest simple alternative is missing.** *Check:* write down what a skeptic would build in a week without the idea. *Precedent:* a growing contiguous allocator (vLLM), Hadoop plus caching (Spark), equal-budget local search (AlphaTensor).
- **Ablation covers only the components the paper sells.** *Check:* compare the system figure's boxes with the ablation's rows. *Fix:* ablate each box or label it as convenience. *Precedent:* AlphaEvolve's database, cascade and islands get no evidence.
- **Breadth without a denominator.** *Fix:* state how problems were selected and publish failures. *Precedent:* AlphaEvolve's "about 75%" of over 50 problems; Tao et al. later supplied negatives and cost.
- **Main-text claims supported only in an appendix or SI.** *Check:* for each main-text claim, is its strongest support in the main text? In Nature-style tiering, evidence for secondary claims can legitimately sit in SI (AlphaTensor's demonstration ablation); in a systems venue the mechanism and its evidence belong in the body.
- **A positioning table where you lose on no row.** *Fix:* add the strongest practical alternative as a row; say which axes were left out. *Precedent:* RDD Table 1 omits MapReduce plus caching; TVM Table 1.

### 3.5 Claims and wording

Wording symptoms are the cheapest to fix and the most often fixed in isolation. Before softening a word, check whether the evidence group above explains why it is too strong.

- **Unsupported adjectives and broad nouns** (first, general, unified, global, novel, "algorithm"). *Check:* point at the evidence for each word. *Fix:* qualify inside the sentence or replace with what was measured.
- **Words a non-specialist will read at full strength.** *Precedent:* AlphaDev's "new sorting algorithm" was attacked as "one MOV" in an unrolled insertion sort; Spanner's "linchpin" was read as beating CAP, which Brewer's 2017 note partly exists to correct.
- **A deployment claim outrunning the record.** *Fix:* state the restriction under which it shipped, or one concrete deployment fact. *Precedent:* "integrated into LLVM" for a narrow, type-restricted patch.
- **Caveats only inline, or only far away.** *Fix:* put each small qualification in the sentence it qualifies and also aggregate limitations where a skimmer will find them. *Precedent:* TVM's Amdahl sentence after the 40× FPGA result is the right inline move, but TVM and JAX have no aggregate.
- **Lessons are platitudes.** *Check:* is each tied to a reversed decision or a measurement? *Fix:* claim, the system's specific evidence, consequence (`evidence-and-venue.md` §6.5). *Precedent:* Bigtable's membership protocol lessons pass; Borg's carry no data and hold because the authors are trusted and Kubernetes exists.

### 3.6 Honesty and scope

Symptoms here are usually invisible in the draft and visible only against the system's history, docs, or release, so they need the authors (§1). They matter because they decide what later readers conclude when the design does not transfer to their setting.

- **Revisionist causation** ("our design was driven by..."). *Check:* would a retrospective by the same authors contradict it? *Fix:* write "is justified by" and state any load-bearing reason as a condition. *Precedent:* GFS says its design was driven by workload observations; the single master was chosen to ship quickly.
- **A derivation without its boundary condition.** *Check:* does the premise chain need an unstated condition (who controls clients, which scale)? *Precedent:* GFS's relaxed consistency depended on the team controlling all clients.
- **Candor about every cost except the decisive one.** *Check:* name the limitation that would most reduce belief in the abstract's first claim; is it stated near that claim? *Precedent:* Spanner is candid about double logging and silent about the missing WAN microbenchmark; vLLM is candid about kernel cost and quiet about the baseline regime.
- **Unbounded generality.** No "not suitable for" statement. *Fix:* write the domain down (RDD §2.4; vLLM §8 "may rather degrade" for training; FunSearch's three conditions). *Precedent:* Ceph; AlphaEvolve.
- **Reality status unclear.** Present tense for unbuilt features. *Fix:* say what is implemented, deployed and evaluated, and in which version, early. *Precedent:* Spanner's "not yet implemented" and "out of expediency" clauses.
- **A cut of qualification.** *Check:* does any removed detail change a claim's truth? *Fix:* restore it inside the claim. *Precedent:* AlphaDev's LLVM patch restrictions.
- **Reconstructed alternatives written as history.** *Check:* the alternatives register's provenance column. *Fix:* "an alternative would be Y; it would cost..." in the voice of analysis. Agents are especially prone to this.
- **Inheritance unstated or hidden.** *Fix:* credit the nearest predecessor in one sentence with the delta (TVM on Halide). *Precedent:* FlashAttention credits Apex FMHA only in acknowledgments and an appendix; HDFS never names GFS.
- **Only failures that motivated choices are kept.** *Check:* ask the authors for failures that challenge the premises. *Precedent:* HDFS's "flawed" assumption sits in future work.
- **Withheld material not disclosed.** *Fix:* say what was withheld and why, and compensate (Dynamo: aggregate-only numbers for business reasons; Pathways: public baselines and worst cases for a closed system). Releasing a checker backs claims about outputs, not about the method.

### 3.7 Cross-artifact consistency

The paper is one of several documents about the system, and readers meet the others first.

- **Two-audience drift.** *Check:* list every public number (blog, README, talks, paper) and the baseline each answers. *Precedent:* vLLM's blog claimed 24× over HuggingFace and the paper 2-4× over research baselines; both true, and the gap set expectations the paper did not support.
- **An evaluated feature is missing from the cited release.** *Precedent:* vLLM's prefix sharing in v0.2.0.
- **Omissions without a home.** *Check:* for each major cut, is there a demotion sentence or a named companion document? *Precedent:* Spark's shuffle, Ray's GC, Spanner's SQL; Spanner needed a 2017 paper to say today's Spanner "is very different from what was described there".
- **The README and the paper frame the system differently.** *Check:* compare the README's first sentence with the thesis sentence. For an open system the README often wins the canonical framing (Ray's README names a market, not a mechanism). Reconcile or explain.

---

## 4. Reviewer and critic simulation

These questions come from what actually reshaped or attacked the classic papers. Run them against the draft and record each answer in the claims ledger. If an answer is "we would add X if asked", add X now: TVM's XLA, TFLite, TC and ACL baselines and PyTorch's performance comparison were rebuttal-time additions, which means they were pre-submission omissions.

| Question | Historical precedent | If the answer is bad |
|---|---|---|
| What would an expert in each adjacent field use instead? Ask once per community (database, compiler, OR, theory, practitioner) | MapReduce never discussed relational systems (DeWitt and Stonebraker, 2008); AlphaDev vs superoptimizers; AlphaTensor vs SAT and flip graphs; FunSearch's hedges read as one per reviewer type | Measure it or scope it out explicitly; it must appear somewhere |
| If a cheaper method beats the headline next month, what remains? | Flip graphs vs AlphaTensor, two months | Move the headline to a structural finding or formulation |
| How will a non-specialist read the title and abstract? | "New sorting algorithm"; TrueTime read as beating CAP | Replace the strong noun or qualify it in place |
| Which claims depend on a mechanism slated for replacement? | Ray's GCS; Dynamo → DynamoDB "shared most of the name ... but little of its architecture"; Spanner 2017 | Restate at the layer that will survive |
| Is the critic attacking method or significance? | AlphaDev's own superoptimizer comparison satisfied reviewers; public critique attacked significance ("one MOV") | Baselines answer method objections only; significance needs its own argument |
| Is the result best-of-N? What is the hit rate? | FunSearch: 4 of 140 cap-set runs found the 512 set | Report runs, successes, and selection |
| Can the system exploit its own evaluator or proxy? | Neither FunSearch nor AlphaEvolve discusses it; Tao et al. found it first-order | Describe safeguards and observed exploitation |
| Which claims rest on access or authority a reader cannot check? | Bigtable, Borg, Dynamo, Pathways | Compensate with public baselines, configurations, worst cases; a new group cannot borrow the authority |
| What does it do over and above its nearest ancestor? | TVM vs Halide; AlphaEvolve's delta table, five of eight rows scale | One-sentence delta; separate conceptual deltas from scale |
| Which part of the headline number is the idea's? | RDD Fig. 9 | Decompose |
| For a non-performance claim, what would persuade a non-believer? | Raft 2012 → 2014; PyTorch's area chair asked for a user study | Proxy with disclosed bias plus conventional evidence |
| Is the shipped artifact as broad as the claim? | AlphaDev's LLVM patch; vLLM's prefix sharing | Narrow the claim to the release |
| Does this venue count this contribution type? | Raft ("understandability per se" not valued); PyTorch ("USENIX or SysML?") | Add the venue's currency or change venue |
| Which premises must a later reader check to know if this applies to them? | GFS's numbered assumptions aged legibly | State them with numbers |

The adjacent-field alternative, competitor reproduction, the system outgrowing the paper, and the significance critic threaten the frame; answer those before the rest.

---

## 5. Prioritizing fixes

**Fix framing defects before local ones.** Order of repair: frame and thesis (§3.1) → middle layer and structure (§3.2-3.3) → evidence (§3.4) → claims, honesty, consistency (§3.5-3.7) → sentences. Revision in the versioned cases meant section-level chops (TensorFlow whitepaper → OSDI; TVM v1 → OSDI; Spark three times), not sentence edits. Polishing paragraphs under a frame that will change is wasted work and makes the wrong frame harder to abandon.

**Rank symptoms by threat to the abstract's first claim.** A missing simple alternative for the headline outranks a weak secondary figure. Rank by how much each symptom, raised by a reviewer, would reduce belief in that claim.

**Choose the cheapest honest fix, with one guard.** Rescoping a claim to its evidence is usually cheaper than a new experiment and better than an assertion. But if rescoping removes the opponent (the narrowed claim contradicts nothing anyone believes), the defect is in the frame, not the wording. Within evidence, spend experiments on attribution and the simplest alternative first and breadth last; one decoupling table outweighs ten end-to-end bars.

**Weight by author position.** Several classic precedents survived on authority: MapReduce's refinements, Borg's data-free lessons, Dynamo's telemetry-only evaluation. For a group without that standing, the same symptoms are more serious, and evidence-producing fixes (ablations, honest baselines, decomposed speedups, scope statements) matter more than presentation fixes.

**When reviewing someone else's draft,** lead with the two or three upstream defects and the symptoms each explains, then list local issues separately, then the questions only the authors can answer. A flat list of forty items hides the three that decide the paper.

**Stop when** the frame has passed trial by abstract, its decisive experiment is in hand, the three-lists test has no orphans, and remaining symptoms are local.

---

## 6. Failure modes of the revision process

- **Filling a wrong frame well.** Every local check passes and the paper is still mispositioned (TensorFlow OSDI). Signs: local fixes keep regenerating related symptoms; the one-sentence summary from §1 does not match what users value. Guard: keep one rejected frame alive until trial by abstract, and ask what drove adoption.
- **Premature freezing.** The frame was fixed before any abstract was tried against the evidence, so symptoms are patched one by one. Guard: freeze only once a frame passes trial by abstract and its decisive experiment is in hand or scheduled.
- **Endless reframing.** Each review round produces a new frame without a named reason. Reopen a frame only for a named trigger (venue or audience change, an evaluable mechanism arriving, demand for evidence of the headline, an attack on an omitted comparison, the system outgrowing the paper; cases and questions in `finding-the-contribution.md` §9.3). Without one, reframing is avoidance of the evidence work.
- **Treating probes as completion.** Passing every check here is evidence about parts of the draft, not about whether the frame is right. A clean catalog run does not mean the paper is good.
- **Patching symptoms to shift poles.** Fixing vapor by pasting in implementation detail moves the draft toward catalog; fixing catalog by deleting mechanism moves it toward vapor. The fix for either is the missing middle layer and the causal chain, not more or less text.
- **Reviewer-pleasing additions that change the claim.** A baseline or caveat added for one reviewer can quietly shift what the paper argues. After a round of fixes, rewrite the one-sentence summary and check it against the abstract.

---

## Assumptions and limits

Precedents here are mostly attacks on, and corrections to, papers that succeeded anyway; they show where pressure came from, not that each symptom is fatal. Some checks need the system, its history, or its authors, and a draft-only review cannot do them. The prioritization in §5 assumes the frame is the most expensive thing to get wrong, which held across the versioned cases but is inferred from their diffs, not from documented process.
