# Basic syntax of tempo type



## Contents
- [Inserting real-time tempo changes into a piece](#inserting-real-time-tempo-changes-into-a-piece)



## Inserting real-time tempo changes into a piece

A chord type's attribute `tempos` is a list containing tempo types. Each tempo type must have a specific start time, which makes the tempo of the piece to change dynamically while playing it.



### The composition of tempo type
```python
tempo(bpm, start_time=0, channel=None, track=None)
```
- bpm: the speed of the piece you want to change to
- start_time: the time at which you want to change position, in bars, can be an integer, decimal or fraction
- channel: MIDI channel number
- track: MIDI track number



### insert the tempo type into the chord type
Insert the tempo type into the `tempos` list of the chord type.
```python
a = chord(['C5', 'D5', 'E5', 'F5', 'G5', 'A5', 'B5', 'C6']) % (1/8,1/8)
a.tempos.append(tempo(bpm=150, start_time=3/8))
play(a, 80)
# The chord type a will be played at 80 BPM from the beginning to E5, and 150 BPM thereafter
```
