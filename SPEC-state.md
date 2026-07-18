# State

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

The state is the set of all account and contract data at a height. It is held in a sparse Merkle trie so that every value has a proof and the whole state has one root.

## The trie

The state trie is a sparse Merkle trie keyed by a fixed width key and hashed with SHA3. A leaf holds a value, and each inner node holds the hash of its children. The root is a 32 byte value that commits to the entire state. Because the trie is sparse, an absent key has a proof of absence just as a present key has a proof of presence.

## Proofs

A proof for a key is the path of sibling hashes from the leaf to the root. A verifier recomputes the root from the claimed value and the path, and accepts only when it matches the state root in a block header. This lets a light client read any value with a proof and no full copy of the state.

## Determinism

The mapping from a set of key and value pairs to a root is fixed by this specification, so every node computes the same root for the same state. The order in which updates are applied does not change the final root.

## State access

An entry declares the keys it reads and the keys it writes. The machine enforces that an entry touches only its declared keys, which is what lets the scheduler run entries that do not overlap at the same time.

## Storage growth is bounded, not only priced

Storage growth is priced by the meter today and has no size ceiling, so a large meter budget can insert unboundedly many slots. That is closed before contracts land, and the instrument is a refundable storage deposit with a per transaction slot cap. To create a slot a contract locks a deposit in the native asset, refunded when the slot is freed, so unbounded growth requires unbounded locked native and there is no rent accounting and no eviction where state silently disappears. The per transaction slot cap is a cheap belt that stops a single transaction ballooning state in one step. The cost to a legitimate contract is proportional locked capital it recovers when it releases state, negligible for modest state and recoverable for a large registry, and the deposit rate is a genesis parameter. This is the chosen design and it is not built, since no contracts deploy yet.
