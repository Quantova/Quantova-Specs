# Quantova Governance

Quantova is governed by the holders of its native asset. Voting power is staked QTOV and nothing else. A token bridged in from another chain, however it is named, can never vote and can never be staked, because governance and staking read only the native balance and never trust a name. A copy of QTOV made on some other chain and carried across the bridge is a foreign asset with zero power here, and its ticker being identical changes nothing.

Every decision runs through one of five governance tracks, and every track passes on the same single bar. A proposal passes only when at least forty percent of the total staked QTOV votes in favour. That one number is both the turnout and the majority at once, and it is a high wall because a no vote and an absent voter both count against a proposal. There is no second threshold to remember and no way to squeak a proposal through on a thin turnout.

Each track carries its own refundable deposit, the bond a proposer locks to open a vote, returned when the proposal passes and forfeited when it fails, and each carries its own voting period. The deposit is what makes an attack on a powerful track expensive in token terms, so a fall in price can never make capturing the chain cheap.

The Chain upgrades track is the most powerful track and the only one that can change the chain code, the governance configuration itself, or any chain parameter. Its deposit is 2,250,000 QTOV and it runs fourteen days. Every routine parameter change now lives here as well, there is no cheaper door to any chain setting.

The Mint QTOV track is the only way new QTOV is ever created after genesis. Because unlimited minting is the single most dangerous power on the whole chain it carries the highest wall of all. Its deposit is 4,000,000 QTOV and it runs three days. No single key can mint, only a passed public vote can, and there is no cap on the amount a passed vote may mint, that is a deliberate choice gated entirely by this high bar.

The Bridge pool migration track moves the bridge custody to a new vault on a security event. Its deposit is 1,500,000 QTOV and it runs five days.

The Freeze and asset recovery track ratifies the clawback of stolen funds back to the address they were taken from. Its deposit is 292,500 QTOV and it ratifies in about six hours. The freeze itself is instant and handled separately by the guardian caucus on the next block, this vote only confirms the return so a genuine theft can be reversed and honest users are never touched.

The Blacklist and kill address track retires a compromised or hostile address. Its deposit is 390,000 QTOV and it runs about two days.

Alongside the five tracks sits the emergency bridge freeze, which is a bonded action rather than a vote so that it can halt the entire bridge on the very next block in a crisis without waiting on any tally. The bond is 1,500,000 QTOV, the freeze lifts automatically after about a week or earlier if the depositor or governance lifts it, and the bond is always returned in full and is never slashed. A cooldown after a freeze lifts stops anyone posting the bond again and again to grief the bridge.

Two things that once had their own place here have been removed on purpose. There is no longer a separate cheap parameter track, so no chain setting can be turned on a low bar, every parameter change now clears the full Chain upgrades wall. And listing a new asset on the bridge is no longer a governance vote at all. Users add their own assets and seed their own liquidity directly in the Qbridge application by connecting a wallet, which is the normal self serve way and is safe precisely because every user listed asset is a foreign asset that can never vote, never stake, and never be mistaken for the native asset.
