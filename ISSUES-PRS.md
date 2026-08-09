# Issue and Pull Request Publication Status

This file is the sole source of truth for every finding's ID, delivery mode, lifecycle status, evidence, and location.
Read and update this ledger instead of inferring state from chat history, clone reports, or earlier reviews.
`FORMAT.md` owns research, drafting, implementation authorization, approval, and publication rules.

Next finding ID: ISSUE-2026-035

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
- Summary: `UpdatePeers` removes the stored peer value but retains its `lastWrote` digest.
  Re-adding identical content can skip the write because the stale digest still matches.
- Evidence: `ipn/ipnlocal/netmapcache/netmapcache.go:81-105` skips writes on digest equality.
  Lines 253-281 remove Store keys without invalidating `lastWrote`.
- Shared change pressure: Not a DRY finding; the cache's file and digest state diverge during peer removal.
- Impact: Source proves that `Load` can omit a peer after a remove and identical re-add sequence.
  Production sequence frequency is not measured.
- Proposed direction: Delete `lastWrote[key]` after a successful Store removal.
  Keep the digest when removal fails and the stored value may still exist.
- Risks and boundaries: Preserve write suppression for unchanged values that remain in the Store.
  Do not invalidate state before knowing removal succeeded.
- Verification: Store a map, remove a peer, re-add the identical peer, and load the cache.
  Confirm that the peer value is written and restored.
- Missing publication evidence: Verify current `upstream/main` and reproduce with the production FileStore.
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
- Summary: A post-open receive failure leaves the newly created destination while retaining the inbox item.
  The default conflict policy then blocks every retry on that partial path.
- Evidence: `cmd/tailscale/cli/file.go:646-707` creates the destination and leaves it on quarantine or copy failure.
  Lines 739-751 delete the inbox item only after a successful receive.
- Shared change pressure: Not a DRY finding; one receive owner does not unwind its newly created filesystem state.
- Impact: Source proves that a transient transfer failure can require manual destination cleanup before retry.
  Failure frequency is not measured.
- Proposed direction: Close and remove only the destination created by the current attempt on every post-open failure.
- Risks and boundaries: Never remove a pre-existing user file or a path created by another process.
  Preserve inbox retention so the transfer remains retryable.
- Verification: Inject quarantine, copy, and close failures, then retry with the default conflict policy.
  Confirm cleanup and successful retry.
- Missing publication evidence: Verify current `upstream/main` and reproduce a partial transfer.
  Search Taildrop retry and partial-file issues and pull requests.

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

### ISSUE-2026-032 — k8s-operator: Stable Egress EndpointSlices are always updated

- Status: Hold.
- Delivery mode: Undecided.
- Location: Not published.
- Evidence class: Source-proven cost path; cluster impact not measured.
- Internal priority: Medium.
- Confidence: High.
- Type: Orchestration and persistence.
- Publication target: Undecided.
- Summary: `ensureEndpointSlices` reaches an update helper for every existing Slice.
  The helper writes the object even when all controller-owned desired fields are unchanged.
- Evidence: `cmd/k8s-operator/egress-services.go:246-276` always calls `createOrUpdate`.
  `cmd/k8s-operator/sts.go:1190-1210` updates every existing object after the callback.
- Shared change pressure: `cmd/k8s-operator/egress-eps.go:133-141` already owns an equality-guarded Slice update.
- Impact: Source proves avoidable Kubernetes API writes and follow-on events.
  Frequency and cluster cost are not measured.
- Proposed direction: Compare only Egress-owned labels, address type, ports, and readiness fields before updating.
- Risks and boundaries: Exclude server-defaulted and externally managed fields from equality.
  Preserve required clearing of endpoint readiness.
- Verification: Reconcile an unchanged Egress Slice twice and count Update calls and ResourceVersion changes.
  Confirm real owned-field drift still updates.
- Missing publication evidence: Verify current `upstream/main` and measure repeated reconciliation.
  Search EndpointSlice no-op update issues and pull requests.

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