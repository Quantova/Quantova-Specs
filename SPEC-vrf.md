# The verifiable random function

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. It is implemented by the QVRF repository and consumed by the consensus and the name service by pinned tag.

QVRF is the first verifiable random function composed entirely from NIST standardized post quantum primitives, which are ML DSA, SLH DSA, and SHA3. It replaces the elliptic curve construction of the classical standard with one that survives quantum attack. It is designed so that randomness adds nothing to block production, to finality, or to throughput.

## Two layers

There is a per block beacon and a per user function.

The per block beacon produces a seed for each block. For every reveal of the committee that finalized the block a ticket is computed as SHAKE256 over the previous seed, the ticket domain, the slot and the reveal. The seed is SHAKE256 over the previous seed, the beacon domain, the slot, and the one reveal with the lowest ticket. The reveals already travel with the certificate, so the beacon is a hash of existing values and not a new round of computation. The beacon drives leader election in the consensus. Its cost to the block pipeline is one hash, and that cost is proven by benchmark, not asserted.

The per user function lets a caller derive a random output bound to an input. The output is SHAKE256 over the hash based signature of the input, followed by the input. The construction uses the deterministic hash based signature set out below, so the same key and input always yield the same signature and the same output with no randomizer to vary, and the output is verified by rechecking that signature and its Merkle authentication. It carries no proof system and no STARK.

A correction is required here and it is load bearing. Uniqueness does not come from an honest signer choosing to derandomize. A signature made with a different randomizer over the same key and input verifies equally and yields a different output, and a verifier without the secret key cannot tell a derandomized signature from a hedged one, because the randomizer is not in the encoding. So a bare signature and hash does not enforce uniqueness against an adversary, and a sortition built on it is grindable.

This is resolved for the committee sortition by the one time key construction in SPEC-sortition-onetime.md, chosen after the derivation proof of option A was measured and rejected. There the sortition output is a deterministic hash of a committed preimage rather than a signature, one committed preimage per bonded account per slot, so uniqueness of the sortition output rests on a protocol level bound, one draw per account per slot backed by slashing, and not on a property of the signature. The specification and the paper state it exactly that way and no stronger, the sortition is unique by the protocol bound, not by signature uniqueness. Verifiability of a derived output comes from rechecking the signature and the proof, and this function maps to the virtual machine opcode that verifies a random output.

## The byte layout of the beacon

The ticket input is the previous seed of 32 bytes, the ticket domain QORUS/beacon/ticket, the slot as an eight byte little endian integer, and the reveal preimage. The beacon input is the previous seed of 32 bytes, the beacon domain QORUS/beacon/reveals, the slot as an eight byte little endian integer, then a single byte one followed by the chosen reveal preimage, or a single byte zero when the committee carried no reveal. The output is the 32 byte SHAKE256 result. The first block uses a fixed genesis seed stated in the genesis tooling.

## The construction

The function has one interface, an operation to generate an output for an input and an operation to verify an output. The construction is hash based. It uses SLH DSA and Merkle authentication, both deterministic, so the same key and input always yield the same signature and the same output, and a derived output is unique for a key and input without any proof over a signature. It allows an unlimited number of evaluations. There is no lattice proof and no STARK anywhere in generation or verification.

## Bias resistance as a reduction

The beacon is deliberately not derived from the certificate or the block, because the leader chooses the block's contents and could grind them. Each reveal is fixed in advance by the committed one time tree, so no participant can choose its reveal, it can only withhold it. Withholding moves the beacon only while the withheld reveal holds the lowest ticket, so a coalition chooses among at most its reveals that rank below every honest reveal, rather than among every subset of its reveals as a beacon over all reveals would allow. The named assumption is that honest reveals are delivered within the reveal window. This is stated as a bounded bias, not as a claim that the output cannot be biased.

## Verification surface

Verifying any output uses only SHA3 and NIST signature operations. There is no elliptic curve, no pairing, and no classical primitive anywhere in generation, proving, or verification. The deny list applies in full.

## The zero cost obligation

The claim that the beacon adds no measurable cost to block production is an evidence claim. It is true only when the benchmark repository shows no measurable difference in the block pipeline against a baseline that has no random function. That record must exist before the claim appears in any document. Say that the beacon adds no measurable cost to block production. Do not say instant or free.

## Security floor and claim discipline

Seeds, outputs, and digests are 32 byte values, meeting the security floor in the accounts specification. Say that this is the first verifiable random function composed entirely from NIST post quantum primitives, and that it is bias resistant by reduction to consensus security. Do not say provably secure until external cryptanalysis has occurred and is cited.
