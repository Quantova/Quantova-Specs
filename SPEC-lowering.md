# Lowering the Quanta language to bytecode

Status. This document is normative. It sits under the crypto policy and the language specification. If anything conflicts with the crypto policy, stop and report. It consolidates the lowering decisions so the code generator emits bytecode that stays aligned with every decision taken since the language brief, and it reopens nothing. The authoritative sources are SPEC-quanta-language.md, SPEC-primitives.md, SPEC-accounts.md, SPEC-isa.md, SPEC-cid.md, SPEC-container.md, SPEC-gateway.md, SPEC-token-standard.md, and SPEC-economics.md, and this document points to them.

## The primitive surface and what it lowers to

The typed primitives are the language surface over the machine opcodes. The code generator lowers each primitive to its opcode and never re implements a cryptographic operation in the language.

Q_Address is the SHA3 256 hash of the scheme identifier followed by the public key, rendered in Bech32m as a Q1 string, never as hexadecimal.

Q_Sig of T is a scheme agnostic signature fact. It lowers to the verify opcode chosen by the scheme identifier, described in the next section. A contract never names a scheme.

Q_Asset is a linear value. Its conservation is proven at type check, and that guarantee is preserved through lowering with no runtime substitute. Some asset kinds are origin tagged bridged kinds, described in the bridged asset section.

Q_Commit of T and Q_Rand lower to the SHA3 hash opcode and the verifiable random function verify opcode.

Q_Sealed of T is the sealed order primitive. The front running rejection depends on its presence, described in the invariants section.

Q_Key is a key handle. A private seed is never expressible in the language, and the code generator can emit no path that reads one.

Hashing is the SHA3 256 opcode only. No classical cryptographic operation is representable, and the code generator must be unable to emit one.

## Multi scheme signatures

Q_Sig of T stays scheme agnostic in the language, and the code generator dispatches on the one byte scheme identifier to the matching verify opcode. The value 1 is ML DSA and lowers to the module lattice verify MLDSA_VERIFY, and it is the default. The value 2 is SLH DSA and lowers to the hash based verify SLH_VERIFY. The value 3 is FN DSA and lowers to FNDSA_VERIFY, which stays behind the fn dsa feature flag and is off in every default build until the standard is final and audited. A contract never branches on the scheme.

The hard law is preserved through lowering. A user account may use any registered scheme, but validator attestations and the QORUS certificate use ML DSA only. The code generator must never emit a path that puts a non ML DSA verify into a consensus artifact. The term for ML DSA is module lattice.

## The inexpressible exploit invariants

The type checker makes all six classic exploit classes true compile errors. The code generator preserves each guarantee at the bytecode level and never reintroduces one.

No synchronous external callback is expressible, so reentrancy fails at name resolution, and the code generator emits no opcode sequence that reconstructs a synchronous call which returns control.

An unbounded addition or multiplication of external input into a stored integer has no lowering. The explicit checked form lowers to the checked arithmetic opcode that reverts on overflow, and the explicit wrapping form lowers to the modular operation. A bare unbounded additive or multiplicative operation has no lowering at all.

A competitive or pooled order that is not sealed does not compile, so the code generator relies on the sealed type being present and never stands a runtime guard in for it.

The remaining classes lower with no runtime check substituting for the compile time guarantee. If lowering a construct ever seems to need a runtime revert to enforce one of these guarantees, that is a signal the guarantee has regressed, so stop and flag it.

## Wide integer lowering

The surface type u128 is two 64 bit machine words, a low word and a high word, and the code generator lowers its arithmetic to two word sequences. An add carries the low word overflow into the high word, a subtract borrows across, and a multiply forms the low word product and the high word of that product with the widening high word multiply, then adds the two low cross terms into the high word. The checked form reverts to the shared trap when any bit of the full product lands above the wide range, which is a carry out of the high word, a high half of either cross term, or a nonzero high by high product, and the wrapping form takes the modular result, the same distinction as the one word arithmetic. Asset amounts are u128, so this lowering underlies every amount the machine moves. A wide value stores its low word in its slot and its high word in that slot lifted by the high offset.

## Event lowering

An emit lowers to the event instruction. The code generator marshals the operand values into the event payload region of scratch memory, a wide value as its low then high word and any other value as a single word, laid out in emission order, then records the event with its interface selector and the payload region. The selector is the SHA3 hash of the event's canonical signature string truncated to the selector width, the same rule as an entry selector. The machine records the event as an effect the host appends to the block event trie, so a fault records nothing and only a clean halt surfaces the events. The container format and the event payload are specified in the container specification.

## Origin tagged bridged assets

A bridged asset is identified by the pair of origin chain and origin asset, never by symbol alone, so the same symbol on two chains is two distinct kinds and qUSDT.eth is not qUSDT.tron. The code generator keeps these as distinct asset kinds and never unifies them. A bridged asset is never valid as validator stake, and the code generator emits that rule, since only native QTOV secures consensus.

## Issuer control token standard

The reference fungible standard needs these operations expressible and lowered correctly. Freeze and unfreeze, an evidence bound clawback, a quorum gated mint and burn, and an emergency pause, all under an issuer quorum of M of N post quantum signatures. The quorum check lowers to the multi scheme verify path, so it accepts ML DSA and SLH DSA members. The evidence binding is preserved as part of the clawback lowering, so an enacted clawback can never reach outside its bundle. Compliance hooks such as an allow and deny registry and a sanctions freeze are optional contract level opt ins, and each is post quantum signed.

## Fee representation

A fee is defined in United States dollars and charged in QTOV through the governance set rate, and it is capped at one tenth of one cent as a runtime invariant. The code generator for any fee touching contract references the protocol fee parameters and never hardcodes a raw QTOV fee, and it never emits a path that could charge above the cap.

## Alignment for the code generator milestone

The end to end milestone is a type checked contract lowered to bytecode with SHA3 selectors, packed into the container the interpreter loads, and run metered with a real meter number. That milestone additionally demonstrates the following. A multi scheme signature verify lowers to the correct opcode by scheme identifier. The checked form lowers to a revert on overflow while a bare unbounded addition has no lowering. A sealed order type lowers correctly. No classical cryptographic opcode is emittable. A conformance vector covers each of these. If any language construct cannot be lowered without a runtime check substituting for a compile time guarantee, or if any lowering path would require enabling the fn dsa flag in a default build, stop and flag it to the founder.
