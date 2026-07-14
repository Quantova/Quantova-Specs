# The Quanta language

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report. It is implemented by the Quanta smart contract language repository. The prose in this document is plain, and the code blocks use the real language syntax.

## Identity

Quanta is the Quantova smart contract language. Security is declared in the entry signature and proven by the compiler before a contract can exist. Quanta is familiar to a Solidity developer but shares no lineage with Solidity. Source files carry the extension qs and compile to a bytecode container with an embedded interface descriptor. There is no ecrecover and no elliptic curve escape hatch, because the machine has only post quantum operations.

## Forbidden tokens

These Solidity tokens are not tokens of Quanta, and their appearance in the grammar or in source is a violation.

```
function   require   constructor   msg   msg.sender   mapping
uint   uint256   ecrecover   pragma solidity   wei   ether
```

## Numeric types

The unsigned types are u8, u16, u32, u64, and u128, and the signed types run i8 through i128. There is no uint256 and no 256 bit surface integer, because the machine computes in 64 bit words. Asset amounts are u128 inside the asset primitive, so a developer never picks a width for money. Integer width has no bearing on quantum security, since the keys and hashes and signatures are all 256 bit class NIST primitives.

Checked arithmetic is the default, so an overflow reverts. A stored integer that grows by unbounded external input can revert forever once it saturates and strand the contract, so a bare addition of unbounded external input into a stored integer is a compile error, and the author states the intended behavior in one of three explicit ways. A limits clause that bounds the result proves the addition cannot overflow and needs no further mark. The explicit form checked of an expression, written checked(a + b), keeps the revert on overflow and records that the author accepted it. The explicit form wrapping of an expression, written wrapping(a + b), takes modular arithmetic for the rare intended case. A safe bounded counter stays clean to write through any of these, while an unacknowledged unbounded addition cannot compile.

## Keyword register

The keywords are contract, entry, guard, genesis, caller, state, invariant, event, emit, asset, let, signed by, sealed, Quorum, after, from, mints, burns, conserves, writes, reads, limits, denies, checked, and wrapping. The divergences are deliberate. It is entry and not function, guard and not require, genesis and not constructor, caller and not the sender field, and state blocks and Map and Registry and not mapping.

## The five signature constructs

Each construct is a fact declared in the signature and proven at compile time or enforced by a native opcode.

signed by. A parameter written `p: T signed by owner` has the effective type `Q_Sig<T>` and desugars to a `MLDSA_VERIFY` over the exact bytes of T before the body runs, so unauthorized data cannot be constructed.

conserves. An entry clause `conserves ASSET` runs a linear flow analysis over the intermediate form and proves on every path that asset in equals asset out plus explicit fees. A leaking or minting path is a compile error that names the line.

invariant. A contract level `invariant Expr;` inserts a gas metered check of Expr into the epilogue of every state writing entry, and a violation reverts.

Quorum and after. A parameter of type `Quorum<M of N, set>` is constructed by the machine only from M valid guardian signatures, and an entry clause `after 7 days from anchor` compiles to a consensus time guard.

sealed. A parameter modifier `p: sealed T` routes the argument through `MLKEM_ENCAP` so it is confidential in the mempool and is opened only at execution. An entry that pools a contributed asset into contract state for later settlement, does not pay out in the same call, and decides the outcome on a field of an order parameter must take that order sealed. A pooled competitive order carried in the clear is a compile error that names the parameter, so front running of order flow is not merely blunted but inexpressible.

## Grammar

```
program        = { import } , { contract } ;
import         = "import" , "{" , ident , { "," , ident } , "}" , "from" , string , ";" ;

contract       = "contract" , ident , "{" , { contract_item } , "}" ;
contract_item  = asset_decl | state_block | genesis_block | invariant_decl
               | entry_decl | event_decl ;

asset_decl     = "asset" , ident , ";" ;
state_block    = "state" , "{" , { field_decl } , "}" ;
field_decl     = ident , ":" , type , [ "=" , expr ] , ";" ;
genesis_block  = "genesis" , "{" , { statement } , "}" ;
invariant_decl = "invariant" , expr , ";" ;
event_decl     = "event" , ident , "(" , [ params ] , ")" , ";" ;

entry_decl     = "entry" , ident , "(" , [ params ] , ")" , { clause } , block ;
param          = ident , ":" , [ "sealed" ] , type , [ "signed" , "by" , ident ] ;
clause         = "writes" , "(" , idents , ")"
               | "reads"  , "(" , idents , ")"
               | "conserves" , ident
               | "mints" , ident
               | "burns" , ident
               | "limits" , expr
               | "denies" , expr
               | "after" , after_target , [ "from" , expr ] ;
after_target   = duration | expr ;

block          = "{" , { statement } , "}" ;
statement      = guard_stmt | let_stmt | emit_stmt | assign_stmt | expr_stmt ;
guard_stmt     = "guard" , expr , ";" ;
let_stmt       = "let" , ident , "=" , expr , ";" ;
emit_stmt      = "emit" , ident , "(" , [ args ] , ")" , ";" ;
assign_stmt    = lvalue , ( "=" | "+=" | "-=" ) , expr , ";" ;

checked_expr   = "checked" , "(" , expr , ")" ;
wrapping_expr  = "wrapping" , "(" , expr , ")" ;

type           = ident , [ generic_args ] ;
generic_args   = "<" , generic_arg , { "," , generic_arg } , ">" ;
generic_arg    = mofn | type | int_lit ;
mofn           = int_lit , "of" , int_lit ;
```

## Desugaring

The construct `signed by` lowers to `MLDSA_VERIFY` then binds the value. The clause `conserves` lowers to a compile time linear check with no runtime opcode. The clauses `mints` and `burns` authorize a supply change only inside the marked entry. An `invariant` lowers to an epilogue that evaluates the expression and reverts on false. A `Quorum` lowers to a set of `MLDSA_VERIFY` checks and constructs the value with a digest of the approvers. An `after` lowers to a consensus time guard. A `sealed` parameter lowers to `MLKEM_ENCAP` at submission and its opening at execution. An `emit` appends a typed event to the event trie. A selector is the SHA3 hash of the canonical signature string.

## Normative examples

These three contracts are normative and must parse against the grammar with no ambiguity.

```
import { Q_Asset, Q_Sig } from "quantova/primitives";
contract Vault {
  state {
    owner: Q_Address;
    reserve: Q_Asset<QTOV>;
    daily_cap: u64 = 50_000;
    spent_today: u64;
  }
  genesis {
    owner = deployer;
  }
  invariant spent_today <= daily_cap;
  entry withdraw(req: WithdrawReq signed by owner)
    writes(reserve, spent_today)
    conserves QTOV
    limits req.amount <= daily_cap - spent_today
  {
    guard reserve.amount >= req.amount;
    let payout = reserve.split(req.amount);
    spent_today += req.amount;
    send(req.to, payout);
    emit Withdrawn(req.to, req.amount);
  }
  event Withdrawn(to: Q_Address, amount: u128);
}
```

```
import { Q_Asset, Quorum } from "quantova/primitives";
import { Registry } from "quantova/stdlib";
contract SovereignStable {
  asset QSGD;
  state {
    issuance_board: GuardianSet<5>;
    monthly_ceiling: u128;
    issued_this_month: u128;
    sanctions: Registry<Q_Address>;
  }
  genesis {
    issuance_board = deploy_params.board;
    monthly_ceiling = deploy_params.ceiling;
  }
  invariant issued_this_month <= monthly_ceiling;
  entry issue(order: IssuanceOrder, approvals: Quorum<3 of 5, issuance_board>)
    mints QSGD
    writes(issued_this_month)
    limits order.amount <= monthly_ceiling - issued_this_month
  {
    issued_this_month += order.amount;
    send(order.treasury, mint(order.amount));
    emit Issued(order.amount, approvals.digest);
  }
  entry transfer(funds: Q_Asset<QSGD>, to: Q_Address)
    conserves QSGD
    denies sanctions.contains(to)
  {
    send(to, funds);
  }
  entry rotate_board(new_set: GuardianSet<5>,
                     approvals: Quorum<4 of 5, issuance_board>)
    writes(issuance_board)
    after 48 hours from approvals.first
  {
    issuance_board = new_set;
    emit BoardRotated(approvals.digest);
  }
}
```

```
contract TreasuryBond {
  asset BOND_2036;
  state {
    registrar: Q_Address;
    holders: Registry<Q_Address>;
    maturity: Time = 2036-07-01;
    coupon_bps: u16 = 425;
  }
  entry subscribe(order: SubscriptionOrder signed by registrar,
                  payment: sealed Q_Asset<QSGD>)
    conserves QSGD
    mints BOND_2036
    denies !holders.contains(order.investor)
  {
    treasury.merge(payment);
    send(order.investor, mint(order.units));
    emit Subscribed(order.investor, order.units);
  }
  entry redeem(units: Q_Asset<BOND_2036>)
    burns BOND_2036
    conserves QSGD
    after maturity
  {
    guard treasury.amount >= units.amount * face_value;
    let principal = treasury.split(units.amount * face_value);
    send(caller, principal);
    emit Redeemed(caller, units.amount);
  }
}
```

## Exploit suite

Every classic exploit class is a compile error, and the compiler test suite asserts that each fails to compile for its own reason. Reentrancy cannot be written because there is no synchronous external call expression, so an attempt to call out and regain control does not parse. An unchecked overflow cannot be written because a bare addition of unbounded external input into a stored integer is rejected, and the author must bound the result with a limits clause or take the explicit checked or wrapping form. A forged authorization is rejected because authority comes only from a signed value produced by a real machine verification, so gating on an unsigned parameter field does not compile and ecrecover does not exist. An infinite mint is rejected because supply changes only on the declared and quorum gated path that conservation proves. A dropped or locked value is rejected because an asset is linear and must be consumed on every path. Front running of order flow is rejected because a pooled competitive order must be sealed, so a visible competing amount cannot be expressed. Safe by construction is total here, all six classes fail to compile rather than relying on a runtime revert or a developer choice.

## Conformance

The three contracts parse against the grammar with no ambiguity. Each exploit maps to the mechanism that makes it inexpressible or a compile error. This document is merged before any compiler code depends on it.
