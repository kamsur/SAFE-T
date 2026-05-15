# Steps to get formant targets from audio files via SAFE-T GUI

## Loading audio files
1. Launch the application by running `player_pyqt6.py`.
2. Use the `Open Audio` button under `File` at top left of application window to select and load any audio file for analysis. Only `.wav` files are supported. Use the sample audio file inside `Test_data` directory for testing.
3. Upon successful loading, the waveform of the audio file will be displayed in the 'Audio Waveform' section. The spectrogram of audio will be displayed in the 'Spectrogram' section, with formant readings by Praat shown as red dots. The audio file location will be shown in the 'Audio Path' field in box below waveform plot
4. You can play the audio using the Play/Pause, Play Window, and Stop buttons below the waveform plot.

## Analyzing audio files

### Segment sentences
1. Click the `Load Stimulus` button below waveform plot to load the stimulus text file corresponding to the audio file. The stimulus text file should be a plain text file with one stimulus sentence per line. The text file at `Test_data/Stimulus_sentences.txt` can be used as an example. After choosing the stimulus file, the path to this file will be shown in the 'Stimulus Path' field.
2. Click the `Run ASR` button below waveform plot and choose one of the methods for sentence segmentation:
   - `Whisper AI`: Uses WhisperX automatic speech recognition and post-processing for sentence segmentation
   - `Subsequence DTW`: Uses Subsequence Dynamic Time Warping for sentence segmentation
3. After processing, the waveform and spectrogram plots will update to show sentence boundaries. The segmented sentences will be listed in the same box where 'Audio Path' is shown (scroll down inside box, if not visible). And the path to the generated TextGrid file will be shown in the 'Sentences TextGrid Path' field. A separate plot for sentence boundaries with sentence labels, `Sentence Labels`, will be visible below the box. You can play whole audio or only the window by using the Play/Pause, Play Window, and Stop buttons below the waveform plot, to verify the sentence boundaries
4. (Optional) Adjust sentence boundaries manually by dragging the vertical lines on the waveform or spectrogram plots, or on plot `Sentence Labels`. You can zoom in/out using the mouse scroll wheel for finer adjustments. You can delete a sentence by clicking on Delete button beside its label in the sentences list(in the box where 'Audio Path' is shown. Scroll inside the box if not visible). You can also add a new sentence through the text input fields below this box. Input the start time, end time, and text of the new sentence, then click the `Add Sentence` button to add it to the list and update the plots.

### Align phonemes/Segment vowels
1. Click the `Run MFA` button to perform phoneme alignment using Montreal Forced Aligner (MFA).
2. After processing, the waveform and spectrogram plots will update to show phoneme boundaries. A separate plot for phoneme boundaries with phoneme labels, `Phoneme Labels`, will be visible below the spectrogram. The path to a temporary directory created by SAFE-T (directory named PyPraat_sentences by default), containing separate audio files for each sentence and their corresponding text transcriptions and TextGrid files for aligned phonemes of each sentence, will be shown in the 'Sentences Path' field.
3. (Optional) Adjust phoneme boundaries manually by dragging the vertical lines on the waveform or spectrogram plots or on plot `Phoneme Labels`. You can zoom in/out using the mouse scroll wheel for finer adjustments. You can add phonemes by using the text input fields below the plot `Phoneme Labels`. Input the start time, end time, phoneme label, parent word and parent sentence of the new phoneme, then click the `Add Phoneme` button to add it to the list and update the plots.

### Extract formant targets
1. Click the `Load Landmark Info` button to extract formant targets for each vowel segment. First choose a cleaning method (Viterbi cleaning or No cleaning). Then file picker will pop up to choose the landmark info file. Choose the CSV file `landmark_identification_mod.csv`.
2. After processing, the path to landmark info file will be shown in the 'Landmark Info Path' field in box below waveform plot. Spectrogram will show the formant readings used for target extraction as colored dots, overlayed on top of raw Praat formant readings (red dots). And target positions will be shown with pink vertical lines on spectrogram. The targets will be plotted in vowel plots at bottom. Boundaries of vowels and timestamps of their targets will be shown in the box below the plot with correspending phoneme label and phoneme boundaries.
3. (Optional) You can adjust target positions by dragging the pink vertical lines on the spectrogram plot. You can zoom in/out using the mouse scroll wheel for finer adjustments. You can also delete a vowel and its targets by clicking on Delete button beside its label in the box below plot `Phoneme Labels`.
4. There is a scrollable list above the vowel plots, showing all stimulus sentences, their words of interest, and vowels, within those words that are plotted in the vowel plots. You can click on any vowel in the list to toggle its visibility in the vowel plots. You choose which formant to plot in which axis of the vowel plots using the dropdown menus beside `X Axis (Formant):` and `Y Axis (Formant):`. You can right-click on vowel plots to get more options like flipping the axes.
5. The 'Gender:' toggle button changes the reference vowel plot (in red). When toggled, the values in `landmark_identification_ground_truth.csv`, are used as reference. If gender is 'F' female gender data is used, otherwise male. Only female data is provided for now, from the paper: Cox, Felicity. (2006). The Acoustic Characteristics of /hVd/ Vowels in the Speech of some Australian Teenagers, Australian Journal of Linguistics. 26. 10.1080/07268600600885494. 
6. Click the `Export Formant Targets` button below waveform plot to save formant targets as visible in spectrogram and vowel plots. The formant targets for each vowel segment will be saved in a CSV file named `formant_targets.csv` in the same directory as the main project.

## Additional settings

### Adjusting settings of Viterbi-based formant cleaning
To activate emission cost in Viterbi-based formant cleaning (penalty for deviating from ground truth of formant targets), open `player_pyqt6.py` and set the variable `self.USE_GROUND_TRUTH` to `True`.

Once activated, the ground truth formant targets from `landmark_identification_ground_truth.csv` will be used during Viterbi cleaning to compute emission costs.

To set scaling factor for emission cost, modify the constants `VITERBI_EMISSION_MONOPHTHONG_WEIGHT` and `VITERBI_EMISSION_DIPHTHONG_WEIGHT` in `formants.py` to scale monophthongs and diphthongs respectively. This factor scales the influence of emission cost relative to transition cost during Viterbi-based cleaning.

Change the value of `VITERBI_DELTA` in `formants.py` to adjust at what value, Huber loss in transition cost becomes linear instead of quadratic.

### Adjusting Praat formant extraction settings
To adjust the Praat formant extraction settings (maximum formant frequency, number of formants, window length, time step, preemphasis base), open `formants.py` and modify the following constants as needed:
```
PRAAT_FORMANT_TIME_STEP = 0.0015 # in seconds
PRAAT_N_FORMANTS = 5
PRAAT_FORMANT_MAX_FREQ = 5500 # in Hz
PRAAT_FORMANT_WINDOW_LENGTH = 0.025 # in seconds
PRAAT_FORMANT_PREEMPHASIS_FROM = 50 # in Hz
```

### Adjusting log output location
To change the location where log files are saved, open `formants.py` and modify the constant `DEBUG_LOG_FILE` to the desired directory path:
```python
DEBUG_LOG_FILE = r"C:\path\to\desired\log\directory"
```
To change the location of log output for formant cleaning process, open `formants.py` and modify the constant `OUTLIER_FIX_LOG_FILE` to the desired directory path:
```python
OUTLIER_FIX_LOG_FILE = r"C:\path\to\desired\log\directory"
```

### Adjusting Subsequence DTW settings
To change the text-to-speech engine used for reference voice generation, uncomment one of the engines in `subsequence_dtw.py` under `generate_reference_audio` function:
```python
tts = gTTS(text=sentence, lang="en", tld=self.tts_tld, slow=False)
tts.save(output_path)
ref_audio, _ = librosa.load(output_path, sr=16000)
return ref_audio

# available_voices : [  'expr-voice-2-m', 'expr-voice-2-f', 'expr-voice-3-m', 'expr-voice-3-f',  'expr-voice-4-m', 'expr-voice-4-f', 'expr-voice-5-m', 'expr-voice-5-f' ]
# audio = self.kitten_tts_model.generate(sentence, voice='expr-voice-2-f', speed=1.2)
# sf.write(output_path, audio, 24000)
# ref_audio, _ = librosa.load(output_path, sr=self.sr)
# return ref_audio

# voice = self.piper_tts_model_male # or self.piper_tts_model_female
# with wave.open(output_path, "wb") as wav_file:
#     voice.synthesize_wav(sentence, wav_file, self.piper_syn_config)
# ref_audio, _ = librosa.load(output_path, sr=self.sr)
# return ref_audio
```
For piper text-to-speech engine, `*.onnx` model and `*.onnx.json` file corresponding to a voice model must be downloaded from Piper model repository and kept in project root directory (already present in this repository). See Piper model repository here:
`https://rhasspy.github.io/piper-samples/`