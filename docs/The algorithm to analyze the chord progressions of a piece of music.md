# The algorithm to analyze the chord progressions of a piece of music

In `algorithms` module of musicpy, there are a chord progression analysis algorithm implemented to analyze the chord progressions of a piece of music or a fragment of music. The algorithm is implemented in `chord_analysis` function, along with `chord_functions_analysis` function which further analyzes each chord's function in a given scale.

Note that this algorithm is in experimental stage, and is not expected to perform well on every situation of a piece of music.

The `chord_analysis` function analyzes and outputs each chord splitted from the given chord instance (which represents a fragment of music).

## Usage
```python
chord_analysis(chords,
               mode='chord names',
               is_chord=False,
               new_chord_tol=database.minor_seventh,
               get_original_order=False,
               formatted=False,
               formatted_mode=1,
               output_as_file=False,
               each_line_chords_number=5,
               functions_interval=1,
               split_symbol='|',
               space_lines=2,
               detect_args={},
               split_chord_args={})
```
* chords: a chord instance to be analyzed
* mode: the output mode, here are the options:
  * 'chords': get the chord instances of each chord
  * 'chord names': get the chord names of each chord
  * 'inds': get the indexes of each chord
  * 'bars': get the bar ranges of each chord
  * 'bars start': get the start bars of each chord
* is_chord: if set to False, the algorithm will firstly perform a melody and chord split operation to the given chord instance, if set to True, use the given chord instance directly to analyze
* new_chord_tol: the new chord tolerance, pitch interval
* get_original_order: if set to True, get the original order of each chord that appears in the given chord instance
* formatted: if set to True, format the output results
* formatted_mode: format mode for the output string, could be 0 or 1
* output_as_file: when `formatted` is set to True, set this parameter to True to output the results as file
* each_line_chords_number: the number of chords in each line in the output string
* functions_interval: the number of spaces between each 2 chords in the output string
* split_symbol: the split symbol between each 2 chords in the output string
* space_lines: the number of empty lines between each 2 lines in the output string
* detect_args: the arguments for `detect` function
* split_chord_args: the arguments for `split_chord` function

## Examples

```python
import musicpy as mp
current_piece = mp.read('test.mid')
current_chord, current_bpm, current_start_time = current_piece.merge()
current_chord_progressions = mp.alg.chord_analysis(current_chord)
>>> print(current_chord_progressions)
['Dm7 omit F', 'Bm7 sort as [1, 3, 4, 2]', 'Em7 omit G', 'Dm7 omit F']
```
