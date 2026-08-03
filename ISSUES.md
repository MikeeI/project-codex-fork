# Issue Publication Status

This file is the sole source of truth for every finding's ID, lifecycle status, and published location.
Read and update status here instead of inferring it from chat history or earlier search output.

Next finding ID: ISSUE-2026-015

## MCP Server Issues

### ISSUE-2026-001 — mcp-server: TurnAborted does not finish the active tool request

- Codex CLI 0.146.0 emitted `turn_aborted` after cancellation but did not resolve request 3 after 30 seconds.
- Current upstream forwards then ignores `TurnAborted`; request 4 still completed, proving the server remained responsive.
- Open issue [#20925](https://github.com/openai/codex/issues/20925) owns the same root cause and observed contract.

Status: Hold — source-diagnosis comment on issue #20925 is not yet drafted or approved.
Location: Not published.

## Core Issues

### ISSUE-2026-002 — core: model requests rebuild the HTTP connection pool

- Revalidated 2026-08-03 at upstream commit `5157493`: [`build_api_transport`](https://github.com/openai/codex/blob/5157493c23713ac12034cf250ffb0a8ce0670277/codex-rs/core/src/client.rs#L978-L990) still creates the reqwest-backed transport inside the HTTP attempt loop.
- Open issue [#29369](https://github.com/openai/codex/issues/29369) owns the exact connection-pool root cause; [#36210](https://github.com/openai/codex/issues/36210) explicitly treats it as separate prior work.
- PR [#34447](https://github.com/openai/codex/pull/34447) added route-aware pooling elsewhere, but no distinct measurement or evidence was found; react to #29369 only.

Research: Open and closed issue, pull-request, discussion, and release searches were completed 2026-08-03 through release [0.146.0](https://github.com/openai/codex/releases/tag/rust-v0.146.0). Issues #29369 and #36210 and PR #34447 were read; #29369 is the sole matching target.

Status: Duplicate — issue #29369 owns the exact root cause; an optional reaction is not yet approved.
Location: Existing upstream issue: https://github.com/openai/codex/issues/29369.

### ISSUE-2026-003 — core: request construction deeply copies conversation history twice

- Revalidated 2026-08-03 at upstream commit `5157493`: [`for_prompt`](https://github.com/openai/codex/blob/5157493c23713ac12034cf250ffb0a8ce0670277/codex-rs/core/src/context_manager/history.rs#L144-L146) materializes normalized history and [`get_formatted_input_for_request`](https://github.com/openai/codex/blob/5157493c23713ac12034cf250ffb0a8ce0670277/codex-rs/core/src/client_common.rs#L52-L60) immediately clones it.
- Merged PRs [#28306](https://github.com/openai/codex/pull/28306), [#28313](https://github.com/openai/codex/pull/28313), and [#34825](https://github.com/openai/codex/pull/34825) removed adjacent request-sized clones but not this two-stage copy.
- No issue, pull request, discussion, or release note owns the remaining copy; long histories make the cost realistic, but allocation volume and request-build latency are not measured.

Research: Open and closed issue, pull-request, discussion, and release searches were completed 2026-08-03 through release [0.146.0](https://github.com/openai/codex/releases/tag/rust-v0.146.0). Issue #13733 and PRs #28306, #28313, and #34825 were read as adjacent work; none owns the local two-stage copy.

Status: Published — opened upstream issue #36643 on 2026-08-03.
Location: https://github.com/openai/codex/issues/36643

### ISSUE-2026-004 — codex-mcp: tool catalog derivations repeat for every sampling step

- Revalidated 2026-08-03 at upstream commit `5157493`: [`capture_binding_with_metadata`](https://github.com/openai/codex/blob/5157493c23713ac12034cf250ffb0a8ce0670277/codex-rs/codex-mcp/src/connection_manager/tool_catalog.rs#L145-L293) rebuilds normalized tools, client maps, and prepared-call maps for each sampling request.
- PR [#34588](https://github.com/openai/codex/pull/34588) deliberately introduced request-bound catalog revisions for correctness; [#35777](https://github.com/openai/codex/pull/35777) already parallelized catalog resolution.
- Searches found no exact report, but the broad finding is not actionable until revision-stable schema work is separated and its realistic cost is proven.

Research: Open and closed issue, pull-request, discussion, and release searches were completed 2026-08-03 through release [0.146.0](https://github.com/openai/codex/releases/tag/rust-v0.146.0). PRs #34588 and #35777 were read; related MCP startup and catalog results had different root causes.

Status: Retired — the broad claim conflicts with deliberate request-bound catalog design in PR #34588; any stable-schema concern requires a new, separately proven finding.
Location: Not published.

## TUI Issues

### ISSUE-2026-005 — tui: transcript measurement and painting rebuild the same layout

- Revalidated 2026-08-03 at upstream commit `5157493`: [`TranscriptAreaRenderable`](https://github.com/openai/codex/blob/5157493c23713ac12034cf250ffb0a8ce0670277/codex-rs/tui/src/chatwidget/rendering.rs#L71-L91) still materializes and measures transcript lines separately in `desired_height` and `render`.
- Open issue [#21945](https://github.com/openai/codex/issues/21945) owns this root cause and now contains current profiling; PR [#34348](https://github.com/openai/codex/pull/34348) cached repeated height queries but not the separate paint pass.
- The existing thread already states that measurement and rendering derive lines independently, so no distinct comment evidence remains; react to #21945 only.

Research: Open and closed issue, pull-request, discussion, and release searches were completed 2026-08-03 through release [0.146.0](https://github.com/openai/codex/releases/tag/rust-v0.146.0). The complete #21945 thread and PR #34348 were read; they already contain the useful diagnosis and current sample.

Status: Duplicate — issue #21945 owns the exact root cause and already contains current profiling; an optional reaction is not yet approved.
Location: Existing upstream issue: https://github.com/openai/codex/issues/21945.

### ISSUE-2026-006 — tui: history grouping fully renders cells only to test visibility

- `add_boxed_history` calls `display_lines(u16::MAX)` and keeps only whether the result is empty.
- Expensive cells such as patch summaries can repeat scanning, sorting, highlighting, and wrapping later.
- The discarded render is source-proven; affected cell frequency and UI latency are not measured.

Status: Hold — upstream research and cell-level measurement pending.
Location: Not published.

### ISSUE-2026-007 — tui: Markdown table rendering deeply clones owned cells and lines

- `render_table_lines` owns `TableState` but clones each row's cells before wrapping them.
- The row renderer then clones selected wrapped hyperlink lines before producing the final output.
- Both clone stages are source-proven; allocation impact for realistic streamed tables is not measured.

Status: Hold — upstream research and table-allocation measurement pending.
Location: Not published.

## App Server Issues

### ISSUE-2026-008 — app-server: core events are deeply cloned before bespoke handling

- The thread listener passes `event.clone()` at the event value's final use.
- The bespoke event handler accepts ownership and immediately consumes or destructures the event.
- The avoidable clone is source-proven; event-size distribution and throughput impact are not measured.

Status: Hold — upstream research and event-allocation measurement pending.
Location: Not published.

### ISSUE-2026-009 — app-server: targeted notifications clone the final subscriber payload

- Targeted fan-out clones one owned `OutgoingMessage` for every selected connection.
- The original message is discarded, so even the single-subscriber case pays one full payload clone.
- The clone is source-proven; payload-size and notification-frequency impact are not measured.

Status: Hold — upstream research and notification-allocation measurement pending.
Location: Not published.

### ISSUE-2026-010 — app-server: every core event rebuilds the subscriber list

- The event listener locks the global thread-state manager and allocates a new connection-ID vector.
- Subscriber membership usually changes less frequently than thread events are emitted.
- The per-event lookup is source-proven; lock contention and allocation impact are not measured.

Status: Hold — upstream research and lock profiling pending.
Location: Not published.

## TypeScript SDK Issues

### ISSUE-2026-011 — sdk: abandoned streamed turns do not await child-process closure

- Early generator termination sends a signal but does not await the spawned Codex process's `close` event.
- Pipe and stderr listeners can remain active until the child exits independently.
- The lifecycle gap is source-proven; a surviving child process has not yet been reproduced.

Status: Hold — upstream research and child-lifecycle reproduction pending.
Location: Not published.

## Exec Server Issues

### ISSUE-2026-012 — exec-server: event history and broadcast deeply copy output payloads

- Revalidated 2026-08-03 at upstream commit `5157493`: [`publish` and `subscribe`](https://github.com/openai/codex/blob/5157493c23713ac12034cf250ffb0a8ce0670277/codex-rs/exec-server/src/process.rs#L121-L151) deeply clone `Vec<u8>` output into history and retained replay, with the latter clone under the history lock.
- PR [#30273](https://github.com/openai/codex/pull/30273) made pushed events the normal remote-exec path; [#31576](https://github.com/openai/codex/pull/31576) bounded retained events and bytes without removing these copies.
- No issue, pull request, discussion, or release note owns the clone path; every output event makes the cost realistic, but allocation and retained-memory impact are not measured.

Research: Open and closed issue, pull-request, discussion, and release searches were completed 2026-08-03 through release [0.146.0](https://github.com/openai/codex/releases/tag/rust-v0.146.0). PRs #30273 and #31576 were read as the owning design history; no external thread owns the clone concern.

Status: Published — opened upstream issue #36644 on 2026-08-03.
Location: https://github.com/openai/codex/issues/36644

### ISSUE-2026-013 — exec-server: successful exits aggregate output for a false classification

- Successful sandboxed exits aggregate retained stdout, stderr, and combined output into owned strings.
- The sandbox-denial classifier returns false immediately when the exit code is zero.
- The avoidable work is source-proven and bounded; process-exit latency is not measured.

Status: Hold — upstream research and exit-path measurement pending.
Location: Not published.

## Exec CLI Issues

### ISSUE-2026-014 — exec: resume lookup reads candidate rollout files serially

- Revalidated 2026-08-03 at upstream commit `5157493`: [`latest_thread_cwd` and resume lookup](https://github.com/openai/codex/blob/5157493c23713ac12034cf250ffb0a8ce0670277/codex-rs/exec/src/lib.rs#L1430-L1500) read each candidate rollout fully and serially, including one needless read before the `--all` short-circuit.
- Open issue [#22411](https://github.com/openai/codex/issues/22411) owns the same rollout-scan family and reports 39–46 second default `thread/list` calls versus 0.173 seconds with state-DB-only listing.
- A comment on #22411 should add this separate exec-side second pass; closed PR [#16338](https://github.com/openai/codex/pull/16338) confirms latest-CWD depends on rollout contents, while this pass's incremental latency remains unmeasured.

Research: Open and closed issue, pull-request, discussion, and release searches were completed 2026-08-03 through release [0.146.0](https://github.com/openai/codex/releases/tag/rust-v0.146.0). Issues #22411, #16158, #21211, and #24510 and PR #16338 were read; #22411 is the only direct comment target.

Status: Published — posted source-analysis evidence to upstream issue #22411 on 2026-08-03.
Location: https://github.com/openai/codex/issues/22411#issuecomment-5161236975
