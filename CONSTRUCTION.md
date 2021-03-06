# V1 - Age proof

We want to create a circuit that allows one to generate a proof to show that the age is above a certain age. We can generalize that to 3 variables, `birthYear`, `comparisonYear` and `minimumDifference`.

With that, we can check that one born in `1990` can enter a night club in the year `2020` that requires the legal age of `21`. This circuit can also be reused with other age limitation and on different years.

## Getting started

```sh
zokrates compile -i ageCheck.zok
zokrates setup
zokrates compute-witness -a 1990 2020 21
zokrates generate-proof
zokrates export-verifier
```

## Comments

With this approach, we can simply pass in two public variables, the `comparisonYear` and `minimumDifference` into the contract and provide the proof.

The circuit can indeed be used to compute the truthiness of the claim but the prover can send in arbitrary numbers as the `birthYear`. We need to extend this so that this circuit does not only check that the prover "knows of a birth year that satisfy the constraint" but rather "has the birthYear that satisfy the constraint".

To do that, we require pre-commitment of a digest of the birth year by a trusted third party (perhaps the government), without revealing the actual birth year.

# V2 - Age Commitment Proof

We want to allow a trusted third party to commit a digest of the `birthYear` without actually revealing it. To do that, we can simply publish a hash of the birthYear plus a random salt.

This means, without the knowledge of both the birthYear and the random salt, one will not be able to generate the same hash.

The inputs to the verifier will then be:

birthYear - 128 bit
rand1 - 128 bit
rand2 - 128 bit
hash1 - 128 bit
hash2 - 128 bit

where (rand1 + rand2) is the 256 bit of the _private_ random salt
and (hash1 + hash2) is the 256 bit of the _public_ hash

## Getting started

```sh
zokrates compile -i birthYearCheck.zok
zokrates setup
zokrates compute-witness -a 1990 123 123 233770742581153321658241226707865859106 161843013006795803511295431198461913882
zokrates generate-proof
zokrates export-verifier
```

## Comments

With the pre-commitment from a trusted third party, we can now merge these two concepts to build a age prover using a commitment from a trusted third party.

# V3 - Complete Implementation

With the two circuits in place, we can now combine both circuit to create the final verifier.

## Getting started

```sh
zokrates compile -i complete.zok
zokrates setup
zokrates compute-witness -a 1990 123 123 233770742581153321658241226707865859106 161843013006795803511295431198461913882 2020 21
zokrates generate-proof
zokrates export-verifier
```

## Comments

The age verification contract is complete when the two circuits are combined.

Public Inputs (Agreement between prover and verifier):

- birthYear hash (`hash1` & `hash2`)
- current year, in this case 2020 (`comparisonYear`)
- minimum age, in this case 21 (`minimumDifference`)

Private Inputs (Known by prover but not revealed to verifier):

- actual birth year (`birthYear`)
- random salt appended to unmasked birthYear (`rand1` & `rand2`)
