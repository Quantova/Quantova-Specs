# The reduction record

This is a living record. It pairs every cryptographic operation in the stack with its exact reduction to a NIST standard. An operation without a reduction blocks release. It is updated in the same change as any new or changed crypto operation. This is the third layer of the four layer enforcement described in the crypto policy.

For each operation the record names the operation, the repository and module where it lives, the algorithm, the standard that defines that algorithm, the parameter set, the source of the vectors, and the status.

## Reductions in place

The SHA3 and SHAKE hashing lives in the crypto crate sha3 module and reduces to FIPS 202. It is validated against the FIPS 202 known answer tests.

The machine lattice signature lives in the crypto crate ml dsa module and covers key generation, signing, and verification for parameter set 65. It reduces to FIPS 204 and is validated against the official NIST vectors.

The key encapsulation lives in the crypto crate ml kem module and covers key generation, encapsulation, and decapsulation for parameter set 768. It reduces to FIPS 203 and is validated against the official NIST vectors.

The hash based signature lives in the crypto crate slh dsa module and covers key generation, signing, and verification for parameter set SHAKE 192 small. It reduces to FIPS 205 and is validated against the official NIST vectors.

The verifiable random function lives in the crypto crate vrf module. It composes the hash based signature and SHAKE, so it reduces to FIPS 205 for the signature and FIPS 202 for the hashing. It is validated by construction tests for determinism and for verification.
