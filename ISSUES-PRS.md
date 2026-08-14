# Issue and Pull Request Publication Status

This file is the sole source of truth for every finding's ID, delivery mode, lifecycle status, evidence, and location.
Read and update this ledger instead of inferring state from chat history, clone reports, or earlier reviews.
`FORMAT.md` owns research, drafting, implementation authorization, approval, and publication rules.

Next finding ID: ISSUE-2026-181

## Prioritization by impact and effort

This view includes findings with `Status: Hold`, `Drafted`, `Implementing`, or `Ready`.
Published, Closed, and Rejected findings remain in the detailed ledger and are omitted here.
Impact mirrors the existing `Internal priority` field.
Effort estimates prospective implementation complexity from the recorded direction, boundaries, and verification.
The grouping does not select a delivery mode or authorize implementation.
Reclassify effort when research changes the recorded scope.

### High impact

#### XS effort

- `ISSUE-2026-003`, `ISSUE-2026-019`, `ISSUE-2026-020`, `ISSUE-2026-023`, `ISSUE-2026-040`, `ISSUE-2026-105`

#### S effort

- `ISSUE-2026-001`, `ISSUE-2026-002`, `ISSUE-2026-004`, `ISSUE-2026-005`, `ISSUE-2026-006`, `ISSUE-2026-015`
- `ISSUE-2026-016`, `ISSUE-2026-017`, `ISSUE-2026-031`, `ISSUE-2026-075`, `ISSUE-2026-076`, `ISSUE-2026-078`
- `ISSUE-2026-083`, `ISSUE-2026-085`, `ISSUE-2026-087`, `ISSUE-2026-090`, `ISSUE-2026-091`, `ISSUE-2026-100`
- `ISSUE-2026-104`, `ISSUE-2026-106`

#### M effort

- `ISSUE-2026-010`, `ISSUE-2026-011`, `ISSUE-2026-018`, `ISSUE-2026-021`, `ISSUE-2026-032`, `ISSUE-2026-035`
- `ISSUE-2026-046`, `ISSUE-2026-048`, `ISSUE-2026-052`, `ISSUE-2026-053`, `ISSUE-2026-055`, `ISSUE-2026-077`
- `ISSUE-2026-079`, `ISSUE-2026-080`, `ISSUE-2026-082`, `ISSUE-2026-084`, `ISSUE-2026-093`, `ISSUE-2026-097`
- `ISSUE-2026-098`, `ISSUE-2026-099`, `ISSUE-2026-102`

#### L effort

- `ISSUE-2026-014`, `ISSUE-2026-036`, `ISSUE-2026-041`, `ISSUE-2026-042`, `ISSUE-2026-060`, `ISSUE-2026-086`
- `ISSUE-2026-089`, `ISSUE-2026-094`, `ISSUE-2026-095`, `ISSUE-2026-096`, `ISSUE-2026-103`

### Medium impact

#### XS effort

- `ISSUE-2026-008`, `ISSUE-2026-029`, `ISSUE-2026-030`, `ISSUE-2026-049`, `ISSUE-2026-050`, `ISSUE-2026-054`
- `ISSUE-2026-113`, `ISSUE-2026-116`, `ISSUE-2026-134`, `ISSUE-2026-146`, `ISSUE-2026-148`, `ISSUE-2026-150`
- `ISSUE-2026-155`, `ISSUE-2026-162`, `ISSUE-2026-165`

#### S effort

- `ISSUE-2026-037`, `ISSUE-2026-038`, `ISSUE-2026-066`, `ISSUE-2026-088`, `ISSUE-2026-110`, `ISSUE-2026-111`
- `ISSUE-2026-112`, `ISSUE-2026-114`, `ISSUE-2026-115`, `ISSUE-2026-118`, `ISSUE-2026-120`, `ISSUE-2026-121`
- `ISSUE-2026-125`, `ISSUE-2026-126`, `ISSUE-2026-127`, `ISSUE-2026-128`, `ISSUE-2026-131`, `ISSUE-2026-136`
- `ISSUE-2026-137`, `ISSUE-2026-138`, `ISSUE-2026-140`, `ISSUE-2026-143`, `ISSUE-2026-147`, `ISSUE-2026-149`
- `ISSUE-2026-151`, `ISSUE-2026-152`, `ISSUE-2026-154`, `ISSUE-2026-156`, `ISSUE-2026-161`, `ISSUE-2026-163`
- `ISSUE-2026-166`, `ISSUE-2026-167`, `ISSUE-2026-169`, `ISSUE-2026-172`

#### M effort

- `ISSUE-2026-007`, `ISSUE-2026-024`, `ISSUE-2026-039`, `ISSUE-2026-043`, `ISSUE-2026-044`, `ISSUE-2026-045`
- `ISSUE-2026-047`, `ISSUE-2026-051`, `ISSUE-2026-063`, `ISSUE-2026-067`, `ISSUE-2026-068`, `ISSUE-2026-069`
- `ISSUE-2026-081`, `ISSUE-2026-092`, `ISSUE-2026-107`, `ISSUE-2026-108`, `ISSUE-2026-109`, `ISSUE-2026-122`
- `ISSUE-2026-124`, `ISSUE-2026-129`, `ISSUE-2026-130`, `ISSUE-2026-132`, `ISSUE-2026-135`, `ISSUE-2026-142`
- `ISSUE-2026-144`, `ISSUE-2026-145`, `ISSUE-2026-153`, `ISSUE-2026-157`, `ISSUE-2026-158`, `ISSUE-2026-159`
- `ISSUE-2026-160`, `ISSUE-2026-170`

#### L effort

- `ISSUE-2026-064`, `ISSUE-2026-065`, `ISSUE-2026-073`, `ISSUE-2026-117`, `ISSUE-2026-119`, `ISSUE-2026-123`
- `ISSUE-2026-133`, `ISSUE-2026-139`, `ISSUE-2026-168`

### Low impact

#### XS effort

- `ISSUE-2026-062`, `ISSUE-2026-164`, `ISSUE-2026-171`, `ISSUE-2026-177`, `ISSUE-2026-180`

#### S effort

- `ISSUE-2026-061`, `ISSUE-2026-070`, `ISSUE-2026-173`, `ISSUE-2026-175`, `ISSUE-2026-176`, `ISSUE-2026-178`

#### M effort

- `ISSUE-2026-057`, `ISSUE-2026-058`, `ISSUE-2026-059`, `ISSUE-2026-174`, `ISSUE-2026-179`

#### L effort

- `ISSUE-2026-141`

## Detailed findings

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

### ISSUE-2026-009 — testwrapper: Shard discovery failures become successful skips

- Status: Published.
- Delivery mode: Issue.
- Location: https://github.com/tailscale/tailscale/issues/20797
- Evidence class: Observed and source-proven; CI occurrence frequency not measured.
- Internal priority: High.
- Confidence: High.
- Type: Error and orchestration.
- Publication target: new issue.
- Summary: `testsForShard` maps invalid shard specifications and `go list` failures to an empty result.
  `runTests` treats that result as a legitimately empty shard and reports the package as skipped.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `cmd/testwrapper/testwrapper.go:181-199` returns `nil, nil` for both error classes.
  Lines 278-286 convert a zero-length result into a successful skip.
  `TS_TEST_SHARD=bogus /tmp/testwrapper-repro ./cmd/testwrapper` exited zero and printed a skip.
  `TS_TEST_SHARD=1/2 /tmp/testwrapper-repro ./does-not-exist` did the same after `go list` failed.
  `TS_TEST_SHARD=2/1` also exited zero and printed a skip for an out-of-range shard.
  `TS_TEST_SHARD=100000/100000` remained a successful skip for a valid empty shard.
  JSON decode and source-read failures can return an empty or partial selection without an error.
  Issue #19886 and merged pull request #19887 introduced the current automatic sharding path.
  Related testwrapper error-handling reports and fixes do not own this discovery-result root cause.
  Focused issue, pull-request, and discussion searches found no exact duplicate or active competing fix.
- Shared change pressure: Not a DRY finding; one discovery boundary conflates failure with valid emptiness.
- Impact: Invalid configuration or failed discovery can omit the package's sharded tests without a failing process status.
  JSON or source-read failures can also omit a partial selection.
  Current workflow shard values are valid, and occurrence frequency in CI is not measured.
- Proposed direction: Reject malformed, out-of-range, and unsafe shard specifications.
  Treat parsing, `go list`, JSON decoding, and source scanning as all-or-error discovery.
  Reserve an empty successful result for a valid shard with no assigned tests.
  Surface discovery diagnostics before exiting or deliberately reach the main `go test` fallback promised by the comment.
- Risks and boundaries: `main` special-cases wrapped `*exec.ExitError` values and can exit before logging them.
  Capturing output alone is insufficient unless the diagnostic is emitted before that exit.
  Preserve package-level fatal handling and do not turn a legitimate empty shard into a failure.
  Do not claim that every JSON or source-read failure produces an empty skip; partial selection is also possible.
- Verification: The current binary returned zero for invalid syntax, an out-of-range shard, and failed `go list`.
  A source overlay returned non-zero with diagnostics for those failure classes.
  The same overlay preserved success for a valid empty shard.
- Missing publication evidence: None.
  Published as https://github.com/tailscale/tailscale/issues/20797 after exact draft and target approval.

### ISSUE-2026-010 — Makefile: SSH integration prerequisites and build failures are backgrounded

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; developer impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Orchestration and error.
- Publication target: Undecided.
- Summary: The continued SSH integration recipe backgrounds the prerequisite `&&` chain with the first Docker build.
  Its operandless `wait` also cannot report which of the four builds failed.
- Evidence: `Makefile:138-145` forms one shell list whose first `&` terminates the preceding `&&` chain.
  The same recipe launches four background jobs and ends with bare `wait`.
- Shared change pressure: Not a DRY finding; one shell recipe owns prerequisite and child-process ordering.
- Impact: Source proves that Docker builds can race prerequisite binaries and that child failures can be masked.
  Failure frequency is not measured.
- Proposed direction: Complete prerequisites in the foreground, then capture all Docker build PIDs.
  Wait for every PID and aggregate a non-zero status after all jobs finish.
- Risks and boundaries: Preserve parallel Docker builds and wait for all children even after one failure.
  Keep the recipe portable to the Makefile's `/bin/sh`.
- Verification: Use harmless probes for prerequisite ordering and four child exit statuses.
  Confirm that no child starts early and any failed child makes the target fail.
- Missing publication evidence: Verify current `upstream/main` and reproduce the recipe under its supported shells.
  Search prior SSH integration target changes and reports.

### ISSUE-2026-011 — wgengine: Failed Router and DNS configs are cached as applied

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: State and lifecycle.
- Publication target: Undecided.
- Summary: `userspaceEngine.Reconfig` updates Router and DNS applied-state caches before their setters succeed.
  An identical retry can therefore return `ErrNoChanges` after a transient apply failure.
- Evidence: `wgengine/userspace.go:843-847` mutates `lastRouter` and `lastDNSConfig`.
  Setters run later at `wgengine/userspace.go:903-939`, after the early-return check at lines 862-863.
- Shared change pressure: Not a DRY finding; one Reconfig owner confuses desired and successfully applied state.
- Impact: Source proves that identical retries can skip a Router or DNS configuration that previously failed.
  Platform failure frequency is not measured.
- Proposed direction: Compare without mutating the applied-state caches.
  Commit each cache only after its corresponding setter succeeds.
- Risks and boundaries: Preserve independent Router and DNS attempts and the historical DNS-after-Router behavior.
  Retried setters must remain idempotent.
- Verification: Inject one transient Router failure and one transient DNS failure in separate cases.
  Repeat the identical Reconfig and confirm the failed setter runs again.
- Missing publication evidence: Verify current `upstream/main` and reproduce both retry paths.
  Search issues and pull requests for Reconfig retry semantics.

### ISSUE-2026-012 — netmapcache: Missing values retain digests that suppress repair

- Status: Published.
- Delivery mode: Issue.
- Location: https://github.com/tailscale/tailscale/issues/20795
- Evidence class: Observed and source-proven; production startup impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: new issue.
- Summary: `Cache` retains `lastWrote` digests after explicit removal and missing-value reads.
  A later identical write is then suppressed even though the Store value no longer exists.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `ipn/ipnlocal/netmapcache/netmapcache.go:81-105` skips writes on digest equality.
  Lines 253-281 remove peer values without invalidating their digests.
  Lines 333-337 and 411-423 retain digests after missing self and ordinary values.
  A FileStore remove and identical peer re-add omitted the peer on `Load`.
  Removing `self`, loading, and storing the identical map left the cache unavailable.
  Removing `dns`, loading, and storing the identical map left DNS absent.
  Merged pull request #20111 introduced `UpdatePeers`; #20132 cherry-picked it to `release-branch/1.100`.
  Pull requests #18497, #18547, and #18590 establish the digest, missing-key, and skipped-write history.
  None reports this missing-value repair root cause.
  Focused issue, pull-request, and discussion searches found no exact duplicate or active competing fix.
- Shared change pressure: Store values and their write-suppression digests share one `Cache` consistency owner.
- Impact: A missing value can remain missing after an apparently successful identical Store or peer re-add.
  Production sequence frequency and startup impact are not measured.
- Proposed direction: Invalidate `lastWrote[key]` after every explicit peer-removal attempt.
  Invalidate the corresponding digest whenever `Load` observes a missing self or ordinary value.
- Risks and boundaries: Preserve write suppression for unchanged values known to remain in the Store.
  Removal errors leave Store state uncertain, so invalidation may cause one safe repair write.
- Verification: Focused temporary subcases in `TestUpdatePeers` and `TestInvalidCache` reproduced the peer re-add,
  missing self, and missing DNS failures.
  Invalidating the three digest paths made those same disposable subcases pass.
  The current checked-in tests do not cover these repair sequences.
- Missing publication evidence: None.
  Published as https://github.com/tailscale/tailscale/issues/20795 after exact draft and target approval.

### ISSUE-2026-013 — k8s-operator: Unavailable ProxyGroup fallthrough is intentional

- Status: Rejected.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven false positive with verified design history.
- Internal priority: Low.
- Confidence: High.
- Type: Validation and state.
- Publication target: Undecided.
- Summary: The candidate treated unavailable-ProxyGroup fallthrough as a missing terminal branch.
  History proves that provisioning while unavailable was introduced deliberately.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `cmd/k8s-operator/egress-services.go:572-579` overwrites Unknown with True and returns `true`.
  Commit `b406f209c` and pull request #14436 deliberately removed the earlier `return false, nil`.
  That change provisions health-check-enabled Egress resources needed by the pre-shutdown design.
  `TestTailscaleEgressServices` provisions successfully with a ProxyGroup lacking an Available condition.
  The focused test passes on current `upstream/main`.
- Shared change pressure: Not a DRY finding; the proposed terminal branch conflicts with established provisioning ownership.
- Impact: The recorded fix would block required Egress resource creation before ProxyGroup availability.
  The intermediate condition assignments may be redundant, but they do not prove premature provisioning.
- Proposed direction: No change while rejected.
  Reopen only if a separate persisted-condition defect is reproduced without blocking intended provisioning.
- Risks and boundaries: Preserve the pre-shutdown health-check resources introduced by pull request #14436.
  Do not infer invalid provisioning solely from the overwritten in-memory condition.
- Verification: History review and the existing unavailable-ProxyGroup fixture disprove the proposed terminal return.
- Missing publication evidence: No publication is warranted because the proposed root cause and fix are disproven.

### ISSUE-2026-014 — k8s-operator: EndpointSlice recovery depends on a status transition

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; cluster impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Lifecycle and orchestration.
- Publication target: Undecided.
- Summary: The main Egress reconciler does not watch managed EndpointSlices.
  Recovery can stall when readiness is already False and another missing Slice causes no status change.
- Evidence: `cmd/k8s-operator/operator.go:579-605` gives only the readiness reconciler an EndpointSlice watch.
  `cmd/k8s-operator/egress-services-readiness.go:68-96` suppresses unchanged status writes and documents the dependency.
- Shared change pressure: Not a DRY finding; recovery ownership is split across two controller event paths.
- Impact: Source proves that deletion can lack an event that reaches the owner of `ensureEndpointSlices`.
  Cluster occurrence frequency is not measured.
- Proposed direction: Reuse `egressSvcFromEps` in the main reconciler behind a bounded event predicate.
  Enqueue deletion or meaningful owned-field drift while ignoring equivalent self-authored updates.
- Risks and boundaries: An unrestricted watch can loop because the current ensure path updates existing Slices.
  Preserve queue idempotence and ignore unrelated EndpointSlices.
- Verification: Delete a managed Slice while readiness is already False and confirm recreation.
  Trace events to prove that self-authored equivalent updates do not requeue indefinitely.
- Missing publication evidence: Verify current `upstream/main` and reproduce the stalled recovery.
  Search current operator recovery and EndpointSlice watch work.

### ISSUE-2026-015 — serve: Funnel validates port 443 and gates teardown

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; user impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Validation and lifecycle.
- Publication target: Undecided.
- Summary: The active Funnel path validates hard-coded port 443 before parsing the requested port.
  It also performs capability validation for `off`, which can block cleanup after capability loss.
- Evidence: `cmd/tailscale/cli/serve_v2.go:405-430` verifies 443 before deriving `srvPort` and `turnOff`.
  `cmd/tailscale/cli/funnel.go:94-107` passes the requested port and skips validation for teardown.
- Shared change pressure: The active and legacy-named paths implement the same Funnel validation decision differently.
- Impact: Source proves incorrect validation for non-default ports and a blocked teardown path.
  Actual usage frequency is not measured.
- Proposed direction: Derive the port and teardown state first.
  Validate only setup and pass the requested port.
- Risks and boundaries: Preserve argument validation before mutation and existing feature-enable behavior.
  Teardown must remain available when control-plane capabilities change.
- Verification: Configure an allowed non-443 Funnel and remove it after capability loss.
  Confirm the requested port is validated and `off` is not gated.
- Missing publication evidence: Verify current `upstream/main` and reproduce both active v2 paths.
  Search Serve and Funnel issues and pull requests.

### ISSUE-2026-016 — serve: Nil ServeConfig crashes first-use commands

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Observed and source-proven; user frequency not measured.
- Internal priority: High.
- Confidence: High.
- Type: State and error.
- Publication target: Undecided.
- Summary: Declarative `get-config`, `set-config`, and `clear` dereference a nil `ServeConfig`.
  Other Serve paths establish nil as the normal no-configuration state.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `cmd/tailscale/cli/serve_v2.go:633-637`, 665-802, and 883-896 dereference the result.
  `cmd/tailscale/cli/serve_v2.go:445-448` and `serve_status.go:20-23` normalize or accept nil.
  A LocalClient fixture returning nil reproduced independent panics in `clear`, `get-config`, and `set-config`.
  Pull requests #16509 and #17435 introduced `clear` and the declarative config commands.
  Focused searches found no matching issue or active pull request.
- Shared change pressure: Three commands consume the same empty-state contract without its established normalization.
- Impact: First-use invocations can panic when no Serve configuration exists.
  User frequency is not measured.
- Proposed direction: Treat nil as a no-op in `clear`.
  Normalize nil to an empty `ServeConfig` in `get-config` and `set-config`.
- Risks and boundaries: `clear` must not perform an unnecessary write for an absent configuration.
  `get-config` must emit the established empty representation.
  `set-config` must be able to create the first configuration.
- Verification: The current source panicked independently in all three fixture cases.
  A source overlay returned success, emitted `{}` for single-service `get-config`, avoided a clear write, and applied config.
- Missing publication evidence: Delivery mode, exact external target, and exact draft remain user decisions.

### ISSUE-2026-017 — serve: Single-service Advertised false is not applied

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; control-plane impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: State and mapping.
- Publication target: Undecided.
- Summary: `get-config` emits `Advertised:false`, but single-service `set-config` only adds advertisements.
  Applying an explicit false value leaves an already advertised service unchanged.
- Evidence: `cmd/tailscale/cli/serve_v2.go:682-687` serializes the false state.
  Lines 963-992 build only the true/add path before `EditPrefs`.
- Shared change pressure: One declarative mapping is asymmetric between serialization and application.
- Impact: Source proves that a single-service export, edit, and apply cycle does not round-trip advertisement state.
  Operator frequency is not measured.
- Proposed direction: Load current preferences once and calculate the selected service's true or false state symmetrically.
  Preserve unrelated advertised services and perform the existing single `EditPrefs`.
- Risks and boundaries: `EditPrefs` and `SetServeConfig` remain separate writes.
  Partial failures must be returned without pretending the two resources are atomic.
- Verification: Apply true and false states for one service while another remains advertised.
  Confirm one preference read/write and preservation of unrelated services.
- Missing publication evidence: Verify current `upstream/main` and reproduce the round trip.
  Search service advertisement issues, pull requests, and format discussions.

### ISSUE-2026-018 — cli: Failed Taildrop receive leaves a file that blocks retry

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; transfer failure frequency not measured.
- Internal priority: High.
- Confidence: High.
- Type: Error and lifecycle.
- Publication target: Undecided.
- Summary: A post-open Taildrop receive failure leaves the new destination while retaining the inbox item.
  Quarantine failure also returns without closing the file, and overwrite or rename loops can repeat the leak.
- Evidence: `cmd/tailscale/cli/file.go:646-707` leaves the destination on quarantine or copy failure.
  Lines 699-701 return from `quarantine.SetOnFile` without `f.Close`.
  Lines 732-795 retain failed inbox items and retry loop batches.
- Shared change pressure: Not a DRY finding; one receive owner must unwind its file and filesystem state.
- Impact: Source proves that a transient failure can block default retry and leak a descriptor on quarantine error.
  Repeated descriptor accumulation requires a retrying overwrite or rename path and is not measured.
- Proposed direction: Close and remove only the destination created by the current attempt on post-open failure.
  Preserve the inbox item so the transfer remains retryable.
- Risks and boundaries: Never remove a pre-existing user file or a path replaced by another process.
  Preserve successful receive behavior and surface the original failure.
- Verification: Inject quarantine, copy, close, overwrite, and rename failures, then retry.
  Confirm exact close ownership, destination cleanup, inbox retention, and successful recovery.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce on affected platforms.
  Search Taildrop retry, quarantine, and partial-file issues and pull requests.

### ISSUE-2026-019 — cli: Route warnings run before set derives routes

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; operator impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Validation and output.
- Publication target: Undecided.
- Summary: `tailscale set` checks forwarding readiness before it calculates requested advertised routes.
  Route-only invocations therefore skip the existing IP-forwarding and UDP-GRO warnings.
- Evidence: `cmd/tailscale/cli/set.go:196` invokes the warning helper.
  Route derivation occurs later at `cmd/tailscale/cli/set.go:212-220`.
- Shared change pressure: Not a DRY finding; one validation call executes before its owned derived input exists.
- Impact: Source proves missing diagnostics for subnet-router and exit-node changes made through `set`.
  Operator frequency is not measured.
- Proposed direction: Invoke `warnOnAdvertiseRoutes` after successful route derivation.
- Risks and boundaries: Preserve App Connector warnings and execute the checks exactly once.
  Do not change preference validation or mutation order.
- Verification: Exercise `--advertise-routes` and `--advertise-exit-node` with warning-producing LocalAPI responses.
  Confirm warnings appear exactly once.
- Missing publication evidence: Verify current `upstream/main` and reproduce both flag paths.
  Search prior forwarding-warning issues and pull requests.

### ISSUE-2026-020 — ping: Final timeout can report success without a direct path

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; user impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Error and output.
- Publication target: Undecided.
- Summary: A relay response sets `anyPong`, and a timeout on the final attempt then returns success.
  The non-timeout exhaustion path correctly reports that a direct connection was not established.
- Evidence: `cmd/tailscale/cli/ping.go:128-142` owns the final-timeout return.
  `cmd/tailscale/cli/ping.go:183-190` owns the contradictory normal exhaustion result.
- Shared change pressure: Two terminal branches implement the same `untilDirect` outcome differently.
- Impact: Source proves exit zero is possible even though the default direct-path condition was not met.
  Automation and operator frequency are not measured.
- Proposed direction: Reuse the same `anyPong` and `untilDirect` decision in both terminal branches.
- Risks and boundaries: Preserve `--until-direct=false`, TSMP, ICMP, PeerAPI, and the true no-reply case.
- Verification: Replay a relay response followed by a final deadline.
  Confirm a non-zero direct-not-established outcome and unchanged alternative modes.
- Missing publication evidence: Verify current `upstream/main` and reproduce through the CLI.
  Search prior ping exit-semantics issues and pull requests.

### ISSUE-2026-021 — ipnserver: Permitted LocalAPI shutdown returns a server error

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; lifecycle impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Lifecycle and error.
- Publication target: Undecided.
- Summary: The LocalAPI shutdown event closes the listener outside `http.Server`.
  `Server.Run` returns the resulting error, and `startIPNServer` suppresses only context cancellation.
- Evidence: `ipn/ipnserver/server.go:503-547` owns listener closure and `http.Server.Serve`.
  `cmd/tailscaled/tailscaled.go:633-644` converts the non-context result into `ipnserver.Run` error.
- Shared change pressure: Not a DRY finding; one lifecycle owner does not classify its expected shutdown result.
- Impact: Source proves that an allowed shutdown can terminate through an unexpected error path.
  User-visible logging and restart impact are not measured.
- Proposed direction: Let `Server.Run` own `http.Server` closure after the handler flushes its successful response.
  Treat only the resulting expected server-closed error as normal.
- Risks and boundaries: Preserve active-connection cleanup, `runDone` ordering, and `LocalBackend.Shutdown`.
  Do not suppress unrelated listener failures.
- Verification: Invoke `ShutdownTailscaled` with and without an active LocalAPI connection.
  Confirm client success, closed connections, and no unexpected Run error.
- Missing publication evidence: Verify current `upstream/main` and reproduce with the production ipnserver.
  Search shutdown and restart issues and pull requests.

### ISSUE-2026-022 — cli: Inaccessible KUBECONFIG path can panic

- Status: Published.
- Delivery mode: Pull request.
- Location: https://github.com/tailscale/tailscale/pull/20799
- Evidence class: Observed and source-proven; user frequency not measured.
- Internal priority: High.
- Confidence: High.
- Type: Error and filesystem.
- Publication target: new pull request.
- Summary: `kubeconfigPath` dereferences `FileInfo` after any non-ENOENT `os.Stat` result.
  Permission and other access errors can return a nil `FileInfo` and trigger a panic.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `cmd/tailscale/cli/configure-kube.go:54-63` owns path selection and the unsafe dereference.
  `cmd/tailscale/cli/configure-kube.go:80-103` already owns actionable access-error reporting.
  A `KUBECONFIG` symlink loop made `os.Stat` return `ELOOP` with a nil `FileInfo`.
  The focused existing `TestCheckKubeconfigWritable/unwritable-dir` reproduced the panic at line 59.
  Pull request #11604 introduced the list selection, while #20009 added the later writability check.
  Issue #20007 and focused issue and pull-request searches found no same-root duplicate.
  Branch `pr/issue-2026-022-kubeconfig-stat` commit `2ddcd8161` is pushed to `origin`.
- Shared change pressure: Not a DRY finding; path selection bypasses the existing error boundary.
- Impact: Source proves a panic path for inaccessible KUBECONFIG entries.
  User frequency is not measured.
- Proposed direction: Dereference `FileInfo` only when `os.Stat` succeeds.
  Preserve list precedence and let `checkKubeconfigWritable` report access failures.
- Risks and boundaries: Keep existing behavior for nonexistent entries and multi-path KUBECONFIG values.
  Do not silently choose a different writable file after an access error.
- Verification: Before the fix, the focused existing subtest panicked on the `ELOOP` path.
  After the fix, `./tool/go test ./cmd/tailscale/cli -run '^TestCheckKubeconfigWritable$' -count=1` passed.
  The test preserves the non-root unwritable-directory check and confirms the failing list entry remains selected.
- Missing publication evidence: None.
  Published as https://github.com/tailscale/tailscale/pull/20799 after exact draft and target approval.

### ISSUE-2026-023 — exit-node: Listing can panic on a peer without an address

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; user impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Output and error.
- Publication target: Undecided.
- Summary: `exit-node list` indexes the first Tailscale IP without checking whether the slice is empty.
  Exit-node eligibility is populated independently from visible Tailscale addresses.
- Evidence: `cmd/tailscale/cli/exitnode.go:138-145` indexes `peer.TailscaleIPs[0]`.
  `ipn/ipnlocal/local.go:1601-1646` assigns addresses and `ExitNodeOption` independently.
- Shared change pressure: Not a DRY finding; one renderer ignores an optional-address state handled elsewhere.
- Impact: Source proves an index-out-of-range path during partial or no-address peer state.
  Occurrence frequency is not measured.
- Proposed direction: Reuse the safe first-address formatter and define a stable no-address placeholder.
- Risks and boundaries: Preserve the row, hostname, location, and status for the partial peer.
  Avoid ambiguous column shifts for parsers.
- Verification: Render an exit-node option with an empty address slice.
  Confirm no panic and stable tabular columns.
- Missing publication evidence: Verify current `upstream/main` and reproduce from a realistic peer state.
  Search exit-node list issues and pull requests.

### ISSUE-2026-024 — client/local: Internally consumed response bodies remain open

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; resource impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Lifecycle and error.
- Publication target: Undecided.
- Summary: Several LocalAPI paths consume or discard an HTTP response without closing its body.
  Successful streaming paths correctly transfer body ownership to their callers.
- Evidence: `client/local/local.go:164-177` consumes 403 and 412 bodies without closing.
  Lines 480-492, 519-536, and 856-878 leave internally handled log, event, or upload bodies open.
- Shared change pressure: One LocalAPI transport boundary owns response-body transfer and internal consumption.
- Impact: Source proves repeated response-body ownership violations.
  Connection reuse and resource impact are not measured.
- Proposed direction: Close only bodies fully handled inside `client/local`.
  Preserve caller ownership for successful streaming responses.
- Risks and boundaries: Do not close successful `TailDaemonLogs`, `StreamBusEvents`, or file-download streams early.
  Close errors should not replace a more meaningful operation error.
- Verification: Use spy response bodies for every internal and transferred path.
  Confirm exact close counts and uninterrupted successful streams.
- Missing publication evidence: Verify current `upstream/main` and measure transport behavior under repetition.
  Search LocalAPI body-lifecycle issues and pull requests.

### ISSUE-2026-025 — cli: Cancelled file loop lacks proven production caller

- Status: Rejected.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven behavior; qualifying recurring pain not established.
- Internal priority: Low.
- Confidence: High.
- Type: Lifecycle.
- Publication target: Undecided.
- Summary: `file get --loop` can repeat context errors after cancellation.
  The production CLI invokes `RunWithContext` using `context.Background`, and no cancellable production caller was found.
- Evidence: `cmd/tailscale/cli/file.go:710-718` and 776-795 implement the repeated error and sleep path.
  `cmd/tailscale/cli/cli.go:177-179` supplies a background context for the ordinary CLI.
- Shared change pressure: Not a DRY finding; the candidate depends on an unverified external embedding workflow.
- Impact: The source behavior is real for a cancelled context, but current recurring user impact is unproven.
- Proposed direction: No change while rejected.
  Reopen only with a concrete cancellable `RunWithContext` owner or reproduction.
- Risks and boundaries: Do not add lifecycle policy for a hypothetical caller.
- Verification: If reopened, cancel a real supported embedding workflow during `--loop`.
- Missing publication evidence: A production caller, recurrence evidence, and expected cancellation contract are missing.

### ISSUE-2026-026 — cli: Help dump output bypass has no demonstrated consumer

- Status: Rejected.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven inconsistency; qualifying recurring pain not established.
- Internal priority: Low.
- Confidence: High.
- Type: Output.
- Publication target: Undecided.
- Summary: `TS_DUMP_HELP` and hidden `--json-docs` bypass the injectable CLI `Stdout`.
  Repository search found no consumer of either special mode outside their definitions.
- Evidence: `cmd/tailscale/cli/cli.go:35-52` owns injectable output.
  Lines 148-160 and 534-536 write directly to process stdout.
- Shared change pressure: Not a DRY finding; the candidate is an unused output-owner inconsistency.
- Impact: Capture behavior differs if a wrapper invokes these modes, but recurring use is unproven.
- Proposed direction: No change while rejected.
  Reopen when a maintained wrapper, generator, or automation consumer is identified.
- Risks and boundaries: Avoid changing hidden output solely for stylistic consistency.
- Verification: If reopened, invoke both modes through the actual consumer with redirected `Stdout`.
- Missing publication evidence: A maintained consumer and concrete failed-capture workflow are missing.

### ISSUE-2026-027 — whoami: Login URL proposal lacks recurring pain evidence

- Status: Rejected.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven output; claimed extra user step assumed.
- Internal priority: Low.
- Confidence: High.
- Type: Output.
- Publication target: Undecided.
- Summary: `whoami` reports state without the available login URL when no Tailscale IP exists.
  The claim that users then run `tailscale status` as a repeated workaround was not established.
- Evidence: `cmd/tailscale/cli/whoami.go:40-45` owns the current error.
  `cmd/tailscale/cli/status.go:260-277` owns the separate actionable state description.
- Shared change pressure: The two commands intentionally own different user questions and output contracts.
- Impact: A more actionable error is plausible, but concrete repeated pain and consumer expectations are unknown.
- Proposed direction: No change while rejected.
  Reopen with operator evidence or an explicit shared state-to-action contract.
- Risks and boundaries: Exact error-text consumers could observe a behavior change.
- Verification: If reopened, test each no-IP backend state and JSON invocation.
- Missing publication evidence: User workflow evidence, output compatibility expectations, and prior-art research are missing.

### ISSUE-2026-028 — cli: Duplicate status snapshots do not justify a caller refactor

- Status: Rejected.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven repeated call; latency and user impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Performance and orchestration.
- Publication target: Undecided.
- Summary: Several hostname commands load status before calling a resolver that loads status again.
  The proposed multi-caller resolver refactor lacks evidence that one local IPC call costs more than its adoption.
- Evidence: `cmd/tailscale/cli/ip.go:64-84` and `cmd/tailscale/cli/ping.go:96-113` preload status.
  `cmd/tailscale/cli/ping.go:195-205` performs the second status request.
- Shared change pressure: The calls share data, but their gate, resolution, and freshness responsibilities differ.
- Impact: One request per affected invocation is avoidable in source.
  Frequency and latency are not measured.
- Proposed direction: No change while rejected.
  Reopen only with representative call-count or latency evidence.
- Risks and boundaries: A shared snapshot can alter freshness, DNS fallback, and partial-state behavior.
- Verification: If reopened, measure command-level status calls and end-to-end latency before refactoring.
- Missing publication evidence: Representative measurements and caller-specific freshness requirements are missing.

### ISSUE-2026-029 — serve: Service status prefixes Unix targets with tcp

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; user impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Output and mapping.
- Publication target: Undecided.
- Summary: Service TCP status prints a Unix socket target as `tcp://unix:/path`.
  The node-level status renderer already preserves the `unix:` representation.
- Evidence: `cmd/tailscale/cli/serve_status.go:91-102` unconditionally prefixes `tcp://`.
  `cmd/tailscale/cli/serve_legacy.go:668-671` owns the correct sibling behavior.
- Shared change pressure: Two renderers express the same TCP target representation and have diverged.
- Impact: Source proves misleading status output for every Service Unix TCP target.
  Usage frequency is not measured.
- Proposed direction: Reuse the node renderer's Unix-versus-host target decision in `printServiceStatusTree`.
- Risks and boundaries: Preserve host-port output and TLS-terminated annotations.
- Verification: Render Service Unix and ordinary TCP targets through the status tree.
  Confirm only Unix targets omit the `tcp://` prefix.
- Missing publication evidence: Verify current `upstream/main` and reproduce the CLI output.
  Search Service Unix-target issues and pull requests.

### ISSUE-2026-030 — serve: Node TCP status violates deterministic ordering

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Observed and source-proven; automation impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Output.
- Publication target: Undecided.
- Summary: The status contract promises deterministic ordering, but node TCP entries range a Go map directly.
  Unchanged configurations can therefore produce reordered lines.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `cmd/tailscale/cli/serve_status.go:26-33` documents deterministic ordering.
  `cmd/tailscale/cli/serve_legacy.go:648-673` iterates `sc.TCP` without sorting.
  Rendering one six-port configuration 128 times produced six distinct outputs.
  Pull request #19600 introduced the deterministic status contract and sorted sibling renderers.
  Focused searches found no matching issue or pull request.
  Open pull request #20391 touches `serve_legacy.go` for an unrelated JSON warning.
- Shared change pressure: Web, Service, and node TCP renderers share one deterministic output policy.
- Impact: Identical configurations produce unstable human-readable status output.
  Parser and diff impact are not measured.
- Proposed direction: Sort the numeric TCP port keys before rendering.
- Risks and boundaries: Preserve group ordering and every rendered line.
  Sort numerically rather than lexically.
- Verification: Current source produced six output variants in 128 renderings.
  A source overlay produced one byte-identical, numerically ascending result.
- Missing publication evidence: Delivery mode, exact external target, and exact draft remain user decisions.

### ISSUE-2026-031 — k8s-operator: Stable Ingress status is written on every reconcile

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven cost path; cluster impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Orchestration and output.
- Publication target: Undecided.
- Summary: `IngressReconciler` writes the status subresource after every successful provision.
  Its primary Ingress watch has no predicate that excludes unchanged status events.
- Evidence: `cmd/k8s-operator/ingress.go:262-289` rebuilds and unconditionally updates status.
  `cmd/k8s-operator/operator.go:455-462` registers the unfiltered primary watch.
- Shared change pressure: The HA Ingress reconciler already owns the equivalent semantic equality guard.
- Impact: Source proves an avoidable API write and event for unchanged desired status.
  Reconcile-loop magnitude is not measured.
- Proposed direction: Snapshot status before mutation and update only after semantic inequality.
- Risks and boundaries: Compare after computing all owned fields and preserve external status writers.
  Continue normal optimistic-lock handling for real changes.
- Verification: Reconcile a stable Ingress twice and observe Status Update calls and ResourceVersion.
  The second pass should perform no status write.
- Missing publication evidence: Verify current `upstream/main` and measure event behavior with controller-runtime.
  Search Ingress status churn issues and pull requests.

### ISSUE-2026-032 — k8s-operator: Generic update helper writes unchanged child objects

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven cost path; cluster impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Orchestration and persistence.
- Publication target: Undecided.
- Summary: `createOrMaybeUpdate` writes every existing object after a non-nil callback.
  `createOrUpdate` wraps a nil callback, so even callers requesting no mutation still update.
- Evidence: `cmd/k8s-operator/sts.go:1184-1235` owns the callback, wrapper, and unconditional update.
  `cmd/k8s-operator/egress-services.go:246-276` reaches the helper for existing EndpointSlices.
  StatefulSet and Secret callers also feed watched child resources through the same boundary.
- Shared change pressure: One generic persistence helper owns the no-op write decision for all callers.
- Impact: Source proves avoidable Kubernetes API writes and follow-on child events.
  Reconcile-loop magnitude and cluster cost are not measured.
- Proposed direction: Pass nil callbacks through unchanged.
  For non-nil callbacks, compare a pre-callback deep copy with the semantically mutated object before update.
- Risks and boundaries: Preserve callback errors, optimistic-lock conflicts, real mutations, and defaulted fields.
  Do not weaken separate Server-Side Apply ownership or caller-specific update contracts.
- Verification: Reconcile unchanged EndpointSlice, StatefulSet, Service, and Secret callers twice.
  Confirm no second update while real owned-field drift still persists.
- Missing publication evidence: Record the exact current `upstream/main` revision and measure stable reconciliation.
  Search generic helper and no-op Kubernetes update issues and pull requests.

### ISSUE-2026-033 — cli: Empty-file NaN progress lacks Cheapwin recurrence

- Status: Rejected.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven output defect; qualifying recurring pain not established.
- Internal priority: Low.
- Confidence: High.
- Type: Output.
- Publication target: Undecided.
- Summary: TTY progress divides zero transferred bytes by zero total bytes for an empty file.
  The defect is real, but repeated zero-byte TTY transfer pain was not established.
- Evidence: `cmd/tailscale/cli/file.go:331-381` paints immediately and computes the zero denominator.
- Shared change pressure: Not a DRY finding; this is one narrow arithmetic edge case.
- Impact: Source proves `NaN%` output for the edge case.
  User frequency and material impact are unknown.
- Proposed direction: No change while rejected.
  Reopen with recurring empty-file transfer evidence or a broader progress invariant.
- Risks and boundaries: Avoid adding permanent branching for an unmeasured cosmetic path.
- Verification: If reopened, send an empty regular file through TTY progress and inspect the final line.
- Missing publication evidence: Recurrence, user impact, and prior-art evidence are missing.

### ISSUE-2026-034 — whois: Writer error lacks Cheapwin recurrence

- Status: Rejected.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven error omission; qualifying recurring pain not established.
- Internal priority: Low.
- Confidence: High.
- Type: Error and output.
- Publication target: Undecided.
- Summary: The JSON identity renderer ignores the error returned by `json.Encoder.Encode`.
  Failed output writers are possible, but repeated current pain was not established.
- Evidence: `cmd/tailscale/cli/whois.go:62-74` returns nil after the unchecked encode.
  `cmd/tailscale/cli/whoami.go:47-51` shares the renderer.
- Shared change pressure: Not a DRY finding; one shared renderer omits normal Go error propagation.
- Impact: Source proves that a failed JSON write can return success.
  Failure frequency and consumer impact are unknown.
- Proposed direction: No change while rejected under the Cheapwin contract.
  Reopen with pipeline or embedding evidence.
- Risks and boundaries: A fix would change rare writer failures from exit zero to non-zero.
- Verification: If reopened, use a failing writer for both JSON commands and confirm error propagation.
- Missing publication evidence: Recurrent consumer pain and prior-art research are missing.

### ISSUE-2026-035 — netstack: Rejected TCP flows retain pre-admission state

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven retained flow keys and addresses; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: `wrapTCP` acquires a flow key and dynamic subnet address before forwarder admission.
  A rejected path can bypass the handlers that own cleanup for either resource.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `wgengine/netstack/netstack.go:514-601,1551-1556` acquires both resources before admission.
  GVisor can report `handled=true` without starting the handler that installs cleanup.
- Shared change pressure: One admission owner must pair every pre-admission acquisition with release.
- Impact: Rejected unique flows can retain map keys and dynamic subnet addresses.
  Trigger rate, retained bytes, address pressure, and production impact are not measured.
- Proposed direction: Acquire resources only after admission or release each one on every rejected return.
- Risks and boundaries: Never decrement counters or release resources that the rejected path did not acquire.
  Preserve per-client and global admission semantics.
- Verification: Exercise malformed SYN, overload, and GVisor in-flight rejection paths.
  Assert bounded map cardinality and zero retained dynamic addresses after each rejection.
- Missing publication evidence: Reproduce both retained-state paths on the recorded current revision.
  Search prior Netstack admission and SYN-flood issues.

### ISSUE-2026-036 — magicsock: Non-relay peer aliases grow without an owner policy

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven growth path; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Networking and state.
- Publication target: Undecided.
- Summary: `peerMap` retains every verified non-relay endpoint alias until the whole peer is deleted.
  Endpoint-state deletion is a different owner and cannot safely prune these aliases.
- Evidence: `wgengine/magicsock/peermap.go:47-48,181-240` owns insertion and bulk cleanup.
  `wgengine/magicsock/endpoint.go:75-76,487-503` establishes the incompatible lock owner.
- Shared change pressure: Not a DRY finding; both alias indexes share one `Conn.mu` consistency contract.
- Impact: Source proves unbounded alias cardinality under address churn.
  Real churn, memory growth, and routing impact are not measured.
- Proposed direction: Define a bounded age or cardinality policy in the `peerMap` owner.
  Remove `byEpAddr` and `epAddrs` entries atomically under `Conn.mu`.
- Risks and boundaries: Preserve cryptographically verified aliases, roaming, collisions, and lock order.
  Do not call the peer map from endpoint-owned cleanup.
- Verification: Churn verified addresses and check both indexes, lookup behavior, and lock ordering.
- Missing publication evidence: Record the exact current `upstream/main` revision and measure real alias churn.
  Search Magicsock endpoint-cache issues and the referenced source TODO.

### ISSUE-2026-037 — derp: Default duplicate policy retains per-frame speaker history

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven growth path; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Protocol and state.
- Publication target: Undecided.
- Summary: The default `lastWriterIsActive` policy appends alternating speakers for every frame.
  That policy only needs the last speaker, while `disableFighters` needs full A-to-B-to-A history.
- Evidence: `derp/derpserver/derpserver.go:790-803,1653-1708` owns activity history.
  Production construction leaves the duplicate policy at its zero-value default.
- Shared change pressure: Not a DRY finding; one strategy owner stores more history than its decision consumes.
- Impact: Source proves append-only growth while duplicate connections alternate.
  Duplicate frequency and heap impact are not measured.
- Proposed direction: Store constant state for `lastWriterIsActive`.
  Preserve full history and pruning semantics for `disableFighters`.
- Risks and boundaries: Do not weaken clone or fighter detection.
  Preserve active-client selection and duplicate registration behavior.
- Verification: Exercise both policies with alternating connections and assert strategy-specific state.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce memory growth.
  Search DERP duplicate-client issues and policy history.

### ISSUE-2026-038 — derpserver: PeerGone does not re-arm sawSrc watchers

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven state divergence; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Protocol and lifecycle.
- Publication target: Undecided.
- Summary: PeerGone removes the global watcher set but leaves the send-loop-owned `sawSrc` key.
  A reconnecting source can then fail to register a new watcher.
- Evidence: `derp/derpserver/derpserver.go:947-965,1973-1985,2020-2034` owns the transition.
  Lines 2261-2267 set `sawSrc` only when its local key is absent.
- Shared change pressure: Not a DRY finding; local and global watcher states form one lifecycle invariant.
- Impact: Source proves stale local state and missed watcher re-arming.
  Production disconnect frequency and user impact are not measured.
- Proposed direction: Delete the local key in the send loop before `PeerGoneReasonDisconnected`.
  Preserve `PeerGoneReasonNotHere` and connection-final cleanup.
- Risks and boundaries: Keep callback and send-loop ownership separate.
  Do not claim strict ordering across concurrent reconnect packets.
- Verification: Disconnect, reconnect, send, and disconnect again while observing watcher registration.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce the sequence.
  Search DERP PeerGone and watcher lifecycle issues.

### ISSUE-2026-039 — dns: TCP requests spawn before bounded admission

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven growth path; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and orchestration.
- Publication target: Undecided.
- Summary: The TCP reader starts one goroutine and allocates one buffer per frame before query admission.
  The existing 256-query guard runs later inside `Manager.Query`.
- Evidence: `net/dns/manager.go:525-531,647-663` owns admission and goroutine creation.
  `net/dns/resolver/tsdns.go:400-423` bounds delegated resolver work to ten seconds.
- Shared change pressure: Not a DRY finding; the TCP dispatch owner admits work after allocating it.
- Impact: Source proves that pending goroutines can exceed the active-query limit under pipelining.
  Heap, scheduler, and user impact are not measured.
- Proposed direction: Add nonblocking pre-spawn admission or a bounded worker owner.
  Preserve the direct-caller guard and explicit overload behavior.
- Risks and boundaries: Do not block the TCP read loop indefinitely.
  Preserve pipelined and out-of-order response semantics.
- Verification: Pipeline more than 256 requests and measure goroutines, buffers, and responses.
- Missing publication evidence: Record the exact current `upstream/main` revision and run a realistic load.
  Search DNS TCP admission and overload issues.

### ISSUE-2026-040 — controlhttp: Non-upgrade response body is not closed

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven ownership violation; resource impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: `tryURLUpgrade` returns a non-101 status error without closing the response body.
  Only a successful upgrade transfers body ownership to the connection.
- Evidence: `control/controlhttp/client.go:550-580` owns the response and all upgrade outcomes.
  Nearby failure paths close the body explicitly.
- Shared change pressure: Not a DRY finding; one HTTP upgrade boundary must classify body ownership.
- Impact: Source proves a response-body ownership violation on each non-101 attempt.
  Retry frequency and retained connection or descriptor impact are not measured.
- Proposed direction: Close `resp.Body` before returning the non-101 status error.
- Risks and boundaries: Preserve error details and successful 101 ownership transfer.
  Do not close the upgraded connection body.
- Verification: Return body-bearing non-101 responses repeatedly and assert exact close ownership.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce transport retention.
  Search control upgrade and HTTP body lifecycle issues.

### ISSUE-2026-041 — netstack: UDP address cleanup lacks exact installation ownership

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven state divergence; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Non-local UDP flows add address references before many early exits and do not release them.
  A naive release can remove a static address that the dynamic flow did not install.
- Evidence: `wgengine/netstack/netstack.go:500-506,658-688,1920-2113` owns add and release.
  Via-range conversion can also change the address key before cleanup.
- Shared change pressure: Not a DRY finding; address installation and release require one exact owner token.
- Impact: Source proves stale dynamic state and a possible static-address removal under naive cleanup.
  Trigger frequency and network impact are not measured.
- Proposed direction: Record the original key and whether this flow installed the address.
  Transfer one cleanup obligation through every handler, error, timer, and cancellation path.
- Risks and boundaries: Never remove pre-existing static addresses or decrement unowned references.
  Complete dynamic cleanup before stack teardown.
- Verification: Cover every early exit, custom handler, via conversion, static address, and shutdown.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce both outcomes.
  Search Netstack subnet-UDP and address ownership issues.

### ISSUE-2026-042 — netstack: UDP forwarders outlive errors and shutdown ownership

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven lifecycle gaps; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: UDP forwarders derive from `context.Background` and do not own all sockets on failure.
  Cancellation can leave a copy blocked, and shutdown does not wait for active forwarders.
- Evidence: `wgengine/netstack/netstack.go:2060-2152` owns registration, contexts, and copy loops.
  `Impl.Close` at lines 449-455 does not track this native forwarder lifecycle.
- Shared change pressure: Not a DRY finding; each forwarder needs one cancel, close, unregister, and wait owner.
- Impact: Source proves permanent registration-error leaks and incomplete shutdown of native resources.
  Failure frequency and retained resource impact are not measured.
- Proposed direction: Derive from `ns.ctx` and close flow-owned resources exactly once.
  Cancel and wait forwarders before gVisor stack close without duplicating gVisor endpoint ownership.
- Risks and boundaries: Preserve two-way copy, idle timeout, subnet release, and proxymap ordering.
  Do not close a resource after transferring ownership.
- Verification: Inject registration, copy, cancellation, timeout, and shutdown interleavings.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce leaks.
  Search Netstack UDP forwarding lifecycle issues.

### ISSUE-2026-043 — magicsock: DERP PeerGone and reader exit leave local route state

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven state divergence; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: PeerGone removes the route but leaves `peerPresent` and `lastPacketSrc`.
  Context cancellation can also return from the DERP reader without cleaning its owned routes.
- Evidence: `wgengine/magicsock/derp.go:555-666` owns reader state and PeerGone.
  Lines 952-959 close the active DERP connection without cleaning all reader-owned routes.
- Shared change pressure: One DERP reader owns both local sender state and its route registrations.
- Impact: Source proves that same-sender traffic can skip route re-addition after PeerGone.
  The structures are peer-bounded, and production impact is not measured.
- Proposed direction: Prune local sender state on PeerGone.
  Independently defer identity-checked cleanup of routes still owned by this reader.
- Risks and boundaries: Preserve route identity guards and routes installed by newer connections.
  Keep PeerGone and reader-exit transitions distinct.
- Verification: Exercise same-sender, interposed-sender, reconnect, and cancellation sequences.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce route behavior.
  Search Magicsock DERP route and PeerGone issues.

### ISSUE-2026-044 — magicsock: Receive activity locks every packet for one peer

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven lock path; contention not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Performance and concurrency.
- Publication target: Undecided.
- Summary: `noteRecvActivity` acquires the per-endpoint mutex for every received packet.
  Only ordinary heartbeat-enabled peers have a potentially read-only fast path.
- Evidence: `wgengine/magicsock/endpoint.go:519-543` owns the lock and mutations.
  Silent Disco and WireGuard-only paths update protected state.
- Shared change pressure: Not a DRY finding; one mode owner decides whether receive state is mutable.
- Impact: Source proves one mutex acquisition per packet for an endpoint.
  Lock wait, throughput impact, and optimization value are not measured.
- Proposed direction: Profile first.
  If material, expose one atomic ordinary mode and recheck special transitions under the mutex.
- Risks and boundaries: Preserve `bestAddr`, trust, heartbeat, timestamp, and notification semantics.
  Do not describe the per-endpoint mutex as global.
- Verification: Compare mutex profiles and packet throughput across every endpoint mode.
- Missing publication evidence: Record the exact current `upstream/main` revision and obtain profiles.
  Search Magicsock receive-lock optimization work.

### ISSUE-2026-045 — derp: Packet key serialization allocates avoidable slices

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven allocation path; performance impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Performance and protocol.
- Publication target: Undecided.
- Summary: Client packet writes allocate key slices with `AppendTo(nil)`.
  Server wire writes already avoid this allocation, while the server HLL insertion still allocates.
- Evidence: `derp/derp_client.go:255-296` owns client serialization.
  `derp/derpserver/derpserver.go:2223-2243` separates HLL and raw wire writes.
- Shared change pressure: Not a DRY finding; the same fixed-size key has two allocation-sensitive consumers.
- Impact: Source proves temporary slices on packet paths.
  `allocs/op`, CPU, GC, and workload frequency are not measured.
- Proposed direction: Benchmark first.
  Use the raw writer for clients and an owner-local scratch only if HLL allocation is material.
- Risks and boundaries: Preserve `wmu`, buffer ownership, synchronous HLL consumption, and wire bytes.
- Verification: Measure `allocs/op` and output equality for Send, ForwardPacket, and HLL insertion.
- Missing publication evidence: Record the exact current `upstream/main` revision and benchmark representative traffic.
  Search DERP allocation and key-serialization work.

### ISSUE-2026-046 — controlclient: Map backoff sleeps while holding c.mu

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven lock stall; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Lifecycle and concurrency.
- Publication target: Undecided.
- Summary: The map routine holds `c.mu` across synchronous jittered backoff.
  Operations requiring the same lock cannot cancel or advance state during the wait.
- Evidence: `control/controlclient/auto.go:643-655` owns the lock and backoff call.
  `util/backoff/backoff.go:60-76` permits a jittered wait approaching 45 seconds.
- Shared change pressure: Not a DRY finding; one routine mixes state capture and waiting under one lock.
- Impact: Source proves bounded stalls for pause, login, logout, status, and shutdown operations.
  Production frequency and latency are not measured.
- Proposed direction: Capture the required state and context under `c.mu`, then unlock before waiting.
- Risks and boundaries: Preserve retry ordering, paused-state decisions, and cancellation semantics.
- Verification: Force maximum backoff and measure concurrent state operations and shutdown.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce the stall.
  Search controlclient map-backoff issues.

### ISSUE-2026-047 — controlclient: TSMP advertisements scan every peer address

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven algorithmic cost; performance impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Performance and mapping.
- Publication target: Undecided.
- Summary: Each TSMP Disco advertisement scans every peer and advertised address under `peersMu`.
  Existing peer mutation paths could own a reverse index.
- Evidence: `control/controlclient/map.go:1208-1224` owns the scan.
  `control/controlclient/direct.go:429-459` invokes it for advertisements.
- Shared change pressure: Peer replacement, removal, and key patching share one index update contract.
- Impact: Source proves O(peers times addresses) work per advertisement.
  Tailnet cardinality, event rate, lock wait, and user impact are not measured.
- Proposed direction: Benchmark first.
  If material, index address to NodeID and resolve the current key under the existing peer lock.
- Risks and boundaries: Preserve exact address matching, collisions, replacements, removals, and key patches.
- Verification: Benchmark lookup and mutation costs across realistic and large tailnets.
- Missing publication evidence: Record the exact current `upstream/main` revision and obtain profiles.
  Search TSMP advertisement and peer-index work.

### ISSUE-2026-048 — tsnet: Last-resort TCP callbacks run under the server lock

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven lock stall; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Lifecycle and concurrency.
- Publication target: Undecided.
- Summary: `getTCPHandlerForFlow` invokes arbitrary fallback callbacks while holding `s.mu`.
  A production NAT callback performs `WhoIs` with a five-second timeout.
- Evidence: `tsnet/tsnet.go:1243-1255` owns lookup and callback invocation.
  `cmd/natc/natc.go:513-520` owns the network-bound fallback.
- Shared change pressure: Not a DRY finding; callback selection and execution have different lock owners.
- Impact: Source proves that unmatched fallback traffic can stall listener operations.
  Trigger frequency and observed latency are not measured.
- Proposed direction: Snapshot callbacks under `s.mu`, unlock, then invoke them.
  Define that deregistration does not wait for an already selected callback.
- Risks and boundaries: Do not hold a new RW lock across user code.
  Preserve listener precedence and callback ordering contract.
- Verification: Block and self-deregister callbacks while running listener operations.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce the stall.
  Search tsnet fallback-handler and callback-lifecycle issues.

### ISSUE-2026-049 — tailssh: Control notification responses remain open

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven ownership violation; resource impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: `notifyControl` returns after both success and error statuses without closing the response body.
- Evidence: `ssh/tailssh/tailssh.go:1454-1459,1484-1493,1580-1590` owns both callers and the response.
  `DoNoiseRequest` transfers the raw response to its caller.
- Shared change pressure: Not a DRY finding; one notification boundary owns every response outcome.
- Impact: Source proves one body ownership violation per returned notification response.
  Body shape, repetition, and transport impact are not measured.
- Proposed direction: Close the response body immediately after a successful request.
- Risks and boundaries: Preserve status handling and do not conflate closure with request timeout.
- Verification: Return successful and error responses with spy bodies and assert one close.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce transport behavior.
  Search TailSSH notification and Noise response lifecycle issues.

### ISSUE-2026-050 — tailssh: Delegated error logging reads an unbounded body

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven unbounded read; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and error.
- Publication target: Undecided.
- Summary: The delegated-action error path reads the entire body before truncating its log to one KiB.
- Evidence: `ssh/tailssh/tailssh.go:854-879` owns the request context, read, truncation, and retry.
- Shared change pressure: Not a DRY finding; one diagnostic boundary reads more data than it can consume.
- Impact: Source proves body-size-proportional transient memory and read work.
  Endpoint behavior, frequency, and user impact are not measured.
- Proposed direction: Read diagnostics through `io.LimitReader` with the logged bound.
- Risks and boundaries: The byte cap does not bound a slow server's wall-clock time.
  Preserve response close, logging, retry, and the 30-minute parent context.
- Verification: Serve large and slow error bodies and assert bounded bytes plus existing cancellation.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce the path.
  Search TailSSH delegated-action error handling.

### ISSUE-2026-051 — tailssh: Equals mapping repeats the same NSS lookup

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven duplicate lookup; performance impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Performance and mapping.
- Publication target: Undecided.
- Summary: A successful `SSHUsers["="]` rule looks up the target user during mapping and again in `clientAuth`.
  Reuse removes one target `getent`, not the probe or group lookup.
- Evidence: `ssh/tailssh/tailssh.go:363-376,1307-1344` owns both target lookups.
  `util/osuser/user.go:109-215` and `group_ids.go:38-58` own the subprocesses.
- Shared change pressure: Not a DRY finding; one authentication evaluation owns repeated metadata.
- Impact: Source proves one redundant target NSS lookup for the accepted equals rule.
  NSS latency, rule frequency, and login impact are not measured.
- Proposed direction: Carry the successful `userMeta` only through the same authentication evaluation.
- Risks and boundaries: Do not add a global cache or reuse metadata during later policy revalidation.
  Preserve explicit mappings, Gokrazy, probes, and group lookup.
- Verification: Count subprocesses across equals, explicit, Gokrazy, password, and revalidation paths.
- Missing publication evidence: Record the exact current `upstream/main` revision and measure NSS latency.
  Search TailSSH user mapping and NSS performance work.

### ISSUE-2026-052 — containerboot: Egress update and error channels can deadlock

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven deadlock path; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Orchestration and lifecycle.
- Publication target: Undecided.
- Summary: The main loop and egress worker exchange updates and errors through unbuffered channels.
  Worker exit can leave each side blocked before the main select can observe the error.
- Evidence: `cmd/containerboot/main.go:920-922,943-958` owns both unbuffered sends.
  `cmd/containerboot/egressservices.go:110-189` performs synchronous work between receives.
- Shared change pressure: One handoff owner must preserve latest state and guaranteed error wakeup.
- Impact: Source proves a deadlock interleaving and main-loop stalls behind synchronization work.
  Occurrence frequency and service impact are not measured.
- Proposed direction: Use capacity-one update and error channels.
  Replace a pending cumulative immutable snapshot with the latest snapshot.
- Risks and boundaries: Do not drop the only error signal or assume non-cumulative events.
  Preserve final-state and resync semantics.
- Verification: Inject slow sync and worker failure during update delivery.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce the deadlock.
  Search containerboot egress handoff issues.

### ISSUE-2026-053 — k8s-operator: Secret comparison always patches existing state

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven branch defect; cluster impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Orchestration and persistence.
- Publication target: Undecided.
- Summary: Secret provisioning compares a `CapabilityVersion` with a `*Secret`.
  The dynamic-type mismatch makes every existing Secret take the patch path.
- Evidence: `cmd/k8s-operator/sts.go:442-475` builds desired data and owns the invalid comparison.
  Managed Secret watches can enqueue their parent resources.
- Shared change pressure: Not a DRY finding; one Secret owner must decide Create, No-op, or Patch.
- Impact: Source proves one unnecessary patch per unchanged existing Secret.
  Event amplification and cluster cost are not measured.
- Proposed direction: Build complete desired `StringData`.
  Create only when absent, return on equal persisted `Data`, and patch only differences.
- Risks and boundaries: Preserve unrelated keys, merge conflicts, capability variants, and serve config.
- Verification: Cover absent, equal, different, extra-key, conflict, and watched-child cases.
- Missing publication evidence: Record the exact current `upstream/main` revision and measure stable reconciliation.
  Search operator Secret patch and child-event issues.

### ISSUE-2026-054 — k8s-operator: DNS record ConfigMap writes unchanged payloads

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven cost path; cluster impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Orchestration and persistence.
- Publication target: Undecided.
- Summary: DNS record reconciliation serializes one target value and always updates the ConfigMap.
- Evidence: `cmd/k8s-operator/dnsrecords.go:314-340` owns serialization and unconditional update.
  Service, Ingress, and EndpointSlice events reach this path.
- Shared change pressure: Not a DRY finding; one ConfigMap owner can compare its serialized field before writing.
- Impact: Source proves avoidable API writes and follow-on events for unchanged DNS records.
  Frequency and cluster cost are not measured.
- Proposed direction: Compare the desired serialized value with the existing key before mutation.
- Risks and boundaries: Preserve optimistic-lock conflict handling and writes for real record changes.
- Verification: Reconcile unchanged and changed records while counting updates and resource versions.
- Missing publication evidence: Record the exact current `upstream/main` revision and measure event frequency.
  Search DNS record ConfigMap and no-op update issues.

### ISSUE-2026-055 — k8s-operator: Tailnet and PeerRelay repeat unchanged status writes

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven cost path; cluster impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Orchestration and state.
- Publication target: Undecided.
- Summary: Four Tailnet exits and two PeerRelay error exits write status without an equality guard.
  Their primary watches can observe the resulting status events.
- Evidence: `k8s-operator/reconciler/tailnet/tailnet.go:157-238` owns the four Tailnet writes.
  `k8s-operator/reconciler/peerrelay/peerrelay.go:209-214,248-254` owns the two error writes.
- Shared change pressure: Both controllers own condition mutation followed by direct status persistence.
- Impact: Source proves avoidable status writes and possible reconcile feedback.
  Loop magnitude and cluster cost are not measured.
- Proposed direction: Snapshot status before owned mutation and update only after semantic inequality.
- Risks and boundaries: Leave PeerRelay `writeStatus` and Server-Side Apply paths unchanged.
  Preserve transition times, generations, conflicts, and real condition changes.
- Verification: Reconcile stable and changing states twice while counting status updates and events.
- Missing publication evidence: Record the exact current `upstream/main` revision and measure feedback.
  Search Tailnet and PeerRelay status-churn issues.

### ISSUE-2026-056 — netstack: Explicit link endpoint close duplicates gVisor ownership

- Status: Rejected.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven false positive.
- Internal priority: Low.
- Confidence: High.
- Type: Lifecycle.
- Publication target: Undecided.
- Summary: The audit proposed calling `linkEP.Close` explicitly from `Impl.Close`.
  Pinned gVisor already closes the NIC link endpoint during `Stack.Wait`.
- Evidence: `wgengine/netstack/netstack.go:449-455` calls stack close and wait.
  Pinned gVisor `Stack.Wait` removes NICs with `closeLinkEndpoint=true`.
- Shared change pressure: Not a DRY finding; the dependency already owns this lifecycle.
- Impact: The proposed additional close would duplicate ownership and could introduce races.
- Proposed direction: No change while rejected.
- Risks and boundaries: Preserve gVisor's close and wait ordering.
  Do not add a second link-endpoint owner.
- Verification: If reopened after a dependency change, trace NIC and link endpoint close calls.
- Missing publication evidence: Reopen only if the pinned gVisor lifecycle contract changes.

### ISSUE-2026-057 — derp: Mesh forwarding allocates one watchdog timer per frame

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven allocation path; performance impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Performance and lifecycle.
- Publication target: Undecided.
- Summary: Every mesh `ForwardPacket` creates and stops a five-second watchdog timer.
  Ordinary client `Send` does not use this path.
- Evidence: `derp/derp_client.go:267-299` owns the timer under the write mutex.
  Mesh forwarding callers reach this method for relayed server traffic.
- Shared change pressure: Not a DRY finding; one serialized client writer could own a reusable watchdog.
- Impact: Source proves timer allocation and runtime timer work per forwarded frame.
  Mesh rate, allocation cost, and throughput impact are not measured.
- Proposed direction: Profile first.
  If material, lazily initialize and reset one client timer under `wmu`.
- Risks and boundaries: Preserve the five-second close guarantee and stop on every return.
  Never arm idle clients or reuse a timer concurrently.
- Verification: Measure allocations and simulate a blocked write plus normal high-rate forwarding.
- Missing publication evidence: Record the exact current `upstream/main` revision and obtain a timer profile.
  Search DERP mesh watchdog optimization work.

### ISSUE-2026-058 — dns: Resolver route selection scans every configured suffix

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven algorithmic cost; performance impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Performance and mapping.
- Publication target: Undecided.
- Summary: Forwarded DNS queries scan the longest-suffix-sorted route slice.
  An index is safe only if it preserves scheme and less-specific fallback semantics.
- Evidence: `net/dns/forwarder.go:460-500,1103-1124` owns route construction and selection.
  `forwarder_test.go:1925-1937` requires fallback after an empty custom-scheme result.
- Shared change pressure: Not a DRY finding; one resolver owner combines suffix matching and fallback policy.
- Impact: Source proves O(routes) matching work per forwarded query.
  Route cardinality, CPU cost, and user impact are not measured.
- Proposed direction: Profile first.
  If material, build an immutable suffix index while retaining every fallback rule.
- Risks and boundaries: Preserve resolver order, label boundaries, root and cloud fallback, and empty routes.
- Verification: Compare answers, fallback decisions, CPU, and memory across high-route workloads.
- Missing publication evidence: Record the exact current `upstream/main` revision and obtain profiles.
  Search DNS split-route indexing and scheme fallback work.

### ISSUE-2026-059 — dns: Delegated queries are parsed and normalized twice

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven repeated work; performance impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Performance and parsing.
- Publication target: Undecided.
- Summary: The resolver parses a query for local decisions, then the forwarder parses the raw bytes again.
  Source comments treat the second parse as a deliberate simplicity tradeoff.
- Evidence: `net/dns/resolver/tsdns.go:405-427,1438-1477` owns the first parse.
  `net/dns/resolver/forwarder.go:1204-1209,1408-1431` owns the second parse.
- Shared change pressure: Both consumers need the same normalized name and type for one query.
- Impact: Source proves duplicate parse and normalization work.
  Allocation, CPU, frequency, and end-to-end impact are not measured.
- Proposed direction: Profile first.
  If material, carry normalized metadata while retaining raw bytes and a low-level parse fallback.
- Risks and boundaries: Preserve malformed-query handling, lowercase normalization, tests, and raw responses.
- Verification: Measure parse costs and compare local, forwarded, peer, malformed, and fallback behavior.
- Missing publication evidence: Record the exact current `upstream/main` revision and obtain profiles.
  Search DNS parser and metadata ownership discussions.

### ISSUE-2026-060 — portmapper: Mapping release performs network work under c.mu

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven lock stall; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Mapping invalidation calls `Release(context.Background())` while holding `c.mu`.
  Moving it out directly can let an old same-port release delete a replacement.
- Evidence: `net/portmapper/portmapper.go:295-410,496-504,767-781` owns invalidation and installs.
  `net/portmapper/upnp.go:113-115,503-509,574-578` proves same-port release and recreation.
- Shared change pressure: One mapping lifecycle owner must serialize detach, release, and replacement.
- Impact: Source proves an indefinite UPnP lock stall and a race in a naive unlock fix.
  Frequency and user-visible outage impact are not measured.
- Proposed direction: Detach and bump generation under `c.mu`.
  Release with a bounded context in a serialized owner and reject stale creations.
- Risks and boundaries: Preserve port reuse, gateway and local-port changes, Close ordering, and protocol variants.
- Verification: Block release and race same-port recreation, network change, and Close.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce both paths.
  Search portmapper release and stale mapping issues.

### ISSUE-2026-061 — controlclient: Streaming map frames zero and copy a temporary slice

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven allocation path; performance impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Performance and parsing.
- Publication target: Undecided.
- Summary: Every streaming map frame grows `msg` through a temporary zeroed slice and `append`.
  Existing capacity can be resliced directly.
- Evidence: `control/controlclient/direct.go:1301-1314` owns frame sizing and read.
- Shared change pressure: Not a DRY finding; one frame-buffer owner controls capacity and length.
- Impact: Source proves avoidable zeroing and copying per frame.
  Allocation rate, copied bytes, CPU, and user impact are not measured.
- Proposed direction: Benchmark first.
  Allocate only when capacity is insufficient and otherwise reslice directly.
- Risks and boundaries: Preserve size validation, read ordering, frame reuse, and error behavior.
- Verification: Measure `allocs/op`, copied bytes, and decoded equality across frame sizes.
- Missing publication evidence: Record the exact current `upstream/main` revision and benchmark map streams.
  Search control streaming-buffer optimization work.

### ISSUE-2026-062 — controlclient: Keepalive cache aliases the reusable frame buffer

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven aliasing defect; performance impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Performance and state.
- Publication target: Undecided.
- Summary: `keepAliveZ` retains a slice alias into the reusable map-frame buffer.
  Later frames can overwrite the cache or leave its old backing array retained.
- Evidence: `control/controlclient/map.go:67` owns the cached keepalive field.
  `control/controlclient/direct.go:1487-1515` stores the reusable `compressedMsg` alias.
- Shared change pressure: Not a DRY finding; the cache requires independent byte ownership.
- Impact: Source proves cache invalidation by buffer reuse and bounded backing-array retention.
  Heap and fast-path impact are not measured.
- Proposed direction: Clone the small compressed keepalive once at ownership transfer.
- Risks and boundaries: Preserve canonical keepalive detection and reuse of the main frame buffer.
- Verification: Reuse and reallocate the frame buffer around keepalives and inspect cache bytes and heap.
- Missing publication evidence: Record the exact current `upstream/main` revision and measure the path.
  Search control keepalive-cache issues.

### ISSUE-2026-063 — ipnlocal: Close can miss a newly published local listener

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven lifecycle race; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Lifecycle and concurrency.
- Publication target: Undecided.
- Summary: `localListener` checks cancellation before `Listen` and publishes its close function later.
  `Close` can run between those operations and miss the resulting listener.
- Evidence: `ipn/ipnlocal/serve.go:152-159,206-227` owns cancellation, publication, and Accept.
  The same listener type is used by local Serve and web-client paths.
- Shared change pressure: Not a DRY finding; listener creation and cancellation require one handoff contract.
- Impact: Source proves a race that can leave one listener and Accept goroutine unowned.
  Occurrence frequency and shutdown impact are not measured.
- Proposed direction: Publish the close function, then recheck cancellation and invoke Close if canceled.
- Risks and boundaries: The post-publication check must close, not merely return.
  Preserve retry, backoff, and shared listener users.
- Verification: Race cancellation against `Listen` before and after publication and count FDs and goroutines.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce the race.
  Search ipnlocal listener lifecycle issues.

### ISSUE-2026-064 — peerapi: Listener close does not own accepted connections

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven lifecycle gap; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: `peerAPIListener.Close` closes only the listener.
  Accepted keepalive and Netstack connections plus their HTTP goroutines remain unregistered.
- Evidence: `ipn/ipnlocal/peerapi.go:129-218` owns listener and per-connection servers.
  `ipn/ipnlocal/local.go:1335,6421,6477-6479,6772` owns close and launch paths.
- Shared change pressure: One PeerAPI lifecycle owner must cover listener, accepted connections, and goroutines.
- Impact: Source proves that active connections can survive listener close and cannot be awaited.
  Connection frequency and production shutdown impact are not measured.
- Proposed direction: Add a closed gate and active connection registry.
  Close and drain accepted work outside `LocalBackend.mu`.
- Risks and boundaries: Do not wait under a lock that handlers can re-enter.
  Preserve active-request semantics and Netstack listeners without an OS listener.
- Verification: Close with idle, active, keepalive, and Netstack connections and assert completion.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce shutdown behavior.
  Search PeerAPI connection ownership issues.

### ISSUE-2026-065 — ipnlocal: Disk cache persistence runs under LocalBackend.mu

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven lock path; performance impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Performance and persistence.
- Publication target: Undecided.
- Summary: Full, delta, and HomeDERP cache writes perform synchronous file work under `LocalBackend.mu`.
  A naive async replacement can reorder writes or resurrect cleared and old-profile state.
- Evidence: `ipn/ipnlocal/local.go:760-798,2426-2573,7433-7453` owns locked callers.
  `ipn/ipnlocal/diskcache.go:17-162` owns persistence, discard, and clear.
- Shared change pressure: One per-profile persistence owner must serialize every cache mutation.
- Impact: Source proves file, JSON, hash, and directory work inside the backend lock.
  Lock wait, storage latency, and user impact are not measured.
- Proposed direction: Profile first.
  If material, use one ordered per-profile owner with immutable inputs, generations, errors, and drain.
- Risks and boundaries: Serialize full, delta, HomeDERP, discard, clear, profile switch, and shutdown.
  Do not use fire-and-forget or a queue that discards pending durable writes.
- Verification: Inject storage latency and reorder, clear, profile, and shutdown interleavings.
- Missing publication evidence: Record the exact current `upstream/main` revision and obtain lock profiles.
  Search LocalBackend cache persistence and ordering work.

### ISSUE-2026-066 — cli: Multi-file Taildrop upload retains every prior descriptor

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven resource cost; user impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Lifecycle and orchestration.
- Publication target: Undecided.
- Summary: `runCp` defers every file close until the whole command returns.
  Uploads are synchronous, so previous descriptors need not remain open.
- Evidence: `cmd/tailscale/cli/file.go:179-200,255` owns open, deferred close, and serial send.
- Shared change pressure: Not a DRY finding; each loop iteration has an independent file owner.
- Impact: Source proves O(file arguments) simultaneous descriptors.
  Shell limits, invocation size, and observed failures are not measured.
- Proposed direction: Scope open, stat, send, and close to one per-file helper.
- Risks and boundaries: Preserve directory checks, progress, errors, and ignored close-error behavior.
- Verification: Upload many files and assert bounded descriptor count across all error paths.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce at scale.
  Search Taildrop multi-file descriptor issues.

### ISSUE-2026-067 — client/local: Status buffers JSON before decoding the full model

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven memory overlap; performance impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Performance and parsing.
- Publication target: Undecided.
- Summary: Status requests read the full response before decoding it.
  Peak memory therefore contains raw JSON and the decoded peer-rich model together.
- Evidence: `client/local/local.go:263-295,767-788` owns buffering and status decode.
  Web status and proxy polling provide repeated production callers.
- Shared change pressure: Not a DRY finding; Status has a typed success body and shared error contracts.
- Impact: Source proves response-size-proportional overlapping memory.
  Tailnet size, peak RSS, latency, and user impact are not measured.
- Proposed direction: Profile first.
  Stream successful Status JSON in a typed path and buffer only non-200 diagnostics.
- Risks and boundaries: Preserve body close, strict trailing JSON, version mismatch, and status errors.
  Do not broadly rewrite the generic response helper.
- Verification: Measure peak RSS and allocations across large status responses and all error classes.
- Missing publication evidence: Record the exact current `upstream/main` revision and obtain memory profiles.
  Search LocalAPI Status decoding work.

### ISSUE-2026-068 — prober: Continuous scheduling uses a negative timeout and double-runs

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven scheduling defect; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Orchestration and lifecycle.
- Publication target: Undecided.
- Summary: Continuous probes run once in the timer arm and again at the next loop top.
  Their negative interval also creates an already expired context for semaphore admission.
- Evidence: `prober/prober.go:313-325,350-357` owns scheduling and admission.
  `prober/derp.go:258-264` registers the production queue-delay probe with a negative interval.
- Shared change pressure: One continuous-mode scheduler owns wait cadence and semaphore context.
- Impact: Source proves duplicate post-wait execution and nondeterministic skipped admission.
  Probe frequency and monitoring impact are not measured.
- Proposed direction: Use `p.ctx` for continuous semaphore admission.
  Remove the timer-arm run so each completed wait leads to one loop-top invocation.
- Risks and boundaries: Preserve positive-interval timeouts, cancellation, concurrency limits, and long-running probes.
- Verification: Count starts with free and saturated semaphores for continuous and ordinary probes.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce the scheduler.
  Search Prober continuous-mode issues.

### ISSUE-2026-069 — k8s-operator: Service events scan every Ingress in a namespace

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven algorithmic cost; cluster impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Performance and mapping.
- Publication target: Undecided.
- Summary: Both Service event mappers list every cached Ingress in the Service namespace.
  They filter backend Service names only after the full informer-cache scan.
- Evidence: `cmd/k8s-operator/operator.go:1271-1305,1807-1837` owns both mappers.
  Existing field-index registration provides the established integration pattern.
- Shared change pressure: Both mappers consume the same nested backend-Service relation.
- Impact: Source proves O(namespace Ingresses) local cache work per Service event.
  Namespace size, event rate, CPU, and reconciliation impact are not measured.
- Proposed direction: Profile first.
  If material, index deduplicated default and path backend Service names once.
- Risks and boundaries: Preserve namespace, class, ProxyGroup, and backend filters.
  Fake clients require the same index during focused tests.
- Verification: Compare mapper results and cache CPU before and after indexing.
- Missing publication evidence: Record the exact current `upstream/main` revision and measure realistic clusters.
  Search operator Service-to-Ingress index work.

### ISSUE-2026-070 — containerboot: Removed Egress FQDNs remain in targetFQDNs

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven stale state; performance impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Performance and state.
- Publication target: Undecided.
- Summary: `targetFQDNs` receives active target keys but never removes deleted configuration.
  The empty-configuration early return preserves all historical keys.
- Evidence: `cmd/containerboot/egressservices.go:200-203,503-537` owns writes and resync scans.
- Shared change pressure: Not a DRY finding; active configuration is the sole owner of target cache membership.
- Impact: Source proves stale memory and extra FQDN-by-peer comparisons.
  Config churn, map growth, and unnecessary resync cost are not measured.
- Proposed direction: Rebuild or prune membership from canonical active configuration before early return.
- Risks and boundaries: Retain active unresolved FQDNs and canonicalize case and trailing dots.
- Verification: Churn services and resolutions while observing map size and resync decisions.
- Missing publication evidence: Record the exact current `upstream/main` revision and measure churn.
  Search containerboot Egress FQDN cache issues.

### ISSUE-2026-071 — appc: Route-count cache proposal lacks measured value

- Status: Rejected.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven scan; qualifying performance value not established.
- Internal priority: Low.
- Confidence: High.
- Type: Performance.
- Publication target: Undecided.
- Summary: `storeRoutesLocked` scans route slices to provide the current count to `rateLogger`.
  The proposed cached count adds mutation invariants without a measured bottleneck.
- Evidence: `appc/appconnector.go:48-59,220-237` owns count consumption and scanning.
- Shared change pressure: Not a DRY finding; one low-frequency reporting path owns the count.
- Impact: The scan is real, but CPU, lock, and user impact are unmeasured.
- Proposed direction: No change while rejected.
  Reopen only if a profile shows material route-count work.
- Risks and boundaries: A cached count must remain correct across every route mutation and rollback.
- Verification: If reopened, profile large route discovery before designing a counter.
- Missing publication evidence: Representative profiles and prior-art research are missing.

### ISSUE-2026-072 — router: cidrDiff copy optimization lacks measured value

- Status: Rejected.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven allocation; qualifying performance value not established.
- Internal priority: Low.
- Confidence: High.
- Type: Performance.
- Publication target: Undecided.
- Summary: `cidrDiff` builds maps on router configuration changes.
  Its result map also preserves partial kernel state after add or delete failures.
- Evidence: `wgengine/router/osrouter/router_linux.go:465-482,1856-1913` owns calls and failure state.
- Shared change pressure: Not a DRY finding; one configuration helper owns normal and partial-failure results.
- Impact: An unchanged fast path could avoid one copy, but no meaningful cost is measured.
- Proposed direction: No change while rejected.
  Reopen only with a representative allocation profile.
- Risks and boundaries: Do not weaken partial-failure state reporting for a configuration-path micro-optimization.
- Verification: If reopened, benchmark unchanged and failure cases before altering return ownership.
- Missing publication evidence: Representative profiles and prior-art research are missing.

### ISSUE-2026-073 — drive: Child process publication races Close and cleanup

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven lifecycle gaps; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Lifecycle and concurrency.
- Publication target: Undecided.
- Summary: `Close` can miss a child between `Start` and publication to `s.cmd`.
  Retry sleep is uninterruptible, and scanner errors return without `cmd.Wait`.
- Evidence: `drive/driveimpl/remote_impl.go:270-321,371-383` owns Close, backoff, publication, and scan.
  Share-worker publication can also race filesystem shutdown.
- Shared change pressure: One user-server owner must publish, stop, wait, and retry each child.
- Impact: Source proves races that can leave a long-lived child or worker after Close.
  Occurrence frequency and resource impact are not measured.
- Proposed direction: Add a done channel and timer-select backoff.
  Serialize child publication with Close and ensure every started child receives exactly one Kill and Wait.
- Risks and boundaries: Preserve restart behavior, share updates, output scanning, and command cleanup.
  Avoid a second worker registry without clear ownership.
- Verification: Race Close with Start, scanner failure, retry sleep, and share replacement.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce lifecycle leaks.
  Search Drive and Taildrive child-process issues.

### ISSUE-2026-074 — ipnlocal: Peer removal loses its StableNodeID before cache update

- Status: Published.
- Delivery mode: Issue.
- Location: https://github.com/tailscale/tailscale/issues/20796
- Evidence class: Observed and source-proven; production startup impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: new issue.
- Summary: `UpdateNetmapDelta` applies peer removal before resolving the removed peer's StableNodeID.
  The updated node backend no longer contains the peer, so the disk cache receives no removal.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `ipn/ipnlocal/local.go:2441` applies mutations before the cache-removal lookup at lines 2565-2569.
  A focused `TestUpdateNetMapCache` overlay removed peer 601 through `UpdateNetmapDelta`.
  Reloading the production FileStore still returned the removed peer.
  Pull request #20111 introduced the delta-cache path; #20132 cherry-picked it to `release-branch/1.100`.
  Pull request #20210 makes post-filter, pre-application identity capture necessary.
  Pull requests #20141 and #20147 address separate live-engine delta regressions.
  Focused issue, pull-request, and discussion searches found no exact duplicate or active competing fix.
- Shared change pressure: One delta owner must preserve removed-peer identity until every downstream consumer uses it.
- Impact: A peer removed by a delta remains in the FileStore and is returned by a fresh `Cache.Load`.
  A later cached startup can therefore install it.
  Actual startup behavior, user-visible effects, production frequency, and affected releases are not measured.
- Proposed direction: Capture StableNodeIDs after TKA mutation filtering and before applying the delta.
  Continue resolving updated peer views after application, then pass both sets to `writePeerDeltaToDiskLocked`.
- Risks and boundaries: TKA filtering can rewrite an upsert into a removal.
  Capture identity after filtering, preserve post-application update lookup, and avoid retaining full removed Node values.
  A later full-map Store or explicit cache clear can repair the key, so do not characterize the stale state as permanent.
- Verification: A focused temporary production-FileStore subcase in `TestUpdateNetMapCache` failed against current source.
  Capturing StableNodeIDs before `cn.UpdateNetmapDelta` made that same disposable subcase pass.
  The current checked-in test does not cover delta removal followed by a fresh cache load.
- Missing publication evidence: None.
  Published as https://github.com/tailscale/tailscale/issues/20796 after exact draft and target approval.

### ISSUE-2026-075 — derper: Rate limiting terminates the entire DERP service during load spikes

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: The listener must reject only excess connections and continue serving.
  Instead, `errLimitedConn` ends `ServeTLS`, after which `main` calls `Fatalf`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/derper/derper.go:477-527`.
  `net/http` retries temporary `net.Error` failures, not plain error values.
- Shared change pressure: Not a DRY finding; `cmd/derper/derper.go` owns listener error classification.
- Impact: Source proves a load spike can end the DERP process and disconnect all relay connections.
  The production frequency of such termination is unmeasured.
- Proposed direction: Return rejections as temporary network errors so the listener continues serving.
- Risks and boundaries: The fix must preserve per-connection rejection while retaining listener ownership and cleanup.
- Verification: A focused test should exhaust a burst and then complete HTTPS against the same running process.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior derper issues and pull requests for the same root cause.

### ISSUE-2026-076 — osrouter: Empty OpenBSD configuration leaves all prior router state active

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: `Set(nil)` and empty configurations must remove all router state.
  Instead, an early return skips every removal and reports success.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `wgengine/router/osrouter/router_openbsd.go:85-93`.
  Production reset paths pass exactly this empty configuration to the OpenBSD router.
- Shared change pressure: Not a DRY finding; the OpenBSD router owns empty-configuration reset behavior.
- Impact: Source proves addresses, routes, and bypass state remain active after a reported successful reset.
  The production frequency and duration of retained state are unmeasured.
- Proposed direction: Let empty configurations proceed through the existing complete removal path.
- Risks and boundaries: The fix must preserve OpenBSD cleanup ordering and remove only router-managed state.
- Verification: A focused test should configure and clear the router, then inspect all kernel routes and addresses.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior osrouter issues and pull requests for the same root cause.

### ISSUE-2026-077 — osrouter: Failed OpenBSD routes remain permanently recorded as applied

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: The cache must describe only successfully applied kernel state.
  Instead, address and route caches update even after commands fail.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `wgengine/router/osrouter/router_openbsd.go:112-246`.
  The next identical `Set` therefore skips the failed operations.
- Shared change pressure: Not a DRY finding; the OpenBSD router owns applied-state cache commits.
- Impact: Source proves missing or stale kernel routes can survive identical retry attempts.
  The production frequency and persistence of divergence are unmeasured.
- Proposed direction: Commit each cache entry to its target state only after that operation succeeds.
- Risks and boundaries: The fix must preserve partial-success state and keep failed operations retryable.
- Verification: A focused test should fail a route command and confirm an identical `Set` retries it.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior osrouter issues and pull requests for the same root cause.

### ISSUE-2026-078 — osrouter: Failed OpenBSD bypass setup is marked successful

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Default-route bypass state must be set only after successful routing-table setup.
  Instead, an error is only logged and `areDefaultRoute` is still set to true.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `wgengine/router/osrouter/router_openbsd.go:256-264`.
  Identical later configurations then skip the required bypass setup entirely.
- Shared change pressure: Not a DRY finding; the OpenBSD router owns default-route bypass state transitions.
- Impact: Source proves system and control connections can incorrectly follow the Tailscale default route.
  The production frequency of incorrect routing is unmeasured.
- Proposed direction: Return the setup error and set success state only after bypass setup succeeds.
- Risks and boundaries: The fix must preserve prior bypass state on failure and keep setup retryable.
- Verification: A focused test should fail bypass setup and confirm a retry attempts it again and reports the error.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior osrouter issues and pull requests for the same root cause.

### ISSUE-2026-079 — osrouter: BSD routers store failed routes as current state

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: The route cache must reflect successfully applied Darwin and FreeBSD routes.
  Instead, `r.routes` receives the target state despite add or delete errors.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `wgengine/router/osrouter/router_userspace_bsd.go:163-204`.
  Equivalent later calls see no difference and skip repair.
- Shared change pressure: Not a DRY finding; the userspace BSD router owns route-cache convergence.
- Impact: Source proves missing or unwanted routes can remain without another repair attempt.
  The production frequency and duration of stale routes are unmeasured.
- Proposed direction: Update the cache separately for successful and failed route operations.
- Risks and boundaries: The fix must preserve partial add and delete successes on Darwin and FreeBSD.
- Verification: A focused test should fail add and delete separately and confirm identical calls retry both.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior osrouter issues and pull requests for the same root cause.

### ISSUE-2026-080 — controlbase: Cancellation after HTTP upgrade does not stop the Noise handshake

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: The dial context must bound the full connection setup until return.
  Instead, after status 101, the blocking Noise handshake ignores `ctx.Done`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `control/controlbase/handshake.go:120-155`.
  `io.ReadFull` has only an absolute deadline, with no cancellation-to-close transition.
- Shared change pressure: Not a DRY finding; `controlbase` owns cancellation during the Noise handshake.
- Impact: Source proves canceled control connections can block until the deadline or indefinitely.
  The production frequency and blocking duration are unmeasured.
- Proposed direction: Observe context cancellation during the handshake and close the underlying connection.
- Risks and boundaries: Preserve deadlines and make concurrent cancellation close the socket once.
- Verification: A focused test should cancel after status 101 and expect prompt return with the socket closed.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-081 — derper: DERP WebSocket setup loses cancellation and socket ownership

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven under js or ts_debug_websockets builds; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Cancellation must terminate setup of a WebSocket-based DERP connection.
  Instead, setup uses `context.Background` after the WebSocket dial.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `derp/derphttp/derphttp_client.go:338-467`.
  The source closes nil `tcpConn`; build reachability exists only with js or ts_debug_websockets.
- Shared change pressure: Not a DRY finding; the DERP HTTP client owns WebSocket setup context and socket cleanup.
- Impact: Source proves a missing DERP greeting can retain Close, a mutex, a goroutine, and a socket.
  The production effect is unmeasured outside js or ts_debug_websockets builds.
- Proposed direction: Bind setup to the WebSocket context and fully close resources on pre-setup errors.
- Risks and boundaries: Preserve socket ownership and avoid double-close on cancellation or setup failure.
- Verification: A focused test should withhold the DERP greeting and expect cancellation to close setup and the peer.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior derper issues and pull requests for the same root cause.

### ISSUE-2026-082 — netmon: Windows network monitor can block forever during close

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: `Monitor.Close` must stop operating-system callbacks without reciprocal locking.
  Instead, Close holds `Monitor.mu` while Unregister waits for a callback.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `net/netmon/netmon.go:509-531; net/netmon/netmon_windows.go:77-190`.
  The callback needs the same mutex through `isActive`, creating lock inversion.
- Shared change pressure: Not a DRY finding; the Windows network monitor owns callback unregistration order.
- Impact: Source proves the Windows daemon can wait forever for a callback during shutdown.
  The production frequency of this deadlock is unmeasured.
- Proposed direction: Unregister the operating-system monitor outside `Monitor.mu`, then finalize state.
- Risks and boundaries: The fix must preserve callback concurrency and prevent callbacks from observing finalized state.
- Verification: A focused test should pause a callback in `isActive` and confirm concurrent Close completes.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior netmon issues and pull requests for the same root cause.

### ISSUE-2026-083 — tsdial: PeerAPI-free builds panic on every dialer close

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: Feature-omit tags must produce runnable binaries without removed components.
  Instead, `Close` unconditionally invokes the intentionally panicking PeerAPI stub.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `net/tsdial/tsdial.go:192-208,713-735`.
  `feature/buildfeatures` marks PeerAPI absent, but the call remains.
- Shared change pressure: Not a DRY finding; `tsdial.Close` owns shutdown dispatch for optional PeerAPI support.
- Impact: Source proves `ts_omit_peerapiclient` binaries panic during normal `tsnet` shutdown.
  The production use and frequency of this build variant are unmeasured.
- Proposed direction: Call `CloseIdleConnections` only when `HasPeerAPIClient` is active.
- Risks and boundaries: The fix must preserve normal PeerAPI cleanup and the `ts_omit_peerapiclient` build boundary.
- Verification: A focused test should build with `ts_omit_peerapiclient` and normally close a `tsnet.Server`.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsdial issues and pull requests for the same root cause.

### ISSUE-2026-084 — netstack: SOCKS proxy drops responses after a valid client half-close

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: EOF in one TCP direction must allow the opposite direction to finish.
  Instead, the first completed `io.Copy` immediately closes both connections.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `net/socks5/socks5.go:245-260`.
  `io.Copy` returns nil on client EOF while the server response transfer remains incomplete.
- Shared change pressure: Not a DRY finding; the SOCKS5 proxy owns bidirectional TCP copy completion.
- Impact: Source proves request-response protocols can lose replies after the client closes its write side.
  The production frequency and amount of lost response data are unmeasured.
- Proposed direction: Half-close each write side as appropriate and wait for both copy directions.
- Risks and boundaries: The fix must preserve full-duplex concurrency, half-close semantics, and final socket cleanup.
- Verification: A focused test should make the backend reply after EOF and expect the client to receive it all.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior netstack issues and pull requests for the same root cause.

### ISSUE-2026-085 — controlbase: Failed logout locally clears the active control key

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: A failed logout must preserve the prior session and its node key.
  `TryLogout` clears `c.persist` regardless of the returned error.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `control/controlclient/direct.go:547-557`.
  Auto and LocalBackend retain profile and key state in parallel, allowing divergence.
- Shared change pressure: Not a DRY finding; `TryLogout` owns the logout transaction and persisted client state.
- Impact: A network error makes the same control client unusable for later logout attempts.
  The production frequency of this failure is unmeasured.
- Proposed direction: Commit the empty persistence view only after logout succeeds.
- Risks and boundaries: Preserve the prior session and node key on every failed logout path.
- Verification: Fail one logout, verify the key remains, then complete a second attempt successfully.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-086 — derper: Conn25 shutdown permits spin loops and send-on-closed-channel panics

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Shutdown must stop consumers and safely reject later producers.
  The work channel closes while producers can still send to it.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `feature/conn25/conn25.go:336-344,954-990`.
  The consumer omits the receive `ok` check and processes endless zero values.
- Shared change pressure: Not a DRY finding; the Conn25 extension owns work admission and shutdown sequencing.
- Impact: Concurrent shutdown can panic the daemon or flood CPU and logs.
  The production incidence of either outcome is unmeasured.
- Proposed direction: Cancel only through context and synchronize producer admission with shutdown.
- Risks and boundaries: Keep producer admission and consumer cancellation race-free during shutdown.
- Verification: Deterministically interleave assignment and shutdown; require neither panic nor spin.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior derper issues and pull requests for the same root cause.

### ISSUE-2026-087 — k8s-operator: Failed auth-key update blocks all later reissuance

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Reconcile errors must retry until the Secret is updated.
  The in-flight marker is set before the failing Secret commit.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/k8s-operator/proxygroup.go:914-1034; tsrecorder.go:478-535`.
  The next reconcile sees the marker and refuses the required retry.
- Shared change pressure: Not a DRY finding; the auth-key reconciler owns marker publication after Secret commit.
- Impact: ProxyGroups and recorders remain unauthenticated after a Secret update failure.
  The production frequency of this state is unmeasured.
- Proposed direction: Set the marker only after a successful update, or clear it on every error.
- Risks and boundaries: Preserve retryability across Secret conflicts without enabling duplicate in-flight work.
- Verification: Inject a Secret conflict; the next reconcile must retry auth-key reissuance.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior k8s-operator issues and pull requests for the same root cause.

### ISSUE-2026-088 — k8s-operator: Nil endpoint conditions repeatedly panic the readiness controller

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven panic path; controller-runtime recovery and production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: Nil EndpointSlice conditions must use Kubernetes-defined defaults.
  `Ready`, `Serving`, and `Terminating` are dereferenced unconditionally.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/k8s-operator/egress-services-readiness.go:180-225`.
  Another production reconciler temporarily writes these condition pointers as nil.
- Shared change pressure: Not a DRY finding; the readiness controller owns EndpointSlice condition interpretation.
- Impact: Egress readiness remains indeterminate while the controller repeatedly panics and requeues.
  `controller-runtime` recovers the panics; no operator-process crash is claimed.
- Proposed direction: Evaluate every condition nil-safely using the Kubernetes defaults.
- Risks and boundaries: Preserve Kubernetes semantics for all nil and non-nil condition combinations.
- Verification: Reconcile an EndpointSlice with all three conditions nil and check correct readiness.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior k8s-operator issues and pull requests for the same root cause.

### ISSUE-2026-089 — k8s-operator: Current ProxyGroup lookup prevents cleanup of former owners

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Deletion, annotation removal, and owner changes must clean up the former owner.
  A missing current ProxyGroup entry ends reconcile before any cleanup.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/k8s-operator/ingress-for-pg.go:118-145; svc-for-pg.go:100-135`.
  The former owner is neither stored nor rediscovered through the current indexes.
- Shared change pressure: Not a DRY finding; the reconciler owns former-owner identity and cleanup ordering.
- Impact: Finalizers, certificates, RBAC, and old serve configurations remain indefinitely.
  The production prevalence of these leftovers is unmeasured.
- Proposed direction: Persist the last owner and clean it up before the current ProxyGroup lookup.
- Risks and boundaries: Preserve ownership boundaries while cleaning deletion, annotation removal, and owner changes.
- Verification: Remove the annotation, delete the ProxyGroup, and change owners; old resources must disappear.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior k8s-operator issues and pull requests for the same root cause.

### ISSUE-2026-090 — containerboot: Container reaper masks every unsuccessful tailscaled exit

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: CLI correctness.
- Publication target: Undecided.
- Summary: Daemon failures must trigger the documented crash loop and container restart.
  The reaper discards `WaitStatus` and calls `os.Exit(0)` unconditionally.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/containerboot/main.go:983-995`.
  The complete exit status is available but is not evaluated before process termination.
- Shared change pressure: Not a DRY finding; the container reaper owns child-status propagation to container exit.
- Impact: A fatal `tailscaled` failure incorrectly ends the container with status zero.
  The production frequency of masked failures is unmeasured.
- Proposed direction: Propagate normal nonzero exits and signal-caused failures as nonzero status.
- Risks and boundaries: Preserve successful zero exits while mapping normal and signaled failures correctly.
- Verification: Exit the child with status 17; containerboot must also report failure.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior containerboot issues and pull requests for the same root cause.

### ISSUE-2026-091 — containerboot: Initial ProxyGroup status patch terminates containerboot with an error

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: The first status write must create a missing Secret key.
  JSON Patch `replace` requires the data member to exist already.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/containerboot/egressservices.go:411-453; ingressservices.go:145-202`.
  The operator template initially creates the state Secret with no `Data` at all.
- Shared change pressure: Not a DRY finding; containerboot owns creation of the first ProxyGroup status member.
- Impact: New ingress or egress ProxyGroups crash-loop before their first status report.
  The production frequency of this startup failure is unmeasured.
- Proposed direction: Create missing members with `add` or a strategic data patch.
- Risks and boundaries: Preserve updates to existing status keys while supporting a Secret with no `Data`.
- Verification: Apply the first patch to a fresh state Secret without a status key; it must succeed.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior containerboot issues and pull requests for the same root cause.

### ISSUE-2026-092 — tsdial: Windows proxy cache survives requested network invalidation

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven stale-cache path; network-change reachability is conditional.
- Internal priority: Medium.
- Confidence: Medium.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: A network change must invalidate every dependent proxy decision.
  The hook clears only backoff state, not `cachedProxy.val`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `net/tshttpproxy/tshttpproxy_windows.go:80-119`.
  After a WinHTTP timeout, the old proxy is returned with a nil error.
- Shared change pressure: Not a DRY finding; the Windows proxy cache owns proxy invalidation across network changes.
- Impact: Roaming can keep sending control traffic to an unreachable old proxy.
  Network-change reachability is conditional, and production effect is unmeasured.
- Proposed direction: Invalidate the platform cache and generation-guard results from late old requests.
- Risks and boundaries: Prevent late requests from restoring stale state, and bound any proposed wait with a timeout.
- Verification: Cache proxy P1, trigger a network change, and exclude P1 after a timeout-bounded check.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsdial issues and pull requests for the same root cause.

### ISSUE-2026-093 — controlbase: AppConnector commits routes before successful backend application

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Persisted RouteInfo must describe routes successfully applied by the backend.
  The queue, event, and cache advance before the backend result is known.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `appc/appconnector.go:353-397`.
  Backend errors are only logged; the same route state then takes an early return.
- Shared change pressure: Not a DRY finding; AppConnector owns the commit boundary between desired and applied routes.
- Impact: Failed DNS routes are persisted and never repaired by identical updates.
  The production frequency of unrepaired routes is unmeasured.
- Proposed direction: Commit only after confirmed backend application and keep failed work retryable.
- Risks and boundaries: Preserve route ordering and retryability without committing failed backend work.
- Verification: Make `AdvertiseRoute` fail; an identical later update must attempt application again.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-094 — controlbase: Stale AppConnector events mutate the newly selected profile

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Profile switches must isolate all profile-bound routes and persisted data.
  Events lack a generation, and the connector is reused across profile switches.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `ipn/ipnlocal/local.go:730-753,8333-8373,8588-8627`.
  Callback processing always targets the profile current at execution time.
- Shared change pressure: Not a DRY finding; LocalBackend owns profile generation and AppConnector lifecycle.
- Impact: Profile A can write routes or RouteInfo into profile B.
  The production frequency of cross-profile mutation is unmeasured.
- Proposed direction: Carry the profile generation, drop stale work, and recreate the connector on profile change.
- Risks and boundaries: Preserve profile isolation while canceling stale asynchronous work during switches.
- Verification: Pause an event across a profile switch; profile B must remain unchanged afterward.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-095 — controlbase: Concurrent AWS state writes can permanently overwrite newer data

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Concurrent `StateStore` writes must be safe and retain every successful write.
  Export and `PutParameter` run outside one shared write lock, allowing an older snapshot to win.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `ipn/store/awsstore/store_aws.go:224-264`.
  A delayed write A can commit after newer write B and replace B's confirmed state.
- Shared change pressure: Not a DRY finding; AWS `StateStore` commit ordering is the single decision owner.
- Impact: Source proves that an older SSM snapshot can overwrite newer confirmed state.
  Production frequency and scale are unmeasured.
- Proposed direction: Serialize the full export-and-put operation, or use a conditional versioned commit.
- Risks and boundaries: Preserve concurrency safety and prevent stale commits without losing successful writes.
- Verification: Control the order of two writes; remote state must contain both changes.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-096 — controlbase: EditPrefs confirms changes after durable storage fails

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: `EditPrefs` must return an error when requested changes cannot be applied.
  `pm.SetPrefs` errors are only logged and hidden behind HTTP 200.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `ipn/ipnlocal/local.go:5427-5551`.
  Memory changes before the failed store write, and that state blocks a retry.
- Shared change pressure: Not a DRY finding; `EditPrefs` commit handling is the single decision owner.
- Impact: Source proves that clients see success although restart can lose the accepted configuration.
  Production frequency and scale are unmeasured.
- Proposed direction: Propagate persistence errors through the public API and roll back failed commits.
- Risks and boundaries: Keep in-memory and durable preferences aligned across failures and retries.
- Verification: Force a `StateStore` write failure; `EditPrefs` must return an error instead of success.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-097 — tailssh: New SSH connection escapes server shutdown

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Shutdown must close every accepted SSH connection, including connections still being established.
  The shutdown check and insertion into `activeConns` are not atomic.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `ssh/tailssh/tailssh.go:132-192,472-481`.
  Shutdown can close an empty map and return between the check and registration.
- Shared change pressure: Not a DRY finding; SSH connection registration is the single decision owner.
- Impact: Source proves that a connection can continue handshake and forwarding after shutdown completes.
  Production frequency and scale are unmeasured.
- Proposed direction: Lock the shutdown gate with registration, and immediately close the losing socket.
- Risks and boundaries: Preserve connection ownership, shutdown ordering, and socket cleanup under concurrency.
- Verification: Pause before the map insert; shutdown must not return before the socket closes.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tailssh issues and pull requests for the same root cause.

### ISSUE-2026-098 — tailssh: Blocking control notification delays mandatory SSH enforcement

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Recording failures must immediately trigger the configured rejection or termination.
  An unbounded synchronous notification runs before the required policy action.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `ssh/tailssh/tailssh.go:1453-1501,1574-1585`.
  The Noise HTTP client has no timeout while response headers are withheld.
- Shared change pressure: Not a DRY finding; SSH recording-policy enforcement order is the single decision owner.
- Impact: Source proves that an unrecorded session can continue despite reject or terminate policy.
  Production frequency and scale are unmeasured.
- Proposed direction: Enforce first, then decouple notification with its own deadline context.
- Risks and boundaries: Preserve reject and terminate semantics while bounding notification concurrency and cleanup.
- Verification: Block the control response; the session must still be rejected or terminated immediately.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tailssh issues and pull requests for the same root cause.

### ISSUE-2026-099 — tsdial: Netcat exits before a delayed server response completes

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: CLI correctness.
- Publication target: Undecided.
- Summary: Client EOF must close only the TCP write half while server output continues to be copied.
  The first completed copy direction ends `runNC` and closes the whole connection.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `cmd/tailscale/cli/nc.go:74-83`.
  Piped input commonly reaches EOF before a response generated only after that EOF.
- Shared change pressure: Not a DRY finding; `runNC` stream lifecycle is the single decision owner.
- Impact: Source proves that valid server responses can be truncated while the command exits successfully.
  Production frequency and scale are unmeasured.
- Proposed direction: Half-close the write side, then keep copying until server output ends.
- Risks and boundaries: Preserve stream cleanup and exit status while waiting for delayed server output.
- Verification: Make the server respond only after EOF; output must be complete and exit must succeed.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsdial issues and pull requests for the same root cause.

### ISSUE-2026-100 — controlbase: Serve succeeds without the configured service advertisement

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: CLI correctness.
- Publication target: Undecided.
- Summary: Successful `serve --service` must configure and advertise the same service.
  The error from `addServiceToPrefs` is discarded while handlers are still saved.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `cmd/tailscale/cli/serve_v2.go:519-580`.
  The dedicated `serve advertise` path correctly propagates the same helper error.
- Shared change pressure: Not a DRY finding; `serve --service` transaction ordering is the single decision owner.
- Impact: Source proves that handlers can be saved while the service stays invisible and unreachable.
  Production frequency and scale are unmeasured.
- Proposed direction: Return a clear advertisement error before any ServeConfig mutation.
- Risks and boundaries: Preserve atomicity between preference advertisement and ServeConfig persistence.
- Verification: Make `EditPrefs` fail; ServeConfig must not be written.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-101 — controlbase: Port range ending at 65535 continues after counter overflow

- Status: Published.
- Delivery mode: Pull request.
- Location: https://github.com/tailscale/tailscale/pull/20798
- Evidence class: Observed and source-proven; production frequency not measured.
- Internal priority: High.
- Confidence: High.
- Type: CLI correctness.
- Publication target: new pull request.
- Summary: Every port in a closed range must be applied exactly once and configuration must terminate.
  A `uint16` counter wraps from 65535 to zero and continues.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `cmd/tailscale/cli/serve_v2.go:934-956`.
  File and Unix targets accept the wrapped values without an error that stops the loop.
  Before the fix, a focused Unix-target fixture using `tcp:65535` timed out after eight seconds with exit status 124.
  Upstream issue #20873 independently reproduces the wrap as `invalid port "0"` with an HTTP target.
  Pull request #17435 introduced the range loop, and #19684 later added the exercised Unix target path.
  Focused issue and pull-request searches found no same-root duplicate or active competing fix.
  Reviewer feedback restored `new_format_all_no_warning` and moved the boundary case into the dedicated
  `TestRunServeSetConfig/max_port_does_not_wrap` regression subtest.
  Branch `pr/issue-2026-101-serve-port-range` commit `9d86de022` is pushed to `origin`.
- Shared change pressure: Not a DRY finding; Serve port-range iteration is the single decision owner.
- Impact: Source proves that valid Serve configurations can block the CLI process indefinitely.
  Production frequency and scale are unmeasured.
- Proposed direction: Break explicitly after processing `Last`.
- Risks and boundaries: Preserve closed-range semantics at the `uint16` boundary without duplicate ports.
- Verification: `./tool/go test ./cmd/tailscale/cli -run '^TestRunServeSetConfig$' -count=1` passed after review updates.
  The dedicated maximal-port subtest applies the HTTP handler at TCP port 65535 and returns instead of wrapping to zero.
- Missing publication evidence: None.
  Published as https://github.com/tailscale/tailscale/pull/20798 after exact draft and target approval.

### ISSUE-2026-102 — netstack: Late port mapping survives Close and local port changes

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Close and `SetLocalPort` must invalidate every mapping from an older generation.
  Background work can commit later without a closed-state or generation check.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `net/portmapper/portmapper.go:301-327,477-781`.
  A late result can install the old port after invalidation has already completed.
- Shared change pressure: Not a DRY finding; port-mapping generation commit is the single decision owner.
- Impact: Source proves that router mappings can remain unmanaged after shutdown or a port change.
  Production frequency and scale are unmeasured.
- Proposed direction: Capture the generation at start and immediately release stale results.
- Risks and boundaries: Preserve lifecycle invalidation, concurrent result handling, and stale router-rule cleanup.
- Verification: Delay the mapping response, call Close, and verify that no router rule remains.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior netstack issues and pull requests for the same root cause.

### ISSUE-2026-103 — clientupdate: Windows MSI retry can remove the installed product

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: A failed update must preserve the previously working installation.
  Any generic first failure triggers uninstall before a second installation attempt.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `clientupdate/clientupdate_windows.go:201-267`.
  If the second installation also fails, the source provides no recovery path.
- Shared change pressure: Not a DRY finding; the Windows MSI update transaction is the single decision owner.
- Impact: Source proves that two installation failures can leave no client installed.
  Production frequency and scale are unmeasured.
- Proposed direction: Uninstall only for confirmed downgrades and keep the prior installation recoverable.
- Risks and boundaries: Preserve product availability, rollback state, and Windows MSI downgrade handling.
- Verification: Make both installation attempts fail; the old product must remain installed.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior clientupdate issues and pull requests for the same root cause.

### ISSUE-2026-104 — controlbase: Web self-update reaches neither install nor progress endpoint

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: The web route must proxy installation POST and progress GET requests to LocalAPI.
  Install is absent, and the proxy permits only POST for Progress.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `client/web/web.go:599-632; src/hooks/self-update.ts:39-59`.
  LocalAPI accepts POST for install, while its progress handler accepts only GET.
- Shared change pressure: Not a DRY finding; the web-to-LocalAPI proxy allowlist is the single decision owner.
- Impact: Source proves that “Update now” starts no update and later always reports failure.
  Production frequency and scale are unmeasured.
- Proposed direction: Allow both routes in the proxy with their actual HTTP methods.
- Risks and boundaries: Preserve endpoint method boundaries and avoid broadening unrelated proxy access.
- Verification: Start a web update and follow it through the successful progress end state.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-105 — doctor: Linux Capget writes beyond an undersized buffer

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: `LINUX_CAPABILITY_VERSION_3` requires two `CapUserData` entries for `Capget`.
  The kernel receives a pointer to only one `CapUserData` entry.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `doctor/permissions/permissions_linux.go:45-52` contains the affected `Capget` call.
  The in-tree TailSSH caller explicitly uses a two-entry array.
- Shared change pressure: Not a DRY finding; the Linux doctor `Capget` call owns its ABI buffer sizing.
- Impact: A Doctor call can corrupt adjacent daemon memory or crash tailscaled.
  The supplied finding does not measure production frequency.
- Proposed direction: Allocate two entries and pass the first array pointer to `Capget`.
- Risks and boundaries: Preserve the Linux capability ABI layout and both capability words.
- Verification: Run Doctor under memory diagnostics and check that both capability words are read correctly.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior doctor issues and pull requests for the same root cause.

### ISSUE-2026-106 — controlbase: Synology migration moves persistent state into the name mem

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Provider prefixes select stores and are not filesystem destinations.
  Migration treats `mem:` as a filename and renames the old state into it.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `cmd/tailscaled/tailscaled.go:352-381,524-529` contains migration and store selection.
  The later store setup still uses the memory provider and ignores the renamed file.
- Shared change pressure: Not a DRY finding; the tailscaled migration gate owns the file-path decision.
- Impact: `--state=mem:` removes old node state from its expected persistent path.
  The supplied finding does not measure production occurrence.
- Proposed direction: Run migration only for real file paths and skip provider prefixes.
- Risks and boundaries: Preserve persistent state and avoid moving it for `mem:`, `kube:`, or `arn:` providers.
- Verification: Start with old state and `mem:`, `kube:`, and `arn:`; verify that nothing is renamed.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-107 — tsnet: Failed tsnet Serve reset is never retried

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Every successful `Up` must clear stale persisted Serve state.
  `sync.Once` is consumed even when the first reset fails.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `tsnet/tsnet.go:566-587` contains the one-shot startup cleanup.
  A later `Up` skips cleanup, while `ListenService` relies on cleanup succeeding.
- Shared change pressure: Not a DRY finding; the tsnet startup cleanup state owns retry eligibility.
- Impact: Stale Serve handlers can keep later service listeners marked as already occupied.
  The supplied finding does not quantify production occurrence.
- Proposed direction: Replace sync.Once with retryable success state, not an unconditional reset.
- Risks and boundaries: Preserve concurrent `Up` safety and do not mark failed cleanup as successful.
- Verification: Disrupt the first reset, repeat `Up`, and verify that the same service can then start.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsnet issues and pull requests for the same root cause.

### ISSUE-2026-108 — derper: DERP connection can survive completed server Close

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: `Close` must close and wait for every accepted DERP connection.
  `Accept` can add to `netConns` after the Close snapshot.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `derp/derpserver/derpserver.go:606-664` contains shutdown and connection registration.
  `Accept` does not check the closed server state when registering a connection.
- Shared change pressure: Not a DRY finding; DERP connection registration owns admission during shutdown.
- Impact: A late connection can remain blocked or process traffic after shutdown.
  The supplied finding does not quantify production occurrence.
- Proposed direction: Synchronize shutdown with registration and immediately close late connections.
- Risks and boundaries: Preserve connection tracking, shutdown waiting, and concurrent `Accept` behavior.
- Verification: Insert `Accept` between the snapshot and close; verify that the connection cannot survive.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior derper issues and pull requests for the same root cause.

### ISSUE-2026-109 — derper: Failed STUN bind is invisible to operators

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Enabled STUN must start or produce a visible startup error.
  The goroutine discards every error returned by `ListenAndServe`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `cmd/derper/derper.go:183-186` launches the affected STUN goroutine.
  The standalone `stund` command explicitly treats the same bind error as fatal.
- Shared change pressure: Not a DRY finding; derper startup owns propagation of the STUN bind result.
- Impact: DERP can appear healthy while the configured STUN service is entirely absent.
  The supplied finding does not quantify production occurrence.
- Proposed direction: Bind synchronously or propagate startup errors through a monitored error channel.
- Risks and boundaries: Preserve DERP startup lifecycle and avoid leaking a failed STUN goroutine.
- Verification: Occupy the STUN port; verify that derper visibly reports the error or fails startup.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior derper issues and pull requests for the same root cause.

### ISSUE-2026-110 — netstack: DNS forwarder sends SERVFAIL while also returning an error

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: DNS forwarding must return a response with nil, or an error without a sent response.
  The all-error branch sends SERVFAIL and still returns `firstErr`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `net/dns/resolver/forwarder.go:1196-1204,1335-1382` contains the affected paths.
  Resolver callers prioritize the error and do not read the buffered response.
- Shared change pressure: Not a DRY finding; the DNS forwarder owns response-or-error completion.
- Impact: Clients can miss the immediate SERVFAIL response and wait or retry unnecessarily.
  The supplied finding does not quantify production occurrence.
- Proposed direction: Return nil after a successful send and mark transport errors unhealthy separately.
- Risks and boundaries: Preserve exactly one DNS completion and retain transport health accounting.
- Verification: Fail all upstreams; verify that exactly one SERVFAIL response arrives.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior netstack issues and pull requests for the same root cause.

### ISSUE-2026-111 — netstack: Repeated OpenBSD DNS Set overwrites the original backup

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: `Close` must restore the `resolv.conf` that existed before Tailscale.
  Every `SetDNS` call overwrites the shared backup.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `net/dns/resolvd.go:35-95` contains OpenBSD backup, Set, and restore handling.
  The second call backs up the file already modified by the first call.
- Shared change pressure: Not a DRY finding; the OpenBSD DNS manager owns the baseline backup lifetime.
- Impact: `Close` can restore an earlier Tailscale search domain instead of the original file.
  The supplied finding does not quantify production occurrence.
- Proposed direction: Save the baseline once per manager lifetime and release it only after restore.
- Risks and boundaries: Preserve the exact original `resolv.conf` across repeated `SetDNS` calls and `Close`.
- Verification: Set two DNS configurations; verify that `Close` restores the exact original file.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior netstack issues and pull requests for the same root cause.

### ISSUE-2026-112 — netstack: NRPT deletion errors permanently forget remaining Windows rules

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: A failed rule removal must remain known for cleanup and retry.
  The deletion error is discarded while the ID list is still shortened.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `net/dns/nrpt_windows.go:237-255` contains the affected deletion and ID update.
  Later `DelAllRuleKeys` no longer knows about the remaining registry rule.
- Shared change pressure: Not a DRY finding; the Windows NRPT rule tracker owns deletion confirmation.
- Impact: Removed domains can keep routing through stale nameservers.
  The supplied finding does not quantify production occurrence.
- Proposed direction: Retain IDs until deletion is confirmed and return the deletion error.
- Risks and boundaries: Preserve Windows registry rule IDs across failed cleanup attempts.
- Verification: Disrupt one deletion; verify that its ID remains and the next cleanup completes it.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior netstack issues and pull requests for the same root cause.

### ISSUE-2026-113 — prober: Prober computes median from unsorted latency values

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Statistics correctness.
- Publication target: Undecided.
- Summary: `RecentMedianLatency` must return the median of recent measurements.
  The function selects the middle element of insertion order.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `prober/prober.go:405-410,463-469` contains history copying and median selection.
  The copied history is never sorted before indexing.
- Shared change pressure: Not a DRY finding; `RecentMedianLatency` owns ordering before median selection.
- Impact: The status page and API can report a wrong median for nonmonotonic latencies.
  The supplied finding does not quantify production occurrence.
- Proposed direction: Clone and sort the slice, then apply the existing even-count policy.
- Risks and boundaries: Preserve history order outside the clone and retain the existing even-count policy.
- Verification: Use `100ms,1ms,2ms`; verify that `RecentMedianLatency` returns `2ms`.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior prober issues and pull requests for the same root cause.

### ISSUE-2026-114 — tsweb: Accept-Encoding with q equal to zero is still served

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: HTTP correctness.
- Publication target: Undecided.
- Summary: A `q=0` value must exclude the corresponding Content-Encoding variant.
  Parameters are stripped and only the encoding name is compared.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  `tsweb/tsweb.go:144-165; util/precompress/precompress.go:63-79` contains negotiation and selection.
  Brotli is selected before Gzip even for `br;q=0`.
- Shared change pressure: Not a DRY finding; HTTP precompressed-asset negotiation owns quality filtering.
- Impact: Clients can receive explicitly rejected compression and fail to load assets.
  The supplied finding does not quantify production occurrence.
- Proposed direction: Parse quality parameters and reject encodings whose quality is zero.
- Risks and boundaries: Preserve Brotli, Gzip, and Identity selection while honoring explicit exclusions.
- Verification: Use `br;q=0, gzip` for Gzip and `gzip;q=0` for Identity; verify both selections.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsweb issues and pull requests for the same root cause.

### ISSUE-2026-115 — tsdial: Synology proxy parse failure becomes a direct success after one occurrence

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: An unchanged invalid proxy configuration must keep returning the same parse error.
  The code caches the mtime but returns the parse error only on the first call.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `net/tshttpproxy/tshttpproxy_synology.go:47-58`.
  The second call skips parsing and returns a nil proxy and nil error; runtime frequency is unmeasured.
- Shared change pressure: Not a DRY finding; the Synology proxy cache is the single decision owner.
- Impact: Source proves later control requests silently bypass the required proxy after a parse error.
  The production frequency and duration of such bypasses are unmeasured.
- Proposed direction: Cache the parse error, or record the mtime only after parsing succeeds.
- Risks and boundaries: Preserve cache state semantics for unchanged files and successful proxy parses.
- Verification: Read an invalid file twice; both calls should return the same error.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsdial issues and pull requests for the same root cause.

### ISSUE-2026-116 — osrouter: Windows NDP rules use the wrong remote address condition

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: Inbound NDP rules must allow sources from `fe80::/10`.
  `permitNDP` instead always sets the multicast value `ff02::2`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `wf/firewall.go:404-480`.
  The later protocol block rejects unmatched ICMPv6 packets; production frequency is unmeasured.
- Shared change pressure: Not a DRY finding; `permitNDP` is the single decision owner for the WFP condition.
- Impact: Source proves Router Advertisements and Redirects are blocked while the killswitch is active.
  The production prevalence of affected IPv6 traffic is unmeasured.
- Proposed direction: Use the passed `remoteAddress` directly as the WFP condition value.
- Risks and boundaries: Preserve Windows WFP translation and IPv6 neighbor-discovery boundaries.
- Verification: Check that RA and Redirect packets from `fe80::/10` pass the generated filter.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior osrouter issues and pull requests for the same root cause.

### ISSUE-2026-117 — osrouter: Exited Windows firewall child is never restarted

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: The desired firewall state must be restored after a child process failure.
  After child exit, `fwProc`, the encoder, and `known` remain unchanged.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `wgengine/router/osrouter/router_windows.go:249-397`.
  Identical states take the fast path, while changes write to the broken encoder; frequency is unmeasured.
- Shared change pressure: Not a DRY finding; the Windows router supervisor is the single decision owner.
- Impact: Source proves killswitch and route changes stop applying after child failure.
  The production rate and duration of child failures are unmeasured.
- Proposed direction: Monitor child exit, invalidate handles, and resend the complete desired state.
- Risks and boundaries: Preserve concurrent state updates, child cleanup, and full-state replay ordering.
- Verification: After a successful Set, terminate the child; an identical Set should restart it.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior osrouter issues and pull requests for the same root cause.

### ISSUE-2026-118 — controlbase: Consensus Stop discards incomplete HTTP shutdown errors

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Stop must return deadline or listener errors from both HTTP server shutdowns.
  Both `http.Server.Shutdown` errors are only logged and then overwritten.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `tsconsensus/tsconsensus.go:413-430`.
  Production callers check an error the function cannot return; runtime occurrence is unmeasured.
- Shared change pressure: Not a DRY finding; consensus Stop is the single decision owner for shutdown errors.
- Impact: Source proves active command or monitor requests may remain while Stop reports success.
  The production frequency of incomplete shutdowns is unmeasured.
- Proposed direction: Attempt both shutdowns, then return their errors with `errors.Join`.
- Risks and boundaries: Preserve both shutdown attempts, deadline handling, and listener cleanup.
- Verification: Hold a request beyond the deadline; Stop should return `context.DeadlineExceeded`.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-119 — controlbase: FollowOnly ignores cancellation during long consensus retry sequences

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: The startup context must bound retry, refresh, and join work, with complete cleanup.
  `time.Sleep`, refresh errors, and join contexts ignore startup cancellation.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `tsconsensus/tsconsensus.go:333-349; tsconsensus/http.go:40-65`.
  Each join uses a new 30-second context from `context.Background`; runtime incidence is unmeasured.
- Shared change pressure: Not a DRY finding; `FollowOnly` startup is the single decision owner for cancellation.
- Impact: Source proves canceled startup may continue for minutes and leave servers and Raft resources.
  The production frequency and resource lifetime are unmeasured.
- Proposed direction: Propagate the context, make sleep selectable, and close all resources on error paths.
- Risks and boundaries: Preserve cancellation concurrency, server cleanup, and Raft cleanup across every retry path.
- Verification: Cancel during sleep and join; startup should end immediately without retained resources.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-120 — controlbase: Direct Close waits up to 25 seconds because of a lock cycle

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Bus callbacks and client Close must not have a cyclic lock dependency.
  Close holds `c.mu`; an active callback needs it, while bus Close waits for the callback.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `control/controlclient/direct.go:424-478`.
  The eventbus waits for five slow-subscriber timeouts, about 25 seconds; occurrence is unmeasured.
- Shared change pressure: Not a DRY finding; Direct client Close is the single decision owner for lock ordering.
- Impact: Source proves disconnect or daemon shutdown can block until the subscriber timeouts expire.
  The production frequency of the lock cycle is unmeasured.
- Proposed direction: Close the bus client before acquiring `c.mu`.
- Risks and boundaries: Preserve callback concurrency, lock ordering, and eventbus cleanup behavior.
- Verification: Block a callback on `c.mu`; Close should not wait for the bus timeout.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-121 — netstack: Capability-based ICMP rules ignore all destination prefixes

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Each `Match` rule must bind its source or capability and its `Dsts`.
  The `SrcCaps` branch returns true on a capability match without checking the destination.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `wgengine/filter/match.go:62-83`.
  The converter preserves `Dsts`; only this ICMP path drops them, with runtime incidence unmeasured.
- Shared change pressure: Not a DRY finding; the `SrcCaps` ICMP match path is the single decision owner.
- Impact: Source proves an authorized peer can pass ICMP rules for unintended local destinations.
  The production frequency and reachable destination set are unmeasured.
- Proposed direction: Require at least one matching destination prefix in the capability branch.
- Risks and boundaries: Preserve capability matching and destination-prefix semantics for ICMP rules.
- Verification: Check a capability source separately against an allowed and an excluded destination.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior netstack issues and pull requests for the same root cause.

### ISSUE-2026-122 — netstack: UPnP validation failure leaves an installed router rule

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: A failed mapping creation must leave no external router resource.
  External IP checks occur only after `AddPortMapping` succeeds.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `net/portmapper/upnp.go:624-690`.
  The deletable mapping object is created only after all checks; router behavior is otherwise unmeasured.
- Shared change pressure: Not a DRY finding; UPnP mapping creation is the single decision owner for rollback.
- Impact: Source proves an unattended UDP forwarding rule can remain until lease expiry or indefinitely.
  The production frequency and router-specific lifetime are unmeasured.
- Proposed direction: After a successful add, defer rollback until the mapping is finally committed.
- Risks and boundaries: Preserve external resource cleanup, lease state, and commit boundaries.
- Verification: Disrupt the external IP query; the router should immediately receive `DeletePortMapping`.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior netstack issues and pull requests for the same root cause.

### ISSUE-2026-123 — clientupdate: Linux update can install different CLI and daemon versions

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: The update must replace `tailscale` and `tailscaled` as one consistent unit.
  Two independent renames provide no rollback when the second rename fails.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `clientupdate/clientupdate.go:1055-1113`.
  After the second failure, the code returns before restart; failure frequency is unmeasured.
- Shared change pressure: Not a DRY finding; the Linux paired-file installer is the single decision owner.
- Impact: Source proves a second rename failure leaves the new CLI with the old daemon installed.
  The production frequency and operational effect of that mismatch are unmeasured.
- Proposed direction: Use an atomic pair strategy, or roll back the first rename if the second fails.
- Risks and boundaries: Preserve installed files, rollback durability, restart ordering, and migration boundaries.
- Verification: Fail only the second rename; both installed files should remain old.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior clientupdate issues and pull requests for the same root cause.

### ISSUE-2026-124 — clientupdate: Distsign loads keys outside the configured proxy path

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Signature keys and payloads must use the same proxy configuration.
  Keys use `http.Get`, while only package data uses the Tailscale proxy hook.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `clientupdate/distsign/distsign.go:215-343`.
  Without the hook, the custom transport also disables the environment proxy; incidence is unmeasured.
- Shared change pressure: Not a DRY finding; the distsign HTTP client is the single decision owner for update transport.
- Impact: Source proves updates fail when a host can reach the network only through its system proxy.
  The production prevalence of proxy-only hosts is unmeasured.
- Proposed direction: Use one hook-capable HTTP client for every downloaded update component.
- Risks and boundaries: Preserve proxy selection, signature verification flow, and transport cleanup.
- Verification: Route the key, signature, HEAD, and GET through a proxy-only fixture.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior clientupdate issues and pull requests for the same root cause.

### ISSUE-2026-125 — drive: Drive StatCache survives replacement of its child backends

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: A new `Generation` must expose changes to the domain, transport, and remotes.
  `SetChildren` replaces backends but does not invalidate a cached PROPFIND.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `drive/driveimpl/local_impl.go:20-31,90-117`.
  The cache runs before `GetChild` and can even return old 404 responses.
- Shared change pressure: Not a DRY finding; `SetChildren` owns child replacement and cache invalidation.
- Impact: Removed or replaced shares can return stale metadata for up to ten seconds.
  The production frequency of stale responses is unmeasured.
- Proposed direction: Fully invalidate StatCache immediately after a successful child replacement.
- Risks and boundaries: Preserve successful replacement semantics and invalidate no earlier than success.
- Verification: Cache a PROPFIND, replace the child, and immediately request the new metadata.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior drive issues and pull requests for the same root cause.

### ISSUE-2026-126 — osrouter: Identical route and extra prefixes lose extra authorization

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: `kindExtra` must remain authorized independently of `RouteAll`.
  The combined route branch returns false before honoring the extra authorization.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `net/routemanager/routemanager.go:659-685,822-860`.
  One peer prefix sets both bits but is discarded as a normal route.
- Shared change pressure: Not a DRY finding; the combined route branch owns route and extra eligibility.
- Impact: Conn25 transit traffic fails with subnet routing disabled despite extra configuration.
  The production frequency of this failure is unmeasured.
- Proposed direction: Evaluate extra authorization independently and before rejection by `RouteAll`.
- Risks and boundaries: Preserve normal route precedence while changing only the combined route and extra case.
- Verification: Configure one `/32` as route and extra; it must remain with `RouteAll=false`.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior osrouter issues and pull requests for the same root cause.

### ISSUE-2026-127 — taildrop: Taildrop retry removes the wrong delayed deletion task

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Resume must remove the deletion task for the reused partial file.
  `Remove(baseName)` does not address the stored `partialName` key.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `feature/taildrop/send.go:88-100; delete.go:112-179`.
  The Deleter uses exact keys and does not know an active transfer when the task expires.
- Shared change pressure: Not a DRY finding; the resume path owns cancellation of its partial-file deletion task.
- Impact: A long-running resume can lose its active partial file after one hour.
  The production frequency of this loss is unmeasured.
- Proposed direction: Remove the full partial key before opening the resume writer.
- Risks and boundaries: Preserve partial-file ownership, delayed cleanup, and the final rename boundary.
- Verification: Keep a resume active past the deletion deadline; the partial file and final rename must survive.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior taildrop issues and pull requests for the same root cause.

### ISSUE-2026-128 — appc: RouteInfo snapshot shares mutable slices with the Connector

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: RouteInfo published outside the lock must be a stable snapshot.
  `maps.Clone` copies only maps, not their `[]netip.Addr` values.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `appc/appconnector.go:231-236,506-510`.
  The Connector later extends and sorts the same slices after publication.
- Shared change pressure: Not a DRY finding; RouteInfo publication owns the snapshot memory boundary.
- Impact: Later DNS changes can mutate or race with already published persistence data.
  The production frequency of mutation or races is unmeasured.
- Proposed direction: Deep-copy every slice value before publishing the event.
- Risks and boundaries: Preserve lock boundaries and ensure no mutable slice remains shared after publication.
- Verification: Pause the snapshot, change the source domain, and verify persistence data stays unchanged.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior appc issues and pull requests for the same root cause.

### ISSUE-2026-129 — controlbase: Failed preferences patch clears AppConnector routes first

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: A failed mutation must leave application state and persistence unchanged.
  `MaybeClearAppConnector` runs before validation and `EditPrefsAs`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `ipn/localapi/localapi.go:1015-1024`.
  `ClearRoutes` empties state and publishes persistence before the later error occurs.
- Shared change pressure: Not a DRY finding; the preferences patch flow owns commit ordering for route cleanup.
- Impact: A rejected preferences patch loses learned routes while preferences remain unchanged.
  The production frequency of rejected patches with route loss is unmeasured.
- Proposed direction: Clear routes only after a successful preferences commit, or roll them back transactionally.
- Risks and boundaries: Preserve preferences and RouteInfo atomically across validation, commit, and failure.
- Verification: Send an invalid patch; both preferences and RouteInfo must remain unchanged.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-130 — controlbase: Incremental filter and UserProfile deltas are absent from the disk cache

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Applied netmap deltas must update the same durable restart cache.
  Only the full-netmap store has persistence paths for filters and profiles.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `ipn/ipnlocal/local.go:2641-2696; netmapcache/netmapcache.go:214-318`.
  The control path ends successful delta processing before any full store.
- Shared change pressure: Not a DRY finding; each filter and UserProfiles delta owner owns its cache update.
- Impact: After restart, the disk cache can restore older filter or profile values.
  The production frequency of stale restart restoration is unmeasured.
- Proposed direction: Add dedicated cache updates at the owners of filter and UserProfiles deltas.
- Risks and boundaries: Preserve full-netmap storage and restart-cache consistency for both delta types.
- Verification: Apply both delta types, reload, and require the exact new values.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-131 — health: Two health setters leave derived state stale

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Every relevant health input must update events and `CurrentState` immediately.
  Both setters change only raw fields and omit `selfCheckLocked`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `health/health.go:629-652`.
  Adjacent setters perform that derivation under the same mutex.
- Shared change pressure: Not a DRY finding; each health setter owns immediate derivation after its mutation.
- Impact: TLS and log configuration errors can appear late or remain visible after clear.
  The production frequency and duration of stale health state are unmeasured.
- Proposed direction: Run the existing self-check under the lock after each mutation.
- Risks and boundaries: Preserve mutex ownership and existing event and `CurrentState` derivation semantics.
- Verification: Set and clear both errors; events and `CurrentState` must follow immediately.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior health issues and pull requests for the same root cause.

### ISSUE-2026-132 — health: Multiple health problems collapse nondeterministically into one entry

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Concurrent independent problems must remain fully and stably observable.
  All map entries use the same `Warnable` key.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `health/health.go:1252-1286`.
  Go map order nondeterministically decides which text remains last.
- Shared change pressure: Not a DRY finding; health warning identity owns aggregation of keyed problems.
- Impact: All but one faulty DERP region or TLS host can be hidden.
  The production frequency of collapsed diagnostics is unmeasured.
- Proposed direction: Aggregate problems deterministically or use a unique warning identity for each key.
- Risks and boundaries: Preserve stable diagnostic identity and complete multi-problem reporting.
- Verification: Set two regions and two hosts; all details must appear stably.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior health issues and pull requests for the same root cause.

### ISSUE-2026-133 — sockstatlog: Sockstat stop neither drains nor isolates goroutine generations

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Disable must finish one generation and make its captured logs available.
  Cancel does not wait, drain `eventCh`, or preserve a generation-local context.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `log/sockstatlog/logger.go:149-261`.
  Old goroutines can adopt the new `lg.ctx` after reenable.
- Shared change pressure: Not a DRY finding; the sockstat lifecycle owns generation context, join, drain, and flush.
- Impact: Events can be lost, and rapid reenable can create duplicate pollers and data races.
  The production frequency of loss, duplication, or races is unmeasured.
- Proposed direction: Keep context per generation, join pollers, and synchronously drain the queue before flush.
- Risks and boundaries: Preserve concurrency ownership and order cancel, join, drain, flush, and reenable safely.
- Verification: Enqueue an event, interleave disable and reenable, and require one generation with that event.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior sockstatlog issues and pull requests for the same root cause.

### ISSUE-2026-134 — logtail: Logtail shutdown reports full success after context cancellation

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: An interrupted shutdown must return the corresponding context error.
  After cancellation and Wait, shutdown unconditionally returns nil.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The finding is at `logtail/logtail.go:384-419`.
  Logpolicy and Netlog propagate this return value as though it were meaningful.
- Shared change pressure: Not a DRY finding; Logtail shutdown owns the final delivery result returned to callers.
- Impact: Callers can believe upload completed although cancellation stopped pending logs.
  The production frequency and number of undelivered logs are unmeasured.
- Proposed direction: After cleanup and Wait, return the observed `ctx.Err` to the caller.
- Risks and boundaries: Preserve cleanup and Wait while changing only the cancellation result contract.
- Verification: Pass an already canceled context and require `context.Canceled`.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior logtail issues and pull requests for the same root cause.

### ISSUE-2026-135 — captiveportal: Captive portal shutdown does not await an active network check

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: After `Extension.Shutdown`, no extension work may remain active.
  Shutdown only cancels and closes the event bus; it does not join the Detection goroutine.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `feature/captiveportal/captiveportal.go:100-110,185-285`.
  Detection's own contract says that the network check can block for a long time.
- Shared change pressure: Not a DRY finding; `Extension.Shutdown` owns the Detection loop lifetime decision.
- Impact: Detection can access backend, host, and health resources after shutdown.
  Production incidence and user-visible impact are unmeasured.
- Proposed direction: Add a loop-done signal or WaitGroup, and await it outside the Extension mutex.
- Risks and boundaries: Do not wait while holding the Extension mutex; preserve event-bus cleanup ordering.
- Verification: Pause Detection; verify that shutdown can finish only after Detection returns.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior captiveportal issues and pull requests for the same root cause.

### ISSUE-2026-136 — netstack: OpenBSD loses the bypass table after a failed deletion

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: Failed bypass cleanup must remain tracked until the routing table is deleted.
  `bypassRtable` is cleared unconditionally, even when deletion fails.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `net/netns/netns_openbsd.go:113-121,151-173`.
  Table search still rejects the occupied table after its ownership record is lost.
- Shared change pressure: Not a DRY finding; the OpenBSD bypass cleanup path owns routing-table retry state.
- Impact: The remaining routing table is never retried and can contribute to table exhaustion.
  Production incidence and user-visible impact are unmeasured.
- Proposed direction: Retain the table ID and retry state until deletion is confirmed.
- Risks and boundaries: Preserve OpenBSD table ownership across errors and clear it only after confirmed deletion.
- Verification: Force deletion to fail; verify that a second cleanup retries the same table.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior netstack issues and pull requests for the same root cause.

### ISSUE-2026-137 — ipn-store: FileStore cache commits before the atomic file write

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: The cache and file must represent the same successfully persisted state.
  The cache changes before `atomicfile.WriteFile` and is not rolled back after failure.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `ipn/store/stores.go:211-226`.
  The equality fast path then skips the required second write attempt.
- Shared change pressure: Not a DRY finding; FileStore owns the cache-to-file commit decision.
- Impact: An identical retry after a file error returns false success without writing the file.
  Production incidence and user-visible impact are unmeasured.
- Proposed direction: Commit the prospective state to the cache only after the file commit succeeds.
- Risks and boundaries: Preserve idempotent retries and never expose unpersisted state through the cache.
- Verification: Disrupt the first write; verify that an identical retry actually writes the file.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior ipn-store issues and pull requests for the same root cause.

### ISSUE-2026-138 — ipn-store: AWS state cache updates before a successful SSM commit

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: The cache must represent only SSM data that was persisted successfully.
  Memory state changes before `PutParameter` and remains changed after an error.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `ipn/store/awsstore/store_aws.go:224-264`.
  `ipn.WriteState` compares the premature cache and suppresses identical retries.
- Shared change pressure: Not a DRY finding; the AWS store owns the memory-to-SSM commit decision.
- Impact: Profile retries report success while SSM still contains the old state.
  Production incidence and user-visible impact are unmeasured.
- Proposed direction: Commit the cache after a successful put, or roll it back while holding the lock.
- Risks and boundaries: Preserve lock safety and ensure failed provider writes remain retryable.
- Verification: Force the put to fail; verify that an identical retry writes to SSM again.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior ipn-store issues and pull requests for the same root cause.

### ISSUE-2026-139 — tka: TKA batch failure leaves partially persisted authority updates

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: `InformIdempotent` must leave storage unchanged whenever the authority update fails.
  AUM files are written sequentially without batch rollback.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `tka/tka.go:575-581; tka/tailchonk.go:593-671`.
  If file two fails, file one remains permanently stored but invisible to runtime state.
- Shared change pressure: Not a DRY finding; the TKA AUM batch writer owns authority-update atomicity.
- Impact: Storage retains part of an update that the authority reports as wholly failed.
  Production incidence and user-visible impact are unmeasured.
- Proposed direction: Persist the batch transactionally with a journal, temporary commit, or full rollback.
- Risks and boundaries: Preserve authority atomicity across storage errors and reopen boundaries.
- Verification: Disrupt the second AUM write; verify after reopen that no new AUM exists.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tka issues and pull requests for the same root cause.

### ISSUE-2026-140 — tka: Stat failure makes young TKA history appear old

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Metadata errors must stop irreversible compaction and purge.
  `CommitTime` converts every stat error to zero time plus nil.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `tka/tailchonk.go:371-401,773-787,935-977`.
  Retention treats zero time as old and marks the AUM as deletable before `MinAge`.
- Shared change pressure: Not a DRY finding; `CommitTime` owns the metadata-error decision used by retention.
- Impact: A young inactive fork history can be deleted before `MinAge` expires.
  Production incidence and user-visible impact are unmeasured.
- Proposed direction: Return a wrapped stat error and abort compaction before purge.
- Risks and boundaries: Preserve legacy-AUM handling while preventing purge after any metadata error.
- Verification: Disrupt stat for a young legacy AUM; verify that Compact fails without deletion.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tka issues and pull requests for the same root cause.

### ISSUE-2026-141 — k8s-operator: Kubernetes StateStore maps distinct keys to the same field

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven key collision through debug state APIs; first-party collision not observed.
- Internal priority: Low.
- Confidence: Medium.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Distinct `StateKey` values must address distinct persisted values.
  Replacing every invalid character with an underscore loses key identity.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `ipn/store/kubestore/store_kube.go:299-320`.
  `foo@bar` and `foo_bar` reach the same Secret field through `dev-store-set`.
- Shared change pressure: Not a DRY finding; Kubernetes StateStore owns the StateKey-to-Secret-field encoding.
- Impact: Writing or deleting one key can silently overwrite another key.
  Production incidence and user-visible impact are unmeasured.
- Proposed direction: Introduce reversible collision-free encoding with migration for existing Secret fields.
- Risks and boundaries: Preserve existing Secret data and opaque `StateKey` identity during migration.
  Debug callers make the collision reachable; use reversible encoding and migrate legacy fields.
- Verification: Independently write, read, and delete two colliding keys without cross-key effects.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior k8s-operator issues and pull requests for the same root cause.

### ISSUE-2026-142 — k8s-operator: Kubernetes certificate cache returns mixed certificate-key pairs

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Readers must see a certificate and private key from the same commit.
  The cache reads and writes the two values through separate store operations.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `ipn/store/kubestore/store_kube.go:323-387,645-663`.
  A reader between writes gets the new certificate and old key without a store error.
- Shared change pressure: Not a DRY finding; the Kubernetes certificate cache owns TLS-pair atomicity.
- Impact: Read-only certificate sharing can fail valid TLS handshakes during reload.
  Production incidence and user-visible impact are unmeasured.
- Proposed direction: Guard pair access with a dedicated lock, without network operations under that lock.
- Risks and boundaries: Preserve reader-writer concurrency and keep all network access outside the pair lock.
- Verification: Interleave a writer and reader deterministically; verify that no mixed pair appears.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior k8s-operator issues and pull requests for the same root cause.

### ISSUE-2026-143 — localapi: ReloadConfig error response is empty and loses the cause

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: CLI correctness.
- Publication target: Undecided.
- Summary: Reload must always return an encoded `ReloadConfigResponse`.
  The error branch sets `res.Err` and returns before `Encode`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `ipn/localapi/localapi.go:735-753`.
  `net/http` therefore emits implicit status 200 with a completely empty body.
- Shared change pressure: Not a DRY finding; the LocalAPI ReloadConfig handler owns response encoding.
- Impact: The CLI and containerboot receive JSON EOF instead of the actual config error.
  Production incidence and user-visible impact are unmeasured.
- Proposed direction: Encode the error branch fully under the existing 200-with-JSON contract.
- Risks and boundaries: Preserve the existing HTTP 200 JSON schema and the original backend error text.
- Verification: Send an invalid reload; verify that LocalClient receives the exact backend error.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior localapi issues and pull requests for the same root cause.

### ISSUE-2026-144 — tsnet: Failed ListenFunnel leaves Funnel permission enabled

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Failed listener setup must leave no newly added Funnel configuration.
  `AllowFunnel` is persisted before Listen and is not removed after a Listen error.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `tsnet/tsnet.go:1539-1567`.
  The next attempt sees permission already set and registers no cleanup callback.
- Shared change pressure: Not a DRY finding; `ListenFunnel` owns Funnel permission setup and rollback.
- Impact: Persisted Funnel permission remains without a listener and survives later retries.
  Production incidence and user-visible impact are unmeasured.
- Proposed direction: Transactionally roll back newly set Funnel permission after every Listen error.
- Risks and boundaries: Remove only permission created by the failed attempt; preserve preexisting Funnel state.
- Verification: Create a port collision; verify that ServeConfig contains no Funnel permission afterward.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsnet issues and pull requests for the same root cause.

### ISSUE-2026-145 — tsnet: ServiceListener Close cannot retry failed cleanup

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: `Close` must fully remove the listener, advertisement, and ServeConfig, with retryable cleanup.
  A shared `sync.Once` permanently caches even transient cleanup errors.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `tsnet/tsnet.go:1695-1747`.
  Later Close calls only return the old error and perform no further cleanup.
- Shared change pressure: Not a DRY finding; ServiceListener Close owns the cleanup retry decision.
- Impact: Source proves closed listeners can remain advertised and configured.
  The production frequency is not measured in the supplied finding.
- Proposed direction: Track listener closure and each idempotent cleanup step separately, retrying incomplete steps.
- Risks and boundaries: Preserve idempotency and concurrency safety while allowing cleanup retries.
- Verification: Disrupt the first config cleanup; a second Close must remove the advertisement and config.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsnet issues and pull requests for the same root cause.

### ISSUE-2026-146 — tsnet: Tsnet accessors panic despite their documented pre-start contract

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: CLI correctness.
- Publication target: Undecided.
- Summary: Before startup, `CertDomains` must return nil and `TailscaleIPs` must return invalid addresses.
  On a fresh Server, both methods dereference `s.lb`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `tsnet/tsnet.go:682-697`.
  Before `Start` or join, the Server intentionally has no LocalBackend.
- Shared change pressure: Not a DRY finding; each public accessor owns its pre-start return decision.
- Impact: Source proves valid pre-start queries can terminate the caller through a nil dereference.
  The production frequency is not measured in the supplied finding.
- Proposed direction: When `s.lb` is nil, immediately return each method's documented zero values.
- Risks and boundaries: Preserve the documented values for both pre-start and initialized Server states.
- Verification: Call both methods on a zero Server; neither call may panic.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsnet issues and pull requests for the same root cause.

### ISSUE-2026-147 — tsnet: Close after a failed tsnet start masks the start error

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: `Close` must be safe after a startup attempt returns, including failed attempts.
  An early startup error leaves `s.sys` nil, but Close dereferences it unconditionally.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `tsnet/tsnet.go:601-669,802-836`.
  Public examples register `defer Close` before operations that trigger startup.
- Shared change pressure: Not a DRY finding; tsnet Close owns partial-start resource cleanup.
- Impact: Source proves common defer patterns can replace the startup error with a shutdown panic.
  The production frequency is not measured in the supplied finding.
- Proposed direction: Have Close explicitly skip uninitialized system and bus resources.
- Risks and boundaries: Preserve cleanup for initialized resources while tolerating every partial-start boundary.
- Verification: Make startup fail with an invalid `Dir`, then close the Server safely.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsnet issues and pull requests for the same root cause.

### ISSUE-2026-148 — k8s-operator: DNS rename deletes only the old IPv4 record

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Renaming must remove the previous DNS name from both address families.
  The rename branch deletes only `rec.IP4[oldFqdn]`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/k8s-operator/dnsrecords.go:146-173`.
  The shared records model has separate `IP4` and `IP6` maps.
- Shared change pressure: Not a DRY finding; the DNS rename branch owns removal of the old FQDN.
- Impact: Source proves the old name can remain resolvable as a stale AAAA record.
  The production frequency is not measured in the supplied finding.
- Proposed direction: On rename, also delete the same old FQDN from the IPv6 map.
- Risks and boundaries: Preserve dual-stack ownership and do not delete records for the new FQDN.
- Verification: Rename a dual-stack name; its old A and AAAA records must both disappear.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior k8s-operator issues and pull requests for the same root cause.

### ISSUE-2026-149 — k8s-operator: Missing endpoint families remain published as DNS records

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Reconcile must set present address families and delete families that are no longer present.
  Empty families are ignored, and two empty lists cause an early return.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/k8s-operator/dnsrecords.go:182-200,452-505`.
  EndpointSlice deletion and family changes reach the same incomplete update path.
- Shared change pressure: Not a DRY finding; the DNS reconcile path owns convergence of both family maps.
- Impact: Source proves removed or unready pods can remain reachable through stale A or AAAA records.
  The production frequency is not measured in the supplied finding.
- Proposed direction: Always reconcile both maps and explicitly delete empty families.
- Risks and boundaries: Preserve records for present families while converging deletion and family-change states.
- Verification: Remove an IPv6 slice, then all endpoints; the corresponding records must disappear.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior k8s-operator issues and pull requests for the same root cause.

### ISSUE-2026-150 — k8s-operator: Invalid multiple DNSConfig objects still provision nameserver resources

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Multiple DNSConfig objects must end reconcile with a not-ready status and no provisioning.
  The invalid-state status result is ignored, and execution continues into `maybeProvision`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/k8s-operator/nameserver.go:111-203`.
  An already addressed Service can subsequently allow `NameserverReady=True`.
- Shared change pressure: Not a DRY finding; the invalid DNSConfig branch owns the reconcile stop decision.
- Impact: Source proves an invalid cluster state can create resources and report False followed by True.
  The production frequency is not measured in the supplied finding.
- Proposed direction: Return the status call directly from the invalid branch and propagate its error.
- Risks and boundaries: Preserve invalid-state status and prevent child-resource creation after singleton failure.
- Verification: Create two DNSConfigs; no child resource or subsequent True status may appear.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior k8s-operator issues and pull requests for the same root cause.

### ISSUE-2026-151 — k8s-operator: Recorder cleanup stops at the first missing replica state

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Cleanup must process every supported recorder replica before removing the finalizer.
  The first `ok=false` returns success instead of continuing to the next replica.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/k8s-operator/tsrecorder.go:402-444`.
  Missing or incomplete state is a normal provisioning state.
- Shared change pressure: Not a DRY finding; the recorder cleanup loop owns finalizer readiness.
- Impact: Source proves later authenticated recorder devices can remain after deletion completes.
  The production frequency is not measured in the supplied finding.
- Proposed direction: Continue on `ok=false` and remove the finalizer only after the full loop completes.
- Risks and boundaries: Preserve cleanup across all replicas and keep the finalizer until the loop is complete.
- Verification: Use replica zero without state and replica one active; finalize only after deleting replica one.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior k8s-operator issues and pull requests for the same root cause.

### ISSUE-2026-152 — containerboot: IPv6 DNS backends never reach their firewall configuration branch

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: All inbound Tailscale connections must be forwarded to the DNS-based proxy target.
  The entire IPv6 branch runs only when local IPv6 is currently absent.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/containerboot/forwarding.go:252-259`.
  The inner `else if` also requires a valid IPv6 address, making it unreachable.
- Shared change pressure: Not a DRY finding; the IPv6 forwarding branch owns backend eligibility.
- Impact: Source proves IPv6 traffic to a DNS-based proxy target receives no DNAT rule.
  The production frequency is not measured in the supplied finding.
- Proposed direction: Gate externally on present IPv6 backends and handle address validity inside.
- Risks and boundaries: Preserve IPv4 behavior and IPv6 address-validity checks at the platform boundary.
- Verification: With valid node IPv6 and an AAAA backend, the path must call `DNATWithLoadBalancer`.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior containerboot issues and pull requests for the same root cause.

### ISSUE-2026-153 — k8s-operator: Health-port collision routes user traffic to the internal health server

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Every declared service port must receive an unreserved internal target-port mapping.
  A collision with the old health port is accepted as a user port by renaming it only.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/k8s-operator/egress-services.go:289-368`.
  `TargetPort` remains 9002, and a new health port is also appended.
- Shared change pressure: Not a DRY finding; egress port reconciliation owns reserved health-port handling.
- Impact: Source proves a valid user port can reach the health endpoint instead of its Tailnet target.
  The production frequency is not measured in the supplied finding.
- Proposed direction: Remove old health entries by reserved name before matching user ports.
- Risks and boundaries: Preserve existing user mappings while keeping internal health ports reserved during migration.
- Verification: After port 80, configure 9002; that user port must carry Tailnet traffic.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior k8s-operator issues and pull requests for the same root cause.

### ISSUE-2026-154 — k8s-operator: Connector status retains removed modes and device identities

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Status fields must describe only the currently observed Connector configuration.
  Several fields are set only in positive branches and are never cleared.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/k8s-operator/connector.go:160-171,266-277`.
  `replicas=0` clears `Devices` but retains `Hostname` and `TailnetIPs`.
- Shared change pressure: Not a DRY finding; Connector status derivation owns clearing stale fields.
- Impact: Source proves the API can report an old AppConnector role or identity with the new state.
  The production frequency is not measured in the supplied finding.
- Proposed direction: Reset mode and identity fields to zero values before each status derivation.
- Risks and boundaries: Preserve current fields while clearing identities and modes removed by transitions.
- Verification: Switch AppConnector to SubnetRouter, then set replicas to zero and inspect status.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior k8s-operator issues and pull requests for the same root cause.

### ISSUE-2026-155 — cli: Debug ts2021 reports success after both connection attempts fail

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: CLI correctness.
- Publication target: Undecided.
- Summary: Exhausted diagnostic retries must return a non-nil process status.
  Both failures are logged, but the function then returns nil.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/tailscale/cli/debug.go:1179-1188`.
  The top-level process exits with status zero exactly when the function returns nil.
- Shared change pressure: Not a DRY finding; `debug ts2021` owns the retry-exhaustion result.
- Impact: Source proof: automation treats total control-connectivity failure as success.
  Production frequency and scope are unmeasured.
- Proposed direction: Retain the last connection error and return it after both attempts are exhausted.
- Risks and boundaries: Preserve successful-attempt behavior and both diagnostic attempts.
- Verification: Deterministically fail both attempts and require a non-zero process status.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior cli issues and pull requests for the same root cause.

### ISSUE-2026-156 — tsidp: Tsidp omits creation of its local persistent state directory

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: The selected default state path must support the first persistent write.
  The path is selected, but its parent directory is never created.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/tsidp/tsidp.go:141-149,1038-1060`.
  Strict mode fails at startup; insecure mode can exit during a JWKS request.
- Shared change pressure: Not a DRY finding; tsidp owns preparation of its selected persistent state root.
- Impact: Source proof: fresh local-tailscaled mode fails on a client or key write.
  Production frequency and affected environments are unmeasured.
- Proposed direction: Create the selected root path with restrictive permissions before any persistence.
- Risks and boundaries: Preserve restrictive permissions and the selected state-path boundary.
- Verification: Start without the config subdirectory and require the first key write to succeed.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsidp issues and pull requests for the same root cause.

### ISSUE-2026-157 — tsidp: Tsidp UI mutates memory before durable persistence succeeds

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: A failed UI save must leave the active OAuth client state unchanged.
  Create, Edit, and Regenerate mutate `funnelClients` before the write.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/tsidp/ui.go:129-140,205-249`.
  The JSON API already rolls back, while the UI paths omit rollback.
- Shared change pressure: Not a DRY finding; the tsidp UI mutation path owns the commit decision.
- Impact: Source proof: runtime and file state diverge after OAuth client write failures.
  Production frequency and recovery behavior are unmeasured.
- Proposed direction: Write a candidate copy first and commit it to memory only after success.
- Risks and boundaries: Preserve the existing OAuth client state exactly on every failed write.
  Do not broaden the change beyond UI Create, Edit, and Regenerate commit ordering.
- Verification: Fail each UI write and require the in-memory client to remain exactly unchanged.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsidp issues and pull requests for the same root cause.

### ISSUE-2026-158 — tsnet-proxy: Tsnet proxy does not propagate TCP half-close to the backend

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Client EOF must propagate as a write-side close to the backend.
  Both `io.Copy` goroutines finish without a corresponding `CloseWrite`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `cmd/tsnet-proxy/tsnet-proxy.go:138-148`.
  Full connection-close defers run only after both copies finish.
- Shared change pressure: Not a DRY finding; the tsnet-proxy bidirectional copy loop owns half-close sequencing.
- Impact: Source proof: backends waiting for EOF before replying can block indefinitely.
  Production incidence is unmeasured.
- Proposed direction: Half-close the opposite side when each copy direction finishes.
- Risks and boundaries: Preserve bidirectional copying and defer full cleanup until both directions finish.
- Verification: Use a backend that replies after EOF and require the proxy to deliver the full reply.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsnet-proxy issues and pull requests for the same root cause.

### ISSUE-2026-159 — sessionrecording: Recorder connection attempts exceed their shared thirty-second budget

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: All recorder probe and connection attempts must share a 30-second deadline.
  The deadline limits only TCP dial, not the following HTTP handshake.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `sessionrecording/connect.go:27-88,386-429`.
  TailSSH intentionally passes `context.Background` for the later upload.
- Shared change pressure: Not a DRY finding; sessionrecording owns the transition from handshake to upload context.
- Impact: Source proof: SSH recording setup can hang on response headers or `100 Continue`.
  Production duration and incidence are unmeasured.
- Proposed direction: Use a separate handshake context, then switch to the upload context only after success.
- Risks and boundaries: Preserve the intentional `context.Background` lifetime for the later upload.
  Apply the 30-second budget to probing and connection setup, not successful upload.
- Verification: Accept the socket, withhold headers, and require return within the total 30-second budget.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior sessionrecording issues and pull requests for the same root cause.

### ISSUE-2026-160 — tailssh: Normal recording completion is classified as a premature upload failure

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: A nil recorder result after normal session completion must mean success.
  `rec.Close` runs before final session cancellation, so completion appears premature.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `ssh/tailssh/tailssh.go:1009-1010,1101,1472-1483`.
  The uploader goroutine sees an active session context and replaces nil.
- Shared change pressure: Not a DRY finding; TailSSH owns the ordering of session completion and recorder closure.
- Impact: Source proof: successful sessions emit false error events or termination messages.
  Production incidence is unmeasured.
- Proposed direction: Signal normal completion before closing the recorder writer.
- Risks and boundaries: Preserve actual premature-upload error reporting and final session cancellation.
  Keep recorder closure and uploader-goroutine cleanup ordered.
- Verification: Return nil after recorder body EOF and require that no error event is produced.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tailssh issues and pull requests for the same root cause.

### ISSUE-2026-161 — web: Self-update log callback overwrites earlier progress messages

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Every progress message must append to the existing React log history.
  The empty effect dependency set permanently captures the initial `updateLog`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `client/web/src/hooks/self-update.ts:30-45,114-122`.
  `setUpdateLog(updateLog + ...)` does not use a functional state update.
- Shared change pressure: Not a DRY finding; the self-update hook owns progress-history state updates.
- Impact: Source proof: rapid update messages collapse to the latest closure snapshot.
  Production timing and message-loss frequency are unmeasured.
- Proposed direction: Use a functional setter that derives each appended message from current state.
- Risks and boundaries: Preserve message order under closely spaced callback updates.
- Verification: Send three rapid progress messages and require all three to remain visible in order.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior web issues and pull requests for the same root cause.

### ISSUE-2026-162 — web: Web proxy dereferences a nil response after a LocalAPI transport error

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Proxy failures must become controlled HTTP error responses.
  The error path reads `resp.StatusCode` even though `resp` may be nil.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `client/web/web.go:1306-1309`.
  `http.Client.Do` can return `(nil, err)` on a transport error.
- Shared change pressure: Not a DRY finding; the web proxy handler owns response validation after `http.Client.Do`.
- Impact: Source proof: LocalAPI transport errors cause handler panics instead of clean web errors.
  Production incidence is unmeasured.
- Proposed direction: Handle the error before any response dereference and send a stable gateway error.
- Risks and boundaries: Preserve response ownership and cleanup when `resp` is non-nil.
- Verification: Refuse the LocalAPI connection and require an error status without a handler panic.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior web issues and pull requests for the same root cause.

### ISSUE-2026-163 — client: ACL client requests and decodes conflicting representations

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven request construction; current external API behavior assumed.
- Internal priority: Medium.
- Confidence: Medium.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: `ACL` must request JSON, and `ACLHuJSON` details must request the JSON wrapper.
  `ACL` omits Accept, while the details request incorrectly sets HuJSON.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `client/tailscale/acl.go:87-107,130-156`.
  The cited current reference is https://tailscale.com/docs/reference/tailscale-api, but it remains unverified.
- Shared change pressure: Not a DRY finding; the ACL client owns representation selection for each decode path.
- Impact: Source proof: HuJSON comments or details responses can fail JSON decoding.
  Production behavior under the unverified external API contract is unmeasured.
- Proposed direction: Request JSON for `ACL`; omit the Accept header for the `ACLHuJSON` details request.
- Risks and boundaries: Preserve the assumption that the current external API contract is unverified.
  Do not change decoding beyond the JSON and details-wrapper representation boundaries.
- Verification: Decode commented HuJSON and the details wrapper through both respective methods.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior client issues and pull requests for the same root cause.

### ISSUE-2026-164 — client: Device mutations reject valid non-200 success statuses

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven status check; non-200 2xx response not observed.
- Internal priority: Low.
- Confidence: Medium.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Any successful 2xx status with an empty object must mean success.
  `SetAuthorized` and `SetTags` accept only status 200.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The defect is at `client/tailscale/devices.go:242-295`.
  Status 201 or 204 is incorrectly passed to `HandleErrorResponse`; none was observed.
- Shared change pressure: Not a DRY finding; the device client owns success classification for both mutations.
- Impact: Source proof: a 201 or 204 result is classified as an error.
  No non-200 success response was observed, so production effect is unmeasured.
- Proposed direction: Use one success check covering the full 200 through 299 range.
- Risks and boundaries: Preserve `HandleErrorResponse` behavior for every non-2xx status.
  Preserve empty-object handling for `SetAuthorized` and `SetTags`.
- Verification: Return 201 and 204 from both endpoints and require both methods to return nil.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior client issues and pull requests for the same root cause.

### ISSUE-2026-165 — controlbase: Empty Synology target file blocks migration of valid legacy state

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: Synology migration skips the legacy path only when the new state file is nonempty.
  An empty existing file is also terminal because `!os.IsNotExist(nil)` is true.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected logic is at `cmd/tailscaled/tailscaled.go:341-365`.
  The comment names a nonempty file as the only migration skip case.
- Shared change pressure: Not a DRY finding; the Synology state migration is the single decision owner.
- Impact: Source-proven: Tailscaled starts unconfigured despite valid legacy node state.
  The production frequency is unmeasured.
- Proposed direction: Treat only an existing file with positive size as already migrated.
- Risks and boundaries: Preserve valid new state and migrate only when the target is empty.
- Verification: Create an empty target and valid legacy state; verify that the legacy state is migrated.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-166 — controlbase: Windows service uninstall reports full success after timeout

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: CLI correctness.
- Publication target: Undecided.
- Summary: Uninstall succeeds only when the service can no longer be opened.
  After 15 seconds, the function returns nil regardless of whether the service still exists.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected logic is at `cmd/tailscaled/install_windows.go:115-135`.
  Non-NotFound `OpenService` errors are also misread as service disappearance.
- Shared change pressure: Not a DRY finding; Windows service uninstall is the single decision owner.
- Impact: Source-proven: The CLI confirms removal while the service can remain in the SCM.
  The production frequency is unmeasured.
- Proposed direction: Return concrete errors for timeout and every non-NotFound `OpenService` error.
- Risks and boundaries: Preserve success only for confirmed service disappearance.
- Verification: Keep the service open beyond 15 seconds; verify that uninstall exits nonzero.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-167 — controlbase: Windows service errors are logged but exit as success

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: CLI correctness.
- Publication target: Undecided.
- Summary: Non-nil errors from `svc.Run` must reach the top-level process.
  `run` logs the error and then returns nil.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected logic is at `cmd/tailscaled/tailscaled.go:499-507`.
  `main` calls `Fatal` only for an error that is actually returned.
- Shared change pressure: Not a DRY finding; the Windows service runner is the single decision owner.
- Impact: Source-proven: SCM or registration failures end Tailscaled with a misleading success status.
  The production frequency is unmeasured.
- Proposed direction: Wrap and propagate errors from `runWindowsService` to `main`.
- Risks and boundaries: Preserve the original service error while adding only contextual wrapping.
- Verification: Force `svc.Run` to fail; verify that the process reports the same error and exits nonzero.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-168 — controlbase: Babysitter dereferences process fields before child startup succeeds

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: Signals reach child resources only after those resources are fully published.
  A goroutine uses `proc.p` and `wStdin` before `cmd.Start` succeeds.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected logic is at `cmd/tailscaled/tailscaled_windows.go:437-455,507-521`.
  Both fields remain nil during pipe setup and after startup failures.
- Shared change pressure: Not a DRY finding; the Windows babysitter lifecycle is the single decision owner.
- Impact: Source-proven: Stop or signal during startup can panic the Windows babysitter.
  Production timing and frequency are unmeasured.
- Proposed direction: Publish the lifecycle atomically and fully handle early cancellation before spawn.
- Risks and boundaries: Preserve concurrency ordering, child cleanup, and cancellation before and after spawn.
- Verification: Interleave stop before, during, and after startup; verify that no panic or hang follows.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-169 — controlbase: Server status waits only after sending an error status

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: A `wait=true` request evaluates backend state after waiting completes.
  The handler writes and flushes 503 before `awaitBackend`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected logic is at `ipn/ipnserver/server.go:136-149`.
  A backend set later cannot change the already committed HTTP status.
- Shared change pressure: Not a DRY finding; the server-status handler is the single decision owner.
- Impact: Source-proven: Waiting clients can receive 503 with a success body after backend startup.
  The production frequency is unmeasured.
- Proposed direction: Wait first, then write exactly one consistent status and body.
- Risks and boundaries: Preserve readiness waiting and avoid multiple status writes or flushes.
- Verification: Set the backend after a delay; verify that the waiting request receives status 200.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-170 — tsdial: Unix safesocket startup permits two active listeners with one name

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: A socket path has exactly one listener at any time.
  Probe, Remove, and Listen are not atomic across concurrent starts.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected logic is at `safesocket/unixsocket.go:24-70`.
  Process A can unlink the socket already bound by process B.
- Shared change pressure: Not a DRY finding; Unix socket-path ownership is the single decision owner.
- Impact: Source-proven: Two daemons can split LocalAPI requests across different states and engines.
  The production frequency is unmeasured.
- Proposed direction: Hold a path-bound lifetime lock until the listener closes.
- Risks and boundaries: Preserve exclusive path ownership and release the lock during listener cleanup.
- Verification: Start two instances at a barrier; verify that exactly one becomes the listener.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsdial issues and pull requests for the same root cause.

### ISSUE-2026-171 — controlbase: Windows actor does not explicitly close duplicated access tokens

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven ownership violation; resource impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: Every caller of `WindowsToken` closes its duplicated handle.
  `connIsLocalSystem` drops the wrapper after `IsLocalSystem`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected logic is at `ipn/ipnserver/actor.go:199-202`.
  Other actor paths use `defer token.Close` for the same ownership contract.
- Shared change pressure: Not a DRY finding; `connIsLocalSystem` is the single token-ownership decision owner.
- Impact: Source-proven: The duplicated token ownership contract is violated.
  Resource growth is unmeasured, and a finalizer exists.
- Proposed direction: Register `defer token.Close` immediately after successful token retrieval.
- Risks and boundaries: Preserve Windows handle ownership and avoid closing a token before `IsLocalSystem` completes.
- Verification: Open many named-pipe connections; verify that the handle count remains stable after close.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-172 — containerboot: Missing HOME variables produce a relative daemon state path

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: The automatic `--state` path is absolute or remains unspecified.
  Without HOME and XDG, it becomes `.local/share/tailscale/tailscaled.state`.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected logic is at `paths/paths_unix.go:59-71`.
  FileStore creates the directory relative to the daemon's current working directory.
- Shared change pressure: Not a DRY finding; Unix default-state path selection is the single decision owner.
- Impact: Source-proven: State can move with the working directory or appear lost after restart.
  The production frequency is unmeasured.
- Proposed direction: Reject a missing or relative XDG base and return no default path.
- Risks and boundaries: Preserve absolute-path persistence and do not migrate state between working directories.
- Verification: Clear HOME and XDG; verify that the default path is never relative.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior containerboot issues and pull requests for the same root cause.

### ISSUE-2026-173 — derper: DERP consistency check compares connections with unique keys

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: `ConsistencyCheck` accepts the documented duplicate-connection state.
  It compares `curClients` with the count of unique keys.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected logic is at `derp/derpserver/derpserver.go:770-815,2507-2511`.
  Multiple connections increase only `curClients`, not `numLocalClientKeys`.
- Shared change pressure: Not a DRY finding; DERP consistency accounting is the single decision owner.
- Impact: Source-proven: Supported overlapping peer connections cause a false HTTP 500 diagnostic error.
  The production frequency is unmeasured.
- Proposed direction: Sum connections across all ClientSets and compare keys separately.
- Risks and boundaries: Preserve duplicate connections while keeping connection and unique-key invariants distinct.
- Verification: Use two connections for one key; verify that `ConsistencyCheck` succeeds.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior derper issues and pull requests for the same root cause.

### ISSUE-2026-174 — controlbase: Stale health timers publish obsolete warning states

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: `Change.UnhealthyState` describes the current warning state when published.
  Old timers check only whether any newer state exists.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected logic is at `health/health.go:448-489`.
  Healthy-then-Unhealthy lets an old callback survive against the new map entry.
- Shared change pressure: Not a DRY finding; health-state timer publication is the single decision owner.
- Impact: Source-proven: The event bus and logs can report an earlier warning after state changes.
  Production timing and frequency are unmeasured.
- Proposed direction: Bind each callback to the stored state identity and timer generation.
- Risks and boundaries: Preserve timer concurrency and suppress only callbacks superseded by newer state.
- Verification: Delay the old timer and replace the state; verify that only the new payload is published.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior controlbase issues and pull requests for the same root cause.

### ISSUE-2026-175 — taildrop: Taildrop reports failed reverse-proxy transfers as successful

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: `Succeeded` must be true only after a transfer completes successfully.
  After `ReverseProxy.ServeHTTP`, success is published regardless of HTTP status.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `feature/taildrop/localapi.go:323-400`.
  Transport errors produce 502 and upstream errors produce 500 without calling `fail`.
- Shared change pressure: Not a DRY finding; Taildrop owns the proxy transfer outcome decision.
- Impact: Source-proven: exported progress data contradicts the error observed by the PushFile caller.
  The production frequency and scale are unmeasured.
- Proposed direction: Capture proxy errors and non-200 statuses, then publish `Succeeded=false`.
- Risks and boundaries: Preserve successful HTTP 200 progress while changing only proxy-failure reporting.
- Verification: Compare progress for a transport error, HTTP 500, and HTTP 200.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior taildrop issues and pull requests for the same root cause.

### ISSUE-2026-176 — capture: Capture registration can revive an already closed sink

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: No new outputs may be registered after the sink closes.
  The closed check and map insertion occur on opposite sides of the mutex.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `feature/capture/capture.go:112-160`.
  Close can clear the map and return between the check and registration.
- Shared change pressure: Not a DRY finding; capture owns the sink registration and close decision.
- Impact: Source-proven: a request gets HTTP 200 and headers but no live capture stream.
  The production frequency and scale are unmeasured.
- Proposed direction: Perform the closed check and registration in one locked operation.
- Risks and boundaries: Keep close and registration atomic without reviving outputs or leaking map entries.
- Verification: Pause registration before the lock, close the sink, and exclude any later map entry.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior capture issues and pull requests for the same root cause.

### ISSUE-2026-177 — portlist: Windows port listing drops explicit non-loopback bindings

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: `IncludeLocalhost=false` must exclude only loopback listeners.
  The Windows filter accepts only unspecified bind addresses.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `portlist/portlist_windows.go:63-65`.
  Explicit LAN or host addresses are not loopback, but the filter drops them.
- Shared change pressure: Not a DRY finding; Windows portlist filtering owns the listener inclusion decision.
- Impact: Source-proven: valid services are absent from the uploaded Hostinfo service state.
  The production frequency and scale are unmeasured.
- Proposed direction: Filter only `Addr().IsLoopback()` instead of filtering every specific address.
- Risks and boundaries: Preserve the `IncludeLocalhost=false` boundary while admitting specific non-loopback binds.
- Verification: Bind a service to a LAN IP and require it to appear in Hostinfo.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior portlist issues and pull requests for the same root cause.

### ISSUE-2026-178 — portlist: macOS port listing publishes partial results from failed netstat processes

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Platform correctness.
- Publication target: Undecided.
- Summary: A non-zero `netstat` exit must discard the incomplete service measurement.
  The deferred `Process.Wait` error is ignored completely.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `portlist/portlist_macos.go:111-124`.
  Parser EOF counts as success and makes the partial snapshot publishable.
- Shared change pressure: Not a DRY finding; macOS portlist collection owns the snapshot acceptance decision.
- Impact: Source-proven: failed enumeration can replace prior service state with an empty snapshot.
  The production frequency and scale are unmeasured.
- Proposed direction: Wait for process exit after parsing and return an error for a non-zero exit.
- Risks and boundaries: Preserve the prior snapshot whenever process completion invalidates parsed output.
- Verification: Emit one valid line, then fail the process; the snapshot must not change.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior portlist issues and pull requests for the same root cause.

### ISSUE-2026-179 — tsconsensus: Consensus error interface breaks during a follower JSON round trip

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Protocol correctness.
- Publication target: Undecided.
- Summary: State-machine errors must remain distinct from outer transport errors.
  A concrete `error` is sent as `{}` and decoded into an interface.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `tsconsensus/http.go:67-91,150-167`.
  The normal `ip pool exhausted` error follows this exact follower path.
- Shared change pressure: Not a DRY finding; tsconsensus HTTP encoding owns the command error wire decision.
- Impact: Source-proven: domain errors appear as JSON protocol errors instead of `CommandResult.Err`.
  The production frequency and scale are unmeasured.
- Proposed direction: Use an explicit wire DTO with an error code and message at the HTTP boundary.
- Risks and boundaries: Keep state-machine errors separate from outer transport and JSON decoding failures.
- Verification: Send pool exhaustion through a follower; decoding must set the result error and succeed.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior tsconsensus issues and pull requests for the same root cause.

### ISSUE-2026-180 — containerboot: IPv4 DNS targets are also classified as IPv6 backends

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven contract violation; production impact not measured.
- Internal priority: Low.
- Confidence: High.
- Type: Networking correctness.
- Publication target: Undecided.
- Summary: Each backend address must belong to exactly one IP-family list.
  For IPv4, both `To4` and `To16` return a value.
- Evidence: Current `upstream/main` is `e1e5325c22a46a9df2e76d725f01f92065885138`.
  The affected code is at `cmd/containerboot/forwarding.go:212-227,252-254`.
  The mapped `::ffff` value is then treated as an IPv6 address.
- Shared change pressure: Not a DRY finding; containerboot forwarding owns the backend family decision.
- Impact: Source-proven: IPv4-only config enables needless IPv6 forwarding and reports false warnings.
  The production frequency and scale are unmeasured.
- Proposed direction: Classify IPv4 first and `continue` before checking IPv6.
- Risks and boundaries: Keep IPv4, mapped IPv4, and native IPv6 mutually exclusive during classification.
- Verification: Require IPv4, mapped IPv4, and native IPv6 to enter exactly one list each.
- Missing publication evidence: Reproduce the focused failure on the recorded current revision.
  Search prior containerboot issues and pull requests for the same root cause.