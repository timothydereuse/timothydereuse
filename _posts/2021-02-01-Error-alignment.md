---
published: false
---
## (Nov 2020 - Jan 2021: Predicting alignments)

Previous error-correction tasks on dutch folk song failed to obtain higher accuracy results when sequences of longer lengths were fed into the model. Accuracy results tended to stay constant when as few as 10 notes were input into the model at a time (as opposed to entire songs at once); the expectation was that, since the bidirectional Transformer architecture used can attend equally to all points in the sequence, the model would "learn" to infer errors when it sees irregularities in what should be repetitive forms in folk songs. What can we infer from observing that this is not the case?

A series of less formal experiments were conducted with a synthetic dataset to try to "force" the transformer architecture from the previous experiments to learn repetitive forms. This dataset comprises repeated sequences of randomly chosen notes (one might call them "exceptionally uninspired serialist compositions"). Each sequence is between four and ten repetitions of a sequence of between five and twenty notes, uniformly chosen from a random range. The _only_ way to recognize errors in such a corpus is to first detect the period at which one of these sequences repeats and then note the locations where notes fail to conform to this regularity.




