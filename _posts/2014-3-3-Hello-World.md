---
layout: post
title: You're up and running!
published: true
---

Next you can update your site name, avatar and other options using the _config.yml file in the root of your repository (shown below).

![_config.yml]({{ site.baseurl }}/images/config.png)

This site documents the code of **Diaclone**, a set of experiments in training Transformer networks to perform error-detection and error-correction tasks on monophonic symbolic music.

## Model Architecture





## Factorizations

We represent each note as two one-hot vectors concatenated together: one for pitch, and one for duration. The pitch vector is simple enough; there are only so many pitch values that are actually used in the MTC dataset, so we can represent that as we would any categorical data. For duration, since we're measuring in MIDI ticks, a huge number of note durations are possible but only a fraction of the possible durations are common. So, in `get_tick_deltas_for_runlength()`, we find out what the most common note durations are throughout the dataset. The method `arr_to_mono_runlength()` we create the one-hot vectors for each note, quantizing all durations to their most common values, and concatenate pitch and duration together.
