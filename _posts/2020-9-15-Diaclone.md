---
layout: post
title: 'Diaclone: Transformers for Error Detection in Symbolic Music'
published: true
---

This site documents the code of **Diaclone**, a set of experiments in training transformer networks to perform error-detection and error-correction tasks on monophonic symbolic music.

All editable parameters for both training and testing are controlled by `transformer_params.py`. A single set of parameters fully defines a train/test experiment, so that different parameter files can be swapped out to perform different experiments.

A quick summary of the process to train a new model:

1. Set data paths, architecture, hyperparameters in `transformer_params.py`.
2. Run `data_management/make_hdf5.py` to make an `.hdf5` file out of the training data (this only needs to be done once per dataset).
3. Run `transformer_errors_train.py`.

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

Memory masking is used to prevent elements at certain positions in a sequence from attending to elements at other positions; for the source sequences, this is a matrix of shape (S, S) and for the targets it is a matrix of shape (T, T). The archetypal example of this is the "square subsequent mask," which looks a little like a descending staircase; it prevents any sequence element from attending to elements that come subsequent to it, and is used in tasks where the goal is to predict future members of a sequence given past values. For our purposes, we want to prevent elements from attending to their own past states (since the goal is to reconstruct every element from context) so we use point-masks instead. See the `make_point_mask` method inside the `TransformerModel` class.

![pointmask.png]({{site.baseurl}}/_posts/pointmask.png) ![Subsequentmask.png]({{site.baseurl}}/_posts/Subsequentmask.png)

Padding masks are used to prevent sequence elements from attending to padding elements that have been concatenated onto the end or beginning of a sequence to bring it up to the necessary length for a batch. Since different sequences in a single batch can have different amounts of padding, these are generated per-batch, on the fly. See the `make_len_mask` method inside the `TransformerModel` class.

![An example of a padding mask.]({{site.baseurl}}/_posts/paddingmask.png)


## Data Preparation

Symbolic music must be processed from `.krn` or `.midi` files into a `.hdf5` file for training. This processing is done by the `data_management/make_hdf5.py` script.  The resulting `.hdf5` file will contain each file of the input indexed as a two-dimensional array of integers under the path `corpus_name/file_name.` The columns of each entry contain `(MIDI Pitch, Onset time, Duration)` for each note.

Relevant parameters:

`raw_data_paths`: A dictionary linking corpus names to directories.
`beat_multiplier`: All durations and onset times are multiplied by this number and then rounded to the nearest integer.

This intermediate representation is transformed into trainable representations on the fly.

### Run-Length
For this representation, each note comprises two one-hot vectors concatenated together: one for pitch, and one for duration. The pitch vector is simple enough, since only so many MIDI pitch values are typically used, so we can represent that as we would any categorical data. Duration is more tricky; a huge number of note durations are possible but only a fraction of the possible durations are common. So, in `get_tick_deltas_for_runlength()`, we find out what the most common note durations are throughout the dataset, and one-hot encode to those (the number of durations used is set in the parameters file as `num_dur_vals`). Any duration that is not in the `num_dur_vals` most common durations is rounded to the nearest common duration. The function `arr_to_mono_runlength()` creates the one-hot vectors for each note, and concatenate pitch and duration together.

### Note-Tuple
The Note-Tuple is a MIDI-like representation of symbolic music, where each note is represented as an ordered 4-tuple of the form `(MIDI pitch, voice, delta from previous event, duration)`.
