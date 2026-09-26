# The parameters manifest

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. It exists so that every chain constant is stated once, in one place, rather than scattered across the specifications with room for one copy to drift from another. Where a number here differs from an older sentence elsewhere in this repository, this document is the one to trust, and the older sentence is the one to fix. Every figure below is drawn from the specifications that define it and, where the specification is implemented, from the running code that enforces it. Nothing here is a throughput, latency, or benchmark figure, and none is added until the finished stack is benchmarked once end to end and the result is committed as its own results file elsewhere.

## Timing

The block interval is one second. The node paces block production so that a height does not open before roughly a second has passed since the last one, which is a floor on the pace rather than a promise of an exact clock, since real proposal and attestation time still varies within it. This is distinct from the shorter internal timing the consensus round uses while it works within that second to select a leader and gather attestations, which is its own protocol parameter and not the block cadence a user or an operator should expect.

## Chain identity

The network is named and the numeric chain identifier is derived from that name, never chosen as a bare number. Mainnet is Q-main-net-1, testnet is Q-test-net-1, and the development network is Q-dev-net-1. The numeric chain id that a transaction signs is the leading eight bytes of the SHA3-256 of the network name, so every signature is bound to one network and can never be replayed onto another. The name is the pinned identity and the number always follows from it, so there is one place to change and no bare hexadecimal identifier lives anywhere in the stack.

## Mempool capacity

The mempool admits up to sixty five thousand five hundred and thirty six transactions in total, with a limit of one thousand and twenty four transactions from any one sender and a reserved allowance of five hundred and twelve slots held for the highest priority transactions so a flood from low fee senders cannot crowd out the pool entirely. The bound is a count of transactions, not a byte size, and when the pool is full the lowest fee transaction is evicted first to make room for a higher one.

## Committee size

The committee sampled each round is budgeted at five hundred. This is a target the stake weighted sortition draws toward, not an exact count enforced every single round, so the realized size of a given committee sits close to five hundred rather than at exactly five hundred every time. The eligibility floor beneath it is the minimum self stake of two thousand QTOV, discussed again under the caps below, and this floor is load bearing for the neutrality proof behind the sortition itself, not only for the validator economics.

## Finality threshold

A block finalizes once more than two thirds of the sampled committee has attested to it, the standard quorum of two thirds plus one. This is the same figure as the byzantine fault assumption the whole protocol rests on, that fewer than one third of the committee is faulty, and the two are the same threshold seen from two directions, what it takes to finalize honestly and what it would take to break that guarantee.

## The fee split

Every transaction fee divides three ways. Seventy percent is burned, permanently removed from the total supply through the same debit that keeps the sum of every balance equal to the total supply. Ten percent goes to the account of the block's proposer, the only place transaction volume ever reaches a validator's income. Twenty percent goes to the grants account, a keyless address spendable only through a passed governance vote. Any rounding dust from the split falls to the grants share, so the three shares always sum to the fee exactly and no unit of the native asset is ever created or lost in the division.

## Governance tracks

Governance runs five parallel tracks, and a proposal passes only when three bars hold at once. Its aye weight must reach the track threshold of the whole staked electorate, 66.67 percent on Chain upgrades, Mint QTOV, and Bridge pool migration and 75 percent on Freeze and asset recovery and Blacklist and kill address. Turnout must reach at least 25 percent of the electorate. And aye must outweigh nay. A vote weighs the stake locked for it, up to the voter's bonded stake, times a conviction of one, one and a half, or two and a half for a one month, one year, or two year lock. Each track carries its own deposit, returned when the referendum passes and is not killed and otherwise forfeited to the treasury, and its own voting period and enactment delay. The Chain upgrades track carries a deposit of 225,000 QTOV, runs fourteen days, and enacts after seven days, and it is the only track that may propose and enact a parameter change. The Mint QTOV track carries a deposit of 400,000 QTOV, runs three days, and enacts after seven days. The Bridge pool migration track carries a deposit of 150,000 QTOV, runs five days, and enacts after seven days. The Freeze and asset recovery track carries a deposit of 29,250 QTOV, runs six hours, and enacts after one hour. The Blacklist and kill address track carries a deposit of 39,000 QTOV, runs two days, and enacts after one day. Governance may retune any deposit only within 1,000 QTOV and ten percent of the total supply. The emergency bridge freeze is a bonded action rather than a vote, and its bond is 39,000 QTOV, the same as the Blacklist and kill address deposit. It lasts up to seven days with a one day cooldown after a lift. The depositor who lifts it early gets the bond back, and expiry or a guardian or governance lift forfeits the bond to the treasury.

## Epoch length

The epoch is not an independently configured span today. It is set equal to the same slot budget that bounds the one time key sortition tree, the height horizon a validator's committed preimages are built to cover before the chain would otherwise run out of fresh draws. A public testnet run has generated that budget at one hundred thousand blocks, which is about a day at the one second block interval, and a local development network has run it at two hundred and sixty two thousand one hundred and forty four blocks. Because epoch length and the key horizon are the same number today, a continuous run in practice stays inside its first epoch until the horizon is reached, and a validator's own registration tooling can raise the horizon for a longer run at the cost of a larger key tree and more startup time. Independent epoch rotation at a shorter, fixed cadence is a known open item, not yet built.

## The genesis capture cap

At genesis, no single validator may bond a third or more of the total genesis validator stake, and no single account may hold a third or more of the total genesis account balance. A third is the byzantine fault threshold the whole protocol rests on, so a single party at or above that share could gather enough of a committee to break the two thirds guarantee the moment the chain starts. The rule refuses any party that reaches a third, and it is enforced by the genesis construction itself, so a genesis file that violates it is refused before the chain ever starts rather than caught afterward.

## The caps

The fee is capped by a native ceiling, a maximum number of base units a single fee can ever be, fixed at genesis and independent of the governance rate, so a stale price can never push a fee above that ceiling however far the target in United States dollars has drifted from it. The dollar figure that ceiling is aimed at is one tenth of one cent, held there by the governance set rate rather than by reading a live price in the transaction path, which the chain refuses to do.

Validator rewards are paid per session of 182 days, and the total paid to all validators in one session is capped at five percent of the total supply. Each session's rewards are drawn first from the 685,714 QTOV staking and network reward pool set once at genesis, and only once that pool is exhausted is the remainder newly issued, so payout moves existing value while the pool lasts and adds supply only under the session cap after it.

The single mint path in governance, the Mint QTOV track, is capped by a yearly ceiling of two percent of the total supply, and never less than 100,000 QTOV. A referendum that would mint above that ceiling is refused, however many votes it carries.

The minimum self stake to bond as a validator is two thousand QTOV. Below that floor a bond is refused. This floor is a security parameter as much as an economic one, since the neutrality proof behind leader selection depends on every account's stake fraction staying comfortably above a numerical cliff in the leader score, and lowering the floor to widen the validator set would silently weaken that proof.

Rewards go to validators only. There is no commission and no delegator share.

The bond lock is ninety days, during which an unbond request is refused outright rather than penalized, and once that lock has passed an exit request opens a twenty one day unbonding period during which the stake stays fully slashable. The earliest a validator can leave is one hundred and eleven days after bonding.

Slashing applies to one kind of fault only. Double signing, an attributable fault that cannot happen by accident and is provable on chain, burns the whole bond and brings a permanent ban with no exceptions. There is no downtime or liveness slashing, so a validator that is offline or misses slots never loses bond for it.

Reward payout carries its own timing cap. It does not begin until mainnet starts, and once it does a blackout period of three hundred and sixty five days follows before the first reward accrues. Each earned reward unlocks 365 days after it was earned, never on a fixed calendar date.
