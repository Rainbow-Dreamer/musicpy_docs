# Musicpy Quickstart and Cheat Sheet

This is a chapter for a quickstart and cheat sheet for musicpy, in case you find the documentation are too long and complicated to read and hard to find what you want.

# Cheat Sheet
(in case you import musicpy as `from musicpy import *`)

Note: the chord type of muscipy has a `interval` attribute which is the list of the length of bars between the starts of each 2 adjacent notes of the chord, please don't confuse this with the music theory's interval which is a difference in pitch between 2 notes (e.g. major third, semitone). In the cheat sheet we'll use `music intervals` to denote the music theory's interval.

|                        functionality                         |                     syntax (recommended)                     |                         alternatives                         |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|                     construct a note C5                      |                           N('C5')                            |                    N('c5');  note('C', 5)                    |
|                   construct a chord Cmaj7                    |                          C('Cmaj7')                          |                       chd('C', 'maj7')                       |
|        construct a chord Cmaj7 with starting note C5         |                         C('C5:maj7')                         |      C('Cmaj7', pitch=5);<br/>chd('C', 'maj7', pitch=5)      |
|           construct a chord with notes C5, E5, G6            |                     chord('C5, E5, G6')                      | chord('c5, e5, g6');<br/>chord(['C5', 'E5', 'G6']);<br/>chord(['c5', 'e5', 'g6']);<br/>chord([N('C5'), N('E5'), N('G6')]) |
|                  construct a C major scale                   |                         S('C major')                         |                     scale('C', 'major')                      |
|                      construct a piece                       | build(track(content=C('C'), instrument=1, start_time=0),<br/>track(content=C('D'), instrument=47, start_time=1), bpm=150) | P(tracks=[C('C'), C('D')],<br/>instruments=[1, 47],<br/>start_times=[0, 1],<br/>bpm=150) |
|                      construct a track                       |      track(content=C('C'), instrument=1, start_time=0)       |                                                              |
|                construct a set of drum beats                 |             drum('K, H, H, K, S, H, H, H, t:1')              |                                                              |
|         transpose a chord/note A up/down n semitones         |                         A + n; A - n                         |                      A.up(n); A.down(n)                      |
|         raise/lower ith note of chord A n semitones          |                    A + (n, i); A - (n, i)                    |                   A.up(n, i); A.down(n, i)                   |
|     raise/lower ith to jth notes of chord A n semitones      |                 A + (n, i, j); A - (n, i, j)                 |                A.up(n, i, j); A.down(n, i, j)                |
|               concatenate chord A and chord B                |                            A \| B                            |                            A + B                             |
|    concatenate chord B after chord A with n bars interval    |                         A \| (B, n)                          |                          A + (B, n)                          |
|                add rest of n bars to chord A                 |                            A \| n                            |                          A.rest(n)                           |
|  concatenate chord A n times with interval i bars each time  |                         A \| (n, i)                          |                          A * (n, i)                          |
|                     stack chord A and B                      |                            A & B                             |                                                              |
|              stack chord B n bars after chord A              |                          A & (B, n)                          |                                                              |
|                    stack chord A n times                     |                            A & n                             |                                                              |
|     stack chord A n times with interval i bars each time     |                          A & (n, i)                          |                                                              |
|                    repeat chord A n times                    |                            A * n                             |                                                              |
|                   get ith note of chord A                    |                             A[i]                             |                                                              |
|               get ith to jth notes of chord A                |                            A[i:j]                            |                                                              |
|                 set ith note of chord A to j                 |                           A[i] = j                           |                                                              |
|              get the number of notes of chord A              |                            len(A)                            |                                                              |
|                  get the reverse of chord A                  |                              ~A                              |                         A.reverse()                          |
|             transpose chord/note A up 1 semitone             |                              +A                              |                            A.up()                            |
|            transpose chord/note A down 1 semitone            |                              -A                              |                           A.down()                           |
| set duration, interval and volume of chord A and return a new chord A |               A % (duration, interval, volume)               |              A.set(duration, interval, volume)               |
|               get the nth inversion of chord A               |                            A / n                             |                        A.inversion(n)                        |
|        invert the nth note of chord A to the highest         |                            A ^ n                             |                A / -n; A.inversion_highest(n)                |
|         invert the nth note of chord A to the lowest         |                            A @ n                             |                           A.inv(n)                           |
| form a new chord from the notes of chord A by index (1-based) |                A @ [1, 2, 3, 2, 1.1, 2, 3, 2]                |                                                              |
| form a new chord from the notes of chord A by index (1-based), increased octaves |                       A / [1, 2, 4, 3]                       |                                                              |
|           modulate chord A from scale i to scale j           |                      A.modulation(i, j)                      |                                                              |
|                  delete ith note of chord A                  |                           del A[i]                           |                                                              |
| append note i to chord A with interval j and return a new chord A |                            A + i                             |                                                              |
|           append note i to chord A with interval j           |                        A.append(i, j)                        |                                                              |
|     remove note i from chord A and return a new chord A      |                            A - i                             |                                                              |
|                  remove note i from chord A                  |                         A.remove(i)                          |                                                              |
|                  pop ith note from chord A                   |                           A.pop(i)                           |                                                              |
|     insert note j at index i with interval k to chord A      |                  A.insert(i, j, interval=k)                  |                                                              |
|                  drop n voicing of chord A                   |                          A.drops(n)                          |                                                              |
|            negative harmony of chord A at scale B            |                            A @ B                             |                    A.negative_harmony(B)                     |
|       get chord A from ith bars to jth bars (0-based)        |                         A.cut(i, j)                          |                                                              |
|      get a new chord based on changing chord A's notes       |                       A('omit 3, b9')                        |                                                              |
|           get the total length in bars of chord A            |                           A.bars()                           |                                                              |
|              get durations of notes of chord A               |                       A.get_duration()                       |                                                              |
|              get intervals of notes of chord A               |                          A.interval                          |                                                              |
|          set duration, volume and channel of note A          |               A % (duration, volume, channel)                |               A.set(duration, volume, channel)               |
| change the accidentals of note A (# to b, b to #) and return a new note |                              ~A                              |                                                              |
|            get dotted note of note A with n dots             |                         A.dotted(n)                          |                                                              |
|                    get duration of note A                    |                          A.duration                          |                                                              |
|               raise/lower scale A n semitones                |                         A + n; A - n                         |                      A.up(n); A.down(n)                      |
|         raise/lower ith note of scale A n semitones          |                    A + (n, i); A - (n, i)                    |                   A.up(n, i); A.down(n, i)                   |
|     raise/lower ith to jth notes of scale A n semitones      |                 A + (n, i, j); A - (n, i, j)                 |                A.up(n, i, j); A.down(n, i, j)                |
|                raise/lower scale A 1 semitone                |                            +A; -A                            |                       A.up(); A.down()                       |
|                  get ith degree of scale A                   |                             A[i]                             |                                                              |
|                   get ith triad of scale A                   |                             A(i)                             |                   A.pickchord_by_degree(i)                   |
|                 get ith 7th chord of scale A                 |                         A(i, num=4)                          |               A.pickchord_by_degree(i, num=4)                |
|                   get ith mode of scale A                    |                            A / i                             |                        A.inversion(i)                        |
|          get chord with notes from scale A by index          |                     A @ [1, 2, 3, 4, 5]                      |            A.pickchord_by_index([1, 2, 3, 4, 5])             |
|              get the reversed scale of scale A               |                              ~A                              |                         A.reverse()                          |
|       get a chord progression with degrees of scale A        |                           A % 6451                           |                       A.pattern(6451)                        |
|                   get ith track of piece A                   |                             A[i]                             |                                                              |
|            get chord type of ith track of piece A            |                             A(i)                             |                         A.tracks[i]                          |
|               raise/lower piece A n semitones                |                         A + n; A - n                         |                                                              |
|         raise/lower ith track of piece A n semitones         |                     A[i] += n; A[i] -= n                     |                                                              |
|                    repeat piece A n times                    |                            A * n                             |                                                              |
|               concatenate piece A and piece B                |                            A + B                             |                            A \| B                            |
|                  stack piece A and piece B                   |                            A & B                             |                                                              |
|                 delete ith track of piece A                  |                           del A[i]                           |                                                              |
|                  get the reverse of piece A                  |                              ~A                              |                         A.reverse()                          |
|             get the number of tracks of piece A              |                            len(A)                            |                                                              |
|               append a new track n to piece A                |                         A.append(n)                          |                                                              |
|          insert a new track n at index i to piece A          |                        A.insert(i, n)                        |                                                              |
|           get the total length in bars of piece A            |                           A.bars()                           |                                                              |
|               read a MIDI file into piece type               |                          read(path)                          |                                                              |
|  write a note/chord/piece/track/drum type A to a MIDI file   |                           write(A)                           |                                                              |
|     play a note/chord/piece/track/drum type A at bpm 100     |                       play(A, bpm=100)                       |                                                              |
|                         stop playing                         |                          stopall()                           |                                                              |
|            concatenate a list of chords [A, B, C]            |                      concat([A, B, C])                       |                                                              |
| concatenate a list of chords [A, B, C] with extra interval i |                  concat([A, B, C], extra=i)                  |                                                              |
|               stack a list of chords [A, B, C]               |                 concat([A, B, C], mode='&')                  |                                                              |
|    stack a list of chords [A, B, C] with extra interval i    |             concat([A, B, C], mode='&', extra=i)             |                                                              |
|                 get note from MIDI degree i                  |                      degree_to_note(i)                       |                                                              |
|         get the degree (MIDI note number) of note A          |                           A.degree                           |                                                              |
|                 get the note name of note A                  |                            A.name                            |                                                              |
|               get the octave number of note A                |                            A.num                             |                                                              |
|              get the music intervals of chord A              |                        A.intervalof()                        |                                                              |
| construct a tempo change instance with bpm 150 and start time at 0 |                   tempo(150, start_time=0)                   |                                                              |
| construct a pitch_bend instance with value of 100 cents and start time at 0 |                pitch_bend(100, start_time=0)                 |                                                              |
| construct a pan instance with value of 100% and start time at 0 |                    pan(100, start_time=0)                    |                                                              |
| construct a volume instance with value of 100% and start time at 0 |                  volume(100, start_time=0)                   |                                                              |
|                                                              |                                                              |                                                              |
|                                                              |                                                              |                                                              |
|                                                              |                                                              |                                                              |
