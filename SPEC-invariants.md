# Invariants, threat model, and scheme agility

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. Every other specification is bound by the invariants here.

## Threat model

The stack defends against a classical adversary and against an adversary with a working quantum computer. The quantum adversary can run Shor and Grover. Shor breaks every classical public key primitive, which is why none appears in the stack. Grover halves the preimage and search strength of a hash, which is why hashes and symmetric keys carry a 256 bit width, leaving a 128 bit margin. Every security property reduces to a NIST post quantum algorithm and to nothing else.

## The unrepresentable rule

Classical cryptography is not rejected at run time. It cannot be expressed. Wire formats, opcodes, the type system, and every interface are shaped so a classical artifact has no valid encoding. A parser that meets a classical signature, a classical handshake, or a pairing proof returns unparseable. The conformance vectors prove this on every commit. This rule binds every other specification in this repository.

## Scheme agility

Cryptography is versioned. A scheme identifier byte precedes every key, every signature, and every address payload. A new NIST scheme is added by assigning it a scheme identifier, and an old scheme is retired the same way, without breaking address or identity binding. The rendered forms, meaning the account and secret and hash strings, stay stable across this change. A change to the set of allowed schemes proceeds only through the governance transition track and only after external cryptanalysis, as the crypto policy requires.

## Determinism

Everything that consensus verifies is deterministic. Given the same inputs, every node computes the same output. There is no floating point in any consensus, gas, or state path. Gas is exact. Iteration order over any structure that affects state is fixed by the specification.

## The security floor

No key, address, or hash derived identifier is shorter than its security requires. The minimum payload is 192 bits and the canonical payload is 256 bits. A short key is a broken key. This floor is permanent and no pressure shortens it. The short human facing layer is names in the name service, never keys.
