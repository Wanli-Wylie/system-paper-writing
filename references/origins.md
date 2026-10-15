# Origins

For maintainers: the two sources this Skill came from, the cases the first one read, how the Skill was built and later revised, and what it rests on. Nothing in the Skill depends on this file; the case evidence the Skill uses is carried in the other references.

## Contents

- [The question](#the-question)
- [The case set](#the-case-set)
- [How the study ran](#how-the-study-ran)
- [How the Skill was built](#how-the-skill-was-built)
- [The second source: reviews of agent-written drafts](#the-second-source-reviews-of-agent-written-drafts)
- [What it rests on](#what-it-rests-on)

## The question

How are classic systems papers actually written from their systems? Not how the systems work, but how authors turned large, messy implementations into papers with independent academic value: how they defined the problem space, chose the system-level concept worth discussing, filtered engineering that was true but carried no argument, rendered concrete designs as mechanisms that explain capabilities, and arranged sections, figures and experiments to carry the argument. The object of study was the gap between each system and its paper.

## The case set

Cases were chosen for academic significance, design complexity, and the availability of material beyond the paper itself: earlier or later versions, code, documentation, retrospectives, critiques. Pairs were chosen so that each case had a contrast.

| Domain | Papers studied | Contrast material used |
|---|---|---|
| Batch dataflow | MapReduce (OSDI 2004); RDD/Spark (NSDI 2012) | CACM 2008 version; HotCloud 2010 Spark paper; dissertation; Spark codebase |
| ML frameworks | TensorFlow whitepaper (2015) and OSDI 2016 paper; PyTorch (NeurIPS 2019) | The same system under two narratives; public NeurIPS reviews; PyTorch design docs |
| ML compilers | TVM (OSDI 2018); JAX (SysML 2018); XLA | TVM arXiv v1 vs v3; XLA documentation and third-party descriptions (XLA has no framing paper) |
| Distributed execution | Ray (OSDI 2018); Pathways (MLSys 2022) | Ray position paper, arXiv versions, Ownership (NSDI 2021), architecture whitepapers |
| File systems | GFS (SOSP 2003); HDFS (MSST 2010) | 2009 ACM Queue interview on GFS; HDFS as a contrast case |
| Structured and object storage | Bigtable (OSDI 2006); Ceph (OSDI 2006) | CRUSH and RADOS papers; dissertation; later Megastore and Spanner motivations |
| Replication and consensus | Dynamo (SOSP 2007); Raft (ATC 2014); Paxos Made Live (PODC 2007) | DynamoDB (ATC 2022); Raft dissertation and its account of review history |
| Planet-scale infrastructure | Spanner (OSDI 2012); Borg (EuroSys 2015); Omega (EuroSys 2013) | Spanner SQL paper (SIGMOD 2017); Brewer's note on Spanner, TrueTime and CAP |
| AI-driven discovery | AlphaTensor (Nature 2022); AlphaDev (Nature 2023) | Methods and supplementary information; released repositories; later human improvements and public critiques |
| LLM-driven discovery | FunSearch (Nature 2023); AlphaEvolve (white paper 2025) | Released code and results; follow-up studies and open reimplementations |
| LLM systems | vLLM/PagedAttention (SOSP 2023); FlashAttention (NeurIPS 2022) | Repositories at the published versions; launch posts |
| Advice literature | Levin and Redell; Lampson; Peyton Jones; Widom; Roscoe; Heiser; conference calls for papers; Nature's format guide | Tested claim by claim against the cases |

## How the study ran

1. **Case studies.** Eleven agents worked in parallel, one per row above, under a shared brief. Each read the primary papers in full and then whatever showed the real system beyond the paper, and answered the same questions: what the system comprised; how the problem was framed (the stated gap and the real gap); what concept was elevated and how it was named; what was filtered and by what rule; how design became mechanism and capability; what each section and figure did in the argument; what the evidence tested; how venue and contribution type shaped the paper; and what transfers. A twelfth agent studied the advice literature and listed its claims to be tested against the cases. Sources that could not be retrieved, or were read only through summaries, were flagged in each study.
2. **Syntheses.** Four agents each read all twelve studies and synthesized across them through one lens: from system to contribution; the architecture of the argument; evidence and context (venue, contribution type, maturity, openness); and the working process with the diagnosis of drafts. Each was asked for judgments, tests and conditions rather than templates, and to test the advice literature against practice.

## How the Skill was built

`SKILL.md` was written from the four syntheses. The seven references were written in parallel against it, each from the syntheses and case studies relevant to one stage, and each author reported where `SKILL.md` should change to stay consistent; those changes were made. A final pass checked about sixty case facts and section references against the studies, removed duplication between references, and softened places that had hardened into fill-in forms. No formal evaluation loop was run.

## The second source: reviews of agent-written drafts

The case study looked at finished papers and the systems behind them. It could not see what a draft written from a system reads like before anyone has shaped it, or how a paper constructs its problem from the field rather than defends a claim. The second source supplies that view: an author's reviews of successive agent-written drafts of one systems paper. Each review judged a whole draft and set directions for the next, so the sequence shows which failures persisted across rounds, which fixes introduced new failures, and what the author accepted as a research paper.

The revision ran in three steps:

1. **Extraction.** One agent read the drafts and the reviews together and extracted the author's preferences about constructive narrative, tracing each preference through the sequence of drafts (what a draft did, what the review said, what the next draft changed) and restating it in a form that applies to any systems paper.
2. **Audit.** A second agent read the Skill in full against those preferences and listed where its guidance conflicted with them, where a literal reading would produce the rejected behavior, and where it was silent. Each item came with proposed text, and the audit separated genuine conflicts from apparent ones that a distinction resolves (honest scope versus defensive qualification, private checks versus printed audit text).
3. **Revision.** `SKILL.md` was revised first: the reader's narrative separated from the writer's analysis, a stage for formulating the problem from the field, a third failure pole (Defense), and a stance on defending in the work rather than in the prose. The references were then revised in parallel to match, and `references/constructive-narrative.md` was added. Sound evidence guidance from the case study (the null-idea test, attribution, baselines, honest versus revisionist causation) was kept; what changed is where its output lands.

The drafts and reviews are not reproduced in the Skill. No project names, titles, quotations, or internal concepts from them appear in it, and its illustrations of the new guidance use other domains.

## What it rests on

- **Survivorship.** Apart from HDFS and the XLA material, the cases are influential papers. Their influence also owed something to authority, scale, access and timing that a new group may not have; the Skill weights evidence-producing moves above authority-dependent ones for that reason.
- **Inferred process.** No case documents its authors' internal writing process. Claims about how papers were reshaped are inferred from differences between versions, from retrospectives and from critiques.
- **Secondhand reading.** Some sources were unavailable or read only through summaries; claims flagged as unverified in the studies were left out of the Skill or kept with their hedge.
- **Critique-weighted evidence.** The case study's strongest evidence is where critics struck: omitted baselines, untested conjunctions, over-attributed numbers. That weights the case-derived guidance toward defensive checks and says little about how a paper constructs its problem from the field. Followed as voice rather than as checks, it produces defensive prose. The second source was added to cover the constructive side, and the Skill now routes the checks' output into the work (claim size, experiments, scope) rather than into the text.
- **A strong but narrow second source.** The reviews are one author's judgment on one paper. The signal is strong, because it comes from direct review of whole drafts rather than from inference across versions, and because the failures it names recurred across rounds. It is also narrow: venues and advisors differ in how much explicit scoping, qualification, and formal structure they expect, and the drafts were written by agents, so the guidance is weighted toward the failures agents make (anchoring on a brief's vocabulary, writing checks into prose, following a design package's order). Where the Skill's voice guidance and a target venue's recently accepted papers disagree, the venue's papers are the better evidence for that venue.
- **Diagnosis over generation.** The material is stronger at telling that a frame is wrong than at producing a good one where no recorded move fits. The drafts behind the second source were written before the revision, so the revised Skill has not itself been applied end to end to an unpublished system; that application is the most useful next revision.
