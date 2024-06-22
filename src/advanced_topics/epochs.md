# Epochs

Epochs are used to manage the adding and removing of network validators, as well as to manage the network's stake distribution and issue network incentive rewards.

## Epoch Changes

Epoch changes start at a predefined commit, dictated by a certain round number from the last committed round from the previous epoch. Once a validator has **committed** the predefined round number, it stops committing further blocks and tells other validators that it is ready to change epochs. Other validators stop committing blocks upon receiving this message.

Once a validator has received **2f + 1** such messages from other validators, it will perform the following steps: 

1. Increment its epoch number.
2. Process reconfiguration of the validator set.
3. Calculate slashing amounts for validators and encoders.
4. Calculate and distribute rewards to validators, encoders, delegators, and Proof of Curiosity winners.
5. Start committing blocks again in the new epoch. 

## Reconfiguration

Adding and removing validators is done through a reconfiguration process at the end of each epoch. During an epoch, candidates may submit a transaction to add or remove themselves from the validator set. In the case of an addition request, if the candidate meets the minimum stake requirement, they are added to the a list of pending additions.

Note that changing an epoch at a predefined commit guarantees that all validators will be synced to the same state before the epoch change. Since pending validator additions and removals are transactions within blocks, this ensures that validators will have the same view of the new epoch's committee.

Once the epoch change is complete, the new validator set is finalized and the new epoch begins.