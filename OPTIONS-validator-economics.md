# Validator economics, the models and the open choices

Status. This is a decision paper for the founder, not normative. It models the two issuance mechanisms the specifications currently imply, it lays out the delegation options, and it reports one real gap in the unbonding defence. It implements none of these. The decided items from the validator economics note are written into SPEC-economics and SPEC-consensus-qorus separately. Nothing on performance is claimed until the sortition work lands.

## The one figure I could not find, and why it does not change the yields

The yields below do not depend on the total supply. A yield is issuance divided by staked, and the total supply cancels from that ratio, so every percentage in this paper holds for any supply. This matters because the total supply is not written down anywhere in the specifications I can read. I derived about 4.571 million QTOV from your figure that 15 percent equals 685,714 QTOV, and the same figure squares with your own worked bond, 2 thousand QTOV at 70 dollars being a 140 thousand dollar bond. But it is a derived number, not a stated one, and the supply base should be written into the economics specification once you confirm it. The absolute QTOV amounts and the raw sell pressure scale with the true supply. The yield percentages do not.

## The contradiction, stated exactly

The specifications imply two different and incompatible issuance mechanisms.

One is a pre allocated pool. The tokenomics allocates 15 percent of supply, 685,714 QTOV, to staking and network rewards over 48 months. Read literally this is a fixed pot that vests out over four years from tokens that already exist at genesis, so it mints nothing and the total supply never changes.

The other is a perpetual mint. The emission schedule issues new tokens at 3.00 percent of supply a year declining to a 1.25 percent terminal floor. Read literally this creates new supply every year forever.

These are not two numbers to average. They are two different monetary designs, one with a fixed supply and one with perpetual inflation, and the choice governs whether QTOV is a capped asset or an inflating one.

There is a second clash underneath the first, and it is the one that constrains you. The 2 percent per year mint ceiling is a protocol invariant, invariant four in the governance specification, enforced as unenactable and set at genesis. A pre allocated pool mints nothing, so it never touches the ceiling. A perpetual mint at 3.00 percent breaches it. To run the perpetual schedule you would have to set the genesis mint ceiling at 3 percent or above, which is a Constitution track decision about a protocol invariant, not a parameter tweak. Only the early years of the schedule breach it, the 1.25 percent floor sits under 2 percent, but the genesis ceiling still has to be set high enough to permit the peak.

## Model A, the pre allocated pool with a fixed supply

The 15 percent pool distributes over 48 months, which is 3.75 percent of supply paid to stakers each year for four years. It mints nothing, the supply is fixed forever, and the governance mint ceiling is untouched. After 48 months the pool is exhausted and stakers earn only the fee flow.

| Staked ratio | Yield across the 48 months | After the pool |
| --- | --- | --- |
| 30 percent | 12.50 percent | fees only |
| 50 percent | 7.50 percent | fees only |
| 70 percent | 5.36 percent | fees only |

Two things to see here. The yield is far above your 1.8 to 2.4 percent band for the whole four years at every plausible participation. And there is a cliff at month 48 where issuance goes to zero and yield drops to whatever fees provide, which is thin at launch. The 12 month lock softens the cliff only for the earliest bonders, who can unlock before it, and everyone who bonds later faces the full drop. A pre allocated pool at this rate is generous then abrupt.

## Model B, the perpetual declining mint

New issuance is a percentage of supply each year, starting at 3.00 percent and declining to a 1.25 percent floor. Yield is that rate divided by the staked ratio.

| Staked ratio | At the 3.00 percent start | At the 1.25 percent floor |
| --- | --- | --- |
| 30 percent | 10.00 percent | 4.17 percent |
| 50 percent | 6.00 percent | 2.50 percent |
| 70 percent | 4.29 percent | 1.79 percent |

This has no cliff, the floor is perpetual, and at the floor with high participation it sits close to your band. But the start breaches the mint ceiling invariant, so adopting it means setting the genesis ceiling at 3 percent, and the early yield is well above the band until the schedule declines.

## What rate actually lands in your band

Your target is 1.8 to 2.4 percent. Working the ratio backwards, the issuance rate that keeps yield inside the band is the band times the staked ratio.

| Staked ratio | Rate for the 1.8 percent floor | Rate for the 2.4 percent ceiling |
| --- | --- | --- |
| 30 percent | 0.54 percent | 0.72 percent |
| 50 percent | 0.90 percent | 1.20 percent |
| 70 percent | 1.26 percent | 1.68 percent |

So to hold the band across 30 to 70 percent participation the issuance rate has to sit somewhere between about 0.5 and 1.7 percent of supply a year. Neither proposed schedule is set there. The pool pays 3.75 percent a year, more than double the top of that range. The perpetual schedule starts at 3.00 percent and only reaches the range at its 1.25 percent floor, and even then only for a participation window. The 1.25 percent floor gives a yield inside the band precisely when the staked ratio is between about 52 and 69 percent. Below 52 percent staked the floor overshoots the band, above 69 percent it undershoots.

The plain reading is that the band you want implies an issuance rate near 1 percent of supply a year, and both current schedules are set higher. To land in the band from day one you either stretch the pool from 48 months to about twelve years, or you start the perpetual schedule near its floor rather than at 3 percent.

## The honesty note the band forces

Earlier analysis in the economics specification put the opportunity cost of the bonded capital at 4 to 6 percent a year and concluded that a yield below that does not make bonding rational for a purely economic operator. Your target band of 1.8 to 2.4 percent sits below that opportunity cost. That is not a contradiction to hide, it is a strategic choice to name. A low nominal yield keeps inflation and sell pressure down and leans on token appreciation, on the fee flow at scale, and on committed and aligned capital rather than on a high coupon. The 12 month lock is the instrument that selects for exactly that kind of capital. So the band is coherent with a government aligned chain that expects its value to come from use and appreciation, and it is honest to say that at 1.8 to 2.4 percent the pure yield case for a purely mercenary solo validator does not close on its own. It closes through appreciation, through fees as throughput grows, and through the lock selecting holders who are not pricing the bond against a treasury bill.

## The recommendation, framed for your decision

If the goal is a capped sovereign asset, Model A is the honest fit, it mints nothing and never touches the invariant, and the work is to stretch its distribution so the yield lands in the band and the month 48 cliff is replaced by a taper into fee funding. If the goal is a perpetual security budget that never runs dry, Model B is the fit, and the work is to start it near its floor and to raise the genesis mint ceiling deliberately as a Constitution track act. A hybrid is available and is what the earlier spec recommended, a small perpetual floor near 1 percent that keeps yield in the band and never expires, paid alongside a growing fee share, with a modest front loaded pool for the launch window. The floor, the taper, the fee split, and whether the supply is capped are yours. I have implemented none of them. I have removed the 0.2 percent cap as you instructed, so yield is now emergent, and I have left the issuance source marked open pending this choice.

## The break even and the delegation question

Your arithmetic is right and it is the crux. A minimum bond of 2 thousand QTOV at 70 dollars is 140 thousand dollars. At the top of your band, 2.4 percent, that earns about 3,360 dollars a year. A server class node costs 3 to 6 thousand dollars a year. So a solo operator at the minimum bond and the target yield does not break even, and this is a property of the band, not a mistake, because a higher yield would clear it but would leave the band. At the higher yields the models produce in their early years the solo minimum bond earns well above server cost, so the break even problem is specifically a low band, steady state problem.

There are three levers, and the first two are the ones you named.

One, operators run many bonds per machine. This is the model that lets a low yield work, and it is what Ethereum solo staking actually does, one machine carrying many validator identities so the fixed infrastructure cost is amortised across all of them. In our numbers a single server carrying ten bonds spreads its 3 to 6 thousand dollars across ten, so the infrastructure cost per bond falls to 300 to 600 dollars and the 3,360 dollar reward clears it comfortably. The cost is that each bond is still 140 thousand dollars of capital, so this favours operators with capital to post many bonds, and it concentrates the set toward larger operators.

Two, delegation carries it. Small holders delegate to professional operators who aggregate enough stake that the fixed infrastructure is negligible against the pooled bond, the operator takes a commission and the delegators net the rest. This is the model most proof of stake chains lean on, and it is viable at a low yield because the operator amortises one machine across a large delegated stake. The cost is concentration of a different kind, stake flowing through a smaller number of professional validators, and it makes the delegation structure load bearing rather than optional. The structural sub choice here is the one already held in the specifications, whether delegation disperses power by letting each holder choose a validator, or pooling concentrates it behind an operator that runs the node.

Three, raise the minimum self stake so a single bond clears infrastructure on its own. At 2.4 percent a bond has to earn 6 thousand dollars to cover the top of the server range, which is a bond of about 250 thousand dollars, roughly 3,570 QTOV at 70 dollars. A minimum self stake near 4 to 5 thousand QTOV would make a solo validator break even at the low band without any amortisation. The cost is a higher barrier to entry, fewer independent operators, and it pushes against the permissionless goal.

These are options, not a decision. My reading is that a low yield band and a permissionless goal together point at delegation carrying the small holder while the minimum self stake stays modest, with multi bond operators as the professional tier, but the choice is yours and each lever trades decentralisation against operator economics differently.

## The unbonding period against the evidence window, a real finding

You asked me to check the 14 day unbonding period against the time it takes for evidence of an attributable fault to surface, and not to assume it. I checked each attributable fault. The answer is that 14 days is adequate for most of them and structurally insufficient for two, and one of those two cannot be fixed by any unbonding period at all.

The immediate faults are covered. Two draws revealed for one slot, a preimage used out of position, and equivocation or double signing are all provable from evidence that exists on chain at or within moments of the slot, because the conflicting reveals or signatures are themselves the evidence and any honest party who sees them reports at once. A cartel attack leaves its coordinated blocks and votes on chain at the time, so the evidence persists, though detecting it depends on analysis that must run continuously rather than surfacing on its own. For all of these 14 days is ample.

Bridge attestation fraud is not reliably covered. Its evidence surfaces when the bridged chain finalises or when the bridge dispute window closes, and that time is set by the slowest corridor, not by us. A corridor with an optimistic challenge window of seven days plus its own finality and propagation can approach or exceed 14 days. So the unbonding period for stake exposed to bridge attestation has to be at least as long as the slowest bridged corridor's finality plus dispute window, and the gateway specification already grades corridors, so the adequate period should be tied to the slowest graded corridor rather than fixed at 14 days.

A long range attack cannot be covered by any unbonding period, and this is the important one. By its nature the evidence surfaces whenever the attacker chooses to reveal the alternate history, which can be long after the bond has unlocked and left. This is the classic weak subjectivity problem, and the standard defence is not a longer unbonding period but a weak subjectivity checkpoint, a recent trusted state that a syncing node starts from so that an old alternate history built from released keys is rejected outright. The 12 month lock and the 14 day unbonding both help by keeping capital slashable for longer, but neither closes this, and pretending the unbonding period defends against long range would be the assumption you told me not to make.

So the finding is three parts. Keep 14 days for the core protocol faults, it is adequate for them. Set the unbonding for bridge exposed stake to at least the slowest graded corridor's finality plus dispute window. And add a weak subjectivity checkpoint for long range, which is a consensus model addition and therefore a founder decision, not something I will wire in unasked. I have written the first into the specification, and I have marked the second and third as open with this paper cited.
