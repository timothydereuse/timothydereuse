---
published: true
---
## Nov 2020 - Jan 2021: Predicting alignments

Previous error-correction tasks on dutch folk song failed to obtain higher accuracy results when sequences of longer lengths were fed into the model. Accuracy results tended to stay constant when as few as 10 notes were input into the model at a time (as opposed to entire songs at once); the expectation was that, since the bidirectional Transformer architecture used can attend equally to all points in the sequence, the model would "learn" to infer errors when it sees irregularities in what should be repetitive forms in folk songs. What can we infer from observing that this is not the case?

A series of less formal experiments were conducted with a synthetic dataset to try to "force" the transformer architecture from the previous experiments to learn repetitive forms. This dataset comprises repeated sequences of randomly chosen notes (one might call them "exceptionally uninspired serialist compositions"). Each sequence is between four and ten repetitions of a sequence of between five and twenty notes, uniformly chosen from a random range. The _only_ way to recognize errors in such a corpus is to first detect the period at which one of these sequences repeats and then note the locations where notes fail to conform to this regularity.

![uninspired_serialist_composition.png]({{site.baseurl}}/_posts/uninspired_serialist_composition.png)

(An example of one of the sequences used in the synthetic repetition dataset.)

Running the exact same default model from the previous set of experiments on this dataset, with the same procedure for introducing errors, showed that the bidirectional model is capable of near-perfectly accurate error correction when the input sequence length is 60 tokens, and performs much worse when given shorter and shorter lengths. This makes sense intuitively; 60 tokens is at least three repetitions of a note sequence, which means each sequence can be compared with two other examples of the sequence and a consensus between them can be formed.

So, the bidirectional transformer model is capable of learning long-term dependencies, but does not when given the error correction task on folk song. My hypothesis is that these songs, containing typically less than 100 notes apiece and not always staying within the notes of a single key, are simply too sparse to have significant redundancy in their long-term repetitious characteristics. Put another way, each individual note encodes a large amount of information relative to the piece as a whole, and melodic phrases do not tend to repeat more than two or three times without variation. As a result, errors in the melody are difficult to distinguish from actual high-information variations of the original piece. If, say, there were a chord progression or a bassline accompanying each song, then single errors would be much easier to point out. It is possible that the transformer model might eventually begin learning from longer-term dependencies if it were powerful enough, after it exhausts the more useful information from short-term melodic expectation that it is presumably making use of when given very short snippets.

The most natural way to test this hypothesis is to test on music that exhibits far more regularity than simple folk songs, and to test on far longer sequence lengths; this will require a new type of transformer architecture as well as a new representation for symbolic music.
