# Mempool and data availability

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

The mempool holds admitted transactions that are waiting to enter a block, and the data availability store holds the bulk data that the throughput target depends on.

## Admission

A transaction enters the mempool only when it is valid, meaning its encoding is canonical, its signature verifies, its nonce matches the next expected value of the sender, and the sender can pay the fee and the gas. An invalid transaction is refused at the edge and never held.

## Priority

The mempool orders transactions by fee for a simple market, and within a sender by nonce so the order of a sender is preserved. A transaction that has waited too long is dropped. The pool has a bounded size, and when it is full the lowest fee transactions are evicted first.

## The data availability store

The heavy data, meaning the transaction bodies and the proofs, is held in a data availability store that is separate from consensus. Consensus commits to this data by its root in the block header, and a validator checks that the data is available without holding all of it. This separation is what lets the chain reach a high throughput, since consensus moves small commitments while the bulk data flows through the store.

## Determinism

Given the same set of admitted transactions and the same fee schedule, every node builds the same ordered candidate set, so block building is reproducible.
