# ML Unknowns

## Experiment Design

In designing ML experiments with our centralized game server, we have a number of ML-specific unknowns to consider. To address these unknowns, we will need to design experiment that vary in the following factors:

* Modality(ies) of input data
* Model architectures
* Loss functions 
* Masking strategies
* Online training dataset
* Competitor count
* Pretraining methods / datasets (if any)
* Benchmark assessment

For the benchmark assessment in particular, it is important for us to consider different ways of evaluating the quality of embeddings produced by our network in comparison to centralized models. It may also require us to develop our own benchmark to account for the unique properties of our network.

## Questions

In addition to these experiment parameters, some different questions we have are:
1. How **manipulatable** is differential loss as a performance metric vs. the complexity of the masking strategy?
    1. Does outlier filtering help catch cheaters?
2. How does **tokenization** for input data work in practice?
    1. How does it differ across modalities?
    2. Is it sufficient to use a client-side method or does something need to be enforced at the protocol level?
3. Does our method of **multimodal** embedding production work in practice?
    1. Will people need to run encoders for multiple modalities simulataneously or is there a way to allow single-modality encoders to contribute to multimodal embeddings?
4. Does **differential loss** help or hurt the quality of embeddings after pretraining on respective benchmarks?
5. How will people play the **Proof of Curiosity** game in practice?
    1. What techniques will they use/
    2. Do these techniques result in "good" data submissions?
6. How does a model / set of models trained or participating in our system perform on other ML **benchmarks**?
7. How does **data** of different formats / sizes get processed by the network?
    1. How does **batching** data work to utilize the network efficiently?