# Economics

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

This document defines the native asset, its issuance, the rewards, the fee flow, and the delegation tier. Every number here is a starting value set at genesis and changed only through the monetary and the constitution tracks of governance.

## The native asset

The native asset is QTOV. It pays fees, it is staked by validators, and it is the unit of the treasury. Its supply changes only through the single mint path in governance, capped by a hard ceiling so that cumulative minting stays at or below 2 percent per year.

## Validator rewards

A validator earns a reward for verification work in each round it is on the committee. The reward is paid from the fee flow and, where governance allows, from the capped issuance. A validator is never paid for being offline. The slashing rule and the size of the yield are reopened by the permissionless server class amendment and are held for the founder decision, set out in the validator economics under permissionless section below.

## Validator economics under permissionless validation

The permissionless server class amendment reopens three economic questions, and each is held for the founder decision, not resolved here.

The slashing model, recommended with numbers, held for the decision. A permissionless set cannot eject a bad operator by revoking approval, so slashing is the only enforcement. The model slashes for equivocation and for sustained downtime.

Equivocation, signing two different blocks at one height, is a direct attack on safety and is unambiguous and attributable to the certificate. The recommendation is a hard slash that scales with correlation, the fraction of stake that equivocated in the same window. A lone double sign, which is usually a misconfigured redundant signer, is slashed by a floor, on the order of a few percent of the offending stake, enough to hurt and to price out carelessness. A coordinated equivocation, many validators double signing together, which is the shape of a real safety attack, escalates toward the whole offending stake as the correlated fraction rises. So an accident costs a few percent and an attack costs everything, and the curve reads the difference from the certificate.

Downtime is a graduated penalty after a grace window, because in a set of paid server operators unpunished unreliability is a liveness attack surface rather than the unpredictable dropping of a phone. The recommendation is a grace of missing up to about one in ten of a validator's assigned slots per epoch at no penalty, which covers a restart or a brief outage, then a small per missed slot penalty beyond the grace, on the order of a hundredth of a percent of stake per slot and capped per epoch, and an escalation when a large share of the set is offline together, since a correlated outage is closer to a liveness attack than to bad luck. An honest operator holding well above ninety nine percent uptime is never meaningfully exposed, and a persistent absentee bleeds.

This couples to the one time key sortition of option C in the sortition uniqueness paper. The key ledger is stake bonded, and using a key out of turn or twice is detectable and is slashed as a protocol violation on the same footing as equivocation, so the key discipline that bounds grinding rests on the same economic penalty. The exact floor, the correlation curve, the grace fraction, the per slot rate, and the cap are the founder's to set, and nothing is implemented until then.

The staking model for holders. A holder staking 2 thousand QTOV through the application participates without running hardware, and the way that stake reaches consensus is a founder decision. Under delegation the holder chooses a validator and shares its reward and its slashing exposure, which disperses power across many chosen validators. Under pooling the stake sits behind an operator that runs the node, which concentrates power in the pool operator. The choice materially changes the staking, the rewards, and the decentralization of the set, so it is held.

The yield. The reward that makes running a validator rational was sized when a validator was a phone class near zero cost participant. A server operator pays real infrastructure, so the yield and the fee share must cover that cost and a margin, or the permissionless set is permissionless only on paper. The two levers are the issuance yield within the two percent mint ceiling, and the share of the transaction fee that flows to the validators of the round rather than to the treasury, which at real throughput is the larger and more sustainable source. The arithmetic, the realistic infrastructure cost, the break even stake at several yields, and the fee revenue at the target throughput, is delivered to the founder with the options, and the numbers are held for the decision.

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
