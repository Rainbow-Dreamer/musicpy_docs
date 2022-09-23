Welcome to Musicpy's documentation!
===================================

Musicpy is a music programming language in Python designed to write music in very handy syntax through music theory and algorithms.

It is easy to learn and write, easy to read, and incorporates a fully computerized music theory system.

Musicpy can do way more than just writing music. This package can also be used to analyze music through music theory logic, and you can design algorithms to explore the endless possibilities of music, all with musicpy.

With musicpy, you can express notes, chords, melodies, rhythms, volumes and other information of a piece of music with a very concise syntax. It can generate music through music theory logic and perform advanced music theory operations.

You can easily output musicpy codes into MIDI file format, and you can also easily read any MIDI files and convert to musicpy's data structures to do a lot of advanced music theory operations. The syntax of musicpy is very concise and flexible, and it makes the codes written in musicpy very human-readable.

### a J-rock intro in musicpy

```python
guitar = ((C('Cmaj7')@1)@[1,2,3,4,1,2,3,2] |
(C('Fmaj7',3)^2)@[1,2,3,4,1,2,3,2] |
(C('G7sus',3)^2)@[1,2,3,4,1,2,3,2] |
C('Am11',3)@[1,2,4,6,1,4,6,4] |
(C('Cmaj7')@1)@[1,2,3,4,1,2,3,2] |
C('Fadd9',3).up(2,1)@[1,2,3,4,1,2,3,2] |
(C('G7sus',3)^2)@[1,2,3,4,1,2,3,2] |
C('Am11',3)@[1,2,4,6,1,4,6,4]) % (1/2,1/8)
bass1 = chord('D2[.8;.],E2[.8;.],D2[.8;.],E2[1;.], F2[1;.], G2[1;.], A1[.2;.], A2[.8;.], G2[.8;.], E2[.8;.], D2[.8;.]')
bass2 = (chord('E2')*8 + chord('F1')*8 + chord('G1')*8 + chord('A1,A1,E2,A1,A2,A1,G2,D2')) % (1/8,1/8) % 4
bass = bass1 | bass2
string1 = chord('B5[1;.],C6[1;.],D6[.2;.],E6[.2;.],\
F6[.2;.],E6[.2;.],C6[1;.],B5[.2;.],C6[.2;.],G5[1;.],\
F5[.2;.],E5[.2;.],C5[1;.],D5[.4;.],E5[.4;.],F5[.4;.],\
E5[.4;.],D5[.2;.],G5[.2;.],E5[1;.]')
play(piece([guitar%3, bass, string1], [28, 34, 49], 135, [0, 4-3/8, 12]))
```

[Click here to hear what this sounds like (Microsoft GS Wavetable Synth)](https://drive.google.com/file/d/1tMKLt3oFdmiGQPTdFVolGvBE1gVGNSwa/view?usp=sharing)

If you think this is too simple, musicpy could also produce music like [this](https://drive.google.com/file/d/1j66Ux0KYMiOW6yHGBidIhwF9zcbDG5W0/view?usp=sharing) within 30 lines of code (could be even shorter if you don't care about readability). Anyway, this is just an example of a very short piece of electronic dance music, and not for complexity.


Contents
-------------

### Introduction

* [Home](https://github.com/Rainbow-Dreamer/musicpy/wiki)

### Data structures

* [Data structures of musicpy](https://github.com/Rainbow-Dreamer/musicpy/wiki/Data-structures-of-musicpy)

### Basic syntax

* [Basic syntax of note type](https://github.com/Rainbow-Dreamer/musicpy/wiki/Basic-syntax-of-note-type)
* [Basic syntax of chord type](https://github.com/Rainbow-Dreamer/musicpy/wiki/Basic-syntax-of-chord-type)
* [Basic syntax of scale type](https://github.com/Rainbow-Dreamer/musicpy/wiki/Basic-syntax-of-scale-type)
* [Basic syntax of piece type](https://github.com/Rainbow-Dreamer/musicpy/wiki/Basic-syntax-of-piece-type)
* [Basic syntax of track type](https://github.com/Rainbow-Dreamer/musicpy/wiki/Basic-syntax-of-track-type)
* [Basic syntax of tempo type](https://github.com/Rainbow-Dreamer/musicpy/wiki/Basic-syntax-of-tempo-type)
* [Basic syntax of pitch_bend type](https://github.com/Rainbow-Dreamer/musicpy/wiki/Basic-syntax-of-pitch_bend-type)
* [Basic syntax of pan type](https://github.com/Rainbow-Dreamer/musicpy/wiki/Basic-syntax-of-pan-type)
* [Basic syntax of volume type](https://github.com/Rainbow-Dreamer/musicpy/wiki/Basic-syntax-of-volume-type)
* [Basic syntax of drum type](https://github.com/Rainbow-Dreamer/musicpy/wiki/Basic-syntax-of-drum-type)
* [Useful functionality](https://github.com/Rainbow-Dreamer/musicpy/wiki/Useful-functionality)

### Musicpy composition code examples

* [musicpy composition code examples Part 1](https://github.com/Rainbow-Dreamer/musicpy/wiki/musicpy-composition-code-examples-part-1)
* [musicpy composition code examples Part 2](https://github.com/Rainbow-Dreamer/musicpy/wiki/musicpy-composition-code-examples-part-2)
* [musicpy composition code examples Part 3](https://github.com/Rainbow-Dreamer/musicpy/wiki/musicpy-composition-code-examples-part-3)

### Musicpy sampler module

* [musicpy sampler module](https://github.com/Rainbow-Dreamer/musicpy/wiki/musicpy-sampler-module)
* [musicpy sampler module composition code examples](https://github.com/Rainbow-Dreamer/musicpy/wiki/musicpy-sampler-module-composition-code-examples)

### Algorithms

* [Introduction of musicpy algorithms module](https://github.com/Rainbow-Dreamer/musicpy/wiki/Introduction-of-musicpy-algorithms-module)
* [the algorithm to split the main melody and chords from a piece of music](https://github.com/Rainbow-Dreamer/musicpy/wiki/the-algorithm-to-split-the-main-melody-and-chords-from-a-piece-of-music)
* [the algorithm to determine the chord type of any group of notes according to the logic of music theory](https://github.com/Rainbow-Dreamer/musicpy/wiki/the-algorithm-to-determine-the-chord-type-of-any-group-of-notes-according-to-the-logic-of-music-theory)

