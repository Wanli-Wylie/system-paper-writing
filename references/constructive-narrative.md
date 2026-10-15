# Constructive narrative

Read this when formulating the research problem (SKILL.md stage 2), drafting (stage 8), or diagnosing a draft that reads as design-led, deduction-driven, defensive, or conceptually packaged (stage 9). It covers how the reader's account is built from the field, the problem, and the ideas, and how to keep the honesty the other references require without writing it as defense. Each section gives the judgment and its reason, the symptoms you can find in your own draft, the constructive alternative, and the conditions under which it applies.

## Contents

1. Two representations
2. Formulating the problem from the field
3. Problem, means, domain, object
4. From ideas to construction
5. The qualification budget: constructive voice
6. Coherence without closure
7. Concreteness without artificial examples
8. Names and titles
9. Re-conceiving vs revising
10. Open evaluations and briefs
11. Assumptions and limits

---

## 1. Two representations

A systems paper exists in two representations, and most failed drafts come from writing one as the other.

- **The reader's narrative**: motivation → the field's progress → the research problem → the ideas → the system as the construction that develops them → evidence. This is the path the reader travels; every paragraph sits somewhere on it.
- **The writer's analysis**: system → decisions → candidate claims → frame → claim/evidence map. This is how the writer finds out what is true, what is new, and what is evidenced. The tests, ledgers, registers, and simulations in this skill live here.

Each part of the analysis that leaks into the prose produces a recognizable failure:

| What leaks | What the draft becomes | Failure pole |
|---|---|---|
| The analysis order: start from the system | Design-led. The outline follows the design package; the problem is inferred from what was built | Catalog |
| The middle layer read as axioms ("what any solution must satisfy") | Deduction-driven. Defined concepts → derived requirements → a design shown to satisfy each | Vapor, manifesto form |
| The checks (ledgers, attack surfaces, reviewer simulation, scope tests) | Defensive. Disclaimers, repeated guarantees, rebuttals, requirement-to-section mappings | Defense |

**The back-of-house rule.** Every working artifact changes the work: the size of a claim, an experiment, a baseline, a scope, a cut. None appears in the paper as such. If a check produced a paragraph, look for the change to the work it should have produced instead (§5.1).

Two clarifications keep the rule from being misread. The narrative order is not the writing order: drafting the body before the first page is still good practice (SKILL.md stage 8). And it is not a claim about history: presenting the problem before the system is argument order, which is legitimate, while writing that the design "was driven by" the problem is a claim about causation and must be true (`filtering-and-honesty.md` §8).

## 2. Formulating the problem from the field

The system tells you what was built. It does not tell you what problem the paper addresses. A problem formulated from the system's own materials (its design documents, its internal vocabulary, its list of features) is the system's account of itself, and a reader has no way to check it. A problem formulated from the field's development can be checked against literature the reader already knows. That is what makes it credible.

### 2.1 Reading the field as a progression

For the two or three lines of work the problem sits in, record for each major approach: the named systems, the object they improve, the mechanism, the result, and what their success made possible or exposed. Success usually exposes something: a new bottleneck once the old one is gone, a workload the approach made feasible that now dominates, an assumption it relied on that a new environment breaks.

Use normal scholarly literature, cited. Internal design documents, earlier drafts of the paper, and the system's own documentation are reading material for the writer; they are not citations for the state of the field.

Symptoms in a draft:
- Trend sentences: "increasingly", "is beginning to", "a growing body of work", "X is doing Y".
- A list of fields with one sentence each, or a citation cluster ([3-11]) attached to a generic sentence.
- Fields described in general terms with few or no citations.
- Prior-work paragraphs that each end by attaching the work to your system ("These results inform our design", "Unlike X, our system...").

The alternative: one or two sentences per line of work, naming the systems and saying what each improves, how, and with what result, grouped by the categories of §2.3. A trend sentence may open a paragraph if the concrete works that follow carry it.

### 2.2 From progression to opportunity

The opportunity is the next step the field's progress makes reachable: a bottleneck exposed by earlier success, two lines improved separately whose interaction has become the cost, a requirement now demanded and unmet (Dynamo's 99.9th-percentile SLA), or assumptions that a new environment revises (GFS). It follows from the progression; it is not a void shaped like your architecture.

Tests for a stated opportunity:
- Could a reader who knows the literature verify it from the citations given?
- Does it survive removing the system's name, its internal terms, and your technique's name?
- Did the field hold the view the work changes, or did your design need it? A state reconstructed to justify an existing architecture usually describes the architecture's premise (`finding-the-contribution.md` §1.3).
- Is it phrased as research questions, or only as "applying our capabilities to Y"? State the concrete systems questions in the introduction, not at the end of related work. For the storage example of §2.3: which layer should reclaim space, what must the interface between layers expose, and what does coordination cost when the device is shared?

### 2.3 Constructive categories

A taxonomy of prior work earns its place by doing four things at once: it places concrete, named systems; it says which problem each solves; it locates the opportunity; and it can then structure the system sections. The most productive axis splits the problem into **objects of improvement that are improved separately but interact**. Other real axes are the information a line of work uses and the problem it solves. A category defined by novelty or by the provenance of outputs ("generative", "AI-driven", "next-generation") is not a research category, because any new system fits it and it says nothing about what was improved.

An illustration, invented for this file. Suppose the work concerns write-heavy storage on flash. A vague taxonomy reads: traditional storage engines, flash-aware engines, learned storage. Each label fits any new system. A constructive one splits by the object each line of work improves:

1. **The store's on-disk organization.** Log-structured merge designs turn random writes into sequential ones and pay with background compaction and read cost; in-place tree designs make the opposite trade.
2. **The device's translation layer**, which remaps writes and reclaims space through its own garbage collection.
3. **The file system between them**, which allocates and journals independently.

Each line has its own literature, benchmarks, and successes. They interact because each layer rewrites data the others also rewrite: amplification at one level multiplies at the next, and a compaction schedule tuned for the store can collide with the device's garbage collection. The opportunity is located in that coupling, not in any line's deficiency: each line's success is what makes the interaction the remaining cost. The same split then structures the paper: one section per object as the construction changes it, one on the interface between them, and an evaluation that measures each object and the coupled total. In a real paper each line names its systems and results, with citations.

Symptoms of vague or non-constructive categories:
- Categories named for novelty or provenance, or labels like "learning-based approaches" used as categories.
- Categories that classify only your own artifacts and place no prior work.
- A taxonomy that organizes related work and then vanishes from the rest of the paper.
- Every category defined by what it lacks; a feature-checkmark table in which only your column is full.
- A category drawn so the practical alternative falls outside it (RDD's "DSM" was defined broadly enough to leave MapReduce plus caching out of the comparison).

Conditions: a taxonomy matters most when the paper spans several research lines. A single-mechanism paper may not need one; do not invent an axis to have a taxonomy.

### 2.4 The manufactured gap

A manufactured gap defines prior work by a missing property that happens to be the new system's signature. Its shapes:
- The conjunction: "each existing system solves part; what is missing is one that does A, B, and C", with legs chosen so that only the proposal has all of them. It silently claims that composing existing systems fails, and owes that experiment (Ray asserted "stitching specialist systems is untenable" and never ran it).
- "Existing approaches lack X", "none of these...", where X is what the system was built around.
- Prior systems recast as special cases of the new system's vocabulary.
- A believer who appears only after the design has been described, or "it is commonly believed" with no citable holder.

Why it fails: the reader cannot check it, it reads as positioning rather than research, and it invites the reviewer to supply the missing comparison. The alternative is §2.1-2.2: say what each line achieved and what that made possible, and show novelty through the construction. A real, cited, measured limitation is not a manufactured gap: vLLM measures how existing serving systems waste KV-cache memory before proposing anything. Saying what is new is fine; defining prior work by what it lacks is not.

## 3. Problem, means, domain, object

Four slots need separate fillers, and the first paragraphs fill the first three: the **object** whose behavior is improved; the **problem** about it; the **domain** that makes it matter. The **means** (the technique) appears in the construction.

### 3.1 Problem vs means

Search, learning, recursion, a decision procedure, an IR, a generative model are means. Promoted to the problem, a technique makes the problem either unboundedly general or a restatement of the solution.

- **Means-removal test.** Would the problem statement still hold if a different technique solved it? If it names your technique, it describes your solution.
- **Over-inclusion test.** Does the statement also describe phenomena plainly outside the work? "How systems can make better runtime decisions about resources" covers cache eviction, query optimization, load balancing, and congestion control. A technique that occurs at every scale of computing defines no problem by itself. Compare "keeping tail latency of a multi-tenant cache within its target as working sets shift", where learning, if used, is one way to do it.

Symptoms: technique words in the research question or title (learn, search, decide, recursive, generate, evolve); the means gets its own top-level section; the problem statement, read aloud, is the system's design in one sentence.

The alternative: state what is improved and for whom; bring in the technique when explaining the construction, in the form "X provides a way to organize Y". Condition: when the contribution is the technique itself (a new search method, a new learning algorithm), the technique is the object, and the test applies one level down: what about the technique is improved?

### 3.2 Domain vs object

The domain explains why the problem matters (genomics pipelines, a trading platform, large-model serving). The subject is the computational object the system improves: a class of computations, a resource, a workload shape. A scheduler motivated by genomics pipelines whose mechanism acts on any workflow of many short, data-dependent tasks has that class of workflows as its object; genomics says why it matters now.

The failure goes both ways.
- **The domain as subject** scopes the paper to one application while its mechanism is general. Readers from other domains do not see that it applies, and any transfer has to be asserted rather than built.
- **The object without the domain** is the overcorrection. The motivation slot goes empty, the reader does not learn why the problem matters now, and mechanisms that the domain justified lose their reason to be in the paper.

Choose the object at the level whose natural scope excludes irrelevant cases. Scope is then stated by naming the object, and few disclaimers are needed (§5.3). Symptoms: the domain in the title's main slot when the mechanism is general; the domain mentioned once, in a list; a system section whose only motivation lived in a domain the introduction no longer discusses.

### 3.3 What the classic openings do

The classics fill object, problem, and motivation before the means, and introduce the means as the idea that answers them.

- **MapReduce** opens on operating reality: hundreds of special-purpose computations, each re-solving distribution and failure. The restricted programming model arrives within about 15 lines as the idea that removes that repetition; the runtime is the construction, and gets most of the pages because it is where the claim is shown.
- **vLLM** measures existing systems' waste before vLLM appears (Figs. 1-2: 20-38% of KV-cache memory holds useful state) and fixes a waste taxonomy (reserved, internal, external) so the evaluation's quantity exists before the design. Paging, the means, is borrowed wholesale from virtual memory and enters as the idea that addresses the measured waste.
- **FlashAttention** takes the attention computation as its object, with long-sequence models as motivation, and recasts the problem as memory traffic rather than FLOPs: "A missing principle is making attention algorithms IO-aware" (abstract, sentence 3). Algorithm 0 restates standard attention in HBM reads and writes, so the incumbent reads as an I/O problem before the new algorithm appears. What is "missing" there is a quantity the field had not optimized, not a feature only the authors' system has.

## 4. From ideas to construction

The system enters after the problem and the ideas, as the concrete development of the ideas. A design document answers "what are the parts"; a paper answers "what problem, what idea, and why does this construction realize it".

**Ideas and mechanisms are two layers.** An idea says what is improved, which quantity matters, or why parts couple, in the field's vocabulary. A mechanism is the data structure, protocol, or algorithm that makes the idea work. State the ideas in the introduction; introduce each mechanism with the idea it serves. Symptoms of collapsed layers: contribution bullets that cite internal mechanism names; each bullet "a capability, paired with the mechanism that provides it" (the design catalog in bullet form); an "idea" that is really a data structure; mechanism names in the abstract's opening; an abstract whose second half walks through components one sentence each.

**The order follows the ideas.** Each system section should read as the construction that makes idea k work, or, with a taxonomy (§2.3), as the construction for object k and their interface. If renaming the design package's chapters would change your outline, the design captured it, even when the sections carry concept titles.

**The system's conceptual framework is the strongest capture**, because it looks like an idea. Systems ship with named layers, a mnemonic set, an analogy to cognition or biology, a coined unit of work; requesters bring favored concepts in the brief. Treat both as hypotheses to investigate. Test: remove the vocabulary; would the problem statement or the section structure change? If so, it is driving the narrative. Translate the concept into the field's terms and see what it explains; keep its substance where it organizes a real mechanism; drop the label where the field has a term; do not map every component onto it. Symptoms: the requester's vocabulary in the title, abstract, or headings; a paragraph mapping each component onto a framework; the prompt's phrasing turned into the thesis; concepts from the brief that appear but do no argumentative work. Agents are especially prone to this, since they anchor on the vocabulary of their instructions (`reading-a-system.md` §6).

**Inherited sections.** After a reframing, check each system section against the new introduction: what in the problem makes this construction necessary? A section kept from an earlier framing whose motivation was dropped reads as design residue. Either restore the motivation in the problem, if it matters, or demote the section.

**Proportion.** Engineering detail stays in proportion to argumentative load. Configuration constants, wire formats, and code-size tables in an idea paper are catalog; route them to an implementation section, an appendix, or the documentation.

**Concreteness is still owed.** A reader should be able to re-implement the key idea: show the real interface (what a user writes, what the system guarantees), a figure of the real construction, and one real path through it (§7).

Conditions: experience and operations papers may be organized by system concern (Borg organizes by reader questions with one unifying metric). Even there, the problem comes first.

## 5. The qualification budget: constructive voice

A paper develops ideas; it does not defend an assembled framework. The cycle claim → search for support → qualify → define → justify → restate produces length but no new content, and it reads as uncertainty about the claims it protects.

### 5.1 Defend in the work

Each check in this skill has an output in the work. The prose states the result plainly.

| Check | Its output in the work | What the reader sees |
|---|---|---|
| Straw-believer check; "did the field hold this?" | The problem re-derived from the literature, or a citation for the state the work changes | The field's progress, cited |
| Attack surface: what a practitioner would use instead | A measured baseline, or a precise account of which different problem it solves | The alternative where it illuminates a design choice, by what it does well |
| Null-idea test | A decomposition or toggle experiment; a number moved off the idea | The experiment and its result |
| Reviewer and critic simulation | An experiment, a smaller claim, or nothing | The claim at its right size |
| The decisive limitation | The claim shrunk, or the limitation identified | One plain sentence where the claim is made (§5.3) |
| Ledgers, matrices, three-lists test | Claim sizes, section structure, destinations for cut material | Nothing |

### 5.2 Defensive patterns and constructive rewrites

The sentences below are invented for illustration. The left column gives shapes you can search your own draft for.

| Pattern (detectable shape) | Defensive | Constructive |
|---|---|---|
| Protective negation: "never", "cannot", "only", "without trusting", where the negation guards the design rather than informing the reader | "The cache never serves a stale entry: entries are only read after a lease check, and without trusting client clocks." | "Each read checks the entry's lease against the server clock, so clients see no stale entries even when their clocks drift." |
| Corrective contrast against a reading nobody proposed: "This is not A; it is B" | "The layer is not a new consensus protocol, nor a replacement for replication; it is a coordination service." | "The layer runs on an existing consensus protocol and adds leases that let applications coordinate without a separate lock service." |
| "Need not" | "Adopting the cache need not require changes to applications." | "Applications use the cache through their existing get and put calls." |
| The same guarantee in abstract, introduction, overview, mechanism section, and conclusion | One invariant restated in five places | Once, in the sentence that describes its mechanism; the abstract states the capability it enables |
| A series of novelty disclaimers | "We do not claim novelty for the log format, the index, or the recovery protocol, each of which appears in prior work." | "The store builds on A's log format and B's recovery protocol, and adds C." (§5.3, lineage) |
| Prior work attached to your system, sentence by sentence | "These systems establish an important consideration for our design." "Unlike X, which lacks Y, ..." | Say what each line achieved and what it made possible; connect once, where the construction uses it ("§4 adopts its batching") |
| Rebuttal paragraph: "One might object... However..." | "A natural concern is that the central log becomes a bottleneck. However, ..." | Run the measurement, or resize the claim; if a reader genuinely expects another design here, give the choice and its cost where the mechanism is introduced |
| Signposting about how to read the paper | "We use A and B as examples because they represent..." "Readers should not infer that..." | Develop the account with A and B directly; drop the instruction to the reader |
| An opening inventory of what the paper is not | "This paper does not address security, multi-tenancy, or heterogeneous devices." | Scope once, as the conditions under which the idea works: "The design targets single-tenant clusters of identical devices." Mark a single omission only where a reader would look for the topic |
| Lack-based positioning | "Existing systems provide either A or B, but none provides both." | "A-style systems made X cheap and B-style systems made Y reliable; deployed together, Z has become the cost that matters." |
| Audit text | "This section realizes requirement R2." A table mapping requirements to components | Delete; let the order of the account carry the justification |
| The qualification cycle | "The system is general. By general we mean... In particular it applies whenever..., although it does not... As defined above, ..." | A right-sized claim with its evidence: "The same mechanism serves three workloads with different access patterns (§6.2)." |

Other markers worth a search: "note that", "it is important to emphasize", "to be clear", "in other words", "as discussed above", "by X we mean". Each is innocent alone; clusters of them mark defense.

**The deletion test.** Delete each suspect sentence. If no claim becomes false, it was defense. If one does, resize that claim rather than restoring the sentence, unless the sentence is one of the mandatory statements below. A word like "never" in a guarantee stated once, at its mechanism, is content; the table lists shapes, not banned words.

### 5.3 What remains mandatory, and how to say it once

Constructive voice does not relax honesty. Honesty concerns what the paper says about the system and its claims; defense concerns how much of the prose is spent protecting them. Removing defense never removes a statement whose absence would make a claim false (`filtering-and-honesty.md` §5). The following are mandatory, and each is said once, plainly.

- **The decisive limitation.** Name to yourself the limitation that would most reduce belief in the abstract's first claim. Either shrink the claim until it is true without it, or state it once where the claim is made, as a condition and its consequence ("Gains depend on request lengths varying; with uniform lengths the allocator matches a static one"), not as hedge words ("may", "potentially"). It may appear again in the scope paragraph. Papers tend to be candid about harmless costs and quiet about this one.
- **Scope.** The conditions under which the idea works, as a property of the result, near the generality claim or in a short scope paragraph: RDD's §2.4 "Applications Not Suitable for RDDs", vLLM's "does not help training", FunSearch's three applicability conditions. A single bound may be negative; a scattered series of "we do not claim" and "this is not" sentences is defense. The cheapest scope statement is a well-chosen object (§3.2).
- **Reality status.** One plain sentence, early: what is implemented, deployed, and evaluated, at what scale. This is information, not hedging; Levin and Redell's cautionary case is fifteen pages of present tense before revealing that the system is hypothetical.
- **Lineage and credit.** In positive form, once per borrowed part, where it is used: TVM credits Halide's compute/schedule separation and claims "also separating target hardware intrinsics". RDD's single "we do not claim novelty for the language-integrated interface" it shares with DryadLINQ works because it is said once, about one specific borrowed part, and points the reader at what is new: the data-sharing and recovery model.
- **Expected alternatives.** Where a reader genuinely expects a different design, say what the alternative does well and why the choice differs, at that point (Dynamo: textbook version → what breaks here → our variant). A mechanism no reader doubts needs its role, not a defense.
- **The kind of contribution**, once, when the genre is unusual: Dynamo's "the evaluation of how different techniques can be combined". This tells the reader what the paper is; it does not instruct reviewers how to read it.
- **Qualifications that change a claim's truth** go inside that claim's sentence, once. Cutting them to make a claim read stronger remains a cut of qualification.

## 6. Coherence without closure

A systems paper is not a closed theory. Defining a conceptual system, deriving requirements from the definitions, and then showing that the design satisfies them is packaging: the paper ends up defending its definitions, the problem disappears behind them, and the result reads as an exercise in analytical philosophy. A paper needs coherence (each part connected to the problem by reasons), not closure (every boundary defined and every requirement discharged).

**The middle layer gives reasons, not axioms.** It consists of observed difficulties of the problem, premises about workload or environment with numbers (GFS), priced principles (PyTorch's "10% slower is acceptable, 100% is not"), named invariants, or one metric (vLLM's waste taxonomy). Test each item: would a reader grant it before seeing the system, and does a later paragraph use it as the reason for a choice? The layer may stay open; difficulties the construction addresses only partly, and questions stated as such, belong in a research account (`argument-architecture.md` §3).

Symptoms in a draft:
- "Three requirements shape...", "The architecture follows from these requirements", "any solution must satisfy".
- Definitions before anything concrete: "Here, X means...", with a boundary-defining paragraph for each concept.
- Triads with parallel names; bold coined terms; slogans of the form "one X, one Y, one Z".
- Equations that no algorithm, proof, or measurement uses.
- Declarations of what computation or systems fundamentally "are".
- A conceptual section that could be published without the system. A thesis that passes the name-removal test but comes with no literature and no construction is an essay, not a systems paper.

**When formalism is content.** A formal object earns its place when a reader programs against it, checks it, or a claim rests on it: Spanner's invariants and four-line proof over TrueTime; FlashAttention's Theorem 2 with its stated range ("for all M in [d, Nd]") and lower bound; Raft's TLA+ specification beside its proof and implementation. Formalism added for legitimacy is artificial, and reviewers read it that way.

**Restating a manifesto sentence.** Turn the declaration into a problem and an idea, with the object named. "Storage is ultimately a problem of placement" becomes "In write-heavy stores on flash, rewriting dominates cost, and three layers each rewrite independently; coordinating their reclamation removes most of the redundant writes." The second sentence can be checked; the first can only be agreed with.

Conditions: papers whose contribution is a formalism (a consistency model, a calculus) are the exception, and even there the definitions serve results.

## 7. Concreteness without artificial examples

An invented example placed in the introduction and carried through the paper replaces developing the problem. It pulls the introduction down to implementation level before the problem is posed, and its identifiers become the paper's vocabulary.

**Placement.** Introduce an example after the problem has been stated in its own terms, where the first mechanism needs it.

**Significance.** Take it from a real workload, a canonical instance from the field, or a real configuration. Dynamo's shopping cart was Amazon's own service, and it carried the design's unflattering consequence (deleted items can resurface). Bigtable's Webtable, MapReduce's word count, and AlphaTensor's Strassen 2×2 each make a construction visible after the problem is stated; vLLM tracks one prompt block by block through allocation, fork, and copy-on-write at the mechanism. None of them is the paper's motivation.

**The deletion test.** Delete the example. If the problem is no longer stated anywhere, the example had replaced it.

Symptoms in a draft:
- Invented numbers, node names, or identifiers in the introduction.
- "Consider a..." before the research question has been asked.
- Example identifiers recurring in later sections and in the abstract.
- A section that opens "We use X to explain the system", or whose argument is "follow the example through component N".

**Substitutes**, when no real instance exercises the mechanisms: invariants with a short proof, a canonical small instance, or distributions over many real instances. Short hypothetical patterns confined to the section that needs them are fine, marked as illustrations and not carried forward.

**Removing a toy does not remove the obligation.** A draft that drops its invented example and leaves general prose has moved from one pole to the other. The concreteness has to come from the real construction: its interface, a figure of how it works, and one real path through it.

Conditions: a real, significant workload can thread the whole paper if it enters after the problem. Real workloads in the evaluation are always appropriate.

## 8. Names and titles

**Name sparingly.** A name earns its place when it is a handle for a construction the reader will refer to repeatedly, or a quantity they will measure. Coined names for the paper's stance, its conceptual framework, or pairs of operations inside the system are packaging; so are slogans and triads. The system's internal names are not paper names by default. When two names help, give one to the idea and one to the artifact, with the idea's name on the component that produces the benefit (IO-aware; FlashAttention). See `finding-the-contribution.md` §7 for what a name must predict.

Symptoms: bold coined terms in the introduction; a coined word pair as the title; abstract nouns that are internal names; a term introduced once and never needed again.

**Titles state the object and the direction of improvement.** The reader should be able to say, from the title alone, what gets better and for what.

- "Fast and Memory-Efficient Exact Attention with IO-Awareness": the direction (fast, memory-efficient), the object (exact attention), and the idea in a subordinate slot.
- "In Search of an Understandable Consensus Algorithm": the direction (understandable) and the object (a consensus algorithm).
- "An Automated End-to-End Optimizing Compiler for Deep Learning": TVM's "Automated" named the novelty it could evaluate. Its earlier "optimization stack" named a box.

A useful shape: [direction of improvement] [object] [for what scope], with the idea optionally in a subordinate slot. A system name before a colon is common and harmless; what follows should still state object and direction. Failures: neutral process words that imply no direction (evolving, rethinking, towards, revisiting); coined pairs; a generic means in the main slot ("... through learned X", "... via search"); the domain in place of the object; an internal term; "X: a framework for Y". Conditions: a measurement or experience paper's title states the object and the finding rather than an improvement.

## 9. Re-conceiving vs revising

**Recognize a conception-level rejection.** Feedback addresses what the paper is about, its organization, or its approach to writing, not its sentences. It calls the draft a summary of the design, an essay, or not a research paper; it asks to abandon the framing; or the same objection survives a revision. Patching sentences, retitling sections, or tightening a draft under such feedback produces the same paper. The freeze rule guards against unmotivated reframing; the authors rejecting the conception is a named trigger to reopen (`finding-the-contribution.md` §9.3).

**Re-conceive from the problem** (SKILL.md stage 2). Treat old drafts as evidence of what failed: write down, privately, what each got wrong and why, then set them aside. Reuse facts only: measurements, verified citations, accurate descriptions of what exists. Do not carry over sentences, the structure, the example, coined terms, the contribution list, or title words. Check the result: set the new outline beside the old table of contents. If the sections map one-to-one under new names, it is a surface revision. In the review history behind this file, every step forward came from re-conception, none from revising sentences.

**Keep earlier directions that are still valid.** Collect every direction received across rounds into one private list, and check the new draft against the whole list, not the latest round. Point-by-point compliance with the most recent comments tends to drop what that round did not mention, and to overcorrect: the object fixed but the motivating domain dropped; the toy example removed and concreteness with it; a design-led draft replaced by an essay with no system; defense deleted together with the decisive limitation. Treat directions as directions to investigate, not as vocabulary to adopt.

**Defensive accretion.** Each review round adds qualifications, definitions, and justifications to answer comments; the paper grows and the research recedes. Symptoms: length grows every round; "refine" is read as "include more design"; new paragraphs open with "To clarify", "Note that", "It should be emphasized". The fix is to resize claims and delete the defense. A comment is answered by changing the work or the claim, not by writing a reply into the text.

## 10. Open evaluations and briefs

**An unfinished evaluation is written as questions and planned experiments.** For each question: the claim it tests, the workload, the baseline and how it will be configured, the measurement, and what result would refute the claim. Organize by claim, as a finished evaluation would be.

- Do not write numbers, trends, or outcomes that have not been measured. That includes placeholders in result-shaped prose ("achieves up to N× higher throughput", "reduces latency by XX%") and expectations phrased as findings.
- Do not substitute a walkthrough of the design, or an execution trace assembled from it, for evidence. It is surrogate evidence, and it reads as such.
- The abstract and introduction claim only what is built or argued, and the reality-status sentence says the evaluation is planned. A hypothesis may be stated as a hypothesis, with what would refute it. In the claims ledger the status is "future".

**Briefs to other writers or agents stay principle-level**: the problem, the ideas, the constraints of voice, and what is known to have failed and why. Do not pass this skill's tests, tables, or probes down as acceptance criteria, and do not prescribe the system's implementation or the content of each section. A checklist turns writing into filling in a form: the receiving writer fills it well and the paper fails as a whole. Offer seed concepts as hypotheses to investigate, carry forward the earlier directions that still hold (§9), and give internal documents as reading material, not as the structure or the citation base.

## 11. Assumptions and limits

- This file rests on an author's reviews of successive agent-written drafts of one systems paper, and on how classic papers open. It does not rest on the critique-weighted case study behind most of the skill. One author and one paper are a narrow base; the judgments were consistent across review rounds, but some are inferred from what the reviews rejected rather than from what they said.
- Venues and advisors differ in how much explicit scoping they expect. Some communities require a threat model, an assumptions section, or a formal model; there these are content, not defense. Check recently accepted papers at the target venue.
- Constructive voice does not relax evidence. The null-idea test, baselines set per claim, honest causation, and the rule against cutting qualifications (`evidence-and-venue.md`, `filtering-and-honesty.md`) hold unchanged; this file changes where their output lands, not what it must be.
- The patterns in §5.2 are shapes, not banned words, and the rewrites and the storage taxonomy are invented for illustration. Applying the table mechanically is its own form-filling: the question each time is whether the sentence informs the reader or protects the writer.
