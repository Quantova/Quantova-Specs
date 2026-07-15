# Transaction availability and the per validator floor

Status. This is decision input analysis for the founder, not normative and not an implementation. It scopes the architectural question under the bandwidth floor decision, which validators must hold the transaction data and why, whether that can be lightened, and what it would cost in trust and complexity. It builds nothing. It is brought with the FN-DSA signature size measurement so the bandwidth floor is decided against the two levers that are real rather than against erasure coding, which does not lower what a reconstructing validator downloads.

## The floor in one line

At a hundred thousand transactions a second and about 3.5 kilobytes a transaction, a node that downloads every transaction faces about 350 megabytes a second, which is roughly 2.8 gigabits a second sustained. Erasure coding does not change that for a node that must reconstruct the block, by information theory reconstruction gathers a whole block. So the floor is lowered only two ways, a smaller transaction, or fewer nodes needing the whole transaction stream. This paper scopes the second.

## Which validators download the full block today, and why

Three populations, and only one of them is unavoidable.

The block producer holds everything, it built the block. The committee for the round, about five hundred validators, must download and hold every transaction, because to attest they verify the block, and verifying means executing the transactions to check the state transition and re deriving the state root. An attestation to a block a validator did not execute is a rubber stamp, which the design forbids. So the committee genuinely needs the whole block.

The larger population is the rest of the active set and the other full nodes, the validators not on this round's committee. They download and execute every block too, but for a weaker reason, to keep a full copy of the state so they are ready to serve on a future committee and to answer state queries. They are not verifying this block for consensus, they are maintaining state. This is the population that makes the floor a whole set problem rather than a five hundred node problem, and it is the population whose download could in principle be lightened.

Light clients already download only headers and the certificate, not bodies, so they are not part of the floor.

## Whether the non committee validators could follow from the certificate instead

Partly, and the part that works is worth having, but it does not remove the need for someone to hold the data.

Under the byzantine assumption the protocol already makes, fewer than one third of a committee is faulty, a supermajority attestation is itself evidence the block is valid, because an invalid block cannot gather two thirds of honest attestations. So a non committee validator that trusts that assumption can follow the chain from the certificate alone, it learns which block is final and it can carry the state root forward, without re executing. That is real and it is most of what a follower needs to track finality.

What it cannot do from the certificate is hold the state contents. It has the state root, a commitment, but not the balances and the code behind it, so it cannot answer a query or serve a proof or step onto a committee without first fetching the state from someone who did hold it. And the deeper problem, if every non committee validator followed from the certificate and none held the data, the data would not be held anywhere outside the current committee, and a committee that finalized a block whose data it then withheld could not be caught, because no one else has it to reconstruct. Following the certificate is safe for tracking finality and unsafe as a reason to stop holding the data.

## What the certificate would have to bind for a non downloading validator to be safe

Today the certificate binds two things, that a supermajority attested, and after the succinct certificate work, that their signatures verify over the exact decision preimage. It binds neither validity nor availability. For a validator to safely not download and execute, both of those would have to become properties it can check rather than things it trusts or downloads.

Validity, that the new state root really follows from the old one by executing the block. Today this rests entirely on the byzantine trust that two thirds of the committee executed and agreed. That is cheap and it already holds under the one third assumption, so a validator willing to rely on that assumption needs nothing more. To make validity trustless rather than trusted, the certificate would have to carry a validity proof, a proof that the state transition is correct, which is a categorically heavier object than the signature aggregation certificate it carries now, on the order of a full execution proof per block. That is a major addition and it is the zero knowledge rollup shape, not a tweak.

Availability, that the block data is actually retrievable so the state can be reconstructed and proofs served later. A signature that the committee saw the data is not a guarantee the data is available to anyone else afterward. This is exactly what data availability sampling provides on an erasure coded block. With the block coded and the shard commitment in the header, a node verifies availability by sampling a few shards at random against the commitment, and if the samples are present the whole is reconstructable with high probability, so availability becomes checkable without downloading the whole. The certificate or header must bind the erasure coding commitment for this to work, and that commitment is what the erasure coding lever already produces. So availability is reachable and the foundation for it is built. Validity is the harder half.

## What breaks if the transaction stream is split across the set

Splitting the stream, so different validators handle different shards of the transactions and no single validator processes every one, is the only thing that takes the per validator floor well below the whole block, and it is a major architectural change with real teeth, not a knob. Four things break and each needs an answer.

Cross shard transactions. A transaction that touches state in two shards can no longer be one atomic execution, it needs a cross shard commit protocol, which adds latency and complexity and removes the simple guarantee that any two transactions compose atomically. This is the largest cost and it changes the contract model.

Per shard security. If a shard is validated by its own smaller committee, that committee is easier to corrupt than the whole set, so the one third assumption is diluted per shard unless committees are rotated and sized carefully or a global validity proof backs each shard. Splitting the set splits the security.

Data availability. If a shard's validators hold only their shard's data, the whole block's data is available only if every shard is honest and online, and a dishonest shard could withhold. So sharding is safe only on top of a data availability layer that lets any node confirm the whole is available from a sample, which is again the erasure coding plus sampling, the foundation the availability lever lays.

State partitioning. The global state must be divided across shards and accounts and contracts assigned to them, with a messaging layer between, which touches the account model and the execution model directly.

So splitting the stream relieves the floor but pays for it in cross shard atomicity, diluted per shard security, a hard dependency on the availability layer, and a partitioned state. It is a roadmap decision, not a parameter.

## The honest data availability consequence

A chain where nobody holds the data is not a chain. Every scheme that lightens a validator's download has to answer where the data lives, because the state must be reconstructable, proofs must be serveable, and history must be auditable. The safe answer is not that individual nodes trust that someone somewhere has it, it is that the data is spread with redundancy across the set by the erasure code and any node can prove the whole is available from a small sample. That is why the erasure coding is the prerequisite and why it was worth building even though it is not itself a per validator saving. Without it, lightening the download means trusting that the data is held. With it, availability is a property the network verifies.

## The two real levers for the floor, for the bandwidth decision

Lever one, keep every validator executing and shrink the transaction. This keeps the full security model, every validator checks every block, no new trust, no availability risk, and it lowers the floor only by the signature size, a smaller user signature shrinking the bytes per transaction. The module lattice baseline is measured, an ML-DSA-65 signature is 3309 bytes, so at a hundred thousand transactions a second the signature bytes alone are about 331 megabytes a second, about 2.6 gigabits. FN-DSA, the compact lattice signature, would cut that hard, about eighty percent off the signature at the 512 parameter set, roughly a fivefold cut on signature bytes and about fourfold on the whole transaction, or about sixty percent at the 1024 set. But that number is a projection from published sizes, not a measurement, because FN-DSA is not implemented in the crypto repository, it is a two line placeholder. So it is a real and large lever but a conditional and future one, it needs FIPS 206 to be final, an audited constant time implementation that clears the floating point sampler hazard, and user adoption, and it leaves consensus bytes untouched since attestations and the certificate stay module lattice only. Until FN-DSA lands through the transition track the floor stands at the module lattice size. Simple and safe, and it leaves a real floor.

Lever two, let most validators stop executing every transaction, through data availability sampling for the data and either byzantine trusted validity, which is cheap and already holds under the one third assumption, or a validity proof, which is trustless and heavy. This is the only path that takes the floor well below the whole block. Its cost is the architecture above, the availability layer, which is founded but not built, and if pushed all the way to splitting the stream, the cross shard and per shard security costs too. Powerful and a major commitment.

The recommendation is not made here, it is the founder's, and it is the shape of the bandwidth floor decision. If the choice is to keep the full execution model, the floor is real and the decision is what bandwidth to require of a validator, lowered by the signature lever alone. If the choice is to reach below the floor, the decision is to commit to the availability architecture as a roadmap, with erasure coding already its first piece. The bandwidth floor should be set against these two, and against the measured signature number, not against erasure coding, which was mistaken for a per validator saving and is not one.
