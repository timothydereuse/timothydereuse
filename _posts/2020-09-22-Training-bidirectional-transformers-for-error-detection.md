---
published: true
---
This post details the results of some experiments run using the bidirectional transformer outlined in the previous post.

## Sept-Oct 2020: Training bidirectional Transformers for error detection

These experiments involve training a bidirectional transformer (similar to the BERT architecture) on an error correction task on monophonic folk tunes: A certain percentage of the number of tokens in each sequence are masked at random positions, and a smaller percentage are set to random values (though only values used elsewhere in the dataset are permitted). The network is then trained to reconstruct the un-corrupted input. Results are given for pitch and duration separately, since the representation used here considers each note's pitch and duration as two separate one-hot vectors.

The four categories of result are:
- `global`: Measuring accuracy across the whole input
- `mask`: Measuring accuracy only on masked tokens
- `rand`: Measuring accuracy only on those tokens that were randomized
- `remain`: Measuring accuracy only on tokens that were neither masked nor randomized.

When parameters are not specified, they default to this base configuration:
- Sequence Length: 40
- Num. Layers: 6
- Num. Attention Heads: 4
- Hidden Dimension: 256
- Masked sequence elements: 15%
- Randomized sequence elements: 1.5%

![attn v duration.png](https://raw.githubusercontent.com/timothydereuse/timothydereuse.github.io/master/_posts/attn%20v%20duration.png)

![attn v pitch.png](https://raw.githubusercontent.com/timothydereuse/timothydereuse.github.io/master/_posts/attn%20v%20pitch.png)

![layers v duration.png](https://raw.githubusercontent.com/timothydereuse/timothydereuse.github.io/master/_posts/layers%20v%20duration.png)

![layers v pitch.png](https://raw.githubusercontent.com/timothydereuse/timothydereuse.github.io/master/_posts/layers%20v%20pitch.png)
