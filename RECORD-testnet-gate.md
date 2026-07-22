# The testnet gate

Nothing reaches testnet without the founder's explicit word. Not the chain, not a
public node, not an announcement, not a firewall change, nothing. This is a
standing rule, written here so it is not assumed and not inferred from any progress
report. A thing working is not a thing cleared to ship. The word is a separate act
and only the founder gives it.

## The honest line between what exists and a testnet

This is the founder's own line, recorded so the distance is never blurred by a
green build or a passing test.

Bonded staking does not exist. The validator set is written into genesis rather than
bonded. Validator keys are derived from the numeric id, so whoever holds the genesis
controls every validator, which is why the daemon refuses to start without the
development flag. There are no rewards. There is no delegation. Slashing never
fires. The fee is now capped in QTOV by a native ceiling and targeted in USD, not
capped in dollars, because a dollar cap needs a price feed the chain refuses, so a
stale rate can drift the realized dollar cost above the target, recorded in
RECORD-fee-cap.md. Congestion is only partly built, the free bid is fixed by pay
what you bid inside the band, but the saturation lever, required meter and in time
stake weighted priority, is not built and the second waits on bonded staking. The
deploy path has no compiler provenance check, and none is load bearing until a
deploy path exists, but it must be met before that surface opens. Storage growth is
priced and uncapped, with a refundable deposit and a per transaction slot cap the
chosen instrument, not yet built. QTE, the encrypted mempool, is research and not
engineering at the committee sizes the chain runs.

None of this is a fault of the build. It is where the stack is. The gate holds until
the founder decides each of these is where he wants it, one at a time, and says so.
