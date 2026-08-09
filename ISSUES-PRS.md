# Issue and Pull Request Publication Status

This file is the sole source of truth for every finding's ID, delivery mode, lifecycle status, evidence, and location.
Read and update this ledger instead of inferring state from chat history, clone reports, or earlier reviews.
`FORMAT.md` owns research, drafting, implementation authorization, approval, and publication rules.

Next finding ID: ISSUE-2026-009

### ISSUE-2026-001 — ipnlocal: Extension shutdown skips its drain window

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; user impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Lifecycle.
- Publication target: Undecided.
- Summary: `ExtensionHost.shutdownWorkQueue` closes its queue before waiting.
  The production `5s` drain is effectively skipped.
- Evidence: `ipn/ipnlocal/extension_host.go:579-598` calls `Shutdown` and then `Wait`.
  `util/execqueue/execqueue.go:142-143` makes `Wait` return immediately after shutdown.
  `util/execqueue/execqueue.go:91-118` already owns the required `ShutdownAndWait` contract.
- Shared change pressure: Not a DRY finding; one lifecycle owner currently composes two incompatible queue operations.
- Impact: Source proves that in-flight extension work is not drained before extension callbacks and engine shutdown.
  Collision frequency and production impact are not measured.
- Proposed direction: Replace the split queue calls with `ShutdownAndWait(ctx)` and update the existing test queue adapter.
- Risks and boundaries: Preserve the `5s` production deadline and unlimited test context.
  Do not wait while holding a lock required by the in-flight operation.
- Verification: Run `TestShutdownAndWait`, `TestShutdownAndWaitTimeout`, and focused ExtensionHost tests.
- Missing publication evidence: Verify current `upstream/main` and search prior issues and pull requests.
  Reproduce an in-flight operation during `LocalBackend.Shutdown`.

### ISSUE-2026-002 — serve: Human status omits active foreground listeners

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; user impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Output.
- Publication target: Undecided.
- Summary: Human-readable `tailscale serve status` ignores `ServeConfig.Foreground`.
  It can report `No serve config` while a foreground listener is active.
- Evidence: `cmd/tailscale/cli/serve_status.go:20-62` renders only top-level TCP, Web, Services, and Funnel state.
  `cmd/tailscale/cli/serve_legacy.go:616-617` records the foreground-status gap explicitly.
  `ipn/serve.go:70-76` defines foreground entries as the normal non-`--bg` serve path.
- Shared change pressure: Not a DRY finding; the human renderer omits state already owned by `ServeConfig`.
- Impact: Source proves that the human and JSON status surfaces can disagree for foreground-only state.
  Actual operator frequency is not measured.
- Proposed direction: Render non-empty foreground configs as separate ephemeral snapshots and include them in emptiness.
- Risks and boundaries: Keep background and foreground state visibly distinct.
  Do not persist, merge, recurse into, or assign stable meaning to session IDs.
- Verification: Replay foreground-only, mixed foreground/background, and ended-session status.
  Confirm that `--json` remains unchanged.
- Missing publication evidence: Verify current `upstream/main` and reproduce the CLI output.
  Search existing Serve issues, pull requests, and design discussions.

### ISSUE-2026-003 — cli: Accidental-up error presents reset as an equivalent retry

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; user impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Output.
- Publication target: Undecided.
- Summary: The accidental-setting-revert error presents `--reset` beside the state-preserving command.
  It does not state that unspecified settings return to defaults.
- Evidence: `cmd/tailscale/cli/up.go:978-982` presents both retry paths as equivalent alternatives.
  The `--reset` help at `cmd/tailscale/cli/up.go:143` states that unspecified settings reset to defaults.
- Shared change pressure: Not a DRY finding; one error surface gives incomplete guidance about an existing flag contract.
- Impact: Source proves that the shortest suggested retry has different state consequences from the generated command.
  User selection frequency and resulting configuration changes are not measured.
- Proposed direction: Present the generated state-preserving command first and label `--reset` as the resetting alternative.
- Risks and boundaries: Preserve generated arguments, ordering, quoting, preference calculation, and reset behavior.
  Exact error-text consumers may observe the revised wording.
- Verification: Exercise the accidental-revert path with multiple non-default preferences and one requested change.
  Confirm that the generated command remains byte-for-byte equivalent apart from surrounding guidance.
- Missing publication evidence: Verify current `upstream/main` and reproduce the message.
  Search prior CLI guidance issues, pull requests, and documentation.

### ISSUE-2026-004 — netcheck: Output format is validated after network work

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; user impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Output and validation.
- Publication target: Undecided.
- Summary: `tailscale netcheck` owns a local format string and validates it after network work.
  `--format=json --every` emits neither one JSON document nor JSON Lines.
- Evidence: `cmd/tailscale/cli/netcheck.go:52-68` defines the local format grammar.
  Validation occurs in `printNetCheckReport` at `cmd/tailscale/cli/netcheck.go:158-176`.
  `cmd/tailscale/jsonoutput/format.go:14-23` names Netcheck as a consumer of the shared format owner.
- Shared change pressure: Netcheck and Routecheck expose the same format decision.
  Only Routecheck uses `jsonoutput.Format`.
- Impact: Source proves delayed validation and an ambiguous repeated-JSON contract.
  Automation breakage, latency, and usage frequency are not measured.
- Proposed direction: Use `jsonoutput.Format`, add its `--json` form, and validate the Netcheck format matrix before setup.
- Risks and boundaries: Preserve human output, one-shot JSON, JSON Lines, and the unstable-schema warning.
  Reject `json` with `--every` explicitly rather than silently changing formats.
- Verification: Check unknown format, `--json`, one-shot `json`, rejected `json --every`, and `json-line --every`.
  Confirm invalid combinations perform no Netcheck or DERP network work.
- Missing publication evidence: Verify current `upstream/main` and search prior format-contract work.
  Check documented compatibility expectations for repeated JSON output.

### ISSUE-2026-005 — wait: Timeouts do not identify the blocked readiness phase

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; user impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Error and output.
- Publication target: Undecided.
- Summary: `tailscale wait --timeout` returns the same context deadline error for LocalAPI, backend/IP, and interface waits.
- Evidence: `cmd/tailscale/cli/wait.go:75-85`, `87-109`, and `111-129` implement three distinct readiness phases.
  Each deadline path returns the underlying context or watcher error without phase information.
- Shared change pressure: Not a DRY finding; one command owns three phases but loses their identity at its error boundary.
- Impact: Source proves that a timeout cannot identify which readiness condition remained false.
  Diagnostic time and operator frequency are not measured.
- Proposed direction: Track the active phase locally and wrap only `context.DeadlineExceeded` with that phase.
- Risks and boundaries: Preserve `errors.Is`, exit status, non-timeout errors, polling, backoff, and silent success.
  Exact error-text consumers may observe a prefix.
- Verification: Delay each phase independently and check its timeout message.
  Confirm success remains silent and non-deadline errors remain unchanged.
- Missing publication evidence: Verify current `upstream/main` and reproduce all three timeout phases.
  Search existing Wait command issues and pull requests.

### ISSUE-2026-006 — wait: Blocking login states remain silent

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; operator impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Output and lifecycle.
- Publication target: Undecided.
- Summary: `tailscale wait` receives `NeedsLogin`, `NeedsMachineAuth`, and `BrowseToURL`.
  It reacts only to `Running`, leaving an indefinite wait without an actionable signal.
- Evidence: `cmd/tailscale/cli/wait.go:87-109` consumes IPN notifications and checks only `ipn.Running`.
  `cmd/tailscaled/tailscale-wait-online.service:7-9` invokes the command as a recurring systemd oneshot.
- Shared change pressure: Not a DRY finding; the watcher already owns the state but drops its actionable meaning.
- Impact: Source proves that these blocking states produce no command signal while the wait continues.
  Operator confusion and journal-inspection frequency are not measured.
- Proposed direction: Emit one deduplicated stderr message for `NeedsLogin` and `NeedsMachineAuth`, then continue waiting.
- Risks and boundaries: Preserve indefinite waiting, success behavior, and exit codes.
  Dedupe repeated states and changed login URLs to avoid journal noise.
- Verification: Replay initial and repeated blocking states, a changed `BrowseToURL`, and a later `Running` notification.
  Confirm one signal per actionable transition and unchanged eventual success.
- Missing publication evidence: Verify current `upstream/main` and reproduce interactive and systemd behavior.
  Search prior discussion of Wait output and authentication states.

### ISSUE-2026-007 — sessionrecording: Stale ACKs reset the progress deadline

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Protocol and lifecycle.
- Publication target: Undecided.
- Summary: Recorder V2 resets its `30s` timer for every ACK frame.
  It does so even when the acknowledged offset does not advance while upload bytes remain outstanding.
- Evidence: `sessionrecording/connect.go:324-353` discards the ACK value and resets the timer unconditionally.
  Lines 338-340 require idle ACKs for liveness, while lines 350-352 identify unbounded ACK lag as unresolved.
- Shared change pressure: Not a DRY finding; one timer currently conflates idle connection liveness and upload progress.
- Impact: Source proves that repeated stale ACKs can suppress the no-progress timeout.
  Recorder behavior and lost or delayed recording impact are not measured.
- Proposed direction: Keep idle ACKs as heartbeats, but require a higher ACK offset while bytes are outstanding.
  Start a full progress window when upload activity creates a backlog.
- Risks and boundaries: Preserve idle broken-connection detection and tolerate delayed or repeated ACK frames.
  Do not treat a lower ACK as progress or time out a newly created backlog using an older idle deadline.
- Verification: Extend `TestConnectToRecorder` for stale ACKs, advancing ACKs, idle heartbeats, and new backlog timing.
  Retain the existing no-ACK case.
- Missing publication evidence: Verify current `upstream/main` and inspect recorder protocol history.
  Reproduce with an H2C fixture and search issues and pull requests for ACK-lag handling.

### ISSUE-2026-008 — cli: Taildrop debug download discards stream failures

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; user impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Error and lifecycle.
- Publication target: Undecided.
- Summary: `tailscale debug --file` neither closes the `GetWaitingFile` response body nor returns the `io.Copy` error.
- Evidence: `cmd/tailscale/cli/debug.go:495-501` discards the copy result and returns success.
  `cmd/tailscale/cli/file.go:688-707` shows the owned stream contract for the normal file-receive path.
- Shared change pressure: Not a DRY finding; one consumer violates the ownership and error contract used by another.
- Impact: Source proves that partial output can return success and that the response body is not explicitly closed.
  Failure frequency and resource impact are not measured.
- Proposed direction: Defer `rc.Close()` after acquisition and return the `io.Copy(Stdout, rc)` error.
- Risks and boundaries: Preserve the LocalAPI request, output bytes, and successful exit behavior.
  Previously swallowed `EPIPE` and source-stream failures will become non-zero exits.
- Verification: Exercise a stream that returns a prefix and then `io.ErrUnexpectedEOF`, plus a failing stdout writer.
  Confirm the error is returned and the body closes exactly once.
- Missing publication evidence: Verify current `upstream/main` and reproduce a partial transfer.
  Search prior Taildrop debug issues and pull requests.
