# Basic syntax of scale type



## Contents

- [Construct scales according to interval relationships or key names](#construct-scales-according-to-interval-relationships-or-key-names)
- [Modulation](#modulation)
- [Selecting the chords according to the number of steps in the scale](#selecting-the-chords-according-to-the-number-of-steps-in-the-scale)
- [Get melody from scale degrees by numbered musical notation](#get-melody-from-scale-degrees-by-numbered-musical-notation)
- [modulation according to the circle of fifths](#modulation-according-to-the-circle-of-fifths)
- [Selecting chords of a scale according to the harmony function](#selecting-chords-of-a-scale-according-to-the-harmony-function)
- [Get the relative and parallel keys of a mode](#get-the-relative-and-parallel-keys-of-a-mode)
- [The up/down transposition of a scale (overall up/down or up/down of individual notes)](#the-updown-transposition-of-a-scale-overall-updown-or-updown-of-individual-notes)
- [Get the mode of a scale](#get-the-mode-of-a-scale)
- [Get the reverse of a scale](#get-the-reverse-of-a-scale)
- [Parsing of a scale (mode) name](#parsing-of-a-scale-mode-name)
- [Extract chord progressions by scale degrees](#extract-chord-progressions-by-scale-degrees)
- [Get a list of all the notes of a scale according to the standard notation](#get-a-list-of-all-the-notes-of-a-scale-according-to-the-standard-notation)
- [Get the negative harmony of a piece of music about a key](#get-the-negative-harmony-of-a-piece-of-music-about-a-key)
- [Get the retrograde and inversion of a piece of music](#get-the-retrograde-and-inversion-of-a-piece-of-music)
- [Detecting whether a note or chord exists within a scale](#detecting-whether-a-note-or-chord-exists-within-a-scale)
- [Scale type extracts chords according to scale degrees expressed using Roman numerals](#scale-type-extracts-chords-according-to-scale-degrees-expressed-using-Roman-numerals)
- [Generate chord progressions from scale types](#generate-chord-progressions-from-scale-types)
- [Get note from scale degree](#get-note-from-scale-degree)
- [Find the scale degree of a note in a scale](#find-the-scale-degree-of-a-note-in-a-scale)
- [Find the standard note name of a note in a scale](#find-the-standard-note-name-of-a-note-in-a-scale)
- [Get the note with a interval from a note in a scale](#get-the-note-with-a-interval-from-a-note-in-a-scale)



## Construct scales according to interval relationships or key names

Scale type can represent a specific scale.

Using this class, you can quickly build a scale according to the interval of the notes, for example, the arrangement of notes in a major key is all-all-half-all-all-half (all for whole notes, half for semitones).

So if you want to build a C major scale, you can write

```python
scale('C5', interval=[2,2,1,2,2,2,1])
```

This gives us the C major scale with C5 as the root note.

Of course, for most of the well-known scales, you only need to enter the name of the scale. For example

```python
scale('C5', 'major')
```

and you get the C major scale with C5 as the root note.

```python
scale('C5', 'minor')
```

Get the C minor scale with C5 as the root note.

The scaleTypes inside database.py are all the scales that musicpy comes with, and users can also customize the scales themselves.



## Modulation

You can use `modulation` method of scale type to convert a chord type from a scale into another scale. For example, if you have a chord type in A major, and you want to convert it to A minor, you can write:

```python
current_chord = chord('A,B,C#,D,E') % (1/8, 1/8)

>>> current_chord
chord(notes=[A4, B4, C#5, D5, E5], interval=[1/8, 1/8, 1/8, 1/8, 1/8], start_time=0)

new_chord = current_chord.modulation(scale('A', 'major'), scale('A', 'minor'))

>>> new_chord
chord(notes=[A4, B4, C5, D5, E5], interval=[1/8, 1/8, 1/8, 1/8, 1/8], start_time=0)
```



## Selecting the chords according to the number of steps in the scale

For example, if the 4th triad of C major is F and the 6th triad is Am, you can use `pick_chord_by_degree` function, where the parameter `degree1` is the chord degree, starts from 0, `num` is the number of notes to pick, and `step` is the number of notes in the scale to span each time. By default,

```python
scale('C', 'major').pick_chord_by_degree(4)
```

will get the 5th triad of C major scale G major triad.

The function`pick_chord_by_degree`, which picks the corresponding chord according to the step of the scale, can be abbreviated as a scale type followed by parentheses with arguments inside.

Advanced syntax:

```python
S('C major')(4)
```

`pick_chord_by_degree` has many other parameters that can be used to extract natural triads, natural seventh chords, natural ninth chords, etc. You can also set whether to stack in thirds or other degrees each time, as well as set the note length and note spacing of the returned chord type, etc.

The parameters of `pick_chord_by_degree` are, in order

```python
degree1: the degree of the chord to pick, as a positive integer
duration: the length of the returned note of the chord type, default is 1/4
interval: the interval of the chord type, default is 0
num: the number of notes of the extracted chord, the default value is 3, i.e. the natural triad is extracted, if it is 4, the natural 7th chord is extracted, and so on.
step: the number of steps of the extracted chord to find the next chord note in the scale, the default value is 2, i.e. stacking by thirds.
```

Since `pick_chord_by_degree` function can be abbreviated to a pair of parentheses, you can add a pair of parentheses directly after a scale type, and then write the parameters of `pick_chord_by_degree` function inside, which is more concise, for example

```python
# Build a C major scale called c_major_scale
c_major_scale = S('C major')
# Extract the 3rd degree natural 7th chord of the C major scale
a = c_major_scale(2, num=4)
>>> print(a)
# Show the chord. The natural 7th chord of the 3rd degree of the C major scale is the Em7 chord
chord(notes=[E4, G4, B4, D5], interval=[0, 0, 0, 0], start_time=0)
```



## Get melody from scale degrees by numbered musical notation

You can use `get` method of scale type to obtain a melody which is derived from the scale by scale degrees. This makes writing a melody easier as you are using numbers relative to a scale instead of absolute pitches, you only need to consider scale degrees when using this method.

The syntax is similar to numbered musical notation:

1. Use 1 to 7 as scale degree to get a note from current scale (1-based).
2. Add `#` and `b` after the number to raise or lower the note by 1 semitone, like `1#`.
3. Add `.` with an integer n after the number to raise or lower the note by n octaves, like `1.1`, `2.-1`.
4. Use `o` with a number to set current octave number, like `o5`.
5. Use `;` to group a set of notes that plays at the same time, like `1;3;5`.
6. Settings block like chord type is also supported, so you can set the duration and interval of each note individually.
7. Use `r` as rest and `-` as continue symbol.
8. Only a string is accepted, you need to write all notes in a string, use `,` to separate notes.

```python
get(current_ind,
    default_duration=1 / 8,
    default_interval=1 / 8,
    default_volume=100,
    pitch_mode=0)

# current_ind: a string which contains all the notes you want to get

# default_duration: default duration of each note

# default_interval: default interval of each note

# default_volume: default volume of each note

# pitch_mode: if set to 0, when choose scale degree from 1 to 7, the octave number is fixed, so there are cases that higher scale degree has lower pitch than lower scale degree in some scales; if set to 1, only the 1st scale degree's octave number is fixed, the higher scale degree's pitch will be calculated from the scale intervals
```

Here is an example of how to use `get` method of scale type.

```python
>>> S('C major').get('1,1,5,5,6,6,5,-,4,4,3,3,2,2,1,-')
chord(notes=[C4, C4, G4, G4, A4, A4, G4, F4, F4, E4, ...], interval=[1/8, 1/8, 1/8, 1/8, 1/8, 1/8, 1/4, 1/8, 1/8, 1/8, ...], start_time=0)

>>> S('A#5 major').get('1,7,5,3,2,3[.16;.], o4, 1[.8.;.],7,1,-, o3, 6;3.1;1.1, 6;3.1;1.1, 5;7.1;2.1, 5;7.1;2.1, 6;3.1;1.1, -')
chord(notes=[A#5, A5, F5, D5, C5, D5, A#4, A4, A#4, G3, ...], interval=[1/8, 1/8, 1/8, 1/8, 1/8, 1/16, 3/16, 1/8, 1/4, 0, ...], start_time=0)
```



## Modulation according to the circle of fifths

The `fifth` function transposes the current mode by a circle of fifths, where the parameter `step` is the number of steps along the circle of fifths, if step is greater than 0 then it will be clockwise, if step is less than 0 then it will be counterclockwise. `fourth` function is the same, but with a circle of fourths instead.



## Selecting chords of a scale according to the harmony function

The scale class has a built-in set of more complete tonal chord picking functions. For example, if you have a mode A, then the major chord of mode A is

```python
A.tonic_chord()
```

The subordinate chord is

```python
A.subdom_chord()
```

The subordinate chord is 

```python
A.dom_chord()
```

and so on. To get the secondary dominant chord of a degree within a mode you can use

```python
A.secondary_dom(degree)
```

where degree is the number of steps.



## Get the relative and parallel keys of a mode

The `relative_key` function gets a relative key, such as a relative minor key for a major key or a relative major key for a minor key.

The `parallel_key` function gets the major or minor key with the same tonic.



## The up/down transposition of a scale (overall up/down or up/down of individual notes)

The `up` and `down` functions for note types, chord types and their advanced syntax are also available for scales.



## Get the mode of a scale

Use `a.inversion(n)` or `a / n` to get the nth mode of a scale type `a`.

```python
current_scale = S('C major')
>>> current_scale.inversion(2)
[scale]
scale name: D4 dorian scale
scale intervals: [M2, m2, M2, M2, M2, m2, M2]
scale notes: [D4, E4, F4, G4, A4, B4, C5, D5]

>>> current_scale / 3
[scale]
scale name: E4 phrygian scale
scale intervals: [m2, M2, M2, M2, m2, M2, M2]
scale notes: [E4, F4, G4, A4, B4, C5, D5, E5]
```



## Get the reverse of a scale

Use `a.reverse()` or `~a` to get the scale from the reverse of a scale type `a`.

```python
current_scale = S('C major')
>>> ~current_scale
[scale]
scale name: C4 phrygian scale
scale intervals: [m2, M2, M2, M2, m2, M2, M2]
scale notes: [C4, Db4, Eb4, F4, G4, Ab4, Bb4, C5]
```



## Parsing of a scale (mode) name

The `to_scale` function allows you to parse a scale (mode) by entering its name directly, returning the type of scale corresponding to the entered scale (mode) name, the octave of the tonic (also the octave of the scale) is determined by the second parameter pitch, the default value is 4. For example

```python
to_scale('C major')
```

You can get the major scale with C5 as the tonic.

```python
to_scale('G lydian', 6)
```

You can get the lydian scale with G6 as the tonic.

The abbreviation is

```python
S('C major')
```

S is the initial capitalization of the scale



## Extract chord progressions by scale degrees

Using the built-in `pattern` function of the scale type, you can extract the chord progression of a mode by entering a string or integer representing the chord scale, for example

```python
S('C major').pattern(6451)
```

You can get a list of chords in the 6,4,5,1 progression of the C major scale.

It is also possible to write

```python
S('C major').pattern('6451')
```

Other parameters of the `pattern` function (in order of precedence):

* duration: the length of each note of the chord in the chord progression, the default value is 0.25, which means that the notes are 1 bar long.

* interval: the interval between the notes of each chord in the chord progression, the default value is 0, i.e. the notes of each chord are played together.

* num: the number of notes extracted for each chord in the mode by step, e.g. num = 3 is a triad, num = 4 is a 7th chord, etc.

* step: how many notes in the scale are skipped in each step of the chord extraction, the default value is 2, for example, the 1st natural triad C E G of C major scale, each time two notes in the scale are skipped, C, two notes to E, then two notes to G. (Here, skipping a few notes means taking a few steps forward, each step is to go to the next note in the scale)

Advanced syntax:

```python
S('C major') % 6451
S('C major') % (6451, 1/2, 0)
```



## Get a list of all the notes of a scale according to the standard notation

When we represent a scale, most commonly the major and minor scales, we usually prefer to use the standard notation to represent each note of the scale, which is a seven-note scale in which  C, D, E, F, G, A, B, all seven letters of the alphabet must appear once, and can be preceded by sharp signs, flat signs, double sharp signs, double flat signs, natural signs, etc., as long as they are equivalent to the corresponding notes in the scale. It doesn't matter if it's not a natural note like `A#`, `Eb`. For example, the notes of the Eb major scale is represented by the standard notation

```
Eb, F, G, Ab, Bb, C, D
```

The notes of the G# major scale are represented by the standard notation

```
G#, A#, B#, C#, D#, E#, Fx
```

In musicpy, if you just use the scale function or the S function to build a scale, then the names of the scales are all represented by default according to the standard 12-note notation in database.py.

For example, if you type `S('D# major')` the first note is `D#`, and if you type `S('Eb major')` the first note is `Eb`, then all subsequent notes are sharp signed notes only.

For example, the sound of `S('D# major')` is `scale notes: [D#4, F4, G4, G#4, A#4, C5, D5, D#5]`,  `S('Gb major')` is `scale notes: [Gb4, G#4, A#4, B4, C#5, D#5, F5, F#5]`. This representation is intended to minimize the amount of arithmetic involved in processing note types, and to try to keep  note names have one and only one, making the arithmetic logic more concise. However, if you want to get the scale's notes according to the standard notation of note names, you can use the built-in function `standard` for the scale type, for example

```python
>>> S('Gb major').standard()
['Gb', 'Ab', 'Bb', 'Cb', 'Db', 'Eb', 'F']
>>> S('D# major').standard()
['D#', 'E#', 'Fx', 'G#', 'A#', 'B#', 'Cx']
>>> S('Eb major').standard()
['Eb', 'F', 'G', 'Ab', 'Bb', 'C', 'D']
>>> S('C# minor').standard()
['C#', 'D#', 'E', 'F#', 'G#', 'A', 'B']
>>> S('Ab minor').standard()
['Ab', 'Bb', 'Cb', 'Db', 'Eb', 'Fb', 'Gb']
>>> S('C minor').standard()
['C', 'D', 'Eb', 'F', 'G', 'Ab', 'Bb']
```

There is also the `relative_note` function, which takes two arguments, both of which are strings that represent the sound name, and can return the name of the subsequent sound using the standard sound name notation for the preceding sound name. For example

```python
>>> relative_note('C', 'D')
Dbb
>>> relative_note('A', 'A#')
A♮
>>> relative_note('F', 'E')
E#
>>> relative_note('B', 'C')
Cb
>>> relative_note('G', 'F')
Fx
```



## Get the negative harmony of a piece of music about a key

The `negative_harmony` function converts a chord type to a negative harmony in the specified key, for example, if you want to convert the chord type a to a negative harmony in C major, the syntax is

```python
negtaive_harmony(S('C major'), a)
```

Gets the new chord type a after converting it to the negative harmony of the C major scale.
The 1st parameter is the scale type, the 2nd parameter is the chord type you want to convert, the 3rd parameter is whether to rearrange the chords from lowest to highest after the conversion (for example, if you just convert a chord, it is important to rearrange the pitch), the default value is False, and the 4th parameter is whether to return the mapping dictionary for the negative harmony of the specified key after the conversion, if it is True, the mapping dictionary will be returned directly and no chord type will be converted, the default value is False.

In addition, the second parameter can be left out, and the default value is None. If the second parameter is left out and the fourth parameter is False, the scale type of the negative harmony of the specified key is returned, i.e. the mirror scale.

Another way to write it is

```python
a.negative_harmony(S('C major'))
```

The abbreviated way is

```python
a @ S('C major')
# If you have other arguments, you can use a tuple
a @ (S('C major'), True)
```



## Get the retrograde and inversion of a piece of music

There are two very important concepts in the twelve-tone technique, retrograde and inversion, I won't describe the specific definition here, you can go online to check, here's how to write.

```python
# The retrograde of the chord type a
a.retrograde()
# The inverse of chord type a
a.pitch_inversion()
# Combine the two
a.retrograde().pitch_inversion()
a.pitch_inversion().retrograde()
```



## Detecting whether a note or chord exists within a scale

If we want to see if a note or chord is contained within a scale, we can use the `in` keyword to determine whether a string indicating the name of a note (with or without the pitch), note type or chord type is contained within a scale type, for example

```python
>>> print('A' in S('C major'))
True

>>> print('Ab' in S('C major'))
False

>>> print('E4' in S('C major', 5)) 
True
# The actual pitch number of the note will be ignored and True will be returned as long as the note name is in the scale, if you want to consider the pitch number,
# you can use 'E4' in S('C major', 5).get_scale()

>>> print('A#' in S('Bb major')) # If it's a homophonic allophone (under the standard of the twelve averages), it will be judged as contained and return True
True

>>> print(N('A#') in S('Bb major'))
True

>>> print(N('C5') in S('Bb major', 5))
True

>>> print(C('Am7') in S('C major'))
True

>>> print(C('Em9') in S('C major'))
False
```



## Scale type extracts chords according to scale degrees expressed using Roman numerals

You can use the `get_chord` function of the scale type to extract chords according to scale degrees expressed using Roman numerals.

```python
get_chord(degree, chord_type=None, natural=False)

# degree: string of chord grades using Roman numerals, e.g.: 'IM7', 'ii7', 'IIm7', 'V7'.
# can be a chord that does not occur naturally in the type of scale being called. It can also be a combination of numbers and chord names, e.g.: '1M7', '2m7'

# chord_type: You can write the number of steps and the chord type separately, where degree is a string indicating the number of steps, which can be Roman numerals, e.g.: 'I', 'II',
# can also be numbers, e.g. '1', '2', chord_type is the chord type, e.g. 'M7', 'm7'

# natural: whether or not the chord occurs naturally in the scale type, if True
# If True, then if the chord obtained is not a chord that can occur naturally in the scale type, then
# The natural chord is extracted within the scale type according to the number of notes and steps of the chord, e.g. 'Im7' in C major becomes 'IM7' after the natural conversion.
# The default value is False

Cmajor_scale = S('C major')

>>> Cmajor_scale.get_chord('IM7')
chord(notes=[C4, E4, G4, B4], interval=[0, 0, 0, 0], start_time=0)

>>> Cmajor_scale.get_chord('ii', '7')
chord(notes=[D4, F4, A4, C5], interval=[0, 0, 0, 0], start_time=0)
```



## Generate chord progressions from scale types

```python
chord_progression(chords,
                  durations=1 / 4,
                  intervals=0,
                  volumes=None,
                  chords_interval=None,
                  merge=True)

# chords: A list of strings representing chord progressions, either Roman numerals or a combination of numerals and chord names.
# or a list or tuples with 2 values, Roman numerals/numbers, chord names.

# durations: note lengths for each chord type

# intervals: note intervals for each chord type

# volumes: the volume of each note of each chord type

# chords_interval: the interval in bars between adjacent chord types

# merge: whether to merge into a new chord type, True returns the merged chord type, False returns a list of chord types, default is True

Cmajor_scale = S('C major')

>>> Cmajor_scale.chord_progression(['IM7', 'Vsus', 'vi7', 'IVM7'])
chord(notes=[C4, E4, G4, B4, G4, C5, D5, A4, C5, E5, ...], interval=[0, 0, 0, 1/4, 0, 0, 1/4, 0, 0, 0, ...], start_time=0)

>>> Cmajor_scale.chord_progression([('I', 'M7'), ('V', 'sus'), ('vi', '7'), 'IV'], intervals=[1/8, [1/8,1/8,1/4], 1/8, 1/8])
chord(notes=[C4, E4, G4, B4, G4, C5, D5, A4, C5, E5, ...], interval=[1/8, 1/8, 1/8, 1/8, 1/8, 1/8, 1/4, 1/8, 1/8, 1/8, ...], start_time=0)

>>> Cmajor_scale.chord_progression(['1M7', '5sus', '6m7', '4M7'])
chord(notes=[C4, E4, G4, B4, G4, C5, D5, A4, C5, E5, ...], interval=[0, 0, 0, 1/4, 0, 0, 1/4, 0, 0, 0, ...], start_time=0)
```



## Get note from scale degree

You can use the scale type method `get_note_from_degree` to get note by scale degree, and the returned value is note type.

```python
get_note_from_degree(degree, pitch=None)

# degree: the degree of the scale, starting from 1, if it is higher than 7 then it will be counted as a higher octave
# pitch: initial number of octaves, if not set then the octave of the scale itself is used

current_scale = S('C major')

>>> current_scale.get_note_from_degree(1)
C4

>>> current_scale.get_note_from_degree(9)
D5

>>> current_scale.get_note_from_degree(5, pitch=5)
G5
```



## Find the scale degree of a note in a scale

You can use the scale type method `get_scale_degree` to find the scale degree of a note in a scale, the note can be a string or a note type.

```python
current_scale = S('C major')

>>> current_scale.get_scale_degree('G')
5
```



## Find the standard note name of a note in a scale

```python
current_scale = S('G# major')

>>> current_scale.get_standard_notation('G')
'Fx'
```



## Get the note with a interval from a note in a scale

You can use the method `get_note_with_interval` of the scale type to get a note in a scale with a interval from a note.

```python
get_note_with_interval(current_note, interval, standard=False)

# current_note: the note to be counted, can be a string or a note type

# interval: the number of notes, starting from 1, 1 being one degree

# standard: whether or not the returned note name corresponds to the standard name of the scale

current_scale = S('C major')

>>> current_scale.get_note_with_interval('C4', 3)
E4

>>> current_scale.get_note_with_interval('C4', 9)
D5

>>> current_scale.get_note_with_interval('C4', -5)
F3
```

