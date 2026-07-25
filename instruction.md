You are an expert AI researcher specializing in multimodal deep learning, computer vision, audio processing, and PyTorch.

Your task is to build the strongest possible baseline for a multimodal multi-task learning competition.

=====================================================
PROBLEM
=====================================================

Each sample consists of two synchronized modalities:

1. RGB Image
2. Ultrasonic FLAC audio recording

The model predicts four outputs simultaneously.

Task 1
-------
Object_Type
Multi-class classification
Metric: Macro F1
Weight: 40%

Task 2
-------
Distance
Regression
Metric contribution:

max(0, 1 - RMSE / 2)

Weight: 30%

Task 3
-------
Location_Zone
Multi-class classification
Metric: Macro F1
Weight: 20%

Task 4
-------
Illumination
Multi-class classification
Metric: Macro F1
Weight: 10%

Overall competition score

Score = 100 × (
0.40 × Object F1
+
0.30 × max(0,1-RMSE/2)
+
0.20 × Zone F1
+
0.10 × Illumination F1
)

=====================================================
IMPORTANT
=====================================================

Do NOT use pretrained models.

Everything must be trained from scratch.

The solution must maximize validation score.

Think like an ML researcher rather than just writing code.

Inspect the dataset first before making assumptions.

=====================================================
STEP 1
DATASET ANALYSIS
=====================================================

Automatically inspect the dataset.

Report

- number of samples
- image size
- class distribution
- sample rates
- audio duration
- mono/stereo
- ultrasonic frequency range
- missing files
- missing labels
- class imbalance
- distance distribution

Also visualize

- waveform
- spectrogram
- log mel spectrogram

for several random samples.

=====================================================
STEP 2
DESIGN THE MODEL
=====================================================

Design a multimodal architecture.

RGB image

↓

Image CNN

↓

Image embedding

FLAC

↓

Spectrogram

↓

Audio CNN

↓

Audio embedding

↓

Fusion

↓

Shared feature

↓

4 prediction heads

The code should be modular so every component can easily be replaced.

=====================================================
IMAGE BACKBONE
=====================================================

Create a modern CNN from scratch.

Suggested block

Conv

BN

SiLU

Conv

BN

SiLU

Residual connection

Downsample

Increase channels

32

64

128

256

Use Adaptive Average Pooling.

Output embedding size

256

=====================================================
AUDIO BACKBONE
=====================================================

Load FLAC using torchaudio.

Inspect sample rate.

Never destroy ultrasonic information.

Build configurable preprocessing.

Support

Raw waveform

STFT

Log Mel Spectrogram

Power Spectrogram

Default

Log Mel Spectrogram

Train a CNN on the spectrogram.

Architecture similar to image CNN.

Output embedding

256

=====================================================
MULTIMODAL FUSION
=====================================================

Implement

1 Concatenation Fusion

2 Gated Fusion

3 Cross Attention Fusion

Use concatenation as baseline.

Automatically allow switching by config.

=====================================================
TASK HEADS
=====================================================

Shared Feature

↓

Object Head

↓

Distance Head

↓

Zone Head

↓

Illumination Head

Each head should optionally support

Linear

or

MLP

=====================================================
LOSSES
=====================================================

Classification

CrossEntropyLoss

Regression

MSELoss

Optional

SmoothL1Loss

Initial total loss

0.40*object

+
0.30*distance

+
0.20*zone

+
0.10*illumination

Track

raw losses

weighted losses

gradient norms

=====================================================
OPTIMIZATION
=====================================================

AdamW

Cosine Scheduler

Warmup

Gradient Clipping

Mixed Precision

EMA optional

=====================================================
VALIDATION
=====================================================

Compute

Object Macro F1

Distance RMSE

Zone Macro F1

Illumination Macro F1

Competition Score

Save best checkpoint according to competition score.

NOT validation loss.

=====================================================
ABLATIONS
=====================================================

Implement

Image Only

Audio Only

Image + Audio

Concatenation Fusion

Gated Fusion

Cross Attention Fusion

Distance normalization

MSE vs Huber

Different loss weights

=====================================================
ANALYSIS
=====================================================

After training automatically produce

Confusion Matrix

Per-class F1

RMSE histogram

Learning curves

Loss curves

Feature embedding visualization

Grad-CAM

Audio attention visualization if applicable

=====================================================
IMPORTANT
=====================================================

Write clean production-quality PyTorch.

Modular files.

Easy to extend.

Document every module.

Avoid hardcoding dataset names.

Infer everything possible from the dataset automatically.

Before writing the final model, explain why each design decision was made.

If a better multimodal architecture exists after inspecting the dataset, choose it instead of blindly following this prompt.

Before implementing the model, inspect the dataset and propose the top 3 architectures ranked by expected performance. Compare their parameter count, computational cost, expected strengths and weaknesses, then implement the strongest baseline and explain why it was selected.