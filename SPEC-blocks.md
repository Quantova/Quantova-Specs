# Blocks

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

A block extends the chain by one height. It commits to the transactions it applied, to the resulting state, and to the events it emitted, and it carries the certificate that proves consensus finalized it.

## The header

The block header holds the height, the hash of the parent header, the state root after the block applied, the transaction root over the transactions in order, the event root over the emitted events, the beacon seed for the block, the proposer, and the time. Every root is a 32 byte value produced by the state and event tries. A light client follows the chain by the headers alone.

## The certificate slot

The header carries a slot for the aggregated certificate that consensus produces. The certificate is a single proof that the validator committee finalized the block, and it travels here rather than as a list of individual votes, so votes never consume block space. The digest of this certificate feeds the beacon for the next block.

## The body

The block body is the ordered list of transactions the block applied. Applying them in order against the parent state produces the state root in the header, so any node can recompute the root and reject a header that does not match.

## The event root

Every event a transaction emits is placed in the event trie, and the event root commits to all of them. A light client verifies that an event happened by checking a proof against the event root in a header, without holding the full state.
