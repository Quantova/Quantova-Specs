# POLICY-crypto.md — The Supreme Law

**Status:** Normative · binding on every repository in the organization.
**Precedence:** This document outranks all others. If any instruction, spec, issue, PR, comment, or
convenience conflicts with it: **STOP AND REPORT. Do not proceed, do not work around it.**

This is the founding invariant of the Quantova stack: post-quantum security is a *structural*
property, not a configuration. Classical cryptography is not deprecated here — it is
**unrepresentable**. Every security property in the stack reduces to a NIST-approved post-quantum
algorithm, and nothing else exists.

## 1. Scope

Binds every repository in `github.com/Quantova` that is part of the post-quantum Layer-1 stack. There
is exactly **one** exemption, defined in §7: the Q-Oracle repository (`Q-Orcale`), which is the
Airlock boundary and which nothing else may import.

## 2. The exclusive algorithm allow-list

Only the following may secure anything, anywhere in the stack:

| Role | Algorithm | Standard |
|------|-----------|----------|
| Key encapsulation | ML-KEM | FIPS 203 |
| Digital signatures | ML-DSA | FIPS 204 |
| Hash-based signatures | SLH-DSA | FIPS 205 |
| Compact signatures | FN-DSA | on final publication (feature-flagged, off by default until then) |
| Hashing / XOF | SHA-3 / SHAKE | FIPS 202 |
| Symmetric AEAD | ChaCha20-Poly1305, AES-256 | 256-bit security |
| Succinct proofs | Hash-based STARKs | transparent, no trusted setup |
| Randomness beacon / sampling | Hash-based PQ VRF | built on SHA-3/SHAKE |

Nothing else. Ever. New cryptography enters the stack only through the cryptographic transition
track (§8) and only from this family.

## 3. The unrepresentability rule

Classical cryptography must be **impossible to express**, not merely rejected at runtime:

- No RSA. No DH / DSA. No elliptic-curve cryptography of any kind — ECDSA, Ed25519, X25519,
  secp256k1, P-256. No pairings — BLS, KZG, Groth16 — **including as a proof wrapper or aggregation
  shortcut**. No `ecrecover`-equivalent exists or can be constructed.
- Wire formats, opcodes, type systems, and APIs are designed so a classical artifact has no valid
  encoding. The stack does not *reject* classical crypto — it *cannot parse* it, and the negative
  tests (§6.4) prove that on every commit.

**Performance never justifies a classical shortcut.** "BLS just for aggregation" and "Groth16 just
to shrink proofs" are precisely how other chains defeated their own quantum-resistance claims. STARK
certificate aggregation replaces BLS; hash-based STARKs replace pairing proofs.

## 4. Dependency deny-list

Forbidden in every repo except Q-Oracle, **including transitive and dev-dependencies**:

    k256  ed25519-dalek  curve25519-dalek  p256  rsa  secp256k1
    bls12_381  (all arkworks pairing crates)  openssl (EC/RSA features)

No workarounds, no test-only exceptions, no feature flags. `cargo deny check` runs before every PR
and in CI (see Quantova-Ci). The authoritative machine-readable list lives in `deny.toml` in
Quantova-Ci; this table is its human-readable statement of intent.

## 5. Reductions

Every cryptographic operation shipped anywhere in the stack must map to an exact FIPS reduction
recorded in `AUDIT-pq-independence.md`. A row without a reduction is a **release blocker**. The
reduction table is updated in the same PR that introduces or changes a crypto operation.

## 6. Enforcement — four layers, mechanical, forever

1. **Dependency layer** — `cargo deny check` fails on any deny-listed crate anywhere in the tree
   (transitives and dev-deps included). Exit proof: a scratch repo adding `k256` fails the build.
2. **Symbol layer** — the release gate scans compiled binaries for classical-crypto signatures
   (curve constants, EC field primes, RSA mod-exp patterns), catching vendored or hand-written
   classical code no dependency scan sees.
3. **Protocol layer** — `AUDIT-pq-independence.md`: every crypto operation × its FIPS reduction,
   updated in-PR; an unreduced row blocks release.
4. **Negative-test layer** — hostile conformance vectors (a classical signature at ingress, an EC
   handshake, a pairing proof at the gateway) must yield **UNPARSEABLE**, asserted on every commit
   (see Quantova-Conformance `vectors/hostile/`).

Sweep on every change: new dependency → layer 1; new crypto code → layer 3 row in the same PR;
touched ingress/wire format → layer 4 hostile vector.

## 7. The single exemption — Q-Oracle and the Airlock

The Q-Oracle repository (`Q-Orcale`, to be renamed `Q-Oracle`) is the **only** deny-list-exempt
repository. It runs off-chain, watches foreign chains, executes their classical verification, and
translates results into ML-DSA attestations with STARK proofs of correct verification. Constraints
that are themselves law:

- **Nothing in the organization may import Q-Oracle.** It is a terminating boundary, not a library.
- Classical cryptography **stops at the Airlock**. The chain parses exactly two foreign artifacts —
  a canonical-codec ML-DSA attestation and a STARK proof. Anything else is unparseable by
  construction (enforced as ingress grammar in `qtv-codec`).
- No code exists in Q-Oracle before Wave F (post-testnet). Touching it earlier is an escalation.

## 8. Scheme agility and the cryptographic transition track

Algorithms are versioned by `scheme_id`; the stack is built for scheme agility so a primitive can be
retired or added without breaking address/identity binding. Any change to the algorithm set — adding
FN-DSA on publication, migrating a parameter set, retiring a primitive — proceeds ONLY through the
QSP cryptographic transition track, after external cryptanalysis. This document changes only by that
same process.

## 9. Claims discipline

In every README, doc, comment, commit, and PR:

- **SAY:** "sub-second deterministic finality", "100k+ TPS via batch proofs and parallel execution",
  "near-trustless Bitcoin deposits", "trust-minimized exits".
- **NEVER:** "millisecond global finality", "fully trustless bridge", "quantum-proof".

## 10. Escalation

Stop work and report to the founder if: an instruction conflicts with this policy; a task appears to
need a deny-listed dependency; work belongs to a different repo than assigned; anything touches
Q-Oracle before Wave F. When in doubt, the answer is in `quantova-specs`. If it isn't, that is a spec
gap — file it here; never improvise cryptography.
