---
published: false
---
## Jan 2021: Predicting error alignments on popular music

The following experiments use musical content from the [Lakh Midi Dataset](https://colinraffel.com/projects/lmd/); specifically, the subset defined as "LPD-cleansed" in the [Lakh Pianoroll Dataset](https://salu133445.github.io/lakh-pianoroll-dataset/dataset). This comprises a database of some 20,000 MIDI files, predominantly containing Western popular music, all in 4/4, with no tempo or time signature changes, where the first downbeat of the song always occurs simultaneously with the first note of the MIDI file. Each file is collapsed down into five channels (as described in the description for the LPD-cleansed-5 dataset in the link above): drums, piano, guitar, bass, and strings.

We represent this polyphonic music using the Note-tuple representation [first proposed for using Transformer networks to model piano performance](https://nips2018creativity.github.io/doc/Transformer_NADE.pdf). Other, "run-length" representations were considered, which would allow collapsing simultaneous MIDI events down into a single token of a sequence, but this merging of separate note events creates problems when trying to localize individual errors later in the process. On the other extreme, a piano-roll representation that "samples" the symbolic music at regular intervals (usually at 48 or 96 times a beat) and treats the result like an image would offer very well-defined localization of errors but would make each sequence take up a huge amount of space in memory. The note-tuple is a decent compromise. We define a passage of polyphonic music as a series of 4-tuples (**N**, **O**, **D**, **V**) where:

- **N** is the note's MIDI pitch,
- **O** is the interval of time between this note's onset and the onset of the next note in the sequence (measured in 48ths of a beat, rounded to the nearest integer)
- **D** is the duration of the note (measured as above)
- **V** is the voice of the note.