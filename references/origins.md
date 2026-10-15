# Origins

For maintainers: the study this Skill came from, the cases it read, how the Skill was built from it, and what it rests on. Nothing in the Skill depends on this file; the case evidence the Skill uses is carried in the other references.

## Contents

- [The question](#the-question)
- [The case set](#the-case-set)
- [How the study ran](#how-the-study-ran)
- [How the Skill was built](#how-the-skill-was-built)
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

## What it rests on

- **Survivorship.** Apart from HDFS and the XLA material, the cases are influential papers. Their influence also owed something to authority, scale, access and timing that a new group may not have; the Skill weights evidence-producing moves above authority-dependent ones for that reason.
- **Inferred process.** No case documents its authors' internal writing process. Claims about how papers were reshaped are inferred from differences between versions, from retrospectives and from critiques.
- **Secondhand reading.** Some sources were unavailable or read only through summaries; claims flagged as unverified in the studies were left out of the Skill or kept with their hedge.
- **Diagnosis over generation.** The material is stronger at telling that a frame is wrong than at producing a good one where no recorded move fits. The Skill has not yet been applied end to end to an unpublished system; the first such application is the most useful next revision.
