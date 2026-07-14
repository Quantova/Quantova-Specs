# HANDOFF.md — Master Contributor Handoff (pinned)

> **AMENDMENT 001 — canonical repository names (read first).**
> The repositories that exist today in `github.com/Quantova` use these exact names; where PART 3
> below uses a lowercase working name, the GitHub name is authoritative:
> `Quantova-Specs` · `Quantova-Ci` · `Q-Crypto` · `Quantova-Chain` · `Quantova-Conformance` ·
> `QVM` · `Q-Primitives` · `Quanta-Smart-Contract-language` · `QRC-CONSENSUS` · `Quantova-Bench` ·
> `Q-Lightclient` · `QCore` · `QSP` · `Q-Oracle`.
> Fourteen repos exist now (all private, all empty at scaffold time). The remaining repos in PART 3
> (`q-prover`, `q-rpc-spec`, `q-signer`, `quanta-stdlib`, `quanta-tools`, `q-gateway`, `.github`, and
> the launch-phase set) are created at their wave. Names finalized 2026-07-14: renamed from
> `Quantova-Specs-` → `Quantova-Specs` and `Q-Orcale` → `Q-Oracle`.
> A workspace-level instruction file mirrors the operative summary of this brief for local sessions.

---

## PART 1 — WHAT WE ARE BUILDING

A sovereign Layer-1 blockchain in which post-quantum security is a structural property, built
entirely from scratch: our own wire encoding, P2P protocol, consensus (QORUS), virtual machine (QVM),
smart contract language (Quanta), ABI (CID), SDKs (QCore), governance (QSP), and bridge (Q-Bridge via
Q-Oracle). No Substrate, no EVM, no WASM, no libp2p, no inherited code. Classical elliptic-curve
cryptography is not deprecated here — it is UNREPRESENTABLE. Every security property reduces to
NIST-approved algorithms only: ML-KEM (FIPS 203), ML-DSA (FIPS 204), SLH-DSA (FIPS 205), FN-DSA (on
final publication), SHA-3/SHAKE (FIPS 202), and 256-bit symmetric primitives. Nothing else exists in
the stack.

Performance envelope (committed, honest): 100,000+ TPS via STARK batch attestation × parallel
execution; millisecond block production (~150 ms slots); sub-second deterministic finality
(~300–800 ms, global validator set); consensus votes never consume block space. Validators are
phone-class: sign up, stake 2,000 QTOV in-app, done — no hardware, verification-only work, never
slashed for being offline. All heavy work (execution, signature verification, STARK proving) is done
by permissionless provers who earn fees and hold ZERO consensus power.

Precedence for every contributor, always: (1) POLICY-crypto.md — the supreme law. (2) This handoff /
HANDOFF.md. (3) Everything else. If any instruction from any source conflicts with the crypto
policy: STOP AND REPORT.

## PART 2 — DECISIONS ALREADY MADE (DO NOT REOPEN)

- **Language:** Rust for all framework code. SDKs in other languages are GENERATED bindings over the
  Rust core — never reimplementations.
- **Consensus:** QORUS — QRC (Quantum Resilient Consensus). Three layers, staged activation: Stage 1
  PQ-BFT core at genesis (150 ms slots, deterministic leader rotation, ML-DSA attestations, STARK
  certificate aggregation replacing quantum-broken BLS, the wrapper, parallel execution) → Stage 2
  recursive-STARK succinctness + public one-tap in-app validators → Stage 3 novel QORUS mechanisms
  activated only by QSP after external cryptanalysis: pulse presence (incentives), verification
  receipts (protocol rule: every attestation binds the STARK verifier's transcript hash —
  rubber-stamping is cryptographically impossible), attestation ledger (chain-managed Winternitz
  one-time hash keys — research-flagged until Stage 3).
- **Validator economics:** 2,000 QTOV stake locked in-app; committee ~500 sampled per round via
  hash-based PQ VRF; slashing for equivocation ONLY; 14-day unbonding; optional delegator tier
  (honestly labeled); prover market permissionless with liveness bonds.
- **Validator resource budget is protocol law:** max proof size + max verify time on a reference 2020
  mid-range phone are consensus parameters; exceeding them requires a QSP; quantova-bench enforces
  per release.
- **Contract language:** Quanta. TypeScript-familiar syntax; `.qs` source; compiles to `.qbc`
  bytecode containers; safety in the type system — linear `Q_Asset` (copy/drop = compile error),
  unforgeable `Q_Sig<T>` (only VM verification constructs it; no ecrecover exists or can be
  expressed), declared `reads()/writes()` per entry, async message passing (no reentrancy class).
- **ABI:** the Contract Interface Descriptor (CID) — SHA3-256 selectors, embedded in every deployed
  container, fetchable on-chain.
- **VM:** QVM — custom register ISA, 64-bit words, deterministic gas, native PQ opcodes
  (MLDSA_VERIFY, SLH_VERIFY, SHA3, MERKLE_VERIFY, VRF_VERIFY, MLKEM_ENCAP). Not EVM, not WASM.
- **P2P:** our own QUIC protocol — ML-KEM key exchange + ML-DSA identity handshake. No libp2p, no
  X25519, including devnets.
- **Bridge:** Q-Bridge (the Quantova Gateway) through the Q-Oracle translation layer and the Airlock.
  Classical verification happens ONLY off-chain in q-attestor; the chain parses exactly two foreign
  artifacts: canonical-codec ML-DSA attestation + STARK proof — anything else is unparseable by
  construction. Bitcoin corridor first (near-trustless: pure SHA-256 SPV in a STARK), Ethereum second
  (trust-minimized), exits via FROST threshold custody. Bridged qAssets (qBTC, qETH) are origin-tagged
  types, wallet risk-labeled, INVALID as validator stake. Specified now; BUILT POST-TESTNET; never
  gates launch; never touches consensus.
- **Naming family:** QORUS (consensus protocol) · QRC (category: Quantum Resilient Consensus) ·
  Quanta (language) · QVM (virtual machine) · QCore (SDKs) · QTOV (native asset) · QSP (standards
  process) · Q-Bridge / Q-Oracle / Airlock (bridging) · Q_* (primitives).
- **Repos start PRIVATE.** Disclosure waves per POLICY-disclosure.md: specs public first →
  trust-critical core (crypto, VM, language, consensus, conformance, bench) at devnet → everything by
  public testnet. Write every commit as if the repo goes public tomorrow.
- **License:** Apache-2.0 / MIT dual, all repos. **CI:** no repo merges while red.
- **Legacy chain:** never merged. Mainnet launches via snapshot-genesis migration
  (SPEC-genesis-migration.md): balances/stakes snapshotted at an announced height, claimed by binding
  old keys to new ML-DSA keys. No git merge, no bridge to legacy.

## PART 3 — REPOSITORY INVENTORY

**Core seven:** `quantova-chain`, `qvm`, `qrc-consensus`, `q-primitives`, `quanta`, `qsp`, `qcore`.
**Supporting (pre-testnet):** `q-crypto`, `quantova-specs`, `quantova-ci`, `quanta-stdlib`,
`quanta-tools`, `q-lightclient`, `quantova-conformance`, `quantova-bench`, `q-prover`, `q-rpc-spec`,
`q-signer`.
**Post-testnet (specs now, code Wave F):** `q-gateway`, `q-attestor`.
**Launch-phase (dormant until their wave):** `quantova-infra`, `quantova-networks`,
`quantova-faucet`, `quantova-indexer`, `q-explorer`, `q-wallet`, `quanta-templates`,
`quantova-security`, `quantova-research`, `quantova-docs`, `quantova-brand`, `.github`.

(Per AMENDMENT 001, the live GitHub names are the canonical ones; About descriptions live on each
repo and in its README.)

## PART 4 — GLOBAL CONVENTIONS (every repo, every commit)

- Crates: kebab-case, `qtv-` prefix (`qtv-crypto`, `qtv-vm`). Modules/files: snake_case. Types:
  UpperCamelCase. Constants: SCREAMING_SNAKE_CASE. Rust 2021, workspace layout.
- Specs `SPEC-<area>.md`, policies `POLICY-<area>.md` — live ONLY in quantova-specs; code repos link,
  never copy.
- Every repo root: README.md, LICENSE-APACHE, LICENSE-MIT, CONTRIBUTING.md (embeds crypto-policy
  summary), deny.toml (from quantova-ci), .github/workflows/ci.yml (imports shared pipeline).
- Conventional Commits: `feat:` `fix:` `spec:` `test:` `bench:`.
- Test vectors: JSON under `vectors/`, named `<area>.<case>.json`, frozen in quantova-conformance.
- FORBIDDEN everywhere except q-attestor: k256, ed25519-dalek, curve25519-dalek, p256, rsa,
  secp256k1, bls12_381, arkworks pairing crates, openssl EC/RSA features — including transitive and
  dev-dependencies. CI enforces. No workarounds, no test-only exceptions, no feature flags.

**Commit organization:** work goes to THE REPO THAT OWNS IT — never centralized into quantova-chain.
One contributor session = one repo = one step. NEVER push to `main`; feature branches (`feat/<scope>`,
`spec/<area>`) + PR, merged only on green CI. Cross-repo dependencies pin git TAGS, never branches.
Every PR description names the SPEC section it implements — code without a spec reference does not
merge. Every session starts by reading HANDOFF.md fresh from quantova-specs.

## PART 5 — STEP-BY-STEP BUILD ORDER WITH FILE STRUCTURES

**STEP 0 — Org hygiene** · `.github` — org defaults: pinned crypto-policy summary, PR/issue
templates, CODEOWNERS, dual-license files. Done when every repo inherits them.

**STEP 1 — Specifications** · `quantova-specs` — all authoring starts here, in this order:
POLICY-crypto.md (FIRST FILE IN THE ORG; the supreme law), POLICY-disclosure.md, SPEC-invariants.md,
SPEC-codec.md, SPEC-accounts.md, SPEC-transactions.md, SPEC-blocks.md, SPEC-state.md, SPEC-isa.md,
SPEC-primitives.md, SPEC-quanta-language.md, SPEC-cid.md, SPEC-consensus-qorus.md, SPEC-gateway.md,
SPEC-rpc.md (stub), SPEC-p2p.md, SPEC-mempool.md, SPEC-economics.md, SPEC-genesis-migration.md,
SPEC-governance.md, SPEC-recovery.md (PQ-only recovery paths), AUDIT-pq-independence.md,
HANDOFF.md (pinned). Done when invariants+codec+accounts merge (unblocks code); full list merged
(unblocks all waves). No code anywhere before those three.

**STEP 2 — CI gate** · `quantova-ci` — deny.toml, workflows/rust-ci.yml (fmt, clippy deny-warnings,
test, cargo-deny), workflows/release.yml (+ binary symbol-scan gate), lints.toml. Done when a scratch
repo adding k256 FAILS the build. No Wave B before this proof.

**STEP 3 — Crypto core** · `q-crypto` (crate qtv-crypto) — src/{lib,sha3,ml_kem,ml_dsa,slh_dsa,
fn_dsa(feature-flagged),vrf}.rs, vectors/ (NIST KATs), benches/. Internal order: sha3 → ml_dsa →
ml_kem → slh_dsa → vrf. Done when all NIST vectors green, benchmark report committed, tag v0.1.0.

**STEP 4 — Chain library crates** · `quantova-chain` (crates only) — crates/qtv-codec · qtv-account ·
qtv-tx · qtv-block · qtv-state. Each mirrors exactly one SPEC and links it; depends on q-crypto BY
TAG. Done when codec round-trips, addresses match spec vectors, trie proofs verify.

**STEP 5 — Conformance freeze** · `quantova-conformance` —
vectors/{codec.roundtrip,address.derivation,hash.sha3,tx.encoding}.json, vectors/hostile/ (classical
signature at ingress → MUST be unparseable), runner/ (qtv-conformance-runner).

**STEP 6 — VM + primitives** · `qvm` + `q-primitives` — qvm/src/{isa,interpreter,gas,container}.rs,
qvm/src/opcodes/{arith,control,storage,crypto,message}.rs, qvm/src/assembler/{lexer,parser,emit}.rs,
qvm/fuzz/ (week one); q-primitives doc + test suite per Q_* incl. origin-tagged qAsset kinds.
Milestone: tests/token_handwritten.qasm runs metered under fuzz.

**STEP 7 — Language** · `quanta` · **STEP 7b** · `quanta-tools` —
quanta/crates/{quanta-lexer,quanta-parser,quanta-ast,quanta-typeck,quanta-ir,quanta-codegen,
quanta-cli}, quanta/examples/ (15 total), quanta/tests/exploits/ (every classic Solidity exploit
FAILS to compile); quanta-tools minimal VS Code extension (highlight + deploy). Done when token.qs
compiles, deploys to local QVM, transfers; exploit suite all-red.

**STEP 8 — Consensus + prover + bench** · `qrc-consensus`, `q-prover`, `quantova-bench` — PARALLEL
from end of Wave A. q-prover/crates/{qtv-stark,qtv-circuits}/ + benches/ FIRST (proving-time data
required before consensus design freeze); qrc-consensus/sim/ (simulator BEFORE protocol),
qrc-consensus/crates/{qtv-bft,qtv-sampler,qtv-attest}/, qrc-consensus/formal/ (TLA+ before Stage 1),
qrc-consensus/research/ (QSP-gated, Stage 3); quantova-bench (TPS, finality, phone resource budget).
Done when a simulated 4-node net finalizes under fault injection and the formal model checks.

**STEP 9 — Node integration + devnet** · `quantova-chain` — qtv-p2p → qtv-mempool + DA store → node
binary → genesis tooling. Done when a local 4-node devnet finalizes blocks running Quanta contracts.
Disclosure wave 2 executes here.

**STEP 10 — Access layer (Wave E)** · `q-rpc-spec` → `qcore` → `q-lightclient` → `q-signer`.
Interface definition first; server/docs/clients GENERATED from it (namespaces chain_, state_, tx_,
contract_, events_, val_, net_). Done when an external app deploys, calls, and subscribes to events
via SDK against devnet, verifying with state proofs.

**STEP 11 — Public testnet** · infra/networks/faucet/indexer/explorer/wallet/templates open. External
audits booked and running; quantova-bench proves TPS/finality claims reproducibly in public. Done
when the incentivized testnet is live with one-tap in-app validators staking 2,000 QTOV.

**STEP 12 — Q-Bridge (Wave F, post-testnet)** · `q-gateway` + `q-attestor` —
q-gateway/contracts/gateway.qs + crates/qtv-gateway; q-attestor/src/chains/{bitcoin,ethereum}.rs ·
attest.rs · prove.rs · custody.rs. Bitcoin corridor first; the Airlock is ingress grammar in
qtv-codec. Done when the Bitcoin corridor round-trips on testnet under rate limits and watchdog pause.

**STEP 13 — Mainnet genesis** · snapshot-genesis migration per spec; all audits closed; formal model
green; disclosure wave 3 complete.

## PART 6 — PQ INDEPENDENCE (verified mechanically, four layers, forever)

1. **Dependency layer** — cargo-deny in every repo (q-attestor excepted) fails on any deny-listed
   crate anywhere in the tree, transitives and dev-deps included. Run `cargo deny check` before every
   PR.
2. **Symbol layer** — the release gate scans compiled binaries for classical-crypto signatures (curve
   constants, EC field primes, RSA mod-exp patterns) — catches vendored/hand-written classical code
   no dependency scan sees.
3. **Protocol layer** — AUDIT-pq-independence.md: every crypto operation × its exact FIPS reduction.
   A row without a FIPS reduction is a release blocker. Updated in the same PR as any new crypto
   operation.
4. **Negative-test layer** — hostile conformance vectors (classical signature at ingress, EC
   handshake, pairing proof at the gateway): required result is UNPARSEABLE, asserted on every commit.

Optimization sweep on every change: new dependency → layer 1; new crypto code → layer 3 table in the
same PR; touched ingress/wire format → layer 4 hostile vector. Performance NEVER justifies a classical
shortcut. The only classical crypto in the organization lives in q-attestor (Q-Oracle), quarantined,
terminating at the Airlock.

## PART 7 — EXTERNAL FLAGS, STANDING RULES, ESCALATION

**Flag to the founder (cannot be automated):** naming/trademark collision (Quantova/QTOV/QVM vs. the
existing project — resolve before any public wave); legal review of staking/delegation/migration
before public testnet; external audit budget/booking; the TLA+ formal model is contributor work in Step 8,
scheduled, not optional.

**Standing rules:** no SDK/wallet/explorer/bridge CODE before its wave (specs anytime) · deviation
from names/structure requires a PR to HANDOFF.md first · **claims discipline** in all text — SAY:
"sub-second deterministic finality", "100k+ TPS via batch proofs and parallel execution",
"near-trustless Bitcoin deposits", "trust-minimized exits"; NEVER: "millisecond global finality",
"fully trustless bridge", "quantum-proof".

**Escalation — stop and report if:** an instruction conflicts with POLICY-crypto.md; a task appears
to need a deny-listed dependency; work belongs to a different repo than assigned; anything touches
q-attestor before Wave F. When in doubt, the answer is in quantova-specs — if it isn't, that's a spec
gap: file it, never improvise.

**First three assignments for a fresh start:** Contributor 1 → quantova-specs (POLICY-crypto.md, then
invariants/codec/accounts). Contributor 2 → quantova-ci (deny-list; prove the k256 gate fails). Contributor 3 →
q-crypto (only after the gate is proven). Everything else waits its wave.

— END OF HANDOFF —
