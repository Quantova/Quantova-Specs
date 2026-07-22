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

## The compiler is the only path to bytecode

The Quanta language makes six exploit classes inexpressible: conservation, linearity, forged authorization, unchecked overflow, reentrancy, and front running of order flow. Every one is enforced in the type checker and lowers to no runtime opcode. The virtual machine has no concept of an asset and no concept of linearity. It moves words through registers and storage, and a program that mints value from nothing or drops an asset on the floor runs on the machine without complaint, because the machine has nothing to check it against. The guarantee holds for one reason only, that the compiler is the sole path from source to bytecode.

This is a hard precondition, not a note. Any deploy path added to the chain must refuse bytecode that did not come from our compiler, by provenance or attestation, or it must add the missing checks to the virtual machine itself. Absent one of those, the six for six claim is only as strong as no one hand writes bytecode, which is not a claim worth making. Until a deploy path exists the chain runs a single fixed transfer program and no user bytecode, so the precondition is not yet load bearing, and it must be met before it becomes so.

## The contracts countdown

The strongest protection a user has on the chain today is that the chain has no contract surface. A transfer is a transfer. There is no approve, no allowance, and no contract call, so the approval draining and blind signing that cost users the most on other chains are impossible here by construction. This is not a property the chain earned. It is a property of not having built the surface yet. The day asset lowering lands the whole class returns, and it returns at the language and the wallet, not the mempool. This is written as a countdown so that when asset lowering starts no one mistakes today's safety for something designed in. The protections that must be in place before that surface opens are the compiler provenance precondition above, the storage growth ceiling, and the closure of the signed integer gap between the language and the machine.

## Defences live in layers, and the layer is part of the defence

The mempool protects almost nothing. It admits and orders transactions and it can filter dust, and that is close to the whole of it. Structural safety lives in the virtual machine, where a dangerous operation is refused because the opcode does not exist. Correctness lives in the language, where the compiler is the only door to bytecode. Address confusion is a wallet and naming problem, not a mempool one, because a look alike address is cryptographically valid and nothing at the door can refuse it. A defence placed in the wrong layer is worse than no defence, because the layer that cannot enforce it is trusted to. Above all, order fairness cannot be enforced by honest mempool ordering, since a proposer is free to ignore it, so front running is defended by encryption or it is not defended at all.
