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
