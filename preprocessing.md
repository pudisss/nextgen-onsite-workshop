=====================================================
AUDIO PREPROCESSING (IMPORTANT)
=====================================================

To reduce training time and avoid repeatedly computing spectrograms every epoch, implement an offline preprocessing pipeline.

The preprocessing pipeline must run BEFORE model training.

Pipeline:

FLAC
↓

Load using torchaudio

↓

Inspect original sample rate

↓

Preserve ultrasonic information

↓

Convert stereo to mono if appropriate

↓

Crop or pad to a fixed duration

↓

Generate Log-Mel Spectrogram

↓

Apply log scaling (AmplitudeToDB or log(x + eps))

↓

Normalize the spectrogram

↓

Save the processed spectrogram to disk

The training loop must NEVER recompute spectrograms.

Instead, the Dataset should directly load the precomputed spectrogram files.

=====================================================
PRECOMPUTE CONFIGURATION
=====================================================

Create a standalone preprocessing script named:

precompute_mel.py

The script should:

- Scan every FLAC file
- Generate Log-Mel spectrograms
- Save each spectrogram as a .pt tensor
- Preserve the original filename
- Skip existing files unless overwrite=True
- Display a tqdm progress bar
- Report corrupted files
- Support multiprocessing when possible

Output structure:

dataset/
    images/
    audio/
        xxx.flac
        yyy.flac

    mel/
        xxx.pt
        yyy.pt

=====================================================
LOG MEL PARAMETERS
=====================================================

Make all parameters configurable.

Example defaults:

sample_rate = original sample rate
n_fft = configurable
win_length = configurable
hop_length = configurable
n_mels = 128
f_min = configurable
f_max = automatically determined from sample rate
power = 2.0

Use torchaudio.transforms.MelSpectrogram

Then convert using

AmplitudeToDB

or

torch.log(spec + 1e-6)

=====================================================
NORMALIZATION
=====================================================

Normalize every spectrogram after log scaling.

Support

1. Per-spectrogram normalization

spec = (spec - spec.mean()) / (spec.std() + 1e-6)

2. Dataset-level normalization (optional)

Default:
Per-spectrogram normalization.

=====================================================
DATASET
=====================================================

The Dataset must load

Image

+

Precomputed Mel Spectrogram (.pt)

instead of

Image

+

Raw FLAC

No spectrogram computation should occur during training.

=====================================================
AUDIO BACKBONE
=====================================================

Treat the Log-Mel spectrogram as a single-channel image.

Input:

[1, n_mels, time_frames]

Feed directly into the custom audio CNN.

=====================================================
ADVANTAGES
=====================================================

This design

- greatly reduces training time
- eliminates repeated STFT computation
- improves DataLoader throughput
- makes GPU utilization higher
- allows larger batch sizes
- keeps preprocessing deterministic