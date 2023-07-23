# Circuit Deployment on Polygon
This repository contains the imlementation of the given circuit diagram using circom and also demonstrates 
how to deploy it on Polygon Mumbai Testnet.

## Contents
- `circuit.circom`: This file contains the implementation of the logical gate circuit using the Circom language. It defines the inputs, outputs, and the constraints that enforce the desired behavior of the logic gate.
- `compile.js`: This script is used to compile the circuit and generate the necessary intermediaries for proving the correctness of the circuit.
- `prove.js`: This script generates a proof using the inputs A=0 and B=1. It uses the compiled circuit and the provided inputs to create a proof of correctness.
- `verifier.sol`: This Solidity contract defines a verifier that can be deployed to the Goerli Testnet. The verifier contract has a method `verifyProof` that takes a proof as input and returns a boolean value indicating whether the proof is valid or not.
- `deploy.js`: This script deploys the verifier contract to the Goerli Testnet.
- `test.js`: This script interacts with the deployed verifier contract by calling the `verifyProof` method with a proof and asserts that the output is true.

## Prerequisites

Before proceeding, make sure you have the following installed on your system:

* Node.js: [https://nodejs.org]

* Circom: [https://github.com/iden3/circom]

* SnarkJS: [https://github.com/iden3/snarkjs]

* A test wallet with some Mumbai Faucet (MATIC) for contract deployment.

## Getting Started

### Installing

To get started with the project, follow these steps:

1. clone the circom repository :
    `git clone https://github.com/gmchad/zardkat`
   
2. Install the required dependencies :
   `npm i`

### Executing program

```
pragma circom 2.0.0;

template harshcircuit() {  

// signal inputs

   signal input a;
   signal input b;

// signals from gates

   signal x;
   signal y;

// final signal output

   signal output q;

// component gates used to create custom circuit

   component andGate = AND();
   component notgate = NOT();
   component norGate = NOR();

// circuit logic

   andGate.a <== a;
   andGate.b <== b;
   x <== andGate.out;

   notgate.in <== b;
   y <== notgate.out;

   norGate.x <== x;
   norGate.y <== y;
   q <== norGate.out;

}
// template for AND
template AND() {
    signal input a;
    signal input b;
    signal output out;

    out <== a*b;
}
// template for NOT
template NOT() {
    signal input in;
    signal output out;

    out <== 1 + in - 2*in;
}
// template for NOR
template NOR() {
    signal input x;
    signal input y;
    signal output out;

    out <== x*y + 1 - x - y;
}

component main = harshcircuit();


```

`npx hardhat circom` 

This will generate the **out** file with circuit intermediaries and geneate the **MyCircuitVerifier.sol** contract

`npx hardhat run scripts/deploy.ts --network mumbai`

This script does 4 things  

1. Deploys the MyCircuitVerifier.sol contract at mumbai network
2. Generates a proof from circuit intermediaries with `generateProof()`
3. Generates calldata with `generateCallData()`
4. Calls `verifyProof()` on the verifier contract with calldata

## Author
Harsh Chaudhary 
https://github.com/harsh08hc

## License

This code is released under the MIT License. Feel free to use, modify, and distribute it as per the terms of the license.

