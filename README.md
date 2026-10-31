# Modeling Human Activity States Using Hidden Markov Models

A four-state Hidden Markov Model implemented from scratch in NumPy, classifying
Still, Standing, Walking and Jumping from smartphone accelerometer, gyroscope and
gravity data.

## Result

100% sensitivity, specificity and accuracy on 24 windows of unseen data recorded a
day after training, and 100% across 161 windows under 16-fold leave-one-session-out
cross-validation.

## Data collection videos

Four videos, one per activity: **[Drive folder](https://drive.google.com/drive/folders/1L_BiMtVd66Uj5IrzgEE1uaRbxkkq-mQr?usp=sharing)** — individual
links in [videos.md](videos.md).

## Dataset

| | |
| --- | --- |
| Device | iPhone 11 Pro, iOS 18.7.7 |
| App | Sensor Logger 1.68 |
| Sensors | Accelerometer, gyroscope, gravity |
| Sampling rate | 50 Hz configured, 50.194 Hz measured (max jitter 0.0439 ms) |
| Recordings | 26 sessions, 20 used after quality control |
| Labelled files | 82 training + 12 test, 7 s each |

| Activity | Training files | Duration | Test files |
| --- | --- | --- | --- |
| Still | 24 | 164 s | 3 |
| Standing | 24 | 166 s | 3 |
| Walking | 18 | 124 s | 3 |
| Jumping | 16 | 110 s | 3 |

Training data recorded 25 October 2026; test sessions recorded 26 October 2026 in a
different room, with no session contributing to both splits.

Six recordings were excluded during quality control: four carried the phone in a
grip that placed gravity on the wrong axis, and two were duplicate exports. Reasons
are recorded per session in `data/session_manifest.csv`.

## Method

**Windowing.** 4-second analysis windows (200 samples at 50 Hz) with 50% overlap,
giving 0.25 Hz frequency resolution. A 2-second window resolves only 0.5 Hz, which
cannot separate walking (1.74 Hz) from jumping (1.95 Hz).

**Features.** Twelve per window — six time-domain (variance of vertical
acceleration, RMS, signal magnitude area, mean gyroscope magnitude, vertical range,
inter-axis correlation), three orientation (mean gravity per axis), and three
FFT-derived (dominant frequency, spectral energy, spectral entropy). Log compression
then Z-score normalisation, fitted on training data only.

**Model.** Gaussian emissions with diagonal covariance. Forward-backward in log
space to avoid underflow, Baum-Welch with a log-likelihood convergence criterion
(epsilon = 1e-4), Viterbi decoding.

## Repository

| Path | Contents |
| --- | --- |
| `HMM_Activity_Recognition.ipynb` | Full pipeline, outputs saved |
| [`report.pdf`](report.pdf) | Report (also viewable as a [live doc](PASTE_DOC_LINK)) |
| `videos.md` | Data collection video links |
| `data/train/` | 82 labelled CSVs |
| `data/test/` | 12 labelled CSVs |
| `data/raw/` | Raw Sensor Logger exports |
| `data/session_manifest.csv` | Per-session rate, jitter, exclusions |

File naming: `activity_session_window.csv`, e.g. `walking_s2_w03.csv`.

## Running it

Open `HMM_Activity_Recognition.ipynb` in Google Colab, place `Data.zip` in Drive,
update the `DRIVE_ZIP` path, then Run all. Requires numpy, pandas and matplotlib.
