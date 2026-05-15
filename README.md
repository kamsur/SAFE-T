# SAFE-T
Semi-automatic formant recognition tool

## Operating System
Tested on Windows 11.

## Installation

**You will first need to install these external dependencies:**
- VLC Media Player
- FFmpeg
- Conda + Montreal Forced Aligner (MFA)

Installation instructions below.

### Build from Source

Clone the repository using git:
```bash
git clone https://github.com/hno-uker/MaRa25-SAFE-T.git
```

### Setup Montreal Forced Aligner (MFA)

Reference: https://montreal-forced-aligner.readthedocs.io/en/latest/installation.html

1. Install Conda-installation[https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html]. Python versions tested: 3.13.7.
    - Add directory of Conda executable to system PATH
    - Windows: Add `C:\Users\<YourUsername>\anaconda3\Scripts` to the PATH environment variable, for example.
    - Restart your terminal or IDE after modifying the PATH variable.
    - Check installation by running:
    ```bash
    conda --version
    ```
    - If path to `conda.exe`, cannot be added to system PATH (generally when conda is not in C:\ drive), then in `player_pyqt6.py`, under `self.CONDA_EXE_PATH`, set the full absolute path to `conda.exe` as in:
    ```python
    self.CONDA_EXE_PATH = r"C:\Users\<YourUsername>\anaconda3\Scripts\conda.exe"
    ```
    - If path to `conda.exe`, can be added to system PATH, then in `player_pyqt6.py`, under `self.CONDA_EXE_PATH`, set as:
    ```python
    self.CONDA_EXE_PATH = "conda"
    ```

2. Setup new virtual environment for MFA through Anaconda Prompt/Terminal. (MFA versions tested: 3.3.7. Python versions tested: Same as Conda. Use version-specific installation if future versions cause issues):
```bash
conda config --add channels conda-forge
conda create -n mfa_env1 montreal-forced-aligner
conda activate mfa_env1
mfa --help
```
The name of the environment (as in example above: `mfa_env1`) should match the name used in `player_pyqt6.py` under `self.MFA_ENVIRONMENT_NAME`.

3. Download pre-trained acoustic models and pronunciation dictionaries (already present in this repository).

Test data belonged to Australian English accent. With no acoustic model for Australian English available, the British English acoustic model and dictionary were used.

Download British English acoustic model from its release page(`english_mfa v3.1.0` tested):
1. Download link here[https://github.com/MontrealCorpusTools/mfa-models/releases/download/acoustic-english_mfa-v3.1.0/english_mfa.zip]. If link is not reachable, go to the download page and find the file. Download page here[https://github.com/MontrealCorpusTools/mfa-models/releases/tag/acoustic-english_mfa-v3.1.0]. Downloadable file: `english_mfa.zip`.
2. Place the downloaded `.zip` file in the following location, without unzipping:
   `<path_to_project_directory>/pretrained_models/acoustic/`

The file location of acoustic model is placed in `mfa_align.py` as `DEFAULT_MODEL_PATH = "pretrained_models/acoustic/english_mfa.zip"`

Download British English pronunciation dictionary from its release page (`english_uk_mfa v3.1.0` tested):
1. Download link here[https://github.com/MontrealCorpusTools/mfa-models/releases/download/dictionary-english_uk_mfa-v3.1.0/english_uk_mfa.dict] If link is not reachable, go to the download page and find the file. Download page here[https://github.com/MontrealCorpusTools/mfa-models/releases/tag/dictionary-english_uk_mfa-v3.1.0]. Downloadable file: `english_uk_mfa.dict`. 
2. Place the downloaded `.dict` file in the following location:
   `<path_to_project_directory>/pretrained_models/dictionary/`

The file location of dictionary is placed in `mfa_align.py` as `DEFAULT_DICTIONARY_PATH = "pretrained_models/dictionary/english_uk_mfa.dict"`

### Download vlc media player
1. Download and install VLC media player from https://www.videolan.org/vlc/. Version 3.0.21,64-bit, with 'minimum' installation preset tested. Needed for python-vlc package to work.
2. (Optional, if vlc import still fails) Add VLC installation directory to system PATH variable:
   - Windows: Add `C:\Program Files\VideoLAN\VLC` to the PATH environment variable.
   - Restart your terminal or IDE after modifying the PATH variable.
   - Check installation by running:
   ```bash
   vlc --version
   ```

### Setup FFmpeg
1. Download a pre-built FFmpeg `.zip` package for Windows, download link here[https://github.com/GyanD/codexffmpeg/releases/download/7.1/ffmpeg-7.1-full_build.zip] If link is not reachable, go to the download page and find the file. Download page here[https://github.com/GyanD/codexffmpeg/releases/]. Version `version 7.1-full_build-www.gyan.dev` tested. Downloadable file: `ffmpeg-7.1-full_build.zip`.
2. Extract the downloaded `.zip` file into `ffmpeg` directory, in a way that the `bin` directory is directly under `ffmpeg`. Move this folder to `C:\` drive so that the path to `ffmpeg.exe` is `C:\ffmpeg\bin\ffmpeg.exe`.
3. Add the `bin` directory of `ffmpeg` to your system PATH:
   - Windows: Add `C:\ffmpeg\bin` to the PATH environment variable.
4. Restart your terminal or IDE after modifying the PATH variable. Check installation by running:
```bash
ffmpeg -version
```

### Setup Python environment
1. Python 3.12.3 (non-Conda) tested for main application.
2. Create a virtual environment:
```bash
python -m venv .venv
```
3. Activate the virtual environment:
   - Windows:
   ```bash
   .venv\Scripts\activate
   ```
4. Install required packages:
```bash
pip install -r requirements.txt
```

## Run the application
1. Ensure the virtual environment is activated.
2. Run the application:
```bash
python player_pyqt6.py
```
First run may take some time as WhisperX ASR model and Wav2Vec2 are downloaded.

## Usage
Refer USAGE.md for detailed usage instructions.

## Data
Used sample data is available in `Test_data` directory.

## Algorithm documentation
1. **ASR_ALGORITHMS.md:** Describes the `Whisper AI` algorithm's implementation, used for sentence segmentation.
2. **SUBSEQUENCE_DTW_ALGORITHMS.md:** Describes the `Subsequence DTW` algorithm's implementation, used for sentence segmentation.
3. **FORMANT_ALGORITHMS.md:** Describes the algorithmic implementation used for cleaning of Praat formant readings and identification of formant targets.
4. **MAIN_ALGORITHMS.md:** Describes the algorithmic implementation behind all UI functionalities.