---
layout: post
title: You're up and running!
published: true
---

This site documents the code of **Diaclone**, a set of experiments in training Transformer networks to perform error-detection and error-correction tasks on monophonic symbolic music.

All editable parameters for both training and testing are controlled by `transformer_params.py`. A single set of parameters fully defines a train/test experiment, so that different parameter files can be swapped out to perform different experiments.

## Model Architecture

The main model is described in `transformer_full_seq_model.py`. It is a


### Masking


## Factorizations

### Run-Length
We represent each note as two one-hot vectors concatenated together: one for pitch, and one for duration. The pitch vector is simple enough; there are only so many pitch values that are actually used in the MTC dataset, so we can represent that as we would any categorical data. A huge number of note durations are possible but only a fraction of the possible durations are common. So, in `get_tick_deltas_for_runlength()`, we find out what the most common note durations are throughout the dataset, and one-hot encode to those (the number of durations used is set in the parameters file as `num_dur_vals`). Any duration that is not in the `num_dur_vals` most common durations is rounded to the nearest common duration. The function `arr_to_mono_runlength()` creates the one-hot vectors for each note, and concatenate pitch and duration together.

### Note-Tuple
The Note-Tuple is a MIDI-like representation of symbolic music, where each note is represented as an ordered 4-tuple of the form `(MIDI pitch, voice, delta from previous event, duration)`.
