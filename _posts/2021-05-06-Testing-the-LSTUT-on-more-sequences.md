---
published: false
---
## Testing the LSTUT on more sequences

Previously, I tested a bidirectional transformer network on a [series of experiments](https://timothydereuse.github.io/predicting-error-alignments-on-polyphonic-popular-music/) wherein the goal was to detect "errors" introduced into symbolic music. These experiments were trying to identify each possible type of error (insertion, deletion, replacement) separately, and this turned out to be quite a lot of trouble (I spent a good month or two chasing ideas that went nowhere, trying to find an error detection method that worked to perfectly locate and describe the nature of the error when detecting it). Here I am using a simpler approach, where I seek to answer a binary question at each point in the input sequence: is there some error here, of any kind? Additionally, I am using the [Long-Short Term Universal Transformer network](https://boblsturm.github.io/aimusic2020/papers/CSMC__MuMe_2020_paper_46.pdf) architecture which has shown promise for detecting long-term dependencies in musical data. Other than these two changes the setup for this experiment is the same as the previous one (including the use of Notetuple format for input data.).

### Fixed variables

Base architecture:

- A feedforward network to increase the dimensionality of each input point from the number of features to the dimension of the LSTM / transformer
- 2 attention heads
- A hidden dimension of 128 for the attention calculation on each head 
- A fully connectedfeed-forward network of dimension 128 on each head, w/GeLU nonlinearity
- A depth-recurrence of 5 (that is, during a forward pass the data goes through the transformer 5 times)
- 3 Layers of Bidirectional LSTMs on either side of the universal transformer, each with hidden dimension 128
- A feedforward network to reduce the dimensionality of each sequence element back to 1
- Dropout of 0.1 everywhere

Base training procedure:

- A dataset of 2048 Sequences for training and 204 for validation
- All sequences are 256 elements long
- A batch size of 512
- Training proceeds for 150 epochs or until the validation score has not improved for 20 epochs (usually about 11 minutes on a V100 32GB GPU on Compute Canada)

### Modified Variables

The only things that change through these experiments are the nature of the sequences input and the types of errors added to them.

There are two types of sequences: a Sawtooth Sequence and a Periodic Random sequence

The sawtooth sequence is a sawtooth wave with a range of [0, 1] and a random exponent in (1/5, 5) applied to it. It has a mean period of `1/4 * sequence length` and a fixed starting phase. The Periodic Random sequence is a random sequence of values in [0, 1] that repeats with mean period `1/4 * sequence length`. 

The errors applied to these sequences are, by default:

- 5 random insertions
- 0 random deletions
- 0 random replacements

The sawtooth sequence here serves as a control, as it should be trivially easy to tell when the sawtooth wave is being interrupted without any information about the periodicity of the sequence. For the periodic random sequence, errors can only be identified by comparing each sequence element with its counterpart in repetitions of the sequence.

The below image shows an example run of the default parameters outlined above, on both the sawtooth and periodic random sequences. The top plot shows the sequence that was fed into the network, and the bottom image shows the positions where the sequence was edited to have errors (on top) and the raw output of the network trying to predict those positions (just underneath).

![sawtooth_default](https://raw.githubusercontent.com/timothydereuse/timothydereuse.github.io/master/_posts/FINAL_2_LSTUT_ODDSEQS_0_(2021.04.28.22.39)_4-1-3-2-5-128-128.png)

![randseq_default](https://raw.githubusercontent.com/timothydereuse/timothydereuse.github.io/master/_posts/FINAL_2_LSTUT_RANDSEQS_0_(2021.05.05.15.45)_2-1-3-2-5-128-128.png)

