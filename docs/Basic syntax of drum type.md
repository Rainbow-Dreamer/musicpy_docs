# Basic syntax of drum type



## Contents

- [Added support for drum types, and a new set of syntax for writing drums](#added-support-for-drum-types-and-a-new-set-of-syntax-for-writing-drums)
- [Introduction to the construction and usage of drum types](#introduction-to-the-construction-and-usage-of-drum-types)



## Added support for drum types, and a new set of syntax for writing drums

Added in early May 2021, the drum type is a new music theory type that reads a string that follows a specific syntax rule and converts it to a drum beat.

I have designed a new syntax for writing drum beats, and have successfully written an interpreter function that can parse strings that conform to this syntax into a MIDI file of drum notes. I'll explain this syntax for drum beats in code.

First of all, you can define your own dictionary of numbers or letters or special characters to map to different MIDI drum notes, I have defined a default drum mapping dictionary in the database.

Here the length of numbers, letters and special characters can be greater than 1, which means you can write multiple digits, words and multiple special characters as a whole for mapping.

This is the current default drum mapping dictionary, there are two sets of templates you can choose to use, or you can customize the drum mapping dictionary.

```python
drum_mapping = {
    'K': 36, # Electric Bass Drum
    'H': 42, # Closed Hi-hat
    'S': 40, # Electric Snare
    'S2': 38, # Acoustic Snare
    'OH': 46, # Open Hi-hat
    'PH': 44, # Pedal Hi-hat
    'HC': 39, # Hand Clap
    'K2': 35, # Acoustic Bass Drum
    'C': 57, # Crash Cymbal 2
    'C2': 49, # Crash Cymbal 1
    '0': -1, # rest
    '-': -2 # the continuation of last note
}

drum_mapping2 = {
    '0': 36,
    '1': 42,
    '2': 40,
    '3': 38,
    '4': 46,
    '5': 44,
    '6': 39,
    '7': 35,
    '8': 57,
    '9': 49,
    'x': -1,
    '-': -2
}
```

Here the different strings of numbers map to values that are MIDI drum notes. The different values represent different pitches, and in the case of drums represent different kinds of drums. Now we can write a music theory type drum, whose 1st argument is a string, and in this string you can write drums according to the drum syntax I designed, which can represent complex drums relatively concisely.  

Single drums, spaced by `,`, indicate sequential playing (e.g. big drum, hihat, snare drum, hihat, big drum, hihat, snare drum, hihat).

```python
'K, H, S, H, K, H, S, H'  
```

Multiple different drums played at the same time, spaced by `;`, indicating simultaneous playing, using comma-separated notes or sequential playing.

```python
'K, K;H, S, H, K, K;H;PH H;S, H'
```

If there is an interval between two drums (drums can be monophonic or polyphonic), it is expressed in the form `i:n`, with n indicating the interval and n being any integer, decimal or fraction in bars.

```python
'K, i:1, S, H, i:1, K, S, H'
```

Set the length, interval and volume for each drum beat, each drum beat can be followed by a settings block like `[l:length;i:interval;v:volume]`, also including drums played at the same time (drums concatenated with `;`), if the second argument is the same as the first, then it can also be omitted by writing `.` to omit it, the default volume is 100.

```python
'K[l:1/4]'
```

In the settings block, if there are parameters you don't want to set, you can write `n` to indicate that you don't want to set them, and use them to set to the batch setting statement if there is one later. Please note that the batch setting statement will overwrite the setting of each drum block, unless `n` is written to indicate no setting.

```python
'K[l:1/4;i:n;v:80]'
```

Note that if drums that are played simultaneously (drums that are joined with ;) are set for the settings block, put it after the last note.

```python
'H;S[l:1/4;i:1/8]'
```

Keyword headers can perform a variety of different functions on the drums within their range of action. The drums are segmented by keyword headers or bar lines `|`, and each keyword headers acts on the drums between the previous keyword headers or bar lines and it.

Some of the keyword header can be placed in the settings block, separated by `;`.

Here is a description of the function of each keyword header:

```python
r:n  Repeat the drum beats n times, also in the setup block, the lengths and intervals of each note in the setup block are divided equally according to the number of repetitions when there is a fixed length assigned to it

R:n  In the set block, repeats the drum beats n times, and when there is a fixed length assigned to it, the lengths and intervals of each note is not divided equally

d:l;i;v  set the default length l, interval i, volume v, or you can use dl, di, dv respectively

a:l;i;v  set all uniform lengths l, interval i, volume v, or use al, ai, av respectively

t:n  set the total length of the interval to n. The length of each note in the interval is the total length divided by the number of notes in the region, and can be occupied using empty notes (characters with a value of -1 in the drum mapping) and continuation notes (characters with a value of -2 in the drum mapping), for example
K, H, S, H, 0, K, S, H, K, -, S, H, K, K, S, H, t:2
This is a section where each note takes up 1/8 of a measure, the 0 is the empty beat, and the - is the continuation of the previous note

b:n  when the total length is set, set the number of beats in the region to n beats, this will assign a unit duration to each drum beat unit; if set in the settings block, set the duration to unit duration * n

B:n used in the settings block, works the same as the global case of b, valid when there is a set total length in the settings block

i:n  insert interval of n bars in the middle of the note, in the setting block this is to set the interval of the note to n bars

l:n  set the note length to n bars in the settings block

v:n  set the note volume to n in the settings block

n:name  set the name of the drum beat in the region

u:name  use the drum region with the name

s:T  whether the connected drums are played simultaneously or not (T/F), if not, the drums will be divided equally by the length of one drum when the total length is fixed

cm:n  use for single continue symbol only, set the continue mode to n, the value could be 0 or 1, if value is 0, only extend the duration of the last note, if value is 1, when the previous note is a group, extend the duration of all notes in the group; if the continue mode is not set, the default action of continue symbol is extending only last note except when the previous note is a group that set to playing at the same time, which in case extending all notes in the group

Use ! in combination with the keyword header means to set all the current notes in batch,
for example: K, H, S, H | K, K, S, H, !r:2
```

## Caution

1. Please note that in the settings block, when the parameter is a list, you need to set a separate settings block, use `;` to separate the elements of the list, you can put multiple settings blocks together.
2. you can place any spaces and newlines, they will not affect the content after parser conversion, so you can write the drum code more beautiful and hierarchical.  
3. Note length and spacing can also be set using syntactic sugar, using `.n` for `1/n`, which is an nth note.

## Introduction to the construction and usage of drum types

Drum types are similar to chord types in that they have a list of notes and can be repeated n times, merged, set overall note parameters, etc. They can also be played directly, but please note that when you play a drum type using the play function (either using the built-in function of the drum type or using the global function), the drum type is automatically coded into a music type and sets the number of MIDI channels to 9 (counting from 0 as the first, i.e. the 10th channel of MIDI, dedicated to the drum track).  

Note that the drum type itself does not contain velocity, or tempo, or bpm, because the drum type is designed to be viewed on the same level as the chord type, and neither the drum type (drum) nor the chord type (chord) contains the velocity parameter.  

The drum type can be constructed by passing a string or a chord type, where the default position of the 1st parameter is a string, or can be specified with the pattern keywordword parameter, or with the notes keywordword parameter if you want to pass a chord type. If a string is passed in, the incoming string is parsed for drum syntax and converted to a chord type for storage in the drum type when the drum type is constructed. You can use the name keywordword argument to name the drums you write, and you can use the mapping keywordword argument to specify a dictionary of characters mapped to drum values, where drum values means the numbers corresponding to the different drums in general MIDI, and I have written which numbers correspond to the different drums in the drum_types in the database.  

You can use the `i` keywordword parameter to set the timbre of the drum kit, see the numbers corresponding to the timbre of the drum kit in general MIDI.  

Also, for chord types you can use the translate function, which uses the same set of syntax as writing drums to write chord types or a tune, just replace the custom characters with note names.

### The composition of drum type

```python
drum(pattern='',
     mapping=drum_mapping,
     name=None,
     notes=None,
     i=1,
     start_time=None,
     default_duration=1 / 8,
     default_interval=1 / 8,
     default_volume=100)
```

- pattern: the string to be parsed to drum beats
- mapping: the drum mapping dictionary, could be customized, the default value is drum_mapping
- name: the name of the drum beat
- notes: the chord type of the drum beat, could be passed in directly as drum beat, the default value is None, which is converting the pattern as drum beat
- i: the instrument type of the drum beat, corresponding to the instrument number of General MIDI
- start_time: the start time of the drum beat, if it is None, then use the start time of the chord type of the drum beat
- default_duration: default duration of drum beat
- default_interval: default interval of drum beat
- default_volume: default volume of drum beat

```python
drum_example = drum('K, H, S, H, K;H, K;H, S, H, r:2')
# This is an example of a drum section with kick, hi-hat, snare, hi-hat at the beginning, then 2 simultaneous kick and hi-hat, then 1 single snare and 1 single hi-hat, and finally the whole section repeats 2 times,
# where K,H,S means kick, hi-hat and snare respectively, so K;H means kick and hi-hat playing at the same time
play(drum_example, 150) # Play the drums at 150bpm
>>> print(drum_example)
[drum] 
chord(notes=[C2, F#2, E2, F#2, C2, F#2, C2, F#2, E2, F#2, ...], interval=[0.125, 0.125, 0.125, 0.125, 0, 0.125, 0, 0.125, 0.125, 0.125, ...], start_time=0)

# If you use drum type as track in a piece type, you must use the notes attribute of the drum type as chord type,
# and set the corresponding channel number to 9
piano1 = C('Cmaj7') % (1, 1/8)
piece_example = P([piano1, drum_example.notes], [1, 1], channels=[0, 9])
play(piece_example)
```

