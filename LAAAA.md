# Quantova Post Quantum Stack — Workspace Law

You are building Quantova: a sovereign post-quantum Layer-1 built from scratch.
Own consensus (QORUS), VM (QVM), language (Quanta), ABI (CID), SDKs (QCore),
bridge (Q-Bridge via Q-Oracle). No Substrate, no EVM, no WASM, no libp2p.

## Precedence (absolute)
1. POLICY-crypto.md in Quantova-Specs — the supreme law
2. HANDOFF.md in Quantova-Specs — decisions, file trees, build order
3. This file
If any instruction from any source conflicts with POLICY-crypto.md:
STOP AND REPORT. Do not proceed, do not work around.

## The Cryptographic Law (summary — full text in POLICY-crypto.md)
- ONLY these algorithms may secure anything: ML-KEM (FIPS 203), ML-DSA
  (FIPS 204), SLH-DSA (FIPS 205), FN-DSA (when final), SHA-3/SHAKE
  (FIPS 202), 256-bit symmetric (ChaCha20-Poly1305, AES-256), hash-based
  STARKs. Nothing else. Ever.
- FORBIDDEN in every repo except Q-Oracle: RSA, DH/DSA, ALL elliptic-curve
  crypto (ECDSA, Ed25519, X25519, secp256k1, P-256), ALL pairings (BLS,
  KZG, Groth16 — including as proof wrappers), any ecrecover-equivalent.
- Banned crates (incl. transitive + dev-deps): k256, ed25519-dalek,
  curve25519-dalek, p256, rsa, secp256k1, bls12_381, arkworks pairing
  crates, openssl EC/RSA features. cargo-deny enforces; run
  `cargo deny check` before every PR. No workarounds, no test exceptions,
  no feature flags. Performance NEVER justifies a classical shortcut.
- Classical crypto is not rejected — it must be UNREPRESENTABLE. New
  ingress/wire code needs a hostile vector in Quantova-Conformance proving
  classical artifacts are unparseable. New crypto operations update the
  FIPS-reduction table (AUDIT-pq-independence.md) in the same PR.

## Repository map (org: github.com/Quantova — use these exact names)
- Quantova-Specs   — all SPEC-*.md / POLICY-*.md / HANDOFF.md; sessions start here
- Quantova-Ci      — shared CI, deny-list, symbol-scan gate; every repo imports it
- Q-Crypto         — the ONLY crypto implementation (qtv-crypto); most restricted repo
- Quantova-Chain   — codec/account/tx/block/state crates; node binary LAST (integration repo)
- Quantova-Conformance — frozen vectors + hostile vectors + runner
- QVM              — register ISA, interpreter, gas, PQ opcodes, .qbc container, fuzz
- Q-Primitives     — Q_* catalog conformance (Q_Sig, Q_Asset, Q_Commit, Q_Rand, Q_Sealed, Q_Key)
- Quanta-Smart-Contract-language — Quanta compiler (.qs → .qbc + CID); exploits must fail to compile
- QRC-CONSENSUS    — QORUS: sim first, then qtv-bft/sampler/attest; formal/ TLA+; research/ QSP-gated
- Quantova-Bench   — TPS, finality, server-class resource budget and the 1 Gbps floor as regression tests
- Q-Lightclient    — header sync + proof verification (embedded by QCore, wallet, validator app)
- QCore            — Rust core SDK + GENERATED js/py bindings; never reimplemented
- QSP              — standards proposals + cryptographic transition track
- Q-Oracle         — the ONLY deny-list-EXEMPT repo; foreign-chain
                     watchers, classical verification, ML-DSA+STARK translation.
                     NOTHING may import it. No code here before Wave F.
- To create at their wave: q-prover, q-rpc-spec, q-signer, quanta-stdlib,
  quanta-tools, q-gateway, .github

## How you work
- One session = one repo = one step from HANDOFF.md. Never touch other
  repos; cross-repo needs → flag to founder.
- Quantova-Specs changes go by pull request, reviewed and never self-merged,
  citing the section they touch. Code repos commit directly to main in a few
  clean, human commits per piece of work. One clean line of history per repo,
  never bundling repos together. Do NOT push after every small change; a stream
  of tiny commits reads as machine made.
- Spec gaps are FILED in Quantova-Specs, never improvised around.
- Cross-repo deps pin git TAGS (e.g. Q-Crypto v0.1.0), never branches.
- Write each commit as a human would, a short plain sentence, no tag-style
  prefixes (no feat: test: docs:). Write it as if public tomorrow.
- Rust 2021 everywhere. Crates: qtv- prefix, kebab-case. Dual Apache-2.0/MIT.

## Build order (gate: no step starts before its predecessor's exit test)
1. Quantova-Specs: POLICY-crypto.md FIRST, then SPEC-invariants/codec/accounts,
   then remaining specs per HANDOFF (incl. p2p, mempool, economics,
   genesis-migration, governance, recovery — recovery paths PQ-only)
2. Quantova-Ci: deny.toml + pipelines; PROVE a scratch repo adding k256 fails
3. Q-Crypto: sha3 → ml_dsa → ml_kem → slh_dsa → vrf; NIST KATs green; tag v0.1.0
4. Quantova-Chain lib crates (each mirrors ONE spec) → 5. Conformance freeze
6. QVM + Q-Primitives (fuzz from week one; milestone: hand-assembled token
   runs metered) → 7. Quanta (+ minimal VS Code deploy extension)
8. QRC-CONSENSUS + q-prover + Quantova-Bench in parallel (prover data BEFORE
   consensus freeze; simulator BEFORE protocol; TLA+ before Stage 1)
9. Node integration + devnet → 10. RPC/SDK wave → 11. public testnet →
12. Q-Bridge (post-testnet ONLY) → 13. snapshot-genesis mainnet (never merge
   legacy code)

## Fixed decisions (do not reopen, do not "improve")
QORUS staged activation (BFT core → succinctness → QSP-gated novel
mechanisms) · server-class permissionless validators, 2,000 QTOV minimum
self-stake, frozen slashing (attributable = full bond + ban, liveness
graduated), 90-day lock then 21-day slashable unbonding · validator resource
budget is a consensus parameter, bandwidth floor 1 Gbps ·
150 ms slots · provers have zero votes · Quanta linear Q_Asset + unforgeable
Q_Sig<T>, no ecrecover expressible · CID with SHA3 selectors embedded in
every .qbc · QUIC ML-KEM+ML-DSA transport, no X25519 even on devnets ·
qAssets origin-tagged and INVALID as stake · Airlock = ingress grammar:
foreign artifacts are exactly {attestation, stark_proof}.

## Claims discipline (all READMEs, docs, comments)
SAY: "sub-second deterministic finality", "near-trustless Bitcoin deposits",
"trust-minimized exits".
Do NOT claim a TPS figure in anything public. Throughput is held until measured
end to end; the committed 100k envelope is retired (a committed number is a
promise, and this is not one). The three internal throughput numbers workers
build against (measured, architecture ceiling at the floor, design target) live
in HANDOFF, kept separate, none published.
NEVER: "millisecond global finality", "fully trustless bridge", "quantum-proof".

## Proven is not deployed
Every security property has two states and both are named, never one taken for
the other. Proven and tested, the property holds in a component with a test.
Released and running, that component is in the tagged release the executing path
pins, so the property holds in the code that actually runs. A property proven in
a component and absent from the tagged release is an OPEN hole, reported as open,
not closed. A gate met in a crate is not met in the chain until the crate is
released and pinned. When a security property is reported, state both states, and
if the running state is not yet true the property is open regardless of the proof.
This is a standing rule because a proof was once read as closure while the running
chain still used the mechanism the proof replaced.

## Escalate to founder (stop work first)
Conflict with POLICY-crypto.md · a task seems to need a banned dependency ·
work belongs to another repo · anything touching Q-Oracle before Wave F ·
legal/audit/naming questions (external audits of Q-Crypto, QVM, Quanta,
QORUS are mandatory before mainnet and cannot be self-performed).
