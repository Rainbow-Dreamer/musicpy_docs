# Basic syntax of pitch_bend type



## Contents

- [Inserting real time pitch bends, glissandos, and vibrato changes into a piece](#inserting-real-time-pitch-bends-glissandos-and-vibrato-changes-into-a-piece)



## Inserting real time pitch bends, glissandos, and vibrato changes into a piece

You can use the pitch_bend type to insert into the `pitch_bends` list of a chord type, which can change the pitch of the notes of a segment in real time, and can change the pitch very subtly.
When constructing the pitch_bend type, three different units can be selected using the mode parameter:

* semitone (any integer, decimal or fraction between -2 and 2, not including -2 and 2 itself)
* cent (1 semitone = 100 cents, any integer, decimal or fraction between -200 and 200, not including -200 and 200 itself, cent is the default unit)
* MIDI pitch bend value (any integer between -8192 and 8192, not including -8192 and 8192 itself)



### The composition of pitch_bend type

```python
pitch_bend(value, start_time=0, mode='cents', channel=None, track=None)
```

- value: the amount of pitch change of the note
- start_time: the position of the pitch change of the note in bars
- mode: mode == 'cents' selects cents as unit, default value, mode selects cents as unit if not set; mode == 'semitones' selects semitones as the unit; mode == 'values' selects MIDI pitch bend values as the unit
- channel: MIDI channel number
- track: MIDI track number



### pitch_bend type is inserted into the chord type

Insert the pitch_bend type into the `pitch_bends` list of the chord type.

```python
a = chord(['C5', 'D5', 'E5', 'F5', 'G5', 'A5', 'B5', 'C6']) % (1/8,1/8)
a.pitch_bends.append(pitch_bend(value=30, start_time=3/8))
play(a, 80)
# The chord type a will be played at normal pitch from the beginning to E5, and the notes after that will be played 30 cents higher (that's 0.3 semitones)
```
