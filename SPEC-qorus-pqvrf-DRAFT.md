# Qorus PQ-VRF, construction and the security it must prove

Status. Phase one build artifact, working draft, not normative, not committed, not built. This
is the exact construction of the Quantova post quantum verifiable random function, the
selection layer the whole Qorus engine rests on, and a precise statement of the properties a
proof must establish before it guards value. It rests on hash security alone. No elliptic
curves, no pairings, nothing a quantum computer breaks.

## What it is, and what it is not

It is a real VRF, the same behaviour as ECVRF, with the elliptic curve replaced by a hash
based construction. A validator turns its secret key and a public per slot seed into a random
output plus a proof. The output is unpredictable to everyone else, anyone can check it against
the validator's public key, and there is exactly one valid output per key per slot so no one
can grind for a better draw.

It is not a bare hash straight through. A bare hash of a secret and a seed is unpredictable
and unique but cannot be proven correct without revealing the secret, and revealing the secret
lets everyone predict every future draw. The verifiability is the hard part, and we get it by
building the VRF on a hash based one time signature, where the signature is the proof.

Honest scope. This is a per slot VRF, one draw per slot index, which is exactly what sortition
needs, one committee draw per slot. It is not a general purpose VRF evaluable an unbounded
number of times on arbitrary inputs, because the one time keys underneath are consumed one per
slot. The finite key tree and its rotation are addressed under key management below.

## Measured on real hardware, and what it settles

A first measurement on rack06 of the stateless option, the SLH-DSA-SHAKE-192s VRF already in
Q-Crypto, is recorded here so the construction choice rests on numbers, not taste. It is
correct and quantum safe, verify passes, the proof is 16 KB, the keys are tiny, verify is about
3 ms, and keygen is about 397 ms. But evaluate is about 3.6 seconds, because stateless SPHINCS+
signing is slow by nature and the small signature variant is the slowest of the family. Three
and a half seconds per draw is fatal for a per slot VRF and would end any hope of sub second
finality.

This settles the construction. Stateless is the wrong tool for a per slot operation. The one
time construction is the right one, and it is what our existing sortition already runs on. The
stateless SLH-DSA VRF stays available for rare, non per slot uses. The per slot VRF is the one
time construction.

The fast construction, measured on rack04. The per slot VRF is realised over our own
qtv-sampler one time preimage tree, output is SHA3-256 of a domain tag, the slot's secret
preimage, and the beacon, proof is the preimage plus its Merkle path to the account's committed
root. Measured:

- evaluate about 2 microseconds, flat across tree sizes.
- verify about 16 to 22 microseconds.
- proof 480 bytes at 16k slots, up to 672 bytes at a million slots.
- one time tree build 78 ms at 16k slots up to 3.3 s at a million slots, an epoch provisioning
  cost, not a per slot one.

Against the stateless option this is roughly a million times faster to evaluate, about 24 to 34
times smaller in proof, and it removes the 3.6 second wall entirely. This is the construction
the spec builds on. The realisation refines the earlier Winternitz sketch, our actual primitive
is a one time preimage reveal, which is simpler and faster than a Winternitz signature.

## The construction

Primitives, all hash based and cited, none reinvented. A cryptographic hash H (SHA3 family). A
one time signature OTS in the Winternitz family (WOTS+ style), whose signature over a fixed
message under a fixed key is deterministic. A Merkle tree binding many one time public keys
under one root.

Keygen. A validator derives, from a secret seed, a Merkle tree of one time public keys, one
leaf per slot index it will ever act in. Its public key pk is the Merkle root. Its secret key
sk is the seed.

Evaluate, for slot s with public seed beacon_s.
1. Take the one time key at leaf s.
2. Sign the slot input m = H(beacon_s, s, domain) with the leaf's one time key, producing the
   deterministic signature sigma.
3. The VRF output is y = H(sigma, "qorus.vrf.output").
4. The proof is pi = (sigma, the Merkle authentication path from leaf s to the root pk).

Verify, given pk, beacon_s, s, y, pi.
1. Recompute m = H(beacon_s, s, domain).
2. Check sigma is a valid one time signature of m under the leaf public key recovered from
   sigma, and that the Merkle path binds that leaf to the root pk at index s.
3. Recompute y' = H(sigma, "qorus.vrf.output") and check y' equals y.
Accept only if both checks pass.

Why the three properties hold, at the level a proof will make rigorous.
- Uniqueness. The leaf is fixed by the slot index, and the one time signature over a fixed
  message under a fixed key is deterministic, so there is exactly one sigma, so exactly one y.
  A second valid sigma would be a forgery or a hash collision, both ruled out under the
  hardness of H. This is trusted uniqueness, the property that stops grinding.
- Unpredictability. Producing sigma requires the one time secret, which only the validator
  holds, so no one else can compute y before the validator reveals it, and y = H(sigma) is
  pseudorandom when H is modelled as a random oracle.
- Verifiability. The signature and the Merkle path are the proof, checkable by anyone against
  the public root, revealing nothing that lets others produce future draws, because each slot
  uses a fresh one time leaf.

## Stake weighted sortition on top

The VRF gives a uniform value. Read y as a number in the unit interval, y over two to the
output length. A validator with stake fraction f is selected into the slot committee, or as
leader, by comparing that value against a threshold derived from f and the target committee
size, the standard cryptographic sortition, so expected selections scale with stake and the
draw is publicly checkable from the VRF proof. A validator proves both that its VRF output is
correct and that the output falls under its stake threshold, and nothing else is trusted.

## Key management, and crypto agility built in

The one time key tree is finite, one leaf per slot, so a long run needs either a large tree at
genesis or key rotation at epoch boundaries where each validator commits a fresh tree bound to
its staking record. Rotation is the clean long run answer and it is a first class part of the
design, not an afterthought, the same seam the current sortition already has.

Crypto agility. The construction names H and the one time scheme as parameters, not as
hardcoded choices, and the public key commits to a scheme identifier. So the hash or the one
time family can be upgraded over decades, or a second scheme added, through a governed
parameter change and a key rotation, without a hard fork. This is the multi decade upgrade
path a government or bank system needs.

## What the Phase one proof must establish, precisely

1. Trusted uniqueness. Under the second preimage and collision resistance of H and the one
   time signature's determinism, no probabilistic polynomial time adversary produces two
   distinct accepted outputs for the same pk, beacon, and slot, except with negligible
   probability.
2. Pseudorandomness. The output is indistinguishable from uniform to anyone without the secret
   key, formalised in the random oracle model for H, before reveal.
3. Provability and completeness. An honestly generated output and proof always verify.
4. Sortition soundness. A validator cannot be selected above its stake weighted expectation
   except with negligible probability, and cannot suppress a selection it is entitled to.

## The build steps from here, on the racks not a laptop

1. Fix the exact parameters, the H instance, the WOTS+ parameters, the tree height, the output
   length, and the domain separation strings.
2. Write the reference implementation of keygen, evaluate, and verify, deterministic and test
   vectored.
3. Measure proof size and verify time on the racks, and compare honestly against ECVRF, since
   the hash based proof is larger and that cost must be stated.
4. Write the four proofs above, then take the construction toward a machine checked proof and a
   standards track draft.
5. Only after the proofs and an outside review does it drive real selection on a value bearing
   network.
