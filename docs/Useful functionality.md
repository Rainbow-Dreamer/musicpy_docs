# Useful functionality



## Contents

- [Play note, chord, piece and other music theory types](#play-note-chord-piece-and-other-music-theory-types)
- [Read MIDI files and convert them to piece types for various music theory operations](#read-midi-files-and-convert-them-to-piece-types-for-various-music-theory-operations)
- [Write music theory types to MIDI files for easy viewing and editing in DAW](#write-music-theory-types-to-midi-files-for-easy-viewing-and-editing-in-DAW)
- [I wrote an IDE specifically for musicpy for everyone to use](#i-wrote-an-IDE-specifically-for-musicpy-for-everyone-to-use)
- [Storing musicpy's data structures as a separate data file](#storing-musicpys-data-structures-as-a-separate-data-file)
- [Conversion between notes and frequencies](#conversion-between-notes-and-frequencies)
- [The play function can be abbreviated when selecting an instrument](#the-play-function-can-be-abbreviated-when-selecting-an-instrument)
- [When reading a MIDI file with all the notes of different instruments in one MIDI track, how to convert it to a piece type](#when-reading-a-midi-file-with-all-the-notes-of-different-instruments-in-one-midi-track-how-to-convert-it-to-a-piece-type)
- [Added support for lowercase note names](#added-support-for-lowercase-note-names)
- [eval_time function has been added to return time value](#eval_time-function-has-been-added-to-return-time-value)
- [Redesign of the reverse function](#redesign-of-the-reverse-function)
- [Added the ability to read other types of MIDI messages](#added-the-ability-to-read-other-types-of-midi-messages)
- [Improvement of the function to clear tempo changes and pitch bends for chord type and piece type](#improvement-of-the-function-to-clear-tempo-changes-and-pitch-bends-for-chord-type-and-piece-type)
- [Stop all currently playing sounds](#stop-all-currently-playing-sounds)
- [Use of interval names](#use-of-interval-names)
- [Modify the properties of the music theory type and return the new piece type](#modify-the-properties-of-the-music-theory-type-and-return-the-new-music-theory-type)



## Play note, chord, piece and other music theory types

musicpy has a function called `play`, which can play note, chord, piece and other music theory types, and generates a MIDI file at the same time.
The first argument of the `play` function is music theory type, supported music theory types include note, chord, piece, track and drum, followed by several important keyword arguments: `bpm` (tempo), `name` (name of the MIDI file), `instrument` (the type of instrument). For other parameters, you can refer to the parameters of the `write` function, which I will talk about in the documentation later.

**Note: In some IDEs (e.g. VS Code) this play function may not make sound, this is because the program exits early before the playing starts. You can set the `wait` parameter in the `play` function to True, this will allow the program to wait before the playing is guaranteed to finish and you will be able to hear the sound.**

* ### Play notes

```python
a = note('A', 5)
play(a)
```

Run it and you'll hear the piano sound of note A5.

```python
# in some IDEs you need to set the `wait` parameter to True in order to hear the sound
a = note('A', 5)
play(a, wait=True)
```

* ### Play chords

```python
b = chd('C', 'maj7', intervals=1, duration=2)
play(b, 150)
```

Play a broken chord of a C major seventh chord at 150 BPM.

```python
c = chd('D', 'm7', intervals=1)*2 + chd('E', 'maj9', intervals=0.5).reverse()
play(c, 100)
```

Play the D minor seventh chord twice at 100 BPM (each note spacing is 1 bar), then play the E major ninth chord backwards (each note spacing is 0.5 bars).

* ### Selecting the instrument type

  The instrument argument in the play function is the instrument name, which defaults to None and corresponds to the list of instruments in General Midi. In addition to entering the instrument name, you can also directly enter the instrument number (any integer between 0 and 127), for example

```python
play(c, 100, instrument='Violin')
```

will play the same thing as before with the MIDI sound of a violin.

* ### The mechanism for running the play function

  When the play function is run, it automatically generates a MIDI file in the musicpy folder, which contains the musicpy code that the play function receives.

In fact, the mechanism of the play function is to write the content of the musicpy code to a new MIDI file, and then call pygame's function to play the MIDI file (previously, the local player was called, recently it has been improved to play directly internally with pygame, without calling the local player).

This generated MIDI file is also easy to keep in the DAW for later viewing and use.

In addition, any of the note and chord types can also be played by calling their own play function, for example, to play chord A, you can write

```python
A.play(150)
```

which is to play chord A with 150 BPM.

## Read MIDI files and convert them to piece types for various music theory operations

Using the read function you can read a MIDI file and convert the contents of the MIDI file to a piece type.

```python
read(name,
     is_file=False,
     get_off_drums=False,
     clear_empty_notes=False,
     clear_other_channel_msg=False,
     split_channels=None)
```

* name: the name of the MIDI file (including the .mid filename extension)
* is_file: is used to handle the case when the first parameter name is passed in as a MIDI file stream. If the first parameter name is a MIDI file stream, is_file needs to be set to True for normal reading. The default value is False, which is not used when the first parameter is a MIDI file name.
* get_off_drums: When set to True, filter out the drum track. The default value of get_off_drums is False.
* clear_empty_notes: set to True to remove all note types with duration <= 0 when converting each track to a chord type.
* clear_other_channel_msg: When it is set to True, it will clear the MIDI messages that do not belong to the channel number of current track of each track returned.
* split_channels: When reading a MIDI file that puts all the notes of all the different instruments in one MIDI track, set this parameter to True to convert to the correct piece type.

### Example of read function usage:

For example, now there is a MIDI file `Clair de Lune.mid`, we can read it using the read function, convert it to piece type.

```python
current_piece = read('Clair de Lune.mid')
```

Now we can perform various music theory operations on piece type current_piece. The piece type current_piece stores information about the whole piece, including all the tracks, instruments, notes and note intervals. You can use many of the syntaxes mentioned in the basic syntax section to play with the music theory of piece type current_piece, such as modulation, key changes upwards and downwards, slice, invert, etc.

## Write music theory types to MIDI files for easy viewing and editing in DAW

Use the write function to write a music theory type like note type, chord type, piece type to a MIDI file.

In fact, the write function is used in the play function, which then uses pygame to play the generated MIDI file.

```python
write(current_chord,
      bpm=120,
      channel=0,
      start_time=None,
      name='temp.mid',
      instrument=None,
      i=None,
      save_as_file=True,
      msg=None,
      nomsg=False,
      ticks_per_beat=960,
      **midi_args)
```

* current_chord: the music theory type you want to write, could be note type, chord type, piece type, track type, drum type or a list of chord types.

* bpm: the BPM of the piece you want to write.

* channel: the channel number you want to write to, note that 9 corresponds to the drum track.

* start_time: the start time of the track to be written (in bars), if it is None, then use the start_time attribute of the chord type.

* name: the name of the generated MIDI file you want to have.

* instrument: the instrument type of the track you want to write, corresponding to the instrument of General Midi, numbered from 0 to 127, you can enter the instrument name (the instrument name in the instrument list of General Midi), or you can enter the number (0 to 127) directly.

* i: same as instrument

* save_as_file: When set to True, the MIDI file is written or generated locally and no value is returned. When set to False, a MIDI file stream is returned. The default value is True.

* msg: list of other MIDI messages you want to write to the MIDI file

* nomsg: set whether write other MIDI messages to MIDI file or not, if set to True, then not written

* ticks_per_beat: ticks per beat of the MIDI file, set this higher to get higher resolution

* **midi_args: other arguments for MIDI file, please refer to [mido documentation](https://mido.readthedocs.io/en/latest/lib.html#midi-files) for details

## I wrote an IDE specifically for musicpy for everyone to use

[musicpy_editor](https://github.com/Rainbow-Dreamer/musicpy_editor) is an IDE I developed specifically for musicpy language.

This editor has a lot of features to facilitate the quick writing of musicpy code, and you can hear the corresponding music in real time.

Next, we will introduce this editor in detail.

First of all, type musicpy code in the box above, and the box below will show you the result, running in real time, not using print and auto-completion are all open by default, you can also go to the corresponding tick box to close when not using. Auto-completion allows you to write code with only one or two characters to give you a list of functions and methods containing those characters, allowing you to quickly select them with mouse clicks and arrow keys plus carriage returns. Auto-completion is also turned on after an object's "." is also turned on after, when the autocomplete list is the class methods and properties owned by this object. Parentheses and brackets are automatically paired (typing the left bracket will automatically fill in the right bracket) and the input cursor is automatically placed between the two brackets. Inside the file bar there are open musicpy code file (as long as it is in text file format), save the currently written musicpy code as a file, and settings. This editor also supports syntax highlighting, and you can customize the syntax highlighting content and corresponding colors in the settings.

I also added a light-on/light-off function for the input and output screens, so that you can switch back and forth between the black-on-white theme and the white-on-black theme to suit the tastes of different programmers. At the same time, you can choose your own font type and font size, as well as customize the background color of the editor's widget, font color and the color of the mouse cursor on the widget. All parameters in the settings file config.py can be modified, and you don't need to open config.py to modify it, just open the editor, then click File - Settings in the upper left corner, modify the parameters in the pop-up settings window, and then click the Save button. Some parameters need to restart the editor after modification to see the modified effect, which I will talk about in the next part of the introduction of the parameters in the settings.

The description of the parameters in the settings.

* bakground_image: you can choose the file path of the background image, click the "Change" button to open a file browser box, select the file path of the background image you want to set, you can also manually enter the file path. Click "Save" to reload the background image after modification
* background_places: the position of the background image, the first item is the x (horizontal) coordinates, the second item is the y (vertical) coordinates, the upper left corner of the editor as 0, 0 (origin), x from the left to the right to increase, y from the top to the bottom to increase. Clicking save after the modification will reload the position of the background image
* pairing_symbols: the list of auto-complete symbols, you can customize the symbols you want to auto-complete, for example, type a `(` will auto-complete `)`
* font_type: the font type of the input and output windows
* font_size: the font size of the input and output windows
* background_mode: the parameter of light on/off mode, `white` means light on mode, `black` means light off mode, there will be a button to toggle light on/off in the editor's main interface
* syntax_highlight: dictionary for syntax highlighting, key is the color name, value is the list of words that need syntax highlighting to this color.
* background_color: the background color of the widget in the editor
* foreground_color: the font color of the widget in the editor
* active_background_color: the background color displayed when the mouse cursor is moved over the widget in the editor
* button_background_color: the background color of the button of the window where the file is saved when the editor is closed
* active_foreground_color: the foreground color displayed when the mouse cursor is moved over the widget in the editor
* language: the language used by the editor

Without print if you check the box, when you type a line of code, in each line, if there is something that can be shown, the box below will be shown, equivalent to automatically adding print. real-time run if you check the box, the editor will run the code you wrote in real time when the code you wrote changes, and the result will be shown in the box below (if you do not use print to check the box). In this musicpy editor, in addition to the live run, auto-completion, no-print checkboxes and the file bar, there is also a save button, which saves the current code as a file; a run button, which runs the current code, and displays the error message in the box below if the current code runs with an error, and does not affect the normal work of the editor. When running in real time, if the current code will run with errors, nothing will be shown in the box below, at this time click the run button to see the error message; the auto line feed button, you can make the following box show the running result automatically line feed.

This musicpy editor has several very useful functions, which will be introduced one by one.

1. Adding `/` before a line of musicpy code will play the music represented by this code directly and internally, without opening any player on your computer. At the same time it will also generate the MIDI file corresponding to the current musicpy code in the musicpy folder. This syntax is equivalent to putting this line of code inside the play function. The parameters of the play function can be set with English commas followed by the code, such as bpm (song speed), instrument (instrument) and so on. It is recommended to run in real time while open (the default is open), you can musicpy code to write where, plus `/` can immediately hear. For example, in the editor write

   ```python
   /C('Dmaj7') * 4 | C('Em7') * 4, 150
   ```

​		Then you can hear the music corresponding to this musicpy language directly.

2. Add a `?` before a line of musicpy code (especially a chord code) You can get the name of the chord type according to the music logic, for example

   ```python
   ?chord(['C','E','G','B'])
   ```

​		will return `Cmaj7`.

​		This syntax is equivalent to representing a musicpy code for a chord placed inside the detect function. The parameters of the detect function can 		also be configured with English commas following the code.

3. Click the right mouse button to bring up the IDE menu, you can choose to play the selected musicpy statement, you can also choose to play the selected musicpy statement visually, the visualization window is the body of another music-related project I wrote, [Ideal Piano](https://github.com/Rainbow-Dreamer/Ideal-Piano).

4. In the right mouse menu, you can choose to import a MIDI file, (also available in the file bar) and after selecting a MIDI file a code for the read function with default parameters is automatically generated and assigned to a variable new_midi_file.
   The variable name can be changed by yourself. Stop play is used when playing musicpy code, if you only want to listen to a section, but not the whole song, then you can stop playing immediately.

5. There are many combinations of computer keyboard shortcuts built into this editor, each corresponding to a variety of different functions.

   ```python
   ctrl + e to stop playback  
   
   ctrl + d import a MIDI file  
   
   ctrl + w open file (text file)  
   
   ctrl + s Save the current code as a text file  
   
   ctrl + q Close the editor  
   
   ctrl + r Run the current code  
   
   ctrl + g Turn on/off lights  
   
   ctrl + mouse wheel can adjust the font size, scroll up to make the font bigger, scroll down to make the font smaller, and will automatically save the changed font size settings.
   
   The Line Col in the lower right corner shows the number of lines and columns where the current input cursor is located, so that it is easy to check the corresponding position when the code is wrong.
   
   alt + z plays the selected musicpy code  
   
   alt + x to play the selected musicpy code visually
   ```

I will improve this musicpy editor afterwards, hope you will have fun with it!

## Storing musicpy's data structures as a separate data file

You can use `write_data` function to store any of musicpy's data structures as a single binary data file, with the suggested file extension `mpb` (musicpy binary), for example

```python
write_data(C('C'), name='C major chord.mpb')
```

This line of code creates a binary data file of musicpy's data structures called `C major chord.mpb` in the current file directory.

You can use `load_data` function to load any mpb file as musicpy's data structures, for example

```python
result = load_data('C major chord.mpb')

>>> result
[C4, E4, G4] with interval [0, 0, 0]
```

There are times when you don't want to store musicpy code or its generated MIDI files, storing them as binary data files is a good option.

## Conversion between notes and frequencies

You can use the `get_freq` function to get the frequency of a note, and you can set the frequency of the standard pitch A4 to get the frequency of a note at a different standard.  
You can use the `freq_to_note` function to convert a frequency to the note closest to that frequency, and you can set the frequency of standard pitch A4 to get the note closest to that frequency under different standards.

```python
# The arguments to the get_freq function can be either a string representing a note or a note type
>>> get_freq('C5') # Get the frequency of the note C5, the default standard pitch A4 is 440 hz
523.2511306011972

>>> get_freq('C5', standard=415) # Get the frequency of the note C5, using the baroque standard pitch of 415 hz
493.5209527261292

>>> freq_to_note(500) # Get the frequency of the note closest to 500 hz, the default standard pitch A4 is 440 hz, the type of note is returned
B4

>>> freq_to_note(500, to_str=True) # Set to_str parameter to True, return the string representing the note
B4

>>> freq_to_note(500, standard=415) # Get the note with the frequency closest to 500 hz, using the baroque standard pitch of 415 hz
C5
```

## The play function can be abbreviated when selecting an instrument

When using the play function to specify an instrument to play, you can use instrument=instrument, and now add another, shorter argument, such as play a, a chord type, and you can write

```python
play(a, i=instrument)
```

is equivalent to

```python
play(a, instrument=instrument)
```

## When reading a MIDI file with all the notes of different instruments in one MIDI track, how to convert it to a piece type

I was recently reading some official MIDI files from Touhou Project and found that ZUN seems to basically put all the notes of all the different instruments in one MIDI track, generally speaking, a MIDI file can have up to 16 MIDI channels, multiple MIDI tracks, each MIDI track can have its own instruments settings, notes played by its own instruments, and each MIDI track can have its own name, and MIDI channel number. Each MIDI channel can have its own instrument settings, its own notes, and each MIDI channel can also have its own name and MIDI channel number. However, when I tried to read some of ZUN's official MIDI files (downloaded from THBWiki), I found that ZUN's MIDI files put all the notes of all the MIDI channels of different instruments in one MIDI track, and each note has a MIDI channel number attribute to distinguish which MIDI channel each note belongs to, and there are program change messages at the beginning of the MIDI file to specify the instrument types of each MIDI channel number, but there is only one MIDI track actually, so I had to design some algorithm to assign these notes to the MIDI track they belong to, and set the instruments for each MIDI track, and then it was easy to convert the MIDI file to the piece type. And since ZUN puts all the notes of all the different MIDI channels (corresponding to different instruments) in one MIDI track, the interval between notes is not the interval between the notes in each MIDI track after separation, I need to recalculate the correct interval of notes for each MIDI track, as well as the length of the notes, and the start time of each MIDI track start time. If you encounter this situation when reading a MIDI file, set `split_channels` to True when using the read function to do the above.

```python
# Read the official MIDI file of the first piece of "Touhou Koumakyou",
# which is the title theme piece "Scarlet Beyond a Crimson Dream", that is,
# the MIDI file that ZUN made itself and made public in the game

# th06 refers to the sixth Touhou Project series official work games,
# the first five works as the old work, all released in the late 90s.
# "Touhou Koumakyou", the sixth work, is also the first work of the new works.

a = read('th06_01.mid', split_channels=True)
```

## Added support for lowercase note names

In April 2021, support for lowercase names was added. Now the latest version of musicpy can write lowercase names when building any music type, and it works fine, for example

```python
a = note('A', 5)
a = note('a', 5)

b = note('Gb', 5)
b = note('gb', 5)

c = N('A6')
c = N('a6')

d = chd('Bb3', 'm11')
d = chd('bb3', 'm11')

e = C('Bbm11', 3)
e = C('bbm11', 3)

f = S('A major')
f = S('a major')

g = chord('C5, Eb5, G5, A#5, D#6, Bb6, F5')
g = chord('c5, eb5, g5, a#5, d#6, bb6, f5')
```

## eval_time function has been added to return time value

When the parameter mode of `eval_time` is set to `number`, the value of seconds is returned, and the value type is decimal.  
Also, in addition to chord type, the `eval_time` function also works with piece type.

```python
>>> a.eval_time(80, mode='number') # Calculate the length of time for a chord type, returning a numeric value
92.56
```

## Redesign of the reverse function

In June 2021, the `reverse` function has been redesigned. The redesigned algorithm of the `reverse` function gives results that are essentially the same as the reverse of the audio file (except for the sound of the beginning and end of the reverse). 
If you want to use the algorithm of the previous reverse function, you can use the `reverse_chord` function. This change is used for chord type, piece type and track type.

## Added the ability to read other types of MIDI messages

Now, in addition to the types of MIDI messages already defined in musicpy (including `tempo`, `pitch_bend`, `pan`, `volume`), musicpy can now read more other types of MIDI messages when reading MIDI files are stored in the property `other_messages`, which is a list of `other_messages`. `other_messages` will generally only be read for chord type and piece type.

You can also set `other_messages` to put other MIDI messages you want to write into the `other_messages` list when building chord type and piece type.
When using the `play` or `write` functions, the MIDI messages stored there will be written by default.

When using the `read` function, if you choose to return the chord type, then `other_messages` will be added to the chord type attribute, so that if you don't want other MIDI messages, then you can read them and then add the `other_messages` to the chord type attribute. If you don't want other MIDI messages, you can clear the `other_messages` of the chord type using the `clear` method of the list, e.g. `a.other_messages.clear()`.

If you choose to return the track type, then `other_messages` will be added to the chord type of each track, and the other MIDI messages of all the tracks will be merged as a whole `other_messages` and added to the track type property. When extracting a track for a read track type, the `content` of the extracted track type (which is a chord type) will also have the `other_messages` added during the previous read.

Since the other MIDI messages defined in musicpy includes `program_change`, that is, information about changing the instrument, if you modify the instrument of the piece type or track type, it is very likely that the instrument will not change after writing to the MIDI file because `other_messages` has the information of `program_change`. You can set the `nomsg` parameter in the `play` and `write` functions to True to ignore the `other_messages` (i.e., no writing), which will allow the instrument to change. Or you can clear the list of `other_messages`. If you still want to keep the other MIDI messages in `other_messages`, you can remove the MIDI messages in `other_messages` that are of type 'program_change', e.g. `a.other_messages = [i for i in a if i.type != 'program_change']`

When using the `play` function or the `write` function, when the chord type or the piece type (or other music theory type) written does not have the `other_messages` attribute or the list of `other_messages` is empty/None, you can specify the `msg` parameter and it will write the MIDI messages in the list of `msg` parameters. If the written piece type has the attribute `other_messages` and is not empty/None, it will write the piece type's own `other_messages`, and the `msg` argument of the `play` function or `write` function will be ignored.

If you don't want to write any `other_messages`, then you can set the `nomsg` parameter to True (the default is False), which will ignore the `other_messages` of the incoming piece type and the `other_messages` of the specified `msg` parameter.

### event type

In musicpy, event types represent MIDI messages.

All event types have `type` (MIDI message type string), `track` (track number, starting from 0), `start_time` (start time, starting from 0, in bars), which are all used in the same way.

The `type` parameter of the event type is based on the MIDI message type of the Mido library, and the parameters other than `track` and `start_time` are based on the parameters of the corresponding MIDI message types of the Mido library, please refer to the official documentation of Mido [Message Types](https://mido.readthedocs.io/en/latest/message_types.html) and [Meta Message Types](https://mido.readthedocs.io/en/latest/meta_message_types.html).

```python
event(type, track=0, start_time=0, is_meta=False, **kwargs)
```

## Improvement of the function to clear tempo changes and pitch bends for chord type and piece type

The `clear_tempo` function and the `clear_pitch_bend` function for chord type and piece type now have a `cond` parameter, and you can specify a conditional function (the lambda function is recommended) as a filter for the velocity change and pitch bend information you want to clear, the conditional function describes the conditions that the messages you want to clear needed to satisfy, and will be cleared if the conditional function returns True.

```python
# Clear the velocity change and bend information for chord type, using the cond argument, analogous to the piece type
a.clear_tempo(cond=lambda s: s.start_time == 1 and s.bpm == 120)
a.clear_pitch_bend(cond=lambda s: s.start_time == 1 and s.value == 0)
```

## Stop all currently playing sounds

You can use the `stopall` function to stop all currently playing sounds, specifically the sounds played by the `play` function.

```python
play(C('C') * 8)
stopall() # Stop the currently playing sound
```

## Use of interval names

There is a complete definition of interval names from database module in musicpy. For example, the value of `major_third` (major third) is 4 (semitones), and the value of `perfect_eleventh` (complete elevenths) is 17 (semitones) ).
In addition, the definition of abbreviated interval names has recently been added. For example, you can use `M3` instead of `major_third` (major third), `m3` instead of `minor_third` (small third),
`M7` instead of `major_seventh` (major_seventh) and so on. Here I will show the complete definition of interval names in musicpy, where all interval names can be used like `database.major_third`.

```python
perfect_unison = diminished_second = P1 = d2 = 0
minor_second = augmented_unison = m2 = A1 = 1
major_second = diminished_third = M2 = d3 = 2
minor_third = augmented_second = m3 = A2 = 3
major_third = diminished_fourth = M3 = d4 = 4
perfect_fourth = augmented_third = P4 = A3 = 5
diminished_fifth = augmented_fourth = tritone = d5 = A4 = 6
perfect_fifth = diminished_sixth = P5 = d6 = 7
minor_sixth = augmented_fifth = m6 = A5 = 8
major_sixth = diminished_seventh = M6 = d7 = 9
minor_seventh = augmented_sixth = m7 = A6 = 10
major_seventh = diminished_octave = M7 = d8 = 11
perfect_octave = octave = augmented_seventh = diminished_ninth = P8 = A7 = d9 = 12
minor_ninth = augmented_octave = m9 = A8 = 13
major_ninth = diminished_tenth = M9 = d10 = 14
minor_tenth = augmented_ninth = m10 = A9 = 15
major_tenth = diminished_eleventh = M10 = d11 = 16
perfect_eleventh = augmented_tenth = P11 = A10 = 17
diminished_twelfth = augmented_eleventh = d12 = A11 = 18
perfect_twelfth = tritave = diminished_thirteenth = P12 = d13 = 19
minor_thirteenth = augmented_twelfth = m13 = A12 = 20
major_thirteenth = diminished_fourteenth = M13 = d14 = 21
minor_fourteenth = augmented_thirteenth = m14 = A13 = 22
major_fourteenth = diminished_fifteenth = M14 = d15 = 23
perfect_fifteenth = double_octave = augmented_fourteenth = P15 = A14 = 24
minor_sixteenth = augmented_fifteenth = m16 = A15 = 25
major_sixteenth = diminished_seventeenth = M16 = d17 = 26
minor_seventeenth = augmented_sixteenth = m17 = A16 = 27
major_seventeenth = M17 = 28
semitone = halfstep = 1
wholetone = wholestep = tone = 2
```

## Modify the properties of the music theory type and return the new music theory type

The `reset` function can be used to reset any number of properties of a common music theory type and return a new music theory type, accepting only keyword arguments.

```python
reset(**kwargs)

a = C('C')
>>> a
[C4, E4, G4] with interval [0, 0, 0]
>>> a.reset(interval=[1,1,1])
[C4, E4, G4] with interval [1, 1, 1]
```

