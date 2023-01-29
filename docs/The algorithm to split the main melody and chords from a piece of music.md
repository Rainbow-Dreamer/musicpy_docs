# The algorithm to split the main melody and chords from a piece of music

Recently, while writing some musicpy features, I came across the need to separate the main melody and chord parts of a piece. There is a function that performs intelligent composition analysis of a piece. Let's say now we read a MIDI file, which is a piano piece, and the main melody and chord parts are all put in one track. This piano piece has a total of 1400 notes, including the main melody and the chord part. Now we want to perform a tonal analysis of these 1400 notes, (ignoring for the moment the modulation and tonicization of the piece) to determine what key this piece is in.

In addition to analyzing the key, the chords of a piece need to be analyzed, so the chords need to be extracted separately. Some people may think that extracting the main melody of a song is just a matter of capturing the highest notes, but it's really not that simple.

First of all, after the MIDI file is read, the order of the notes depends on the time when each note appears (the note_on information is read). musicpy's chord class data structure is designed to store the notes in a list, and then the interval between each two adjacent notes is stored in another list. Each note has its own parameter duration, which records the duration of the note. The order of the notes in the list is the same as the information in the MIDI file, which note appears first in the list to start playing.

The main melody and the chord part are placed together in this piano piece. The difficulty is that many of the chord notes, especially the highest notes of the broken chords, can be very high and played just a little bit below the current main melody. These chord notes, which are played within the range of the main melody, are intended to harmonize the main melody and to enrich the layering and rhythm of the piece. However, when we listen to the human ear, we can still distinguish those chords that play into the main melody, because they do not form a "coherent melody" like the main melody, so they are generally not classified as such by the listener. (Although not without exceptions, there are occasional piano pieces where part of the main melody is also the highest note of the current chord)

The range of this piano piece is very wide, with the main melody spanning about three octaves and the chord part also spanning about three octaves, so it is obviously not practical to find a single note as a dividing line between the main melody and the chord part.

Now let's talk about my algorithm.

1. First, find all the groups of notes that are spaced at 0, (that is, the notes played together at the same time) and keep only the highest notes. This is because if there is a main melody and some notes are played at the same time, then these notes are generally chord notes or ornamental notes that enrich the harmony.
2. Next, a loop is started, traversing the entire list of notes. Here I need to mention two original concepts of my algorithm: melody tolerance and chord tolerance, which have been tested many times and have default values of minor seventh and major sixth. First, for the first note, if the pitch difference between the second note and the first note is greater than or equal to the chord tolerance threshold, then the first note is judged to be a chord note and the second note is the first note judged to be a melody, otherwise the second note is a chord note and the first note is the main melody note.
3. Now we have the first melody note. In the process of traversing the entire list of notes, the average pitch of the notes classified as melody within the length of the last bar (8 units of note length) is calculated, and if the total length of the notes currently classified as melody is less than one bar, the average pitch of all the notes currently classified as melody is calculated. If the pitch of the current note is higher than this average pitch, it is determined to be the main melody note. If it is lower than the average pitch, but the pitch difference is less than or equal to the melody categorization tolerance threshold, then the current note is most likely the main melody note.
4. On the basis of this condition, if the difference in pitch between the most recent note judged to be the dominant melody and the current note is less than the chord categorization tolerance threshold, then the current note is judged to be the dominant melody. (Because if the nearest note of the main melody needs to suddenly jump a long way down to the current note, then even if the composer intended the current note to be the main melody as well, it would be difficult for the listener to develop a sense of melody coherence, since a melody is essentially a set of notes of relatively similar pitch, contrasted with chord notes of large pitch difference and treated as melody.) If the current note is lower than the average pitch and the pitch difference exceeds the melody categorization tolerance threshold, the current note is determined to be the melody note when and only when the pitch difference between the nearest melody note and the current note and the pitch difference between the next note and the current note are both less than the chord categorization tolerance threshold.

I have tested this algorithm with many of my own compositions, and it works surprisingly well, extracting almost 100% of the complete and correct melody notes, regardless of the range of the melody. Once the main melody purification algorithm is successful, the chord extraction is very simple, which means that after the main melody is found, the remaining notes are the chord notes or the main melody ornamental harmonies.

In musicpy I wrote this original algorithm for separating the main melody and chord notes of a piece as a function called `split_melody`, which can separate the main melody of a piece with a chord type as a new chord type. There is also the `split_chord` function that separates out chord notes as new chord types. The `split_all` function returns 2 chord types, the main melody and chord notes of the piece.

The arguments to the `split_melody` function are, in order

```python
split_melody(current_chord,
             mode='chord',
             melody_tol=database.minor_seventh,
             chord_tol=database.major_sixth,
             get_off_overlap_notes=True,
             get_off_same_time=True,
             average_degree_length=8,
             melody_degree_tol='B4')
```

- current_chord: the chord type you want to separate the main melody from the chord notes (a chord type itself can store a complete piece for a single instrument, such as a piano piece)

- mode: an expression of what is returned after separating the main melody, mode='index' returns the position of all the main melody notes in x (the first note), mode='notes' returns a list of all the main melody notes, mode='chord' returns a new chord type composed of all the main melody notes, whose note intervals The note spacing is recalculated so that it is the same as the spacing of the remaining notes after the chord notes are actually removed.

- melody_tol: This parameter is the melody tolerance threshold that I described above when describing my algorithm.

- chord_tol: This parameter is the chord tolerance I described above when describing my algorithm, you can set your own interval size, the default value is major_sixth.

- get_off_overlap_notes: This parameter is used to deal with the situation where some MIDI files have a lot of overlapping notes (homophonic overlap) that may cause the algorithm to fail to achieve the desired effect.

- get_off_same_time: if set to True, for each set of notes that starts at the same time, only keep the note with the highest pitch.

- average_degree_length: The current average pitch of the algorithm is calculated for each passing bar. The default value is 8 bars.

- melody_degree_tol: This is the third tolerance threshold I recently added after improving the algorithm, called melody degree tolerance. It allows for a more flexible separation of the melody and chord notes, and improves the purity of the separation of the melody and chord notes. The default value is B4.

Next I will describe how to do this.

First we prepare the piece, which can be any MIDI file, and then use the `read` function to read the track you want to separate. for example

```python
a, bpm, start_time = read('example.mid').merge()
```

The `read` function returns a piece type, using the `merge` function you can get a tuple with 3 elements, respectively the chord type of the piece, the tempo of the piece (BPM), and the start time of the piece (in bars)

Then we can use

```python
example_melody = a.split_melody(mode='chord')
```

Then we can use the `play` function or `write` function to write the main melody into a new MIDI file (the name of the `play` function can be left out, the name of the output MIDI file is temp.mid by default)

```python
play(melody, bpm, name='example melody.mid')
```

or

```python
write(melody, bpm, name='example melody.mid')
```

Then the MIDI file of the main melody of the example will be generated in the musicpy folder, which you can take to DAW or use on other occasions as needed.

You can use the `split_chord` function to extract the chord notes of a piece, which actually does the same thing as `split_melody`, similar to the above, except that the output MIDI file becomes the chord notes of a piece, for example, called `example_chords`.

Using the `split_all` function, you can get both the main melody and the chord notes of a piece in 2 chord types, and everyone can output them separately as new MIDI files in the same way. The start times of melody and chord notes are stored in the results, so when you want to merge the melody and chord notes into a new piece after making changes, you can write

```python
example_melody & example_chords
```

to get a new piece that starts at the same position as the previous piece.

Note that the 2 chord types which are the main melody and chords returned by `split_all` function both contain the same pitch_bend and tempo types as the piece before the separation in their notes list, because the non-note types are not involved in the separation algorithm, but are retained in the separation result. So when you merge the main melody with the chord notes, please clear the pitch_bend and tempo types of one of the chord types before merging, to avoid duplication of the pitch_bend and tempo types, and it is recommended to use the `only_notes` function that comes with the chord type to get a chord type containing only the note types. For example:

```python
example_melody & example_chords.only_notes()
```

The start times of the pitch_bend and tempo types reserved for the main melody and chord notes are relative to the original piece, so in case where the main melody or chord notes do not appear from the very beginning of the original piece, the start times of the pitch_bend and tempo types may be off when the main melody or chord notes are played individually.

If you want to get the precise start times of pitch_bend and tempo types relative to the main melody or chord notes, you can apply the start times of melody and chord notes to the pitch_bend and tempo types inside them by using `apply_ start_time_to_changes` function of the chord type (you don't need to apply the start times if you want to merge them into the original piece). For example:

```python
example_melody_individual = copy(example_melody)
example_chords_individual = copy(example_chords)
example_chords_individual.apply_start_time_to_changes(example_chords.start_time)
example_melody_individual.apply_start_time_to_changes(example_melody.start_time)
```

For different pieces, setting different 3 tolerance thresholds may give better results. The default 3 tolerance thresholds will work for most pop or jazz piano pieces.

I have also included this algorithm as one of the features in an intelligent piano software I wrote, where you can choose to separate the main melody and listen to the chords only after selecting the MIDI file.

