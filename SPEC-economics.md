# Economics

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

This document defines the native asset, its issuance, the rewards, the fee flow, and the delegation tier. Every number here is a starting value set at genesis and changed only through the monetary and the constitution tracks of governance.

## The native asset

The native asset is QTOV. It pays fees, it is staked by validators, and it is the unit of the treasury. Its supply changes only through the single mint path in governance, capped by a hard ceiling so that cumulative minting stays at or below 2 percent per year.

## Validator rewards

A validator earns a reward for verification work in each round it is on the committee. The reward is paid from the fee flow and, where governance allows, from the capped issuance. A validator is never paid for being offline and is slashed only for equivocation.

## The prover auction

Provers do the heavy work of execution, signature verification, and proving, and they are paid from fees through an open auction. A prover bids to serve work and earns the fee for the work it completes. A prover holds a liveness bond that is forfeited if it accepts work and fails to deliver. A prover holds no vote.

## Fees, burn, and split

A transaction pays a fee in the native asset. Part of the fee is burned, which ties the asset to real use, and the rest is split between the validators of the round and the treasury. The exact shares are genesis parameters.

## Delegation

A holder who does not run a validator may delegate stake to one and share in its rewards, under an honestly labeled tier. Delegation carries the same slashing exposure as the validator it backs, and this is stated plainly to the delegator.
