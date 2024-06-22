# Calling Encoders

## Initial Transaction

The individual submitting data to the network for encoding submits a special transaction that contains the entire fee for performing encoding. This fee is dependent on the modality and size of the dataset. To link to a dataset without risking DDOS attacks from external parties attempting to censor the data submitter, a hash of (dataset hash, submitting address) is included in the transaction. This allows called encoders to quickly verify that they should participate in encoding when provided with the data hash while keeping it hidden from the rest of the network.

## Data Transfer

The individual submitting the data then contacts all the nodes in the shard (a random but deterministic subset of the network) based on the data hash plus some other network randomness. The shard selection is deterministic based on round number, and stake for the epoch. Every round the shard changes for a given data hash. The submitter provides the transaction hash and data hash which acts as a pre-image to prove to the validator that they should perform the work. This message is signed.

With this signed pre-image after a predetermined number of rounds, this can be submitted to the network to claim the data submitters funds and split between encoders.

The data is then transferred to the encoder and hashed to ensure that it matches the data hash. If it does not match, the encoder ceases work and notifies the other encoders in their shard.

By notifying the other encoders, prior to commit selection they are able to reduce being slashed for latency. If they do end up getting selected and it comes to light that they have committed while saying they would not commit, they are heavily slashed.

## Commit

If the data checks out, the encoder then starts executing the encoder model on the data. The resultant embeddings are hashed to create a “commit” hash. The commit hash is returned to the individual submitting the transaction.

## Selection

To progress to the reveal stage, the quorum of encoders must be met which is between 80-90% of the shard. The data submitter, must sign off on the selected quorum of commits and corresponding encoders. Typical flow for this is to select the first nodes that respond which incentivizes low latency, but selection is left entirely to the person paying the network. We want to incentivize the lowest latency response because we will evaluate the quality of the embeddings later using a different method.

It is important to note, due to market of experts routing mechanism, it is possible that other encoders are participating in the game, unbeknownst to the data submitter.

At this point, the not selected encoders are slashed unless they proactively notified the rest of the encoders that they opted out.

In the case that the leader goes offline, before making a selection, after a pre-determined number of blocks the encoders can split the encoding fee evenly across the shard. While there might be an incentive for all encoders to ‘not do work’ there is the risk of getting latency slashed for not doing work.

## Reveal

The data submitter provides the selection proof (signature of selection) to the selected encoders. The encoders then verify the proof and reveal their embeddings.

The encoders respond with a signed message that includes their embeddings e.g. merkle hash root or something of the nature.

In the case of a mismatch between the commit and reveal values or a lack of response from an encoder results in slashing. This mismatch is proven to the other encoders in the network.

The leader responds with all the received embeddings and associated signatures and a sign off on that bundle. There is an incentive to do this because without the final proof, all of their funds are still liable.

## Final Proof

The encoders then evaluate the quality of the embeddings and calculate the final embeddings based on performance. They submit a final proof to the network that specifies all the consensus of the encoders on who won and supplied the highest quality embeddings, who was slashed, etc. Failure to comply results in major slashing for encoders. The encoders use a BLS signature, and provide the final embeddings to the greater network.
