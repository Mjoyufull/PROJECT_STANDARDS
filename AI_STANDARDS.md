# AI Standards

**Document Version:** 2.0.0\
**Last Updated:** 2026-09-07\
**Audience:** People using AI to work on their projects

These are my defaults for working with AI on a project. Give the model a clear job,
let it investigate, read what it finds, and use that information to change the project.
The work may be code, documentation, configuration, or something else. The amount of
process should depend on what is still unknown. Instructions addressed to the agent
belong in the project's `codingprocess.md`.

## Start with the project

Point the model at `codingprocess.md` when it exists. Keep that file clear about where
to look, which project documents apply, and how to check the work. You should be able
to name the task without copying those instructions into every prompt.

[CODE_STANDARDS.md](./CODE_STANDARDS.md) covers code and file placement.
[PROJECT_STANDARDS.md](./PROJECT_STANDARDS.md) covers repository layout, Git, and
releases. This document covers the AI workflow. Projects can adapt these defaults.

Be clear about what you want changed. If you are revising an earlier project decision,
say so and include the affected documentation in the work. Expect the model to flag
conflicts it finds. Check its reasoning before accepting a recommendation that changes
an existing requirement.

Existing API contracts, protocol specifications, tests, and user documentation are
evidence about the project. Use them to assess the model's findings and decide which
recommendations to take forward.

### Read enough to understand the change

Use the model to locate the implementation, its callers, and nearby tests. Follow the
affected behavior yourself far enough to judge the proposed change. File names and
summaries are useful starting points, but check the code behind them.

Goedecke's [*In Defense of Not Understanding Your Codebase*](https://www.seangoedecke.com/in-defense-of-not-understanding-your-codebase/)
is the starting point here: useful work often depends on understanding one part of a
large system. [RepoCoder](https://aclanthology.org/2023.emnlp-main.151/) also found that
retrieving relevant code from other files improved completion in its experiments.
The practical rule is to find the context the task needs.

Keep instructions easy to find. In [*Lost in the Middle*](https://arxiv.org/abs/2307.03172),
the models studied used information less reliably when it appeared in the middle of a
long context. That supports concise instructions and direct references; loading every
document is no guarantee the model will use it.

## Research, then make the change

When the approach is uncertain, ask for a research document. Have the model investigate
the actual project and relevant sources, then read its findings and question the parts
that do not add up. Use that information to decide what needs changing. You can make
the changes yourself, work through them together, or delegate them. Straightforward
changes can go directly to implementation.

Research is the default document for working through an unresolved problem. We do not
require a separate spec, plan, decision record, or codebase audit for each stage.
Questions, findings, tradeoffs, and the next steps can live together.

### What a useful research document contains

You should be able to find the question and conclusion quickly, then follow the
evidence behind the recommendation. Look for:

- what was inspected or tested, with useful file references or results;
- relevant papers, books, official documentation, or existing implementations;
- how those findings apply to this project's constraints;
- the recommended change, its costs, and any important unresolved questions;
- how to check whether the change worked.

Use headings when they help. There is no required template, decision numbering scheme,
or minimum length. A comparison needs only the alternatives that could reasonably
change the recommendation. A source belongs next to the claim it supports.

Check the sources behind consequential claims. Prefer primary research and official
documentation; ask for the chapter or edition of a book when that matters. The document
should distinguish measured results, the source author's judgment, and the model's
inference. A study of one model or a small set of repositories does not establish a
rule for every project.

For codebase questions, expect findings grounded in the code: actual dependencies,
tests, and history where it explains why files change together. Keep the investigation
focused on the question. A complete tree or an inventory of every module rarely helps
you decide what to change.

For tooling or dependency choices, check that the research uses the versions and
documentation relevant to your project. Ask for missing evidence when a recommendation
depends on it.

### Use the findings

Say whether you want an investigation only or want the model to carry the findings
through to changes. When delegating the changes, give it enough scope to handle routine
implementation choices without asking you to approve each edit.

Revisit the recommendation when implementation exposes a bad assumption. You can
change direction as the evidence improves. Expect the model to bring back unresolved
behavior questions or proposed work beyond what you delegated.

Keep the next steps in the research document if the work spans sessions. A separate
implementation plan is optional when sequencing is complicated enough to need one.
Ordinary edits need neither a new document nor a path-by-path explanation.

After the work is complete, put lasting rules and explanations where future readers
will need them: the code, tests, existing documentation, or standards. Remove temporary
research once its useful content has been incorporated. Keep a research document only
while it contains useful material that has no better home, or as a record you want
to retain.

This workflow is a project preference. [ReAct](https://arxiv.org/abs/2210.03629) provides
related evidence for revising reasoning through interaction with tools in its tested
tasks; it does not establish that engineering needs any particular set of Markdown
files.

## Make changes that are easy to review

Check proposed file placement against `CODE_STANDARDS.md` and the existing project
layout. New code should sit with the module responsible for the behavior. If the model
adds `common`, `helpers`, or another layer of directories, check whether it has worked
out where the code belongs.

Keep each change focused on the requested outcome. Fix the relevant code directly;
avoid unrelated renames, formatting sweeps, abstractions, and documentation. A wider
refactor can be worthwhile, but it needs an actual reason tied to the task.

When applying a pattern across many files, inspect one complete example first. Check
that the other cases have the same requirements before repeating it. Use a script for
a mechanical edit when that is simpler and easier to check.

Read the diff before handing it over. Address review comments in the affected code
rather than regenerating the whole change. The author remains responsible for what
they submit. These habits follow the responsibility and review guidance in
[Oxide's RFD 576](https://rfd.shared.oxide.computer/rfd/0576).

### Working together in the editor

When you are actively editing, keep the model's job narrow: finish a function, wire
a module, investigate an error, or review a selected change. Ask for small sets of
edits and brief replies so you can review each change and keep working.

A prompt can be as simple as:

> Read `codingprocess.md` and the search code. Fix the stale results after clearing
> the query, check the relevant tests, and tell me what changed. No commits.

A research request can be just as direct:

> Investigate why file placement is confusing here. Read the code and relevant sources,
> recommend changes that fit our standards, and put the findings in a research document.

Neither needs a form. Include constraints or completion criteria when they are not
already clear from the task and project documents.

For a longer delegated task, describe the outcome and the choices you want brought
back to you. Expect progress through the agreed work, with useful updates when findings
change the approach. If it repeatedly fails, inspect the cause together before letting
it keep making changes.

## Check the result

Have the model run the project's required checks and inspect the results yourself.
For a bug fix, reproduce the failure and check the corrected behavior, including the
relevant failure or edge case. For documentation, check the described behavior, links,
and examples. For performance claims, measure.

Passing tests support a conclusion; they do not prove every behavior is correct.
Inspect what the tests exercise. Do not weaken tests, disable hooks, or bypass review
to get a passing result.

Generated code needs the same security review as other code. In
[Perry et al.'s study](https://arxiv.org/abs/2211.03622), participants using an AI assistant
wrote less secure code on the studied tasks while being more confident in its security.
Confidence in the answer is not evidence that it is safe.

A useful handoff tells you what changed, how it was checked, and what remains unresolved,
with file links where they help. If a check could not run, you need to know why before
deciding whether the work is complete.

When comparing models or tools, try representative tasks from your own project and
consider correctness, review effort, cost, and time. Check what external benchmarks
actually measure before using their results to justify a choice.

## Keep project instructions short

`codingprocess.md` is the place for instructions addressed to the model. Prefer that
name for per-repo rules. It only needs the details a new session would otherwise miss:
relevant documents, test commands, local constraints, and any rules about collaboration
or Git.

Link to existing documents instead of copying their contents. Do not require missing
files just because an example mentions them. When the workflow changes, update the
instructions that would otherwise send the next session back to the old approach.

### Status files

If a project tracks work across sessions, keep that status accurate. Use `.status.txt`
to identify the documents or directories that track progress, and explain any update
triggers in `codingprocess.md`.

Put the manifest at the working directory root or name its location in
`codingprocess.md`. Use one path per line; a directory includes its Markdown children.
Comments can explain scope and triggers.

Update entries when the work changes their status. A completed feature, a newly found
blocker, or a changed recommendation may need an update. Rerunning a passing test does
not require a fresh paragraph in every listed file. Do not create status documents
just to satisfy the manifest.

## Write for the person reading it

Use ordinary sentences. Explain the rule and its reason together. Cut repeated
summaries, slogans, mock arguments, unnecessary bold text, and tables that turn two
sentences into a page. Edit out forced slang or swearing the model adds to imitate you.

Lead research with the finding, a change report with the result, and instructions with
what the reader needs to do. Name concrete behavior and evidence. Avoid making a small
choice sound like an architectural doctrine.

Public documentation describes what the project currently does. Update it from the
verified behavior. Internal research and session history belong in working notes unless
they answer a question the public document is meant to cover.

### Public files and reference material

When the project uses `.public.txt`, read it before editing public documentation.
It lists public paths, one per line; a listed directory includes its children.
Keep it at the working directory root or identify its location in `codingprocess.md`.

Public files must not expose internal instructions, research paths, session notes,
or workstation paths. Add deliberately published material to the manifest in the same
change. Keep release claims and licensing text free of internal work narration.

Reference material can live in the project's existing `ref/` directory or equivalent.
It is background unless project instructions explicitly adopt it. Do not create a
reference tree merely to copy templates, and do not publish it by accident.

## Git and external actions

Keep commits under your control by default. If you want the model to commit, say so
and check that it follows these rules:

- It checks the branch and working tree and preserves your unrelated changes.
- It follows the project's branch and PR rules and Conventional Commit format.
- It batches related work instead of committing every edit or internal document update.
- It includes the project's `Co-authored-by` footer for the tool that created the commit.
- It tells you what it committed and what relevant work remains uncommitted.

Set the co-author identity in the project instructions if you delegate commits. Check
for secrets before committing. Be explicit about whether you are also delegating merges,
publishing, destructive Git operations, or messages to other people.
