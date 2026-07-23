# The parameters manifest

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. It exists so that every chain constant is stated once, in one place, rather than scattered across the specifications with room for one copy to drift from another. Where a number here differs from an older sentence elsewhere in this repository, this document is the one to trust, and the older sentence is the one to fix. Every figure below is drawn from the specifications that define it and, where the specification is implemented, from the running code that enforces it. Nothing here is a throughput, latency, or benchmark figure, and none is added until the finished stack is benchmarked once end to end and the result is committed as its own results file elsewhere.

## Timing

The block interval is one second. The node paces block production so that a height does not open before roughly a second has passed since the last one, which is a floor on the pace rather than a promise of an exact clock, since real proposal and attestation time still varies within it. This is distinct from the shorter internal timing the consensus round uses while it works within that second to select a leader and gather attestations, which is its own protocol parameter and not the block cadence a user or an operator should expect.

## Mempool capacity

The mempool admits up to sixty five thousand five hundred and thirty six transactions in total, with a limit of one thousand and twenty four transactions from any one sender and a reserved allowance of five hundred and twelve slots held for the highest priority transactions so a flood from low fee senders cannot crowd out the pool entirely. The bound is a count of transactions, not a byte size, and when the pool is full the lowest fee transaction is evicted first to make room for a higher one.

## Committee size

The committee sampled each round is budgeted at five hundred. This is a target the stake weighted sortition draws toward, not an exact count enforced every single round, so the realized size of a given committee sits close to five hundred rather than at exactly five hundred every time. The eligibility floor beneath it is the minimum self stake of two thousand QTOV, discussed again under the caps below, and this floor is load bearing for the neutrality proof behind the sortition itself, not only for the validator economics.

## Finality threshold

A block finalizes once more than two thirds of the sampled committee has attested to it, the standard quorum of two thirds plus one. This is the same figure as the byzantine fault assumption the whole protocol rests on, that fewer than one third of the committee is faulty, and the two are the same threshold seen from two directions, what it takes to finalize honestly and what it would take to break that guarantee.

## The fee split

Every transaction fee divides three ways. Seventy percent is burned, permanently removed from the total supply through the same debit that keeps the sum of every balance equal to the total supply. Ten percent goes to the account of the block's proposer, the only place transaction volume ever reaches a validator's income. Twenty percent goes to the grants account, a keyless address spendable only through a passed governance vote. Any rounding dust from the split falls to the grants share, so the three shares always sum to the fee exactly and no unit of the native asset is ever created or lost in the division.

## Epoch length

The epoch is not an independently configured span today. It is set equal to the same slot budget that bounds the one time key sortition tree, the height horizon a validator's committed preimages are built to cover before the chain would otherwise run out of fresh draws. A public testnet run has generated that budget at one hundred thousand blocks, which is about a day at the one second block interval, and a local development network has run it at two hundred and sixty two thousand one hundred and forty four blocks. Because epoch length and the key horizon are the same number today, a continuous run in practice stays inside its first epoch until the horizon is reached, and a validator's own registration tooling can raise the horizon for a longer run at the cost of a larger key tree and more startup time. Independent epoch rotation at a shorter, fixed cadence is a known open item, not yet built.

## The genesis capture cap

At genesis, no single validator may bond more than half of the total genesis validator stake, and no single account may hold more than half of the total genesis account balance. This is a majority denial rule, not a rule capping any one party to a third, and holding exactly half is allowed since the rule only refuses a party that would exceed half. Its purpose is to stop a single validator or holder from being able to command a committee majority the moment the chain starts, and it is enforced by the genesis construction itself, so a genesis file that violates it is refused before the chain ever starts rather than caught afterward.

## The caps

The fee is capped by a native ceiling, a maximum number of base units a single fee can ever be, fixed at genesis and independent of the governance rate, so a stale price can never push a fee above that ceiling however far the target in United States dollars has drifted from it. The dollar figure that ceiling is aimed at is one tenth of one cent, held there by the governance set rate rather than by reading a live price in the transaction path, which the chain refuses to do.

A validator's own reward for a session is capped twice over. It is bounded above by a ceiling stated in United States dollars and converted to QTOV at the governance rate, so a validator's payout in QTOV shrinks if the price of QTOV climbs, and it is drawn from a staking and network reward pool that was set once at genesis and is not replenished by any ongoing issuance today, so the pool itself depletes as it pays out. Neither cap touches the total supply of the native asset, since the reward pool was minted once at genesis and every session's payout only moves value that already exists, it does not create new value at payout time.

The single mint path in governance, the Mint QTOV track, is not capped. There is no supply ceiling and no per year limit on it, by decision rather than by omission, and the amount any single referendum may mint is whatever that referendum enacts, gated only by the high threshold public vote itself. This is the one place the word cap does not apply, named here so it is not assumed to by accident.

The minimum self stake to bond as a validator is two thousand QTOV. Below that floor a bond is refused. This floor is a security parameter as much as an economic one, since the neutrality proof behind leader selection depends on every account's stake fraction staying comfortably above a numerical cliff in the leader score, and lowering the floor to widen the validator set would silently weaken that proof.

Validator commission runs from zero to twenty five percent, and it may move by at most one percentage point in an epoch and at most five percentage points in a day, so a delegator is never surprised by a sudden jump.

The bond lock is ninety days, during which an unbond request is refused outright rather than penalized, and once that lock has passed an exit request opens a twenty one day unbonding period during which the stake stays fully slashable. The earliest a validator can leave is one hundred and eleven days after bonding.

Slashing is graduated by the kind of fault. An attributable fault, one that cannot happen by accident and is provable on chain, costs the whole bond and a permanent ban with no exceptions. A minor liveness fault costs one percent of the bond and removal for the epoch. A repeated or prolonged liveness fault costs ten percent of the bond and removal. Both liveness penalties scale with how many validators failed together in the same window, so an isolated outage costs little and a mass correlated one costs severely.

Reward payout carries its own timing cap. It does not begin until mainnet starts, and once it does a blackout period of three hundred and sixty five days follows before the first reward accrues. Earned rewards themselves unlock on a rolling twelve month schedule from the epoch in which they were earned, never on a fixed calendar date.
