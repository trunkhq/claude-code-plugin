---
name: propose
description: Create or revise a Trunk Change with a patch, intent, specification, and observed test evidence. Use when the user asks to submit work through Trunk.
---

Use the connected Trunk MCP tools and their live input schemas. Tool names below
are unprefixed; discover the corresponding tools on this plugin's Trunk server.
If disconnected, direct the user to `/mcp` to authenticate. Never ask for secrets
in chat or extract the connection's OAuth token.

Call `whoami` before proposing, then resolve the target with `list_repos` and
`get_repo`. Respect repository and path scopes. A missing tool or denied scope
is not permission to use human credentials or a different transport.

Establish whether this is new work or a revision of an existing Change. For a
revision, read `get_change` first and retain its exact ID, observed revision,
base, and proposal content. Do not create a second Change to evade a conflict.

Read the relevant source and repository instructions before editing. For a local
checkout, inspect status and verify repository and base identity; preserve
unrelated staged, unstaged, and untracked work. Build a complete unified Git diff
containing only the intended changes, including intended new files. Do not
assume `git diff` alone includes staged changes or untracked files.

Run appropriate checks when a usable execution environment exists. Report only
observed results, including failures and checks not run. Remote source reading
does not establish that tests passed. Supply a meaningful title and intent, the
specification worked to, and a concise session trace without credentials.

When targeting the current Trunk head, use `preflight_patch` if available to
check the textual diff. Submit the exact checked diff with its returned
`base_commit`; preflight does not reserve the head or authorize submission.
Unsupported patches or incomplete source evidence are not successful checks.
For work based on another recorded commit, preserve that exact base instead of
silently relabeling it as the current head. Reconcile source changes if rebasing.

For new work, call `propose_change` with an explicit `base_commit`. Trunk manages
backing refs; do not pass `branch` or push directly to trunk.

For existing work, call `revise_change` with `change_id`, the observed
`expected_revision`, and the complete replacement proposal. Resend all spec,
reported results, and trace content intended to survive: this is not a partial
update. Omitted `base_commit` retains the existing base; supply a new one only
when intentionally rebasing. After a conflict, reread and reconcile rather than
incrementing the revision blindly. After an ambiguous write failure, read back
the Change or recent Changes before retrying; do not blindly duplicate writes.

Read back the result with `get_change`. If the user's request includes releasing
the work for normal landing, `mark_change_ready` can release the author's exact
current Draft revision. Preserve Draft when the user asks for a draft or review
before release. Ready is not proof of landing: policy, approvals, and required
checks still apply. Agents cannot directly land or record a human approval.

Report the Change ID, revision, actual lifecycle state, observed checks, and
remaining blockers. Use returned human-facing links when available. Never claim
landed status without readback evidence.
