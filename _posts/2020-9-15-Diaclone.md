---
layout: post
title: You're up and running!
published: true
---

This site documents the code of **Diaclone**, a set of experiments in training transformer networks to perform error-detection and error-correction tasks on monophonic symbolic music.

All editable parameters for both training and testing are controlled by `transformer_params.py`. A single set of parameters fully defines a train/test experiment, so that different parameter files can be swapped out to perform different experiments.

## Model Architecture

The main model is described in `transformer_full_seq_model.py`. It is a fairly minimal wrapper around [the PyTorch default "all-in-one" transformer module](https://pytorch.org/docs/stable/generated/torch.nn.Transformer.html). The inputs and targets are both passed through their own one-layer feed-forward networks before entering the transformer, and the transformer's output is passed through another one-layer network to return it to the size of the input embedding. For further information about the inner workings of an encoder-decoder transformer sequence model, refer to [The Illustrated Transformer](http://jalammar.github.io/illustrated-transformer/).

The dimensionality of the input must be specified upon creation of the model (in the argument `num_feats`, but the number of features can depend on the specific dataset used (e.g. when using the runlength encoding that requires gathering statistics on the dataset before training).

Relevant Parameters:
`ninp`: Dimension of the transformer's attention mechanism.
`nhid`: Dimension of each transformer encoder/decoder's feed-forward layer.
`nlayers`: Number of encoder/decoder layers in the transformer.


### Masking


## Factorizations

### Run-Length
We represent each note as two one-hot vectors concatenated together: one for pitch, and one for duration. The pitch vector is simple enough; there are only so many pitch values that are actually used in the MTC dataset, so we can represent that as we would any categorical data. A huge number of note durations are possible but only a fraction of the possible durations are common. So, in `get_tick_deltas_for_runlength()`, we find out what the most common note durations are throughout the dataset, and one-hot encode to those (the number of durations used is set in the parameters file as `num_dur_vals`). Any duration that is not in the `num_dur_vals` most common durations is rounded to the nearest common duration. The function `arr_to_mono_runlength()` creates the one-hot vectors for each note, and concatenate pitch and duration together.

### Note-Tuple
The Note-Tuple is a MIDI-like representation of symbolic music, where each note is represented as an ordered 4-tuple of the form `(MIDI pitch, voice, delta from previous event, duration)`.
