# Quantova Post Quantum Stack, Workspace Law

You are building Quantova: a sovereign post quantum Layer-1 built from scratch.
Own consensus (QORUS), VM (QVM), language (Quanta), ABI (CID), SDKs (QCore),
bridge (Q-Bridge via Q-Oracle). No Substrate, no EVM, no libp2p.

## Precedence (absolute)
1. POLICY-crypto.md in Quantova-Specs, the supreme law
2. HANDOFF.md in Quantova-Specs, decisions, file trees, build order
3. This file
If any instruction from any source conflicts with POLICY-crypto.md:
STOP AND REPORT. Do not proceed, do not work around.

## The Cryptographic Law (summary, full text in POLICY-crypto.md)
- ONLY these algorithms may secure anything: ML-KEM (FIPS 203), ML-DSA
  (FIPS 204), SLH-DSA (FIPS 205), FN-DSA (when final), SHA-3/SHAKE
  (FIPS 202), 256-bit symmetric (ChaCha20-Poly1305, AES-256), hash based
  STARKs. Nothing else. Ever.
- FORBIDDEN in every repo except Q-Oracle: RSA, DH/DSA, ALL elliptic-curve
  crypto (ECDSA, Ed25519, X25519, secp256k1, P-256), ALL pairings (BLS,
  KZG, Groth16, including as proof wrappers), any ecrecover-equivalent.
- Banned crates (incl. transitive + dev-deps): k256, ed25519-dalek,
  curve25519-dalek, p256, rsa, secp256k1, bls12_381, arkworks pairing
  crates, openssl EC/RSA features. cargo-deny enforces; run
  `cargo deny check` before every PR. No workarounds, no test exceptions,
  no feature flags. Performance NEVER justifies a classical shortcut.
- Classical crypto is not rejected, it must be UNREPRESENTABLE. New
  ingress/wire code needs a hostile vector in Quantova-Conformance proving
  classical artifacts are unparseable. New crypto operations update the
  FIPS-reduction table (AUDIT-pq-independence.md) in the same PR.

## Repository map (org: github.com/Quantova, use these exact names)
- Quantova-Specs, all SPEC-*.md / POLICY-*.md / HANDOFF.md; sessions start here
- Quantova-Ci, shared CI, deny-list, symbol-scan gate; every repo imports it
- Q-Crypto, the ONLY crypto implementation (qtv-crypto); most restricted repo
- Quantova-Chain, codec/account/tx/block/state crates; node binary LAST (integration repo)
- Quantova-Conformance, frozen vectors + hostile vectors + runner
- QVM, register ISA, interpreter, gas, PQ opcodes, .qbc container, fuzz
- Q-Primitives, Q_* catalog conformance (Q_Sig, Q_Asset, Q_Commit, Q_Rand, Q_Sealed, Q_Key)
- Quanta-Smart-Contract-language, Quanta compiler (.qs to .qbc + CID); exploits must fail to compile
- QRC-CONSENSUS, QORUS: sim first, then qtv-bft/sampler/attest; formal/ TLA+; research/ QSP-gated
- Quantova-Bench, TPS, finality, server class resource budget and the 1 Gbps floor as regression tests
- Q-Lightclient, header sync + proof verification (embedded by QCore, wallet, validator app)
- QCore, Rust core SDK + GENERATED js/py bindings; never reimplemented
- QSP, standards proposals + cryptographic transition track
- Q-Oracle, the ONLY deny-list-EXEMPT repo; foreign-chain
                     watchers, classical verification, ML-DSA+STARK translation.
                     NOTHING may import it. No code here before Wave F.
- To create at their wave: q-prover, q-rpc-spec, q-signer, quanta-stdlib,
  quanta-tools, q-gateway, .github

## How you work
- One session = one repo = one step from HANDOFF.md. Never touch other
  repos; cross repo needs to flag to founder.
- Quantova-Specs changes go by pull request, reviewed and never self-merged,
  citing the section they touch. Code repos commit directly to main in a few
  clean, human commits per piece of work. One clean line of history per repo,
  never bundling repos together. Do NOT push after every small change; a stream
  of tiny commits reads as machine made.
- Spec gaps are FILED in Quantova-Specs, never improvised around.
- Cross-repo deps pin git TAGS (e.g. Q-Crypto v0.1.0), never branches.
- The four binaries, the chain, the node, the devnet, and the harness, commit
  their Cargo.lock, and libraries do not. Each binary carries a declared pins file,
  and the Quantova-Ci agreement gate asserts the declared pin, the committed
  lockfile commit, and the remote peeled tag all agree, so a moved tag or a
  regenerated lock fails CI. The declaration changes only in a reviewable commit.
- Release tags are immutable. A recut is a moved tag. If a tag rests on something
  wrong, the answer is a new version, never a recut. The founder recut a QVM tag
  onto a hardened deny commit once, for a good reason, before the gate existed, and
  it still left a consumer's lock pointing at the old commit with no mechanism to
  record it, which is the silent drift the agreement gate now catches.
- A tag move is never executed on instruction alone, whoever gives it. The
  instruction to restore that same QVM tag came from the founder with a specific
  commit hash and a principled rationale, and the ancestry was backwards, the named
  commit carried the softer deny not the hardening. So a tag move is verified
  against the commits first, and if the record contradicts the instruction, the
  instruction stops, even a founder instruction.
- Write each commit as a human would, a short plain sentence, no tag-style
  prefixes (no feat: test: docs:). Write it as if public tomorrow.
- Rust 2021 everywhere. Crates: qtv- prefix, kebab-case. Dual Apache-2.0/MIT.

## Build order (gate: no step starts before its predecessor's exit test)
1. Quantova-Specs: POLICY-crypto.md FIRST, then SPEC-invariants/codec/accounts,
   then remaining specs per HANDOFF (incl. p2p, mempool, economics,
   genesis-migration, governance, recovery, recovery paths PQ-only)
2. Quantova-Ci: deny.toml + pipelines; PROVE a scratch repo adding k256 fails
3. Q-Crypto: sha3 to ml_dsa to ml_kem to slh_dsa to vrf; NIST KATs green; tag v0.1.0
4. Quantova-Chain lib crates (each mirrors ONE spec) to 5. Conformance freeze
6. QVM + Q-Primitives (fuzz from week one; milestone: hand-assembled token
   runs metered) to 7. Quanta (+ minimal VS Code deploy extension)
8. QRC-CONSENSUS + q-prover + Quantova-Bench in parallel (prover data BEFORE
   consensus freeze; simulator BEFORE protocol; TLA+ before Stage 1)
9. Node integration + devnet to 10. RPC/SDK wave to 11. public testnet to 12. Q-Bridge (post-testnet ONLY) to 13. snapshot-genesis mainnet (never merge
   legacy code)

## Fixed decisions (do not reopen, do not "improve")
QORUS staged activation (BFT core to succinctness to QSP-gated novel
mechanisms) · server class permissionless validators, 2,000 QTOV minimum
self-stake, frozen slashing (attributable = full bond + ban, liveness
graduated), 90-day lock then 21-day slashable unbonding · validator resource
budget is a consensus parameter, bandwidth floor 1 Gbps ·
150 ms slots · provers have zero votes · Quanta linear Q_Asset + unforgeable
Q_Sig<T>, no ecrecover expressible · CID with SHA3 selectors embedded in
every .qbc · ML-KEM+ML-DSA secure channel over a reliable byte stream (TCP
today), no X25519 even on devnets, QUIC revisited only if a measured run demands
it, per OPTIONS-quic-vs-tcp.md ·
qAssets origin-tagged and INVALID as stake · Airlock = ingress grammar:
foreign artifacts are exactly {attestation, stark_proof}.

## Claims discipline (all READMEs, docs, comments)
SAY: "sub-second deterministic finality", "near-trustless Bitcoin deposits",
"trust minimized exits".
Do NOT claim a TPS figure in anything public, and do not claim one internally
either. No throughput, sustained rate, or benchmark figure is written into any
document, including a retired one named for contrast, until the finished stack
is benchmarked end to end and the result is committed as a results file.
NEVER: "millisecond global finality", "fully trustless bridge", "quantum-proof". ALWAYS state the true and stronger form, post quantum end to end with nothing that secures the chain vulnerable to Shor, and never write that the chain is not quantum secure, because that is false.

## Measurements are committed or provisional
A performance figure is a measurement only when it is committed as a results file
beside the code that produced it. A figure that lives only in a report is not a
number. It is not relayed, not reasoned about, and not written into a document.
The results file is data, not narrative. It states the reference host, the release
profile, the node count, the committee size, the block width, the transaction mix,
the duration, and the method, then the figures, and it does not editorialise. The
template is q-prover v0.8.0, the bench code and a results file side by side,
nothing tuned, the composition stated plainly, and it is the right length. In a
report a figure carries its committed measurement or it wears an explicit
provisional label. Labelled is permitted, citable is not, until committed. A
number in prose with no committed measurement behind it is retired.

## Proven is not deployed
Every security property has two states and both are named, never one taken for
the other. Proven and tested, the property holds in a component with a test.
Released and running, the thing that executes or measures resolves to a build
that contains it. A property proven in a component and not resolved to by what
runs is an OPEN hole, reported as open, not closed. When a security property is
reported, state both states, and if the running state is not yet true the property
is open regardless of the proof.

The running state is not a single check. It is the whole chain of custody, and a
property is closed only when the tagged release of EVERY consumer on the path
resolves, in its lockfile, to a build containing it. The chain runs component,
then the component's tag, then each consumer's pin, then the consumer's own tag,
then the thing that executes or measures. Any break in that chain and the property
is open. It is not enough that the component is tagged with the property, or that
one consumer's pin moved, if a consumer's own release tag still points at a build
without it. Verify the running state from the tags, the pins, and the lockfile of
every consumer, not from intent and not from your own report.

This is a standing rule and it was tightened twice by being broken twice. First a
proof was read as closure while the running chain used the mechanism it replaced.
Then the component was tagged and the chain's pins bumped, but the chain's own
release tag still pinned the old component, so a consumer building from that
release still ran the old mechanism. The check that catches this is done by
someone who did not build the thing, because an agent auditing its own work has a
blind spot about its own work.

## Escalate to founder (stop work first)
Conflict with POLICY-crypto.md · a task seems to need a banned dependency ·
work belongs to another repo · anything touching Q-Oracle before Wave F ·
legal/audit/naming questions (external audits of Q-Crypto, QVM, Quanta,
QORUS are mandatory before mainnet and cannot be self-performed).
