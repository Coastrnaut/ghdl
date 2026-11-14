# GHDL for Apple — AutoCreator Engineering Contract

**Repository:** https://github.com/Coastrnaut/ghdl  
**Execution branch:** ios (branch from master commit 8e642a9a5168bfe39bda65e23e5f7a6fdfec1b61 on 2026-11-14)  
**Upstream:** https://github.com/ghdl/ghdl  
**Status:** PLANNING ONLY. These instructions do not mean that an Apple app, portable backend, toolchain, or automation scripts already exist.

This is the **single forward-looking design and execution book** for making GHDL a self-contained, offline Apple-platform VHDL development application. The short, automatically loaded agent contract is [CLAUDE.md](CLAUDE.md). Keep this document edited in place; do not append daily progress or successive contradictory plans. Track live progress in a machine-readable ledger, versioned specs, ADRs, and reproducible test evidence *once those facilities have been created*.

## 1. Mission and deliverable

Ship **one coherent offline VHDL IDE/simulator for iPhone, iPad, and Mac** built around the real GHDL front end and a legally distributable, Apple-compatible execution engine. Local users can create/open multi-file VHDL projects, analyze, elaborate, run bounded simulations, inspect diagnostics and hierarchy, view waveforms, and export VCD. The app must work without a network, account, desktop companion, remote service, downloaded compiler, or third-party toolchain on the end user's device.

The work is also an **upstream engineering contribution**: isolate GHDL changes into minimal reviewable PRs with host regression coverage; retain existing native backends, and avoid a lasting unmergeable fork. The Apple UI must not subsume or duplicate the compiler.

**Critical distinction:** The mobile app is the product goal; technical feasibility and permission to distribute are *not* established yet. Never claim GHDL is already working on iOS or that Apple has accepted a GPL distribution.

### Product definition

- VHDL source editing with line numbers, diagnostics, jump-to-definition if supported, syntax highlighting, and accessibility.
- Multi-file projects with WORK/IEEE and additional local libraries; local Files-app import/export, sandbox-safe document access and crash-safe project persistence.
- GHDL-backed analyze/elaborate/simulate, process reports/assertions, safe cancellation, time/step limits and resource budgets.
- Correct event scheduling, delta cycles, signal resolution, drivers, time, process wakeups and waveforms; not a fake RTL evaluator.
- VCD waveform browser with time navigation, cursors, zoom, search, signal grouping, value inspection, and export; retain GHW/FST only when verified compatible.
- Offline samples: combinational logic, sequential logic, counter, FSM, multiple entities, packages, testbench, std_logic_1164, numeric_std, VHDL-2008 constructs accepted by the pinned reference.
- iPhone navigation optimized for limited screen; iPad and macOS multi-column editing, keyboard, accessibility and responsive layout.
- No analytics, tracking, advertisements, subscriptions, mandatory accounts or network services. Pricing is a **later owner decision**, not an implicit entitlement to ship GPL software commercially.

**Release quality:** Real local analysis and simulation, testable output and robust failure behavior. A project that displays a diagnostic screen or simulated waveforms from fixtures alone is not done.

## 2. Verified baseline versus open hypotheses (2026-11-14)

Evidence inspected at the original fork master revision above:

- The fork README describes a native code-generating VHDL compiler, simulator and experimental synthesis flow; it says VHDL-1987/1993/2002 full and 2008/2019 partial support. Do not promise more than that.
- The README lists LLVM, GCC, and built-in x86-family mcode backends, and names the deprecated interpreted simulator, ghdl_simul, as slow and feature-incomplete.
- The build starts from the existing configure script, whose default backend is mcode and whose development version is 7.0.0-dev in the inspected revision.
- GHDL's existing waveform support includes VCD, FST and GHW. The exact reusable APIs and their embedding limitations still require source investigation.
- COPYING.md contains GPLv2. Source-file licenses, runtime/standard packages, external code and copyright holders require a file-level audit.
- Apple App Review Guidelines §§2.4.5, 2.5.1 and 2.5.2 are relevant; their application to user-written VHDL execution must be established, not assumed.
- The reference projects VisualCore and projectm-ios established an agent-driven workflow using steering contracts, Spec Kit dossiers, evidence-backed task checkboxes, short foreground tests, simulator-first development, up-to-ten independent research agents, and strict owner-only release boundaries.

**Unverified:** Ada/GNAT iOS cross-toolchain; ability to compile all required runtime features for iOS; whether historical ghdl_simul is suitable; what Apple permits for the actual in-app execution design; exact licensing exception rights; actual binary size, speed, memory requirements and physical device behavior. Record experiments before architecture commitments.

Reference material:
- https://github.com/Coastrnaut/VisualCore/blob/main/Auto-Creator.md
- https://github.com/Coastrnaut/VisualCore/blob/main/CLAUDE.md
- https://github.com/Coastrnaut/projectm-ios/blob/main/CLAUDE.md
- https://github.com/ghdl/ghdl/blob/master/doc/licenses.rst
- https://developer.apple.com/app-store/review/guidelines/

**Only section 2 is a snapshot.** Everywhere else is a requirement, proposal or future gate, not a claim of implementation. Always print current branch, HEAD, worktree and evidence status before continuing.

## 3. Hard constraints and precedence

In order of precedence: observed compiler/Apple-toolchain behavior and applicable law; explicit current owner requests; this contract and CLAUDE.md; the project constitution once generated; accepted ADRs; Spec Kit feature specs and plans; old notes or examples. When guidance conflicts, document and resolve it rather than silently discarding a requirement.

1. All compilation, elaboration and simulation occurs locally, inside the app; no remote execution.
2. Reuse GHDL front end, language rules, IEEE libraries and runtime to the maximum verified extent.
3. No iOS native JIT, writable/executable memory workaround, private entitlement, unsigned generated executable, arbitrary child process, or runtime-downloaded executable code.
4. Do not infer compliance from WASM alone: interpreter-only WASM may be a hypothesis, not an App Review approval.
5. iOS/iPadOS need an in-process, non-JIT execution path. macOS may use another backend only if distributable as a self-contained sandboxed app with no Homebrew dependency.
6. Preserve VHDL simulation semantics. Explicitly label unsupported features; differential-test against reference GHDL at identical configuration and standard.
7. Maintain physical-device separation: simulator success is never a device result.
8. No unlicensed code, hidden fork, license removal, fake GPL exception, assumption about a single copyright owner, or upload before the legal gate is approved.
9. No destructive git reset, force-push, rewriting upstream history, secret/signing-file commit or device-OS changes.
10. Never fabricate executed tests, created assets, evidence, upstream approval or release readiness.
11. Everything required to build must be reconstructible from a fresh clone and documented prerequisites. Generated build products, SDKs, signing data and test logs are not tracked.
12. A backend is completed only by **working compilation + semantic equivalence tests**, not by building a framework containing stubs.

## 4. Architecture decision order (do not preselect a fashionable backend)

**Reference:** Build normal GHDL on a supported host at a pinned commit. Record commands, versions, tests and golden outputs. Verify language behavior and runtime before making compiler changes.

**Candidate A — restore/extend ghdl_simul.** Audit the actual implementation, dependencies, unsupported language features, execution model and performance. Attempt a bounded host proof of concept. Prefer this if it can reuse correct GHDL semantic and runtime components with tractable changes.

**Candidate B — GHDL portable bytecode VM.** If A cannot satisfy coverage or maintainability, reuse GHDL's analyzed/elaborated IR to define typed bytecode and a no-JIT interpreter. Document lowering, call frames, signal operations, delay scheduling, diagnostics, exceptions and cleanup. Never claim this is simple.

**Candidate C — interpreted WASM.** Compare compiling the engine itself to WASM against lowering designs to WASM. Validate Ada cross-target/toolchain, host callbacks, filesystem/ABI, sandbox and interpreter performance. No JIT, no process execution, no automatic policy claim.

**Candidate D — hybrid platform.** A portable in-process backend on iOS/iPadOS, a separately verified native compiled path for macOS. Share front-end, public API and tests; do not require an external executable in a Mac App Store package.

Evaluate each on language fidelity, iOS feasibility, build repeatability, code ownership, expected cost, binary/memory limits, maintainability, licensing, upstream acceptability and measurable performance. Write an ADR selecting one after small experiments. **Fallback if GNAT iOS proves infeasible:** research an in-process interpreter/WASM execution architecture with fully demonstrated compiler semantics and legal distribution. Do not replace GHDL silently with Tree-Sitter or a different VHDL simulator.

## 5. Target layered design

~~~
SwiftUI app (iOS, iPadOS, macOS)
  -> app services: project/document handling, cancellation, diagnostics, waveforms
    -> C ABI / Swift bridge (single runtime owner, explicit error and memory contract)
      -> reused GHDL parser / semantic analysis / elaboration
        -> portable interpreted execution backend on iOS and iPadOS
        -> separately validated native or portable backend on macOS
      -> reused standard libraries, simulation runtime, waveform writer
~~~

**Proposed layout to add incrementally (none of these directories is asserted to exist today):**

~~~
CLAUDE.md
autocreator.md
src/                        # preserve existing upstream GHDL sources and layout
tests/                      # preserve original regression tests
apple/
  App/                      # SwiftUI shells and local project services
  Packages/
    GHDLBridge/              # Swift-facing C interface and ownership wrapper
    WaveformKit/             # VCD parser, timeline model and waveform UI
    EditorKit/               # editor and diagnostics UI
  Harnesses/                 # isolated C ABI and engine-smoke harnesses
  Configs/                   # nonsecret shared build configuration
  project.yml                # optional XcodeGen, only after ADR
  README.md
  Sources/                   # Apple integration glue, not a compiler rewrite
scripts/
  apple/                     # deterministic Python 3 stdlib-first automation
specs/                       # Spec Kit feature dossiers; numbered immutably
.specify/                    # Spec Kit, after bootstrapping
.autonomy/                   # tracked ledger and evidence; local logs ignored
docs/
  apple/                     # feasibility, architecture, test reports, compatibility
  adr/                       # accepted decision records
  upstream/                  # patch rationale / PR boundaries and tracking
~~~

Keep C-compatible interfaces stable and testable outside SwiftUI. The bridge must return structured diagnostics with filenames and spans; do not have Ada cross Swift ownership or error boundaries without explicit lifetime tests. Consider whether each simulation is single-session/reentrant; if not, serialize work or isolate state safely. Simulations cannot call exit() on error or wedge the UI thread.

A future xcframework is a **packaging milestone**, not a substitute for proving the required GNAT/Ada runtime actually links on each Apple architecture. Distinguish device arm64, simulator arm64, macOS arm64 and optional macOS x86_64.

## 6. Milestones, dependencies and irreversible gates

One feature = one Spec Kit dossier, one acceptance checklist and evidence for its **actual** execution. IDs are stable: AP-00 through AP-14. Do not mark future gates passed merely because this plan mentions them.

| Gate | Build result / question | Exit evidence |
|---|---|---|
| AP-00 Governance and bootstrap | Add constitution, Spec Kit setup, ledger, validators, secret scan, reproducible setup instructions and CI skeleton | clean fresh-clone bootstrap; ledger unit tests |
| AP-01 Baseline and provenance | Pin upstream/fork revision; build reference GHDL; inventory actual source/build/license dependencies; test representative VHDL designs | host build, baseline manifest and golden results |
| AP-02 Legal and Apple policy | File-level license map, runtime/IEEE obligations, App Store distribution options, rights-holder list and counsel/owner decision questions | explicit report; UNKNOWN/BLOCKED until reviewed; no release permission implied |
| AP-03 Ada cross-toolchain spike | Prove freestanding minimal Ada/C runtime + exceptions/tasking/IO assumptions on macOS, iOS Simulator, iOS device toolchain; test symbol export and code signing | reproducible binaries and architecture matrix; device run waits for device phase |
| AP-04 Interpreter feasibility | Trace ghdl_simul and runtime, support matrix, host POC, delta-cycle and IEEE tests | demonstrable VHDL result + decision ADR |
| AP-05 Portable executor | Implement selected interpreter/VM/WASM path in small upstreamable changes; retain existing native backends | host semantic differential tests, no JIT/process proof |
| AP-06 Embedded compiler ABI | In-process analyze/elaborate/run/stop/results API, cancellation and memory lifetime | C harness tests, multi-session isolation and leak checks |
| AP-07 Apple build/package | Build/link reusable engine for macOS and iOS Simulator; device target compile; package XCFramework if supported | clean build, symbol validation, bundle/size/license manifests |
| AP-08 App skeleton | SwiftUI cross-platform UI, Documents/Files, project model, error recovery | unsigned iOS Simulator and macOS automated launch |
| AP-09 Editing and projects | Real multi-file project, syntax diagnostics, library resolution, local persistence | end-to-end workspace tests and accessibility |
| AP-10 Simulation UX | Run/stop/step, assertions, hierarchy, bounded resource use and cancellation | real engine-backed simulator UI tests |
| AP-11 Waveform UX | Real VCD generation, parser, timeline, export; optionally verified FST/GHW | timestamp and value comparison against reference |
| AP-12 Simulator lock | Fresh-clone host+Mac+simulator matrix, all launch workflows and regression suite; no mocks masquerading as runtime | immutable SimulatorLock commit and evidence manifest |
| AP-13 Device validation | Physical iPhone/iPad execution, performance, thermal/memory, crash recovery and local files | dated device-specific reports; not interchangeable with simulator results |
| AP-14 Release preparation | legal distribution permission, source notices, app privacy, signing, App Review guidance and metadata | owner decision only; no upload/submission without explicit approval |

Dependency rules: AP-00 -> AP-01 -> AP-03/AP-04 -> AP-05 -> AP-06 -> AP-07 -> AP-08/09/10/11 -> AP-12 -> AP-13 -> AP-14. AP-02 runs early, can proceed in parallel, but **blocks release and any binary redistribution** until resolved. If architecture legal risk makes further engineering unreasonable, record that explicitly for the owner.

AP-05 starts only after the interpreter comparison ADR. AP-12 forbids claiming iOS-ready from an x86/macOS-only result. AP-13 never runs before AP-12. AP-14 is not a background agent's authorization to publish.

## 7. Spec Kit: reproducible feature loop

**First run:** confirm installed Spec Kit version, available slash commands/skills, .specify directory, local Mac tools and repository state. If absent, establish Spec Kit using its actual installed documentation; do not assume the existing GHDL tree already has Spec Kit. AP-00 creates the constitution, validator, ledger and evidence recorder **before any command below is treated as available**.

The workflow observed in the reference repositories (verify names against the installation): constitution once, then for each feature **specify -> clarify -> plan -> checklist -> tasks -> analyze -> implement -> converge**. Invoke the actual installed skills; do not silently bypass a missing skill or impersonate one with hand-written artifacts. Re-run analyze/implement/converge after corrections as appropriate.

Create one feature directory for each immutable gate:

~~~
specs/001-ap-00-governance/
specs/002-ap-01-baseline/
specs/003-ap-02-licensing/
specs/004-ap-03-ada-cross-toolchain/
specs/005-ap-04-interpreter-feasibility/
specs/006-ap-05-portable-execution/
specs/007-ap-06-embedded-abi/
specs/008-ap-07-apple-build/
specs/009-ap-08-app-shell/
specs/010-ap-09-projects-editor/
specs/011-ap-10-simulation-ux/
specs/012-ap-11-waveform-ux/
specs/013-ap-12-simulator-lock/
specs/014-ap-13-devices/
specs/015-ap-14-release/
~~~

Dossier minimum: spec.md, research.md, plan.md, tasks.md, checklists/requirements.md, contracts as relevant, quickstart.md and evidence references. Use requirement IDs FR-APxx-###, success IDs SC-APxx-### and task IDs T-APxx-###, unique across all phases. All test claims link to exact command, commit and recorded environment. Tag tasks {owner}, {device}, {legal} or {external} when automation cannot close them.

**Starter briefs (feed as feature prompts, with platform/code details confirmed during research):**

- AP-00: "Establish auditable GHDL Apple autonomy: Spec Kit constitution, short runbook, safe ledger, evidence recorder, validator, secret scan, CI skeleton. No app or compiler rewrite."
- AP-01: "Reproduce and document upstream GHDL on host. Inventory front end, ghdl_simul, GRT, backends, libghdl, IEEE libraries, supported standards and source-level licenses. Create golden differential tests."
- AP-02: "Audit GHDL App Store licensing and Apple's current rules with citations and specific contributor permission requirements. Do not claim an exception exists."
- AP-03: "Prove GNAT/Ada and C target build/link feasibility for Apple platform slices through minimal code; verify exact Xcode/SDK/host versions and no code execution policy violation."
- AP-04: "Trace, build and exercise historical interpreted simulation on host, measure coverage and semantics, compare interpreter restoration versus new backend; produce a decision ADR."
- AP-05: "Create smallest portable no-JIT GHDL execution backend selected by ADR; keep native backends intact and verify against golden VHDL waveforms."
- AP-06: "Provide memory-safe C ABI and isolated engine lifecycle; support diagnostics, source/library management, simulation stop/step, signal inspection and VCD export."
- AP-07: "Create reproducible Apple build for verified targets and self-contained packaging; do not paper over missing Ada cross runtime or bundle licenses."
- AP-08: "Build a real offline SwiftUI shell, resource providers and test harness; no backend fixtures for production behavior."
- AP-09: "Make project import/edit/save/analyze work end-to-end with actual GHDL diagnostics, IEEE libraries and Documents security scopes."
- AP-10: "Make real testbench simulation controllable, time-bounded, cancelable and debuggable; include delta-cycle and event queue regression."
- AP-11: "Implement VCD file-backed waveform inspection with verified timestamps, value formats, hierarchy and export. Add FST/GHW only after proven need."
- AP-12: "Lock one tested revision, rerun full host and simulator matrix independently, publish immutable evidence and support matrix."
- AP-13: "Validate the locked build on physical Apple devices; do not change device OS versions or signing on behalf of the owner."
- AP-14: "Prepare a complete App Store compliance/release packet and open owner-only legal/signing/submission checklists; do not submit."

**Skill prompt convention:** Prefix every step with the gate ID, its starter brief, the applicable sections of this file, the actual GHDL source paths discovered, and the objective acceptance check. If clarifications are ordinary engineering choices, recommend the conservative default and record it in the dossier; material licensing, release, money or protected owner decisions stay pending.

**After AP-00, the intended local status interface** (these commands are planned contracts, NOT existing scripts at initial checkout):

~~~
python3 scripts/apple/autonomy.py status
python3 scripts/apple/autonomy.py next
python3 scripts/apple/autonomy.py show AP-04
python3 scripts/apple/autonomy.py run-check AP-04 --name host-differential -- <verified command>
python3 scripts/apple/autonomy.py seal AP-04
python3 scripts/apple/autonomy.py record AP-04
python3 scripts/apple/autonomy.py validate
~~~

AP-00 must implement unit-tested versions before the agent calls them. Each check stores exit status, time bound, commit, tool versions, log digest and artifact hashes; logs and private device IDs are gitignored. No manual edits to sealed evidence. Record FAIL, PASS, NOT_RUN, BLOCKED_OWNER_ACTION and BLOCKED_EXTERNAL separately. A failure never becomes PASS by disabling its test.

## 8. Simulation correctness contract

The exact reference standard is selected and documented (VHDL-2008 priority; acknowledge upstream partial coverage). Required differential corpus includes:
- std_logic_1164 nine-value and resolved/unresolved signals; numeric_std signed/unsigned arithmetic, conversions and overflow rules;
- combinational processes, sensitivity lists, sequential registers, multiple drivers, hierarchy, packages, generics, configurations and library lookup;
- inertial/transport delay, transactions and signal driver replacement;
- delta-cycle ordering, wait-on/for/until, clocks, testbench assert/report and process termination;
- constrained recursion/large arrays/records/protected types to the degree upstream supports; malformed-source diagnostics and exact spans;
- deterministic waveform timestamps, timescale, hierarchy, signal state and assertion output;
- cancellation, infinite-loop guard, out-of-memory/huge-waveform behavior, repeated simulation sessions and app suspension.

Create short unit and integration tests with deterministic seeds and representative cases. Store reference GHDL executable version, flags, VHDL sources and expected traces. Compare semantic timelines after normalizing harmless VCD formatting differences; do not assert byte-for-byte identity of unrelated headers. A simulator UI that cannot run the same corpus is incomplete.

## 9. Automated testing, time budgets and locks

Take the specific lessons of projectm-ios and VisualCore, without copying unrelated ANGLE, audio, StoreKit or game rules.

- Host tests first; macOS native compile/bridge tests next; iOS Simulator tests before any physical device campaign; device results only after SimulatorLock.
- Prefer small tests (individual unit seconds, focused suite near a minute, UI test under 30 s, any single CI gate under 10 minutes). Split longer test matrices; investigate hangs rather than retrying silently.
- Simulator tests use headless xcodebuild/xcrun simctl and current runtime discovery. No owner-interrupting simulator GUI/attach tools.
- Build once for testing; use test-without-building for selected chunks where the toolchain supports it. Avoid incompatible parallel builds on the same Mac/DerivedData.
- Use Python wrappers for command timeouts, structured logs, toolchain discovery and test selection; do not run 30-minute soak tests by default. Do not rely on shell pipes that hide xcodebuild progress or status.
- Parallel agents: maximum ten including subagents. One writer per file; a single lead owns git, shared source/ABI, Spec Kit skills, integration, evidence and commits. Independent research/test fixture work may happen in parallel in separate worktrees. No recursive/self-spawning agents.
- Commit meaningful feature/spec/test slices on **ios** and push ios after verified changes. Do not push master. No force-push, destructive reset, co-author trailers or sensitive identifiers in commits.
- Monitor CI without blocking other independent work. When CI finishes and fails, inspect logs, fix root cause and rerun relevant gates before advancing the dependent gate.
- Documentation is not evidence of compilation; an agent's assertion is not evidence; simulator screenshots are not physical-device performance measurements.
- SimulatorLock records commit SHA, complete source tree cleanliness, Xcode/SDK/Ada versions, passing result hashes, unsupported feature list, and device test plan. Replacing the locked code requires a new lock and rerun of impacted gates.
- A release lock additionally requires verified source obligations, notices, third-party package licenses and owner-only legal/distribution approval.

## 10. License, privacy and owner-only gates

**Do not distribute a GHDL-containing App Store binary until legal rights and App Store compatibility are confirmed.** GPLv2-or-later obligations, the GHDL runtime GRT, IEEE packages, GCC-related code, and each contributor's copyright position must be audited. The app UI's own source and the linking model may carry obligations. An exception or dual license cannot be assumed merely because Tristan Gingold is a major maintainer; obtain written permission from every required holder or document another qualified lawful solution. Engage competent legal counsel for distribution questions.

Apple's code-execution rules (including Guideline 2.5.2) require careful reading in light of **user-authored VHDL**. Do not claim that non-JIT bytecode or WASM guarantees App Review approval. All app-delivered executable behavior must be self-contained and reviewable as required.

Owner-only: Apple Developer membership, signing identities/certificates/profiles, device unlocking/trust/OS, legal agreements/exception requests, App Store Connect credentials, prices, app identity, payment setup, final upload and submission. Agents prepare exact steps and continue feasible engineering but do not act without explicit authorization. Never move or modify existing unrelated owner projects.

## 11. Fresh-clone setup and CI contract

AP-00 produces an idempotent setup document plus Python 3 script. It prints installed git, Python, Xcode, Swift, GNAT, LLVM, SDK and simulator runtime versions, verifies tool availability, fetches only pinned dependencies and caches downloads outside git. It never changes host security settings, installs credentials, silently upgrades a device, or deletes other projects. A fresh clone without manual tweaks must pass the documented host gates or report exactly which external tool is missing.

CI matrix begins with Linux/macOS GHDL regression, licensing/secret/metadata scanners and documentation validation. macOS Apple jobs are added only after reproducible host builds. Every job has a bounded timeout, caches by verified tool version and ref, records artifact hashes and separates expected toolchain unavailability from code failures. No fake green CI for unavailable SDK/device tasks.

**Proposed after bootstrap:**

~~~
python3 scripts/apple/setup.py --check
python3 scripts/apple/validate_docs.py
python3 scripts/apple/check_compliance.py
python3 scripts/apple/autonomy.py status
~~~

The agent creates these only in AP-00 and ensures --help / --check behave without external model calls. Prefer stdlib Python over fragile PowerShell/macOS shell pipelines.

## 12. Upstream and git protocol

- Work on owner fork **Coastrnaut/ghdl, branch ios**, based on verified ancestor master. Keep fork master unmodified.
- Preserve upstream source layout. Use narrow commits (for example, simulation runtime portability, interpreter semantics, C ABI, test fixes) with tests and changelog/patch rationale; avoid monolithic Apple-only rewrites.
- Track patches and upstream discussions in docs/upstream/ when the docs infrastructure exists; do not assert an upstream PR is accepted before it is merged.
- Upstream GHDL stays the behavioral baseline; do not rebase/force-push the working ios branch without direct owner instruction. Fetch upstream and reconcile changes carefully.
- Don't modify projectm-ios, VisualCore, ANGLE or their git submodules in this project. Their docs were studied for process lessons only.
- Scan staged changes before committing for keys, serials, UDIDs, certificates, team identifiers, provisioning data, user-specific paths and excessive binary artifacts.

## 13. Durable state, continuation and safe stopping

The agent must be restartable after context loss. On every new session:

1. Check current branch and HEAD (must be ios), git status, remote tracking, last commits and any work in progress. Never discard uncommitted work; reconcile diffs.
2. Read CLAUDE.md, this document sections 2/6/7/13, constitution once created, the currently active dossier and ledger (if created).
3. Verify toolchain and current preflight; never assume the developer machine or test runtime matches an old log.
4. Take the **first dependency-ready gate** from the ledger. Before AP-00, work only on establishing real bootstrap, not pretending ledger commands exist.
5. Invoke the installed Spec Kit skills in their verified order; generate research/spec/plan/tasks then implement and test. Fix blockers rather than skipping acceptance.
6. Reproduce evidence, mark only actually verified tasks, commit/push ios, review CI and continue to the next ready gate.
7. On technical infeasibility, record a blocker and fallback ADR. On owner-only/legal tasks, record exact request and continue independent work.
8. Stop only when no eligible engineering task remains, the required owner decision blocks all useful progress, the environment is irrecoverably unavailable, or further action would violate safety/legal/git rules. Do not run endlessly, promise background completion, or silently weaken a gate.

**Acceptance status vocabulary:** PLANNED, IMPLEMENTED, BUILT, HOST_VERIFIED, SIMULATOR_VERIFIED, DEVICE_VERIFIED, BLOCKED_OWNER_ACTION, BLOCKED_EXTERNAL, FAILED, NOT_RUN, READY_FOR_OWNER_REVIEW. These terms are *not* interchangeable.

### Copy/paste continuation prompt for Claude Code

~~~
You are the autonomous lead engineer for Coastrnaut/ghdl on the ios branch.
Mission: create one fully offline GHDL VHDL IDE/simulator for iOS, iPadOS and
macOS, with upstreamable modifications, real IEEE/VHDL simulation semantics,
no JIT or external compiler execution on iOS, and independently gated legal
App Store distribution.

Read CLAUDE.md and autocreator.md. Print branch, HEAD, working-tree state,
tool versions, last valid evidence and active gate. Read any current
.specify/memory/constitution.md, .autonomy ledger and active specs. On a fresh
fork without these directories, start AP-00 by verifying Spec Kit and
bootstrapping the ledger; never invoke scripts that do not yet exist.

Follow AP-00 through AP-14 in dependency order. For each, use installed
Spec Kit skills: specify, clarify, plan, checklist, tasks, analyze,
implement, converge (actual installed names take precedence). Use the
provided gate brief. Give each task a short acceptance test, re-run real
checks, record hashes, commit and push ios after each verified slice.
No invented proof, mock-only releases, or unsupported platform claims.

Keep research and safe engineering moving in parallel where useful, max ten
agents, one writer per file, one lead for git/builds/evidence. Keep tests
bounded and CI monitored without blocking unrelated work. Simulator-first
until SimulatorLock; physical devices later. Preserve existing GHDL
backends and make upstream-ready patches.

Legal rights, signing, owner device actions, app pricing and submission are
owner-only gates. Never distribute until approved. If every ready path is
blocked, report exact evidence and next owner action; do not ask the owner
questions already answered in the source or replace GHDL without an ADR.
~~~
