# Musicpy daw module

I wrote a daw module for musicpy to load instruments and export audio files (including wav, mp3, ogg and so on) in June 2021, which will be very useful since now you are no longer limited to only MIDI files (You can take the MIDI files exported by musicpy and put in DAW to load instruments and export audio files anyways).

Some of the basic audio mixing and editing functionalities are already implemented, including reverse, pan, ADSR envelope (currenly only with audio volumes), fade in/out effects.

You can also build basic waveforms that appeared on synthesizer, including sine waves, sawtooth waves, square waves, triangle waves and white noises.

Making new timbres using these basic waveforms with musicpy daw will probably be possible in the near future.



## Contents

- [Preparation before importing](#preparation-before-importing)
- [Initialize a daw object](#initialize-a-daw-object)
- [Supported instruments](#supported-instruments)
- [Load instruments](#load-instruments)
- [Play and export audio files](#play-and-export-audio-files)
- [Modify daw's attributes](#modify-daws-attributes)
- [Adding, deleting and clearing channels](#adding-deleting-and-clearing-channels)
- [Audio mixing and editing](#audio-mixing-and-editing)
  * [Audio mixing on mixer's level](#Audio-mixing-on-mixers-level)
  * [Audio mixing on musicpy data structure's level](#Audio-mixing-on-musicpy-data-structures-level)
- [Generating and playing with waveforms](#generating-and-playing-with-waveforms)
- [Pitch shifter (make a whole instrument using only one recording audio file)](#pitch-shifter-make-a-whole-instrument-using-only-one-recording-audio-file)
- [More about MDI instrument format](#more-about-mdi-instrument-format)
- [Some other reminders and thoughts](#some-other-reminders-and-thoughts)



## Preparation before importing

Firstly, open cmd/terminal and run `pip install sf2_loader pedalboard scipy numpy` to install all of the required python modules and packages to run musicpy daw. If you are using Linux or macOS, there are some necessary configuration steps for the required module sf2_loader, you can refer to [here](https://github.com/Rainbow-Dreamer/sf2_loader#installation) for details.

Next, please download `ffmpeg.zip` in [Releases](https://github.com/Rainbow-Dreamer/musicpy/releases/latest), extract the ffmpeg folder from the file, and then put the folder to `C:\` if you are on Windows or equivalent root path on macOS/Linux.

Then add the path `C:\ffmpeg\bin` to environment variables of your system (the path will be different if you are on macOS/Linux depends on the path you put the ffmpeg folder).

(this step is not required in some systems) Finally, restart the computer.

Then you can import the daw module in IDE or cmd/terminal using this line:
```python
from musicpy.daw import *
```



## Initialize a daw object

Let's say we want to make a new song, we'll initialize a daw called `my first song`.

This song will contain 3 instruments, and 1 channel for each instrument, so there will be 3 channels in total.

```python
new_song = daw(3, name='my first song')
>>> print(new_song)
[daw] my first song
Channel 1 | not loaded
Channel 2 | not loaded
Channel 3 | not loaded
```
Now you have initialized a daw object with 3 channels. The `not loaded` text means that currently this channel has not loaded any instruments yet.
Next we need to know what types of instruments that musicpy daw currently support and could load successfully.



## Supported instruments

The file formats that musicpy daw currently could load:

* SoundFont files (.sf2, .sf3, .dls)
* audio files (such as .wav, .mp3, .ogg)
* `MDI`, which is an instrument format invented by myself
* python scripts, which is a python instrument class called `Synth`



### SoundFont files

SoundFont is a very popular virtual instruments file format, you can load any SoundFont files into musicpy daw, supports .sf2, .sf3, .dls.

This daw module has built-in [sf2_loader](https://github.com/Rainbow-Dreamer/sf2_loader), which is my another project, you can refer to readme for documentation, you can use the syntax of sf2_loader to change the current instrument of loaded SoundFont files, play a piece of musicpy code by itself and so on. You can use `current_daw.instruments(i)` to get the instrument object that is loaded in the ith channel (0-based), (current_daw is the name of the variable of your current daw object), if there are SoundFont files loaded in the ith channel, then it will return a sf2_loader object.



### Audio files

Musicpy daw currently could load a folder of audio files as an instrument for each channel, the formats of the audio files could be mixed (for example, the folder could contains a mixture of wav, mp3, ogg files and so on). It is strongly recommended to name each audio file as a pitch, for example, `C5.wav`, which is a note name with an octave number. If the pitch name contains a flat sign, it is recommended to change it to an equivalent pitch name with a sharp sign (in twelve-tone equal temperament), for example, if it is `Ab5.wav`, change it to `G#5.wav`.

If the names of some audio files are not pitches, you will need to change the default mappings from musicpy note names to the file names of these audio files in order to match the pitches with the corresponding audio files you want correctly. I will talk about this later.



### MDI files

`MDI` is an instrument format invented by myself, which could merge and combine a folder of audio files (and maybe with a settings file) into a single binary file that is more convenient to operate and store.

You can use `make_mdi` function to make an mdi file easily:
```python
make_mdi(path_of_instruments_folder, name_of_instruments_you_want_to_have)
```
An mdi file will be generated in the current working directory.

I am planning to add support for VST (this is a very commonly used audio plugin format) in musicpy daw in the future.



### Python scripts

In `daw.py` file in musicpy folder, there is a class called `Synth`, which is a standard interface to make a python instrument or effect that could be used in a daw object.

You can copy the standard interface to a standalone python script, and then you can set the instrument name, author name, write python codes to generate sound or apply effects.

The method `generate_sound` is used to generate a sound when given a note instance and bpm, the return value is a pydub AudioSegment instance. This method is only used when it is loaded as an instrument. You can custom the instrument parameters in `instrument_parameters` attribute, and then use the parameter values in the method. It is encouraged to use mathematics and statistics python libraries such as numpy, scipy to play with sound waves.

The method `apply_effect` is used to apply effect on audio, it takes a pydub AudioSegment instance, and the return a new AudioSegment instance. This method is only used when it is loaded as an effect. You can custom the effect pararmeters in `effect_parameters` attribute, and then use the parameter values in the method. There are a lot of python dsp libraries could be used to apply interesting effects to the audio.

Some python instruments and effects examples could be found [here](https://github.com/Rainbow-Dreamer/musicpy-daw/tree/main/scripts/effects).



## Load instruments

You can load an instrument for each channel in a daw object.
```python
new_song.load(channel_number, path_of_instruments) # channel number is 0-based

new_song.load(0, 'piano') # load a folder named 'piano' with audio files as an instrument for the first channel

new_song.load(0, 'test.sf2') # load a soundfont file named 'test.sf2' as an instrument for the first channel

new_song.load(0, 'piano.mdi') # load an mdi file as an instrument for the first channel
```



## Play and export audio files

You can convert musicpy data structures to an audio file using `export` function of the daw object, or play using loaded instruments in the daw object using `play` function of the daw object.

Supported musicpy data structures to export or play include note, chord, piece and track.

You can also specify using which channel to play or export if the input is not a piece type. If the input is a piece type, you can specify using which channel for each track using the `daw_channels` attribute of the piece type.

Use `stop_playing` method of daw object to stop all playing sounds from the daw instance.

Use `pause` method to pause playing, `unpause` method to unpause playing, and play current musicpy data structure in loop by setting `loop` argument to `True` in `play` method.

```python
play(current_chord,
     channel_num=0,
     bpm=None,
     length=None,
     extra_length=None,
     track_lengths=None,
     track_extra_lengths=None,
     soundfont_args=None,
     wait=False,
     loop=False)

# current_chord: the musicpy data structures you want to play by the daw

# channel_num: the index of channel used to play, 0-based, used when current_chord is note or chord type

# bpm: the BPM (tempo) used to play, if not specified, the daw will use its own default bpm

# length: you can specify the whole length of the rendered audio in seconds (used in case of audio effects)

# extra_length: you can specify the extra length of the rendered audio in seconds (used in case of audio effects)

# track_lengths: the length settings of each track, could be a list or a tuple, used when current_chord is a piece instance

# track_extra_lengths: the extra length settings of each track, could be a list or a tuple, used when current_chord is a piece instance

# soundfont_args: refer to export function

# wait: function the same as the `wait` parameter in the play function of musicpy

# loop: if set to True, play current musicpy data structure in loop


export(obj,
       mode='wav',
       action='export',
       filename='Untitled.wav',
       channel_num=0,
       bpm=None,
       length=None,
       extra_length=None,
       track_lengths=None,
       track_extra_lengths=None,
       export_args={},
       show_msg=False,
       soundfont_args=None)

# obj: the musicpy data structures you want to export by the daw

# mode: the audio format of the exported audio file you want to have, supports wav, mp3, ogg and so on

# action: when set to 'export' which is the default value, export the input musicpy data structures to
# an audio file; when set to 'play', firstly convert the input musicpy data structures to an AudioSegment
# instance (an audio type in pydub), then play the AudioSegment instance; when set to 'get', convert the
# input musicpy data structures to an AudioSegment instance and return the AudioSegment instance for further use

# filename: the file name of the exported audio file you want to have

# channel_num: the index of channel used to export, 0-based, used when current_chord is note or chord type

# bpm: the BPM (tempo) used to export, if not specified, the daw will use its own default bpm

# length - track_extra_lengths: same as play function

# export_args: The keyword arguments dictionary of the format settings parameters of the exported audio file,
# you can refer to the parameters of export function of AudioSegment in pydub

# show_msg: When set to True, it will print the current progress when rendering the audio

# soundfont_args: When the instrument is SoundFont files, the dictionary of keyword arguments of rendering audio,
# here the arguments are corresponding to the arguments of export_chord function of sf2_loader


# play/export chord types examples
new_song.play(C('C')) # play a C major chord using the daw object with loaded instruments of channel 1
new_song.play(C('C'), 2) # do the same thing except using channel 2
new_song.play(C('C'), 2, bpm=165) # do the same thing except using channel 2 with BPM 165

new_song.export(C('C'), mode='wav') # export a C major chord as a wav file, using instruments of channel 1
new_song.export(C('C'), mode='mp3') # export a C major chord as a mp3 file, using instruments of channel 1
new_song.export(C('C'), channel_num=2, mode='wav', filename='my first song.wav') # export a C major chord as
# a wav file, using instruments of channel 2, the name of the exported wav file is "my first song.wav"

# play/export piece types examples
rule1 = lambda x: x % (1 / 8, 1 / 8) @ [1, 2, 3, 2]
part1 = rule1(C('D#', 5)) | rule1(C('F', 5)) | rule1(C('Gm', 5)) * 2
part1_bass = chord('D#3[.2;.], F3[.2;.], G3[.2;.], G3[.4;.], F3[.4;.]')
part1_harmony = part1_bass + perfect_fifth
drums = drum('K, H, S, H, r:2').notes
drums.set_volume(80)
current_song = P([part1 * 2 | (part1 + database.octave) * 2, part1_bass * 4, part1_harmony * 2, drums * 4],
                 bpm=120,
                 start_times=[0, 0, 4, 4.03],
                 daw_channels=[0, 1, 1, 2])
new_song.play(current_song) # play the piece type current_song
new_song.export(current_song, filename='my first song.wav') # export the piece type current_song to
# a wav file named "my first song.wav"
new_song.export(current_song, action='play') # this will play the piece type current_song,
# but firstly compiled it to an audio object, this is different from play function which is
# playing on the fly, this method will be slower to start playing but will have more stable playing

new_song.stop_playing() # stop all playing sounds from new_song
```



## Modify daw's attributes

You can change the channel names of the daw object by modifying `channel_names` attribute.
```python
new_song.channel_names = ['Piano', 'Bass', 'Electric Guitar']
>>> print(new_song)
[daw] my first song
Piano | not loaded
Bass | not loaded
Electric Guitar | not loaded

new_song.channel_names[0] = 'Piano 2' # change the name of the first channel to 'Piano 2'

# or you can use "set_channel_name" method to set the name of the channels, the indexing is 0-based
new_song.set_channel_name(1, 'Piano 2') # change the name of the first channel to 'Piano 2'
```

You can change the channel mappings of the daw object by modifying `channel_dict` attribute.

Change the mapping dictionary of some of the channels will be necessary if not all of the audio files in the instruments folder loaded for some channels are named as pitches, and you are lazy to rename them.

```python
# change some of the mappings of the 3rd channel of the daw object
# note that the mappings will always be a pitch maps to a file name (without file extensions)
new_song.channel_dict[2]['C2'] = 'Kick'
new_song.channel_dict[2]['E2'] = 'Snare'
new_song.channel_dict[2]['F#2'] = 'CH1'
```
**Please note, when you have modified the channel mappings dictionary of a channel, if the channel has loaded instruments, then you need to reload the instruments of the channel to make the modification of the channel mappings dictionary take effect.**

You can change the instruments of each channel by 2 ways:
```python
# simply load new instruments with new path for some of the channels
new_song.load(0, new_path_of_instruments)

# or modify the path of instruments for some of the channels
# in 'channel_instrument_names' attribute of the daw object
# and then reload for the channels which instruments path has been modified
new_song.channel_instrument_names[0] = new_path_of_instruments
new_song.reload_channel_sounds(0)
```

You can change the name of the daw object by modifying `name` attribute.
```python
new_song.name = 'another song'
>>> print(new_song)
[daw] another song
Channel 1 | not loaded
Channel 2 | not loaded
Channel 3 | not loaded
```

Each daw object will have a default bpm, it is used when you play or export without specifying bpm parameter.

The default value of the default bpm of a daw object is 120. You can modify the default bpm of a daw object by modifying `bpm` attribute.

```python
new_song.bpm = 150 # change the default bpm of new_song to 150
```

You can set the ith channel to be enabled or not by setting the ith value of `channel_enabled` to `True` or `False`, if a channel is not enabled, any track that using the instrument from the channel won't be rendered or played when play/export.

```python
>>> new_song.channel_enabled
[True, True, True]

>>> new_song.channel_enabled[0] = False # set the first channel to be not enabled
```



## Adding, deleting and clearing channels

To add a new channel to the daw object, use `add_new_channel` method:
```python
# this method takes one parameter "name", which is the name of the new channel,
# if the name parameter is not specified, it will be "Channel number" where number is the new channel's number
new_song.add_new_channel('Strings') 
>>> print(new_song)
[daw] my first song
Channel 1 | not loaded
Channel 2 | not loaded
Channel 3 | not loaded
Channel 4 | not loaded
Strings | not loaded

new_song.add_new_channel()
>>> print(new_song)
[daw] my first song
Channel 1 | not loaded
Channel 2 | not loaded
Channel 3 | not loaded
Strings | not loaded
Channel 5 | not loaded
```

To delete a channel of the daw object, use `delete_channel` method, the indexing is 0-based:
```python
new_song.delete_channel(1) # delete the first channel
# or you can use del keyword, the indexing is also 0-based
del new_song[1] # delete the first channel
```

To clear a channel of the daw object, use `clear_channel` method, the indexing is 0-based:  (clear a channel means to reset the channel's name, instruments and other set attributes)
```python
new_song.clear_channel(1) # clear the first channel
# the name of the first channel will be reset to 'Channel 1', and the instruments will be reset
# to 'not loaded' and the loaded instruments will be unloaded if the first channel has loaded
# instruments
```

To clear all of the channels of the daw object, use `clear_all_channels` method:  (note that this method is to delete all of the channels of the daw object, so that the channel number of the daw object will be 0 after you use this method because there are no channels in the daw object, the name of the daw object will not be reset)

```python
new_song.clear_all_channels()
>>> print(new_song)
[daw] my first song
```



## Audio mixing and editing

Currently some basic audio mixing and editing functionalities are already implemented in musicpy daw. There are 2 ways to do audio mixing in a daw object:

* Load python scripts that use the standard interface of `Synth` class with custom audio effect method or VST3 plugins (thanks to pedalboard) into the mixer of the daw object, where you can apply audio effects to master and each channel. By this way you are operating on the mixer's level.

* Use `effect` and `effect_chain` class to make lightweight audio effect and add them to musicpy data structures you want the daw object to play or export. By this way you can add audio effects to each note, chord, track and piece of the object you play or export.

Here I will talk about both ways, you can use either of these ways as you would like.



### Audio mixing on mixer's level

Each daw object has a built-in mixer, where you can add effects on master and each channel.

The `master_effects` attribute of the daw object is a list that could take `Synth` data structure as effect plugins. The `channel_effects` attribute of the daw object is a list of lists that could take `Synth` data structure as effect plugins for each channel. There is a list for each channel of the daw object to store effect plugins.

You can load effects from python scripts or a VST3 plugin to master or a channel by using `load_effect` method, when you load a VST3 plugin, it will be converted to a `Synth` data structure for more convenient processing.

```python
load_effect(channel_num, file_path)

# channel_num: when it is 'master', load the effect into master, when it is an integer, load the effect into the corresponding channel by index (0-based)

# file_path: the file path of the effect plugin, could be a python script file or VST3 plugin
```

You can also use the global function `load_effect` to load an effect plugin as a `Synth` instance from a file path.

For the `Synth` instance, you can set the `enabled` attribute to `False` to disable the effect when play or export.

You can add, remove and replace effects directly through `master_effects` and `channel_effects` attributes, and modify the effect parameters by modifying the `effect_parameters` dictionary of the `Synth` instance.

If you load a VST3 plugin as an effect, you can get the `VST3Plugin` instance from the `vst` attribute of the `Synth` instance.

P.S. Currently pedalboard library only supports loading and using VST3 effect plugins, and the developers are still working on the project to provide MIDI and VSTi plugins support. If pedalboard starts to support VST instruments in the future, I will integrate the functionality of loading and using VST instruments into the daw module.



### Audio mixing on musicpy data structure's level

#### General usage of audio effects on musicpy codes

There is a class called `effect` implemented in the musicpy daw module, which could store a type of audio effect, such as reverse, offset, fade in, fade out, ADSR envelope, and you can customize your own audio effect functions.

You can use `effect` class to package an audio effect function and put it as an element in a list, the list could be used as the value of `effects` in the play and export functions.

There are already some predefined effect instances such as `reverse`, `fade`, `adsr`. I will describe these predefined effects one by one below.

Before that, here is the usage of `effect` class, which tells you how to make an effect instance, how to pass in parameters to make a more specific effect instance, how to use effects for musicpy data structures.



#### The definition of effect class

```python
effect(func, name=None, *args, unknown_args=None, **kwargs)

# func: the function to process the audio effect, the first parameter must be a pydub AudioSegment instance,
# the other parameters could be customized as you like, the return value must be a pydub AudioSegment instance

# name: the name of the audio effect

# *args, **kwargs: the positional arguments and keyword arguments that
# the function to process the audio effect could receive

# unknow_args: the keyword arguments that the function to process the audio effect could receive,
# but cannot be known when packaged (for example, the bpm in real time)
```
Note that the `unknown_args` must be a dictionary, since it represents keyword arguments, for example, `unknown_args` could be something like `{'bpm': None}`



#### Examples of making an effect instance

Let say you have already written a function called `delay` which takes a pydub AudioSegment instance (must be the first parameter) and adding delay effects by an user-defined time interval of adjacent delay sounds and number of delay sounds, and then return the modified pydub AudioSegment instance.

The parameters of the `delay` function are `interval`, `unit`, with default values of 0.5 (in seconds) and 6.

So now if you want to make an effect instance called `delay_effect` with the effect name `delay`, you can write:
```python
delay_effect = effect(delay, 'delay')
```
Note that no parameters are specified for `delay_effect`, this is acceptable, because you can pass parameters later (which I will talk about in a minute) or the audio effect function could simply takes no parameters other than the AudioSegment instance, such as reverse.



#### The representation of an effect instance

```python
>>> delay_effect
[effect]
name: delay
parameters: [(), {}] unknown arguments: {}
```



#### Pass in parameters to set the values of parameters of the audio effect funcion

Besides of setting up parameters for the audio effect function when you initialize an effect instance, you can also pass in parameters in an already defined effect instance to set up the parameters for the audio effect function it takes, which returns a new effect instance, the parameters you pass in could be positional arguments and keyword arguments. For example:
```python
delay_effect1 = delay_effect(interval=0.2, unit=10) # using keyword arguments
# set up the parameters of the audio effect function 'delay' with interval = 0.2, unit = 10,
# delay_effect1 is a new effect instance with the same audio effect function and effect name,
# except the parameters of the audio effect function changed to what you pass in

>>> delay_effect1
[effect]
name: delay
parameters: [(), {'interval': 0.2, 'unit': 10}] unknown arguments: {}

delay_effect2 = delay_effect(0.2, 10) # or using positional arguments

>>> delay_effect2
[effect]
name: delay
parameters: [(0.2, 10), {}] unknown arguments: {}
```



#### How to use effect instances on musicpy data structures

you can use `set_effect` function to add a list of effects to common musicpy data structures (note, chord, track, piece).  

The return value of `set_effect` function is the same musicpy data structures wrapped by it, but with an attribute `effects` which will be read when it is passed in play and export functions of the daw. The added attribute `effects` is a list of the effect instances passed in by the `set_effect` function.

```python
set_effect(sound, *effects)

# sound: the common musicpy data structures

# *effects: you can pass in effect instances as positional arguments as much as possible,
# or pass in a list of effect instances

# examples

Cmaj7_chord = C('Cmaj7')

Cmaj7_chord = set_effect(Cmaj7_chord, [reverse, delay_effect(interval=0.2, unit=6)])

# or you could just use effect instances as arguments
Cmaj7_chord = set_effect(Cmaj7_chord, reverse, delay_effect(interval=0.2, unit=6))
```



#### Introducing effect_chain class

While an effect instance holds an effect on its own, there is also a called `effect_chain` which collects a couple of effect instances and could straightly receive musicpy data strcutures to add effects it contains. This sometimes makes the processing of a combination of audio effects simpler.

With `effect_chain` class, you can build up something like a mixer in a DAW, with a list of audio effects applied.

Here are the examples of how to build up an `effect_chain` instance and how to use it.
```python
mixer1 = effect_chain(delay_effect(interval=0.2, unit=6), fade_in(duration=2000), fade_out(duration=3000))
# you pass in effect instances as positional arguments when initialize an effect_chain instance

# the representation of an effect_chain instance
>>> mixer1
[effect chain]
effects:
[effect]
name: delay
parameters: [(), {'interval': 0.2, 'unit': 6}] unknown arguments: {}

[effect]
name: fade in
parameters: [(), {'duration': 2000}] unknown arguments: {}

[effect]
name: fade out
parameters: [(), {'duration': 3000}] unknown arguments: {}

# how to use an effect_chain instance on musicpy data structures

Cmaj7_chord = C('Cmaj7')

Cmaj7_chord = mixer1(Cmaj7_chord)
# you can just simply apply the effect_chain instance on musicpy data structures by wrapping it up,
# the return value is the same musicpy data structures with the effects stored
```



#### How audio effects are processed in the musicpy daw module

When you use play and export functions of the daw, if the musicpy data structures passed in has an attribute `effects`, and is a non-empty list, then the rendered audio of the musicpy data structures will be processed by the audio effect functions of the effect instances one by one.



Now we will talk about the predefined effect instances.



#### Reverse

You can add a reverse effect to common musicpy data structures by using `reverse`.
```python
a = C('Cmaj9') % (1, 1/8)
new_song.export(set_effect(a, reverse), 1) # export a Cmaj9 chord with a reverse effect

current_song = P([A1, set_effect(B1, reverse), C1, D1], daw_channels=[0, 1, 2, 3])
new_song.export(current_song)
# export the piece type current_song with the track B1 having a reverse effect

current_song2 = P([A1, B1, C1, D1], daw_channels=[0, 1, 2, 3])
new_song.export(set_effect(current_song2, reverse))
# export the piece type current_song2 with the whole piece type having a reverse effect
```



#### Offset

You can add a offset to common musicpy data structures by using `offset`. The unit of the offset is bar (in 4/4 time signature). Add offset to the audio means the audio will start to play at the offset location. In other words, the audio before the offset location will be cut off. The offset effect requires an unknown argument `bpm`, you can set it by usual keyword arguments or `unknown_args` when initialized if you know the bpm beforehand, or you can just leave it because the current bpm will be passed to the offset instance by default in the play and export function of the daw.
```python
a = C('Cmaj9') % (1, 1/8)
new_song.export(set_effect(a, offset(1/8)), 1)
# export a Cmaj9 chord with an offset at 1/8 bar, which means the audio will start to play at the place of 1/8 bar

current_song = P([A1, B1, C1, D1], daw_channels=[0, 1, 2, 3])
new_song.export(set_effect(current_song, offset(1))) # export the piece type current_song with an offset at 1 bar
```



#### Fade in/out effects

You can add fade in/out effects to common musicpy data structures by using `fade_in` and `fade_out`. Both of these 2 functions take a parameter `duration` which sets how much time the audio fades in/out, the time unit of duration is milliseconds, note that fade in/out means the volume of the audio gradually changes from 0% to 100% / from 100% to 0%. There is also a `fade` effect where you can set both of fade_in duration and fade_out duration.
```python
a = C('Cmaj9') % (1, 1/8)
new_song.export(set_effect(a, fade_in(500)), 1)
# export a Cmaj9 chord with a fade in effect of 0.5s, which means the audio will fade in from the beginning by 0.5s

new_song.export(set_effect(a, fade_out(500)), 1)
# export a Cmaj9 chord with a fade out effect of 0.5s, which means the audio will fade out to the end by 0.5s

new_song.export(set_effect(a, fade(500, 2000)), 1)
# export a Cmaj9 chord with a fade in effect of 0.5s and a fade out effect of 2s,
# which means the audio will fade in from the beginning by 0.5 s and fade out to the end by 2s
```



#### Add ADSR envelope

You can add ADSR envelope to common musicpy data structures by using `adsr`. Note that currently the ADSR envelope added to the musicpy code only controls volumes of the audio, which is the most classic way that ADSR envelope works. `adsr` function takes 4 parameters which respond to ADSR: `attack`, `decay`, `sustain`, `release`. `attack` specifies how much time the audio fades in from the beginning, `decay` specifies how much time the audio decrease its volume, `sustain` specifies the sustain level that the audio decreases to, `release` specifies how much time the audio fades out to the end. ADSR envelope is a tool that works well not only for musicpy data structures, but also for basic waveforms that could be generating from musicpy daw, as there are many possibilities in terms of timbres when combining ADSR envelope with basic waveforms (sine waves, triangle waves, etc.), The time unit of attack, decay and release are all milliseconds, the volume level unit of sustain is percentage, where 0% is mute and 100% is the loudest (could be an integer or float).
```python
a = C('Cmaj9') % (1, 1/8)
new_song.export(set_effect(a, adsr(500, 1000, 20, 2000)), 1)
# export a Cmaj9 chord with an ADSR envelope where attack = 0.5s, decay = 1s, sustain = 20%, release = 2s
```



#### Make your own audio effects

The first parameter of the audio effect function for the effect instance must be a pydub AudioSegment instance, you can read the raw audio data from it, and then write algorithms to perform audio signal processing using python packages like numpy, scipy and so on in order to achieve the desired mixing effect. The return value of the audio effect function for the effect instance must also be a pydub AudioSegment instance.



#### TODO

Planning to add some more predefined effect instances like EQ, reverb, compression, delay and so on in the future.



#### Panning and volume of channels

The pannings and volumes of channels of a piece type (or track type) also works for musicpy daw, 
as they will work the same as the output MIDI files.



#### Get the audio generated by daw from musicpy data structures

You can use `audio` function to get an AudioSegment instance from the daw object. The AudioSegment instance itself could be exported 
and do many other audio editing operations, for more details, you can refer to pydub's API documentations. The AudioSegment instance could also 
be put in musicpy data structures such as chord types and piece types, and pass to the daw to play or export.
```python
audio(obj, daw, channel_num=0, bpm=None)

# obj: the musicpy data structures to process, could be note/chord/piece/track

# daw: the daw object you want obj to pass

# channel_num: the index of channel you want to use in the daw object, 0-based

# bpm: the BPM (tempo) used to generate audio, if not specified, then use the daw's default bpm

audio(C('C'), new_song) # generate an AudioSegment object with the new_song daw object's first channel for a C major chord
```



#### Convert a list of audio objects to a chord type

If you have a list of AudioSegment objects and wanted to convert it to a chord type, you can use `audio_chord` function.
```python
audio_chord(audio_list, interval=0, duration=1/4, volume=127)

# audio_list: a list of AudioSegment objects to convert to chord type

# interval: the intervals of the chord type, you can refer to the interval parameter in the constructor function of chord type

# duration: the durations of the chord type, you can refer to the duration parameter in the constructor function of chord type

# volume: the volumes of the chord type, you can refer to the volume parameter in the constructor function of chord type

audio_chord([audio(C('Cmaj7') + i, new_song) for i in range(10)], 1/8, 1, 50)
# generate a chord type of AudioSegment objects with interval = 1/8, duration = 1, volume = 50
```



#### Load audio files from path into audio objects

Loading instruments into the daw objects might be convenient to do a play or export operation for whole musicpy data strcutures, 
but sometimes we need to operate on more single audio files one by one. You can use `sound` class to load an audio file from a file path 
and could use its `sounds` attribute (which is an AudioSegment object) in a chord type or piece type as an audio clip. An AudioSegment object 
could be used as a note in a chord type, and since piece type is built upon chord types, an AudioSegment object also works in piece types.

```python
sound(path, format=None)

# path: the file path of the audio file you want to load

# format: the audio file format of the audio file you want to load, if not specified,
# then auto detect the audio file format from its filename extension

sound_effect_1 = sound('sound effect.wav') # load an audio file 'sound effect.wav' as a sound object
sound_effect_1.play() # you can use sound object's function play to play the audio
sound_effect_1.stop() # stop playing the audio

>>> len(sound_effect_1) # you can use len(sound) to get the length of the audio clips in the sound object in milliseconds
2000

curren_audio = audio_chord([sound_effect_1.sounds, other_audio_objects])
# used the sound object's sounds attribute as an audio clip
```



#### Globally play and stop audio clips

You can use `play_audio` function to globally play AudioSegment objects and sound objects 
(and pitch objects as well, which will be talked about later), and you can use `stopall` function to stop all sounds playing currently.

```python
play_audio(sound_effect_1) # play the audio clip of sound object sound_effect_1
stopall() # stop all sounds playing currently
```



## Generating and playing with waveforms

Musicpy daw can generate some types of basic waveforms that appeared on synthesizer, including sine waves, sawtooth waves, square waves, triangle waves and white noises. Here we will go through the functions that could generate basic waveforms in musicpy daw module.  



### Sine waves

To generate sine waves, you can use `sine` function, this is a global function (which is not a method of daw class and uses globally)
```python
sine(freq=440, duration=1000, volume=0)

# freq: the frequency of sine wave

# duration: the duration of sine wave, the time unit is milliseconds

# volume: the volume of sine wave, the unit is percentage, from 0% to 100%, could be integer or float

sine_A4 = sine(440, 2000, 20) # generate a sine wave AudioSegment with frequency 440 hz (A4), duration of 2s and volume of 20%
```



### Triangle waves, sawtooth waves, square waves

To generate these basic waveforms, the parameters are the same as sine waves, you just need to change the name of the function from `sine` to `triangle` / `sawtooth` / `square`.



### White noise

To generate white noises, you can use `white_noise` function
```python
white_noise(duration=1000, volume=0)

# duration: the duration of white noise, the time unit is milliseconds

# volume: the volume of white noise, the unit is percentage, from 0% to 100%, could be integer or float

white_noise(2000, 20) # generate a white noise with duration of 2s and volume of 20%
```



### Generate a chord type of basic waveforms

Well, now you may think: yeah we can generate these basic waveforms, that's kind of cool, but how can we use them in musicpy daw? 

And how can we get a chord type or even piece type of these basic waveforms? Here are the answers.

To generate a chord type of basic waveforms, you can use `get_wave` function (this is a global function), this is a convenient function to generate basic waveforms from musicpy data structures.

```python
get_wave(sound, mode='sine', bpm=120, volume=None)

# sound: the musicpy data structures to process, must be chord type

# mode: the type of basic waveforms you want to generate, must be one of 'sine', 'triangle', 'sawtooth', 'square', or a function 
# that takes exactly 3 parameters: frequency, duration, volume (must be in this order) and return an AudioSegment object,
# you can refer to `sine`, `triangle`, `sawtooth`, `square`, `pulse` function in daw.py

# bpm: the BPM (tempo) of the basic waveforms, this will determine the absolute durations of generated basic waveforms

# volume: the volumes of basic waveforms, could be an integer or float, or a list of volumes, the unit is percentage,
# if set to None, then use the volumes of the musicpy data structures

get_wave(C('C') % (1, 1/8), 'sine', volume=20) # generate a chord type of sine waves of C major chord with volume of 20%
get_wave(C('C') % (1, 1/8), triangle, volume=20) # generate a chord type of triangle waves of C major chord with volume of 20%
```
The generated chord types of basic waveforms could be used as regular musicpy data structures in the play/export function of the daw object, and could be put in a piece type (or a track type) to play/export by the daw object.



### Generate more general waveforms

You can use `pulse` function to generate more general waveforms, where you can specify the duty cycle of the waveform. 
Actually this still limits to only pulse waves (which includes square waves), but you can have a lot more new timbres from this function.
```python
pulse(freq=440, duty_cycle=0.5, duration=1000, volume=0)

# freq: the frequency of the waveforms

# duty_cycle: the duty cycle of the pulse wave, note that square waves have duty cycle of 50% (duty_cycle = 0.5)

new_waveform = pulse(440, 0.2, 2000, 20)
# generate a pulse wave of duty cycle of 20% with frequency 440 hz (A4), duration of 2s, volume of 20%
```

If you want to generate general waveforms from mathematical and statistical functions, you can import SignalGenerator class from pydub, 
```python
from pydub.generators import SignalGenerator
```
write a new class that inherits SignalGenerator (which is set SignalGenerator as a parent class), and override `__init__` and `generate` methods, where `__init__` is the constructor function and `generate` is the function that yields sample data as an iterator when called each time. You can refer to [here](https://github.com/jiaaro/pydub/blob/master/pydub/generators.py) to see how to override `generate` function of SignalGenerator to use mathematical and statistical functions to generate more general waveforms.



## Pitch shifter (make a whole instrument using only one recording audio file)

There is a pitch shifter written in musicpy daw module to make pitch changes of audio clips.

**Please note: some extra python libraries are required for using this part of functionality, you can run `pip install librosa soundfile` in cmd/terminal to install.**

You can change the pitch of audio clips to higher or lower by semitones, or more microtonal unit.

You can use `pitch` class to load audio files from file path, and you can do some interesting stuffs using pitch object.

You can use `pitch_shift` function of the pitch object to make pitch changes of audio clips, the unit is semitones, could be integer or float, and there are 2 methods to make pitch changes, 'pydub' or 'librosa' method, you can choose one of them.

I will explain the differences between these 2 methods later. The result of pitch changes is an AudioSegment object.

For short, you can use `+` and `-` after a pitch object with the value of semitones to perform pitch changes, like `pitch + 2`, `pitch + 1.5`, `pitch - 1`, note that 'librosa' method will be used when you use this syntax.

You can use `get` function of pitch object to get an AudioSegment object from provided pitch name.

You can use `set_note` function of pitch object to reset the initial note of the pitch object.

You can use `play` and `stop` function of pitch object to play and stop audio clips loaded in the pitch object.

To generate a whole instrument using the audio file loaded in the pitch object, you can use `export_sound_files` function of the pitch object, and `generate_dict` function of the pitch object will generate a dictionary with pitch names and their corresponding audio clips.
```python
pitch(path, note='C5', format=None)

# path: the file path of the audio file you want to load

# note: the initial pitch of the audio file, could be a string of pitch or a note type

# format: the audio file format of the audio file

pitch_1 = pitch('voice.wav', note='C5') # load an audio file of someone's voice, initial pitch is C5
new_pitch_1 = pitch_1.pitch_shift(1) # shift up the pitch of pitch_1 by 1 semitone, and get an AudioSegment object
# the default pitch shift method is `librosa`, you can specify the pitch shift method to be
# 'pydub' using mode parameter
new_pitch_1 = pitch_1.pitch_shift(1, mode='pydub') # shift up the pitch of pitch_1 by 1 semitone, using 'pydub' method
```
The differences between 'pydub' and 'librosa' methods are that 'pydub' method make pitch changes by changing the sample rates of the audio clips, but this results in speed changes of the audio clips while making the pitch changes.

In 'pydub' method, the higher the pitch changes to, the faster the audio clip changes to, in contrast, if the pitch changes to be lower, the audio clip changes to be slower, in other words, the length of the audio clip changes, which is not we want in many situations, although 'pydub' method is much more faster than 'librosa' method in comparison.

In 'librosa' method, it performs a FFT to the raw audio data of the audio clips, and get a numpy array of the result of FFT, and using the numpy array to make transformations in order to make pitch changes, this method is slower since it performs some mathematical and statistical computations, but the speed of 
the audio clips won't change, the length of the audio clips will remain the same before the pitch changes.

Note that 'librosa' method might result in a lower audio quality after pitch changes compared to 'pydub' method, especially when make pitch changes to long audio clips (several minutes for example), if it is an audio clip for not more than few seconds, this is not that noticeable.

The default method of pitch_shift function of the pitch object is 'librosa' method.
```python
pitch_1.get('D5') # get pitch shifted AudioSegment object of pitch D5 of pitch_1 (initial pitch is C5)

pitch_1.set_note('C6') # reset the initial pitch of pitch_1

pitch_1.pitch_shift(1.5) # pitch shift up by 1.5 semitones
pitch_1 + 1.5 # pitch shift up by 1.5 semitones
pitch_1.pitch_shift(-0.5) # pitch shift down by 0.5 semitones
pitch_1 - 0.5 # pitch shift down by 0.5 semitones

pitch_1_dict = pitch_1.generate_dict(start='A0', end='C8')
# generate a dictionary of pitch name and pitch shifted AudioSegment objects

pitch_1.export_sound_files(path='.', folder_name="someone's voice", start='A0', end='C8', format='wav')
# export a whole instrument of pitch_1 ranges from A0 to C8 named 'someone's voice' to current working directory,
# the audio file format for this instrument is wav, given that the initial pitch of pitch_1 is C5,
# you will have a folder of pitch shifted audio files of the audio clips in pitch_1 named 'someone's voice'
# in the path you specified, and this folder could be loaded into the daw object as an instrument

# note that for both of generate_dict and export_sound_files methods, you can specify
# the method to make pitch changes with mode parameter.

>>> len(pitch_1) # you can use len(pitch) to get the length of the audio clips in the pitch object in milliseconds
2000

# the play and stop function of the pitch object are similar to the sound object
pitch_1.play()
pitch_1.stop()
```



## More about MDI instrument format

MDI is an instrument format invented by myself to pack a folder of audio files (and maybe with a settings file) into a single binary file that is more convenient to operate and store. The name of MDI stands for Musicpy Daw Instrument.

Musicpy daw is one of my other projects, which is mainly a plaintext music DAW that is fully compatible with musicpy. You can use musicpy daw to load instruments and make music with musicpy, export audio files with various of audio file formats. Musicpy daw has GUI, so you can make music with musicpy and some helps from GUI. I suggest that you take a look at this project that I spend a lot of time on. This project is on github (click [here](https://github.com/Rainbow-Dreamer/musicpy-daw)), you could add a star or fork if you like it.

In fact, the musicpy daw module is a porting of musicpy daw in musicpy. I firstly finished the development of musicpy daw, and then port the functionalities of musicpy daw to musicpy, which results in a new module in musicpy called musicpy daw. So musicpy daw module is essentially a non-GUI version of musicpy daw. I almost port all of the functionalities of musicpy daw to the musicpy daw module, so musicpy daw and the musicpy daw module share almost the same set of functionalities. As the name of MDI, this instrument format comes from musicpy daw, you can make, load and unzip MDI files in musicpy daw.

You can use `make_mdi` function to make MDI instruments from a folder of audio files. Each time you make an MDI file from a folder of audio files, you will have an MDI file generated at the current file path. The MDI files contain information for unzipping instruments files, so it could be used independently.

Besides audio files, you can also add a settings file in the folder to make MDI file, which is a text file (.txt) of some informations to set the mappings of the instrument's dictionary of a channel.  

The format is very simple, you write a change of mappings with `pitch,value` on each line, note that the whitespaces will be counted into pitch and value, so it is recommended to not add any whitespaces in each line.

I will give an example of the format of the informations in a settings file.  

If we want to change some of the mappings of a channel with drum instrument, for example,

```python
new_song.channel_dict[2]['C2'] = 'Kick'
new_song.channel_dict[2]['E2'] = 'Snare'
new_song.channel_dict[2]['F#2'] = 'CH1'
```
Then we can write this in the settings file in the drum instruments folder:
```
C2,Kick
E2,Snare
F#2,CH1
```
and then use
```python
new_song.load(2, 'drum.mdi')
```
to load drum instruments (if the name of drum's MDI file is called drum.mdi).

You can use `unzip_mdi` function to unzip MDI files into a folder of audio files (and a settings file if it has) for further uses.  

```python
unzip_mdi(file_path, folder_name=None)

# file_path: the file path of the MDI file

# folder_name: the name of the folder you unzip to

unzip_mdi('drum.mdi', 'drum from mdi')
# unzip the audio files from drum.mdi to a folder named 'drum from mdi'
```



## Some other reminders and thoughts

1. Note that the tempo changes and pitch bends will not work for musicpy daw, but there are some ways to work out.  

   For tempo changes, with `normalize_tempo` function, you can normalize a chord type or a piece type's tempo changes as the tempo changes will straightly appear on the durations and intervals of the notes, which will result in a new chord type or piece type that is essentially the same as before but without tempo changes.  

   For pitch bends, it works for SoundFont files, but not for the audio samples. The reason that why it is not working for audio samples in the musicpy daw is that currently the pitch of audio samples (audio files) cannot be modified primarily, so pitch bends are ignored by musicpy daw when using audio samples, but I write codes to make this works with the loaded SounFont files, so if you are using SoundFont files as instruments on the channels, then it will work perfectly.

   If you want to have pitch bends in the exported audio files when using audio samples, you can have audio samples that is pitch bend itself in the instruments and set a special pitch with pitch bends audio samples in the mappings of channels.

4. If you hear some crackles sounds when notes start or end when playing or in the exported audio files, 
   you can add some little fade effects to each note in the chord types or piece types to remove the crackles sounds, usually 20 milliseconds is enough to remove the crackles sounds entirely, and it is very effective, since the fade in and fade out time are very short that you won't be able to notice that while the crackles sounds are removed entirely. You can do list comprehensions or for loops to add little fade effects to each note in the chord types or piece types. For example:

   ```python
   # add little fade effects to all of the notes in chord types
   piano.notes = [set_effect(i, fade(20, 20)) for i in piano.notes]
   
   # add little fade effects to all of the notes in all of the tracks in piece types
   for each in current_song.tracks:
       each.notes = [set_effect(i, fade(20, 20)) for i in each.notes]
   ```
