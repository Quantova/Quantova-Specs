# The reduction table

This is a living record. It pairs every cryptographic operation in the stack with its exact FIPS reduction. A row without a reduction blocks release. It is updated in the same pull request as any new or changed crypto operation. This is the third layer of the four layer enforcement described in the crypto policy.

For each operation the record notes the operation itself, the repository and module where it lives, the algorithm it uses, the standard that defines that algorithm, the parameter set, the source of the test vectors, and the status. Rows are added as code lands.
