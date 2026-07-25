=====================================================
OVERFITTING REDUCTION STRATEGIES
=====================================================

The current baseline shows signs of overfitting.

Improve generalization using modern regularization techniques while keeping
all features configurable for ablation studies.

Do NOT permanently enable every technique.

Everything should be controlled from the configuration.

=====================================================
CONFIGURATION
=====================================================

Add the following configuration variables:

USE_LABEL_SMOOTHING = True
LABEL_SMOOTHING = 0.10

USE_CLASS_WEIGHTS = False

USE_MIXUP = False
MIXUP_ALPHA = 0.20

USE_CUTMIX = False
CUTMIX_ALPHA = 1.0

USE_RANDOM_ERASING = True
RANDOM_ERASING_P = 0.20

USE_SPEC_AUGMENT = True
TIME_MASK_PARAM = 20
FREQ_MASK_PARAM = 10

USE_GRADIENT_CLIPPING = True
GRAD_CLIP_NORM = 1.0

EARLY_STOPPING_PATIENCE = 10

WEIGHT_DECAY = 1e-4

HEAD_DROPOUT = 0.30

BACKBONE_DROPOUT = 0.20

=====================================================
IMAGE AUGMENTATION
=====================================================

Create a training augmentation pipeline designed for generalization.

Recommended baseline:

RandomResizedCrop(224)

RandomHorizontalFlip
(only if it is semantically valid for the dataset)

RandomRotation
(small angle only)

ColorJitter

brightness=0.10
contrast=0.10
saturation=0.05
hue=0.02

RandomErasing

ToTensor

Normalize

Avoid aggressive augmentation because one prediction target is
illumination.

Do NOT use heavy AutoAugment or RandAugment unless configured.

=====================================================
SPECTROGRAM AUGMENTATION
=====================================================

If USE_SPEC_AUGMENT=True

Apply SpecAugment after loading the Mel spectrogram.

Include

Frequency Masking

Time Masking

Small Gaussian Noise

Keep augmentation moderate.

Do not completely destroy the signal.

=====================================================
LABEL SMOOTHING
=====================================================

Use

CrossEntropyLoss(
    label_smoothing=LABEL_SMOOTHING
)

for every classification head when enabled.

Regression loss should remain unchanged.

=====================================================
DROPOUT
=====================================================

Use dropout in

Image backbone projection layer

Audio backbone projection layer

Fusion MLP

Prediction heads

Recommended defaults:

Backbone projection:

0.20

Fusion:

0.30

Heads:

0.30

Make every dropout probability configurable.

=====================================================
WEIGHT DECAY
=====================================================

Use AdamW.

Weight decay should be configurable.

Suggested default:

1e-4

Provide recommended values to experiment with:

5e-5

1e-4

5e-4

1e-3

=====================================================
EARLY STOPPING
=====================================================

Early stopping must monitor ONLY

Validation Competition Score

NOT validation loss.

Patience:

10 epochs

Restore the best checkpoint automatically.

=====================================================
MODEL CAPACITY
=====================================================

Do not make the backbone unnecessarily deep.

Provide a configuration option:

MODEL_SIZE

Options:

"small"

"base"

"large"

Example:

Small

Channels:

32

64

128

256

Base

32

64

128

256

256

Large

32

64

128

256

512

Default:

small

=====================================================
OPTIONAL MIXUP
=====================================================

Implement MixUp support.

Default:

disabled

Only apply to image inputs.

Adjust

classification targets

and

distance regression target

correctly.

Document the implementation carefully.

=====================================================
OPTIONAL CUTMIX
=====================================================

Implement CutMix support.

Default:

disabled.

Again, update regression labels correctly.

=====================================================
MONITOR GENERALIZATION
=====================================================

Every epoch log

Training loss

Validation loss

Training competition score

Validation competition score

Generalization gap

Training Object F1

Validation Object F1

Training Distance RMSE

Validation Distance RMSE

Training Zone F1

Validation Zone F1

Training Illumination F1

Validation Illumination F1

=====================================================
GENERALIZATION GAP WARNING
=====================================================

Automatically print a warning if

Training Competition Score

exceeds

Validation Competition Score

by more than

10%

Example

WARNING:
Possible overfitting detected.

=====================================================
BEST CHECKPOINT
=====================================================

Always save

best_score.pt

based on

highest validation competition score.

Never overwrite it with a worse epoch.

=====================================================
FINAL REPORT
=====================================================

After training print

Best epoch

Best validation competition score

Generalization gap

Whether early stopping occurred

Which regularization techniques were enabled

Final dropout values

Final weight decay

Final label smoothing

Whether SpecAugment was used

Whether MixUp was used

Whether CutMix was used

Provide recommendations for which techniques should be evaluated in future
ablation experiments.