# Bird song detection

My bachelor thesis at Charles University. A desktop tool that finds bird song in long field recordings. Researchers label a few recordings, train a neural network, and let it mark the rest.

<!-- TODO: link the thesis PDF and add one result, e.g. precision and recall on the test set. -->

## Pipeline

1. **Labels**: read song start and end times from Excel sheets, one row per song.
2. **Slicing**: cut WAV files into 3-second windows. Windows inside a labeled song are positive. Random windows outside are negative.
3. **Augmentation**: overlay background noise from negative windows onto positive ones. More augmentations give a more robust model, at the cost of RAM and extraction time.
4. **Features**: convert each window to MFCCs (mel-frequency cepstral coefficients), a compact description of the sound spectrum, using `librosa`.
5. **Models**: train and compare an MLP, a CNN, an RNN, and a CRNN (stacked GRU and 1D convolution layers) in Keras. The CRNN is the model the tool keeps.
6. **Detection**: slide a 3-second window over new recordings with 75% overlap. Write `start`, `end`, `activity` rows to `<recording>_results.csv`.

## Run

Needs Python with `tensorflow`, `keras`, `librosa`, `scikit-learn`, `openpyxl`, `pandas`, and `pathos`.

```sh
python GUI.py
```

1. Click `Extract data`, then pick the folder with WAV files and Excel labels.
2. Enter which Excel columns hold the recording name, song start, and song end.
3. Click `Train model`.
4. Click `Apply model on data`, then pick the recordings to scan.

Extraction can take hours on an HDD, and the window freezes while it works. `Manual.txt` has more usage notes.

## Code map

| File | Role |
|---|---|
| `GUI.py` | Tkinter app: extract, train, save, load, and apply |
| `data_processing.py` | Label parsing, slicing, augmentation, and MFCC features |
| `Machine_learning.py` | The four model architectures and training |
