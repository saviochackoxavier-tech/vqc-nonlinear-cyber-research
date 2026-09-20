# Improved VQC Results Summary

## Scope

The revised notebook improves the original variational quantum classifier on a controlled synthetic benchmark. The benchmark contains 240 samples, four telemetry-inspired features, and three balanced classes generated from concentric radial bands with angular warping and nonlinear nuisance coordinates. The first two features therefore form curved, nonlinearly separable manifolds rather than the original linear threshold.

## Technical corrections

The preprocessing scaler is fitted on the training split only, and the train, validation, and test partitions are stratified. Three bounded nonlinear feature angles are engineered from the standardized features and encoded into a three-qubit circuit.

The original probability calculation was corrected. It previously truncated an eight-state quantum distribution to its first three amplitudes and then applied a second softmax. The revised model obtains three Pauli-Z expectation values from the quantum state and passes them through a trainable affine three-logit readout followed by one valid softmax operation.

The objective now optimizes training loss while validation metrics are monitored independently. The optimizer uses a deterministic numerical gradient, L-BFGS-B, L2 regularization, a fixed seed, and a bounded iteration budget. A deterministic optimization subset keeps statevector finite-difference training tractable; final train, validation, and test metrics are still reported separately.

## Measured results

| Split | Accuracy | Balanced accuracy |
|---|---:|---:|
| Train | 92.86% | 92.86% |
| Validation | 88.89% | 88.89% |
| Test | 86.11% | 86.11% |

The validation report has macro F1 of 0.888. The held-out test report has macro F1 of 0.862. Test-class precision/recall are: class 0 = 1.000/0.917, class 1 = 0.818/0.750, and class 2 = 0.786/0.917.

## Interpretation and limitation

The improvement demonstrates that the low original accuracy was primarily caused by a flawed readout and optimization/data design rather than an unavoidable limitation of the VQC. These values are proof-of-concept results on a reproducible synthetic manifold and must not be interpreted as cybersecurity detection performance. A separate real-data experiment should use a labeled intrusion-detection benchmark such as UNSW-NB15 or CIC-IDS2017, with feature provenance, leakage checks, class-imbalance handling, and a time-aware evaluation protocol.

## Artifact

The executed notebook contains the generator, corrected VQC, retraining trace, classification reports, confusion matrix, loss/accuracy curves, and confidence distribution.
