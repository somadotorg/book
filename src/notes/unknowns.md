# Implementation Unknowns

1. Tokenization
    1. Whether we need it at the protocol level for masking
    2. If we do, what should we use for text, images, video, audio?
2. Masking Ratios for Modalities
    1. What are the optimal masking ratios for different modalities?
3. Sharding
    1. How many nodes in a shard?
4. Market of Experts
    1. Will people actually use this if it's not required?
5. Tokenomics
    1. What control mechanism will we use for controlling the burn fee?
    2. Rewards
        1. Specific rewards amounts for embedders, validators, and data submissions
    3. Slashing
        1. Validator slashing mechanics
        2. Encoder slashing formula
6. Multimodality
    1. What is the embedding space's dimensionality?
    2. Does our mechanism for multimodality work?
7. Consensus
    1. Do we need leaders for Mysticeti?
    2. What state needs to be synced and how do we implement it?
8. How normal people will use the network
    1. Obtaining the embeddings with requested dimensions ([Matryoshka](https://arxiv.org/abs/2205.13147))
9. How will nodes validate the Proof of Curiosity game?
    1. Vector storage for nearest neighbor search
10. k in Proof of Curiosity
    1. What is the optimal value for k?


---

1. how does negotiating the price of embeddings work?
2. who calls the encoders from the validator nodes
3. how do batches of data work
4. data formats / resizing data
5. How can we fast sync efficiently while checkpointing state rather and using SPVs and merkle trees?
6. Practically how do we store balances
7. how do we update state
