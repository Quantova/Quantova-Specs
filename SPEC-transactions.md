# Transactions

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

A transaction carries an action against the chain, signed by the account that authorizes it. The signature is carried outside the transaction body, which is the wrapper requirement. This lets a verifier hash the body once, check the signature against that hash, and pass the body to execution, and it keeps the large signature material off the part of the transaction that execution reads.

## The wrapper

A transaction is a wrapper around a body. The body holds the sender address, a nonce that orders the transactions of a sender and stops replay, the gas limit, the fee, and the call, meaning the target and the encoded arguments. The wrapper adds the scheme identifier and the signature over the canonical encoding of the body. A verifier recomputes the body hash by the codec, checks the signature under the scheme identifier, and only then admits the transaction.

## Domain separation

The bytes that are signed are the body hash together with a fixed domain tag for transactions, so a signature made for one purpose cannot be replayed as a signature for another. The domain tag is stated here and never reused across message kinds.

## Fees and nonces

The fee is paid in the native asset. The nonce increases by one for each admitted transaction from a sender, and a transaction whose nonce does not match the next expected value of the sender is rejected. This gives a total order to the transactions of a sender and makes replay impossible.

## Validity

A transaction is valid when its encoding is canonical, its signature verifies, its nonce matches, and the sender can pay the fee and the gas limit. An invalid transaction is refused at ingress and never enters a block.
