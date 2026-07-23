# Qorus, the Quantova post quantum consensus and VRF

Status. Working draft, not normative, not committed, not built. Internal codename Sturche.
This is the design we build from the ground up, our own protocol on cited foundations. It
supersedes the earlier STARK certificate direction, which is dropped because proving was too
slow to make every block on affordable hardware.

Author position on originality. The protocol, the mechanism, the randomness construction, and
every line of code are ours and new. The foundations underneath, secure hashing, the hardness
that ML-DSA rests on, Byzantine agreement bounds, Merkle commitments, verifiable random
functions as a concept, are the shared mathematics of the field, owned by no one, and we cite
them openly. Building on cited foundations is what makes a system trustworthy and
standardizable, and it is the same posture as every landmark before us. Qorus is original at
the layer where originality is real and defensible, the design.

## The problem Qorus exists to solve

Classical BFT makes every validator verify every other validator's signature on every block,
so throughput falls as validators are added, the opposite of scaling. Post quantum makes it
worse, because the usual fix, folding thousands of signatures into one with BLS aggregation,
uses cryptography a quantum computer breaks, and post quantum signatures do not aggregate that
cheaply. Qorus removes the per validator verification bloat without any quantum vulnerable
cryptography, so validators share the verifying load instead of piling it on each other.

## The three layers

Qorus is a selection layer, an agreement layer, and a compression layer, over a staking base.

### Layer one, selection, the Quantova post quantum VRF

A verifiable random function lets a validator turn its secret key and a public seed into a
random output plus a proof, so anyone with the public key can check the output is the one and
only correct value, and no one can predict it in advance. Qorus uses a VRF built on hash
security alone, the most conservative and most quantum resistant assumption in cryptography,
so the randomness that drives the whole engine rests on nothing that a quantum computer
threatens. Selection is stake weighted, a validator's chance of leading a slot or sitting on
the committee scales with its bonded stake.

The property that must hold, and that gets a proof and outside review before it guards value,
is uniqueness, exactly one valid output per input, unknowable before reveal. A flaw here would
let a validator grind or predict its own selection, which is riggable leadership, which breaks
the chain at the root. This is the highest stakes primitive in the system.

Our starting material is real, the stack already selects by a hash based one time key
sortition weighted by stake, so the work is to formalise and harden that into a proper VRF
with uniqueness and pseudorandomness proofs, spec it, and take it to standardization. There is
no standardized post quantum VRF in the world today, so this is a genuine first, not a copy.

### Layer two, agreement, the folding committee

The VRF selects a small committee for each block. The committee attests, and the attestations
fold together up a hierarchy. Each validator verifies only the small handful of attestations
directly beneath it and passes one combined attestation upward, so no validator ever verifies
all of them, and the verifying work spreads across the committee as it grows. Add a validator
and you add a helper in the tree, not a burden on everyone. Finality is deterministic, a block
is final by mathematics once the root certificate forms, not by probability.

### Layer three, compression, distributed hash based certificate

The folded attestations compress into one compact certificate that anyone can verify cheaply,
a light client on a phone, another chain, a regulator's auditor. The compression is
distributed, the tree does it as attestations fold, rather than one certifier building it at
the end. It rests on hash security only. The certificate is small and its verification cost is
near flat regardless of committee size, which is what keeps finality messages tiny and light
client checks cheap.

### The base, staking

Validators bond the native asset. Stake is the sybil defence and the weight in selection, and
provable misbehaviour, equivocation or an invalid attestation, is slashed. The cryptographic
mechanism and the economic mechanism back each other.

## What is ours, and what is cited

Ours. The three layer composition, the folding committee, the distributed hash based
compression, the specific VRF construction, and all of the code. This combination has not been
built.

Cited foundations, used and not reinvented. Secure hash functions. ML-DSA (FIPS 204) and
SLH-DSA (FIPS 205), both NIST finalised, for signatures. The VRF concept. Merkle commitments.
Byzantine agreement theory and its one third bound. These belong to the field.

## How Qorus differs from Algorand, stated so the originality is documented

Algorand's core consensus selects with a VRF built on elliptic curves, Ed25519, which a
quantum computer breaks, so its main consensus is not post quantum. Its post quantum security
lives only in its State Proofs, a separate light client mechanism that uses Falcon signatures
and compact certificates built at a certifier.

Qorus differs on four concrete axes.

1. Post quantum at the core, not bolted on. Algorand is post quantum only in its state proofs.
   Qorus is post quantum end to end, the VRF, the signatures, and the compression all.
2. Finalised standards only. Algorand's state proofs use Falcon, which is FIPS 206 draft and
   whose encoding can still change before final. Qorus signs with ML-DSA, finalised, and
   parks Falcon until it is final.
3. Hash only foundation for the VRF and the certificate. Algorand leans on curves and Falcon.
   Qorus rests the randomness and the compression on hash security alone, the most
   conservative post quantum assumption.
4. Distributed compression versus a single certifier. Algorand builds its compact certificate
   at one place. Qorus folds and compresses across the committee, so the work spreads with the
   validator set, which is the load sharing behaviour a single certifier does not give.

The only thing shared, and credited rather than hidden, is the generic goal every consensus
has, choosing validators fairly and verifiably by stake. Algorand pioneered doing that with a
VRF and we credit the approach openly, because pretending we invented sortition is the one
thing a serious reviewer catches and never forgives. The resemblance stops at the goal. Our
VRF is a different cryptographic family from theirs, hash based on the finalised SLH-DSA
standard against their elliptic curve, our attestations fold across a committee tree where
theirs do not fold at all, and our post quantum reaches the core where theirs reaches only the
light client. Sharing a goal is not sharing a design, and Qorus is a different machine on all
four axes above.

A note grounded in our own code, so this is not aspiration. The Q-Crypto crate already carries
this VRF, keyed on SLH-DSA (FIPS 205), proof is the deterministic signature, output is SHAKE256
of it. So the hash based, quantum safe VRF that separates us from Algorand's curve based one
exists today, and Phase one is to formalise, prove, and harden it, not to build it from zero.

## Honest performance posture

Finality. Deterministic and targeted under one second on good networks. Real and defensible.

Throughput. There are two different signature walls and they need different answers. The
consensus certificate wall, the cost of proving a quorum agreed, Qorus removes with the
folding and compression. The transaction signature wall, one post quantum signature on every
user transaction, is the true cap on throughput and Qorus does not lift it by itself, because
users sign independently and post quantum signatures do not fold cheaply. A materially higher
throughput needs smaller transaction signatures, Falcon once final, or a proof over many
transactions, and those are separate levers pursued honestly. No throughput number is
published until it is measured on real hardware, committed as a results file, and defensible
under scrutiny, never a marketing figure.

Fit for governments and banking. Post quantum security end to end, deterministic sub second
settlement finality, tiny auditable certificates, on finalised standards only. That
combination, proven and audited, is the pitch a regulated institution signs.

## Non negotiables before mainnet

Safety and liveness proved with our own mathematics. The VRF uniqueness and pseudorandomness
proved. The whole design and code torn at by outside adversaries and reviewed. These are not
borrowing, they are the stress test that earns a standard, and unreviewed cryptography is
broken cryptography without exception.

## The plan

Phase zero, this design.
Phase one, formalise and prove the post quantum VRF, uniqueness and pseudorandomness, and
build a reference of it, all measured on the racks not a laptop.
Phase two, the folding committee and the distributed compression, spec and reference build,
benchmarked against Algorand's published compact certificate numbers so better means measured.
Phase three, safety and liveness proofs for the whole engine.
Phase four, adversarial testing and outside review, then a paper aimed at a top venue and the
randomness taken to a standards track.
Phase five, testnet, then mainnet.
