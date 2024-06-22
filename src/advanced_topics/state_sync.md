# State Sync

New or recovering validators need a way to synchronize their state with the rest of the network.

# Snapshot Sync

By default, validators sync up with the network using snapshots. Snapshots represent the network's current state, attested by nodes with 2f+1 of stake. To synchonize a node, all that must be done is to download the state snapshot, verify the integrity of the data, and load into local storage. Afterwards, the node can catch up to any new transactions by acquring the intermediate state changes since the snapshot's creation.

## Generating Snapshots

### Chunking State

The network state is composed of user balances and embedding related data. Both can be chunked intelligently to minimize redundant work.

Given that Soma does not support arbitrary smart contracts, user state related to balances remains small, with very controlled bloat. State simply scales linearly with the number of accounts.

To further improve the chunking performance, it is possible to apply grouping of accounts such that state that changes frequently are grouped together in the same chunks. By intelligently grouping state, the validators minimize the hashing and chunk processing required for each incremental update to the state snapshot.

The embedding data can grow to be extremely large in size given the high dimensional space of embeddings and frequency of generation. To deal with this, the network may apply a pruning algorithm that prunes based on time, position in embedding space, or a hybrid of both.

Again, to minimize the work required between state snapshots, the validators can intelligently group state in such a way that chunks of embeddings that are far from being pruned do not need to be reprocessed every time that new embeddings are added or pruned.

Chunking state allows a node that is attempting to synchronize with the network to download and process the chunks of state in parallel. Additionally, given the intelligent chunking algorithm, a node that is recovering only needs to download the chunks that have changed.

### Cuckoo Filters

More traditional snapshot approaches (i.e. Merkle proofs) introduce an attack vector from malicious peers that can significantly degrade performance by supplying invalid chunks to nodes. Moreover, the traditional process of creating Merkle proof snapshots leads to performance hiccups from having to search the tree, serialize all nodes, build chunks, and save them to local storage.

To efficiently verify snapshots the network takes inspiration from [**_A Bloom Filter Based Scalable Data Integrity Check Tool for Large-Scale Dataset_**]() to use a Bloom filter to verify the checksum of chunks in parallel, with minimal memory usage. Bloom filters are probabilistic data structures that are used to quickly and efficiently check if an element is in a set.

Cuckoo filters are similar to Bloom filters, with the additional feature of deleting items in the set. By leveraging a Cuckoo filter insead of a Bloom filter, it is possible to update snapshots incrementally with high efficiency.

After passing all the hashes of state chunks through a Cuckoo filter, the bitmap of the filter can be hashed, giving us a verifiable Cuckoo filter. A validator that is syncing to a snapshot would only need to:

1. Download the corresponding Cuckoo filter and verify the hash
2. Process every incoming chunk using a parallel work stealing pattern

This is far more efficient than waiting for all chunks to be downloaded before verifying and loading them into local storage.

To update to the Cuckoo filter, nodes simply delete the hashes of old state chunks from the filter and add in the new state chunk hashes. This allows for extremely efficient incremental updates to state snapshots.

### False Positive Rate

Cuckoo and Bloom filters have false positive rates. The probability of a false positive is controllable, but the risk is non-zero.

To mitigate false positives, the network employs a more traditional cryptographic proof that is computed once the entire snapshot has been downloaded by a syncing node. This allows the chunks to still be processed and loaded into the local database in parallel with an extremely high probability of being valid. However, to reach 100% certainty on the integrity of the data, the final checksum can be computed after all chunks have been downloaded.

The final hash is as simple as concatenating the individual chunk hashes together in a determistic way e.g: `hash(sort(chunk_1_hash, chunk_2_hash, chunk_3_hash, ...))`. There is no need for redundant hashing given that all the hashes were precomputed from earlier steps.

In the case that malicious chunks becomes extremely problematic, this could be converted to a Merkle tree to efficiently figure out which chunk is incorrect. However, it is likely sufficient to simply increase the size of the Cuckoo filter, which makes it substantially more difficult for a malicious chunk to pass as a false positive.

The Cuckoo filter gives us a high probability (99.999%) of filtering out malicious or corrupted state chunks. The final hash allows us to assure 100% integrity of the synced data.

## Syncing Using Snapshots

To sync a new validator:

1. The operator finds the latest snapshot using peer-to-peer gossip, initiated with a set of trusted entrypoint nodes.
2. The validator gathers the required proof that a snapshot has been attested by 2f+1 of the stake in the network.
3. After verification, the validator downloads the snapshot's corresponding Cuckoo filter and checks the hash of the filter's bitmap against the hash in the snapshot.
4. Using the Cuckoo filter to check incoming chunks, the validator efficiently downloads and processes chunks in parallel until all chunks have been downloaded.
5. The hashes from each chunk are then concatenated together in a determistic order and hashed to produce the final checksum.

After successfully loading the snapshot, the validator then downloads the additional state diffs from subsequent rounds that happened after the snapshot was taken.

### Future Research

It may be possible to effectively snapshot state in an ultra efficient way that allows a state snapshot to be computed on a round by round basis rather than at epoch changes. If so, there may be an efficient way to download only the state that has changed from the last snapshot and apply it to the state chunk more like a delta but allowing the delta to be efficiently calculated across trivial ranges.

e.g.

```
(New round 100 state chunk) - (old round 90 state chunk) = (10 round state delta)

(10 round state delta) + (old round 90 state chunk) = (New round 100 state chunk)
```

# Archival Sync

Archival sync is a specialized sync that attempts to download the entire history of transactions and embeddings. Archival nodes in addition to storing this information intentionally also organize and serve the archive in an efficient manner for others to download. Archival nodes attempt to sync and verify records in a P2P fashion, but may also be run in a faster version that downloads the entire archive from a storage bucket like S3, GCS, etc and only samples P2P nodes to spot check the historical record.
