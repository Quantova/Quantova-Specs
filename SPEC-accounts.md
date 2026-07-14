# Accounts and addresses

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

## Security floor

Address and key payloads are sized by security, not by style. The minimum payload is 192 bits, which gives a 96 bit quantum preimage margin. The canonical payload is 256 bits, which gives a 128 bit margin. There is no six character key and no six character address. A short key is a broken key. This constraint is permanent, and no future pressure shortens it.

Identity has three layers. The human layer is a name in the Quantova Name Service, which is short and readable. The name resolves to a Q1 address, which is a hash and the on chain identifier. The address is derived from a quantum keypair, which stays hidden and is derived from a 32 byte seed.

## The Q1 address

An address is the Bech32m encoding of a payload, using the human readable part q, where the payload is the SHA3 256 hash of the scheme identifier followed by the public key. Because the character after the human readable part is the natural Bech32 separator, every address reads as q1 by construction.

There are two sanctioned tiers. The canonical tier uses a 32 byte payload and is about 60 characters. It is the default. The compact tier uses a 24 byte payload and is about 47 characters, with a 96 bit quantum margin, and is optional. Both tiers are Bech32m, checksummed, and error detecting.

## The Q2 secret

A private key export uses the human readable part q2 and encodes the 32 byte master or account seed as Bech32m. The same secret is also expressible as a 24 word mnemonic that uses Quantova derivation based on SHAKE256. It is not BIP39 and not BIP32, and there is no secp256k1 derivation path anywhere.

## The key generation pipeline

The pipeline is frozen, fully post quantum, and lives entirely in the qtv crypto crate. It runs as follows. The operating system random source produces a 32 byte master seed. The account seed is SHAKE256 over the master seed followed by the scheme identifier followed by the index. The lattice key is produced by ML DSA key generation from the account seed as defined in FIPS 204. The address is the q1 encoding of the SHA3 256 hash of the scheme identifier followed by the public key.

The stored secret is the 32 byte seed. The full lattice key is expanded at signing time and is never stored, displayed, or exported as the user key. This is the layered structure. A short seed and a short address sit on top, and the large quantum key sits beneath, never seen.

## Performance

Bulky public keys appear only inside the signature envelope. The wrapper verifies them off the hot path, and the data availability store archives them. Consensus never sees a lattice key. The length of an identity has no effect on block finality or on throughput.

## Scheme agility

A scheme identifier byte precedes every key. The Q format is stable across future NIST schemes, so the address and export forms do not change when a new scheme is added.

## The scheme identifier registry

A one byte scheme identifier prefixes every key and every signature, and verification dispatches on it. The registry is fixed. The value 0x01 is ML DSA 65 and is the default. The value 0x02 is SLH DSA. The value 0x03 is FN DSA, which is pre final and stays behind the fn dsa feature flag, off in every default build until the standard is final and audited. A contiguous range of values above these is reserved for other parameter sets of the same schemes, for example ML DSA 44 and ML DSA 87, so a future set never renumbers an existing one. The address is the SHA3 256 hash of the scheme identifier followed by the public key, so the address does not reveal the scheme. The signature envelope carries the scheme identifier, and a verifier reads it and runs the matching verification.

## Scheme choice is per account and never per consensus

A user chooses a signature scheme per account at signup, and a user account and its transactions may use any registered scheme. This choice never reaches consensus. Validator attestations and the aggregated finality certificate use ML DSA only. A worker must never wire SLH DSA or FN DSA into a consensus attestation or the certificate, because mixing schemes in the hot path complicates the prover and the certificate for no benefit and would put finality and throughput at risk. This law is written here and in the consensus specification so it is not crossed by accident.

## Wallet framing

The wallet presents the scheme choice by property, not by raw name, with a default. ML DSA is the recommended balanced default. SLH DSA is the most conservative choice and has larger signatures. FN DSA has the smallest signatures but uses a standard that is still being finalized, and that is disclosed at the point of choice. The competition names belong to the wallet display only, and the FIPS names are used everywhere in the code, the specifications, and the identifiers.
