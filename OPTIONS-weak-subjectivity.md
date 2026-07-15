# Weak subjectivity, the options

Status. This is a decision paper for the founder, not normative and not an implementation. It is the response to the open long range gap flagged in the validator economics, where no unbonding period can bound an attack whose evidence the attacker chooses when to reveal. A weak subjectivity checkpoint is the standard defence, and it is a consensus model addition, so it is the founder's to decide. Nothing is wired in. This paper states what a checkpoint binds, who signs it, how a node obtains one safely, what it costs the trustless story, and what other chains do.

## The problem, stated plainly

A long range attack works from stake that was once legitimate. An attacker who controlled a supermajority of the stake at some past height, or who acquired the keys of validators that have since unbonded and left, can build an alternate history forward from that height, sign it with those once valid keys, and present it later. The signatures verify, because those keys were real at that time. A node that starts from genesis, or that has been offline long enough, cannot tell the real chain from the forged one by the consensus rules alone, because both carry valid signatures from validators who were legitimate when they signed. The unbonding period does not close this, because the attacker reveals after the bond has unlocked and left, so there is nothing left to slash. This is not unique to Quantova, it is the fundamental weak subjectivity of every proof of stake chain, and it is why the honest position is to name it and defend against it rather than to claim it away.

## What a checkpoint binds

A weak subjectivity checkpoint is a recent trusted pair, a finalised block hash at a recent height, that a node accepts as its starting point instead of genesis. It binds one thing, that this hash is the canonical chain as of this recent height. From there the node follows the normal rules forward. Any presented history that does not descend from the checkpoint is rejected outright, no matter how well its old signatures verify, so an alternate history that diverges before the checkpoint is dead on arrival. An alternate history that diverges after the checkpoint would require equivocation by validators still bonded and therefore still slashable, which the base protocol already handles. So the checkpoint plus the unbonding period together close the gap that the unbonding period alone cannot.

## Who signs it

There are three shapes and the honest answer names which trust each one really rests on.

Social, out of band. The checkpoint is distributed the way the majors distribute it, shipped inside the client release, published on the project site and on independent explorers, and cross checked by the operator against several sources. Its trust is social, the operator trusts that a recent widely published hash is not a coordinated forgery, which for a value everyone can see and compare is a strong assumption in practice but is not cryptographic.

Governance signed. The chain's governance periodically signs a checkpoint on chain, so there is one authoritative recent hash carried by the protocol itself. This gives a node a single well defined source, but it does not remove the out of band step, because a brand new node still has to obtain the governance keys from somewhere it trusts, so it moves the social trust rather than removing it.

Validator set signed. The current validator set signs a recent checkpoint. This is circular for a node that does not yet know the validator set, which is exactly the node that needs the checkpoint, so it does not stand on its own and is useful only as a secondary confirmation.

The honest reading is that the root of trust for a syncing node is social and out of band under every option, because there is no purely cryptographic way to bootstrap trust in a proof of stake chain's recent state from genesis alone. The choice is how to make that social step as safe and as well defined as possible, and a governance signed checkpoint published widely and shipped with the client is the strongest version of it.

## How a new node or a long offline node obtains one safely

The safety turns on one number, the weak subjectivity period, which is roughly the unbonding period, because it is the span over which enough stake could have exited and stopped being slashable to mount a forgery. With a twenty one day base unbonding that period is about twenty one days.

A new node obtains a checkpoint no older than that period from the sources above, shipped with the client and cross checked against independent publishers, and syncs forward from it. It never trusts a single peer, it trusts a recent hash it verified against several public sources.

A node offline for less than the period can sync forward on its own without a new checkpoint, because the validators that were bonded when it went offline are still within their slashable window, so a forged fork in the gap is still punishable and the node's existing view is still a safe anchor.

A node offline for longer than the period cannot safely sync forward on its own, because validators bonded when it left may have exited and passed out of slashing range, so the gap is now forgeable. It must obtain a fresh checkpoint out of band, exactly as a new node does, before it trusts anything past its stale view. This is the rule that must be stated to operators plainly, a node that has been dark longer than the weak subjectivity period re anchors from a fresh checkpoint rather than trusting a peer's history.

## What it costs the trustless story

It costs the claim that a node can synchronise trustlessly from genesis. It cannot, and neither can any proof of stake chain. A syncing node relies on a recent trusted checkpoint obtained out of band, and that is a real and permanent qualification of the trustless story that must be stated honestly and never claimed away. It does not weaken the running chain, a synced node with a current view verifies everything forward with no trust, it qualifies only the bootstrap and the long dark node. The claims discipline should reflect it, the chain does not claim trustless sync from genesis, it claims trustless operation from a recent checkpoint, which is the accurate and the standard claim.

## What other chains do

Ethereum names weak subjectivity explicitly. Clients ship with recent checkpoints, checkpoint sync from a trusted state is the normal way to start a node, and the weak subjectivity period is computed from validator churn. Cosmos and the Tendermint family use a trusted block hash and height, state sync starts a node from a recent trusted height, and the unbonding period defines the safe window. Solana starts nodes from snapshots anchored to trusted validators. The common pattern across all of them is the same as the recommendation here, ship a recent checkpoint, define the safe period as the unbonding period, and require a node that has been offline longer than that to re anchor from a fresh one. None of them claims trustless sync from genesis, because none of them can.

## The recommendation, framed for the decision

The recommendation, which is the founder's to accept, is to adopt weak subjectivity checkpoints with the safe period set to the base unbonding period of twenty one days, to distribute the checkpoint shipped with the client and published on the project site and independent explorers for cross checking, and to add a governance signed on chain checkpoint as the authoritative source once governance exists. State in the claims discipline that sync is trustless from a recent checkpoint and not from genesis. The founder decides whether to add the governance signed checkpoint, how checkpoints are distributed, and the exact period, and only then is any of it wired in.
