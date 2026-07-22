# The verifiable random function

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. It is implemented by the QVRF repository and consumed by the consensus and the name service by pinned tag.

QVRF is the first verifiable random function composed entirely from NIST standardized post quantum primitives, which are ML DSA, SLH DSA, and SHA3. It replaces the elliptic curve construction of the classical standard with one that survives quantum attack. It is designed so that randomness adds nothing to block production, to finality, or to throughput.

## Two layers

There is a per block beacon and a per user function.

The per block beacon produces a seed for each block. The seed is SHAKE256 over the previous seed, followed by the digest of the aggregated STARK certificate for the block, followed by the block height. The certificate is an artifact that consensus already produces, so the beacon is a hash of an existing value and not a new round of computation. The beacon drives leader election in the consensus. Its cost to the block pipeline is one hash, and that cost is proven by benchmark, not asserted.

The per user function lets a caller derive a random output bound to an input. The output is SHAKE256 over the ML DSA signature of the input, followed by the input. The full construction proves derandomized signing, the proof is the signature together with a STARK that the signing was run with a zero randomizer, and only that proof makes the output unique.

A correction is required here and it is load bearing. Uniqueness does not come from an honest signer choosing to derandomize. A signature made with a different randomizer over the same key and input verifies equally and yields a different output, and a verifier without the secret key cannot tell a derandomized signature from a hedged one, because the randomizer is not in the encoding. So a bare signature and hash does not enforce uniqueness against an adversary, and a sortition built on it is grindable.

This is resolved for the committee sortition by the one time key construction in SPEC-sortition-onetime.md, chosen after the derivation proof of option A was measured and rejected. There the sortition output is a deterministic hash of a committed preimage rather than a signature, one committed preimage per bonded account per slot, so uniqueness of the sortition output rests on a protocol level bound, one draw per account per slot backed by slashing, and not on a property of the signature. The specification and the paper state it exactly that way and no stronger, the sortition is unique by the protocol bound, not by signature uniqueness. Verifiability of a derived output comes from rechecking the signature and the proof, and this function maps to the virtual machine opcode that verifies a random output.

## The byte layout of the beacon

The beacon input is the concatenation of three fields in this order. First the previous seed, which is 32 bytes. Then the certificate digest, which is 32 bytes. Then the height, which is an eight byte unsigned integer encoded little endian by the codec. The output is the 32 byte SHAKE256 result. The first block uses a fixed genesis seed stated in the genesis tooling.

## One interface, two constructions

Both constructions implement one interface. The interface has an operation to generate an output for an input, an operation to produce a proof, and an operation to verify an output and its proof. The output type and the proof type are defined by the interface.

The first construction is hash based. It uses SLH DSA and Merkle authentication. It is the conservative baseline, it allows an unlimited number of evaluations, and it is implemented first.

The second construction is lattice based. It uses ML DSA and a STARK, and it produces compact proofs. It reuses the proving system in the prover repository.

The benchmark chooses which construction is the default. Neither construction is removed.

## Bias resistance as a reduction

The beacon derives from the aggregated certificate, not from any single validator value. No participant can grind the output without controlling the supermajority that forms the certificate, and controlling that supermajority is breaking consensus itself. Therefore the bias resistance of the beacon reduces to the security of consensus, and both are post quantum. The named assumption is that an adversary controls less than the consensus supermajority threshold. This is stated as an argument, not as a claim that the output cannot be biased.

## Verification surface

Verifying any output uses only SHA3 and NIST signature operations. There is no elliptic curve, no pairing, and no classical primitive anywhere in generation, proving, or verification. The deny list applies in full.

## The zero cost obligation

The claim that the beacon adds no measurable cost to block production is an evidence claim. It is true only when the benchmark repository shows no measurable difference in the block pipeline against a baseline that has no random function. That record must exist before the claim appears in any document. Say that the beacon adds no measurable cost to block production. Do not say instant or free.

## Security floor and claim discipline

Seeds, outputs, and digests are 32 byte values, meeting the security floor in the accounts specification. Say that this is the first verifiable random function composed entirely from NIST post quantum primitives, and that it is bias resistant by reduction to consensus security. Do not say provably secure until external cryptanalysis has occurred and is cited.
