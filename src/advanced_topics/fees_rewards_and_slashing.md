## Fees

### Base Transaction Fees

The network targets a 5% annual burn of circulating supply. Every network transaction has a base fee that continually adapts such that the amount burned per transaction meets the projected 5% target. The base fee is adjusted every epoch using a control algorithm.

### Cost of Generating Embeddings and Submitting Data

The cost of generating an embedding is a free market. When encoders join the network, they submit their cost per byte for each modality that they are participating in. The costs are aggregated across all encoders, ordered, and the 2/3rd percentile value by stake is selected as the stable rate per epoch. At any point, an encoder can submit a transaction to the network that changes their price per byte for any modality. At the epoch boundry, the 2/3rd percentile values are recalculated accounting for the new values and applied for that epoch. Any additional fees paids on top of the base cost of generation acts as a tip.

The "best" encoding model from a shard receives the full fee amount.

Submitting data is identical to the cost required to generate embeddings. The person submitting data must pay in order to receive an embedding. In the case of network rewards issued for that data, the data submitter takes a risk by paying the embedding fee and only receives a network reward if it meets the criteria, this is to mitigate spam.

### Cost of Proxying

The cost of Proxying to a model is decided as a free market by the proxied model. We impose no network-level control here, just the mechanism to have a market.

## Rewards

### Embedding Incentive

Every winner of an embedding competition receives a network reward. The reward amount per win is small and distributed evenly across all shards. The objective here is to incentivize performance while distribute fairly over time. The best data scientists should probabilistically receive the most rewards over time.

### Data Submission Incentives

Every round, the top-k number of highest scores for the [Proof of Curiosity](proof_of_curiosity.md) game receive a small network reward. Again, the objective is to distribute small rewards but frequently and across many participants. We want to incentivize performance but make the distribution of network rewards extremely fair over time. The best data submitters should probabilistically receive the highest amount of rewards over time.

K is defined in [Governance Parameters](../reference/parameters.md).

### Validator Staking Incentive

Validator staking is not tied to performance in anyway. Running a validator offers a stable and predictable stream of network rewards proportional to the cumulative amount of stake. [Delegation](delegated_stake.md) in validators offers the same return minus whatever commission the validator has set.

### Validator Transaction Incentive

Validators receive a small reward for every operation they include inside of a block. This is to encourage validators to produce full blocks.

## Slashing

### Encoder Performance

Encoders are evalutated on multiple metrics. The lowest-k _(what's the k?)_ encoders based on a composite score _(what's the score formula?)_ of the following three metrics are slashed:

#### Differential Loss

The total loss from differential loss. Lower loss is better.

#### Outlier

How much do the encoders' embeddings deviate from the rest of the shard? Submitting an outlier is bad.

#### Latency

High latency negatively impacts the encoders' scores.

### Malicious Validators

In order to get slashed, validators must receive a vote from 2f+1 of the network stake. Validators self-report each other for not producing blocks, or high latency. When 2f+1 of the network stake has voted to slash a validator, every epoch that validator loses an amount of stake at a decay rate.
