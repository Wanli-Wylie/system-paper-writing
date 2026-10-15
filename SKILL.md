---
name: system-paper-writing
description: "Turn a complex software system (design docs, specs, code, experiment logs) into a systems research paper with independent academic value. Finds the contribution worth publishing, chooses the narrative spine, decides what to keep, demote, or cut, maps claims to evidence, and guides drafting and revision. Use whenever someone wants to write, outline, frame, restructure, or review a paper, tech report, white paper, or thesis chapter about a system - distributed systems, storage, databases, ML frameworks or compilers, runtimes, infrastructure, AI-driven discovery or agentic systems. Triggers include 'help me write the OSDI/SOSP/NSDI/MLSys paper for our system', 'what is the contribution of this codebase', 'turn these design docs into a paper', 'our draft reads like documentation', 'reviewers said there is no big idea', or 'frame our system for Nature/NeurIPS'. Prefer it over generic writing help when the hard part is deciding what the paper argues rather than polishing prose."
---

# System Paper Writing

A systems paper is an argument whose units are **claims**. A system's units are modules, documents, releases, and the order in which decisions were made. None of those is the paper's unit. Writing the paper is a series of re-representations (system → candidate claims → chosen frame → claim/evidence map → draft → diagnosed draft), and each one is a cut that must be checked against the whole it was cut from.

This skill bridges **system understanding** and **paper construction**. It was distilled from studying how classic papers were actually made from their systems (MapReduce, Spark/RDD, TensorFlow whitepaper→OSDI, PyTorch, TVM, XLA/JAX, Ray, Pathways, GFS, HDFS, Bigtable, Ceph, Dynamo, Raft, Paxos Made Live, Spanner, Borg/Omega, vLLM, FlashAttention, AlphaTensor, AlphaDev, FunSearch, AlphaEvolve), comparing each paper against its code, docs, earlier versions, retrospectives, and critiques.

There are two failure poles, and the work is steering between them:

- **Catalog**: the outline mirrors the repo tree; each component gets a section at uniform depth; defaults without reasons; no rejected alternative anywhere. (HDFS's paper, the TensorFlow whitepaper, most design docs.)
- **Vapor**: the concept is stated but no mechanism is concrete enough for a reader to re-implement the key idea; capabilities are claimed at concept level while evidence sits lower. (Pathways' motivating workloads; AlphaEvolve's one-sentence program database.)

The target: a reader who has never seen the codebase comes away knowing the new idea, the problem it addresses, the few mechanisms that make the capabilities possible, the evidence, and where it does and doesn't apply.

**Where to enter.** Starting from a system with no paper: work through stages 1–8 below. Holding a draft: read it against stage 8 first, then return to whichever upstream stage the symptoms trace to, usually 2 or 3. Reviewing someone else's paper: use stage 8 and `references/diagnostics.md`, separating what the draft alone shows from what only its authors can answer. Asked only to polish prose: check first that the frame holds (thesis without the system's name, an opponent, evidence for the headline claim); if it doesn't, say so before polishing.

## Core stance

These are the few judgments everything else follows from. Each is a claim about what made papers work or fail, not a rule to obey blindly.

1. **The contribution is chosen, not found.** The same Spark code was "a framework for working sets" (2010), "a fault-tolerant distributed memory abstraction" (2012), and "a unified execution model" (2016). Choose under three constraints that must hold together: it is *true of the system* (it actually explains the capabilities); it is *carried by evidence the authors can produce*; and it *contradicts something a competent reader believes*. HDFS fails the third; Ray's "stitching specialist systems is untenable" fails the second.

2. **Write against a belief.** Every strong paper has an opponent: a believed tradeoff (PyTorch: eager must be slow), a named incumbent design (TensorFlow vs the parameter server), inherited assumptions (GFS), an unmet requirement (Dynamo's 99.9th-percentile SLA), a wrong metric (FlashAttention: FLOPs vs memory traffic). If the only opponent you can state is "system X is slower than ours", you have a benchmark result, not a contribution.

3. **Depth follows argumentative load, not effort or code size.** vLLM's 273-line block manager carries the paper; its roughly 8.4K-line model executor gets one sentence. MapReduce spends about 1.5 pages on its model and 4.5 on the runtime and refinements because the runtime is where the claim is demonstrated. The rule is not "give the novel thing the most pages"; it is "make every other section depend on the novel thing."

4. **Put the thesis at the most stable layer the evidence supports.** Interfaces, formulations, metrics, invariants, and requirements outlive the implementations that first realized them (Ray's task/actor API survived its control plane being inverted; "IO-aware" outlived FlashAttention's Algorithm 1; RADOS outlived Ceph's MDS-first framing). Present the implementation as one realization of the idea, and do not omit it: every surviving concept was believed because a working mechanism demonstrated it.

5. **Every claim has evidence with a known status; every figure answers a claim.** The claims that later got attacked were exactly the ones that were asserted rather than measured (Ray's untested unification, Dynamo's undefined "consistency window", Spark's 20×, which the paper's own breakdown attributes to avoided HDFS reads, parsing, and deserialization rather than to lineage).

6. **Bounded generality is credible; unbounded generality invites attack.** RDD's "Applications not suitable for RDDs", vLLM's "does not help training", FunSearch's three applicability conditions all made the remaining claim harder to attack.

7. **Filter honestly.** Cutting *scope* is healthy (TensorFlow dropping TensorBoard; Spanner: "we will not go into detail about the query language"). Cutting a *qualification* that makes a claim read stronger is not (AlphaDev's "integrated into LLVM" without the patch's restrictions). Presenting the design in argument order is fine; asserting false causation is not (GFS says its design was "driven by" workload observations; the real driver of the single master was shipping fast, and that the team controlled every client was a load-bearing condition the paper never stated).

## The work

The stages below are the decisions this work has to make, roughly in the order each constrains the next. They are not a template; expect to loop, especially between 2–3 and 6. Frames in the classic cases were usually settled late and more than once. Spend most of your attention on stages 1–3, where the paper is decided; the checks, ledgers, and diagnostic catalogs in later stages and in the references are aids for finding defects, and running them exhaustively is no substitute for holding the whole system and the whole argument in view.

### 1. Read the system for decisions, not components

Docs and repos are organized by component and chronology. Paper-worthy ideas live in *decisions*. Look for:

- **Fan-out decisions**: one choice that many capabilities depend on. Count what would disappear if it were reversed. MapReduce: determinism → safe re-execution → fault tolerance, backup tasks, load balancing. Fan-out 1 is a feature; fan-out ≥3 is a thesis candidate.
- **Departures from the obvious design**, where a reader expects an alternative (GFS: no client cache, 64 MB chunks, single master).
- **Things removed** (Ceph sheds allocation tables): crisp, locatable claims. Check it is a real removal, not a renaming.
- **Stated invariants and contracts**, and the conditions under which they weaken.
- **Interfaces that stayed stable while implementations churned** (visible in git history: low-churn API files over rewritten internals).
- **Surprising measurements**, especially one where a believed proxy and the real outcome come apart (FlashAttention: more FLOPs, less memory traffic, faster).
- **A change of unit**: per iteration, per block, per memory access.
- **The price the system charges its users** (pure functions, static shapes, tolerating duplicate records). Often the honest form of the idea.
- **Dead ends that produced a rule** (Raft's abandoned ranking-based elections).

Signals of necessary-but-not-argument engineering: breadth (model zoos, frontends, backends), operability without a number, ecosystem tooling, parameter defaults, what the environment provides. This material often drove adoption; route it to a companion (docs, blog, later paper) rather than treating it as unimportant.

Guard against capture by the codebase: build the claim map before any outline; if renaming the repo's top-level directories would change your outline, the outline was captured. Docs answer "what is it" and "how to extend it", rarely "why this and not that"; the why has to be rebuilt from design reviews, reverted commits, old versions, and the people.

A reading order that puts discovery before verification, after first pinning the version the paper will describe: external claims (READMEs, launch posts) → guarantees and non-goals → alternatives and dead ends (design reviews, RFCs, reverts) → surprising measurements (dashboards, postmortems) → code, to confirm chosen mechanisms → the people, for the history no document records. Reading code to *discover* the outline is how capture happens.

→ `references/reading-a-system.md` for the full method, including what to mine from git history and what to ask the authors.

### 2. Find the opponent and the candidate theses

Write, in one sentence each: the belief a named, citable reader holds that this work shows false or incomplete; the **stated gap**; and the **real gap**. In nearly every classic the stated gap sits one abstraction level above the real one. That elevation is how an engineering fact becomes a claim others can use, and it is legitimate only if the evidence reaches the higher level. Two checks on the opponent: can you cite someone who actually holds the belief, and would they recognize your statement of it (RDD's "DSM" category was defined broadly enough to leave the practical alternative, MapReduce plus caching, out of the comparison)? And beware the conjunction gap ("no existing system has A, B, and C"), the weakest frame, because it silently claims that composing existing systems fails; the experiment it owes is that failing composition (Ray asserted it and never ran it).

Generate thesis candidates by several independent routes and look for agreement: trace the headline capability back to the decision it depends on; find the decision an outsider would call a mistake that the team defends; find the distinction engineers use to explain behavior (narrow vs wide dependencies; consistent vs defined); look at what got promoted or demoted across versions; check what the evidence can isolate; explain it aloud to someone with no context and see what they repeat back.

Test each candidate:

| Test | Question |
|---|---|
| Name removal | Can it be stated without the system's name? |
| Reimplementation | Would someone rebuilding from the paper get the benefit? |
| Refutability | Is there a number or condition that could prove it wrong? |
| Ancestry | Is it new against *named* ancestors, with borrowed parts disclaimed? |
| Explanatory reach | Does it explain several mechanisms, and generate the section structure? |
| Isolation | Can an experiment separate its effect from incidental engineering? |
| Layer | Is it stated at the WHAT (interface/specification) or HOW (implementation) layer? |

Elevated concepts come in recognizable kinds: a restriction that buys properties (MapReduce, RDD), an interface exposing something hidden (TrueTime), an imported analogy (PagedAttention as virtual memory), a swapped metric (IO-awareness), a problem formulation (TensorGame), a priced non-performance property (Raft's understandability, PyTorch's "10% slower is acceptable, 100% is not"), a premise register (GFS), a synthesis with a requirements vocabulary (Dynamo), organization by reader concern with a unifying metric (Borg), a taxonomy of a gap (Paxos Made Live). Two forms cut across these kinds: subsumption ("X expresses Y"), only as strong as its definition of "express", and a delta against a predecessor the authors own (TensorFlow OSDI against DistBelief's parameter server). Strong papers often combine two kinds: typically one earns attention and the other earns belief (TrueTime's exposed interface plus a four-line proof). Use the kinds to borrow each one's *needs* and *failure modes*, not as boxes to fill. A new system may fit none; then derive its needs directly (what must the reader believe, what could refute it, what does it cost) rather than forcing a kind.

The tests above judge candidates; they do not produce them. When no candidate survives, produce more rather than lowering the bar. Generative moves that work on raw material: write the one-paragraph explanation you would give a strong engineer from an adjacent field, then look at which sentence they would find surprising; take the system's most surprising measurement and ask what belief it contradicts; take the price the system charges its users and ask what that restriction buys; take the decision the team defends hardest and state the world in which it would be wrong; describe the system in a neighboring field's vocabulary (operating systems, databases, compilers, numerical methods) and see whether the mapping is near-isomorphic. Write several candidate thesis sentences quickly and badly before testing any; the cases show one codebase supporting several honest theses, so the first one written is rarely the best.

→ `references/finding-the-contribution.md` for the opponent types, the concept kinds with conditions and failure modes, naming, generality, and splitting one system into several papers.

### 3. Choose the frame, the genre, and the venue fit

Not every system has a single idea, and forcing one produces a slogan. Decide honestly which genre the system supports: an **idea paper** (one concept, mechanism, isolating evidence); a **synthesis** (known techniques combined, with a contribution-type sentence telling reviewers where to look, as in Dynamo); a **concern-organized** paper about a mature revealed system (Borg, held together by a cross-cutting metric and paired benefit/cost sections); an **experience** paper (organized by gap category, failures quantified, abstractions extracted, as in Paxos Made Live); a **system-as-instrument** paper where the headline is a discovery and the system is demoted to Methods (AlphaTensor, FunSearch); a **measured-system** paper whose contribution is operational data and where inherited assumptions broke (the thesis HDFS's paper had in its own data but never claimed); or **several papers**, when candidate ideas need different evidence environments (Ceph split placement, replication, and the file system). The list is not exhaustive. The diagnostic is not "is there one idea?" but "is there any organizing principle other than the code's module boundaries?" `references/reading-a-system.md` §9 maps what the reading found to the genre it supports.

Venues reward different currencies: a transferable abstraction with broad evaluation (SOSP/OSDI), deployment lessons without novelty (operational tracks), principle plus theorem plus quality result (NeurIPS), scientific significance verifiable without the system (Nature). Read a few recently accepted papers at the target venue and note what counts as a contribution, where related work sits, and which evidence type carries the claim.

Generate two or three competing frames. For each, write at least the thesis sentence, the believer, and the decisive experiment, plus the venue and layer where frames differ on them. Then do a **trial by abstract**: draft an abstract for each using only claims that are measured or argued. A frame whose key sentences need asserted claims is out, or needs a new experiment. Freeze a frame once it passes and its decisive experiment is in hand or scheduled; reopen only for a named reason (new evidence, venue change, reviewer pressure, the system changing under the paper).

→ `references/finding-the-contribution.md` (competing frames, trial by abstract, reframing triggers, splitting into several papers) and `references/evidence-and-venue.md` (venue currencies, contribution types, maturity, openness).

### 4. Build the argument spine and control altitude

Every successful paper places each paragraph on a causal path: **spine** (the top object) → a small **middle layer** → **mechanisms** → **capabilities** → **evidence**. Section order, figures, running examples, and tables are devices for making that path visible and short. Papers that read as documentation are the ones with no such path.

- **The middle layer** is what keeps a paper from being vague or a list: two to five premises, requirements, priced principles, or named invariants, or one metric. A principle earns its place only if it excludes something a reasonable designer would do *and* a later paragraph cites it as the reason for a choice. Generic principles ("simple, scalable, flexible") give no leverage.
- **Describe each layer only through its neighbors**: the layer above says why it exists, the layer below says what it is made of. Lower layers (the file system you build on, the closed RPC substrate) appear by the contract you use, with the one dependency that can take you down quantified (Bigtable on Chubby: 0.0047% unavailability).
- **A mechanism earns full treatment** if it is a link in the chain to a claimed capability, has an alternative the reader expects, has a number attached, or separates you from a named rival. Put the *why* in the sentence next to the *what*, not in a separate rationale section. Useful patterns: "choice → benefits → cost → why the cost hasn't bitten" (GFS); "textbook version → what breaks here → our variant" (Dynamo).
- **Demote, don't silently drop.** A named "Refinements" bucket keeps the core readable. An Implementation section's role depends on the claim type: when the claim is "this abstraction enables these capabilities" it holds necessary facts (size, language, substrate, lineage) and stays short; when the claim is "this can be implemented at acceptable cost" (PyTorch) or the runtime is where the chain is shown (MapReduce), it *is* the argument and needs matching experiments. Either way it must not introduce a new organizing idea. A one-sentence dead end shows design maturity.
- **Ordering**: concept first when the reader can see what the idea removes without knowing internal structures (Bigtable's data model, vLLM's paging analogy); substrate first, trimmed to exactly the objects the idea acts on, when the idea's value is simplifying structures the reader hasn't met (Spanner). Outside-in (user view before internals) fixes the capability vocabulary early.
- **Running example**: choose one that forces every on-chain mechanism to fire at least once and exposes at least one cost (vLLM's "Four score and seven years ago" prompt tracked block by block through allocation, fork, and copy-on-write; Dynamo's shopping cart where deleted items resurface). Happy-path-only examples are decoration. When there is none, invariants with a short proof, a canonical small instance (Strassen's 2×2 for AlphaTensor), a real configuration, or distributions over many real instances carry concreteness instead.
- **Figures argue**: write the one sentence each figure argues before drawing it. Figures do six jobs: roadmap (boxes labeled with section numbers), separation (control vs data flow), thesis in one picture, a bridge where proxy and outcome come apart, the user-facing contract, and robustness built into the plot type (CDFs over many clusters). More than one "here are the components" figure is inventory.
- **Indexes must agree**: abstract, contributions list, overview figure, and evaluation questions are different cuts of the same argument for different readers. Build the contributions × sections × experiments matrix; empty cells are defects, and a contribution that points to every section is the system, not an idea.

→ `references/argument-architecture.md` for spine types and what each generates, chain shapes, ordering tests, tables, vocabulary, paragraph moves, and the first page.

### 5. Filter and tier the material

For every true, substantial thing about the system, decide where it goes: on the causal chain (full treatment) → refinements bucket → implementation section → one-sentence demotion placed where the reader would wonder → appendix/Methods/SI → companion paper → docs, blog, or README → nowhere. Rules the classics actually used: keep links on the causal chain to the thesis; drop what would need a second organizing idea; keep what differs from a named alternative or has a number; hold lower layers fixed as environment, unless a lower-layer property is load-bearing for a claim, in which case it is a premise and must be stated (GFS's relaxed consistency worked because the team controlled every client); keep what a reader can verify without the system; keep a few small failures that motivated choices. Papers tend to drop the failures that *challenge* a premise; those belong next to the premise they break.

Two checks that matter most:

- **Does the cut change the truth of any claim?** If so, it is a cut of qualification. Put it back, inside the sentence that makes the claim.
- **What would a skeptical practitioner use instead?** List three. Any that is absent from both the evaluation and related work is a future attack surface (MapReduce never discussed relational databases; the critique came three years later). The strongest operational alternative must appear, even if only to be scoped out.

Decide where demoted material lives *before* cutting. What a flagship paper omits becomes the system's public identity until another document corrects it (Spanner needed a 2017 paper to say "today's Spanner is very different from what was described there").

→ `references/filtering-and-honesty.md` for the rules, destinations, honest vs revisionist narrative, and disclosure of what was withheld.

### 6. Design the evidence as claim tests

The paper's real claim is whatever its evidence tests. Classic papers repeatedly measured the whole system and attributed the result to the idea. The gaps took three shapes: *attribution* (the number is real, the cause is other engineering: Spark's 20× came from avoiding HDFS reads, text parsing, and deserialization, not from lineage), *scope* (the claim covers a regime the evaluation doesn't reach: Spanner's "global" microbenchmarks ran with zones <1 ms apart), and *property* (the claimed adjective has no instrument: TensorFlow's "flexibility"). Type each claim (existence, mechanism attribution, magnitude in a regime, parity, generality, non-performance property, guarantee, use), because the type decides what can test it. Map every claim to the experiment that tests it, and every figure back to a claim. Then:

- **Apply the null-idea test** to each experiment, especially the ones behind abstract numbers: would the result change if the central idea were false but all other engineering stayed the same? If not, the experiment tests the system, not the idea. Decompose it, move the number off the idea, or run the experiment that does change. Production material makes this the default case: internal docs report results per rollout ("p99 dropped 3× after the launch"), and a rollout usually bundles several changes.
- **Make the central idea, not incidental engineering, what the evidence isolates.** In rough order of directness: toggle the mechanism inside the same system (MapReduce: backup tasks off → 44% slower); decompose your own headline before a reviewer does (RDD's breakdown of its speedup); show proxy and outcome coming apart (FlashAttention: more FLOPs, less memory traffic, faster); measure the mechanism alone, then the whole (PyTorch's per-mechanism traces); compare against the design a competent engineer would build in a week *without* the idea, the comparison the classics most often skipped and where critics struck.
- **For a tradeoff opponent, measure the side that was supposed to lose** (PyTorch: within 17% of the fastest framework; Spanner: commit-wait cost; Pathways: single-controller costs nothing at parity).
- **For non-performance claims** (usability, understandability), build a proxy and disclose its bias, narrow the claim to what is measured, and pair it with conventional evidence so the paper stands even if the proxy is discounted (Raft).
- **Choose evidence kinds that fit the claim**: microbenchmarks against hardware limits when no fair baseline exists; end-to-end against the strongest real baseline; production telemetry and fleet distributions for operational claims; trace replay and simulation for policy; proofs and invariants for guarantees; user studies (with bias disclosed) for non-performance properties; verifiable artifacts for discoveries.
- **Baselines**: set the baseline bar per claim, not per paper: full rigor for magnitude and attribution claims, less for existence, disclosure, and guarantee claims (GFS and Borg had no competitor baseline and didn't need one; Spark's aging Hadoop baseline and AlphaTensor's missing equal-budget search did cost them). State how any self-built baseline was configured, in the sentence that uses it; report where competitors win.
- **Costs and scope**: pair each benefit with its measured cost; state where the idea doesn't apply. Papers tend to be candid about costs that don't threaten the headline and quiet about the gap that does, so name the limitation that would most reduce belief in the abstract's first claim, and state it next to that claim. Put small qualifications inside the sentence they qualify, *and* aggregate limitations somewhere a skimming reader will find them.
- **Match evidence to maturity and openness.** The younger the system, the more evidence should rest on the WHAT (invariants, mechanism ablations, parity) rather than implementation numbers the next version will change. A closed system makes the paper the artifact: parity on public baselines, published configurations, worst cases, and the contract required of any closed substrate (Pathways). An open system must keep paper and release in agreement. Releasing a checker but not the generator backs claims about *outputs*, not claims about the *method* (efficiency, necessity, which components matter).
- **For each claim without evidence, choose one of three moves**: run the isolating experiment; rescope the claim by writing its condition into the sentence; or move it to the discussion as a stated bet. Rescoping is usually cheaper than an experiment and always better than an assertion. Spend experiments on the decisive uncertainty (attribution, the simplest alternative) before breadth.
- **Organize the evaluation by capability or claim, not by dataset or platform**; open with the questions it answers.
- **Reconcile across artifacts**: every public number (blog, README, paper) should state which baseline it answers; every evaluated feature should exist in the cited release.

→ `references/evidence-and-venue.md`.

### 7. Draft

Draft the body before the first page; write the first page last, against these jobs: name the belief being displaced; state the thesis early (GFS by sentences 3–4, MapReduce within about 15 lines; Ray's paragraph 7 is the slow end); disclose reality status (implemented? deployed? at what scale?); define the gap by a missing property; give a map when the order is unusual. The abstract's grammar works best when the **concept is the subject** and the features are its consequences ("TrueTime … is critical to supporting external consistency"), not "we describe the design, implementation, and evaluation of X". Use two names when helpful: one for the idea (IO-aware), one for the artifact (FlashAttention); the idea's name should attach to the component that actually produces the benefit.

Place related work early when the reader's existing systems define the gap, late when the difference lies in premises the reader must learn first. Credit the nearest respected predecessor and give the delta in one sentence (TVM: Halide's compute/schedule separation, "also separating target hardware intrinsics"). Lessons sections earn their place only when each lesson is tied to a reversed decision or a measurement; write each as claim → evidence → consequence (Borg puts the bad lessons first, which reads as candor).

### 8. Diagnose and revise

Revision in the classics meant section-level chops, not sentence edits. Diagnose by symptom, but trace each symptom upstream before fixing it locally; patching a symptom of a wrong frame is filling a wrong form well. Fix top-down: frame, then structure, then evidence, then wording; rank defects by how much they threaten belief in the abstract's first claim. When reviewing someone else's draft, report what only the authors can answer (the real decision history, whether an unstated reason was load-bearing) as questions, not as defects. Key probes:

- Section headings are module names → outline captured by the codebase.
- A mechanism appears with no alternative and no cost → description, not argument.
- A figure maps to no claim, or a claim has no figure.
- The thesis can't be stated without the system's name.
- The introduction reads like a README (lineage, feature list, "X is a framework for…").
- Related work is a list rather than one-clause differences.
- Adjectives (first, general, unified, global) with no evidence pointing at them.
- No mechanism is concrete enough to re-implement the core idea.
- The named concept is a renaming, or names a component that isn't where the benefit comes from.
- The paper's best data or candor sits in its last two sections.

Two meta-tests: the **three-lists test** (contributions, sections, figures side by side; orphans in any direction are defects) and the **rewrite test** (which sentences would survive the system's next major rewrite? If the thesis isn't among them, reconsider its layer).

Simulate the pressures that historically reshaped papers before a reviewer applies them: an expert from each adjacent field asking what they'd use instead; a competitor beating your headline record next month with a cheaper method (what remains?); a critic of *significance* as well as of method (AlphaDev answered the method critic with a baseline and was still attacked as "one saved instruction"); a non-specialist reading "algorithm", "first", or "global" at full strength; the system outgrowing the paper. If a simulation produces "we would add X if asked", add X now.

→ `references/diagnostics.md` for the full symptom → cause → fix catalog and reviewer-simulation questions. When a draft reads like a catalog and the fix isn't obvious, `references/worked-transformations.md` helps identify which transformation move is missing.

## Working artifacts

Keep an artifact only while it changes decisions; none is a deliverable, and a perfectly filled ledger for the wrong thesis is still the wrong paper (TensorFlow's OSDI paper was locally excellent and globally mispositioned against what drove adoption).

- **Thesis sentence without the system's name**: two to four candidates early.
- **Belief to overturn**: who holds it, with a citation. If you can't cite a believer, the opponent may be a straw man.
- **Claims ledger**: claim → claim type → mechanism → specific evidence (a figure, table, or proof, not "§6") → status (measured / argued / asserted / future). Mark the one claim the paper cannot lose. Tag generality claims as built-and-measured / built / argued by correspondence / conjecture; only the first two belong in the abstract. Keep it to the paper's 3–6 main claims plus what readers will *infer* from the title and abstract ("global", "first", "unified").
- **Filter ledger**: what's cut, why, where it goes, whether its absence changes any claim's truth, whether it opens an attack surface, and whether it drove adoption (and so needs a home elsewhere). Include opponents you chose not to engage.
- **Alternatives register**: for each key decision, the alternative a reader would expect, why it was rejected, and whether that reason is *documented history* or *reconstructed analysis*. Do not present reconstructed alternatives as history; "an alternative would be Y; it would cost…" is legitimate analysis in its own voice.
- **Premise register**: workload and environment assumptions with numbers. GFS's conclusions aged badly but its method aged well, because readers could see exactly which premise broke.
- **Figure outline**: one claim sentence per planned figure.

## What only the authors can decide

Ask early, and bring two or three candidate framings with their evidence needs rather than open questions; people answer "which of these would you defend?" better than "what's your contribution?".

1. Which claim do they want to be held to for years?
2. Target venue, track, and deadline.
3. What evidence exists or can be produced in time (telemetry, traces, ablation-capable builds, failure injection, user studies, access to competitors)? Ask specifically about natural experiments: staged rollouts or canaries where only one change shipped, config history, tickets and migration notes as evidence of use. These are often the cheapest way to close an attribution gap.
4. What is confidential or unnameable (business numbers, internal system names, closed substrates)?
5. Which version the paper describes; what is implemented, deployed, and evaluated; which components are about to be rewritten.
6. The real decision history, and whether any reason (time-to-market, control of all clients, team size) was load-bearing; any known failures that challenge the premises, not just ones that motivated choices.
7. Lineage, credit, prior public claims (blog numbers must agree with the paper or the difference be explained).
8. Which alternative they consider the real competitor, and which omissions their community would punish.
9. Artifact release plans. These decide whether evidence can be "checkable outputs" or must be "behavior we measured", and whether evaluated features must ship.

## Caveats on this skill's own representation

The classics are survivors: influential papers whose success also owed something to authority, scale, access, and timing a new group may not have. Moves that worked for Google, Amazon, or DeepMind (adoption tables in place of baselines, telemetry-only evaluation, unevidenced lessons) are weaker for an unknown group; weight evidence-producing moves (ablations, honest baselines, decomposed speedups, scope statements) above authority-dependent ones. Passing every test and probe here is evidence about parts of the draft, not proof the chosen frame is right. Before drafting inside a frame, ask whether it is the right one.

## Reference files

Read the one relevant to the stage you're in; none needs to be read in full up front.

| File | Read when |
|---|---|
| `references/reading-a-system.md` | Starting from docs/code/logs; mining decisions, history, and measurements; matching the system to a genre; preparing questions for authors |
| `references/finding-the-contribution.md` | Choosing the opponent; stated vs real gap; thesis candidates and tests; concept kinds; WHAT/HOW layer cutting; naming; generality and scope statements; competing frames; splitting into several papers |
| `references/argument-architecture.md` | Outlining; spine and middle layer; ordering; running examples; figures and tables; the first page |
| `references/filtering-and-honesty.md` | Deciding what to keep, demote, or cut; destinations across documents; lower layers by contract; tidy vs revisionist narrative; disclosure and lineage |
| `references/evidence-and-venue.md` | Designing the evaluation; baselines; limitations and lessons; venue, contribution type, maturity, openness (closed systems, checker-only releases) |
| `references/diagnostics.md` | Reviewing or revising a draft; reviewer simulation; anticipating attacks |
| `references/worked-transformations.md` | Seeing how real papers were made from their systems, organized by transformation move (raw material → paper → decision logic → what aged), each with a cue for recognizing it in your own system; includes labeled counterfactual rewrites |
| `references/origins.md` | Maintaining or revising this Skill: the study it came from, the case set, and what it rests on |
