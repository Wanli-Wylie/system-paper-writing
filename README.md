# System paper writing

An Agent Skill for turning a complex software system into a systems research paper with independent academic value. A system's units are modules, documents, releases and the order in which decisions were made; a paper's units are claims. The Skill bridges the two. It reads a system for the decisions that carry an idea; formulates the research problem from the development of the field rather than from the system's account of itself; finds what the work changes and chooses a thesis at the layer most likely to outlive the implementation; builds a constructive narrative from problem to ideas to the system as their construction; decides what to keep, demote or cut without cutting the qualifications that keep claims honest; designs evidence that tests the idea rather than incidental engineering; and diagnoses drafts that read as a catalog of features, as concepts with no mechanism or problem behind them, or as a defense of claims instead of their development. It keeps two representations apart: the narrative the reader follows, and the claims, tests and ledgers the writer uses to check it, which change the work but never appear in the prose. It is a set of judgments with their reasons and conditions, not a chapter template.

It was distilled from a study of how about two dozen classic papers were actually made from their systems (MapReduce, Spark/RDD, TensorFlow, PyTorch, XLA/JAX, TVM, Ray, Pathways, GFS, HDFS, Bigtable, Ceph, Dynamo, Raft, Paxos Made Live, Spanner, Borg, Omega, vLLM, FlashAttention, AlphaTensor, AlphaDev, FunSearch, AlphaEvolve), comparing each paper against the code, documentation, earlier versions, retrospectives and critiques of the system it describes, from a reading of the advice literature on writing systems papers tested against those cases, and from an author's reviews of successive agent-written drafts of one systems paper, which showed where agents fail at the constructive side of the work.

## Who it is for

Anyone, human or agent, who must write, outline, frame, restructure or review a paper, technical report, white paper or thesis chapter about a system: distributed systems, storage, databases, ML frameworks and compilers, runtimes, infrastructure, AI-driven discovery and agentic systems. It is written for agents first. An agent handed a large pile of design documents and code tends to produce an outline that mirrors the repository or the design package, adopt the system's internal vocabulary as the paper's frame, mistake its technique for the problem, derive requirements from concepts it just defined, manufacture a gap shaped like the system, qualify and re-justify every claim, credit end-to-end numbers to the idea, and polish prose inside a frame it never questioned; the Skill names those tendencies and gives the moves that catch them. It is also useful to authors who want to know why their draft "reads like documentation" or why reviewers found "no big idea".

## Installing

The repository is the Skill directory itself, so cloning it into a skills directory installs it:

```
git clone https://github.com/Wanli-Wylie/system-paper-writing.git ~/.claude/skills/system-paper-writing
```

To update, pull in that directory. A downloaded archive of the repository, unpacked and renamed `system-paper-writing`, works the same way.

**Claude Code.** Place the directory, named `system-paper-writing`, in `~/.claude/skills/` to make it available in every project, or in `.claude/skills/` at the root of a project to share it with everyone who works in that repository. Claude Code reads the name and description from the frontmatter of `SKILL.md`. The Skill then triggers by itself when a task matches its description, and it can also be invoked by name, as `/system-paper-writing`.

**Other environments.** Any environment that follows the Agent Skills specification can load the directory as it is: `SKILL.md` with YAML frontmatter (`name` and `description`) and Markdown instructions, and a `references/` directory of Markdown files that the instructions name and that are read only when needed. There are no scripts, assets or dependencies.

## How it is used

The Skill triggers when the hard part of a writing task is deciding what the paper argues: "help me write the OSDI paper for our system", "what is the contribution of this codebase", "turn these design docs into a paper", "our draft reads like documentation", "reviewers said there is no big idea", "frame our system for Nature". It applies whether the starting point is a system with no paper, a draft, or someone else's submission under review.

Read `SKILL.md` first. It holds the stance, nine stages (reading the system for decisions; formulating the research problem from the field; finding what the work changes and the thesis; choosing the frame, genre and venue fit; building the argument; filtering and tiering material; designing evidence as claim tests; drafting; diagnosing and revising), the few working artifacts worth keeping, and the decisions only the authors can make. Most work can proceed from it alone. When a stage needs depth, open the reference its closing table names; references are loaded only then.

## Layout

```
system-paper-writing/
  SKILL.md                         the entry: two representations, three failure poles, stance, the nine stages, working artifacts, questions for authors, caveats, which reference to open
  README.md                        this file
  CHANGES.md                       the change record, newest first
  references/
    reading-a-system.md            reading docs, code, git history, telemetry and postmortems for decisions; structural and conceptual capture; the genre a system supports
    constructive-narrative.md      formulating the problem from the field; problem vs means, domain vs object; constructive categories for prior work; constructive voice; coherence without closure; examples, names, titles; re-conceiving; open evaluations and briefs
    finding-the-contribution.md    what the work changes; stated vs real gap; generating and testing thesis candidates; kinds of elevated concept; WHAT vs HOW layers; naming; generality; competing frames; splitting into several papers
    argument-architecture.md       altitude; spine and middle layer; mechanism-to-capability chains; ordering; tables, vocabulary, examples and concreteness, figures; the first page
    filtering-and-honesty.md       what to keep, demote or cut and where it goes; cuts of scope vs cuts of qualification; attack surfaces; honest vs revisionist narrative; disclosure
    evidence-and-venue.md          claim types; the null-idea test; kinds of evidence; baselines; limitations and lessons; venue currencies; contribution types; maturity and openness
    diagnostics.md                 reviewing and revising a draft: meta-tests, a symptom catalog traced upstream (including narrative and voice), reviewer and critic simulation, prioritizing fixes
    worked-transformations.md      how real papers were made from their systems, organized by transformation move, with recognition cues and labeled counterfactual rewrites
    origins.md                     for maintainers: the studies the Skill came from, its case set and its limits
  LICENSE                          MIT
```

## Origins

The Skill was produced by a research study run as a set of coordinated agents: eleven parallel case studies, each comparing one or two classic papers against the real system behind them, plus one study of the advice literature; four cross-case syntheses, each through one lens (from system to contribution; argument architecture; evidence and context; process and diagnosis); then the Skill, written against those syntheses and checked once for consistency against the case material. A second edition added guidance on constructive narrative, drawn from an author's reviews of successive agent-written drafts of one systems paper: one agent extracted the author's preferences from the drafts and reviews, another audited the Skill against them, and the Skill was revised where its checks, read literally, would have produced design-led, deduction-driven or defensive drafts. The research records are not distributed and nothing here depends on it; the cases the Skill needs are carried in its references. `references/origins.md` describes the studies, the case set and what they rest on.

## Revising

The Skill should change when applying it to a real system produces a worse paper, or when it is silent on a decision that mattered, and the reason can be stated in general terms. Record the change in `CHANGES.md`: what changed, what forced it, and what it replaces. A new lesson is added as a new case or move, not by rewriting an old case to fit it.

## License

MIT; see [LICENSE](LICENSE). The Skill may be copied, adapted and redistributed with the copyright notice kept.
