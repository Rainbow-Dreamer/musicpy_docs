# The algorithm to determine the chord type of any group of notes according to the logic of music theory

This is an algorithm I developed in musicpy that determines the type of chord formed by any set of notes according to the logic of music theory.

This algorithm is a logically complex algorithm, and I may have to write a whole lot to talk about it from beginning to end, so I'll pick the key points to talk about.

This algorithm is very good at determining chord types. First of all, this algorithm can determine the chord type of any group of notes, no matter how complex the chord formed by this group of notes sounds, no matter how many notes there are in the group, and it must be correct in terms of music theory analysis. Although even the exact same chord may be called differently in different contexts, because it may have a completely different function, extrapolating the chords according to the results returned by this algorithm will definitely give you the group of notes you entered, and in the same order.

The algorithm can determine a wide variety of complex chord types, including root position chords, inverted chords, chord voicings (various sequences of chord notes), compound chords, chords with altered notes (e.g. C7#9 chord, C7b9#13 chord), chords with omitted notes (e.g. Cmaj9 omit 3 chord), chords with many repeated notes (notes that differ by one or multiple octaves).

The algorithm can also determine chords with a combination of these conditions, such as chords with omission and inversions, and chords with variations. 

The algorithm for chord determination is in the `detect` function, so we will talk about the usage of the `detect` function.

```python
detect(current_chord,
       change_from_first=True,
       original_first=True,
       same_note_special=False,
       whole_detect=True,
       poly_chord_first=False,
       root_preference=False,
       show_degree=False,
       get_chord_type=False,
       original_first_ratio=0.86,
       similarity_ratio=0.6,
       custom_mapping=None)
```

* current_chord: a set of notes you enter, which can be represented in many different ways, either as a chord type, or as a list of note types.  
* It can also be a list of strings that represent notes, or it can be a separate string (with notes separated by commas).  
* The notes can have only the note name and no octave number, in which case the chord construction will be done for the note name according to the rules of normalized chords.
* change_from_first: if True, it gives priority to the logical analysis of whether a set of notes is a certain chord obtained by changing the pitch (e.g. #5, b5, #9, b9, #11, b13). The returned chord result is also more likely to be a chord with a change (the name of the change is given, as well as the exact rise and fall). The default value is True.
* original_first: In this algorithm, the original order of the notes of the chord is analyzed logically (first to check if it matches the interval of the original chord), then the chord is inverted (including the lowest and highest notes), and then each inversion is analyzed logically, returning the result as soon as the chord type is successfully determined on the way. When original_first is True, if the chord type of the original order of the chord's notes is determined, the result will be returned if the chord similarity of the result is greater than or equal to the chord similarity threshold and the type of the result is not altered chord. If original_first is False, then this step is not performed, and after determining the chord type of the original order of the notes of the chord, the chord type of each inversion of the chord is then analyzed by the music logic. If the chord similarity of the original order of chord is 1, i.e. an exact match, then the result of the original order of chord is returned, regardless of whether the value of original_first is True or False. The reason why the original_first is True and the original order of the chord is not a voicing type is that if the chord is a voicing type, there may be a better and more logical analysis of the chord after transforming or splitting the chord into two parts (e.g. omission, voicings, polychords, etc.). The default value is True.
* same_note_special: if True, if the set of note names of chord is equal to the set of note names of the chord certain chord (same note name composition, regardless of order), then the chord similarity is set to 1 for that chord, i.e. the chord type with the same set of note names will be preferred. The default value is False.
* whole_detect: if True, when the chord is so complex that the chord type cannot be analyzed by using the original order and various inversions, the detector function is used to determine the chord type again for each inversion of the chord. In other words, the chord type can be determined for almost all possible cases of the chord's note ordering, as long as there is The result is returned as soon as the chord type is determined in one case. The default value is True.
* poly_chord_first: if True, before the whole_detect is done, the chord is split into its upper and lower parts and analyzed separately for musical logic, and the result is returned in the form of a polychord. If the number of notes of chord is less than 4, then the whole_detect is continued. If it is greater than or equal to 4 and less than 6, the chord is broken into a new chord with the first note at the bottom and the remaining notes above, and the result is the result of the upper chord/the bottom note. If it is greater than 6, the chord is divided into two chords according to its length, where the number of notes of the lower chord is the number of notes of the chord divided by 2 rounded down, and the upper chord is the remaining notes except for the lower chord. For example, if chord has 7 notes, then the chord below is the first 3 notes of chord, and the chord above is the last 4 notes of chord. The upper and lower chords are judged separately by the detect function, and the result is the result of the upper chord / the result of the lower chord. Set poly_chord_first to True could makes this algorithm run much faster in very complex chord detection. It is very suitable for jazz chord analysis (especially for real-time chord analysis). The default value is False.
* root_preference: if True, if the current chord is not a root position chord, it will first try to match the chord similarity using the lowest note of the current chord as the root note, and return the current result if it matches chord voicing or omitted notes of a root position chord. The default value is False.
* show_degree: if False, the chord type is returned, and if there is an alteration of some note in the chord that needs to be mentioned, such as an omitted note or a change, the name of the changed note is written out, e.g. `Cmaj7 (omit E)`. When True, the position (interval, or degree, of the root note) of the changed notes in the chord is calculated and displayed in degrees, e.g. `Cmaj7 (omit 3)`. The default value is False.
* get_chord_type: if True, return the chord type instance instead of string
* original_first_ratio: the threshold of the match ratio of the detected chord with the root position chords when the detected chord is in the original order
* similarity_ratio: the threshold of the match ratio of the detect chord with the root position chords when the detected chord is in non-original order
* custom_mapping: custom mapping of chord detections, should be a list [a1, a2, a3], where a1 is a dictionary that maps integers with note interval names (refer to INTERVAL in database), a2 is a match instance that maps integer tuples to chord types (refer to detectTypes in database), a3 is a match instance that maps chord types to integer tuples  (refer to chordTypes in database)

The detect function returns a string with the specific name of the chord type (including the name of the root note with the chord type) of the note composition entered, for example `Cmaj7`, `Cmaj9(omit 3)`, `Em/G`

```python
# These examples are all correct, they all perform a musical logic analysis on the group of notes A5, C5, E5, G5, and return the chord type (including the root note's name) for the group of notes.

import musicpy as mp

>>> mp.alg.detect(chord(['A5', 'C5', 'E5', 'G5']))
>>> mp.alg.detect(chord('A, C, E, G'))
>>> mp.alg.detect([N('A5'), N('C5'), N('E5'), N('G5')])
>>> mp.alg.detect([N('A'), N('C'), N('E'), N('G')])
>>> mp.alg.detect(['A5', 'C5', 'E5', 'G5'])
>>> mp.alg.detect(['A', 'C', 'E', 'G'])
>>> mp.alg.detect('A5, C5, E5, G5')
>>> mp.alg.detect('A, C, E, G')
Am7
```

The analysis steps of this algorithm are as follows:

1. Firstly, normalizes the chords formed by the input notes. This is a unique concept in musicpy's music theory system, please go to the chapter on basic syntax for the exact definition, which simply means removing all notes with repeated names (octave equivalents), and then adjusting the number of octaves of all notes without affecting their order. This is done so that the lowest note is within 15 degrees of the highest note. The key point of the standardization is that the two groups of notes before and after the standardization are actually equivalent chords, because we just remove the octave equivalents and make the group more concentrated without affecting the note order, with the benefit that the algorithm can analyze the chord types of the group of notes in musical logic more smoothly.
2. Calculate the interval relationship between the second to the last note and the first note, and then to see if it matches the interval relationship of a certain root position chord. The chordTypes in the database.py file are the types of root position chords that are currently supported. The root position chords are determined by the interval relationship between each note from the second note and the first note of the input. If the interval of a set of notes from the second to the last note and the first note is `4, 7, 11`, then it is the original position of the major seventh chord, so it is the easiest way to determine the original chord. The information about the chromatic numbers is written directly in the chordTypes.
3. If none of the interval relationships of the original chords can be matched, then start to consider a possible inversion chord. The steps of the musical logic analysis of an inversion chord are rather complicated, so I won't explain them here, but you can just look at the code of the detect function if you are interested.
4. If it is not an inversion of a chord, then the logical analysis is done to see if there is an omission or a change (or both), and the algorithm for this part is also very complicated. At the same time, the music logic analyzes whether the input notes are voicings of a certain chord (one of the sequences of notes of a certain chord), and the specific algorithm is to determine whether they are the same as the constituent notes of a certain chord (the order can be different).
5. If there is still no match, then it will consider whether it is a polychord or not, and then the input notes will be analyzed in a musical logic from the perspective of a polychord, and the input notes will be divided into two parts according to the number of notes (if the number of input notes is less than 6, then it will be divided into the first note and the other two parts, if it is greater than or equal to 6, then it will be divided into two parts evenly, and if it is an odd number, then the first part is the first half rounded up. (then the first part is the first half of the number of notes rounded, and the second part is the rest of the notes) The chord type of the two parts is determined separately, and then returned as a polychord (also as a string).

You can also customize the chordTypes in database.py to include the desired root position chord type, with the syntax `list of chord type names : intervals between notes and root note in the chord`.

This algorithm works very well and does not rely on the chord table (there are many websites that look up chord types and return the results directly from the chord table, not according to the algorithm of the logical analysis of the music theory, but just from the dictionary, so there are many very complicated cases that cannot be judged), and there are many logical parameters that can be set, as well as the display of the returned chord type. In my piano software Ideal Piano, I use this algorithm to analyze the music logic in real time and display the chord types of the notes you are currently playing, and it works very well. This algorithm allows you to see the current chord type in real time if you want to pick up the chords of a piece, which is very convenient.

