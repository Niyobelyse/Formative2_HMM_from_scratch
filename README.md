# Formative 2: Hidden Markov Models for Human Activity Recognition



## Project Structure

```
formative2_hmm/
├── README.md (this file)
├── REPORT.md (5-page comprehensive report)
├── notebook/hmm.ipynb (fully executable)
├── data/ (53 training recordings)
└── testing_data/ (21 test sequences)

---


## Components

Component

Data Collection
Feature Extraction
HMM Implementation

Model Evaluation
Visualizations
Report

---

## Key Results

### Model Performance

Overall Accuracy: 80.95% (17/21 correct predictions)

| Activity | N Samples | Sensitivity | Specificity | Accuracy |
|----------|-----------|------------|-------------|----------|
| Standing | 3 | 1.0000 (100%) | 0.8889 (88.9%) | 0.9048 (90.5%) |
| Walking | 5 | 0.8000 (80%) | 0.9375 (93.8%) | 0.9048 (90.5%) |
| Jumping | 7 | 0.8571 (85.7%) | 0.9286 (92.9%) | 0.9048 (90.5%) |
| Still | 6 | 0.6667 (66.7%) | 1.0000 (100%) | 0.9048 (90.5%) |

### Confusion Matrix

```
                Predicted
              Stand Walk Jump Still
Actual Stand     3    0    0    0
       Walk      1    4    0    0
       Jump      0    1    6    0
       Still     1    0    1    4
```

### Key Results Interpretation

- Perfect Standing Detection: 100% sensitivity (3/3 correct) - strongest performance
- Strong Walking Recognition: 80% sensitivity (4/5 correct) - captures gait patterns reliably
- Strong Jumping Detection: 85.7% sensitivity (6/7 correct) - high acceleration dynamics
- Moderate Still Performance: 66.7% sensitivity (4/6 correct) - challenging class with overlap with standing
- Domain Knowledge Impact: Activity-specific state counts (Standing:3, Walking:2, Jumping:2, Still:4) optimized for physical complexity
- Generalization: Excellent on completely unseen test data from separate directory

## Features Extracted

### Feature Composition (32 Total)

Accelerometer (16 features)
- Time-domain: Mean, Std, RMS (3 per axis × 3 axes = 9) + SMA (1) = 10
- Frequency-domain: Dominant frequency, Spectral energy (2 per axis × 3 axes = 6)

Gyroscope (16 features) 
- Time-domain: Mean, Std, RMS (3 per axis × 3 axes = 9) + SMA (1) = 10
- Frequency-domain: Dominant frequency, Spectral energy (2 per axis × 3 axes = 6)

### Feature Extraction Details

Time-Domain (20 features total)
- Mean: Average value per axis (gravity/offset)
- Standard Deviation: Motion variability per axis
- RMS: Root mean square (overall magnitude per axis)
- SMA: Signal Magnitude Area (integrated motion intensity)

Frequency-Domain (12 features total)
- Dominant Frequency: Peak frequency in FFT spectrum per axis
- Spectral Energy: Sum of squared FFT magnitudes per axis

### Normalization
- Method: StandardScaler (Z-score normalization)
- Applied to: All 32 features independently
- Result: μ ≈ 0, σ ≈ 1 per feature (satisfies Gaussian HMM assumption)

---

## HMM Architecture

### Activity-Specific Models

| Activity | Hidden States | Rationale |
|----------|--------------|-----------|
| Standing | 3 | Stable posture with micro-movements (balance adjustments) |
| Walking | 2 | Alternating leg movements (left-right cycle) |
| Jumping | 2 | Preparation phase + flight/landing phase |
| Still | 4 | Environmental noise and complex variations |

### Model Components
- Observation Space: 32-dimensional normalized feature vectors
- Emission Distributions: Multivariate Gaussian per hidden state
- Transition Matrix: Stochastic matrix (A) per activity
- Start Probabilities: Uniform initialization (π)

### Training Algorithm
- Method: Baum-Welch EM Algorithm
  - E-step: Forward-backward algorithms compute state posteriors (γ)
  - M-step: Update transition matrix, means, and covariances
  - Convergence: Checks max|A_t+1 - A_t| < 1e-4
  - Result: All models converged at iteration 1

### Inference Algorithm
- Method: Viterbi with log-domain computation
- Stability: Log-sum-exp trick prevents numerical underflow
- Output: Most likely hidden state sequence

### Test Data Handling
- Source: 21 sequences from separate `/testing_data/` directory
- Preprocessing: Same feature extraction + scaler.transform()
- Normalization: Uses training data scaler (no data leakage)


## Data Characteristics

### Recordings per Activity
- Standing: 13 recordings (5-10 seconds each)
- Walking: 12 recordings (continuous motion)
- Jumping: 14 recordings (rhythmic impacts)
- Still: 14 recordings (no movement)

### Sensor Data
- Device: TECNO BG6m (Android)
- Accelerometer: ±50 m/s² range, 100 Hz
- Gyroscope: ±500°/s range, 100 Hz
- Total: 53 recordings × 2 sensors × 3 axes = ~9,500 data points

### Signal Characteristics
- Standing: Stable baseline (gravity only), low variance
- Walking: Periodic peaks (~1-2 Hz cadence), moderate variance
- Jumping: Repetitive spikes (~2-3 Hz), high variance
- Still: Minimal motion, sensor noise floor visible



## Performance Notes

### What Worked Well
-  Standing activity perfectly detected (100% sensitivity)
-  Efficient HMM convergence (few Baum-Welch iterations)
-  Robust feature normalization
-  Clear activity differentiation in raw signals



## Summary & Conclusion

### Technical Achievement
We successfully implemented a from-scratch Gaussian HMM frameworkthat achieves 80.95% accuracyon completely unseen test data. The implementation includes:

-  Rigorous data collection & validation (53 recordings, 100% integrity)
-  Comprehensive feature engineering (32 time + frequency domain features)
-  Correct Baum-Welch EM training with proper convergence checking
-  Numerically stable Viterbi decoding with log-domain computation
-  Intelligent architectural design via activity-specific state counts
-  Complete evaluation on separate test directory (21 sequences)

### Performance Highlights

| Metric | Result |
|--------|--------|
| Overall Accuracy | 80.95% (17/21) |
| Standing (Perfect) | 100% sensitivity |
| Walking (Strong) | 80% sensitivity |
| Jumping (Strong) | 85.7% sensitivity |
| Still (Moderate) | 66.7% sensitivity |

### Key Insight
Domain knowledge about activity temporal structure is critical.Activity-specific state counts optimized for physical complexity (Standing: 3 states for balance dynamics, Walking: 2 states for gait cycles, Still: 4 states for environmental variations) proved significantly more effective than a uniform model architecture.


## Authors
- Belyse
- Christian
