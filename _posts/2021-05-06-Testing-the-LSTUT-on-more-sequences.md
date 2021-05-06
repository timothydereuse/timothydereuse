---
published: false
---
## Testing the LSTUT on more sequences

Previously, I tested [the Long-Short Term Universal Transformer network](https://boblsturm.github.io/aimusic2020/papers/CSMC__MuMe_2020_paper_46.pdf) on a series of  




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

The only things that change through these experiments are the nature of the sequences input and the types of errors added to them.

The basic sequence:

- Is a sawtooth wave with a range of [0, 1] with a random exponent in (0.2, 5) applied to it
- A period of 1/8 * the sequence length
- A random starting phase

The errors applied to these sequences are, by default:

- 5 random insertions
- 0 random deletions
- 0 random replacements

