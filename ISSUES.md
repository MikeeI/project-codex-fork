# Issue Publication Status

This file is the sole source of truth for every finding's ID, lifecycle status, and published location.
Read and update status here instead of inferring it from chat history or earlier search output.

Next finding ID: ISSUE-2026-015

## MCP Server Issues

### ISSUE-2026-001 — mcp-server: TurnAborted does not finish the active tool request

- `codex_tool_runner.rs` treats `TurnComplete` as terminal but groups `TurnAborted` with ignored events.
- The runner can keep waiting while its request-to-turn registry entries and thread references remain live.
- The control flow is source-proven; duplicate research and a runtime reproduction are still required.

Status: Hold — upstream research and runtime reproduction pending.
Location: Not published.

## Core Issues

### ISSUE-2026-002 — core: model requests rebuild the HTTP connection pool

- `build_api_transport` constructs a new reqwest-backed client inside the HTTP request-attempt loop.
- Consecutive model requests cannot reuse that client's TCP, TLS, HTTP/2, or proxy connections.
- Client reconstruction is source-proven; connection count and warm-request latency are not measured.

Status: Hold — upstream research and connection-reuse measurement pending.
Location: Not published.

### ISSUE-2026-003 — core: request construction deeply copies conversation history twice

- Prompt normalization materializes an owned `Vec<ResponseItem>` from shared session history.
- Request construction immediately clones the full vector again for each model step and retry.
- Both copies are source-proven; allocation volume and request-build latency are not measured.

Status: Hold — upstream research and allocation measurement pending.
Location: Not published.

### ISSUE-2026-004 — codex-mcp: tool catalog derivations repeat for every sampling step

- Step-context capture rebuilds normalized tools, client maps, and prepared-call maps from the current catalog.
- The same immutable schema work repeats across tool follow-ups until readiness or catalog revision changes.
- The repeated derivation is source-proven; CPU and allocation impact for realistic catalogs are not measured.

Status: Hold — upstream research and catalog-size measurement pending.
Location: Not published.

## TUI Issues

### ISSUE-2026-005 — tui: transcript measurement and painting rebuild the same layout

- The default height path materializes display lines and computes wrapped row counts before painting.
- Painting the same frame materializes the lines and wrapped row counts again at the same width.
- The duplicate default-path work is source-proven; frame-time impact for large transcripts is not measured.

Status: Hold — upstream research and frame profiling pending.
Location: Not published.

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

- Publishing clones each owned output event into history before moving the original into broadcast.
- A new subscriber can deeply clone the retained replay again while holding the history lock.
- Both clone paths are source-proven; allocation and retained-memory impact are not measured.

Status: Hold — upstream research and output-allocation measurement pending.
Location: Not published.

### ISSUE-2026-013 — exec-server: successful exits aggregate output for a false classification

- Successful sandboxed exits aggregate retained stdout, stderr, and combined output into owned strings.
- The sandbox-denial classifier returns false immediately when the exit code is zero.
- The avoidable work is source-proven and bounded; process-exit latency is not measured.

Status: Hold — upstream research and exit-path measurement pending.
Location: Not published.

## Exec CLI Issues

### ISSUE-2026-014 — exec: resume lookup reads candidate rollout files serially

- Resume lookup can read complete rollout files one after another to recover each candidate's latest working directory.
- The `--all` path can perform the same read before accepting the first ordered candidate without a CWD filter.
- The serial reads are source-proven; realistic rollout sizes and resume latency are not measured.

Status: Hold — upstream research and resume-latency reproduction pending.
Location: Not published.
