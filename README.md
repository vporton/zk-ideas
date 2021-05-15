# ZK ideas for Hermes network

## ZK contract calls

Make it easy to make a contract call without revealing who called it.

Now it can be done this way:

- Make a new secret Ethereum account.
- ZK-transfer some ETH for gas to it (it may also need ERC-20/NFT/etc. transfers).
- Call the contract.

This way has some deficiencies:

- Multiple steps (slow transactions, no atomicity, more than optimal gas usage, need to guess the amount of gas used).

The problem of multiple steps can be resolved by creating a contract that accomplishes the above three steps from one method, but it still has high gas usage and need to guess the amount of gas used.

So, it could be done better in way similar to ZK transfers, but with an arbtirary call instead of Mint/Burn.

(Note that it is known who calls our ZK contract, even despite of it's unknown who calls the "target" "secret" contract; so it is sometimes more secure to use the three steps scheme explcitly, but the distrinction of secutiry is minor: most serious Ethereum users do call some contracts, not only do transfers.)

## Un-ZK

I call Un-ZK a ZK action (e.g. a ZK-transfer or a contract call with ZK of who called it) with the ability to recover who did the call and call data (e.g. contract call data or whose money are transferred to whom).

It can be done this way:

- Store a ZK proof that the caller is going to do this operation (it can be stored in a Merkle tree, to be more efficient) but ciphered with a caller's secret that he stores somewhere (the called could store all his secrets in an online DB like Arweave or a Merkle tree in Ethereum, but ciphered with one secret, such as his Ethreum private key). We can store a hash of this proof (after joining it with the private key). Actually we need to store namely hash, for all kinds of the operations have the same length of the ZK proofs, not to be able to distinsguish between different kinds of operations. It can be either an Ethereum private key or some other secret data of the caller (the later has the advantge of not manipulating the private key in JS, what may be less dangerous for caller's security and/or require less permissions to be confirmed).

- Do the operation, storing also a "pointer" (that is the hash of the ZK proof) to the ZK proof from the previous item. (It can be done by a contract that calls a ZK contract together with storing that hash. So, we will have an association between the ZK proof and the operation stored in Ethereum history.)

- ("Recovery") When the caller wants to reveal his identity, he can publish the (first) ZK proof for other to verify the hash he published is of this ZK proof.

Note that the process uses two ZK proofs.

The previous can be generalized by allowing to restore any of several "details" (functions of) of the call data (caller as in the previous example or any other data).

Moreover, we can make "restorable" arbitrary (quickly) computable functions of the data, by storing (similar to above) hashes of ZK-proofs that the value of the function is this, that can be done by the original caller assoiating the value of the function with the hash of the original data conctenated with the caller's secret.

An easier (and more efficient) thing is: instead of being able to use abitrary functions, split input data into chunks (it could be: caller, ERC-20 address, payer, payee, and amount for transfers) and specify which chunks can be recovered by the keys.

Moreover, the above is easy to modify to be able to specify which chunks can be recovered by which keys.

An improvement: Use another account for recovery (second ZF proof) than the account used to send the ZK transaction. To do this, we need to allow

## Mutlisig ZK recovery

We can do mutisig ZK recovery by using concatenation of secrets of several users as the ZK key.

It could be useful to do also recovery by non-100% voting (e.g. by >50% voting), but that's not easy to invent.
