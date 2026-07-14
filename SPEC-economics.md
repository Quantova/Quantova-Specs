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

## The fee model

The fee is defined in United States dollars and charged in the native asset. Every fee, measured in dollar value, falls within a band from one hundredth of a cent to one tenth of a cent, which is USD 0.0001 to USD 0.0010, and it never exceeds one tenth of a cent whatever the price of the native asset. This band is set to undercut the incumbent low fee chains so the chain is attractive for stablecoin settlement.

## How a fee is computed

The fee schedule is stated in dollar micro units, not in the native asset. A simple transfer sits at the low end of the band, and a heavier transaction scales up within the band but never past the one tenth of a cent ceiling. At the time a fee is charged it is converted to the native asset by a protocol parameter, the rate of the native asset in dollars. The fee in the native asset is the fee in dollars divided by that rate.

## The rate parameter

The rate of the native asset in dollars is a protocol parameter updated through governance on the monetary track, on a controlled cadence and within a bounded step size per update. It is never read from a live external price feed in the transaction hot path, because a live feed in consensus is a manipulation risk and a liveness risk, and it is prohibited.

## The ceiling is a runtime invariant

The one tenth of a cent ceiling is a hard runtime invariant. Any schedule or any rate that would produce a fee above USD 0.0010 is unenactable, refused the way an over ceiling mint is refused. The fee can never exceed the disclosed maximum even when the rate is stale or the price of the native asset moves sharply.

## Fee edge cases

When the rate is older than a defined freshness window, the fee is clamped to the band using the last known rate. Drift toward a cheaper fee is allowed, and a fee above the band is impossible. When the native asset rises sharply, the fee becomes a smaller amount of the native asset while the dollar ceiling still binds, so a user pays less and never more. When the native asset falls sharply, the base fee stays in the band, and the congestion component below handles spam separately and never pushes a normal user over the band. A fee never rounds to zero, since the one hundredth of a cent floor keeps spam paying something.

## The congestion component

There is a congestion component that is zero under normal load and rises only when block capacity is genuinely contended. It exists only to price out a flood. It is not a permanent priority fee market, and it must not inflate the everyday cost the way priority fees do on other chains. Cheap fees come from abundant capacity, not from removing pricing.

## Fee reconciliation

The fee is split with a part burned and the rest shared between the validators of the round and the treasury, and the congestion component follows the same split. None of this changes the single mint path or the mint ceiling of the native asset, since the fee is a charge in the native asset and not an issuance of it.
