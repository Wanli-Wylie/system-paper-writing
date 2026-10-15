# Diagnostics: reviewing and revising a draft

Read this when you have a draft, yours or someone else's, and must decide what is wrong with it and what to fix first (SKILL.md stage 9). Symptoms are grouped by where they show up, but most of them are produced upstream: a structure symptom is often a framing defect, an evidence symptom is often a stated gap pitched above what was measured, and a voice symptom is often a check written into the prose instead of used to size a claim. Diagnose broadly, trace upstream, then fix from the top down. Precedents are cases where the symptom was later attacked, corrected, or avoided. The tests and the claims ledger below are the reviewer's working tools; their output is a changed claim, experiment, or structure, never text in the paper.

## Contents

1. How to run a diagnosis
2. Meta-tests
3. Symptom catalog
   3.1 Framing and thesis · 3.2 Structure · 3.3 Mechanism description · 3.4 Evidence · 3.5 Claims and wording · 3.6 Honesty and scope · 3.7 Cross-artifact consistency · 3.8 Narrative and voice
4. Reviewer and critic simulation
5. Prioritizing fixes
6. Failure modes of the revision process

---

## 1. How to run a diagnosis

1. **Read the whole draft once without marking anything.** Then write one sentence: what problem the paper addresses, what it changes in the field's current state (and with whom that state is documented), and what the construction contributes. If you cannot write it, that is the first finding, and it outranks everything you will mark later. If you can write it only in the system's own vocabulary, or only as "nothing else has A, B and C", treat that as the first finding too.
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
| Mechanism description (defaults, no alternatives) | No problem stated against the field's state, so no choice needs a reason | HDFS |
| Structure (evaluation per platform) | A middle layer cut that the evaluation could not follow | TVM |
| Structure (sections follow the design documents, even under concept titles) | A problem taken from the system's account of itself rather than from the field | Design-led drafts (§3.8) |
| Voice (disclaimers, repeated caveats, rebuttal paragraphs) | Checks written into the prose instead of used to resize claims | Defensive drafts (§3.8) |
| Naming (brand on a slower component) | A name attached to the novel-looking component rather than the one that carries the benefit | PagedAttention |

If three or more symptoms trace to one decision, revisit that decision and re-diagnose. Patching them one by one is filling a wrong form well.

---

## 3. Symptom catalog

Each entry: symptom; what it usually means; a quick check; the fix; a precedent. Groups run roughly from upstream to downstream, which is also the order of repair.

### 3.1 Framing and thesis

Symptoms here mean the paper's top object is missing, ungrounded, or pitched at the wrong layer. Every other group inherits from this one.

- **The thesis cannot be stated without the system's name or its internal vocabulary.** *Means:* no separable idea has been found, or there is none. *Check:* rewrite the abstract's key sentence without the name and without the system's own layer names or coined terms. *Fix:* go back to reading for decisions (fan-out decisions, removals, invariants, surprising measurements; see `reading-a-system.md`). If nothing turns up, choose an honest genre: experience, operational, data, synthesis. *Precedent:* HDFS's unused thesis was in its own data: an open implementation of the GFS design point meets a diverse user base, and here is where its assumptions broke.
- **The problem is not grounded in the field.** The introduction does not say what existing work achieved and what it leaves open, or says so only through trend labels or one property all prior systems supposedly lack. *Means:* the reader has nothing to check the opportunity against, so they will not know what to update. *Check:* could a reader who knows the literature verify the stated opportunity from the citations given? Did the field hold the view the paper changes, or did the design need it? *Fix:* read the field's progress and state the problem from it (SKILL.md stage 2; `constructive-narrative.md` §2). Do not fix this by inserting "it is commonly believed" sentences: a belief with no citable holder is a straw man. If the only change you can state is "X is slower", you have a benchmark result and need a different frame. *Precedent:* HDFS stated no problem of its own and changed nothing a reader held.
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
- **The introduction reads like a README** (lineage, feature list, platforms, "X is a framework for..."). *Check:* time to thesis in paragraphs (GFS: sentences 3-4; MapReduce: about 15 lines; PyTorch: paragraph 3; Ray: paragraph 7, the weak part of an accepted paper). *Fix:* get the problem, its place in the field's progress, and the thesis onto the first page, and cut lineage to one sentence crediting the nearest predecessor by what this work builds on. The opening register depends on the spine (the field's state, observations, or operating reality), but in every register the problem comes before the system; see `argument-architecture.md` §14.
- **The abstract is a table of contents** ("we describe the design, implementation and evaluation of X"), or its second half walks through components one per sentence (§3.8). *Fix:* make the idea, as it bears on the problem, the grammatical subject and give the reader what they need to decide whether to believe it: which limitation it removes, by what mechanism, with which number, within what scope. The order and emphasis follow the frame: Raft's abstract carries its user-study result; Dynamo's carries its tradeoff.

### 3.3 Mechanism description

Symptoms here are the two poles at paragraph scale: description without argument (catalog) or argument without mechanism (vapor). Both often mean the middle layer is missing, so nothing connects the problem's observed difficulties to the mechanisms that respond to them.

- **A mechanism a reader would expect to be done differently appears with no alternative and no cost.** *Check:* can you name the alternative a competent reader would expect at this point? If you cannot, this is not a symptom: the mechanism needs its role in the construction explained, not a defense, and may compress to a clause. *Fix:* where the alternative is real, use GFS's pattern (choice, benefits "First... Second...", cost, why the cost has not bitten) or Dynamo's (textbook version, what breaks here, our variant). Applied to every mechanism, these patterns turn each paragraph into assert, justify, concede, rebut (§3.8, Defense). *Precedent:* HDFS's defaults in prose.
- **Reasons sit in a separate rationale section.** *Fix:* move each why into the sentence next to its what (Ray §4.2.1).
- **No mechanism is concrete enough to re-implement the core idea.** *Check:* could a competent reader rebuild the idea, not the system, from the paper? *Fix:* after the problem is stated, trace a real or canonical example through the mechanism where the mechanism is introduced (vLLM's prompt across six figures; Ray's nine-step trace; GFS's seven-step write), give a one-page specification (Raft), or show the user-facing contract plus a real result moving through it. Removing an artificial example (§3.8) does not settle this symptom; the construction itself still has to be concrete. *Precedent:* AlphaEvolve's program database, called the component that "resurfaces previously explored ideas", is described in one sentence; open reimplementations had to guess.
- **Generic principles.** *Check:* does each exclude something a reasonable designer would do, and is it cited later as a reason? *Fix:* sharpen into priced principles or cut. *Precedent:* Dynamo's principle list vs PyTorch's.
- **The named concept is a renaming or names the wrong part.** *Check:* can a careful reader reduce the slogan to a rename, or find that the named part is not where the benefit comes from? *Fix:* name the part that carries the benefit, or say which it is. *Precedent:* TensorFlow's "no parameter server" kept the topology; "PagedAttention" names a kernel 20-26% slower in isolation while the block manager delivers the gain.
- **Over-attribution.** Features credited to the root concept that rest on other mechanisms. *Check:* for each, name the mechanism that fails without the root. *Precedent:* Brewer (2017) attributes Spanner's serializability to two-phase locking and its snapshot reads mostly to multiversioning; AlphaTensor's "improves over 70 tensors" includes a separate recombination pipeline credited to the agent.
- **An analogy with hidden gaps.** *Fix:* list where it breaks (vLLM names all-or-nothing eviction as an LLM-specific departure).
- **Happy-path example; more than one inventory figure.** *Fix:* choose a real or canonical example that makes each central mechanism fire and exposes at least one cost, introduced where the first mechanism needs it rather than in the introduction; write a claim sentence for each figure and cut those whose sentence is "here are the components". *Precedent:* Dynamo's shopping cart shows deleted items resurfacing; AlphaEvolve Figs. 1-2 are inventory.

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

- **Unsupported adjectives and broad nouns** (first, general, unified, global, novel, "algorithm"). *Check:* point at the evidence for each word. *Fix:* resize the claim to what was measured, or qualify it inside the sentence, once.
- **Words a non-specialist will read at full strength.** *Precedent:* AlphaDev's "new sorting algorithm" was attacked as "one MOV" in an unrolled insertion sort; Spanner's "linchpin" was read as beating CAP, which Brewer's 2017 note partly exists to correct.
- **A deployment claim outrunning the record.** *Fix:* state the restriction under which it shipped, or one concrete deployment fact. *Precedent:* "integrated into LLVM" for a narrow, type-restricted patch.
- **The decisive limitation is only inline, or only far away.** *Fix:* a qualification that changes a claim's meaning goes in that claim's sentence; the decisive limitation appears at the claim and once more in a short scope paragraph where a skimmer will find it; minor caveats appear once or not at all. First ask whether resizing the claim would make the caveat unnecessary; usually it would. *Precedent:* TVM's Amdahl sentence after the 40× FPGA result is the right inline move, but TVM and JAX have no scope paragraph. *Opposite symptom:* the same caveat repeated in several places, or caveats on claims no reader would doubt. *Fix:* resize the claim and delete the repeats (§3.8, Defense).
- **Lessons are platitudes.** *Check:* is each tied to a reversed decision or a measurement? *Fix:* claim, the system's specific evidence, consequence (`evidence-and-venue.md` §6.5). *Precedent:* Bigtable's membership protocol lessons pass; Borg's carry no data and hold because the authors are trusted and Kubernetes exists.

### 3.6 Honesty and scope

Symptoms here are usually invisible in the draft and visible only against the system's history, docs, or release, so they need the authors (§1). They matter because they decide what later readers conclude when the design does not transfer to their setting.

- **Revisionist causation** ("our design was driven by..."). *Check:* would a retrospective by the same authors contradict it? *Fix:* write "is justified by" and state any load-bearing reason as a condition. *Precedent:* GFS says its design was driven by workload observations; the single master was chosen to ship quickly.
- **A derivation without its boundary condition.** *Check:* does the premise chain need an unstated condition (who controls clients, which scale)? *Precedent:* GFS's relaxed consistency depended on the team controlling all clients.
- **Candor about every cost except the decisive one.** *Check:* name the limitation that would most reduce belief in the abstract's first claim; is it stated near that claim? *Precedent:* Spanner is candid about double logging and silent about the missing WAN microbenchmark; vLLM is candid about kernel cost and quiet about the baseline regime.
- **Unbounded generality.** No statement of the conditions under which the idea works. *Fix:* work out the non-applicability conditions privately, then state the scope once, as a property of the result, in positive form where possible (RDD §2.4; vLLM §8 "may rather degrade" for training; FunSearch's three conditions). A scattered series of "we do not claim" sentences is the opposite failure (§3.8). *Precedent:* Ceph; AlphaEvolve.
- **Reality status unclear.** Present tense for unbuilt features. *Fix:* say what is implemented, deployed and evaluated, and in which version, early. *Precedent:* Spanner's "not yet implemented" and "out of expediency" clauses.
- **A cut of qualification.** *Check:* does any removed detail change a claim's truth? *Fix:* restore it inside the claim. *Precedent:* AlphaDev's LLVM patch restrictions.
- **Reconstructed alternatives written as history.** *Check:* the alternatives register's provenance column. *Fix:* "an alternative would be Y; it would cost..." in the voice of analysis, and only where a reader would expect the alternative and it illuminates the choice. Agents are especially prone to this.
- **Inheritance unstated or hidden.** *Fix:* credit the nearest predecessor in one sentence, in the form of what this work builds on and adds (TVM on Halide). *Precedent:* FlashAttention credits Apex FMHA only in acknowledgments and an appendix; HDFS never names GFS.
- **Only failures that motivated choices are kept.** *Check:* ask the authors for failures that challenge the premises. *Precedent:* HDFS's "flawed" assumption sits in future work.
- **Withheld material not disclosed.** *Fix:* say what was withheld and why, and compensate (Dynamo: aggregate-only numbers for business reasons; Pathways: public baselines and worst cases for a closed system). Releasing a checker backs claims about outputs, not about the method.

### 3.7 Cross-artifact consistency

The paper is one of several documents about the system, and readers meet the others first.

- **Two-audience drift.** *Check:* list every public number (blog, README, talks, paper) and the baseline each answers. *Precedent:* vLLM's blog claimed 24× over HuggingFace and the paper 2-4× over research baselines; both true, and the gap set expectations the paper did not support.
- **An evaluated feature is missing from the cited release.** *Precedent:* vLLM's prefix sharing in v0.2.0.
- **Omissions without a home.** *Check:* for each major cut, is there a demotion sentence or a named companion document? *Precedent:* Spark's shuffle, Ray's GC, Spanner's SQL; Spanner needed a 2017 paper to say today's Spanner "is very different from what was described there".
- **The README and the paper frame the system differently.** *Check:* compare the README's first sentence with the thesis sentence. For an open system the README often wins the canonical framing (Ray's README names a market, not a mechanism). Reconcile or explain.

### 3.8 Narrative and voice

Symptoms here mean the paper follows the writer's analysis instead of the reader's narrative: it starts from the system rather than the field, or it carries the checks of this skill into the prose. The classic cases supply few precedents, because the case study weighted what critics attacked; these symptoms come mostly from reviewing agent-written drafts, where they are the commonest failures. They fall into two tiers that are repaired differently. Detail and constructive rewrites are in `constructive-narrative.md`.

**Conception-level.** These decide what the paper is about. They rank with §3.1, and when several are present the fix is to re-conceive from the problem (SKILL.md stage 2), not to revise sentences (§6).

- **Design-led order.** The sections follow the design documents' components or chronology, even when they carry concept titles. *Check:* align the section list with the design package's table of contents; a near match is capture. *Fix:* re-derive the order from problem → ideas → construction; the components become the construction, introduced by the ideas they serve (`constructive-narrative.md` §4). *Precedent:* the TensorFlow whitepaper's manual-plus-implementation-tour outline, which the OSDI paper re-cut around one delta.
- **Deduction-driven structure.** Concepts are defined, requirements derived from them, and the design shown to satisfy each, often with sections that end by naming the requirement they meet. *Check:* are the requirements facts or difficulties a reader would grant before seeing the system, or consequences of definitions the paper introduced? *Fix:* replace them with observed difficulties of the problem and measured premises; delete definitions nothing else uses; let questions the construction answers only partly stay open (`constructive-narrative.md` §6). *Precedent:* GFS's premises were observed workload facts with numbers, which is why readers could later see which one broke.
- **Means taken for the problem.** The problem statement names a technique (learning, search, recursion, a decision procedure, an IR). *Check:* would the problem still hold if a different technique solved it? Does the statement also cover phenomena plainly outside the work? *Fix:* state what is improved, and for whom; move the technique to the ideas (`constructive-narrative.md` §3). *Precedent:* vLLM's problem is wasted KV-cache memory; paging is its means.
- **Domain taken for the object.** The scope is an application domain (genomics pipelines, a trading platform, large-model serving) while the mechanism acts on a general computation, resource, or workload shape. *Fix:* scope the subject by the computational object; keep the domain visible as the reason the problem matters, rather than dropping it. *Precedent:* FlashAttention's subject is the attention computation; long-sequence models are the motivation.
- **Internal vocabulary as frame.** The system's layer names, a mnemonic set, an analogy, or a coined unit of work organizes the introduction, the headings, or the abstract; or vocabulary from the brief has become the thesis. *Check:* remove the vocabulary; does the problem statement or the section structure change? *Fix:* translate the idea into the field's terms and see what it explains; reintroduce a term only where it names a construction the reader needs (SKILL.md stage 1, conceptual capture).
- **Artificial example as motivation.** An invented scenario opens the introduction and is carried through the paper; its identifiers recur in later sections or the abstract. *Check:* delete the example; is the problem still stated anywhere? *Fix:* state the problem from the field; illustrate mechanisms with real or canonical instances where each is introduced (`constructive-narrative.md` §7). Do not leave the construction vague in its place (§3.3). *Precedent:* Dynamo's shopping cart was Amazon's own service, not a scenario invented to illustrate the design.
- **Manufactured gap.** All prior work is said to lack one property, prior systems each get one disqualifier, or the field is described by trend labels ("increasingly", "X is doing Y"). *Check:* can a reader who knows the literature recognize the stated state of the field, and was it the field's view or the design's need? *Fix:* describe each line of work by what it solved and what that made possible, in precise categories (`constructive-narrative.md` §2.3–2.4); a conjunction gap owes its composition experiment (§3.1). *Precedent:* RDD's DSM category left MapReduce plus caching out of the comparison.
- **Conceptual packaging and manifesto.** Coined names for the paper's stance, slogans or triads, declarations of what computation or systems "are", a framework presented before any problem. *Fix:* restate as problem and idea; keep names only for constructions or quantities the reader will refer to repeatedly (`constructive-narrative.md` §8). *Precedent:* "IO-aware" earns its name because a theorem and one figure carry it.

**Voice-level.** These are fixed after the frame holds and before sentence polish. Most disappear when claims are right-sized.

- **Results not measured.** Numbers, trends, or outcomes in evaluation prose without experiments, including placeholders written as results, or a design walkthrough standing in for evidence. This is a truth defect, not a matter of voice: fix it first. *Fix:* write the evaluation as the questions it will answer and the experiments that will answer them; the abstract and introduction claim only what is built or argued (SKILL.md stage 7; `constructive-narrative.md` §10). *Precedent:* Pathways' motivating capabilities sit in future work without being labeled unevaluated.
- **Defense.** Disclaimers; "this is not A but B"; protective negations aimed at readings nobody proposed ("X never Y", "need not", "only at"); the same caveat or guarantee restated in several sections; a claim followed by evidence-hunting, qualification and a definition; sentences announcing which systems serve as examples or how the paper should be read; prior-work sentences that each end by attaching the work to the paper's own system. *Check:* the deletion test: delete each such sentence. Does any claim become false? If not, it was defense; leave it deleted. If so, resize that claim so it is true as stated, rather than keeping the sentence. *Fix:* state scope once, the decisive limitation once where it changes belief, and each guarantee once where its mechanism is described (`constructive-narrative.md` §5).
- **Audit text.** Requirement-to-section mappings; "this section realizes requirement 2"; correspondence to design documents; printed ledgers or matrices; tables whose job is to show that every concept has a component. *Fix:* delete them and keep the checks private. A table that tells the reader which technique answers which difficulty and what it buys is content (Dynamo's Table 1), not audit.
- **Artificial formalization.** Notation or definitions on which no result depends, or a model from which nothing follows. *Fix:* replace with prose; keep formalism where a reader programs against it, checks it, or a proof uses it (Spanner's invariants and four-line proof).
- **Abstract with component cadence.** The abstract's second half walks through components one per sentence ("The scheduler assigns… The store keeps… A monitor adjusts…"). *Fix:* make the idea the subject and the capabilities its consequences; the components belong to the body.
- **Title without direction.** A slogan, an internal term, a means in the main slot, or a neutral process word ("evolving", "rethinking", "towards"). *Fix:* name the object and the direction of improvement ("Fast and Memory-Efficient Exact Attention with IO-Awareness").

---

## 4. Reviewer and critic simulation

These questions come from what actually reshaped or attacked the classic papers. Run them against the draft and record each answer in the claims ledger. If an answer is "we would add X if asked", add X now when X is evidence (an experiment, a baseline, a measured cost): TVM's XLA, TFLite, TC and ACL baselines and PyTorch's performance comparison were rebuttal-time additions, which means they were pre-submission omissions. If X is a paragraph rebutting the objection, resize the claim instead so the objection no longer applies, or leave it; a rebuttal written in advance of an objection reads as defense (§3.8). The output of this section is a changed claim or a new experiment, not new text answering imagined reviewers.

| Question | Historical precedent | If the answer is bad |
|---|---|---|
| What would an expert in each adjacent field use instead? Ask once per community (database, compiler, OR, theory, practitioner) | MapReduce never discussed relational systems (DeWitt and Stonebraker, 2008); AlphaDev vs superoptimizers; AlphaTensor vs SAT and flip graphs; FunSearch's three applicability conditions answer several communities at once, stated once | Engage it in the work: measure it, or understand it well enough to say which different problem it solves. In the paper it appears where it illuminates the problem or a choice, described by what it does well |
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

**Fix framing defects before local ones.** Order of repair: frame and thesis (§3.1, with the conception-level defects of §3.8) → middle layer and structure (§3.2-3.3) → evidence (§3.4, with unmeasured results from §3.8) → claims, honesty, consistency (§3.5-3.7) → defense, packaging and the rest of voice (§3.8) → sentences. Narrative and voice defects that come from the conception (design-led order, deduction-driven structure, the wrong problem, internal vocabulary as frame, an artificial example as motivation) rank with framing, because no sentence edit removes them. Defense and packaging are fixed after the frame holds and before sentence polish: many disappear once claims are right-sized, and deleting them earlier only lets them grow back around a frame that is still changing. Revision in the versioned cases meant section-level chops (TensorFlow whitepaper → OSDI; TVM v1 → OSDI; Spark three times), not sentence edits. Polishing paragraphs under a frame that will change is wasted work and makes the wrong frame harder to abandon.

**Rank symptoms by threat to the abstract's first claim.** A missing simple alternative for the headline outranks a weak secondary figure. Rank by how much each symptom, raised by a reviewer, would reduce belief in that claim.

**Choose the cheapest honest fix, with one guard.** Rescoping a claim to its evidence is usually cheaper than a new experiment and better than an assertion. But if rescoping removes what the work changes (the narrowed claim changes nothing in the field's state), the defect is in the frame, not the wording. Within evidence, spend experiments on attribution and the simplest alternative first and breadth last; one decoupling table outweighs ten end-to-end bars.

**Weight by author position.** Several classic precedents survived on authority: MapReduce's refinements, Borg's data-free lessons, Dynamo's telemetry-only evaluation. For a group without that standing, the same symptoms are more serious, and evidence-producing fixes (ablations, honest baselines, decomposed speedups, a clearly stated scope) matter more than presentation fixes. Standing does not call for more caveats; it calls for more evidence and claims sized to it.

**When reviewing someone else's draft,** lead with the two or three upstream defects and the symptoms each explains, then list local issues separately, then the questions only the authors can answer. A flat list of forty items hides the three that decide the paper. When the conception itself is wrong, say so first and plainly; a list of local fixes to a rejected conception invites surface revision (§6).

**Stop when** the frame has passed trial by abstract, its decisive experiment is in hand, the three-lists test has no unexplained orphans, the reader's narrative runs from the field's problem to the construction without defense, and remaining symptoms are local. Coherence is the target, not closure: open questions stated as such are not orphans.

---

## 6. Failure modes of the revision process

- **Filling a wrong frame well.** Every local check passes and the paper is still mispositioned (TensorFlow OSDI). Signs: local fixes keep regenerating related symptoms; the one-sentence summary from §1 does not match what users value. Guard: keep one rejected frame alive until trial by abstract, and ask what drove adoption.
- **Premature freezing.** The frame was fixed before any abstract was tried against the evidence, so symptoms are patched one by one. Guard: freeze only once a frame passes trial by abstract and its decisive experiment is in hand or scheduled.
- **Endless reframing.** Each review round produces a new frame without a named reason. Reopen a frame only for a named trigger (venue or audience change, an evaluable mechanism arriving, demand for evidence of the headline, an attack on an omitted comparison, the system outgrowing the paper, the authors rejecting the conception itself; cases and questions in `finding-the-contribution.md` §9.3). Without one, reframing is avoidance of the evidence work.
- **Surface revision of a rejected conception.** When the authors or advisors reject a draft's conception, editing its sentences, retitling its sections, or keeping its example, structure, and coined terms produces the same paper. Re-conceive from the problem (SKILL.md stage 2): treat the old draft as evidence of what failed and carry over facts, not text. This is a named trigger, not endless reframing (`constructive-narrative.md` §9).
- **Treating probes as completion.** Passing every check here is evidence about parts of the draft, not about whether the frame is right. A clean catalog run does not mean the paper is good.
- **Patching symptoms to shift poles.** Fixing vapor by pasting in implementation detail moves the draft toward catalog; fixing catalog by deleting mechanism moves it toward vapor; fixing either by adding qualifications moves it toward defense. The fix is the missing connection from problem to mechanism (observed difficulties, not a derived requirement set) and the causal chain, not more or less text.
- **Defensive accretion.** Each round of review adds qualifications, definitions, and justifications; the paper grows and the research recedes. Fix by resizing claims and deleting the defense, not by adding a reply to each comment.
- **Point-by-point compliance that drops still-valid directions.** Revising only to the latest round of feedback tends to lose what that round did not mention: an earlier direction about motivation, scope, or voice is undone while a newer one is satisfied (fixing the object of study by deleting the domain that motivated it, for example). Before revising, list the directions from all rounds that still hold, and check the revision against all of them.
- **Reviewer-pleasing additions that change the claim.** A baseline or caveat added for one reviewer can quietly shift what the paper argues. After a round of fixes, rewrite the one-sentence summary and check it against the abstract.

---

## Assumptions and limits

Precedents here are mostly attacks on, and corrections to, papers that succeeded anyway; they show where pressure came from, not that each symptom is fatal. Because that evidence is weighted toward what critics struck, most of §3.1-3.7 is defensive in kind; use it to size claims and design evidence, and let §3.8 govern the voice. §3.8 rests on a narrower source, one author's reviews of successive agent-written drafts of a systems paper; venues and advisors differ in how much explicit scoping they expect, so check its voice guidance against recently accepted papers at the target venue. Some checks need the system, its history, or its authors, and a draft-only review cannot do them. The prioritization in §5 assumes the frame is the most expensive thing to get wrong, which held across the versioned cases but is inferred from their diffs, not from documented process.
