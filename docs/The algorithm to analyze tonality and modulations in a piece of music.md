# The algorithm to analyze tonality and modulations in a piece of music

In `algorithms` module of musicpy, there are several algorithms implemented to analyze the tonality of a piece of music or a fragment of music.

The tonality here includes any custom scales defined in `database` module, including major, minor, all church modes, melodic minor, harmonic minor and so on, users can also customize new scales with pitch intervals.

There are totally 3 algorithms implemented for the goal at this time, each algorithm is stored in a function, respectively `detect_scale`, `detect_scale2`, `detect_scale3`. Among these 3 algorithms, the third algorithm has the ability to analyze tonality and modulations of a piece of music, the other 2 algorithms can only analyze the tonality of a piece of music.

Here we firstly introduce the third algorithm, which at this time is the most useful one in these 3 algorithms.

## Usage
```python
detect_scale3(current_chord,
              get_scales=False,
              most_appear_num=3,
              major_minor_preference=True,
              unit=5,
              key_accuracy_tol=0.9,
              is_chord=True)
```
* current_chord: a chord instance to be analyzed
* get_scales: if set to True, get the scale instances as result, if set to False, get a string represents the tonality and modulations as result
* most_appear_num: number of the the most possible scales
* major_minor_preference: put the major or minor key to the first in result
* unit: the unit length in bars to analyze tonality and modulations
* key_accuracy_tol: the minimum percentage of the ratio of notes matched with a scale to be determined as the scale in the analysis range
* is_chord: if set to True, use the given chord instance directly to analyze, if set to False, merge the input as a list of chord instances to a chord instance and analyze

The output when `get_scales` is set to False is in the format `[start1, end1] scale1, scale2, ..., [start2, end2] scale1, scale2, ..., [startn, endn] scale1, scale2, ...`, each start and end is the bar range for each tonality that appears in the given input.

## Examples

```python
import musicpy as mp
current_piece = mp.read('test.mid')
current_chord, current_bpm, current_start_time = current_piece.merge()
current_scales = mp.alg.detect_scale3(current_chord)
>>> print(current_scales)
[0, 25] G major, E minor, [25, 44.25] G minor, A# major
```

