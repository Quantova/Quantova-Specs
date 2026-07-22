# Crypto dependency map

## What this records

This is the shape of the post quantum stack after the crypto unification pass. It
names which repository at which tag depends on which, and it shows where the
crypto crate resolves to exactly one version and where it cannot yet.

## The crypto crate

The crypto crate is Q Crypto. The whole stack pins it at tag v0.3.0. That tag is
the lattice based random function release, and its crate version field reads 0.2.0,
so a tree prints the node as qtv crypto v0.2.0 at tag v0.3.0. One tag means one
source and one node, which is what a single version graph requires.

## The unified part of the graph

q prover at tag v0.7.0 depends on the crypto crate at v0.3.0. A tree over q prover
resolves exactly one crypto version, and the hardened deny file passes.

QRC CONSENSUS at tag v0.3.0 depends on the crypto crate at v0.3.0 and on q prover
at v0.7.0 for the stage two certificate proof. Its sampler now draws the committee
and leader sortition with the ML DSA random function and derandomized signing. A
tree over QRC CONSENSUS resolves exactly one crypto version, and the hardened deny
file passes.

## The part that cannot reach one version yet

Quantova Chain moves its library crates to the crypto crate at v0.3.0 and its
consensus pins to QRC CONSENSUS at v0.3.0. The library crates on their own resolve
one crypto version. The node and the devnet also depend on the virtual machine
QVM, and QVM pins the crypto crate at v0.1.0 at every tag it has published, both
v0.1.0 and v0.2.0. So the node and the devnet resolve two crypto versions, v0.1.0
reached only through QVM and v0.3.0 reached everywhere else. This is not a Chain
pin left behind. It is QVM carrying an old crypto pin, and QVM is outside this
pass. Until QVM is released against the crypto crate at v0.3.0, Quantova Chain
cannot reach one version, so it is not tagged v0.5.0 and its crypto bump is held.

Quantova Bench depends on the crypto crate at v0.3.0, on QVM at v0.2.0, on the
Quantova Chain crates, and on the consensus crates. It reaches the same two crypto
versions through QVM and the node, so the end to end finality re measurement on a
single version graph is held behind the same QVM release.

## The guard

Quantova Ci holds the deny file, which now fails a duplicate crypto crate version
as a hard error rather than a warning. The same file is propagated to every repo
that carries one. It passes over q prover and over QRC CONSENSUS, and it fails over
Quantova Chain and Quantova Bench, which is the drift it exists to make loud.
