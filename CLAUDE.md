# GHDL Apple Port — CLAUDE.md

> **Agent bootstrap, automatically read by Claude Code.** This is the short operating contract for the `ios` development branch of [Coastrnaut/ghdl](https://github.com/Coastrnaut/ghdl). Read [autocreator.md](autocreator.md) for the detailed product architecture, phase prompts, gates, and continuation instructions. **The app and automation infrastructure described there are not implemented merely because these documents exist.**

## 1. Read in this order

1. `autocreator.md`, especially §§2–7, 9–10 and 13; it is the canonical forward-looking engineering book.
2. The *actual checkout* at `git status --short --branch`, `git log -5 --oneline`, `git remote -v`, and the existing GHDL `README.md`, `configure`, and relevant compiler source. Verify rather than presume.
3. `.specify/memory/constitution.md` **if it exists**. AP-00 must create it through an installed Spec Kit constitution workflow before it becomes authoritative.
4. The live `.autonomy` ledger and active `specs/<NNN>-...` dossier **once AP-00 creates them**. Never treat planned commands as existing tooling.
5. Any applicable accepted `docs/adr/` decisions and `docs/upstream/` patch notes **once created**.

**Precedence:** measured compiler/toolchain/Apple behavior and applicable legal requirements; current explicit owner directives; `autocreator.md` and this file; the constitution and accepted ADRs; feature dossiers; obsolete instructions. Resolve contradictions explicitly with evidence and a documented decision.

## 2. Boot and recovery checklist

At the beginning of each session:

```sh
git status --short --branch
git log --oneline -8
git remote -v
git diff --stat
git diff --cached --stat
python3 --version
```

- Work only in **`Coastrnaut/ghdl` on `ios`**. The branch started from fork `master` commit `8e642a9a5168bfe39bda65e23e5f7a6fdfec1b61` on 2026-11-14. Confirm the present HEAD every session; the starting SHA is *not* the live state.
- Do not reset, clean, rebase, force-push, overwrite, or discard a dirty worktree. Inspect and preserve interrupted work, then reconcile it with its intended task. If multiple writers are active, coordinate file ownership and use separate worktrees.
- Before AP-00 is completed, the `scripts/apple/` commands in `autocreator.md` are **requirements to implement**, not commands you may assume exist. Probe `--help` and validate paths first.
- After AP-00, use the real ledger's `status`, `next`, `show`, `validate`, and evidence commands as implemented. If missing, repair the bootstrap. A dated prose status is never proof of a passing gate.
- Print a compact checkpoint: branch/HEAD, dirty files, toolchain availability, active gate, last proved gate, pending owner actions, and immediate next verifiable task.
- Recover from a documented passing commit and real logs; never falsify evidence or erase previous failing test records to create a green result.

## 3. Product invariants (do not bend)

1. **GHDL is the compiler.** Reuse its real parser, semantic analyzer, elaboration, IEEE libraries, simulator runtime, and waveform support wherever proven possible. Do not substitute Tree-Sitter syntax checking, a generic RTL evaluator, screenshots, pre-recorded output or another engine for actual GHDL simulation.
2. **Fully offline on device.** No cloud build/simulation, remote execution, network-dependent activation, mandatory account, or dependence on user-installed Homebrew/compiler tools in the shipping Apple app.
3. **Preserve normal GHDL.** Keep upstream-supported host compiler and simulation backends working. Local Apple changes must be small, test-backed, easy to review and ideally acceptable upstream.
4. **Portable execution, not policy evasion.** iOS/iPadOS must not require generated native machine code execution, native JIT, writable-and-executable pages, child compilers/linkers, unsupported sandbox escapes or private entitlements. WASM is only a candidate if an **interpreter-only** deployment and current Apple rules are independently verified.
5. **Semantics above speed.** Preserve event scheduling, delta cycles, resolution, inertial/transport delays, processes, wait statements, IEEE `std_logic_1164`, `numeric_std`, assertions and deterministic waveforms. Never present subset-RTL behavior as full VHDL-2008.
6. **Standards claims must match tests.** GHDL's fork README describes partial 2008 and 2019 support; do not claim full 2008 until the actual revision and backend have passed comprehensive coverage. Maintain an explicit support matrix.
7. **Safe process lifecycle.** Long-running or malicious user projects need bounded resources, cancel/stop, reentrancy or serialization, crash recovery and memory cleanup. No VHDL execution on the UI thread.
8. **One coherent app.** Design SwiftUI shell(s) for iPhone, iPad and Mac around a stable C ABI and portable engine. Separate compiler, integration layer, editor, waveform UI and app services. iOS Simulator and native macOS are separate acceptance targets.
9. **No advertising, tracking or mandatory online services.** Do not introduce ad SDKs or monetization machinery. Price and final distribution model remain owner decisions.
10. **Real release gates.** No App Store binary distribution, rights exception assertion, signature upload, or submission until the owner approves a documented license-compliance and distribution plan. The code's existence is not legal clearance.

## 4. GHDL-specific architecture discipline

- **First prove a host reference baseline.** Record the exact GHDL fork and upstream SHAs, configure switches, GNAT/GCC/LLVM versions, source fixtures, output and test invocation.
- **Investigate `ghdl_simul` before inventing a VM.** The README explicitly calls this interpreter deprecated, slow and incomplete. Audit its implementation, feature limits and connection to GHDL's intermediate representation. A host execution proof and ADR choose whether to restore it.
- Then compare an independently specified GHDL bytecode VM, interpreted WebAssembly and a hybrid macOS/native plus iOS/portable design. Demonstrate feasibility with measured source-level experiments.
- **Ada portability is an explicit blocker, not a TODO to hand-wave.** Prove iOS-arm64 and iOS Simulator Ada runtime/linking, runtime initialization, exceptions, heap and filesystem needs. Cross-target object generation alone is insufficient. If GNAT iOS support fails, document alternatives without pretending an `.xcframework` exists.
- Distinguish `libghdl` analysis APIs from a complete runnable embedded simulator API. Do not declare `ghdl_initialize`, `run_until`, `get_signal`, etc. as existing functions without verifying source; document new API ownership, ABI and cleanup.
- Use GHDL's actual simulation and waveform code when possible. Compare normalized event traces and VCD values/times rather than fragile differences in comment headers. Make VCD support a release requirement; FST/GHW only after research.
- No dynamic backend loading, process creation or external link step on iOS. App sandbox filesystem access, document providers and permissions must be tested rather than inferred.
- Keep the GHDL core's existing source tree and build conventions; place Apple-specific wrappers under `apple/` only after an ADR validates the actual layout. Never copy the compiler source into the Swift app.
- Any performance improvement must preserve reference semantics and pass the same test corpus. Profile representative workloads before optimizing.

## 5. Autonomous Spec Kit execution

**Expected feature progression:** AP-00 governance/bootstrap, AP-01 host baseline, AP-02 licensing/policy research, AP-03 Ada/Apple toolchain proof, AP-04 existing interpreter feasibility, AP-05 portable execution, AP-06 embedded ABI, AP-07 Apple build/package, AP-08 app shell, AP-09 projects/editor, AP-10 simulation UI, AP-11 waveforms, AP-12 simulator lock, AP-13 physical device testing, AP-14 release preparation. The dependency graph and detailed prompts are in `autocreator.md` §6–7.

For each ready feature, once its prerequisites exist:

1. Find the canonical gate and feature directory in the ledger; record the tested HEAD.
2. Run the **installed** Spec Kit skills in their actual supported names/order: *specify, clarify, plan, checklist, tasks, analyze, implement, converge*; use constitution initially. If a skill is unavailable, inspect its installation and fix the environment rather than merely fabricating its output.
3. Feed each skill the gate's starter brief, source-verified findings, acceptance criteria and documented constraints. Answer routine engineering clarifications with the safest defensible default and record each decision. Leave rights, signing, price and release decisions owner-only.
4. Keep one feature's `spec.md`, `research.md`, `plan.md`, `tasks.md`, checklist, contracts and quickstart cross-consistent. Give requirements `FR-APxx-###`, success criteria `SC-APxx-###` and tasks `T-APxx-###` stable unique IDs.
5. Run analysis checks, apply all non-owner fixes, implement actual compiler/app code and its tests, repeat convergence until no new actionable issue is found.
6. Mark a task `[X]` only with observed acceptance evidence: command, environment, commit, date and log/artifact digest. A subagent's report or unexecuted unit test is not evidence.
7. Seal and record the gate with the **real** AP-00 ledger tool after all mandatory applicable checks pass; do not edit evidence JSON, hashes or a ledger manually.
8. Commit and push verified, scoped improvements to `origin ios`. Check CI status without waiting to do independent work. Advance only when dependencies are satisfied.

**Never mutate `master`.** Do not create upstream PRs, update tags, distribute binaries or merge branches merely because the loop reached a new stage; such actions need their own explicit plan and authorization.

## 6. Lessons retained from VisualCore and projectm-ios

These rules are transplanted from *reviewed, dated source documentation*, not assumptions about GHDL's present state:

### 6.1 One canonical steering book, short agent entrypoint

VisualCore's `Auto-Creator.md` is the design/roadmap and its `CLAUDE.md` is the compact bootstrap; live progress lives in checked ledgers, not stale paragraphs. Reproduce that separation here. Update `autocreator.md` **in place**, recording significant changes in ADRs. Do not append redundant giant prompts, contradictory status narratives or a new roadmap every session.

### 6.2 Commit often and preserve authorship style

ProjectM-iOS's memory export documents earlier user feedback to commit and push after each finished task, skill and cohesive tested change, not after many unrelated features. Here push **`ios`**, not VisualCore's or projectm-ios's `main`. Keep small readable commits with explicit tests; never force-push. Do not add `Co-Authored-By` or “Generated with Claude Code” trailers/PR text. Use LF endings and the configured git author. Scan diffs for signing data and other secrets before commits.

### 6.3 Delegation is bounded, and the lead verifies

Use independent read/review/test-design agents when genuinely useful, **maximum ten total**. Single writer per file and one integrator for shared spec, ABI, ledger, toolchain, builds and git. Subagents never declare a gate passed, commit/push shared work or start competing heavy simulator builds. Re-run their proposed commands before ticking tasks. No recursive agent orchestration, hidden model API calls, or unreviewed auto-generated PRs.

### 6.4 Work continues without CI becoming the critical path

After a push, check CI with bounded queries, investigate failing jobs promptly, but work on unrelated ready research/implementation while CI runs. Do not run an indefinite `gh run watch` or abandon unrelated work while a job queues. Do not advance a gate that depends on a failing result. Distinguish CI unavailable from code passing.

### 6.5 Tests must be concise, headless and diagnostic

Avoid the 15–30 minute suites and hung simulator UI workflows documented in projectm-ios's lessons. Default: individual host test seconds, package suite near one minute, UI test ideally below 30 seconds, each invocation **bounded to ten minutes**. Representative vectors in the normal gate; expansive standards sweeps on purpose with a separately documented matrix. When a test stalls, inspect logs/processes/test name, fix the cause and add a regression.

Use `xcodebuild`, `xcrun simctl`, and XCUITest without simulator-panel permissions prompts. Discover installed simulator runtimes and destinations on this machine, never copy UDIDs or OS versions from another repo's logs. For Xcode tests, build once and use `test-without-building` where practical. Monitor simulator runtime instability separately from app failures. Do not erase somebody else's simulator data without clear scope.

### 6.6 Simulator-first, with separate physical-device evidence

Implement and validate the actual GHDL execution path, bridge, app projects, editor and waveforms using host tests and iOS Simulator *before* installing or measuring the unified build on hardware. Create an immutable `SimulatorLock` baseline after an independent full rerun. Device testing only starts after that gate and owner-controlled provisioning. Device results from projectm-ios / VisualCore **never certify GHDL**. No untested compatibility, memory or performance promises.

### 6.7 Reproducibility and machine portability from day one

ProjectM-iOS's Mac migration demonstrated that Claude per-project memory and toolchain state do **not** travel with a git clone. Record decisions/lessons in versioned files, create an idempotent `scripts/apple/setup.py` and `apple/README.md`, and test a fresh clone. Prefer pinned, integrity-checked dependencies, Python scripts over fragile OS-specific scripting, cached outputs outside git, and generated rather than committed Xcode projects when justified.

### 6.8 Owner-only interventions are explicit blockers, not hidden workarounds

Do not create or alter signing profiles, Apple developer teams, App Store Connect entries, paid agreements, pricing, licensing grants, legal attestations, device OS versions or credentials. Preserve sensitive identifiers outside git. Prepare precise steps, flag `BLOCKED_OWNER_ACTION`, and continue other ready work. Never interpret other-repository standing approvals as permission to publish this GHDL app.

### 6.9 Native upstream separation, not an accidental fork

VisualCore kept its projectM reference pinned/read-only; projectm-ios kept upstream-ready engine patches separate and documented. Apply this *principle*, not the unrelated repository names: leave GHDL's existing native backends intact, isolate Apple abstraction and interpreter work, track each patch's test rationale, and propose small upstream PRs after owner review. **Do not modify VisualCore or projectm-ios** as part of this GHDL effort.

### 6.10 Honest evidence and statuses

Report `NOT_RUN`, `FAILED`, `HOST_VERIFIED`, `SIMULATOR_VERIFIED`, `DEVICE_VERIFIED`, `BLOCKED_EXTERNAL`, and `BLOCKED_OWNER_ACTION` precisely. An unsigned Simulator build is not an App Store distribution pass; an Ada object file is not a linked working iOS simulator; a signed binary is not an approved GPL exception. Never tick a task to keep a schedule.

## 7. Verification and CI expectations

- Baseline host GHDL builds and its existing regression tests remain the first check after compiler changes.
- Every changed interpreter/back-end operation needs reference differential fixtures: time/delta-event ordering, multiple driver resolution, IEEE libraries, delays, VHDL-2008 constructs when supported, assertions, and real VCD exports.
- Every new C ABI operation needs ownership/ABI, error propagation, repeated-session, thread/concurrency and bounded-cancellation tests. No process `exit` from a bridged library.
- Every app UI milestone needs an end-to-end test using **the real engine** (not fixture-only fake outputs). Smaller unit tests may use mocks, clearly identified.
- CI may add macOS/GNAT/Xcode lanes after toolchain proof; don't construct fake passing CI when a necessary SDK is unavailable.
- Keep source, binary, signing and dependency provenance. Review privacy manifest and linked frameworks **after building**, not just by searching Swift source.
- Evidence should record exact commit, command/exit, tool versions, duration, relevant result digest and a concise failure explanation; store private raw result bundles locally or under approved CI retention, not in git.

## 8. Licensing and App Store blockers

GHDL's GPLv2-or-later terms and source-specific licenses require a real audit. Do not assume Tristan Gingold can personally relicense every transitive component. Trace GRT, standard libraries, GCC/LLVM-linked code, the Apple app UI, source availability and relevant distribution terms. Explicitly review current App Store rules, including user-authored code execution and embedded interpreters; no WASM, static linking or bytecode decision alone constitutes legal approval.

AP-02 produces a rights-holder/component matrix and counsel/owner decision points. Engineering can continue on the research branch, but **redistribution and release are blocked** until a lawful plan is approved. Never remove license notices, conceal GPL obligations or attempt technical bypasses.

## 9. Practical operating boundaries

- This file authorizes planning and normal, reversible development on `ios`; it is not permission for owner-only or destructive actions.
- Preserve existing upstream CI/builds, copyright headers and unrelated files. Avoid rewrites outside the feature scope.
- Keep host-only generated executables, Apple DerivedData, xcframeworks, VCD test outputs, private device data, logs, provisioning profiles and user-specific paths out of commits.
- Tooling and commands may differ between Mac environments; verify installed Spec Kit, GNAT, Xcode, SDKs, Swift and Python. Do not hard-code Xcode 27 or a presumed simulator runtime.
- Commit completed work promptly; if CI or a tool is unavailable, record what actually ran and what remains blocked.
- **Do not stop at a feature boundary** when safe, authorized, dependency-ready work remains. Do stop/report if every remaining task requires owner/legal action, the environment cannot be safely repaired, or continuing would violate a hard restriction. No uncontrolled infinite loop or unattended release actions.

## 10. Continuation entrypoint

Start a fresh Claude Code session in the fork on `ios`, then use the **copy/paste continuation prompt at the very end of [autocreator.md](autocreator.md)**. It is canonical; do not duplicate or silently drift a second variant in this file.

If AP-00 has not yet run, its first task is **bootstrap and prove the governance/testing infrastructure**, not to claim the 15 phases are already automated. After bootstrapping, the real ledger chooses the next gate. Do not ask the owner for decisions the source and routine engineering judgment already resolve; do surface decisions requiring licensing, signing, financial or release authority.
