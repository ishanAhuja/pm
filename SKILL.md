---
name: pm
description: Concurrently supervise ready tickets produced under a parent spec issue and land them serially.
---

# PM

Run the live `ready-for-agent` frontier produced from the supplied parent spec issue with concurrent implementation and serial landing. Accept an issue number or URL.

## Authority

This invocation authorizes creating implementation tasks, pushing their branches, landing them through the repository's required path, and closing landed child issues.

Leave the parent issue unchanged and open. Manual deployments, production or data mutations, bypassed protections, and product decisions remain separate. If landing triggers a production deployment, get explicit user authorization before the first dispatch; repository guidance that merely documents the trigger is not authorization.

## Process

1. **Pin the live queue.** Read the repository's agent, tracker, and landing guidance. Treat the supplied issue as the authoritative spec, not an implementation ticket, and fetch its current body and comments in full. Discover native child issues; if none exist, use only tickets whose `Parent` section or configured tracker relationship points to the spec. An ordinary issue mention is not a child edge, and the spec's own `ready-for-agent` label does not make it dispatchable. Refresh every child's state, labels, and blockers. Determine the integration branch and required landing path.

   Complete when the ordered live frontier and landing path are known.

2. **Select the frontier.** Choose as many open `ready-for-agent` children with closed blockers and no active task as current task and worktree capacity permits. Prefer native sub-issue order; otherwise use dependency order, then issue number. Read every selected child and its comments in full, confirm its parent is the supplied spec, and refresh its declared blocking edges. Update the integration branch and record its commit as their shared predecessor.

   Complete when the selected child URLs and predecessor commit are pinned. If none can be selected but implementation tasks remain active, return to supervision. If none are eligible or active, finish with the exact reason.

3. **Dispatch fresh tasks.** Create one isolated worktree task from the shared predecessor for each selected child. Dispatch all of them before waiting, using this initial prompt:

   ```text
   Implement <full-child-issue-url>.

   Before acting, call the Skill tool with `implement` and follow its process.
   Treat that skill as authoritative.
   ```

   Communicate through context pointers: the child URL, predecessor commit, integration branch, and repository guidance. Add only context unavailable through those pointers. Keep exactly one implementation task per child and never dispatch a duplicate.

   Complete when every selected task and isolated worktree exist.

4. **Supervise by exception.** Wait across the active tasks for one to finish or request attention. Let ordinary progress run without nudges and keep sibling tasks running. Intervene only for evidenced scope drift, a missed process requirement, or a question answerable from repository evidence. Surface product, authority, or user-preference decisions to the user.

   Complete when a task supplies a scoped commit with its validation and review evidence. If a task reports a concrete blocker, verify it and stop with the exact blocker.

5. **Apply the landing gate serially.** Gate only one completed child at a time. Fresh-read the child ticket and parent spec, then refresh the integration head. If the task started from an older head, send the current head to that same task and require it to integrate the new base and rerun affected validation and review. Inspect the resulting commit, ancestry, diff, and worktree status. Account for every child acceptance criterion, confirm the change stays within the parent spec's decisions and out-of-scope boundary without absorbing sibling tickets, and confirm the authoritative `implement` process completed. Treat the task's final report as a claim until repository evidence supports it.

   Send a precise correction to the same task when the gate fails, then wait again. Stop if the same failure remains after that correction.

   Complete when one exact commit is landable with acceptance, validation, review, scope, and ancestry accounted for.

6. **Land, close, and refresh.** Use the repository's required path: push a branch, open a pull request, wait for required checks, and merge where protections require it; use direct integration only where repository guidance allows it. Close the child only after its commit is reachable from the integration branch. Leave an automatically closed issue alone.

   When landing triggers an authorized production deployment, wait for its terminal result before advancing. Continue only after success; stop with exact deployment evidence on failure.

   After confirming the commit is reachable and the completed task's worktree is clean, clean up that task and worktree. Refresh the integration head and the entire live frontier, then return to step 2 to fill available capacity before supervising the active tasks again. Start every new task from the refreshed head.

## Finish

Stop when all children are landed, no child is ready or active, user input is required, the same failure survives one correction, or the landing path is blocked. Report each child's exact task, commit, branch, pushed, pull-request, merged, closed, deployed, and remaining-frontier state. Leave the parent spec ready for human acceptance.
