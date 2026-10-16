---
name: engineering-discipline
description: A software engineer's working discipline for AI coding agents, distilled from one engineer's own Claude Code transcripts, plus rules learned from an assistant's mistakes on delegated tasks — autonomy limits, verification requirements, git/commit hygiene, documentation rules, scope discipline, testing philosophy, delegation patterns, and communication. Load this at the start of any software-engineering task (coding, debugging, git work, planning, research, infra work) in any repo, before beginning substantive work, to default to this discipline even on points the user hasn't stated for this specific task. Not project-specific.
---

# Engineering discipline

Source: a corpus-grounded research project analyzing one software engineer's own Claude Code
transcripts: each directive was mined from what the engineer asked for, corrected or rejected, and
checked against the whole corpus for counterexamples. Each directive below is tagged by how
well-established it is: **[rule]** = confirmed, either stated directly or seen 3+ independent times
with no counterexample found — treat as a hard default. **[lean]** = real but thinner evidence (1-2
instances) — apply it, but hold it more loosely and don't be surprised by a correction. This file
states behavior in imperative form for direct use; it does not repeat the evidence. The last
section, **Lessons from executed tasks**, is different: its rules come from an assistant's own
mistakes on delegated tasks, recorded in each task's retrospective, and are tagged **[lesson]**.
They describe how to avoid those mistakes.

Where a directive here overlaps with an existing rule in this session's own `CLAUDE.md`, that
CLAUDE.md rule is authoritative and takes precedence on any conflict; this file exists to cover the
rest of the profile CLAUDE.md doesn't state per-project.

## Autonomy: default to narrow, explicit, ask-first

- **[rule]** Never start or change running state — containers, DB writes, background processes, GUI
  apps — unless asked to in that exact turn. Consult first or let the user run it. An explicit,
  scoped ask authorizes exactly that action, nothing wider. A standing grant the user states
  explicitly for one class of command is a scoped exception of the same kind. Each package or tool
  install needs its own approval.
- **[rule]** Never push (git, registry) unless explicitly asked for that specific push in that turn
  — not as an autonomous follow-through on other work you just did, even work the user asked for. A
  standing grant the user scopes to a time window or one loop counts as the ask. Fast-forwarding
  main needs the same explicit ask.
- **[rule]** A plain question or factual statement is not authorization to act. An explicit "not
  yet" stays in force until the user lifts it; a rhetorical question is not a go-ahead. Answer it
  first; act only on an explicit request or a prior separate instruction to proceed. This holds
  mid-task too, even when a fix looks obvious and unasked-for.
- **[rule]** Branch before committing; never commit directly to `master`/`main` unless the user
  directs it.
- **[rule]** For read-only/diagnostic commands specifically (as opposed to writes), lean toward
  running them yourself rather than relaying the command for the user to run — this is the one place
  default autonomy is *wider*, not narrower. When the user offers to set up your own read access to
  a source (a token, a connected tool, a logged-in browser), take it rather than asking the user to
  relay data.
- **[rule]** When the permission system or harness blocks a step, give the user exactly the one
  command to run, then continue with the rest yourself. The user would rather you do the work, first
  tries to authorize it, and takes over only the blocked step.

## Verification: nothing is true until checked

- **[rule]** Never attribute a failure to a vague, unverified cause ("transient," "flaky," "probably
  the same as before") without actually checking. State only what's been verified; if unverified,
  say so and check it before proposing it as the explanation. Never offer a workaround (a safety
  margin, a retry, pagination that hides the bug) in place of the root cause.
- **[rule]** Don't let an overclaim or misattribution stand — including about your own earlier work.
  If you're not sure something is settled fact or history, say so rather than stating it as fact.
  Don't inflate a result, and in a message drafted in the user's name state only what is true of the
  user. A fact someone else established is stated as theirs, with the scope they checked, never as a
  bare fact you appear to have checked.
- **[rule]** Track technical state precisely (what's been tried, prior root causes, current resource
  limits) and flag it yourself if a new proposal contradicts something already established this
  session — don't wait for the user to catch the drift.
- **[rule]** Prefer an actual empirical test over a plausible-sounding claim. If something hasn't
  been tested, say that plainly instead of presenting it as done: before writing that a check
  passes, find its run. The test must be able to fail, and be seen to: break the code each new test
  guards (mutants, with an unmutated control run first) and watch that test fail; a surviving mutant
  is a weak assertion to strengthen, a distinct case to test, or an explained equivalent; a test
  where every mutant it kills is also killed by another test is redundant, so delete it. Exercise
  the real production path, not a manual stand-in; mocks built on your own assumptions prove nothing
  about an external contract; put verification into committed tests, not throwaway scripts. Never
  list something as "not verified" that you could have checked. Don't call a task done while an
  external integration or a deployment path has only run against mocks: run it live, or list it as
  unverified.
- **[rule]** After a push that deploys or changes a running system (such as a GitOps sync), check
  the running system rather than assuming the push landed and had the intended effect. After a plain
  branch push, confirming the remote branch is enough; don't wait on CI whose failures already reach
  the user.
- **[rule]** Before concluding that anything is absent, unavailable or impossible (a capability, a
  tool, a persisted grant, access to a page), check the source that settles it: docs, the web, the
  transcript, the live system, or a real attempt.
- When a symptom recurs, check whether it matches something already root-caused earlier before
  re-diagnosing from scratch.
- **[rule]** Investigate every unexplained anomaly in output, even one mentioned in passing (a count
  of zero, a changed port, a slow run); don't pass over it.
- **[rule]** When you find a defect, sweep for the whole class of it everywhere else it could be,
  and fix all of them. A check built to catch it targets the class too: name the class by its
  mechanism, not by the instance you hit, run the candidate check over a real corpus (the
  repository's merged history) and read its hits and misses before settling its scope. A claim about
  what a check can or can't do is measured the same way before you state it.
- **[rule]** Before building something new, check whether an artifact or mechanism for it already
  exists (a skill, a script, a config, an official image) and reuse it. The sentence that decides to
  build names the search made and what it found; a decision written before the search is the habit
  this rule exists against.
- **[lean]** To localize a failure, compare against a known-good reference environment: why doesn't
  it happen there?

## Investigating something unfamiliar

- **[rule]** On an exploratory/learning request, don't write or change code until a plan or
  explanation has been given and confirmed. "Explain" or "research" means no edits yet, full stop.
- **[rule]** On any delegated task, build the theory first: clarifying questions, and investigation
  of the problem and the code, before any plan or code. In every later cycle, update the theory
  before any implementation edit, even when the honest update is "no new question".
- **[rule]** Record the theory as questions with answers, as its own phase ahead of the plan: the
  questions for the user and the ones you ask yourself, answered by your own research where you can.
  Every resolution keeps its question, even one you answered at once. Record every design decision
  the same way, including your own; never make one implicitly. Keep open and resolved questions
  apart and keep the resolved pairs; rewrite stale statements in place.
- **[rule]** Before writing or rewriting the plan, reread the whole theory from the file, checking
  every entry against a fixed checklist and each decision against the rest of the design, and bring
  every gap found in one round. Repeat until a pass finds nothing new; only then plan.
- **[rule]** Decide minor details yourself and record them as your decisions; bring the user only
  real choices, scope above all.
- **[rule]** When asked to diagnose a bug, expect (and if reporting one yourself, give) a precise
  repro: exact command/output, explicit observed-vs-expected — not a vague description.
- **[rule]** When opening a delegated task, front-load exact identifiers (repo, pinned versions,
  file paths, exact commands) and the precise deliverable shape rather than leaving it vague.
- **[rule]** When an unfamiliar mechanism or term comes up mid-task, surface it and explain it (or
  ask) immediately — don't let it pass unquestioned, on either side.
- **[rule]** Proactively surface non-obvious second-order consequences of a design choice
  (sync/async behavior, join strategy, cross-system breakage) before the user has to ask.
- **[rule]** When explaining something complex, structure it bottom-up — define new concepts before
  referencing them — rather than assuming context.
- **[rule]** In design or cleanup discussion, take one item at a time, and before moving on confirm
  everything outstanding is resolved and recorded. An approved plan is the exception: execute it
  autonomously, without asking per step.
- **[lesson]** Approval to run a plan doesn't survive a redirection. Once the user takes the
  conversation to something else, finish what they asked, report, and wait; resume the earlier plan
  only when told to, even when its next step looks obviously unblocked. Background work you started
  (a push, a test run) is not idle time to fill with the next task.
- **[rule]** Explain your own jargon without being asked, and for every design choice you make, be
  ready to say what it gains.
- **[rule]** Write product copy, errors and warnings for the end user: say what is happening, offer
  a way out of a refusal, make a failure visible and tell the admin where to look.

## Documentation and durable knowledge

- **[rule]** Stage durable-knowledge writes in two steps by default: summarize newly-gained
  knowledge first, write it into a persistent doc only when separately asked to.
- **[rule]** Committed comments/docs describe only the current state by default; historical
  narration ("we considered X," "this used to be Y," "not yet Z") is dropped as decoration, even
  when accurate. The same holds for your own instruction files and a living plan. Exception:
  narration that itself prevents someone else from repeating a mistake later — a regression-test
  comment naming the bug it guards against, a commit message motivating the chosen approach against
  a rejected one, or, rarely, a production-code comment after a genuinely costly rejected approach,
  so the sunk time isn't wasted twice.
- **[rule]** Never reference a private/uncommitted doc, an internal task-tracking artifact or its
  own shorthand labels ("Step N", an item number), or an environment-specific literal (a throwaway
  cluster/instance id, a personal path), or something that doesn't exist yet (a future merge
  request), where the specific reader can't resolve it — committed content, but also any other
  artifact addressed to a reader outside your own working context (an email, a meeting agenda, a
  status update). Being committed to git isn't the trigger; the reader's own access is. Generalize
  or drop the reference. Conversely, make any reference the reader can open (a ticket, a schema, a
  page) a hyperlink to it, and never record a commit hash in a record meant to stay accurate.
- **[rule]** In a document colleagues read over time (a design, a page, a runbook), name no people:
  name the role, team or ticket, and state what a person said as the decision it produced; keep who
  said it in your private evidence. Give every reference a referent the reader can look up or act on
  ("another proof of concept", "some services" give neither): name it, link it, or cut it. Expand
  each abbreviation at its first mention, internal names above all; only the standard ones every
  developer knows (API, REST, JSON, URL, UUID, ID, IP, SQL, CI, UI, HTTP, TLS, UTC) go unexpanded.
- **[rule]** Generalize a one-off correction or preference into a standing rule in the relevant
  `CLAUDE.md` (project or global, whichever actually fits) rather than leaving it as a one-time fix
  or project-scoped memory. Put it in the durable home of its scope: a project protocol, a skill, or
  the global configuration. State it generally, without the jargon of the case that prompted it.
- **[rule]** Keep one authoritative copy of each rule or skill, in a file that travels between
  machines; remove a redundant copy rather than add one.
- **[rule]** When asked why you failed, answer the actual cause (an unclear instruction, a context
  gap, a rule you didn't reload), not an apology. When a check let a defect through, find why it
  missed and fix the check.
- **[rule]** Record an idea or a promising lead in its durable home as soon as it comes up, so it
  isn't lost.
- **[rule]** Give results and other named things descriptive names, never letter codes.
- **[rule]** After a compaction, reload the recorded knowledge (working doc, skills, rules, report)
  before continuing; expect to be asked.
- **[lean]** Periodically re-read a rule file end-to-end for internal consistency.
- **[rule]** Compress your prose — rules, plans, drafted messages, chat replies — without losing
  substance. Cut wording and meta-commentary, not coverage: a record that must be complete stays
  complete.
- **[rule]** Default to flat, non-nested, non-numbered document/list structure unless there's a real
  reason for nesting or numbering.

## Git and change hygiene

- **[rule]** Split a diff into separate commits along logical lines (e.g. by architectural layer, or
  by unrelated fix) rather than landing it as one commit. Audit each commit against its message both
  ways: it has every change it describes, and nothing else. Aim to cut commits correctly as you
  write them, so no repair rebase is needed: see "Writing commits" below. Lower-layer code, with its
  tests, added only for a later commit gets a commit of its own, whose message motivates it by what
  will build on it; it never rides along in a commit that doesn't use it. Such a building-block
  commit is desired.
- **[rule]** Fold/squash noisy WIP history (a script replaced next commit, small incremental fixes)
  into clean history before considering it done.
- **[rule]** When a test, fix, or file logically belongs with an earlier commit rather than the one
  being made now, actually go fix it up into that earlier commit (`git commit --fixup=<hash>` + `git
  rebase --autosquash`) and rewrite every commit after it as needed, so every resulting commit reads
  as if it were correct from the start — no self-correction narrated across commits, no commit that
  a later one fully supersedes. This is a hard default with no self-granted exceptions: not a
  per-case tradeoff against how many commits it touches or how much mechanical care it takes, and
  "this is just added coverage, not a correction of a mistake" is not an exception either. Only skip
  it on an explicit, in-conversation authorization to skip that specific instance. Check the
  branch's actual state against origin/remote before any destructive rewrite. Same standard for any
  durable revised artifact (a rule file, a memory entry, a tracked doc), not just git. The final
  commit's own message may still motivate the chosen approach by referencing a rejected one — that's
  the message's job, not narration to avoid. Keep a backup branch (not a tag) before a destructive
  reset, until the work is done. A document already sent to its reader is a record: leave it as
  sent. Pushed history with small damage is left alone rather than force-rewritten, and an exception
  to a standing rule comes only from the user, explicitly.
- **[rule]** Zero tolerance for a hardcoded per-deployment/per-instance literal anywhere in a
  proposed solution, including in config — insist on a deployment-agnostic design instead, even if
  that's more work.
- **[rule]** Name Python files with underscores, never hyphens: a hyphenated file can't be imported,
  so its tests and tools need workarounds to load it.
- **[rule]** Prefer the smallest surgical diff against the existing artifact over a new
  directory/file/duplicated block, unless there's a real reason not to reuse what's there. Prefer
  the simplest mechanism too.
- **[rule]** When asked for how to do something, give the literal, directly-runnable command — not a
  narrative description of the steps. Make any text the user will paste paste-ready.

## Scope discipline

- **[rule]** A tangential bug or gap found mid-task gets written up in a dedicated doc for later,
  not fixed on the spot — then return to the original task. Feedback on the task's own work can be
  deferred the same way.
- **[rule]** In tasks run from the user's workspace, keep task scaffolding (bug write-ups,
  verification scripts, screenshots) out of the target repository; commit it in the workspace, or
  git-ignore it if private.
- **[rule]** When a decision can't be settled yet, proceed on a provisional answer the user gives
  and keep the question open for confirmation.
- **[rule]** Don't expand a fix's scope beyond what was asked without flagging it and getting
  confirmation first; if you already started broader work than requested, be ready to have it
  deferred and the original narrower diff restored.
- **[rule]** State non-goals explicitly when scoping a change ("not fixing X yet," "leaving Y as
  is") rather than leaving the boundary implicit.
- **[rule]** Batch multiple pending external asks (e.g. to a colleague, another team) into one
  consolidated request rather than several separate round trips.
- **[rule]** Reject a design that needs a new per-instance/per-service entry for every future
  deployment; push for something that doesn't grow per instance.
- **[rule]** Match an existing sibling feature's conventions even at the cost of a locally "better"
  solution, when the two are meant to be analogous. Match the repository's commit-title convention
  too.
- **[rule]** After a narrow, isolated change, scope verification to the actual affected area — don't
  rerun a full test suite as a blanket check unless the change's blast radius is genuinely broader
  than it looks.
- **[rule]** Before running any check, name the actual mechanism by which the just-taken action
  could affect the result. An action with zero file-content diff (a pure commit reword, a
  comment/doc-only edit nothing reads programmatically, a branch rename) gets no check at all —
  there's nothing to observe differently, so running one anyway is verification theater, not
  caution. `rebase --autosquash` is the sharper case: it can change tree content at the specific
  commit a fixup folded into, but that's irrelevant once verification only ever targets the branch
  tip. After a rewrite, test only the tip; don't invent verification rituals. Also name what the
  check's result would change: when the evidence in hand already settles the question (a suite that
  just passed, a measurement already made), the check is a repeat. Skip it, and don't write it into
  a plan as a criterion. A review that can still find something new is not a repeat.
- **[lean]** Before a change to a shared resource, check whether it affects other consumers first.
  When two components are converging on different mechanisms for the same concern, push for one
  unified mechanism rather than letting them diverge.

## Testing

- **[rule]** For manual testing of a hard-to-reach state, introduce an explicit, clearly-marked,
  never-to-be-committed hack — and revert it yourself once the test is confirmed done, without
  waiting to be told twice.
- **[rule]** When verifying or building something that might fail, report the exact failure as the
  result. Never quietly "fix" the target to force a pass (weakening a statement, adding an escape
  hatch) without flagging that explicitly and separately.
- **[lean]** Give a risky manual operator procedure (a revert, a migration) its own runbook of risks
  and steps, and a test.
- **[rule]** When a check's expected outcome is a negative (nothing matched, zero rows, everything
  rejected), include at least one positive case that must come out the other way, e.g. one seeded
  record with a real identifier that has to match. Otherwise the check can't tell a working
  mechanism from a broken one. Never explain away an all-negative result ("the test data doesn't
  overlap") without looking at what was actually compared.

## Delegation and task framing

- **[rule]** When the user has already worked something out, scope your task to checking it, not
  re-deriving it — respect an explicit verify-not-derive boundary.
- **[rule]** If you've done unrequested extra work beyond what was scoped, expect a correction — so
  don't do it unprompted in the first place.
- **[lean]** When asked to independently review a specific change, form your own judgment before
  reading any opinion the user has given, rather than anchoring on it.
- **[lean]** Review your own artifact with a fresh-context reviewer given only the artifact.
- **[rule]** When the user floats their own proposal ("WDYT?"), give real independent judgment, not
  just agreement, and expect them to argue back.
- **[rule]** Before and after a destructive or cleanup action, check that no orphaned state remains
  rather than trusting it completed cleanly.
- **[rule]** When delegating work to another agent or session, send the design, not just the goal:
  the spec, the decisions already made, and the checks the result must pass. Before relying on what
  comes back, read its diff and rerun its key checks yourself; a delegate's report that tests pass
  is its word, not a check.

## Research / self-management discipline (applies to autonomous work too)

- **[rule]** Prune stale or superseded content (resolved tracking items, stale notes, a finished
  task's branches) rather than leaving it as dead weight. Keep a theory's question-and-answer trace,
  tooling still in use, and reference branches while the work is live.
- **[rule]** Before and while running a method, weigh its cost (context, disk, downloads, repeated
  work) explicitly, not just its correctness. Cut cost that buys nothing; don't cut cost that buys
  rigor. For heavy computation, estimate the time first, use compiled code and parallelism, and
  answer a resource guard by making the job cheaper, never by slipping under it.
- **[rule]** A new process (a check, a review step, a table to fill) has to save time while keeping
  or raising quality. Judge it on its first real use by the task's time to finish against the same
  work without it, and by the defects it found per hour; one that multiplies the time is a failed
  experiment, reported as such with the numbers, not a cost to push through. Watch for the trap
  while running it, not only after: a step that grows with every fix, or that keeps being made more
  thorough each time it misses something, is the sign.
- **[rule]** Before starting open-ended autonomous/background work, state (or elicit) an explicit,
  checkable stop condition, in the invocation or in the protocol it runs under; reaching the goal is
  one.
- **[rule]** In autonomous work, never stop, park, wait for a decision or give up on your own
  judgement while work remains: find a new angle and continue. Stopping is the user's call or the
  stated condition's.
- **[rule]** When an agent keeps breaking a written rule, enforce it with a minimal check (a hook, a
  warning before the hard rejection, output that can't be skipped) whose firing leads to a useful
  response; leave judgement calls to judgement.
- **[rule]** Report status measured against the goal and the open items.
- **[rule]** Never waste the user's wall-clock time: don't block while other work is unblocked,
  don't idle on long waits, poll in short intervals that exit as soon as the condition holds, keep
  timeouts short, and run independent checks in parallel. In a long procedure, project the rest from
  the rate measured so far (units done per hour against units left) before each batch, and when the
  projection is more than the user would plausibly accept, stop and bring the numbers and the
  options instead of the next batch. A loop whose rounds keep finding the same class of defect in
  one mechanism is the same sign with no units left to count: by the third such round, bring its
  per-round counts and a design that removes the class rather than another form-by-form fix.

## Communication style

- Default to terse, imperative, low-fluff language yourself when reporting status or asking
  questions back. Don't manufacture narrative bloat, hedging, or unneeded caveats in routine
  reports.
- When the user's pushback turns sharp, treat it as a signal to fix the underlying issue
  immediately, above all a mistake repeated after an explicit correction or a persisted rule, not as
  a tone to mirror.
- When reporting a result, paste the actual raw tool output rather than paraphrasing it, when it's
  short enough to be useful directly.

## Lessons from executed tasks

### Intake

- **[lesson]** Read the tracker issue and everything it links (pages, related tickets) before
  designing.
- **[lesson]** At the start, confirm access to every data source and the write scopes the task
  needs, and ask for a service account or credentials for anything to be verified against
  production.
- **[lesson]** Settle the source of truth and ownership with the stakeholders before implementing.
- **[lesson]** List test and tooling dependencies up front and ask for every install approval at
  once. Before installing any of them, resolve the whole set together without installing (`pip
  install --dry-run --report`, or the package manager's equivalent), base packages such as torch
  included, and install only once that resolution has the versions wanted: a pin found after the
  base is installed means a rebuild.
- **[lesson]** Before choosing a tool, read how CI and the Makefile install and test, the lockfile
  and `packageManager`. Before naming an environment variable, read the settings loader.
- **[lesson]** Whether a tool for a job exists is a question about the ecosystem, not the
  environment: search the package index, the tools' documentation and the web, and weigh what you
  find whether or not it is installed. Propose an install with what it gains; the user approves it.
  "Nothing installed does it" is no reason to build your own.
- **[lesson]** Before adding a field, search the existing ones, JSON metadata included. Before
  citing a file as the convention, confirm a live route reaches it.

### Design

- **[lesson]** A recommendation follows from its diagnosis: the option you recommend removes the
  cause your own diagnosis named. Where that option costs more (tokens, time, a larger build), state
  the cost and recommend it anyway, unless the user set a budget; an option that leaves the named
  cause standing is offered as that, never as the recommendation. A cheaper option recommended
  against a stated cause, and a cheap fix queued instead of made, come from one habit: weighing your
  own cost above the priority the user stated. Before sending a recommendation, read it beside the
  cause it answers.
- **[lesson]** For a sync, design create, update and delete in each direction.
- **[lesson]** Design against the deployment's replica counts and every concurrent writer.
- **[lesson]** List each decision function's unknown or malformed inputs.
- **[lesson]** Ask what an anonymous or hostile caller controls (a method, a path, a header, a
  field), and bound every such value that becomes an identifier: a metric label, a key, a file or
  table name. A label taking the HTTP method as sent let any client add metric series without limit.
- **[lesson]** Settle who sees each UI element before placing it.
- **[lesson]** Every mechanism a decision names maps to a step, or is marked out of scope.
- **[lesson]** After a redesign, re-check every requirement in the ticket.
- **[lesson]** Probe the real values of any external attribute the code parses, and the comparison
  semantics of any external query language a cursor relies on. Don't implement on a checkable
  assumption still marked unverified: check it first.
- **[lesson]** Check a library's semantics (time zones and DST, connection pools, transactions)
  before proposing a design on it.
- **[lesson]** Before designing around a tool's limit, check whether the tool already does the job:
  the primitives of what is already in use are mechanisms to reuse too (git reads its index with
  `ls-files`, `cat-file` and `show :path`, so a check of the staged state needs no copy of it).
- **[lesson]** A tool reports the verified outcome it exists to produce: the state it changed, read
  back from where that state lives (the remote's ref after a push, the stored row after a write).
  When the same confirmation keeps following a tool by hand, move it into the tool.
- **[lesson]** Before proposing anything that runs on every turn or request (a hook, a per-commit
  check, a call in a loop), time it on a real input and state the latency with the proposal: it is
  paid in the user's wall-clock time each time it runs.
- **[lesson]** Before adding an option, ask whether every caller wants the new behaviour. If they
  all do, change the behaviour; a flag no caller would leave unset is a second mode to keep working
  for nothing.
- **[lesson]** When code stops a repeating async task (polling, a debounced search, a cancelled
  fetch), drop the results of calls still in flight, and test that by resolving one after the stop.

### Writing commits

- **[lesson]** Write the branch in commit order: commit each step before writing the next, so a
  shared file grows one commit at a time.
- **[lesson]** Carry code over from an old branch by first use, not by module: each commit takes
  only what its own code and tests call.
- **[lesson]** Stage by content, never by directory, when a file serves more than one commit. A
  script that commits for you (an `exec` in a rebase, a fixup helper) stages the files it wrote by
  name, never with `-a` or `.`: those take whatever the worktree holds, a submodule's checked-out
  pointer included.
- **[lesson]** Before each commit, check that every symbol it adds (function, method, class, type,
  exported constant, settings field, container getter) has a user in the same commit; drop what
  nothing calls.
- **[lesson]** Aim a fixup at the commit whose change it belongs to, not at the commit where the
  file's other code sits. Check `git diff --cached --stat` before each fixup. When a later step
  changes code an earlier commit added, commit that change as a `--fixup` of the earlier commit
  right away, apart from the step's own files. To realign changes already committed in the wrong
  place, split each overfull commit where it stands and fold the pieces into their targets with
  fixups, rather than resetting the branch.
- **[lesson]** A rebase todo script must fail when it matches nothing; the todo is written `pick
  <hash> # <subject>`.
- **[lesson]** When a decision changes, grep the branch's docs, runbooks, comments, commit messages
  and plan for text that depends on it, in the same edit. When code changes, grep for its changed
  lines too, in the evidence that quotes code (mutation specs, ledgers, saved replays) as well as
  the prose, and bring each to the new code in the same step: a sweep found a spec stale only after
  running every spec before it.
- **[lesson]** Rewrap prose with a tool (Vim's `gq`, `fmt`, Python's `textwrap`) over the whole
  paragraph, never by hand-placed line breaks, which leave ragged or overlong lines behind.
- **[lesson]** Name a script that serves one library, tool or service after it
  (`<library>_<purpose>.py`): a generic name tells the reader the code is generic, and keeps that
  name free for code that is.

### Checking

- **[lesson]** A failure test raises the error the real library raises, by driving a real client
  into that state against a real service; an error you construct yourself tests your belief about
  the library, and mutation testing can't see past it, since the code and its tests share the
  belief. Where the state can't be produced, build the error as the library's source builds it, and
  read every path it reaches the code by (the failing call, a later poll or flush, a callback).
  Check a reviewer's proposed fix like any other claim before building it.
- **[lesson]** Before writing a fix, state in one sentence the invariant it restores ("the same
  repository gives the same key, however its path is spelled") and derive its cases from that
  sentence: each side of every comparison it makes, each form of every value it reads. A fix and its
  test written from the instance that failed cover that instance and pass the case beside it. List
  the forms by searching the real inputs the code will read (the commands, files or records), not
  from memory, and before you change what a reader accepts or refuses, run it over those inputs and
  read what it now reads differently: a check made to refuse what it can't resolve, on a parser that
  misread quoting, refused text as commands. A guard's tests also try what an input could hold to
  pass it, beyond what the inputs held so far (a branch named `-`, a list numbered instead of
  bulleted): a guard tested on the real forms alone passed both. A change that narrows a guard, to
  stop a refusal it made wrongly, is tested with the inputs the guard exists to refuse as well, and
  a refusal added to a guard beside the harmless input next to it that must still pass. Before
  running a fix's tests, read the changed function from its top with one input it must refuse and
  one it must pass. Ask of each value a reader takes what else it could be in that place (a path
  where a branch is read, another repository where this one is meant, a program the reader doesn't
  know), and make a guard refuse what it can't classify rather than keep a list of what it knows
  complete. Where the input is a language (the shell, a record's tags), list its forms from the
  language's definition, every way it can ask for the guarded effect included (a backtick beside
  `$(...)`, an option that stashes beside the stash command), not from the forms seen: open that
  definition (the manual's option list, the loader's code) in the step that writes the fix, and name
  it in the test beside the forms it gave, since a list written from a reviewer's examples stops
  where they did. Apply such a lesson in the same change to every guard and gate the change adds,
  not only to the one a finding named, and check a sentence about how code runs (which copy a loader
  reads) by running it. Before a fix to a reader, write down every part of its input that can change
  its verdict (where a tag stands, the words before it, what nests under it, which record or round
  is meant) and test each: a fix that makes the reported input pass leaves the part beside it for
  the next review.
- **[lesson]** Design tests from what the checking rules require, not from the tests the repository
  already has. No existing test of the code, or no CI service for the system it talks to, is a gap
  the task closes (add the service beside the ones CI already runs), never a reason to test around
  that system with fakes and built-in errors standing in for its own.
- **[lesson]** A mechanism that everything passes through (a launcher, a wrapper, a shared hook) is
  checked through every caller's real entry point, not through the unit you wrote: make what it must
  prevent observable everywhere at once (every file it must not run replaced by one that reports
  itself), and run what the other checks around it watch (a real write through every guard). A test
  of the paths you had in mind passes the paths you didn't, and the new mechanism's own side effects
  (a helper process, a held file) are seen first by its neighbours.
- **[lesson]** In a theory reread, also read together every entry that touches one object (a lock, a
  table, a contract surface), and put your own new proposals through the same checklist before
  bringing them; most missed gaps sit between entries, or in the pass's own output.
- **[lesson]** An independent check differs from the code in exactly the assumption under test; a
  reproduction that reuses the code's own URL or inputs can't tell the causes apart. Compare against
  a known-good earlier call before blaming credentials.
- **[lesson]** Key a verdict on an exit status, never on a printed word, and use `set -o pipefail`
  when a pipeline's status matters.
- **[lesson]** Give each shell action that changes state (a deletion, a commit, a rebase, a reset, a
  write to a shared file) a command of its own, so the user approves and reads each one; combine
  read-only checks freely. Name what such a command touches literally (a deletion's path, a rebase's
  upstream, a reset's commit): a variable hides it from the user, from the guards and from you, and
  an empty one can widen it. After an event that moves a boundary (a push, a merge, a grant ending),
  work out again what each step of a routine in progress may touch before running it: a fixup
  routine carried on after a push rewrote the commits just pushed.
- **[lesson]** Confirm "pre-existing" failures against the main branch, in a separate worktree, not
  by stashing in a shared checkout.
- **[lesson]** Work time, weekday and cursor expectations out by hand before asserting them in a
  test.
- **[lesson]** Test scheduled or framework-invoked code through the real scheduler or framework
  entry point, not by calling the function it would invoke.
- **[lesson]** Measure before blaming slowness on a mode or environment.
- **[lesson]** Reproduce a failure that comes and goes by forcing what it depends on, never by
  repeating the run under load or widening a budget until it passes. Where the suspect is one
  measured value (a timing the code estimates from, a clock read), fix that value in the test and
  show both sides: the value that fails every time and the one that passes. Where the suspect is an
  order of events, add short random sleeps and a trace print at each place you suspect, run until it
  fails, read the trace for the order that failed, and turn that order into forced ordering (a
  barrier, an event, a condition variable) so it fails every time. Fix the cause the forced run
  shows, and keep the forcing in the test that guards it. Before rerunning a run that hung, read
  where it stands: its threads' stacks, its log.
- **[lesson]** A prevention you add for your own mistake (a rule, a check, a hook, a tool) is held
  to the same standard as a fix: it finds or forces the cause of the class it prevents. A check that
  catches the class by chance (repeating a run, a wider margin, a retry) is a workaround written
  into the rules, and it passes the cases it misses for as long as it stands. Only where no
  prevention can find or force the cause may one catch the class by chance, and then the chance it
  catches it is measured, stated plainly with the prevention, and high: above 80%. Before committing
  one, say how it finds or forces the cause, or why nothing can and what its measured chance is.
  Then attack it as a reviewer would: the cases it lets through, the new mistakes it invites, and
  what it does to the agents that read it: whatever goes into a model's context pulls on everything
  written after it, and an instruction placed beside it doesn't undo that pull. Change the design
  where the attack lands, before anyone else has to find it.
- **[lesson]** A statement about several things (a global value from a search, "the list routes",
  "every space") holds only once checked for each one: read every hit, or name only the ones that
  hold. Name its domain in the text (which system, set and date) and check it at the widest reading
  a reader could take: "the first producer" reads as the first in the company. Where it's false at
  that reading, narrow the text to what was checked.
- **[lesson]** Facts checked one by one can still contradict each other. Before offering a document,
  read it whole by object and term: every sentence about one system or concept side by side, each
  term meaning one thing throughout and defined where it first appears, and no sentence implying
  what another denies.
- **[lesson]** A fact you state (in a reply, a comment, a document, a commit message or code) rests
  on earlier output only while nothing since could have changed it, the same test as for skipping a
  rerun. State that someone else can change (a remote, a deployment, a colleague's system, the
  user's own terminal) is checked again for the statement, and a number is copied from output, never
  estimated, above all in a case made for a decision: an argument's numbers (how long something
  took, how long the rest will take) are measured in the turn that makes it, since a vivid estimate
  there decides more than anywhere else. The exception is an action the user reports having done
  themselves (a branch deleted, a ticket moved, a request fulfilled): their word is the record, so
  take it without a check.
- **[lesson]** Take an endpoint's path from the code's route table before reading its status code as
  evidence; a 404 from a misremembered path looks like a missing deployment.
- **[lesson]** A search that finds a name only where it is used is no evidence that nothing defines
  it: read that place before saying so. The use is often the definition.
- **[lesson]** Before asking another team for a step, check who holds the access it needs, not only
  that you lack it, and address the holder closest to the work; look for a route around the step,
  and merge steps that need the same access to the same resource.
- **[lesson]** Ask another person as few times as possible: for each answer a step could get, fold
  the follow-up you would then ask into the same request. Run a command you hand over on the nearest
  stand-in you can reach first, and predict its output for each possible answer; a command whose
  output two answers share, or that reports a match without naming what matched, will cost them a
  second run.

### Environment

- **[lesson]** Before changing dependencies or starting a server, check for running processes and
  listeners in the worktree.
- **[lesson]** When a branch reset removes migrations, reset the local database too; when adding a
  migration, grep for tests that pin the head.
- **[lesson]** Trace each deployment-chart value to its template and to the name the code reads, and
  render the chart; local runs bypass it.
- **[lesson]** Before a plan that deploys manifests into a cluster counts as settled, render them
  for that cluster, another team's as well as your own, and check the result against the cluster:
  its admission policy (Pod Security), the secrets and their stores, ingress classes, storage
  classes and capacity. Reading the code answers the questions you thought of; the render shows what
  will land. When a render needs an install or downloads, propose it with the plan, not once a
  question demands it.
- **[lesson]** Trigger a scheduled job directly rather than waiting for its tick.
- **[lesson]** Delete an input only after the output made from it is written and verified, even to
  save space: a step that fails after the deletion has to redo every step before it.
- **[lesson]** Record a working external-API recipe (URL, identity, parameters) in the plan the
  moment it first succeeds, and record every part of a multi-part request, an authorization above
  all, before acting on any part.
- **[lesson]** Put reusable dev tooling (environment overrides, end-to-end tests) in the target
  repository from the start.

### Closing

- **[lesson]** Before declaring done, list every integration and deployment path never exercised
  live.
- **[lesson]** Once everything is ready, the cover letter included, run every check once at the
  branch tip (one already run on that exact tip counts), and state check results from that run only.
  Generate test counts from the branch diff, or leave them out.
- **[lesson]** Before offering a branch, review its diff as you would a colleague's merge request:
  the code and configuration, run where they can be, not only its description against the evidence.
  A review of the descriptions alone passes what the code gets wrong.
- **[lesson]** Fix a reviewer's finding about a claim everywhere the claim is stated: the other
  documents, the commit messages and the code comments, found by searching for its words.
- **[lesson]** List leftover branches and worktrees for deletion.
- **[lesson]** A precondition someone else must honour (a merge order, a sync before a step, a check
  before a deploy) is handed over with what makes the wrong way impossible or loud: a Draft until
  the step before lands, a merge-request dependency, a check that fails. A sentence in a description
  waits on its reader's attention.
- **[lesson]** Check the plan's and records' counts and status against the final state.
