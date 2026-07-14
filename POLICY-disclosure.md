# POLICY-disclosure.md — Private → Public Wave Schedule

**Status:** Normative. Governs when each repository moves from private to public.

All repos start **private**. Write every commit as if the repo goes public tomorrow — no secrets, no
throwaway comments, no internal-only language. Disclosure happens in waves; nothing goes public ahead
of its wave.

## Waves

1. **Specs public first.** `Quantova-Specs` (POLICY-*, SPEC-*, AUDIT-pq-independence, HANDOFF)
   discloses first, so the fixed target is auditable before implementations are.
2. **Trust-critical core at devnet.** `Q-Crypto`, `QVM`, `Quanta-Smart-Contract-language`,
   `QRC-CONSENSUS`, `Quantova-Conformance`, and `Quantova-Bench` disclose when the local 4-node
   devnet finalizes blocks (build order Step 9).
3. **Everything by public testnet.** Remaining repos disclose by the public testnet (Step 11).

## Blocking conditions (per PART 7 of HANDOFF)

- **Naming/trademark collision** (Quantova / QTOV / QVM vs. any existing project) must be resolved by
  the founder **before any public wave**. Private development is unaffected.
- Legal review of staking / delegation / migration is required before public testnet.
- External audits (Q-Crypto, QVM, Quanta, QORUS) are booked and running before public testnet, and
  closed before mainnet.

## Rule

Moving a repo from private to public is a founder decision, gated on the wave above and the blocking
conditions. An contributor never flips visibility on its own.
