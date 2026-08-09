# Issue and Pull Request Publication Status

This file is the sole source of truth for every finding's ID, delivery mode, lifecycle status, evidence, and location.
Read and update this ledger instead of inferring state from chat history, clone reports, or earlier reviews.
`FORMAT.md` owns research, drafting, implementation authorization, approval, and publication rules.

Next finding ID: ISSUE-2026-074

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

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; CI impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Error and orchestration.
- Publication target: Undecided.
- Summary: `testsForShard` maps invalid shard specifications and `go list` failures to an empty result.
  `runTests` treats that result as a legitimately empty shard and reports the package as skipped.
- Evidence: `cmd/testwrapper/testwrapper.go:181-199` returns `nil, nil` for both error classes.
  `cmd/testwrapper/testwrapper.go:278-286` converts a zero-length result into a successful skip.
- Shared change pressure: Not a DRY finding; one discovery boundary conflates failure with valid emptiness.
- Impact: Source proves that sharded tests can be omitted without a failing process status.
  Occurrence frequency in current CI is not measured.
- Proposed direction: Return errors for invalid specifications and failed discovery.
  Preserve a distinct successful result for a valid empty shard.
- Risks and boundaries: Do not turn a legitimately empty shard into a failure.
  Preserve package-level fatal error and exit-code handling.
- Verification: Exercise an invalid shard, a failing `go list`, and a valid shard with no assigned tests.
  Only the valid empty shard should remain a successful skip.
- Missing publication evidence: Verify current `upstream/main` and reproduce the false-green outcomes.
  Search prior testwrapper and sharding issues and pull requests.

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

### ISSUE-2026-012 — netmapcache: Removed peer digest suppresses an identical re-add

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; startup impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Persistence and state.
- Publication target: Undecided.
- Summary: `UpdatePeers` removes stored peer values but retains their `lastWrote` digests.
  Missing Store values retain the same stale digests, and the LocalBackend caller resolves removed peers too late.
- Evidence: `ipn/ipnlocal/netmapcache/netmapcache.go:81-105` skips writes on digest equality.
  Lines 253-281 remove Store keys without invalidating `lastWrote`.
  Lines 333-337 and 411-423 retain digests after missing-key reads.
  `ipn/ipnlocal/local.go:2565-2569` looks up removed NodeIDs after applying the delta.
- Shared change pressure: Not a DRY finding; cache values, digests, and removed-peer identity have one consistency owner.
- Impact: Source proves that `Load` can omit a peer after a remove and identical re-add sequence.
  The current caller can also fail to send peer removals to the disk cache.
  Production sequence frequency and startup impact are not measured.
- Proposed direction: Capture removed StableIDs before applying the delta.
  Delete `lastWrote[key]` on explicit removal and every missing-key path, including the self value.
- Risks and boundaries: Preserve write suppression for unchanged values that remain in the Store.
  Invalidate the digest even when removal is uncertain so a later write can repair storage.
- Verification: Store a map, remove a peer, re-add identical content, and load the cache.
  Cover missing peer and self values plus the production delta caller.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce with FileStore.
  Search prior netmap cache delta issues and pull requests.

### ISSUE-2026-013 — k8s-operator: Unavailable ProxyGroup falls through to valid

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; cluster impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Validation and state.
- Publication target: Undecided.
- Summary: Egress validation sets `EgressSvcValid` to Unknown when a ProxyGroup is unavailable.
  It then falls through, overwrites the condition with True, and permits provisioning.
- Evidence: `cmd/k8s-operator/egress-services.go:572-575` owns the unavailable branch.
  Lines 577-579 unconditionally set valid True and return `true`.
- Shared change pressure: Not a DRY finding; one validator has a missing terminal branch.
- Impact: Source proves internally contradictory status and premature provisioning eligibility.
  Cluster occurrence frequency is not measured.
- Proposed direction: Return `false, nil` immediately after applying the unavailable conditions.
- Risks and boundaries: Preserve requeue through the existing ProxyGroup watch.
  Do not change invalid, missing, or retrieval-error branches.
- Verification: Reconcile an Egress Service with an existing unavailable ProxyGroup.
  Confirm no child provisioning and no overwrite of Unknown with True.
- Missing publication evidence: Verify current `upstream/main` and reproduce with a fake Kubernetes client.
  Search prior ProxyGroup availability issues and pull requests.

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
- Evidence class: Source-proven; user impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: State and error.
- Publication target: Undecided.
- Summary: Declarative `get-config`, `set-config`, and `clear` dereference a nil `ServeConfig`.
  Other Serve paths establish nil as the normal no-configuration state.
- Evidence: `cmd/tailscale/cli/serve_v2.go:633-637`, 665-802, and 883-896 dereference the result.
  `cmd/tailscale/cli/serve_v2.go:445-448` and `serve_status.go:20-23` normalize or accept nil.
- Shared change pressure: Three commands consume the same empty-state contract without its existing normalization.
- Impact: Source proves first-use panics or nil dereferences when no Serve configuration exists.
  User frequency is not measured.
- Proposed direction: Normalize nil immediately after each `GetServeConfig`.
  Treat `clear` with no config as a no-op.
- Risks and boundaries: Preserve the ability of `set-config` to create a new empty-backed configuration.
  Avoid unnecessary writes from `clear`.
- Verification: Run all three commands with a LocalClient returning nil ServeConfig.
  Confirm no panic and the expected empty or no-op result.
- Missing publication evidence: Verify current `upstream/main` and reproduce all three commands.
  Search current Serve config issues and pull requests.

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

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven; user impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Error and filesystem.
- Publication target: Undecided.
- Summary: `kubeconfigPath` dereferences `FileInfo` after any non-ENOENT `os.Stat` result.
  Permission and other access errors can return a nil `FileInfo` and trigger a panic.
- Evidence: `cmd/tailscale/cli/configure-kube.go:54-63` owns path selection and the unsafe dereference.
  `cmd/tailscale/cli/configure-kube.go:80-103` already owns actionable access-error reporting.
- Shared change pressure: Not a DRY finding; path selection bypasses the existing error boundary.
- Impact: Source proves a panic path for inaccessible KUBECONFIG entries.
  User frequency is not measured.
- Proposed direction: Dereference `FileInfo` only when `os.Stat` succeeds.
  Preserve list precedence and let `checkKubeconfigWritable` report access failures.
- Risks and boundaries: Keep existing behavior for nonexistent entries and multi-path KUBECONFIG values.
  Do not silently choose a different writable file after an access error.
- Verification: Exercise a denied parent, a nonexistent entry, a directory, and a normal file.
  Confirm no panic and unchanged path precedence.
- Missing publication evidence: Verify current `upstream/main` and reproduce as an unprivileged user.
  Search prior kubeconfig path issues and pull requests.

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
- Evidence class: Source-proven; automation impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Output.
- Publication target: Undecided.
- Summary: The status contract promises deterministic ordering, but node TCP entries range a Go map directly.
  Unchanged configurations can therefore produce reordered lines.
- Evidence: `cmd/tailscale/cli/serve_status.go:26-33` documents deterministic ordering.
  `cmd/tailscale/cli/serve_legacy.go:648-673` iterates `sc.TCP` without sorting.
- Shared change pressure: Web and Service renderers already sort their map-owned output.
  The node TCP renderer implements the same ordering policy differently.
- Impact: Source proves unstable output ordering with multiple node TCP forwards.
  Parser and diff impact are not measured.
- Proposed direction: Sort numeric TCP port keys before rendering.
- Risks and boundaries: Preserve group ordering, line contents, and numeric rather than lexical order.
- Verification: Render two or more ports repeatedly and require byte-identical ascending output.
- Missing publication evidence: Verify current `upstream/main` and reproduce repeated output.
  Search Serve status ordering issues and pull requests.

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

### ISSUE-2026-035 — netstack: Rejected TCP flows remain in packetsInFlight

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven growth path; production impact not measured.
- Internal priority: High.
- Confidence: High.
- Type: Networking and lifecycle.
- Publication target: Undecided.
- Summary: `wrapTCP` inserts a flow key before admission and returns on overload without deleting it.
  Rejected unique flows can therefore accumulate in `packetsInFlight`.
- Evidence: `wgengine/netstack/netstack.go:542-571` inserts before the `tooMany` return.
  Cleanup is installed only in later paths at lines 584-610.
- Shared change pressure: Not a DRY finding; one admission owner must pair map insertion and cleanup.
- Impact: Source proves retained keys for rejected flows.
  Trigger rate, retained bytes, and production memory impact are not measured.
- Proposed direction: Insert only after successful admission or delete immediately on rejection.
- Risks and boundaries: Never decrement counters that the rejected path did not increment.
  Preserve per-client and global admission semantics.
- Verification: Send unique rejected TCP flows and assert bounded map cardinality after every return.
- Missing publication evidence: Record the exact current `upstream/main` revision and reproduce the growth.
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