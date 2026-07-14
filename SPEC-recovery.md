# Key recovery

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

This document defines how an account recovers from a lost key. Every recovery path is post quantum only. There is no email reset, no classical escrow, and no third party that can move funds on its own.

## Guardian recovery

An account may name a set of guardians, each an account with its own post quantum key. A recovery is a request signed by the account owner where possible, or proposed by the guardians, and it takes effect only when a threshold of the guardians approve it with their machine lattice signatures. The threshold and the guardian set are chosen by the account owner.

## Time locked recovery

A recovery carries a time lock. After the guardians approve, the recovery waits for a stated delay before it takes effect, and during the delay the original owner can cancel it with the original key. This window defends against a set of guardians that turns malicious, since the true owner has time to stop a wrongful recovery.

## Binding a new key

A recovery binds a new post quantum key to the account and retires the old one. The new key is derived through the standard seed pipeline, so the recovered account has the same layered structure as any account. The name in the name service is repointed by the recovery.

## No classical path

There is no path that uses a classical signature, a password sent off chain, or a custodian that holds the key. Every step is a post quantum signature checked on chain.
