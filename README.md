# V1 - Age proof

We want to create a circuit that allows one to generate a proof to show that the age is above a certain age. We can generalize that to 3 variables, `birthYear`, `comparisonYear` and `minimumDifference`.

With that, we can check that one born in `1990` can enter a night club in the year `2020` that requires the legal age of `21`. This circuit can also be reused with other age limitation and on different years.

## Getting started

```sh
zokrates compile -i root.zok
zokrates setup
zokrates compute-witness -a 1990 2020 21
zokrates generate-proof
zokrates export-verifier
```

## Comments

With this approach, we can simply pass in two public variables, the `comparisonYear` and `minimumDifference` into the contract and provide the proof.

The circuit can indeed be used to compute the truthiness of the claim but the prover can send in arbitrary numbers as the `birthYear`. We need to extend this so that this circuit does not only check that the prover "knows of a birth year that satisfy the constraint" but rather "has the birthYear that satisfy the constraint".

To do that, we require pre-commitment of a digest of the birth year by a trusted third party (perhaps the government), without revealing the actual birth year.
