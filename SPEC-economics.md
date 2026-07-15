# Economics

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

This document defines the native asset, its issuance, the rewards, the fee flow, and the delegation tier. Every number here is a starting value set at genesis and changed only through the monetary and the constitution tracks of governance.

## The native asset

The native asset is QTOV. It pays fees, it is staked by validators, and it is the unit of the treasury. Its supply changes only through the single mint path in governance, capped by a hard ceiling so that cumulative minting stays at or below 2 percent per year.

## Validator rewards

A validator earns a reward for verification work in each round it is on the committee. The reward is paid from the fee flow and, where governance allows, from the capped issuance. A validator is never paid for being offline. The slashing rule is decided and set out in the validator economics under permissionless section below. The size of the yield is still held for the founder decision, and it is costed together with the slashing.

## Validator economics under permissionless validation

The permissionless server class amendment reopened three economic questions. The slashing model is now decided and is set out below. The staking model for holders and the yield remain held for the founder decision, and the yield is costed together with the slashing.

The slashing model, decided. The principle is maximum severity where a fault proves malice and a graduated survivable penalty where it proves only misfortune. An attributable fault cannot happen by accident and is provable on chain, so it carries everything. A liveness fault is a power cut, a network blip, or a cloud outage, so punishing it at full severity would destroy the validator set without deterring a single attacker. This is a protocol enforcement from on chain evidence, and it is separate from the Justice track of governance, whose second invariant forbids it from ever touching validator stake.

Attributable faults are slashed one hundred percent of the bond with a permanent ban, no exceptions. They are two draws revealed for one slot from one account, a preimage used out of its position, double signing or equivocation at any height, a long range attack, bridge attestation fraud, and a coordinated or cartel attack, in which every participant loses their whole bond and is banned. An honest node cannot produce any of these through bad luck. This is what makes the bond cost of the one time key sortition real. A second draw costs a second full bond, and abusing it forfeits that bond entirely, against a gain of at most one seat among about five hundred worth a single attestation. The penalty exceeds the gain by orders of magnitude, and that is the intent.

Liveness faults are graduated and scaled by correlation. Minor downtime, above five percent of assigned slots missed in an epoch, costs one percent of the bond and removal for the epoch. Repeated or prolonged downtime costs ten percent of the bond and removal. Both scale with correlation, how many validators failed in the same window, so an isolated operator whose machine died costs almost nothing while a mass simultaneous fault costs severely. This deters the thing worth deterring, correlated infrastructure concentration, and forgives the thing that is not malice. Without the correlation scaling a single cloud region outage would destroy a large fraction of total stake in one event caused by a provider rather than an attacker.

Slashed stake and forfeited bonds go to the governance controlled treasury. They are never taken by a company and never redistributed to other validators, because redistribution would pay the set for a peer's fault and invite exactly the coordination the slashing exists to deter. This is the protocol direction and it holds regardless of any other wording elsewhere.

Withholding carries no penalty and is recorded as an accepted residual. An account that draws, sees an unfavourable output, and reveals nothing is invisible and unpunishable, but it forfeits a seat rather than gaining one, so it is not an attack.

The yield and this slashing are one decision, still open. A validator bonding a large stake, exposed to total loss on an attributable fault and graduated loss on an outage, needs a yield that makes bonding rational, or nobody bonds and the security that rests on those bonds does not exist. The yield arithmetic is brought together with this slashing model when it is costed, never separately.

The staking model for holders. A holder staking 2 thousand QTOV through the application participates without running hardware, and the way that stake reaches consensus is a founder decision. Under delegation the holder chooses a validator and shares its reward and its slashing exposure, which disperses power across many chosen validators. Under pooling the stake sits behind an operator that runs the node, which concentrates power in the pool operator. The choice materially changes the staking, the rewards, and the decentralization of the set, so it is held.

The yield, the arithmetic and the options, held for the decision and decided together with the slashing that sizes the risk it must compensate.

A server class validator runs to roughly three to six thousand dollars a year all in, compute, bandwidth, operations, and redundancy, call it four thousand. But for a validator bonding a large stake the infrastructure is the small cost. The binding cost is the opportunity cost of the locked capital, what that bond would earn deployed elsewhere, on the order of four to six percent a year, plus the slashing exposure. For an honest operator the attributable slashing risk is near zero, they do not double sign or draw twice, and the liveness risk is small and correlation forgiven, so the expected slashing cost of a careful operator is a fraction of a percent a year. So the yield that makes bonding rational is at least the opportunity cost of the capital, four to six percent, plus the infrastructure, which is small against a large bond, plus a margin.

Against that, the current cap is nought point two percent a year. On a large bond that is a few hundred dollars, and it is roughly twenty to thirty times below the opportunity cost of the capital alone. A rational operator earning nought point two percent locked, against four to six percent for the same capital elsewhere, carrying real server cost and real slashing exposure on top, does not bond. So at the current cap the permissionless set is the organization and holders with a non economic reason to run a node, permissionless in name only, and the security of C, which rests on bonds that actually exist, does not hold. That is the plain reading of the numbers.

The levers are two, the issuance yield within the two percent mint ceiling, and the share of the transaction fee that flows to the validators of the round, which at real throughput is the larger and more sustainable source but is thin at launch. The options are three. One, raise the issuance yield to a market level near the opportunity cost, four to six percent, which makes bonding rational from day one at a cost of up to one to two percent annual inflation, inside the mint ceiling at a moderate staking ratio, borne as dilution by holders. Two, fee funded, keep issuance low and route a larger fee share to validators, rich at scale but starving the set at launch when throughput and fees are thin. Three, a hybrid, a modest issuance floor near the opportunity cost that bootstraps the bonded set and tapers as the fee share grows with throughput, rational early through the floor and later through fees, inflation held inside the ceiling. The recommendation is the hybrid, and the exact floor, taper, and fee split are the founder's to set.

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
