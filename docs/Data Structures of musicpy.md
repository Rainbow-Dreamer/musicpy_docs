# Data structures of musicpy

Musicpy is a language that allows you to express the notes, rhythms, etc. of a piece of music in a very simple syntax, and can be easily exported to a MIDI file format. There is a lot of music theory involved in this library, so I recommend that you understand at least some of it before using it. If you have a good understanding of music theory, you should be able to use it quickly after reading the tutorials I have written.

**Special reminder: In musicpy, except some music theory functions, the indexing of the music theory types starts from 0 (0-based), such as chord type, scale type, piece type, etc.**

In musicpy, the most basic data structures are note, chord and scale. These classes are the basis of music code. musicpy has a lot of music theory functions, so let's start with the most basic ones.  
First of all, we need to declare that all note lengths and note intervals in musicpy are in 4/4 time signature, which can be integers, floats or fractions. The tempo is in BPM, which is the number of beats you can play in 1 minute. In 4/4 time signature, 1 bar is 4 beats, 1 beat is 1/4 bar, that is, the number of bars played per minute is BPM/4, the time length of 1 bar in seconds is 240/BPM. All bars in musicpy are treated as 4/4 time signature.

In this chapter, we will introduce some of the most important and most commonly used data structures of musicpy.



## Contents

  - [note](#note)
  - [chord](#chord)
  - [scale](#scale)
  - [piece](#piece)
  - [track](#track)
  - [drum](#drum)
  - [tempo](#tempo)
  - [pitch_bend](#pitch_bend)
  - [pan](#pan)
  - [volume](#volume)
  - [rest](#rest)
  - [beat](#beat)
  - [rest_symbol](#rest_symbol)
  - [continue_symbol](#continue_symbol)
  - [rhythm](#rhythm)



## note

Initializing an instance of note just gives you a note name (such as C, Eb, A#, could also be lowercase) and an octave number (an integer), and now you can use that note to do anything in the music. You can also set the duration and volume of the note. The note length defaults to 0.25 (1/4 bar) and the volume defaults to 100.

For example. 

```python
a = note('A', 5)
```

This assigns the note A5 to a, which is represented as follows.

```python
A5
```

represents the note name and the octave number, which together determine the pitch.

When you use `note` to construct note types, the octave number could be omitted, the default value is 4.

### The composition of note type

```python
note(name,
     num=4,
     duration=0.25,
     volume=100,
     channel=None)
```

- name: the name of the note (C, D, E, G#, Gb, ...) , a string representing the name of the note

- num: the octave number, which is an integer, it determines the pitch of a note together with the note name

- duration: the length of the note in bars, e.g. duration = 1 means that the note is 1 bar long, the default value is 0.25

- volume: the intensity of the note, corresponding to the intensity of the note in the MIDI file, ranging from 0 to 127, with a default value of 100

- channel: the MIDI channel number of the note, when write to MIDI file, if channel is not None, then it will write to the corresponding channel

- degree: the pitch number of a note, C0 is 12, each octave has 12 notes, for example, the pitch number of C1 is 24, the pitch number of D1 is 26, the pitch number of C5 is 72, and so on, each note will automatically calculate its own pitch number corresponding to its name and octave number and store it, which is very useful afterwards. (The pitch number is the MIDI number that corresponds to the note in the common standard for MIDI files)

Because of this basic property of pitch number, the note class itself is equivalent to a pure number, i.e. it can be used as a pure number, and the chord class is a collection of note classes. It also means that the chord class itself is equivalent to a set of all numbers, and can be treated as a vector or even a matrix (For example, the concatenation of multiple chords can be seen as a concatenation of vectors, and therefore as a matrix). Therefore, the data structures of this language are designed in such a way that notes, chords, and scales can be used for mathematical operations. For example, operations in the field of linear algebra, operations in the field of discrete mathematics, and so on. It is also possible to build a set of algorithms for musical logic based on the data structure of the language. It is possible to combine pure mathematical logic to perform various aspects of musical analysis. Many experiments in the field of modern music, such as sequentialism, incidental music, and postmodernism (e.g., minimalist music), have been developed. Post-modernist music (e.g., minimalist music) can all theoretically be created rigorously on the basis of the purely digital data structures of this language. Even without mentioning experimental music, the language can be used to write any classical, jazz, or pop music.



## chord

This should be the most important class. In musicpy, a chord class is defined as "a set of notes", which is perhaps more general than the definition of a chord in music theory, because a complete piece of music can fit into a chord class, and it does in this library.

You can use the chord type to represent a chord, and also a melody, or the combination of both.

To initialize a chord instance, you just need to give a list of notes. You can also set the duration (the length of all notes), the interval (the interval between notes, expressed as a list). One of the more user-friendly things here is that you don't need to initialize the note list with the note class first. Instead, you can just write the names of the notes (strings).

For example:

```python
Cmaj7 = chord(['C5', 'E5', 'G5', 'B5'])
```

This writes a C major seventh chord.

(There are many more ways of constructing chord types, which could be written more concisely than here, you can refer to the basic syntax chapters for details)

We can play this chord with the play function:

```python
play(Cmaj7)
```

This C major seventh chord is represented as follows.

```python
[C5, E5, G5, B5] with interval [0, 0, 0, 0]
```

If you want to create a melody in musicpy, the logic is the same as creating a chord, here is an example

```python
melody = chord('C5, C5, G5, G5, A5, A5, G5, F5, F5, E5, E5, D5, D5, C5',
                duration=[1/8, 1/8, 1/8, 1/8, 1/8, 1/8, 1/4, 1/8, 1/8, 1/8, 1/8, 1/8, 1/8, 1/4],
                interval=[1/8, 1/8, 1/8, 1/8, 1/8, 1/8, 1/4, 1/8, 1/8, 1/8, 1/8, 1/8, 1/8, 1/4])
```

You can play this melody, for example, at 80 BPM

```python
play(melody, bpm=80)
```

### The composition of chord type

```python
chord(notes,
      duration=None,
      interval=None,
      rootpitch=4,
      other_messages=[],
      start_time=None)
```

- notes: a list of all the notes of the chord (or piece)

- duration: The length of each note of the chord, the default value is None, or the length of the note itself if it is None, or the length of the note if it is an integer, a float or a list

- interval: The interval between each two consecutive notes, in bars, as a list of note intervals (if initialized as an integer or a float, set all intervals to this integer or float) The default value is None, if the interval is not specified, all of the intervals of notes are 0 by default.  
  **Please note, the definition of the interval of notes here is the interval from the beginning of current note to the beginning of the next note.**

- rootpitch: If the element of the note list is not a note type, but a string representing a note, it will be converted to a note type using the to_note function. If the note string does not have an octave, but only the name of the note, the rootpitch will be used as the octave of the note, with a default value of 4

- start_time: The start time of the chord type in bars, you can understand it as the rest from the beginning to the beginning of the first note, the default value is 0  
  **Please note that when a chord type is placed as a track in the track list of a piece type, the `start_time` property is ignored and the start time of each track's chord type is based on the `start_times` list of the piece type, except when the chord type is merged (e.g. the piece type itself is merged into a chord type) or when the chord type is played separately from the track list, the `start_time` will still work. It is recommended that the `start_time` be set to 0 for all chord types placed as tracks in the piece type, to avoid possible conflicts between the start time of the chord type and the piece type.**



## scale

This class can represent a specific scale. Using this class you can quickly build a scale according to the interval of the notes, for example, the arrangement of the major notes is whole whole half whole whole whole half (whole for whole step, half for half step), so if you want to build a C major scale, you can write

```python
scale('C5', interval=[2, 2, 1, 2, 2, 2, 2, 1], name='major')
```

This gives us the C major scale with C5 as the root note, expressed as follows

```python
[scale]
scale name: C5 major scale
scale intervals: [2, 2, 1, 2, 2, 2, 1]
scale notes: [C5, D5, E5, F5, G5, A5, B5, C6]
```

Of course, for most of the well-known scales, you only need to enter the name of the scale. For example

```python
scale('C5', 'major')
```

and you get the C major scale with C5 as the root note.

```python
[scale]
scale name: C5 major scale
scale intervals: [2, 2, 1, 2, 2, 2, 2, 1]
scale notes: [C5, D5, E5, F5, G5, A5, B5, C6]
```

For example

```python
scale('C5', 'minor')
```

Get the C minor scale with C5 as the root note.

```python
[scale]
scale name: C5 minor scale
scale intervals: [2, 1, 2, 2, 2, 1, 2, 2]
scale notes: [C5, D5, D#5, F5, G5, G#5, A#5, C6]
```

etc.
The scaleTypes in database.py are all musicpy's own scales, but users can also customize their own scales.

### The composition of scale type

```python
scale(start=None,
      mode=None,
      interval=None,
      notes=None)
```

- start: The main note of the scale (start note), which is a note class

- mode: The name of the scale, such as major, minor, dorian, lydian, etc.

- interval: The interval of a scale, 1 for a seminote, 2 for a whole note, 3 for an increasing second, and so on, in the form of a list, e.g. the interval of a major scale is [2,2,1,2,2,2,1].

- notes: The list of notes, see the list of notes in the chord class, a scale itself is a definite set of notes, so notes is all the notes in a scale instance, could also be a chord instance

The built-in methods of the scale class are rich in musical logic functions, such as harmonic functions, tonic chord, dominant chord, subdominant chord, secondary dominant chord of a degree, and so on. Extraction of natural triads and natural seventh chords from the scale in a certain number of steps, modulation in a circle of fifths in a certain number of steps in clockwise or counterclockwise direction, relative key, parallel key, negative harmony (mirror harmony), chord progression extraction by step, modulation to a specified key, derivation of the key from a degree of the note and get the standardized note name notation (the sharp and flat notation of the note names in each key), etc. I will explain the details in the section on basic syntax and how to use it.



## piece

When we need to write a piece with multiple tracks and each track with its own instrument, we could use the piece type. An instance of the piece type can store any number of tracks, and each track can have its own instrument, and the instrument can be changed at any time via MIDI messages.

There are detailed introductions of piece type in `Basic syntax of piece type`.



## track

The track type is a data structure used to store information about a single track in a piece type. Although the piece type itself does not consist of track types, the track type can be extracted from or added to the piece type, and the track type itself can be played and written to MIDI files directly.

There are detailed introductions of track type in `Basic syntax of track type`.



## drum

The drum type is a special data structure dedicated to representing drum beats, also known as percussion rhythms. The drum type is essentially very similar to the chord type, which also internally stores note types that can correspond to different types of drum beats on the drum track when written to a MIDI file. The most important feature of the drum type is that it can be built using a unique syntax for writing drums. This syntax is relatively simple, does not require consideration of which note corresponds to which type of drum beat in the MIDI file, and supports a certain degree of batch processing.

There are detailed introductions of drum type in `Basic syntax of drum type`.



## tempo

The tempo type can be used to change the tempo of the current piece in real time (BPM). Instances of the tempo type can be inserted as note types into chord types, and the tempo of the current piece can be changed between any two note types, or you can set your own start time in bars, so that you can set a more precise start time for the tempo change. The tempo types can be applied to chord types, piece types and track types, and are also stored as MIDI events in the MIDI file after being written to it.

There are detailed introductions of tempo type in `Basic syntax of tempo type`.



## pitch_bend

The pitch_bend type can be used to change the pitch of notes of a fragment in real time, allowing very subtle pitch changes. Like the tempo type, the pitch_bend type can be inserted into a chord type as a note type, or you can set your own time, in bars, when the pitch change begins. The pitch_bend types can be applied to chord types, piece types and track types, and are also stored as MIDI events in the MIDI file after being written to it.

There are detailed introductions of pitch_bend type in `Basic syntax of pitch_bend type`.



## pan

The pan type is a type specifically used to store the position of the left and right channel mixing. Using the pan types in the piece type allows you to set the pan of each track.

There are detailed introductions of pan type in `Basic syntax of pan type`.



## volume

The volume type is a type dedicated to storing the overall volume level of a track. Using the volume type in the piece type allows you to set the overall volume level of each track.

There are detailed introductions of volume type in `Basic syntax of volume type`.



## rest

Currently, the definition of a rest in musicpy is a data structure that can be passed in during chord type construction and merging, but does not exist as a note in the note list of the chord type, because the interval between adjacent notes is already defined by the list of note intervals of the chord type, so there is not much need for rests to exist in the chord type at this time. When you pass in rests when building a chord type, the rests are reflected in the list of note intervals, but the rests themselves are not added to the note list of the chord type. Currently the note list of chord types can contain only three data structures: note type, tempo type, and pitch_bend type.

### The composition of rest type

```python
rest(duration=1/4, dotted=None)
```

- duration: The duration of the rest in bars
- dotted: The number of dotted notes of the rest, the default value is None, which is not a dotted note

### Use rest when constructing chord types

```python
# add a half rest between the note D and E
C1 = chord(['C', 'D', rest(1/2), 'E'])

>>> C1
[C4, D4, E4] with interval [0, 0.5, 0]
```

### Use rest when merging chord types

```python
C1 = C('Cmaj7')
C2 = C('Dmaj7')
C3 = C1 | rest(1/2) | C2

# or using a number
# C3 = C1 | 1/2 | C2

>>> C3
[C4, E4, G4, B4, D4, F#4, A4, C#5] with interval [0, 0, 0, 0.75, 0, 0, 0, 0]
```



## beat

This is a data structure that represents meter in music theory, which is used in a rhythm.

A beat instance holds a single beat with a duration and a dotted number (in case it is a dotted beat).

You can form a complete rhythm with a list of beat instances.

### The composition of beat type

```python
beat(duration=1/4,
     dotted=None)
```

* duration: the duration of the beat in bars (without the dotted number)
* dotted: the dotted number of the beat, if it is None, the beat is not a dotted beat

You can use the `get_duration` method of the beat instance to get the actual duration of the beat by applying the dotted number.



## rest_symbol

This is a data structure that represents a rest in music theory, which could be used along with the beat type to form a rhythm.

The rest_symbol instances take the same parameters as the beat instances.

In practice, you can use `rest` instances interchangeably with `rest_symbol` instances, but it is recommended to use `rest_symbol` instances to form a rhythm, because there is a `continue_symbol` type that works together with it, it is better that their names are more standard.

```python
rest_symbol(duration=1/4,
            dotted=None)
```



## continue_symbol

This is a data structure that extends the previous note's duration and interval, which could be used along with the beat type to form a rhythm.

The continue_symbol instances take the same parameters as the beat instances.

```python
continue_symbol(duration=1/4,
                dotted=None)
```



## rhythm

This is a data structure that represents a rhythm, which is equivalent to a list of beats, rest symbols and continue symbols.

A rhythm instance can be applied to a chord instance to change the intervals and durations of a chord instance.

You can think of this data structure as a higher level abstraction of the drum type.

You can set a fixed total length when initializing a rhythm instance, then the durations of the beats you set will be divided equally according to the length and number of the beats.

You can also set the time signature for the rhythm, such as 4/4, 3/4, 5/4.

For simplification purposes, there is a rhythm pattern syntax which is somewhat similar to the drum pattern syntax to help you make a rhythm instance quicker.

You can add 2 rhythm instances to get a new combined rhythm instance, or multiply a rhythm instance by an integer to get a new repeated rhythm instance.

This data structure is inherited from list, so you can do the same operations as lists on the rhythm instances.

### The composition of rhythm type

```python
rhythm(beat_list,
       total_bar_length=None,
       beats=None,
       time_signature=None,
       separator=' ',
       unit=None)
```

* beat_list: a list of beat, rest_symbol and continue_symbol instances, or a string that matches a rhythm pattern syntax
* total_bar_length: the total length in bars of the rhythm, if set, the durations of the beats will be equally divided
* beats: when total_bar_length is set, the number of beats of the rhythm, you can set this to assign a fixed duration to each beat
* time_signature: the time signature of the rhythm, a list of the form [numerator, denominator], if not set, the time signature of the rhythm will be 4/4
* separator: the separator of the beats if `beat_list` is a string
* unit: if set, set the durations of all beats to the value



### The rhythm pattern syntax

Use `b` to represent a beat, `0` to represent a rest symbol, `-` to represent a continue symbol.

The beat symbols are separated by whitespaces by default.

if you want to set the duration of the beat, use `:` after the beat symbol, then write the duration after it, the duration supports the same syntax as the drum pattern syntax.

You can add `.` after a beat symbol to make the beat dotted, the dots could be one or more, which is the dotted number of the beat.

You can add settings block with keywords and values after a beat, which is similar to the drum pattern syntax. Note that if the separator is whitespace, there cannot be any whitespaces in the settings block. The supported keywords are:

```
r:n repeat the beat n times with the equally divided unit duration
R:n repeat the beat n times with the unit duration
b:n change the duration of the beat to the unit duration * n
```



### Apply rhythm to a chord instance

You can use `apply_rhythm` method of a chord instance to apply a rhythm instance, which will set the note intervals and durations of the chord instance according to the rhythm instance that is applied. If the number of the beats of the rhythm instance does not match with the number of the notes of the chord instance, the minimum of them will be chosen as the number of notes to apply the rhythm. This method returns a new chord instance.

Here is an example of creating a rhythm instance and apply the rhythm to a melody.

```python
rhythm1 = rhythm('b b b - b b b - b b b - b - b -', 2)
'''
equivalent to: rhythm1 = rhythm([beat(), beat(), beat(), continue_symbol(), beat(), beat(), beat(), continue_symbol(), beat(), beat(), beat(), continue_symbol(), beat(), continue_symbol(), beat(), continue_symbol()], 2)
'''

>>> print(rhythm1)
[rhythm]
rhythm: beat(1/8), beat(1/8), beat(1/8), continue(1/8), beat(1/8), beat(1/8), beat(1/8), continue(1/8), beat(1/8), beat(1/8), beat(1/8), continue(1/8), beat(1/8), continue(1/8), beat(1/8), continue(1/8)
total bar length: 2
time signature: 4 / 4

chord1 = chord('C5, D5, E5, E5, F5, G5, E5, D5, C5, A5, G5').apply_rhythm(rhythm1)

>>> print(chord1)
[C5, D5, E5, E5, F5, G5, E5, D5, C5, A5, G5] with interval [0.125, 0.125, 0.25, 0.125, 0.125, 0.25, 0.125, 0.125, 0.25, 0.25, 0.25]

```

