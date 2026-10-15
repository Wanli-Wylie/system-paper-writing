# Filtering and honesty

Read this when you have a candidate thesis and a causal graph and must decide, for every true and substantial thing about the system, whether it gets full treatment, gets demoted, moves to another document, or is left out. Read it again when revising, to check that the cuts and the narrative order have not changed what the paper's claims mean. It covers the filtering rules, the destinations, the honesty tests, and the risks of splitting material across documents.

## Contents

1. What is being filtered, and the filter ledger
2. The rules, with evidence
3. Destinations and how to choose
4. What drove adoption is what gets filtered
5. Cut of scope vs cut of qualification
6. The attack-surface check
7. Holding lower layers fixed by contract
8. Honest vs revisionist narrative
9. Disclosing what is withheld
10. Stating inheritance and lineage
11. Splitting across documents, and the identity risk
12. Assumptions and limits

---

## 1. What is being filtered, and the filter ledger

The input is the inventory and decision list from reading (`reading-a-system.md`), not the repo tree. Every row is true; most will not get full treatment. The filter's job is to keep the causal path from thesis to evidence short and visible, without changing what any surviving sentence means.

Keep a filter ledger with one row per substantial item cut or demoted. The columns that earn their place:

- **Item** and the **rule** that cut it (§2).
- **Destination** (§3), decided before the cut is made.
- **Does its absence change the truth of any claim?** This is the column that matters most. A "yes" means the cut is a cut of qualification (§5) and must be reversed or moved into the claim's sentence.
- **Does its absence open an attack surface?** If a practitioner would ask about it, either scope it out in one sentence or engage it (§6).
- **Did it drive adoption?** If so, record where it will be documented (§4).

Include opponents you chose not to engage and why. MapReduce's omitted relational comparison was a cut of the opponent, not of material, and it was punished publicly three years later.

The ledger is scaffolding. It is useful while it changes cuts; a complete ledger for the wrong thesis is still the wrong paper.

## 2. The rules, with evidence

These are the rules the classic papers actually applied, inferred from comparing each paper against its system.

| Rule | Keep if… | Clearest evidence |
|---|---|---|
| Causal chain | It is a link from the thesis to a claimed capability | Spanner keeps directories and Paxos groups because the timestamp argument runs over them; SQL, a decade-scale investment by the 2017 account, gets one sentence |
| One organizing idea | It does not need a second organizing idea to explain | vLLM cuts the API server, tokenizers, 14-model zoo, and quantization; Spark 2012 drops broadcast variables and accumulators |
| Named alternative | A reader would expect an alternative, and the paper can say why it was not taken | GFS keeps no client cache, 64 MB chunks, lazy garbage collection; RPC framing and thread model vanish |
| Measurable | It has a number attached | MapReduce keeps backup tasks (44% slower without); status pages go to refinements |
| Environment fixed | It is a lower layer: cite it, quantify only the dependency that can take you down | Bigtable on Chubby (0.0047% unavailability); MapReduce on GFS and the scheduler |
| Independently verifiable | A reader can check it without the system | AlphaTensor and FunSearch main texts keep the formulation and the checkable outputs |
| Implement and verify | An implementer needs it (for algorithm papers) | Raft's one-page specification and invariants; extensions deferred |
| Reader's checklist | A peer operator would ask about it (for concern-organized papers) | Borg |
| Credibility ballast | It is a small failure that motivated a choice | GFS's hot spot; Spanner's abandoned design in one sentence |
| Disclosure limits | Business or IP constraints prevent it, stated as such | Dynamo's aggregate-only numbers; Omega never names Borg |

**Effort is not a rule.** vLLM's 273-line block manager carries the paper; its roughly 8.4K-line model executor gets one sentence. Section length that tracks subsystem size is the catalog failure.

**When rules conflict**, the cases favor the causal chain over completeness. The exception is the concern-organized paper, where there is no single chain and the reader's checklist wins.

**A paper may break its own filter on purpose.** Dynamo's filter was "six techniques", yet §6.2 promotes three successive partitioning strategies with two figures, because the lesson lives in the evolution. Breaking the filter is fine when the promoted material is itself evidence for a claim; say why it is there.

**Size-without-argument material.** TVM's seven frontends, constant folding, memory planning, runtime, RPC, and five backends each became a clause, while fusion and layout kept a figure; breadth was claimed once and shown by one exemplar per hardware class. Breadth that matters to the claim can be shown by representatives rather than enumerated.

## 3. Destinations and how to choose

Demoting is not deleting. Decide where each item lives before cutting it, because material cut without a home disappears, and what the paper omits becomes part of how the system is understood.

| Destination | What belongs there | Test | Risk |
|---|---|---|---|
| Full treatment | Links on the causal chain; mechanisms with an expected alternative, a number, or a named rival | Does a claim depend on it? | Uniform depth if everything qualifies |
| Refinements bucket | Real optimizations not on the chain | Would a reader of the core miss it? If not, bucket it | Reads as a feature list without standing (MapReduce's nine refinements) |
| Implementation section | Necessary facts: size, language, substrate, kernel lineage | Does it argue anything? If not, here | Smuggling in a second organizing idea |
| One-sentence demotion | Dead ends, not-yet-implemented pieces, lineage, deliberate omissions | Would a reader wonder about it at this exact point? | Losing a lesson that deserved a paragraph |
| Appendix, Methods, SI | Proofs, secondary ablations, re-implementation detail, hardware primers | Is it needed to *understand* the contributions? If yes, it cannot go here | Hiding evidence the main claim needs |
| Companion paper | A second idea, or claims needing a different evaluation environment | Would it need its own thesis and experiments? | The flagship cannot defend the subsystem |
| Docs, blog, README | Practitioner material: breadth, setup, integrations, practitioner-baseline numbers | Who needs it: a user or a researcher? | Numbers that disagree with the paper |
| Nowhere | Mechanisms with no plausible alternative; defaults with no reason; plumbing | Does its absence change any claim's truth? Must be "no" | Silently dropping a qualification |

Notes on the less obvious destinations:

**The refinements bucket** (Bigtable §6, MapReduce §4) keeps the core section readable by moving real optimizations under a title that marks them as non-core. It works best with a sentence explaining why each exists. Without the authors' standing, a long bucket reads as catalog.

**The implementation section** means different things depending on the claim type: a short quarantine of facts that argue nothing (vLLM §5), or the argument itself with matching experiments (PyTorch §5, MapReduce §3). `argument-architecture.md` §5 has the table. In every case it must not introduce a new organizing idea.

**One-sentence demotions** show maturity cheaply when placed where the reader would ask: "An early Spanner incarnation supported multiple Paxos state machines per tablet … led us to abandon it"; "Borg originally used a variant of E-PVM"; Spanner's double logging "out of expediency" and "not yet implemented" clauses. The same device declares scope: "We will not go into detail about the query language."

**Appendix, Methods, SI** follow the venue. Nature papers tier explicitly: main text for what a scientist needs to believe the result, Methods for what a practitioner needs to re-implement, SI for what an expert needs to audit. AlphaTensor's ablation showing synthetic demonstrations are worth 15 ranks sits in the SI because "the engineering matters" is not a main-text claim there. That tiering depends on outputs being checkable without the system. In a systems venue, where claims are about system behavior, the mechanism and its evidence belong in the body, and appendices hold proofs and secondary detail (Spanner's lease-disjointness appendix; FlashAttention's backward pass).

**Companion papers** are the right destination when an item needs a different kind of evidence. Ceph split by research question and evaluation environment: placement needed simulation and analysis (CRUSH), replication and recovery needed failure-injection runs (RADOS), the integrator needed scale runs. Spanner deferred SQL to a 2017 paper. Raft's dissertation carried the simpler single-server membership change, compaction, and the full study.

**Docs, blog, README** carry practitioner material and practitioner-baseline numbers. vLLM's blog claimed up to 24× over HuggingFace (what practitioners ran), the paper 2–4× over research baselines. That division of labor is reasonable if each number states its baseline and the paper's claims stay within the release.

**Nowhere** is legitimate for plumbing with no plausible alternative. It is the destination most likely to hide a cut of qualification, so every "nowhere" row needs a "no" in the truth column.

A quick sequence for an uncertain item: Is it on the chain? → full treatment. Does a reader expect an alternative here, or is there a number? → full treatment or a refinements entry. Is it a fact the reader needs to trust the artifact? → implementation section. Would a reader wonder about it at a specific point? → one sentence there. Does it need its own evidence? → companion paper. Does a user need it? → docs. Otherwise, nowhere, after the truth check.

## 4. What drove adoption is what gets filtered

A pattern the cases show repeatedly: TensorBoard, PyTorch's ecosystem, vLLM's model zoo, Spanner's SQL, Ray's libraries, and Ceph's block and object gateways were cut or compressed in their papers, and each was a large part of why people used the system. The filter is right for the claim: breadth and tooling are not ideas, and a paper cannot cash them in as contributions. But the paper's contribution and the system's value come apart, and that has consequences.

Handle it in four ways.

1. **Do not imply the concept explains adoption.** "Widely deployed" next to the thesis suggests the thesis is why. If adoption came from breadth or usability, keep adoption figures in the reality-status sentence, not in the argument for the idea.
2. **Note the omitted value's existence in a clause, and document it elsewhere.** One sentence in the implementation section ("vLLM supports N model families; see the documentation") keeps the paper honest about what the system is, without arguing it.
3. **Ask whether the gap reveals a mispositioned thesis.** TensorFlow's OSDI paper argued that stateful dataflow subsumes the parameter server and evaluated throughput; adoption turned on usability, the property PyTorch made central. The case writer calls the paper "locally excellent against its own schema and globally mispositioned". When the adoption driver and the thesis differ, decide deliberately whether the thesis is still the claim the authors want to be held to.
4. **When adoption is the evidence, say so.** PyTorch was accepted largely on adoption; MapReduce, Bigtable, and Dynamo used deployment as evidence of generality. That move rests on authority and scale. For a group without them, adoption is a reality-status fact, not evidence for a mechanism claim, and the paper needs evidence that stands without it.

## 5. Cut of scope vs cut of qualification

A **cut of scope** removes a topic; the remaining claims mean what they meant. TensorFlow dropping TensorBoard, Spark dropping broadcast variables, Spanner deferring SQL, Dynamo's opening inventory of production concerns it will not cover. These are healthy and usually improve the paper.

A **cut of qualification** removes a condition, restriction, or attribution, and the remaining claim reads stronger than the system supports. The test is one question: *with this item removed, would a reader believe something about the system that is false?*

The forms it takes in the cases:

- **Restriction dropped from a deployment claim.** AlphaDev's "integrated into LLVM" omits that the libc++ change was restricted to arithmetic types, with mixed benchmarks on some orderings.
- **Attribution moved.** AlphaTensor's "more than 70 tensors" improvement is credited to the agent, though a recombination pipeline produced it from small factorizations.
- **Load-bearing condition left unstated.** GFS's relaxed consistency depended on the team controlling all clients ("we controlled both the horizontal and the vertical"), which the paper only half-says.
- **Denominator dropped.** AlphaEvolve's results release excludes problems where results only matched the state of the art "to avoid clutter", so the matched half of its breadth claim cannot be checked.
- **Release scope.** vLLM's evaluated shared-prefix feature was absent from the public tag two weeks after the preprint.
- **Opponent dropped.** MapReduce's missing relational comparison (§6).

Where the qualification goes matters as much as whether it is present:

- **Inside the sentence that makes the claim**, when it changes the claim's meaning. AlphaEvolve puts its complex-valued caveat in the Table 2 caption; TVM follows its 40× FPGA result with an Amdahl's-law sentence about the end-to-end gain.
- **Next to the claim it most threatens**, for the limitation that would most reduce belief in the abstract's first claim. Papers tend to be candid about costs that do not threaten the headline (Spanner's "out of expediency" and "not yet implemented" clauses sit in §2, §4, and §7, never in the abstract) and quiet about the one that does.
- **Also aggregated** in a limitations or scope section, because inline caveats alone are missed by skimming readers (TVM and JAX have only inline caveats).

Scope statements are not concessions. RDD's "Applications not suitable for RDDs", vLLM's statement that its techniques may degrade training, and FunSearch's applicability conditions all made the remaining claim harder to attack.

## 6. The attack-surface check

The omissions that hurt the classics were not missing features; they were missing comparisons.

| Omission | Who exploited it |
|---|---|
| MapReduce never discusses relational systems | DeWitt and Stonebraker (2008); the 2010 rebuttal had to add the comparison |
| Bigtable presents "single-row transactions suffice" as a finding | Megastore, Percolator, Spanner cite the complaints |
| vLLM dismisses dynamically growing contiguous allocators in two sentences | The gain depends on a max-length reservation baseline |
| AlphaTensor has no fixed-compute search baseline | Flip graphs matched most of its table within two months, without a neural network |
| AlphaDev omits the patch's restrictions and calls the result a "new sorting algorithm" | Critics: "one MOV" in an unrolled sort |
| AlphaEvolve never discusses evaluator exploitation | Tao et al. found it first-order |

The procedure:

1. **List three things a skeptical practitioner would use instead**, once per adjacent community (databases, compilers, operations research, theory, the practitioner who would just add a cache). FunSearch's hedges read like one per reviewer type.
2. **Check each against the evaluation and related work.** Any alternative absent from both is a future attack surface.
3. **Engage the strongest operational alternative**: what a practitioner would actually deploy, not the cleanest research foil. Engaging can mean measuring it, or scoping it out in one sentence with a reason, as RDD's "Applications not suitable for RDDs" does by sending asynchronous fine-grained updates to Piccolo, RAMCloud, and Percolator. Scoping out is acceptable; silence is not.
4. **Watch for straw comparison classes.** A category defined so that the live alternative falls outside it (RDD's "DSM", which left MapReduce plus caching unmeasured) or a table on which you lose no row is an omission in disguise. Checks for positioning tables are in `argument-architecture.md` §8; the straw-believer check is in `finding-the-contribution.md` §1.3.
5. **Simulate the critic of significance as well as the critic of method.** AlphaDev's comparison with its own superoptimizer satisfied method critics; the public critique attacked significance.

Record what you chose not to engage, and why, in the filter ledger.

## 7. Holding lower layers fixed by contract

A paper owns one or two layers. The layers below are environment: describe them by the contract the system uses, not by their internals.

- **State the contract used.** Bigtable's "Building Blocks" section names GFS, SSTable, and Chubby, lists exactly what Chubby is used for, and then uses "files in GFS" and "locks in Chubby" as primitives.
- **Quantify the one dependency that can take you down.** Bigtable measured Chubby-caused unavailability (0.0047% averaged over 14 clusters). One number answers the reader's main worry about the substrate.
- **For a closed substrate, list the properties it must provide and argue portability.** Pathways lists what PLAQUE must offer and says the design could be re-implemented on Ray, naming the gaps. That detaches the idea from a system readers cannot inspect.
- **Depend on widely exercised features**, and say so. Bigtable's lesson from its membership-protocol rewrites was that depending on seldom-exercised Chubby features caused trouble.

Two warnings. First, held-fixed layers get probed: MapReduce declared GFS and the scheduler "not the focus of this paper" while depending on them for locality and worker restart, and critics later went after exactly those layers. Second, when a property of the environment is load-bearing for a claim, it is no longer neutral environment; it is a premise. GFS's control of all client applications was environment in the paper's representation and a condition of validity in reality. Move such properties into the premise register and state them where the claim depends on them.

## 8. Honest vs revisionist narrative

The advice literature disagrees: Levin and Redell call a rational derivation that hides a different real path close to dishonest; Widom and Peyton Jones advise telling only the story of the results. The cases settle it as follows.

**Argument order is allowed; false causation is not.** Presenting premises before consequences is an expository choice and claims no history, unless the text makes it one. Raft puts "Designing for understandability" before the algorithm, which is argument order. GFS says its design "has been driven by" workload observations, while its authors later said the single master was "one of the very first decisions, mostly just to simplify the overall design problem", made to ship quickly. The workload facts are true, so the derivation is a valid argument; "driven by" makes it a false history. The fix costs a phrase: "is justified by".

**Two tests for any causal sentence:**

- *Retrospective test:* would a retrospective by the same authors contradict it? If yes, rewrite now. Distinguish this from an honest snapshot that aged: Ray's GCS-on-the-critical-path principle was later inverted by Ownership, and DynamoDB dropped Dynamo's mechanisms, but neither paper misstated why its design was chosen at the time.
- *Applicability test:* would a reader deciding whether the design fits *their* system be misled by the omission of the real reason? If yes, state the real reason as a boundary condition ("this works because the storage team controls every client"), even if the exposition stays deductive.

**Documented vs reconstructed alternatives.** Large systems accumulate decisions without records, and an agent is especially tempted to supply plausible alternatives nobody considered and present them as design history. Keep the two voices separate in the text:

- Documented: "We chose X over Y because…" Use only when a design doc, review, revert, or the authors confirm Y was considered.
- Reconstructed: "An alternative would be Y; it would cost…" This is analysis in its own voice, legitimate and often clearer than the history.

Do not let the second slide into the first during editing. The alternatives register's provenance column exists to prevent it.

**Dead ends that carry lessons get a paragraph; the rest get a sentence or nothing.** Raft keeps exactly one abandoned design, the ranking-based election dropped because "new corner cases appeared", because it is the evidence for the principle. Bigtable's thrice-redesigned membership protocol is its most transferable passage. Dynamo's three partitioning strategies are promoted for the same reason. A dead end that taught only "this was buggy" does not earn space.

**Failures that motivated choices vs failures that challenge premises.** Papers keep the first kind and omit the second, which is a selection effect worth correcting. A hot spot that led to a replication fix makes the design look responsive. A premise that broke in production tells the reader where the design stops applying, which is what they most need. HDFS's "our assumption that applications will create a small number of large files was flawed" is that paper's best candor and sits in future work; it belongs next to the premise it refutes. Ask the authors directly whether premise-challenging failures are known, since docs rarely record them.

**Over-attribution is a narrative problem too.** An abstract whose grammar credits several features to one concept asserts causation. Spanner credits three features to TrueTime; Brewer's later note narrowed that. Check each "enables" against the causal graph before it goes into the abstract.

## 9. Disclosing what is withheld

Withholding is often unavoidable. Saying that you withheld, and what compensates, is not.

**Confidentiality.** Dynamo states that business interests limit it to aggregate numbers. Omega describes an architecture without naming the production system it was built against. Say what category of information is withheld and why, once, early. Readers discount undisclosed gaps far more than disclosed ones.

**Closed systems, checker-only releases, open systems.** What each openness level lets the evidence claim, and what compensates when the system is withheld (Pathways' kit for closed systems; the output-claim vs process-claim split for releasing a checker but not the generator; release agreement for open systems), is in `evidence-and-venue.md` §9.2. The honesty point here is narrower: say in the paper which of these situations applies and which claims it limits, rather than letting readers discover it from the repository.

## 10. Stating inheritance and lineage

Crediting the nearest predecessor sharpens the claim rather than weakening it, because it tells the reader exactly where to look for what is new.

- **The pattern that works:** one sentence naming the predecessor and one stating the delta. TVM credits Halide's compute/schedule separation and claims "also separating target hardware intrinsics". RDD says it does "not claim novelty for the language-integrated interface" it shares with DryadLINQ, which points the reader at the data-sharing and recovery model instead.
- **The patterns that fail:** credit pushed to acknowledgments or an appendix (FlashAttention's lineage from Apex FMHA), or no credit at all (HDFS never names GFS as its origin, which also removed its most natural thesis).
- **Owning the predecessor makes criticism cheap and credible.** TensorFlow criticized DistBelief; Ownership used Ray's earlier design as a labeled baseline. When the predecessor belongs to someone else, hedge as TensorFlow did ("other systems based on this architecture have addressed these limitations in other ways").
- **Inheritance includes borrowed components.** If a kernel, protocol, or library came from elsewhere, say so where it is used. Readers who later find undisclosed lineage reread every novelty claim with suspicion.

Prior public claims are part of lineage too. Numbers in launch posts, READMEs, and earlier preprints must agree with the paper or the difference must be explained.

## 11. Splitting across documents, and the identity risk

For a large system, the paper is one document in a set. A workable division of labor: the paper carries the idea and the why; docs carry the what and how to use it; the blog or README carries the practitioner headline; companion or later papers carry second ideas and rebalancing; a dissertation or technical report carries completeness.

**Decide the set before cutting.** Material cut without a planned home disappears. Write the filter ledger's destination column with real document names, even if the documents do not exist yet.

**The flagship's omissions become the system's identity.** Until another document corrects it, what a widely read paper leaves out is read as what the system lacks or doesn't care about. The Spanner SQL paper had to say "today's Spanner is very different from what was described there". Two defenses:

- **Name deliberate omissions in the flagship.** "We will not go into detail about the query language" tells readers the omission is a choice, not an absence. Dynamo's opening list of production concerns it will not cover does the same.
- **Plan the catch-up document**, and say in the flagship that it exists or is planned ("another paper"), if that is true.

**Keep one reconciliation list across the set.** Every additional document is another place for numbers to disagree (vLLM's blog 24× over HuggingFace vs the paper's 2–4× over research baselines: both true, different baselines).

When to split into several papers, in what order, what splitting costs, and what skipping the paper costs (XLA) are in `finding-the-contribution.md` §10.

## 12. Assumptions and limits

The rules and destinations are inferred from successful papers compared against their systems. That shows which omissions were later exploited, not that following these rules makes a paper succeed. Several cases (GFS's revised causation, Dynamo's telemetry-only evidence, Borg's lessons) survived filtering choices that an unknown group could not afford, so the honesty tests here are set to the standard a group without that authority will be held to.
