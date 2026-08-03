# Issue Publication Status

This file is the sole source of truth for every finding's ID, lifecycle status, and published location.
Read and update status here instead of inferring it from chat history or earlier search output.

Next finding ID: ISSUE-2026-015

## MCP Server Issues

### ISSUE-2026-001 — mcp-server: TurnAborted does not finish the active tool request

- Codex CLI 0.146.0 emitted `turn_aborted` after cancellation, but request 3 remained unresolved after 30 seconds.
- Revalidated 2026-08-03 at upstream commit `bb5054f`: [`TurnComplete`](https://github.com/openai/codex/blob/bb5054fe47abe73ecbbd454751066a28c89f4bb9/codex-rs/mcp-server/src/codex_tool_runner.rs#L297-L310) finishes the request, while [`TurnAborted`](https://github.com/openai/codex/blob/bb5054fe47abe73ecbbd454751066a28c89f4bb9/codex-rs/mcp-server/src/codex_tool_runner.rs#L324-L392) remains in the complete ignored arm.
- Open issue [#20925](https://github.com/openai/codex/issues/20925) owns the exact contract; the latest-version reproduction and pinned diagnosis are materially new comment evidence.

Research: Issue, pull-request, discussion, and release searches were completed 2026-08-03 through release [0.146.0](https://github.com/openai/codex/releases/tag/rust-v0.146.0). Issue #20925 remains the exact open target; issues #20194, #14115, and #25061 have different request paths, and historical PR #2365 introduced the still-ignored `TurnAborted` arm.

Status: Published — posted source-diagnosis evidence to upstream issue #20925 on 2026-08-03.
Location: https://github.com/openai/codex/issues/20925#issuecomment-5161685679

## Core Issues

### ISSUE-2026-002 — core: model requests rebuild the HTTP connection pool

- Revalidated 2026-08-03 at upstream commit `5157493`: [`build_api_transport`](https://github.com/openai/codex/blob/5157493c23713ac12034cf250ffb0a8ce0670277/codex-rs/core/src/client.rs#L978-L990) still creates the reqwest-backed transport inside the HTTP attempt loop.
- Open issue [#29369](https://github.com/openai/codex/issues/29369) owns the exact connection-pool root cause; [#36210](https://github.com/openai/codex/issues/36210) explicitly treats it as separate prior work.
- PR [#34447](https://github.com/openai/codex/pull/34447) added route-aware pooling elsewhere, but no distinct measurement or evidence was found; react to #29369 only.

Research: Open and closed issue, pull-request, discussion, and release searches were completed 2026-08-03 through release [0.146.0](https://github.com/openai/codex/releases/tag/rust-v0.146.0). Issues #29369 and #36210 and PR #34447 were read; #29369 is the sole matching target.

Status: Duplicate — issue #29369 owns the exact root cause; thumbs-up reaction added 2026-08-03.
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

Status: Duplicate — issue #21945 owns the exact root cause and current profiling; thumbs-up reaction added 2026-08-03.
Location: Existing upstream issue: https://github.com/openai/codex/issues/21945.

### ISSUE-2026-006 — tui: history grouping fully renders cells only to test visibility

- Revalidated 2026-08-03 at upstream commit `bb5054f`: [`add_boxed_history`](https://github.com/openai/codex/blob/bb5054fe47abe73ecbbd454751066a28c89f4bb9/codex-rs/tui/src/chatwidget.rs#L1228-L1250) materializes `display_lines(u16::MAX)` when `keep_placeholder_header_active` is false and keeps only whether the vector is empty.
- [`HistoryCell::display_lines`](https://github.com/openai/codex/blob/bb5054fe47abe73ecbbd454751066a28c89f4bb9/codex-rs/tui/src/history_cell/mod.rs#L196-L198) returns a complete line vector, so cost and allocation can scale with cell content before the same cell is inserted.
- No issue or pull request owns the visibility probe; its reachability and avoidable work make it a valid source-only performance concern, but UI latency is not measured.

Research: Issue, pull-request, discussion, and release searches were completed 2026-08-03 through release 0.146.0. Issue #21945 and PRs #34194, #34206, and #34232 concern different transcript rendering or ownership paths; no exact target was found.

Status: Published — opened upstream issue #36652 on 2026-08-03.
Location: https://github.com/openai/codex/issues/36652

### ISSUE-2026-007 — tui: Markdown table rendering deeply clones owned cells and lines

- Revalidated 2026-08-03 at upstream commit `bb5054f`: [`render_table_lines`](https://github.com/openai/codex/blob/bb5054fe47abe73ecbbd454751066a28c89f4bb9/codex-rs/tui/src/markdown_render.rs#L1085-L1108) owns `TableState` but deeply clones every retained body row.
- [`render_table_row`](https://github.com/openai/codex/blob/bb5054fe47abe73ecbbd454751066a28c89f4bb9/codex-rs/tui/src/markdown_render.rs#L1463-L1541) then clones selected wrapped lines and hyperlinks; streaming updates and width reflow repeat table rendering.
- No issue or pull request owns either clone stage; cost scales with table content and render count, making this a valid source-only concern, but allocation volume is not measured.

Research: Issue, pull-request, discussion, and release searches were completed 2026-08-03 through release 0.146.0. Table-correctness issues had different roots; PRs #22052 and #34045 establish streaming and reflow reachability but do not own the clone stages.

Status: Published — opened upstream issue #36653 on 2026-08-03.
Location: https://github.com/openai/codex/issues/36653

## App Server Issues

### ISSUE-2026-008 — app-server: core events are deeply cloned before bespoke handling

- Revalidated 2026-08-03 at upstream commit `bb5054f`: the [thread listener](https://github.com/openai/codex/blob/bb5054fe47abe73ecbbd454751066a28c89f4bb9/codex-rs/app-server/src/request_processors/thread_lifecycle.rs#L309-L354) passes `event.clone()` at the value's final use.
- The owned bespoke handler consumes or destructures the event, so moving it preserves behavior and removes one deep clone from every forwarded core event, including high-frequency deltas and large payload events.
- No issue or pull request owns this clone; event frequency and the one-token ownership fix make it a valid source-only concern, but allocation throughput is not measured.

Research: Issue, pull-request, discussion, and release searches were completed 2026-08-03 through release 0.146.0. Closed, unmerged PR #29545 included the ownership move inside a broader throughput patch; current source retains the clone, and no active target owns it.

Status: Published — opened upstream issue #36654 on 2026-08-03.
Location: https://github.com/openai/codex/issues/36654

### ISSUE-2026-009 — app-server: targeted notifications clone the final subscriber payload

- Revalidated 2026-08-03 at upstream commit `bb5054f`: [targeted notification fan-out](https://github.com/openai/codex/blob/bb5054fe47abe73ecbbd454751066a28c89f4bb9/codex-rs/app-server/src/outgoing_message.rs#L585-L620) clones the owned `OutgoingMessage` for every selected connection.
- The original is discarded, so even one subscriber pays a deep payload clone; moving it to the final connection would preserve order and reduce cost to at most `subscriber_count - 1` clones.
- No issue or pull request owns this fan-out detail; it affects every targeted thread notification and scales with payload size, but allocation volume is not measured.

Research: Issue, pull-request, discussion, and release searches were completed 2026-08-03 through release 0.146.0. PR #32905 introduced timestamped fan-out without changing clone ownership; broader app-server resource issues had different roots, and no exact target was found.

Status: Published — opened upstream issue #36655 on 2026-08-03.
Location: https://github.com/openai/codex/issues/36655

### ISSUE-2026-010 — app-server: every core event rebuilds the subscriber list

- Revalidated 2026-08-03 at upstream commit `bb5054f`: every core event [requests a subscriber snapshot](https://github.com/openai/codex/blob/bb5054fe47abe73ecbbd454751066a28c89f4bb9/codex-rs/app-server/src/request_processors/thread_lifecycle.rs#L309-L340).
- [`subscribed_connection_ids`](https://github.com/openai/codex/blob/bb5054fe47abe73ecbbd454751066a28c89f4bb9/codex-rs/app-server/src/thread_state.rs#L383-L390) locks the global manager and collects the thread's `HashSet` into a new vector despite subscriber membership changing far less often.
- No issue or pull request owns this lookup; event frequency and cross-thread lock sharing make contention and allocation realistic, but neither is measured.

Research: Issue, pull-request, discussion, and release searches were completed 2026-08-03 through release 0.146.0. Issues #35676 and #35750 concern presence and unload semantics; closed, unmerged PR #29545 contained a snapshot approach, but no active target owns the per-event rebuild.

Status: Published — opened upstream issue #36656 on 2026-08-03.
Location: https://github.com/openai/codex/issues/36656

## TypeScript SDK Issues

### ISSUE-2026-011 — sdk: abandoned streamed turns do not await child-process closure

- Revalidated 2026-08-03 at upstream commit `bb5054f`: [`CodexExec.run`](https://github.com/openai/codex/blob/bb5054fe47abe73ecbbd454751066a28c89f4bb9/sdk/typescript/src/exec.ts#L240-L248) removes child listeners, sends `SIGTERM`, and returns without awaiting `exit` or `close`.
- A controlled public `codexPathOverride` reproduction returned from `generator.return()` while a SIGTERM-ignoring child remained alive; harness process-tree cleanup then terminated it.
- No issue or pull request owns the SDK lifecycle gap; issue [#25744](https://github.com/openai/codex/issues/25744) concerns Desktop/MCP helper retention through a different process path.

Research: Issue, pull-request, discussion, and release searches were completed 2026-08-03 through release 0.146.0. Issues #34802 and #25744 concern broader process control and Desktop helpers; PRs #6378 and #8825 cover abort support and normal exit waiting, not explicit iterator closure.

Status: Published — opened upstream issue #36658 on 2026-08-03.
Location: https://github.com/openai/codex/issues/36658

## Exec Server Issues

### ISSUE-2026-012 — exec-server: event history and broadcast deeply copy output payloads

- Revalidated 2026-08-03 at upstream commit `5157493`: [`publish` and `subscribe`](https://github.com/openai/codex/blob/5157493c23713ac12034cf250ffb0a8ce0670277/codex-rs/exec-server/src/process.rs#L121-L151) deeply clone `Vec<u8>` output into history and retained replay, with the latter clone under the history lock.
- PR [#30273](https://github.com/openai/codex/pull/30273) made pushed events the normal remote-exec path; [#31576](https://github.com/openai/codex/pull/31576) bounded retained events and bytes without removing these copies.
- No issue, pull request, discussion, or release note owns the clone path; every output event makes the cost realistic, but allocation and retained-memory impact are not measured.

Research: Open and closed issue, pull-request, discussion, and release searches were completed 2026-08-03 through release [0.146.0](https://github.com/openai/codex/releases/tag/rust-v0.146.0). PRs #30273 and #31576 were read as the owning design history; no external thread owns the clone concern.

Status: Published — opened upstream issue #36644 on 2026-08-03.
Location: https://github.com/openai/codex/issues/36644

### ISSUE-2026-013 — exec-server: successful exits aggregate output for a false classification

- Revalidated 2026-08-03 at upstream commit `bb5054f`: [successful sandboxed exits](https://github.com/openai/codex/blob/bb5054fe47abe73ecbbd454751066a28c89f4bb9/codex-rs/exec-server/src/local_process.rs#L923-L957) rebuild retained stdout, stderr, and combined output as vectors and owned strings.
- [Retention is capped at 1 MiB](https://github.com/openai/codex/blob/bb5054fe47abe73ecbbd454751066a28c89f4bb9/codex-rs/exec-server/src/local_process.rs#L77), so this can allocate and copy roughly 4 MiB while holding the process-map lock before the [classifier returns false on exit code zero](https://github.com/openai/codex/blob/bb5054fe47abe73ecbbd454751066a28c89f4bb9/codex-rs/sandboxing/src/denial.rs#L19-L25).
- No issue or pull request owns this exit path; successful sandboxed commands make the cost realistic and the early gate behavior-preserving, but exit latency is not measured.

Research: Issue, pull-request, discussion, and release searches were completed 2026-08-03 through release 0.146.0. Issues #18711 and #4859 concern classifier correctness; PR #29424 introduced the exit classification path, but no target owns the zero-exit construction work.

Status: Published — opened upstream issue #36657 on 2026-08-03.
Location: https://github.com/openai/codex/issues/36657

## Exec CLI Issues

### ISSUE-2026-014 — exec: resume lookup reads candidate rollout files serially

- Revalidated 2026-08-03 at upstream commit `5157493`: [`latest_thread_cwd` and resume lookup](https://github.com/openai/codex/blob/5157493c23713ac12034cf250ffb0a8ce0670277/codex-rs/exec/src/lib.rs#L1430-L1500) read each candidate rollout fully and serially, including one needless read before the `--all` short-circuit.
- Open issue [#22411](https://github.com/openai/codex/issues/22411) owns the same rollout-scan family and reports 39–46 second default `thread/list` calls versus 0.173 seconds with state-DB-only listing.
- A comment on #22411 should add this separate exec-side second pass; closed PR [#16338](https://github.com/openai/codex/pull/16338) confirms latest-CWD depends on rollout contents, while this pass's incremental latency remains unmeasured.

Research: Open and closed issue, pull-request, discussion, and release searches were completed 2026-08-03 through release [0.146.0](https://github.com/openai/codex/releases/tag/rust-v0.146.0). Issues #22411, #16158, #21211, and #24510 and PR #16338 were read; #22411 is the only direct comment target.

Status: Published — posted source-analysis evidence to upstream issue #22411 on 2026-08-03.
Location: https://github.com/openai/codex/issues/22411#issuecomment-5161236975
