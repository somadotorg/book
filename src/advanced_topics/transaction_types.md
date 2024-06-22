# Supported Signatures

The network supports the following signing schemes. By abstracting the signing method from transaction types, it allows for the future addition of new cryptographic methods, such as quantum-secure signing.

## Ed25519

Ed25519 is an elliptic curve signing algorithm using EdDSA and Curve25519. It is known for its speed and efficiency. This scheme provides strong security guarantees and is widely used due to its robust performance.

## Multi-sig

Transfers that verify the supplied signatures against the corresponding multi-sig account. N-of-M signatures are required, with a maximum of 32 signatures. The signature scheme is the same as for simple transfers. Multi-sig accounts enhance security by requiring multiple approvals for transactions, making unauthorized transfers more difficult.

## BLS Threshold-signature

Transfers that verify a BLS aggregate signature for a corresponding account. BLS signatures allow multiple parties to produce a single signature, which can be verified against the aggregate public key. This scheme is particularly useful for threshold cryptography and decentralized applications.

# Transaction Types

Soma does not support generalized smart contracts. Instead, the network opts for efficient transaction types built directly into the network functionality. These transaction types can be extended through decentralized governance.

## Transfers

Debit Soma tokens from one address and credit them to another address. Transfers are the fundamental transaction type for moving tokens within the network.

### Simple Transfer

A simple peer-to-peer transfer that verifies the transaction signature against the public key of the sender. This is the most straightforward way to transfer tokens between two parties securely.

### Timed Conditional Transfers

Standard Soma accounts are controlled by a single signing key. Although that signing key can require multiple signatures (e.g., multi-sig or aggregate BLS), the underlying account is controlled by one key. Certain special accounts can be controlled by two parties. Once either the time condition is met or another condition is fulfilled, the account self-destructs and debits the original owner's account with any remaining tokens.

#### Hashed Timelock Contract

The initiating party supplies:

1. Time to live (TTL) for the conditional account
2. Amount of tokens to supply
3. A hash pre-image that unlocks the total amount of tokens
4. The recipient of the tokens

During the TTL period, anyone can submit the hash pre-image to initiate the conditional transfer to the recipient. If the TTL expires, the account self-destructs, and the tokens are transferred back to the account creator. This mechanism ensures secure and conditional fund transfers based on predetermined criteria.

Any signing scheme can be used. Any transfers while the account is alive are added to the amount transferred to the recipient. Since the network directly controls these accounts and reserves a custom namespace, they cannot be transferred to after self-destruction.

#### Unidirectional Payment Channel

The initiating party supplies:

1. Time to live (TTL) for the conditional account
2. Amount of tokens to supply
3. The recipient of the tokens

During the TTL period, anyone can submit an amount to send along with the corresponding signature(s). The signature(s) must be valid, and the amount must be monotonically increasing. Ordering does not matter because the outcome is monotonically increasing. If duplicates are submitted, no additional funds can be sent; if a higher amount is submitted before a lower amount, the lower amount is invalid. The payment channel allows incremental streaming of funds off-chain. Any signing scheme can be used. After the TTL expires, all remaining tokens are returned, and the account self-destructs. The account can be transferred to while alive, incrementing the total amount. After the TTL expires, the account cannot be transferred to.

## Data and Intelligence

The core use case of the network. Data and intelligence transactions facilitate the creation, management, and utilization of data within the network.

### Create Data Orders

Unlike generating embeddings, data orders are NOT real-time but function more like an order book for data. Users can place bounties for data that meet specific constraints related to the embedding space, modality, and quantity. This allows for efficient matching of data suppliers and requesters based on predefined criteria.

### Generate Embeddings

This takes the sender's supplied data and corresponding costs for generating embeddings and returns the finalized embeddings from the network. Embeddings are used to represent data in a way that is optimized for machine learning and other computational tasks.

## Stake Management

Stake management transactions enable control over staking in both validators and encoders. Staking is essential for securing the network and incentivizing participants.

### Delegate Stake to Validator or Encoder

Delegate stake to a validator or encoder. Additional delegations will update the existing stake account and increase the quantity. This process supports the stability and performance of the network by empowering reliable validators and encoders.

### Remove Delegation from Validator or Encoder

Remove delegation allows for removing a specified amount from the validator or encoder. Additional calls will continue to decrease the amount delegated. This provides flexibility for stakeholders to adjust their investments according to network performance and personal strategies.

## Network Management

Controls joining or leaving for validators and encoders. Effective network management ensures the robustness and decentralization of the network.

### Register Validator

Adds a validator to the network. The validator must meet minimum stake requirements. This process ensures that only qualified and committed entities participate in validating transactions.

### Unregister Validator

Removes a validator voluntarily. Going below the minimum stake or acting maliciously results in hard and soft bans, respectively. This mechanism maintains the integrity and security of the network.

### Register Encoder

Adds an encoder to the network. The encoder must meet minimum stake requirements. Encoders contribute to the processing and management of data, enhancing the network's functionality.

### Unregister Encoder

Removes an encoder voluntarily. Going below the minimum stake automatically removes the encoder. This ensures that only active and compliant encoders are part of the network.
