# Basic syntax of rhythm type



## Contents

- [Introduction of rhythm type](#introduction-of-rhythm-type)
- [The composition of rhythm type](#the-composition-of-rhythm-type)
- [The rhythm pattern syntax](#the-rhythm-pattern-syntax)
- [Apply rhythm to a chord instance](#apply-rhythm-to-a-chord-instance)
- [Play a rhythm instance](#play-a-rhythm-instance)
- [Get the rhythm information of a rhythm instance](#get-the-rhythm-information-of-a-rhythm-instance)
- [Convert a rhythm instance to another time signature](#convert-a-rhythm-instance-to-another-time-signature)
- [Get chords with rhythm](#get-chords-with-rhythm)
- [Analyze the rhythm of a chord instance](#analyze-the-rhythm-of-a-chord-instance)



## Introduction of rhythm type

This is a data structure that represents a rhythm, which is equivalent to a list of beats, rest symbols and continue symbols.

A rhythm instance can be applied to a chord instance to change the intervals and durations of a chord instance.

You can think of this data structure as a higher level abstraction of the drum type.

You can set a fixed total length when initializing a rhythm instance, then the durations of the beats you set will be divided equally according to the length and number of the beats.

You can also set the time signature for the rhythm, such as 4/4, 3/4, 5/4.

For simplification purposes, there is a rhythm pattern syntax which is somewhat similar to the drum pattern syntax to help you make a rhythm instance quicker.

You can add 2 rhythm instances to get a new combined rhythm instance, or multiply a rhythm instance by an integer to get a new repeated rhythm instance.

This data structure is inherited from list, so you can do the same operations as lists on the rhythm instances.



## The composition of rhythm type

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



## The rhythm pattern syntax

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



## Apply rhythm to a chord instance

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
chord(notes=[C5, D5, E5, E5, F5, G5, E5, D5, C5, A5, ...], interval=[1/8, 1/8, 1/4, 1/8, 1/8, 1/4, 1/8, 1/8, 1/4, 1/4, ...], start_time=0)
```



## Play a rhythm instance

You can use `play` method of a rhythm instance to take a listen to what the rhythm is like. This generates a chord instance that contains notes of the same pitch with intervals and durations applied the rhythm. You can set the pitch of the note in the method.

```
play(*args, notes='C4', **kwargs)
```

* *args, **kwargs: the parameters of the regular play function
* notes: the note of the generated chord instance, could be a string or a note instance



## Get the rhythm information of a rhythm instance

There are several attributes and methods to get the rhythm information of a rhythm instance.

The `total_bar_length` attribute of a rhythm instance is the total length of bars corresponding to the time signature of the rhythm instance, for example, if a rhythm instance contains 3 beat instances of duration 1/4 and has time signature 3 / 4, then the total length of bars of the rhythm instance is 1.

The `time_signature` attribute of a rhythm instance is the time signature of the rhythm instance, the value is a list `[numerator, denominator]`.

You can use `get_length` method to get the total number of beats including rest symbols and continue symbols with the dotted number applied of a rhythm instance. You can also specify the parameter `beat_list` to get the total number of beats of a list of beats.

You can use `get_beat_num` method to get the number of beat instances of a rhythm instance, this method only counts the number of beat instances, not considering the dotted numbers.

You can use `get_total_duration` method to get the total duration in bars of a rhythm instance, set the parameter `apply_time_signature` to True to apply the time signature of the rhythm instance (the default is False).



## Convert a rhythm instance to another time signature

You can use `convert_time_signature` method to convert a rhythm instance to another time signature. This method returns a new rhythm instance.

There are 2 different ways to convert to another time signature:

1. only change the total bar length to match the new time signature
2. keep the total bar length unchanged, change the durations of the beats to match the total bar length in the new time signature

These 2 ways result in different new rhythm instances, which both are useful in different use cases.

```python
convert_time_signature(time_signature, mode=0)
```

* time_signature: a list that represents the new time signature `[numerator, denominator]`

* mode: if set to 0, convert in way 1, if set to 1, convert in way 2



```python
rhythm1 = rhythm('b b b')

>>> print(rhythm1)
[rhythm]
rhythm: beat(1/4), beat(1/4), beat(1/4)
total bar length: 3/4
time signature: 4 / 4

rhythm2 = rhythm1.convert_time_signature([3, 4])

>>> print(rhythm2)
[rhythm]
rhythm: beat(1/4), beat(1/4), beat(1/4)
total bar length: 1
time signature: 3 / 4
```



## Get chords with rhythm

You can use `get_chords_from_rhythm` function to repeat a chord with the rhythm of a rhythm instance, or apply the rhythm to a list of chords. This function returns a new chord instance that concatenates all of the chords according to the rhythm.

```python
get_chords_from_rhythm(chords, current_rhythm, set_duration=True)
```

* chords: could be a chord instance, a list of chords or a note instance, if it is a note instance, the result will be a chord instance with the note repeats with the rhythm
* current_rhythm: a rhythm instance
* set_duration: set the durations of the notes of each chord according to the rhythm



```python
>>> get_chords_from_rhythm(C('C'), rhythm('b - b. b. b b -', 1))
chord(notes=[C4, E4, G4, C4, E4, G4, C4, E4, G4, C4, ...], interval=[0, 0, 1/4, 0, 0, 3/16, 0, 0, 3/16, 0, ...], start_time=0)
```



## Analyze the rhythm of a chord instance

You can use `analyze_rhythm` function to analyze the rhythm of a chord instance, this function will find the unit of the chord instance, and extract the beats, rest symbols and continue symbols from the chord intervals and durations. If a total bar length is provided, this function can also get the time signature. This function returns a rhythm instance.

```python
analyze_rhythm(current_chord,
               include_continue=True,
               total_length=None,
               remove_empty_beats=False,
               unit=None,
               find_unit_ignore_duration=False,
               merge_continue=True)
```

* current_chord: a chord instance
* include_continue: whether to consider the durations of the notes to convert to continue symbols
* total_length: the total length in bars of the chord instance
* remove_empty_beats: remove the beats with 0 duration
* unit: you can manually provide a unit in bars
* find_unit_ignore_duration: find unit only from chord intervals
* merge_continue: merge successive continue symbols as extra duration to the beat before them



```python
chord1 = get_chords_from_rhythm(N('C5'), rhythm('b - b. b. b b -', 1))
>>> chord1
chord(notes=[C5, C5, C5, C5, C5], interval=[1/4, 3/16, 3/16, 1/8, 1/4], start_time=0)

>>> analyze_rhythm(chord1)
[rhythm]
rhythm: beat(1/4), beat(1/8.), beat(1/8.), beat(1/8), beat(1/4)
total bar length: 1
time signature: 4 / 4
```

