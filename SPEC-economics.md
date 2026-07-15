# Economics

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

This document defines the native asset, its issuance, the rewards, the fee flow, and the delegation tier. Every number here is a starting value set at genesis and changed only through the monetary and the constitution tracks of governance.

## The native asset

The native asset is QTOV. It pays fees, it is staked by validators, and it is the unit of the treasury. Its supply changes only through the single mint path in governance, capped by a hard ceiling so that cumulative minting stays at or below 2 percent per year.

## Validator rewards

A validator earns a reward for the work it performs in each epoch it is on the committee, which is attesting and proposing, exactly the work the protocol can see and count. A validator is never paid for being offline, no work earns no reward. The reward is paid from the fee flow and from issuance, and the issuance source is the open founder decision costed in OPTIONS-validator-economics.md. The yield is not a set parameter and no longer carries a cap. It is emergent, the issuance pot divided by the total staked, so a small staked ratio gives a high yield that attracts validators and raises the ratio, which lowers the yield until it finds its own level, the way every large proof of stake network works. The slashing rule is decided and set out in the validator economics under permissionless section below.

Rewards accrue continuously and unlock at twelve months. An operator sees what it has earned accrue visibly per epoch as the work is performed, but the earned reward does not become transferable until the twelve month point that the bond lock below also sets. This is the sell pressure control, earned value is real and visible from the first epoch but does not reach the market for a year, and it is the right tool for that because it never starves the validator set the way a yield cap would at launch when the set most needs to grow.

## Validator economics under permissionless validation

The permissionless server class amendment reopened the economics. The slashing model is decided and is set out below, and the exit rules, the bond lock, and the reward accrual are decided and set out with it. The yield is now emergent with the cap removed. What remains for the founder decision is the issuance source that fills the pot, a pre allocated fixed supply pool or a perpetual declining mint, and the delegation model that carries the small holder. Both are modelled with their tables and their tradeoffs in OPTIONS-validator-economics.md, and neither is implemented until the founder chooses.

The slashing model, decided. The principle is maximum severity where a fault proves malice and a graduated survivable penalty where it proves only misfortune. An attributable fault cannot happen by accident and is provable on chain, so it carries everything. A liveness fault is a power cut, a network blip, or a cloud outage, so punishing it at full severity would destroy the validator set without deterring a single attacker. This is a protocol enforcement from on chain evidence, and it is separate from the Justice track of governance, whose second invariant forbids it from ever touching validator stake.

Attributable faults are slashed one hundred percent of the bond with a permanent ban, no exceptions. They are two draws revealed for one slot from one account, a preimage used out of its position, double signing or equivocation at any height, a long range attack, bridge attestation fraud, and a coordinated or cartel attack, in which every participant loses their whole bond and is banned. An honest node cannot produce any of these through bad luck. This is what makes the bond cost of the one time key sortition real. A second draw costs a second full bond, and abusing it forfeits that bond entirely, against a gain of at most one seat among about five hundred worth a single attestation. The penalty exceeds the gain by orders of magnitude, and that is the intent.

Liveness faults are graduated and scaled by correlation. Minor downtime, above five percent of assigned slots missed in an epoch, costs one percent of the bond and removal for the epoch. Repeated or prolonged downtime costs ten percent of the bond and removal. Both scale with correlation, how many validators failed in the same window, so an isolated operator whose machine died costs almost nothing while a mass simultaneous fault costs severely. This deters the thing worth deterring, correlated infrastructure concentration, and forgives the thing that is not malice. Without the correlation scaling a single cloud region outage would destroy a large fraction of total stake in one event caused by a provider rather than an attacker.

Slashed stake and forfeited bonds go to the governance controlled treasury. They are never taken by a company and never redistributed to other validators, because redistribution would pay the set for a peer's fault and invite exactly the coordination the slashing exists to deter. This is the protocol direction and it holds regardless of any other wording elsewhere.

Withholding carries no penalty and is recorded as an accepted residual. An account that draws, sees an unfavourable output, and reveals nothing is invisible and unpunishable, but it forfeits a seat rather than gaining one, so it is not an attack.

Exiting is a right and never a fault. A validator that requests to unbond or leaves the set is not slashed for it. Slashing an exit would be capital entrapment, and it would deter honest staking far more than it deters an attacker who was always prepared to lose the bond. The defence against a validator that bolts to escape a slash is not a penalty on leaving, it is the unbonding period, during which the stake stays slashable so a fault discovered after the request still lands.

The bond lock is twelve months, and it is a deliberate departure from the majors, stated here as a design choice with its cost named. A validator cannot unbond before twelve months have passed. The protocol refuses the request until then, which is not a penalty and not a slash, it is simply not available. Ethereum clears a withdrawal in three to seven days and Solana in two to three, so twelve months is twenty five to fifty times the majors and is a genuine deterrent to mercenary capital. The cost is accepted on purpose. The lock buys committed capital rather than capital that arrives for a yield and leaves at the first better rate, and committed capital is what suits a government aligned chain. It is stated plainly as ours, not offered as a neutral parameter.

After the twelve month lock ends, a validator that requests exit enters an unbonding period during which the stake remains slashable, so a fault discovered after the request still reaches the bond. The published period is fourteen days, and it is adequate for the attributable faults whose evidence is on chain at the time, the double draw, the out of position preimage, and equivocation. It is not automatically adequate for two faults, and this was checked rather than assumed. Bridge attestation fraud surfaces only when the slowest bridged corridor finalises or its dispute window closes, which can exceed fourteen days, so the unbonding for bridge exposed stake must be at least the slowest graded corridor's finality plus dispute window rather than a fixed fourteen days. A long range attack cannot be bounded by any unbonding period, because its evidence surfaces whenever the attacker chooses to reveal, and it needs a weak subjectivity checkpoint rather than a longer unbonding. Both of these are open and are set out in OPTIONS-validator-economics.md, and neither is wired in unasked.

Validators set a commission and delegators receive the rest. The commission runs from zero to twenty five percent, and it may change by at most one percent per epoch so a delegator is never surprised by a sudden jump. This follows the majors. Whether delegation disperses power by letting each holder choose a validator, or a pool concentrates it behind an operator, and whether the small holder is carried by delegation or by operators running many bonds each, is the delegation model that remains the founder decision, modelled with its break even arithmetic in OPTIONS-validator-economics.md.

The yield mechanism is decided and it is emergent. There is no cap. The yield is the issuance pot divided by the total staked, and it finds its own level as participation rises and falls, which is how Ethereum, Solana, Avalanche, and Cosmos all work and none of them caps a validator yield. Removing the cap is the fix for the failure the earlier draft named, that a fixed low cap left the permissionless set to the organization and to holders with a non economic reason to run a node. What is still open is not the mechanism but the source that fills the pot.

The staking model for holders. A holder staking 2 thousand QTOV through the application participates without running hardware, and the way that stake reaches consensus is a founder decision. Under delegation the holder chooses a validator and shares its reward and its slashing exposure, which disperses power across many chosen validators. Under pooling the stake sits behind an operator that runs the node, which concentrates power in the pool operator. The choice materially changes the staking, the rewards, and the decentralization of the set, so it is held and it is modelled with its break even arithmetic in OPTIONS-validator-economics.md.

The issuance source is the open decision, and it is a choice between two monetary designs, not a number to average. A pre allocated pool vests the fifteen percent staking allocation over its schedule with a fixed supply and no minting ever, which never touches the mint ceiling invariant. A perpetual declining mint issues new supply forever on a schedule that starts above the current two percent ceiling and would require that genesis ceiling to be raised as a Constitution track act. Both are modelled in OPTIONS-validator-economics.md with the yield they produce at thirty, fifty, and seventy percent staked, how long each lasts, and which lands in the target band of one point eight to two point four percent. The target band sits below the four to six percent opportunity cost of the bonded capital, which is a deliberate low inflation choice that leans on token appreciation, on the fee flow as throughput grows, and on the twelve month lock selecting committed capital, rather than on a high nominal coupon. That tradeoff is named honestly in the paper. The founder sets the source, the shape, and the fee split, and none of them is implemented until then.

## The prover auction

Provers do the heavy work of execution, signature verification, and proving, and they are paid from fees through an open auction. A prover bids to serve work and earns the fee for the work it completes. A prover holds a liveness bond that is forfeited if it accepts work and fails to deliver. A prover holds no vote.

## Fees, burn, and split

A transaction pays a fee in the native asset. Part of the fee is burned, which ties the asset to real use, and the rest is split between the validators of the round and the treasury. The exact shares are genesis parameters.

## Delegation

A holder who does not run a validator may delegate stake to one and share in its rewards, under an honestly labeled tier. The validator sets a commission from zero to twenty five percent that may move by at most one percent per epoch, and the delegator receives the rest. Delegation carries the same slashing exposure as the validator it backs, and this is stated plainly to the delegator. Whether delegation is the mechanism that carries the small holder, or operators running many bonds each carry the economics instead, is the open model in OPTIONS-validator-economics.md.

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
