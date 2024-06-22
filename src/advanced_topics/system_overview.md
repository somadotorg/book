# Overview

## Mission 

Soma's mission is to develop a deep understanding of the world by generating the most versatile and effective general-purpose embeddings for a wide range of data types.

## Embeddings as Foundations

Embeddings are crucial for many existing machine learning applications. They provide a compact and efficient representation of complex data, enabling tasks such as natural language processing, computer vision, and recommendation systems. By transforming raw data into refined embeddings, these embeddings serve as the building blocks that more specialized advanced AI tasks possible and effective.

## Network Objective

The objective of Soma is to generate the most useful general-purpose embeddings for various data types, creating a deep understanding of the world. While the network supports online inference, its primary goal is continuously improve a massive multimodal embedding network. Such that distilled models from Soma outperform previous versions and set new standards in machine learning benchmarks. By transforming raw high-dimensional data into refined embeddings, Soma makes it possible to fine-tune models with minimal compute and energy, supporting a wide range of use cases.

## Decentralization

Soma leverages the inherent advantages of decentralization:

**Pooling of Resources**: Decentralization allows the network to effectively pool compute power, energy, data, and machine learning techniques from a diverse set of contributors. This collective approach enables the creation of networks that would be cost-prohibitively expensive to train otherwise.

**Diverse Perspectives and Reduced Bias**: The world is inherently decentralized, with data and insights spread across various sources and regions. Soma's network mirrors this natural distribution, ensuring that embeddings are enriched by a wide array of perspectives and contexts. This diversity reduces bias, as there is no central controlling entity, promoting fairness and inclusivity in data representations.

**Continuous Improvement through Competition**: The decentralized structure of Soma fosters continuous improvement through economic competition and a "survival of the fittest" dynamic. As the network evolves, it self-improves by encouraging the best contributions to rise to the top. By democratizing access to advanced AI capabilities, Soma lowers barriers to entry, allowing more individuals and organizations to benefit from and contribute to the development of high-quality world model.

## Architecture

Soma is composed of two major systems: the encoders and the validators. Encoders are isolated machine learning models equipped with webhooks that allow validators to call on them to perform tasks. Validators handle maintaining balances, validating transactions, issuing and calculating network rewards, and verifying the work performed by encoders.

This separation of roles is designed to focus the skill sets of network participants, improve maintainability, and increase modularity. Individuals with the expertise and stake necessary to run validators can do so without needing to be top-tier data scientists. Conversely, data scientists can contribute their skills without the additional work and stake requirements of running a validator.

### Encoders

Encoders are machine learning models that generate embeddings from a given piece of data. Encoders are organized into groups called shards, which are selected using a stake-weighted random sample. The "best" embedding produced by the encoders in the shard receives a reward for its quality. Although only the highest-performing encoder is rewarded, all embeddings from the shard contribute to the final result. The final embedding returned to the data submitter is a weighted average based on the differential loss of the embeddings. If an embedding is deemed an outlier, the encoder responsible is slashed, and the proposed embedding is disqualified. The lowest-performing encoder models receive a minor penalty, which, though severe, creates a strong incentive for improvement or departure from the network. The reward and slashing system is designed to meritocratically enhance performance and eliminate unfit encoder models.

### Validators

Validators form the core infrastructure responsible for handling transactions, managing account balances, performing staking operations, and bridging between encoders and data submitters. They use a directed acyclic graph (DAG) method to achieve high throughput and fast finality, prioritizing speed and efficiency over atomic ordering of events. This results in lower latency between transaction submission and finality. For more detailed information, refer to the Consensus section.

## Embedding Process

1. A new piece of data is submitted to the network for a fee.
2. The network self-selects a shard of encoders to individually embed the data.
3. Their embeddings are aggregated with a ***performance-weighted average*** that ensembles features from the different encoders while ensuring high semantic integrity.
4. The individual embedding with the **best performance** is rewarded for its work. Poor performing encoders are penalized.
5. Shard selection becomes increasingly intelligent in ***routing*** submitted data to specialized encoders that perform best in their respective section of the embedding space.