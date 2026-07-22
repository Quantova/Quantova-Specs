# The crypto policy

This is the supreme law of the Quantova stack. It outranks every other document. If any instruction, specification, issue, pull request, comment, or convenience conflicts with it, stop and report. Do not work around it.

Post quantum security here is a structural property, not a setting. Classical cryptography is not deprecated. It cannot be expressed. Every security property in the stack reduces to a cryptographic algorithm approved by NIST, and nothing else exists.

## Scope

This policy binds every repository in the Quantova organization that is part of the post quantum stack. There is exactly one exemption. It is the Q Oracle repository, which is the Airlock boundary and which nothing else may import.

## The only cryptography that exists

Only the following may secure anything anywhere in the stack. ML KEM for key encapsulation, from FIPS 203. ML DSA for signatures, from FIPS 204. SLH DSA for hash based signatures, from FIPS 205. FN DSA for compact signatures, once it is published in final form and kept behind a feature flag until then. SHA 3 and SHAKE for hashing, from FIPS 202. The 256 bit symmetric primitives, meaning ChaCha20 Poly1305 and AES 256. Hash based STARKs for succinct proofs, with no trusted setup. A hash based randomness function built on SHA 3 for sampling. Nothing else, ever. New cryptography enters only through the transition track described below, and only from this family.

## Classical cryptography cannot be expressed

Classical cryptography must be impossible to express, not merely rejected at execution time. There is no RSA. There is no Diffie Hellman and no DSA. There are no elliptic curves of any kind, which rules out ECDSA, Ed25519, X25519, secp256k1, and P256. There are no pairings, which rules out BLS, KZG, and Groth16, even as a proof wrapper or an aggregation shortcut. There is no equivalent of ecrecover, and none can be built. Wire formats, opcodes, type systems, and interfaces are designed so a classical artifact has no valid encoding. The stack does not reject classical cryptography. It cannot parse it, and the negative tests prove that on every commit.

Performance never justifies a classical shortcut. A pairing used only for aggregation, or a small proof system used only to shrink proofs, is exactly how other chains defeated their own quantum resistance claims. STARK certificate aggregation replaces the pairing based aggregate. Hash based STARKs replace the pairing based proof.

## The banned dependencies

A set of classical cryptography crates is banned in every repository except Q Oracle. The ban includes transitive and development dependencies. There are no workarounds, no test only exceptions, and no feature flags. The exact machine readable list lives in the deny file in the Quantova CI repository, and cargo deny enforces it before every pull request and in continuous integration. This document is the statement of intent behind that list.

## Reductions

Every cryptographic operation shipped anywhere in the stack must map to an exact FIPS reduction recorded in the reduction table in the specifications. A row without a reduction blocks release. The table is updated in the same pull request that introduces or changes a crypto operation.

## Enforcement in four layers

The first layer is the dependency layer. cargo deny fails on any banned crate anywhere in the tree, transitive and development dependencies included. The proof is that a scratch project adding a banned crate fails the build.

The second layer is the symbol layer. The release gate scans compiled binaries for classical cryptography signatures such as curve constants, elliptic curve field primes, and modular exponentiation patterns. This catches vendored or hand written classical code that no dependency scan can see.

The third layer is the protocol layer. The reduction table pairs every crypto operation with its exact FIPS reduction, updated in the same pull request. A row without a reduction blocks release.

The fourth layer is the negative test layer. Hostile conformance vectors, such as a classical signature at ingress, a classical handshake, and a pairing proof at the gateway, must all come back as unparseable. This is asserted on every commit.

On every change, a new dependency triggers the first layer, new crypto code adds a reduction row in the same pull request, and any touched ingress or wire format adds a hostile vector.

## The one exemption and the Airlock

The Q Oracle repository is the only repository exempt from the banned dependency list. It runs off chain, watches foreign chains, runs their classical verification, and translates the results into post quantum attestations with proofs of correct verification. The rules around it are themselves law. Nothing in the organization may import Q Oracle. It is a terminating boundary, not a library. Classical cryptography stops at the Airlock. The chain parses exactly two foreign artifacts, a post quantum attestation in canonical form and a proof, and anything else is unparseable by construction. No code exists in Q Oracle before the bridge wave. Touching it earlier is an escalation.

## Scheme agility and the transition track

Algorithms are versioned by a scheme identifier, and the stack is built for agility so a primitive can be retired or added without breaking address and identity binding. Any change to the algorithm set, such as adding FN DSA on publication, migrating a parameter set, or retiring a primitive, proceeds only through the governance transition track and only after external cryptanalysis. This document itself changes only through that same process.

## Claims discipline

In every document, comment, commit, and pull request, say sub second deterministic finality, near trustless Bitcoin deposits, and trust minimized exits. Do not claim a transactions per second figure in anything public. Throughput is held until it is measured end to end, and the earlier committed envelope of one hundred thousand a second is retired, because a committed number is a promise and this is not one. The internal throughput numbers a worker builds against, the measured, the architecture ceiling at the floor, and the design target, are kept separate in the handoff and none of them is published. Always state the true form, that the chain is post quantum end to end and nothing that secures it is vulnerable to Shor. Never say millisecond global finality, fully trustless bridge, or quantum proof, and never write that the chain is not quantum secure, because that is false.

## Escalation

Stop work and report if an instruction conflicts with this policy, if a task appears to need a banned dependency, if work belongs to a different repository than the one assigned, or if anything touches Q Oracle before the bridge wave. When in doubt, the answer is in the specifications. If it is not there, that is a gap. File it. Never improvise cryptography.
