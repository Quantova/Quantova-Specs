# The primitive catalog

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

This document defines the primitive types that the language and the machine share. Each type maps to a backing machine instruction and to a clear gas rationale, and each is defined so that a value of the type can only be produced in the sanctioned way.

## The address type

An address names an account or a contract. It carries the address payload defined in the accounts specification and renders in the Q1 format. It is compared by value.

## The signature type

A signature type binds a signed message to the party that signed it. A value of this type can only be produced by the machine verifying a lattice signature over exactly that message. A contract cannot forge one, because there is no way to construct the type except through verification. This is what makes an unchecked authority impossible to express.

## The asset type

An asset value carries an amount of a declared asset kind and is linear. A linear value must be used exactly once. Copying it or dropping it is a compile error, which is what makes a double spend and a lost balance impossible to write. An asset can be split into two, merged with another, and sent. Some asset kinds are origin tagged, meaning they record the foreign chain they were bridged from, and an origin tagged asset is not valid as validator stake.

## The commitment type

A commitment binds a hidden value to a public digest using SHA3, so a party can commit now and reveal later without being able to change the value.

## The randomness type

A randomness value is a verified output of the verifiable random function. It can only be produced by the machine verifying a random output and its proof, so a contract cannot forge randomness.

## The sealed type

A sealed value is confidential in the mempool. It is carried under key encapsulation and is opened only at execution, which gives protection against front running without breaking any conservation or audit.

## The key type

A key value holds a public key with its scheme identifier byte, so the machine knows which scheme to use when it verifies. The secret half is never a value inside a contract.
