---
layout: post
title: You're up and running!
published: true
---

This site documents the code of **Diaclone**, a set of experiments in training transformer networks to perform error-detection and error-correction tasks on monophonic symbolic music.

All editable parameters for both training and testing are controlled by `transformer_params.py`. A single set of parameters fully defines a train/test experiment, so that different parameter files can be swapped out to perform different experiments.

## Model Architecture

The main model is described in `transformer_full_seq_model.py`. It is a fairly minimal wrapper around [the PyTorch default "all-in-one" transformer module](https://pytorch.org/docs/stable/generated/torch.nn.Transformer.html). The inputs and targets are both passed through their own one-layer feed-forward networks before entering the transformer, and the transformer's output is passed through another one-layer network to return it to the size of the input embedding. The positional encoding mechanism used is identical to the one found in the PyTorch examples. For further information about the inner workings of an encoder-decoder transformer sequence model, refer to [The Illustrated Transformer](http://jalammar.github.io/illustrated-transformer/).

The dimensionality of the input must be specified upon creation of the model (in the argument `num_feats`, but the number of features can depend on the specific dataset used (e.g. when using the runlength encoding that requires gathering statistics on the dataset before training).

Relevant Parameters:
`ninp`: Dimension of the transformer's attention mechanism.
`nhid`: Dimension of each transformer encoder/decoder's feed-forward layer.
`nlayers`: Number of encoder/decoder layers in the transformer.
`dropout`: Dropout probability.

### Masking

Two distinct types of masking are used in this Transformer implementation; memory masks and padding masks. (N is the number of input sequences; S is the length of the input sequences; T is the length of the target sequences.)

Memory masking is used to prevent elements at certain positions in a sequence from attending to elements at other positions; for the source sequences, this is a matrix of shape (S, S) and for the targets it is a matrix of shape (T, T). The archetypal example of this is the "square subsequent mask," which looks a little like a descending staircase; it prevents any sequence element from attending to elements that come subsequent to it. For our purposes, we want to prevent elements from attending to their own past states (since the goal is to reconstruct every element from context) so we use point-masks instead. See the `make_point_mask` method inside the `TransformerModel` class.

Padding masks are used to prevent sequence elements from attending to padding elements that have been concatenated onto the end or beginning of a sequence to bring it up to the necessary length for a batch. Since different sequences in a single batch can have different amounts of padding, these are generated per-batch, on the fly. See the `make_len_mask` method inside the `TransformerModel` class.

## Factorizations

### Run-Length
We represent each note as two one-hot vectors concatenated together: one for pitch, and one for duration. The pitch vector is simple enough; there are only so many pitch values that are actually used in the MTC dataset, so we can represent that as we would any categorical data. A huge number of note durations are possible but only a fraction of the possible durations are common. So, in `get_tick_deltas_for_runlength()`, we find out what the most common note durations are throughout the dataset, and one-hot encode to those (the number of durations used is set in the parameters file as `num_dur_vals`). Any duration that is not in the `num_dur_vals` most common durations is rounded to the nearest common duration. The function `arr_to_mono_runlength()` creates the one-hot vectors for each note, and concatenate pitch and duration together.

### Note-Tuple
The Note-Tuple is a MIDI-like representation of symbolic music, where each note is represented as an ordered 4-tuple of the form `(MIDI pitch, voice, delta from previous event, duration)`.
