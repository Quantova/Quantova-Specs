# Costing A7, the twenty percent threshold, against the trust it shares with byzantine trusted validity

Status. This is the security analysis behind a decision, not normative, and there is no measurement, it is a reasoning about assumptions. A7 is costed here not on its own but against the byzantine trusted validity of the availability path, because the founder asked for the combined assumption rather than two separate ones, and because both are security reductions that concentrate trust in the same sampled committee.

Decision. A7 is not taken, decided by the founder on this costing. It is recorded as a decision with its reasoning in SPEC-consensus-qorus.md so it is not reopened the next time a faster number is wanted. If it is ever revisited, the formal model is re checked at the one fifth threshold before any protocol code changes.

## The two reductions, named plainly

A7 lowers the fault tolerance of the consensus from below one third to below one fifth. It buys faster finality because a smaller quorum finalises, and it costs safety margin, because safety now breaks when committee corruption passes one fifth rather than one third.

Byzantine trusted validity lets a non executing validator accept the committee's certificate as proof the block is valid, instead of re executing and checking the state root itself. It buys the download saving of the availability path, and it costs the independent re execution that today catches a committee which produces an invalid block. It is a security reduction, not an optimisation, and that is the point the founder made.

## What we have today, the baseline both reductions erode

Today there are two independent lines and they protect different things.

Safety, meaning the chain never finalises two conflicting blocks at one height, holds while fewer than one third of the committee is corrupt. This is the quorum intersection argument, two two thirds quorums always share an honest member.

Validity, meaning an honest node never accepts an invalid state transition, holds absolutely and independently of the committee, because every honest full node re executes every block and rejects an invalid one. A committee that is entirely corrupt cannot make an honest node accept invalid state, the worst it can do to validity is halt the chain when honest nodes refuse to follow it. One honest re executor anywhere is enough to protect validity for itself, and the network is full of them. This is defence in depth, safety rests on the one third threshold and validity rests on universal re execution, and the two do not depend on each other.

## A7 on its own

A7 drops the safety threshold to one fifth. An adversary that controls one fifth of the committee can finalise two conflicting blocks, where today it needs one third. Because the committee is sampled from the active set, the tolerable global adversary is somewhat below one fifth, not exactly one fifth, since a sample drawn from a global fifth exceeds a fifth about half the time, so A7 needs real margin under twenty percent to be safe across sampled committees, and that margin is tighter and less forgiving than the same margin under one third. Validity is untouched by A7 alone, re execution is still there. What A7 buys is a smaller quorum, roughly three fifths where today it is two thirds, and therefore faster finality. What it costs is thirteen points of safety margin and a tighter sampling margin on top.

## Byzantine trusted validity on its own

This does not change a threshold, it removes a line. Validity stops being verified by everyone and becomes trusted from the committee. To make non executing validators accept an invalid block, an adversary needs the quorum of the committee to attest to it, and since honest committee members re execute before they attest and will not sign an invalid block, that means the quorum of the committee must be corrupt, above two thirds today. That is a high bar on its own, higher than the safety bar. But the loss is not the number, it is that the re execution backstop is gone, so a committee that does reach that bar, by corruption or by an unlucky sample, is no longer caught by the rest of the set. Today such a committee is caught by any honest re executor. Under trusted validity it is not caught at all.

## The two together, the combined assumption

This is what the founder asked for, and it is worse than either alone because both reductions fall on the same sampled committee and they compound.

The combined assumption we would rest on is that a sampled committee of about five hundred is trustworthy for both things at once, for safety at a one fifth threshold, and for validity with no independent check behind it. The committee stops being a fast finality mechanism backed by a wider set and becomes the sole holder of both guarantees.

What an adversary needs to break it. To fork the chain, one fifth of the committee, down from one third, and with the sampling margin that is a global stake somewhat below twenty percent. That alone is the safety cost. The compounding is what byzantine trusted validity adds to it. Today, an adversary that reached the safety threshold and forced a fork would still be checked on validity by universal re execution, so it could halt or fork but it could not push invalid state into honest nodes. Remove re execution and that second check is gone, so the same adversary that now needs less stake to break safety also faces no independent audit of what the committee accepts. The bar to subvert the committee goes down and the consequence of subverting it goes up, in the same change, on the same body.

What we lose that we have today. Thirteen points of safety margin, and the guarantee that one honest re executor protects validity. We would be replacing validity is verified by everyone with validity is trusted from a committee that a fifth can subvert. The defence in depth that makes today's chain survive a bad committee, safety on one threshold and validity on a separate universal check, collapses into a single point, the committee, made both easier to subvert and more powerful in the same move.

## The honest verdict

Taken together they concentrate too much trust in the committee, and I will say it as plainly as the erasure coding result. Each reduction alone is a defensible trade for its own gain. Together they make a sampled fifth subvertible committee the sole holder of both safety and validity, which is exactly the concentration to avoid, and the compounding is not additive, the same stake buys a fork more cheaply and an unaudited state at once.

So the recommendation is do not take both. If throughput is the goal, the availability path should use proven validity, which is trustless and keeps validity independent of the committee, rather than byzantine trusted validity, and A7 should not be stacked on a validity reduction. If proven validity is not affordable, and today it is not, then the honest choice is to keep universal re execution and the one third threshold and accept the slower chain. That is the founder's stated preference and on these assumptions it is the correct one. A faster chain that asks the world to trust a fifth of the validators for both safety and validity is a worse chain than a slower one that re executes, and the numbers here support that judgement rather than merely restate it.

## Status of A7

A7 is a consensus safety parameter and it is escalated, not taken. If it is ever approved it needs the formal model re checked at the one fifth threshold before activation, and this costing recommends against approving it in combination with byzantine trusted validity. The sortition gate stays closed and nothing here is a performance claim.
