# The one time key sortition

Status. This is the design of option C from OPTIONS-sortition-uniqueness.md, adopted after option A was measured and rejected for a seven minute lookahead. It is presented for the founder to confirm the enforcement and the numbers before it is built, because the binding is the whole security. It stays inside the NIST only law, the construction is hash based. No optimisation of finality or throughput begins until this lands and the grinding budget is a measured number.

## Why the old draw was grindable and why this is not

The old sortition signs the beacon with the validator key and hashes the signature. A module lattice signature is not unique, a signature made with a different randomizer over the same key and input verifies equally and gives a different output, and a verifier cannot tell them apart from the encoding, so a validator re signs until the output lands under its threshold. This design removes the re sign entirely by making the output a deterministic hash of a committed value rather than a signature, so there is nothing to re roll.

## The mechanism

Each staking account commits, at registration and bonded to its stake, the Merkle root of a tree of one time preimages, one leaf per slot it will serve. The root is on chain in the stake registry. For slot N the account's sortition output is the hash of its preimage at position N together with the beacon of slot N, and the credential it presents is that preimage with its Merkle path to the registered root. The account is a committee member when the output falls under a threshold set by its stake, exactly as before.

Verification accepts the credential only when the revealed preimage sits at position N in the account's registered root, the Merkle path checks, and the recomputed output matches. A preimage at any other position, or against any root not in the stake registry, is rejected.

## The enforcement and the budget as a number

The output for a slot is a deterministic hash of one fixed preimage and the beacon, so there is no randomizer to vary and nothing to re roll. The preimage for slot N is fixed by position, the account cannot substitute another because the verifier checks the position, so the account has exactly one valid draw per slot. And the whole tree is committed at registration, before the beacon of any of its slots is known, so the account could not have chosen preimages for favourable future outputs, the beacon is unpredictable when the root is fixed.

So the grinding budget is one draw per bonded staking account per slot. Not a loose bound, one deterministic output at no compute advantage.

The only path to a second draw for a slot is a second bonded account with its own registered root, which costs a second full stake bond, locked, unbonding delayed, and at slashing risk. So a draw is not free, it costs a bond. An attacker with stake S can hold at most S divided by the minimum stake accounts, and so that many draws per slot, but each account holds a fraction of S and its selection threshold is that same fraction, so the expected committee share of the split equals the committee share of the whole stake held as one account. Splitting buys draws but not expected seats, the sortition is stake weighted and splitting is stake neutral.

## The one place that needs care, leadership

Committee membership is stake neutral under splitting because the thresholds scale with the split stake. Leadership is the committee member with the lowest output, and more independent draws give more chances at a low output, so the leader rule must be made stake neutral under splitting rather than assumed to be. The weighting that decides leadership must be such that holding stake as one account and as many give the same leadership probability, the way a sub weight model does, so a validator cannot buy a better leadership chance by splitting into many small bonded accounts. This is a stated design requirement of C, and it carries a conformance vector that splitting does not raise the measured leadership probability.

## The slashing that backs the bound

C is a bound rather than an impossibility, and the bound holds only if the penalty for cheating within an account exceeds any gain, so the slashing is part of this design and is decided with it. Two draws revealed for one slot from one account, or a preimage used out of its position, are detectable from the chain and are slashed on the same footing as equivocation, a hard slash of the account's bond. The gain from cheating is at most one committee seat or one leadership, whose marginal value is the reward for a single attestation or a single proposal among a committee of about five hundred, tiny against a full stake bond. So the penalty exceeds the gain by orders of magnitude. The decided fractions are in the economics spec, one hundred percent of the bond and a permanent ban for these attributable faults.

## Withholding, an accepted residual

One thing is not preventable and is not an attack, so it is written here as known and accepted. An account draws its one output for a slot, sees that it is above its threshold and unfavourable, and simply reveals nothing. This is invisible, there is no evidence of it on chain, and it is unpunishable. But it costs the account a seat it could have taken rather than gaining it one, so it forfeits participation rather than stealing it, and it does not bias the committee toward the withholder. It is recorded as an accepted residual of the design, not a hole to be closed.

## What this means for the uniqueness theorem

The uniqueness the sortition needs no longer comes from a unique signature, that claim is dead, a module lattice signature is not unique and a verifier cannot check derandomization from its encoding. It comes from a protocol level bound, one committed preimage per account per slot, so one output, enforced by position and by the registration being fixed before the beacon. So the theorem is restated honestly. Uniqueness of the sortition output holds by the one draw per account per slot rule and the stake neutrality of splitting, backed by slashing, not by a property of the signature, and the residual is the bounded and slashed cost of an extra bonded account rather than an impossibility. No stronger claim than this is made in the specification or the paper.

## What is measured before it is called done

A conformance vector that a second draw for a slot from one account is rejected, and that a preimage out of position is rejected, so the budget of one is a tested fact and not an assertion. A conformance vector that splitting stake across accounts does not raise the measured committee share or the measured leadership probability. The grinding budget stated as the measured number one draw per bonded account per slot, with the cost of an extra draw being a full stake bond at slashing risk.
