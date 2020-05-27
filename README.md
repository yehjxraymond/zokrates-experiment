# Rationale

This experiment aims to apply Zero-Knowledge Proof to verify that a person is above the legal age, based on his government issued document, without revealing the actual age of the subject.

## Quick Demo

Simon has been issued his ID with evidence of his birth year by his government. The evidence field has the value `0xAFDE9B79B5468DF38FEE34234F56742279C1D49BE864FA63C491A8A4335FEB1A`.

With that value, we want to prove that Simon is above the legal age of 21, without revealing his actual age.

At the convenient store, the cashier wants to verify that Simon of legal age before he can buy alcohol. So the cashier wants Simon to prove that he is above age `21` in the year `2020` (let's assume the cashier isn't sticky with month).

To provide the proof, Simon generate a proof with following public parameter:

- Evidence of age: `0xAFDE9B79B5468DF38FEE34234F56742279C1D49BE864FA63C491A8A4335FEB1A`
- Year to compare: `2020`
- Minimum age: `21`

Together with the public parameters, Simon also used some secret parameters only known to him to generate the proof:

- Birth year
- Secret salt

Once the proof has been generated, Simon passes the following proof to the cashier:

```json
{
  "proof": {
    "a": [
      "0x1b44a56bbf034807a1b94fa735c57baf29784e24f2e971740f382e42f5f5be90",
      "0x30389fbb44d2623a55720e52b1d275ccb5661d8539ced3702fc08e57e80704c3"
    ],
    "b": [
      [
        "0x1abe0f5cafe9d9d8a99e2527d94eb5f35e72d9c5d9ce2be38a5c8e209a3ef3e5",
        "0x015186049e67fbb9feebd4a12a0e06d498323e0dfd4aa69382b7129774d6f842"
      ],
      [
        "0x1b5e54bf4fc76151b1d2fc8e29dbe1c9a40ac62b75e78ba0b8b72cc1ff06d092",
        "0x1bbcfb81a298a28db69db48342dd949c01ccf0fad3d0b2451bf7471954fe7518"
      ]
    ],
    "c": [
      "0x00d6cbcbf5fc2f146c81e97a7a09320db46e5417f7a086876147f07590560d51",
      "0x0e5526ca98c59d46eb1e30a1648215695c3dca871503538b168ec49d741a141c"
    ]
  },
  "inputs": [
    "0x00000000000000000000000000000000afde9b79b5468df38fee34234f567422",
    "0x0000000000000000000000000000000079c1d49be864fa63c491a8a4335feb1a",
    "0x00000000000000000000000000000000000000000000000000000000000007e4",
    "0x0000000000000000000000000000000000000000000000000000000000000015",
    "0x0000000000000000000000000000000000000000000000000000000000000001"
  ]
}
```

From the inputs, we can see the following:

1. First half of the evidence (`0xAFDE9B79B5468DF38FEE34234F567422`)
1. Second half of the evidence (`0x79C1D49BE864FA63C491A8A4335FEB1A`)
1. Year, 2020 (`0x07e4`)
1. Minimum age, 21 (`0x15`)
1. Valid = true

together with some proofs, denoted by `a`, `b` & `c`

With the proof, we can verify the validity by checking it against the verification software. In this case, we are hosting the verification code on Ethereum as a smart contract. So we posted the above proof to the smart contract at [0xeaa81f8f0f6d807d7abf54c5cc0b133d6c74f283](https://ropsten.etherscan.io/address/0xeaa81f8f0f6d807d7abf54c5cc0b133d6c74f283) to get the following transaction:

https://ropsten.etherscan.io/tx/0x06aaf49782891c041dee26034a17adbc9db92f96de3788b2b9f3eb6e3cb9a461

and we can see the event emitted:

```txt
Transaction successfully verified.
```

Hurray, we now know that Simon is above 21 without knowing his actual age!

This system uses two high level constraints:

1. Simon cannot cheat on this birth year. He cannot use any other years to generate a valid proof against the evidence published by his government.
1. Using that same birth year, we verify that `comparisonYear` - `birthYear` >= `minimumAge` holds.

## Usage with OpenAttestation or any verifiable credential system

Now that we know that we can construct a proof given a fixed value for the evidence of age, we can put this evidence into a verifiable credential and the subject can use this to generate multiple proofs.

For instance, using the same evidence, we can also show that Simon is above age 16 in year 2010 by reusing the same evidence and generating the following proof:

```json
{
  "proof": {
    "a": [
      "0x2be9dd7fe9e62bff54e4446b7dad03e2d7df11a04bbffcac4abeed89b8e1c1de",
      "0x1b548baf978f53aa3e146e432188b95def86b6b4a76e956482b1c8714ed7b720"
    ],
    "b": [
      [
        "0x2117b4b666589acf7fea333916da9bf95e114b06e3a73d718d9c323a2381339c",
        "0x2346dac665a4fad21c51602b3065280da92d5f0765ebfb335d6a3864028f785f"
      ],
      [
        "0x0d3b21d04da2e6b637a82b7d901a40f9a676bc7bca81da74d10cb30648fa4b94",
        "0x25e87340db3371977df420ecf5d5f3ffe2c7dcdddf2dfa2d3861ea44fb95355e"
      ]
    ],
    "c": [
      "0x1089f5571f7c0fcd024de8de064c8094f3876a914e6d4fb8f7a5f912ca352aef",
      "0x171b1b5643365d0b07f5f094e2457533692119b78b0e64eae1fd5989235b3c05"
    ]
  },
  "inputs": [
    "0x00000000000000000000000000000000afde9b79b5468df38fee34234f567422",
    "0x0000000000000000000000000000000079c1d49be864fa63c491a8a4335feb1a",
    "0x00000000000000000000000000000000000000000000000000000000000007da",
    "0x0000000000000000000000000000000000000000000000000000000000000010",
    "0x0000000000000000000000000000000000000000000000000000000000000001"
  ]
}
```

as seen in [this transaction](https://ropsten.etherscan.io/tx/0xd53b02887514d015a41e38a4466036cd710d7822c42613a1aa425ecf20c5f2af).

## Step-by-step

To try this for yourself, you may follow [this document](./CONSTRUCTION.md) to construct your age verifier circuit.
