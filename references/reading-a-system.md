# Reading a system for a paper

Read this when you have a pile of material about a system (docs, specs, RFCs, code, git history, issues, dashboards, postmortems, experiment logs, launch posts) and need to turn it into candidate claims. It covers what to look for, where each kind of source hides it, the order to read in, how not to be captured by the material's own organization, and what to bring to the authors. Its outputs feed `finding-the-contribution.md` and `filtering-and-honesty.md`.

## Contents

1. What the reading produces
2. Pin the version before anything else
3. Reading order, and what each pass is for
4. Decision signals in detail
5. What to mine from each source type
6. Guarding against capture
7. The inventory with layer tags
8. The mechanism → capability causal graph
9. When the system offers no single idea
10. Preparing questions for the authors
11. Assumptions and limits

---

## 1. What the reading produces

The goal is not an understanding of the system in the maintainer's sense. A maintainer needs to know where things are and how to change them. A paper needs to know which decisions explain the capabilities, which of those a competent outsider would dispute, and what evidence exists for each. Those are different representations of the same system, and the second cannot be read off the first.

Concretely, the reading should leave you with:

- a **pinned snapshot** (commit, tag, or release) and a list of evaluated vs shipped features;
- an **inventory with layer tags**: each subsystem with the capability it serves, WHAT or HOW, contribution or environment, and evidence status (§7);
- a **decision list**: the decisions that carry signals from §4, each with its expected alternative and whether the reason is documented or reconstructed;
- a **causal graph** from decisions through mechanisms to capabilities, with fan-out counted and edge evidence marked (§8);
- first drafts of the **premise register** (workload and environment assumptions with numbers) and the **alternatives register**;
- a short list of **surprising measurements**, each paired with the belief it contradicts;
- a set of **questions for the authors**, each attached to a candidate framing (§10).

None of these is an outline. If you find yourself writing section headings during reading, stop: the outline comes after a thesis exists, and an outline drafted during reading will copy the structure of whatever you were reading.

## 2. Pin the version before anything else

A paper describes a version, and large systems move under their papers. Ray's OSDI paper describes the first-generation core (tasks, actors, GCS, bottom-up scheduler, object store) of a system whose later whitepapers cover memory model, spilling, placement groups, autoscaling, multitenancy and more, and whose control plane was later inverted. vLLM's paper describes essentially the June 2023 codebase (the paper says 8.5K lines of Python and 2K of C++/CUDA; v0.1.0 had about 7.3K and 2.5K), while v0.2.0 had nearly doubled.

Decide early, with the authors:

- **Which snapshot.** Name a commit or release. Read docs and code at that snapshot, not at HEAD; current docs describe a different system and will import vocabulary the paper's version did not have.
- **Evaluated vs shipped.** List every feature the evaluation exercises and check it exists in the snapshot or the release the paper will cite. vLLM's shared-prefix sharing has a full evaluation section (1.67× and 3.58×), and a grep of the v0.2.0 tag, two weeks after the arXiv posting, finds no prefix-sharing code (the case writer cannot rule out a private branch). That kind of gap is cheap to catch while reading and expensive after publication.
- **Planned rewrites.** Read the roadmap, open RFCs, and the issue tracker for components marked for replacement. A component about to be rewritten is a poor home for the thesis, or must be labeled as a snapshot. JAX's paper framed itself around a mechanism (PSC tracing, the `jit_ps` API) that the project later dropped; the README now cites the paper as "a nascent version".

If the system is still changing fast, this is also when to note that its evidence should lean on WHAT-layer claims (invariants, mechanism ablations, parity) rather than numbers the next release will change.

## 3. Reading order, and what each pass is for

The order puts discovery before verification. Each pass has a different job, and reading a source for the wrong job wastes it.

| Pass | Sources | Read for | Produces |
|---|---|---|---|
| 1. External claims | Launch posts, READMEs, talks, prior preprints | The numbers practitioners and reviewers have already seen; the baseline practitioners use; the team's own one-line pitch | Claims to reconcile later; a first guess at the opponent |
| 2. Guarantees and non-goals | Specs, API contracts, "semantics" sections, FAQs, non-goals lists | Invariants and the conditions under which they weaken; scope boundaries | Candidate invariants; draft scope statements |
| 3. Alternatives and dead ends | Design docs, RFCs, design-review threads, reverted commits, rewrites, deprecated directories | What the team argued against; what was tried and abandoned and why | Alternatives register (documented entries); dead-end candidates |
| 4. Surprising measurements | Experiment logs, notebooks, dashboards, A/B rollouts, postmortems, incident counts | Numbers the team found surprising; places where a proxy and the outcome disagree; premises that broke | Opponent candidates; premise register; failures that challenge premises |
| 5. Code and history | Source at the pinned snapshot; git log, churn, blame | Confirming that chosen mechanisms exist and do what the docs say; locating the component that produces the benefit; stability of interfaces | Verified causal-graph edges; size facts; layer boundaries |

Two reasons for putting code last. First, code is organized by implementation decomposition, and reading it first imports that decomposition as the outline (§6). Second, code answers "how" with great precision and "why" almost never; you need the "why" candidates in hand before the code becomes useful as a check.

Pass 1 is rarely where the thesis lives, but skipping it causes a specific failure: the paper's numbers disagree with numbers already in circulation. vLLM's launch blog said up to 24× over HuggingFace Transformers; the paper said 2–4× over research baselines. Both are true and answer different baselines, but the gap set expectations the paper did not support.

Loop back freely. A surprising measurement in pass 4 often sends you back to pass 3 to find whether anyone argued about it.

**When a pass has no sources.** Most real piles are missing something, and each gap shifts work elsewhere:

- *No design docs or reviews* (common in fast-moving open projects): the "why" has to come from reverts, commit messages, issue threads, and old versions of the project's own descriptions, and then from the people. Expect most alternatives to be reconstructed, and label them so.
- *No usable history* (a closed system, a squashed import, a monorepo export): rely on successive internal versions of docs, and on sibling or later documents by the same team. The Spanner and Borg case files reconstruct the "real system" from the 2017 SQL paper, Brewer's note, and Omega, which is the best public evidence when code is closed.
- *No measurements beyond benchmarks*: the surprising-measurement pass comes up empty, and the opponent has to come from design-review objections or the price charged to users. Note early that the paper will need a new experiment to show a proxy and outcome coming apart.
- *No external claims yet* (pre-launch): you are free of reconciliation constraints, but whatever the paper says first will become the system's public framing (see `filtering-and-honesty.md` on identity).

## 4. Decision signals in detail

SKILL.md lists the signals. This section gives, for each, what it looks like in raw material, the test that separates a real signal from a false one, and the common misread.

| Signal | How it shows up in raw material | Test | Common misread |
|---|---|---|---|
| Fan-out | A choice that design docs cite as the reason for several other choices; many code paths that assume one property | Count capabilities that disappear if the decision is reversed; ≥3 is a thesis candidate | Crediting capabilities that rest on other mechanisms (below) |
| Departure from the obvious | Design-review objections; FAQ entries defending a choice; "why don't we just…" issues | Name the alternative a reader expects; if none, it is not a signal | Departures from *your* expectation that no field reader shares |
| Removal | "We don't need X"; a deleted subsystem; a component every predecessor had and this one lacks | Is the function really gone, or renamed and moved? | TensorFlow's "no such thing as a parameter server", while deployments still ran "PS tasks" |
| Invariant or contract | Spec sections on semantics under failure; assertions; model-checking specs; consistency tables | Is the condition under which it weakens stated? | Treating a test suite's coverage as the invariant |
| Stable interface | Low-churn API files above rewritten internals | Did the interface survive at least one major internal rewrite? | Stable because unused or vendored, not because right |
| Surprising measurement | "Huh" moments in notebooks; dashboards someone bookmarked; postmortem findings | Does it contradict a belief someone outside the team holds and can be cited for? | Surprising only relative to the team's own earlier guess |
| Change of unit | A new granularity in data structures or scheduling (per block, per iteration, per memory access) | Does the new unit explain several design choices at once? | A refactor that changed units without changing what is possible |
| Price charged to users | Restrictions in user docs ("must be pure", "static shapes"), error messages, "not supported" issues closed as wontfix | Does the restriction buy a property the system depends on? | A missing feature that buys nothing |
| Dead end that produced a rule | Reverts, abandoned branches, "we tried X first" in design docs | Can the failure be stated as a principle the final design obeys? | A dead end that taught only "this was buggy" |

**Fan-out needs attribution checks.** The test is "would this capability disappear if the decision were reversed?", not "is this capability mentioned near the decision?". Spanner's abstract credits external consistency, lock-free snapshot reads, and atomic schema changes to TrueTime; Brewer's 2017 note is blunter that serializability comes from two-phase locking and snapshot reads rest mostly on multiversioning plus safe time. Spark's headline 20× is decomposed by the paper's own breakdown into avoided HDFS reads, parsing, and deserialization, not lineage. Do this check during reading, while the evidence is in front of you, not after the abstract is written.

**Removals need a function check.** For each claimed removal, write what the removed component used to do and find where that work happens now. If it happens somewhere, the honest claim is about where it moved and what that bought (TensorFlow's real delta was the same topology with a more general program running on the server tasks), not that it vanished.

**Prices are often the honest form of the idea.** JAX requires pure functions, XLA static shapes, TVM schedule templates, GFS applications that tolerate duplicate records. The XLA/JAX/TVM case notes these obligations were footnotes in each paper and were exactly what later systems changed. Collect them from user-facing docs, error messages, and declined feature requests; each one is both a candidate thesis ("restricting X buys Y") and a scope statement.

**Engineering that is necessary but not argument** has its own signals: breadth (model zoos, frontends, backends), operability with no number attached (status pages), ecosystem tooling, parameter defaults, and what the environment provides. Tag it in the inventory rather than discarding it; it often drove adoption, and `filtering-and-honesty.md` covers where it goes.

## 5. What to mine from each source type

### Launch posts, READMEs, talks

Extract every number and note the baseline each answers. Extract the one-sentence pitch; it is rarely the thesis, but it shows what the team thinks is distinctive and what practitioners repeat. Note operational facts mentioned in passing: vLLM's launch blog says it had been deployed at Chatbot Arena and Vicuna Demo for two months, which the paper never mentions. Facts like that are evidence a paper may want, or claims it must reconcile.

### Specs, API docs, semantics sections

Look for guarantees stated with conditions. MapReduce's semantics-under-failure section, GFS's consistent/defined table, Spanner's invariants, and Raft's property figure are the most quotable units their papers have. In a new system, these are usually spread across a spec, an FAQ, and comments on a few key functions; assemble them into one list with the weakening conditions next to each. Also collect explicit non-goals: they are free scope statements.

Expect docs to answer "what is it" and "how do I extend it", not "why this and not that". XLA's architecture page lists objectives and pipeline stages and has no alternatives, tradeoffs, or limitations. Treat silence about alternatives in docs as missing data, not as evidence there were none.

### Design docs, RFCs, design reviews

These are where the opponent is written down, though rarely labeled as one. Read for: the alternatives considered and the stated reason each was rejected; the objection that reviewers raised repeatedly; claims the authors had to defend. Spanner's "an early incarnation supported multiple Paxos state machines per tablet" and GFS's abandoned attempt to persist chunk locations are the kind of entry this material yields.

Read design docs for content, not for order. They are ordered by when decisions were made or by a lineage story (the TensorFlow whitepaper opens with DistBelief history). The paper will be ordered by argument.

Record each alternative with its provenance: *documented* (a doc, review, or revert shows it was considered) or *reconstructed* (you inferred a reader would expect it). Keep the two apart from the first entry; mixing them is how reconstructed alternatives end up presented as history.

### Git history

The repository records decisions the docs omit. Useful queries, run against the pinned snapshot's history:

- **Churn by path.** `git log --since=<date> --numstat --format= -- <dir>` summed per directory, or `git log --format= --name-only | sort | uniq -c | sort -rn`. Look for interface or API files with low churn sitting above implementation directories with high churn. Ray's task/actor/futures API survived its control plane being inverted; a thesis placed at a layer like that ages better. Adjust for renames (`--follow`, `-M`) and for vendored or generated code, which is stable for reasons unrelated to design.
- **Reverts and rewrites.** `git log --grep='[Rr]evert'`, plus large deletions (`--diff-filter=D`, or commits with deletions far exceeding additions in one directory). Each revert of a design-level change is a candidate dead end; the commit message or linked review often states why. Bigtable's thrice-redesigned membership protocol, which ended up depending only on widely used Chubby features, is the paper-level form of what a revert trail looks like.
- **Deleted and renamed top-level components.** What was a co-equal subsystem two years ago and is now gone or folded in? Spark's broadcast variables and accumulators were co-equal abstractions in the 2010 paper and unmentioned in 2012. What keeps getting promoted across versions is a thesis candidate; what keeps getting demoted tells you something too.
- **Config and defaults history.** `git log -p` on config files and default-parameter definitions. Which knobs changed default, which were added in response to incidents, which settings users actually choose. Dynamo reports the common (N, R, W) setting, (3,2,2), and a small taxonomy of usage patterns; that is how a single-company system supported "general store" without benchmarks. Config history is also where premises are encoded as numbers.
- **Dates against claims.** Tags and commit dates show what existed when. AlphaDev's libc++ change was committed in April 2022, before the Nature submission, and restricted to arithmetic types; the paper's "integrated into LLVM" lacks that restriction. Dates also tell you which version a talk or blog describes.
- **Commit messages that name alternatives.** `git log --grep` for "instead of", "rather than", "tried", "doesn't work", "workaround". These are cheap documented-alternative entries.
- **Who touched what.** Blame and author spread identify whom to ask about which decision. They do not measure importance.

Lines of code are a fact, not a ranking. vLLM's 273-line block manager carries the paper; its roughly 8.4K-line model executor gets one sentence.

### Issue trackers and code review

Read for the objection the team keeps answering. Repeated "why doesn't it support X" issues closed as by design mark the price charged to users. Repeated "this is slow when…" issues mark the regime where the idea stops working, which is the raw material for a "not suitable for" paragraph. User docs that warn the reader something "may be surprising" (PyTorch on putting usability over performance) point straight at the belief under attack.

### Dashboards, telemetry, experiment logs

Look for three things.

- **Numbers the team found surprising**, especially where a believed proxy and the real outcome come apart: FlashAttention's more FLOPs, far less HBM traffic, faster; vLLM's finding that only 20–38% of KV memory in existing systems holds tokens; Dynamo's order-of-magnitude gap between average and 99.9th-percentile latency. A surprising number usually marks a belief the paper can overturn, and it often becomes the paper's bridge figure.
- **Distributions, not points.** Fleet-wide or per-cluster distributions resist cherry-picking claims; note where they exist (Borg's compaction results across 15 cells) so evidence design can use them.
- **Natural experiments.** A/B rollouts, staged deployments, and feature flags can isolate a mechanism's effect without a new experiment. Check what else shipped in the same stage. If the rollout that cut p99 latency also introduced request batching, the null-idea test fails on that data: batching alone could explain the drop. Look for a stage where only one change shipped, or record the confound so the claim can be worded as a joint effect with the idea's share measured or marked unmeasured.

### Postmortems and incident records

Sort every failure into two piles. **Failures that motivated a choice** (a hot spot that led to a replication rule) are what papers usually keep; they make the design look responsive. **Failures that challenge a premise** (an assumption about workload, scale, or environment that turned out false) are what papers usually omit, and they matter more to a reader deciding whether the design applies to them. HDFS's "our assumption that applications will create a small number of large files was flawed" is that paper's best candor and is buried in future work. GFS's paper keeps the hot-spot story; its authors' later interview adds that record append was "more painful than it was worth" and that one cell per data center did not hold. Ask the authors whether failures of the second kind are known; postmortems are where you find the leads.

Also count. HDFS's 0.8% of nodes lost per month and about 20 bad replicas per fortnight are operational data that a GFS-lineage paper uniquely had; Paxos Made Live organizes its failures by category and extracts reusable abstractions from them. If the system's postmortems are numerous, a categorized count may be a contribution in itself (§9).

### Migration notes, deprecations, old versions, prior papers

Diff old versions of the system's own descriptions. TVM's four hardware challenges, mapped one-to-one to features in arXiv v1, became two challenges (expressiveness, search) once a learned cost model arrived, and "Automated" entered the title. Ray's seven position-paper requirements collapsed to three. Migration guides record before/after effort and what users had to change, which is evidence for usability claims and for the price charged.

### Code, last

With candidate mechanisms in hand, use the code to verify four things: the mechanism exists at the snapshot; it does what docs and design reviews say; the component that produces the benefit is the one the team's vocabulary credits (vLLM's name points at the attention kernel, which the paper's own microbenchmark shows is 20–26% slower in isolation, while the block manager delivers the benefit); and the layer boundaries are where you think they are, so lower layers can be described by the contract actually used.

### People

Some things no artifact holds: which reason was load-bearing, which alternatives were seriously considered, which failures are known but undocumented. Gather these through the questions in §10, after the reading has given you something specific to ask.

## 6. Guarding against capture

The default failure is an outline isomorphic to whatever was read most: the repo tree, the docs' table of contents, or the design doc's chronology. HDFS and GFS cover similar component inventories; HDFS has one subsection per component with parameter defaults, while every GFS subsection serves one of four premises stated in the introduction. Same raw material, different organizing unit. Capture comes in several forms.

- **Module structure.** Assign modules to claims before any outline exists. A module that serves no claim goes to the implementation section or out. Rename test: if renaming the repo's top-level directories would change your outline, the outline was captured.
- **Doc chronology.** Design docs and position papers order material by when it was decided or by lineage. Ray's introduction, inherited from that kind of material, reaches "we propose Ray" in paragraph 7. The paper's order is argument order. Note that reordering raises an honesty question, covered in `filtering-and-honesty.md`.
- **The docs' voice.** Docs are written for users and extenders. Their vocabulary is useful; their emphasis is not evidence of what is argument-worthy.
- **The team's pride and recency.** Authors often guess wrong about which part will last. Ceph led with metadata-server partitioning while RADOS outlived it; Bigtable elevated "single-row transactions suffice" while its data model and SSTable design survived. What the team worked on last or hardest is not thereby the thesis.
- **Lines of code and effort.** Section length that tracks subsystem size is the catalog failure.
- **The README's numbers.** External claims are framed for practitioners against what practitioners run. A paper that inherits them inherits their baseline.

A cheap check: after reading, write a 300-word explanation of the system with no component names, and have a fresh reader (or a fresh agent context) state the idea back. If the restatement is a list of components, the reading has not yet found a decision.

## 7. The inventory with layer tags

The inventory is the bridge between the system's units and the paper's. Build it per subsystem, but with columns that force a claim-oriented reading. The columns, and why each is there:

- **Capability served.** The user-visible thing this subsystem makes possible. If you cannot name one, the subsystem is environment or support.
- **WHAT or HOW.** Is this a specification (interface, formulation, invariant, metric, requirement) or an implementation of one? WHAT-layer items are where durable theses live; HOW-layer items are realizations that demonstrate them.
- **Contribution or environment.** Did this team build it as part of the idea, or is it a substrate the idea runs on? Environment is described by contract and held fixed.
- **Expected alternative**, with provenance (documented / reconstructed).
- **Evidence**: what exists (benchmark, telemetry, proof, ablation-capable build), what could be produced in time, and its status (measured / argued / asserted).
- **Adoption role.** Did users come for this? Breadth and tooling often score high here and low on everything else, and that combination is the signal to route them to a companion document rather than the argument.
- **Size and churn.** Facts for the implementation section and for the stable-interface signal, not rankings.

A sketch of what the vLLM case's reading yields:

| Subsystem | Capability | Layer | Role | Expected alternative | Adoption role |
|---|---|---|---|---|---|
| Block manager (273 lines) | Near-zero KV waste; cheap sharing | HOW realizing a WHAT (paged KV memory) | Contribution | Contiguous allocation (documented as the incumbent); growable contiguous chunks (dismissed in two sentences) | Indirect |
| Attention kernel | Reads non-contiguous KV | HOW | Contribution, but not where the benefit comes from | Contiguous kernel | Low |
| Scheduler and preemption | Admission under memory pressure | HOW | Contribution, secondary | Swap vs recompute (both measured) | Low |
| Model executor (~8.4K lines, 14 models) | Breadth | HOW | Support | — | High |
| Entrypoints, tokenizer, async engine | Usability | HOW | Support | — | High |

The table's value is in the disagreements it exposes: the named component is not the benefit-producing one; the largest subsystem is support; the highest-adoption rows carry no argument. Do not fill every cell for every subsystem. Stop when the rows that matter are clear.

## 8. The mechanism → capability causal graph

The inventory lists parts; the graph shows how they compose. Nodes are decisions, mechanisms, and capabilities. A directed edge from A to B means "B would disappear or degrade if A were reversed". Draw it from the decision list and the inventory, then use it four ways.

1. **Find thesis candidates.** Roots with fan-out of three or more capabilities are candidates. MapReduce's graph has one deep root: the restriction to deterministic functions → safe re-execution → fault tolerance, backup tasks, load balancing. Spanner's graph has TrueTime feeding commit wait, which feeds several transactional features.
2. **Check attributions.** Label each edge with its evidence: measured (an ablation or toggle), argued (a proof or mechanism-level reasoning), or asserted. Edges that the abstract will rely on and that are only asserted are the claims that later get attacked. If a capability has two incoming edges (snapshot reads from both TrueTime and multiversioning), the paper must say which carries how much, or credit both.
3. **Derive experiments.** Each measured edge is an experiment you have; each asserted edge on the chain to a headline claim is an experiment you need. MapReduce's backup-task edge was tested by turning backup tasks off (44% slower). A graph with no toggleable edges will produce an evaluation of the whole system attributed to the idea.
4. **Identify what the filter will cut.** Nodes with no path to a claimed capability are refinements, implementation facts, or companion material. Nodes on a path but with an untested edge are where the paper must either produce evidence or weaken the claim.

Keep the graph small: the decisions with signals from §4 and the capabilities a reader would care about, not every function. Its shape also tells you which genre the system supports (§9).

## 9. When the system offers no single idea

Some systems do not have a decision with fan-out ≥3 that a competent reader would dispute. Forcing one produces a slogan. Signs, from the reading itself:

- The causal graph has no root with fan-out ≥3, or several roots with no shared descendants.
- Every thesis candidate fails name removal: it cannot be stated without the system's name.
- The design was inherited (HDFS from GFS) or assembled from known techniques (Dynamo).
- The most interesting material is operational: failure counts, broken premises, policy outcomes over years.
- The system's value is distributed across many policies, none decisive alone (Borg).

The question is not "is there one idea?" but "is there any organizing principle other than the module boundaries?" Match what the reading found to the genre it supports:

| What the reading found | Genre | What it needs | Case |
|---|---|---|---|
| One root with high fan-out, a disputable belief, an isolating experiment | Idea paper | Mechanism concrete enough to re-implement; toggle or proxy/outcome evidence | MapReduce, FlashAttention, vLLM |
| Known techniques whose combination meets a requirement nobody met | Synthesis | The requirement made measurable; a problem → technique table; a sentence telling reviewers the contribution is the combination | Dynamo |
| A mature, revealed system; many policies; one metric that makes them comparable | Concern-organized | Reader questions stated up front; a cross-cutting metric; paired benefit and cost sections; lessons tied to reversed decisions | Borg (cell compaction) |
| A gap between theory and practice, documented through failures | Experience | A taxonomy of the gap; failures quantified; reusable abstractions extracted | Paxos Made Live |
| Inherited design, unique operational data, broken premises | Measured system / data | Data no one else has; premises stated and checked against it | HDFS's unused thesis: where an open GFS-style design's assumptions broke |
| A discovery made with the system | System as instrument | Checkable outputs; the system demoted to Methods | AlphaTensor, FunSearch |
| Several roots that need different evaluation environments | Split into several papers | One root per paper; an integrator first or last | Ceph (CRUSH by simulation, RADOS by failure injection) |

Conditions matter. Concern-organized papers work for mature systems with something to reveal and a unifying metric; a young system should not borrow the form. Omega, by overlapping authors with Borg, shows the forward-looking alternative: pick a taxonomy and an idea and simulate across the design space. Synthesis and operational papers need a venue or track that counts them (the operational tracks now say papers "need not present new ideas"). Authority helps these genres more than idea papers, which matters for a group without Google- or Amazon-scale credibility.

Record the genre decision as a candidate, not a conclusion. It goes to the authors with the others in §10.

## 10. Preparing questions for the authors

SKILL.md lists what only the authors can decide. Reading's job is to turn those into specific questions with candidate answers attached, because people answer "which of these would you defend?" better than "what is your contribution?".

Bring:

- **Two or three candidate framings**, each with at least its thesis sentence (no system name), the believer it opposes, and the decisive experiment (`finding-the-contribution.md` §9.1). Include one that you think is wrong but plausible; the authors' reason for rejecting it is often the most informative answer.
- **The reconstructed alternatives**, marked as such, with the question "was this considered, and why was it rejected?". Confirmed ones move to the documented column; the rest stay as analysis.
- **The suspected load-bearing reasons.** If the reading suggests a decision was made for schedule, team size, or control of all clients rather than for the stated technical reason, ask directly. GFS's single master was "one of the very first decisions, mostly just to simplify the overall design problem", and "we controlled both the horizontal and the vertical" was load-bearing for its relaxed consistency. Neither is in the paper as a condition.
- **Specific artifacts to explain.** "This revert in March: what broke?" "This config default changed after an incident: what was the incident?" "This dashboard shows a regression at 4K nodes: is that a premise breaking?" Pointing at an artifact gets a factual answer; asking an open question gets the team's current story.
- **The premise register with numbers**, asking which premises have already been violated in production.
- **The evaluated-vs-shipped list**, asking which features will be in the cited release.
- **Public numbers to reconcile**: every figure already in a blog, README, or talk, with the baseline it answers.
- **The adoption question**: what do users say they came for? If the answer differs from every candidate thesis, raise that explicitly; TensorFlow's OSDI paper argued throughput while adoption turned on usability, the property PyTorch built its paper around.

When the authors' account disagrees with the reading, treat the disagreement as a finding, not a correction to absorb silently. Either the reading missed something (go back to the source) or the authors' framing reflects what they hoped the system was (raise it, with evidence).

## 11. Assumptions and limits

The case files document published versions, not internal process; the reading order and signals here are inferred from how papers differ from their systems' code, docs, and retrospectives. The git-mining queries are a mechanical form of signals the cases show at the paper level (stable interfaces, abandoned designs, demoted abstractions); only the vLLM and FlashAttention cases were checked against repository snapshots directly. Churn, revert counts, and line counts are proxies for decisions and must be read, not tallied.
