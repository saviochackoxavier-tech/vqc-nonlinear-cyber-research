# Variational Quantum Classification on Nonlinear Manifolds: A Reproducible Proof-of-Concept for Cybersecurity-Inspired Threat Classification

**Author:** Savio Chacko Xavier  
**Affiliation:** Department of Computer Applications, Mahatma Gandhi University, Kottayam, Priyadarshini Hills, Athirampuzha, Kottayam, Kerala 686560, India  
**Programme:** Master of Computer Applications in Cybersecurity  
**ORCID:** 0009-0004-6363-2619  
**SSRN Author ID:** 13082928  
**Date:** September 2026

## Abstract

Cybersecurity analytics increasingly depends on classifying telemetry whose decision structure is nonlinear, noisy, and difficult to represent with low-dimensional models. Variational quantum classifiers (VQCs) offer a compact framework for studying this problem because a small parameterized quantum circuit can transform encoded features into expectation values that are subsequently optimized for classification. However, claims about quantum classification are meaningful only when the data geometry, probability readout, optimization objective, and evaluation protocol are specified precisely.

This paper presents a reproducible proof-of-concept VQC for three-class classification on a cybersecurity-inspired nonlinear manifold. The experiment deliberately uses a controlled synthetic benchmark rather than operational network data. It generates 240 balanced observations from three concentric radial bands with nonlinear angular warping and additional nuisance telemetry coordinates. The data are divided into stratified training, validation, and test partitions of 168, 36, and 36 observations. After training-only standardization, nonlinear bounded features are mapped to a three-qubit circuit. The circuit uses data re-uploading, rotation gates, nearest-neighbor entanglement, and a single trainable variational layer. Instead of incorrectly truncating an eight-state quantum distribution, the corrected model extracts three Pauli-Z expectation values and passes them to a trainable three-logit softmax readout. The model contains 18 trainable parameters and is optimized with L-BFGS-B using a deterministic numerical gradient and a bounded optimization budget.

The resulting VQC achieves 92.86% training accuracy, 88.89% validation accuracy, and 86.11% accuracy on the held-out test set. Under the same splits and engineered features, the classical baselines achieve 91.67% test accuracy for logistic regression, 91.67% for the radial-basis-function support-vector machine, 97.22% for random forest, and 94.44% for the multilayer perceptron. Thus, the classical models outperform the VQC on this benchmark, while the VQC remains well above the 33.33% balanced three-class chance level. These results indicate that the corrected VQC can learn a controlled nonlinear decision structure under statevector simulation, but they provide no evidence of quantum advantage. They also do not establish quantum hardware performance or operational cybersecurity effectiveness. The principal contribution is therefore methodological: a transparent and reproducible experimental design that exposes the importance of valid quantum probability readouts, leakage-resistant preprocessing, nonlinear benchmark construction, held-out evaluation, and fair classical comparison. The study provides a foundation for validation on public intrusion-detection datasets.

**Keywords:** variational quantum classifier; quantum machine learning; nonlinear manifolds; cybersecurity analytics; threat classification; statevector simulation; quantum feature maps; proof of concept

## 1. Introduction

Security operations increasingly rely on the interpretation of heterogeneous telemetry. Network flows, endpoint events, authentication records, encrypted-traffic metadata, and Internet-of-Things measurements may contain interactions that are nonlinear, class-dependent, and only partially observable. A classifier that performs well on a simple linear boundary may fail when the same classes occupy curved or folded regions of the feature space. This challenge motivates research into compact representation-learning and classification architectures that can be studied under controlled experimental conditions.

Quantum machine learning provides one possible research direction. In a variational quantum classifier, classical observations are encoded into a parameterized quantum circuit. Trainable circuit parameters are adjusted so that measurements of the resulting quantum state support a classification decision. The approach is compatible with near-term quantum-computing research because it combines a small quantum circuit with a classical optimization loop rather than requiring a fault-tolerant quantum computer.

The present study does not claim that a small simulated VQC is ready to replace established cybersecurity classifiers. Instead, it addresses a narrower and more defensible question: **Can a carefully specified three-qubit VQC learn a genuinely nonlinear, cybersecurity-inspired three-class manifold when its readout and optimization procedure are implemented correctly?** This question is appropriate for a proof-of-concept study because it separates algorithmic validity from claims about deployment, quantum advantage, or superiority over classical models.

The experiment revises an earlier prototype in four important ways. First, the original label rule was primarily a linear threshold based on the sum of two features. The revised generator creates concentric radial classes with angular warping, producing curved decision structure. Second, the original model truncated an eight-state quantum probability vector to its first three entries and then applied another softmax. That operation did not define a principled three-class quantum readout. Third, the revised pipeline fits preprocessing only on the training data and evaluates a held-out test set. Fourth, the optimization procedure separates the training objective from validation monitoring and uses a bounded, reproducible optimizer configuration.

The contribution of this paper is a complete, auditable experimental framework rather than a claim of quantum advantage. The framework includes a nonlinear benchmark generator, a three-qubit variational circuit, a valid expectation-value readout, deterministic optimization, and post-training diagnostics. Its measured results show that the corrected model reaches 86.11% test accuracy on the synthetic benchmark. The result should be interpreted as evidence that the implementation can represent and learn the selected controlled manifold, not as evidence that quantum models outperform classical alternatives in cybersecurity.

## 2. Research Questions and Contributions

The study is organized around three research questions.

**RQ1.** Can a balanced synthetic dataset with curved class geometry be generated so that the benchmark is meaningfully nonlinear while remaining reproducible and interpretable?

**RQ2.** Does a corrected three-qubit VQC with an expectation-value readout learn the resulting three-class structure under statevector simulation?

**RQ3.** What can be concluded from the resulting metrics, and what cannot be concluded without classical baselines, public cybersecurity data, repeated trials, and hardware experiments?

The paper makes four contributions. It defines a nonlinear manifold benchmark that is more appropriate for a proof-of-concept than the earlier linear threshold generator. It specifies a compact variational circuit and a valid three-class readout based on Pauli-Z expectations. It documents a leakage-resistant training and evaluation protocol with an independent test set. Finally, it reports the resulting performance while explicitly limiting the interpretation to synthetic proof-of-concept evidence.

## 3. Related Conceptual Background

### 3.1 Variational quantum classification

A VQC combines a feature-encoding circuit with trainable variational gates. For an input vector \(x\), a quantum circuit prepares a state \(|\psi(x,\theta)\rangle\), where \(\theta\) denotes trainable parameters. Measurements produce observables such as expectation values of Pauli operators. A classical readout can then transform those observables into class logits. Training minimizes a loss function, commonly cross-entropy for multiclass classification.

This hybrid structure is important for the present experiment. The quantum circuit is not treated as a complete classifier by itself. Instead, it acts as a compact nonlinear feature transformation. The final class probabilities are produced by a clearly defined softmax readout. This design avoids conflating basis-state amplitudes, measurement probabilities, and class probabilities.

### 3.2 Nonlinear manifolds in cybersecurity-inspired data

The phrase “high-dimensional cybersecurity” can easily become misleading when an experiment uses only a few synthetic variables. In this paper, the cybersecurity connection is intentionally conceptual. The four generated coordinates are treated as telemetry-inspired measurements, while the underlying radial and angular structure represents a controlled nonlinear pattern that could serve as a toy abstraction of interacting security signals. The benchmark is not a substitute for packet captures, flow records, endpoint logs, or labeled intrusion data.

A manifold benchmark is useful because its geometry can be inspected directly. The three classes are arranged in radial bands, and the angular warp prevents the experiment from reducing to a simple set of axis-aligned thresholds. Two additional coordinates act as nonlinear nuisance features. This creates a more informative test of feature encoding than a label generated directly from one linear combination of two independent variables.

## 4. Experimental Methodology

### 4.1 Dataset construction

The experiment generates 240 observations, with 80 observations in each of three classes. For every observation, an angle \(\phi\) is sampled uniformly from \([0,2\pi)\). A class-specific radial center is selected from approximately 0.55, 1.10, and 1.65, and small Gaussian variation is added. The angular coordinate is then warped according to

\[
\phi' = \phi + 0.22\sin(3\phi) + 0.10r^2.
\]

The first two coordinates are constructed as

\[
x_1 = r\cos(\phi') + \epsilon_1,
\qquad
x_2 = r\sin(\phi') + \epsilon_2,
\]

where the noise terms are small independent perturbations. Two additional coordinates are defined as nonlinear functions of the original angle and radius:

\[
x_3 = \sin(2\phi)(0.35+0.12r)+\epsilon_3,
\]

and

\[
x_4 = \frac{r^2\cos(\phi)}{2.2}+\epsilon_4.
\]

The resulting classes are balanced by construction. The dataset is split using stratified sampling into 70% training, 15% validation, and 15% test partitions. The final split contains 168 training observations, 36 validation observations, and 36 test observations. Standardization is fitted exclusively on the training partition and then applied to the validation and test partitions.

### 4.2 Nonlinear feature engineering and angle encoding

The standardized features are transformed into three bounded angles. The first angle is based on the radial magnitude of the first two standardized coordinates. The second combines the third coordinate with an interaction term involving the first two coordinates. The third combines the fourth coordinate with a quadratic contrast between the first two coordinates. Each result is passed through a hyperbolic tangent and scaled by \(\pi/2\). This produces three bounded inputs suitable for rotation gates while retaining nonlinear information relevant to the radial class structure.

This step is not described as a quantum advantage. It is a classical preprocessing choice that makes the experiment fit the three-qubit circuit and reduces the risk that the circuit is asked to learn a representation that the benchmark encoding has discarded.

### 4.3 Quantum circuit architecture

The circuit uses three qubits and one trainable variational layer. For each input, the circuit performs data re-uploading across two encoding stages. Each stage applies \(R_y\) and \(R_z\) rotations determined by the encoded feature angles. Neighboring qubits are connected with a linear chain of controlled-NOT gates. Between encoding stages, trainable \(R_y\) and \(R_z\) rotations are applied to each qubit.

The circuit is evaluated with an exact statevector simulator. For each input, three single-qubit Pauli-Z expectation values are measured:

\[
z_q(x,\theta)=\langle \psi(x,\theta)|Z_q|\psi(x,\theta)\rangle,
\qquad q\in\{0,1,2\}.
\]

The resulting three-dimensional vector is not itself treated as a probability vector. Instead, a trainable affine readout creates three class logits:

\[
\ell_k = w_k^Tz+b_k,
\qquad k\in\{0,1,2\}.
\]

The probabilities are then defined by a single softmax operation:

\[
p_k = \frac{e^{\ell_k}}{\sum_{j=0}^{2}e^{\ell_j}}.
\]

The circuit contains six variational rotation parameters, and the readout contains twelve parameters. The complete model therefore has 18 trainable parameters.

### 4.4 Loss and optimization

Training uses multiclass cross-entropy with a small L2 penalty:

\[
\mathcal{L}(\theta,W,b)=
-\frac{1}{n}\sum_{i=1}^{n}\log p_{y_i}(x_i)
+\lambda\|\theta,W,b\|_2^2.
\]

The optimizer is L-BFGS-B with a maximum of 20 iterations, a fixed random initialization, and a deterministic central finite-difference gradient. Because exact statevector simulation is computationally expensive when the gradient is evaluated repeatedly, a deterministic subset of up to 120 training observations is used for optimization. The final train, validation, and test scores are computed separately after optimization. This computational compromise is reported explicitly so that it is not mistaken for full-batch training.

Validation loss and accuracy are monitored during optimization, but validation data are not used as the optimization objective. A held-out test partition is evaluated only after the final parameters have been selected.

### 4.5 Evaluation measures

The study reports accuracy, balanced accuracy, precision, recall, F1 score, and a confusion matrix. Because the final partitions are class-balanced, accuracy and balanced accuracy coincide in this experiment. The analysis also records the training and validation loss curves and the distribution of maximum predicted probability on the test set.

## 5. Results

### 5.1 Optimization behavior

The initial validation accuracy was 33.33%, which is the expected chance-level baseline for three balanced classes. By iteration 10, validation accuracy had reached 72.22%. At iteration 20, it reached 86.11%. After the bounded optimization terminated, the final validation accuracy was 88.89%. The optimizer reported that the maximum iteration limit had been reached rather than reporting convergence to a stationary optimum. This detail matters: the results demonstrate successful learning within the prescribed budget, but they do not establish that the global or even local optimum was found.

The final training loss was approximately 0.258. The training accuracy was 92.86%, while validation accuracy was 88.89%. The moderate difference between training and validation performance is consistent with some generalization gap but does not, by itself, establish severe overfitting.

### 5.2 Validation performance

The validation set contained 12 observations per class. Overall accuracy and balanced accuracy were both 88.89%. Class 0 achieved precision and recall of 1.000 and 1.000. Class 1 achieved precision of 0.900 and recall of 0.750. Class 2 achieved precision of 0.786 and recall of 0.917. The macro-averaged F1 score was 0.888.

The class-specific values suggest that the principal remaining confusion is between the two outer or adjacent radial regions rather than a complete failure to identify any one class. However, because the validation set is small, a single observation changes the class recall by approximately 8.33 percentage points. The reported values should therefore be interpreted with appropriate uncertainty.

### 5.3 Held-out test performance

On the 36-observation held-out test set, the model achieved 86.11% accuracy and 86.11% balanced accuracy. The macro-averaged F1 score was 0.862. Class 0 achieved precision of 1.000 and recall of 0.917. Class 1 achieved precision of 0.818 and recall of 0.750. Class 2 achieved precision of 0.786 and recall of 0.917.

The test result is lower than the training and validation results, as expected for a small held-out sample, but it remains substantially above the 33.33% chance-level accuracy of a balanced three-class task. The result supports the narrow conclusion that the corrected VQC learned useful structure from the constructed nonlinear benchmark.

| Split | Accuracy | Balanced accuracy |
|---|---:|---:|
| Training | 92.86% | 92.86% |
| Validation | 88.89% | 88.89% |
| Test | 86.11% | 86.11% |

### 5.4 Classical baseline comparison

To determine whether the VQC result reflected a meaningful advantage over standard methods, four classical baselines were trained using exactly the same train, validation, and test partitions and the same three engineered features. The comparison included logistic regression, an RBF-kernel support-vector machine, a random forest with 300 trees, and a multilayer perceptron with two hidden layers. Model fitting used the training partition only, and the test partition was reserved for final evaluation.

| Model | Training accuracy | Validation accuracy | Test accuracy | Test macro F1 |
|---|---:|---:|---:|---:|
| VQC | 92.86% | 88.89% | 86.11% | 0.862 |
| Logistic Regression | 89.88% | 88.89% | 91.67% | 0.915 |
| SVM-RBF | 92.26% | 91.67% | 91.67% | 0.918 |
| Random Forest | 100.00% | 100.00% | 97.22% | 0.972 |
| MLP | 100.00% | 97.22% | 94.44% | 0.944 |

The random forest achieved the highest held-out test accuracy at 97.22%, followed by the MLP at 94.44%, the SVM-RBF and logistic regression at 91.67%, and the VQC at 86.11%. The classical results are scientifically important because the radial-manifold benchmark is readily learnable by conventional nonlinear models. The VQC therefore should not be presented as superior on this task. Its contribution is the demonstration of a valid and reproducible hybrid quantum-classical pipeline, not an empirical performance win.

The comparison also illustrates why a baseline is necessary in quantum machine-learning research. A result above chance can demonstrate that a circuit has learned a pattern, but it cannot establish that the circuit is useful unless the result is compared with strong classical alternatives under the same information and evaluation constraints. Because the present benchmark is small and synthetic, the comparison should be confirmed with repeated seeds, confidence intervals, and public cybersecurity data before making broader claims.

### 5.4 Interpretation of the improvement

The earlier prototype produced validation accuracy in the approximate 30–40% range. The revised experiment improves substantially, but the cause should not be attributed to quantum computation alone. Several changes occurred simultaneously: the labels were redesigned, preprocessing was changed, the feature map was changed, the quantum readout was corrected, the optimizer was changed, and a test partition was added. The result therefore demonstrates the effectiveness of the revised pipeline as a whole. It does not isolate the contribution of any individual architectural component.

The corrected readout is particularly important. A statevector for three qubits contains eight basis-state probabilities. Selecting the first three probabilities does not generally create a meaningful three-class measurement because the selected entries need not correspond to mutually exhaustive class events. Applying an additional softmax to those values further changes their interpretation. The expectation-value readout avoids this ambiguity by defining a three-dimensional quantum feature vector and a separate, explicit classical class-probability layer.

## 6. Discussion

### 6.1 What the experiment demonstrates

The experiment demonstrates that a small VQC can be configured as a reproducible nonlinear classifier under exact statevector simulation. It also demonstrates that careful experimental specification can materially change the apparent performance of a quantum classifier. A valid readout, training-only preprocessing, stratified data handling, and a held-out test partition are not cosmetic details. They determine whether the reported metrics have a defensible interpretation.

The model is compact. It uses three qubits, one trainable variational layer, 18 parameters, and a low-dimensional classical readout. Such compactness may be relevant to research on constrained hybrid models, but compactness should not be confused with computational superiority. On this benchmark, conventional models perform better, so the VQC should be described as an interpretable hybrid research prototype rather than a superior classifier. The experiment does not measure runtime, energy use, noise resilience, hardware execution cost, or quantum-resource-normalized performance.

### 6.2 Cybersecurity relevance

The cybersecurity relevance is methodological rather than operational. Real security data often contain nonlinear interactions among measurements, and a controlled curved-manifold benchmark is a useful first test of whether a model can represent non-axis-aligned class structure. The benchmark could be viewed as a toy abstraction of telemetry classes such as benign activity, suspicious behavior, and a distinct threat family.

However, the current experiment does not use packet captures, enterprise logs, malware traces, or public intrusion records. It does not model concept drift, adversarial manipulation, missing values, label noise, class imbalance, or temporal dependence. It also does not establish that encrypted-traffic classification, advanced-persistent-threat detection, or Internet-of-Things monitoring would benefit from a VQC. Those applications require independent experiments with domain-relevant datasets and evaluation protocols.

### 6.3 Comparison with classical baselines

The completed comparison shows that classical models are preferable on this controlled benchmark in predictive accuracy. Random forest, MLP, SVM-RBF, and logistic regression all exceed the VQC's held-out test score. This outcome is not a failure of the proof of concept; it is the correct empirical conclusion for the current data and architecture. The benchmark confirms that the VQC can learn the task, while the baselines establish that it does not yet outperform conventional approaches. Hyperparameter selection was kept separate from the test set, but future work should add repeated trials and confidence intervals to determine whether the observed ranking is stable.

### 6.4 Reproducibility and statistical confidence

The experiment uses fixed seeds and reports a complete split size, but one train/validation/test split is not sufficient to characterize uncertainty. Future work should repeat the experiment over multiple seeds or use repeated stratified cross-validation for model selection, followed by a final independent test set. Mean performance, standard deviation, confidence intervals, and paired comparisons should be reported.

The current test set contains only 36 observations. Its accuracy is therefore sensitive to a small number of predictions. Larger synthetic benchmarks can reduce measurement noise, but the more important next step is evaluation on public, labeled cybersecurity datasets with clearly documented provenance.

## 7. Limitations

This study has six principal limitations. First, the data are synthetic and intentionally controlled. They do not establish operational validity. Second, the feature dimension is four, so the phrase “high-dimensional” should not be used to describe the current experiment without qualification. The work studies a nonlinear manifold under a small-qubit constraint rather than a genuinely high-dimensional production dataset. Third, exact statevector simulation does not reproduce the noise, connectivity, calibration drift, queueing, or measurement error of a quantum processor. Fourth, the optimizer uses a bounded deterministic subset for computational tractability, so the optimization path is not equivalent to full-batch training. Fifth, the classical comparison uses fixed illustrative hyperparameters and does not constitute an exhaustive tuning study. Sixth, the experiment lacks an ablation study and repeated-seed uncertainty analysis. Seventh, the final partitions are small, which limits statistical precision.

These limitations do not invalidate the proof of concept. They define the scope of the evidence. The appropriate conclusion is that the revised VQC is a reproducible research prototype that learns the selected synthetic nonlinear structure. Stronger cybersecurity or quantum-advantage claims require additional evidence.

## 8. Future Work

The next experimental phase should replace the synthetic benchmark with a public intrusion-detection dataset such as UNSW-NB15 or CIC-IDS2017. The study should document the data source, licensing, label construction, feature cleaning, missing-value policy, and duplicate-removal procedure. It should also use a time-aware split when timestamps are available, because random splitting can overestimate performance when neighboring records are highly correlated.

The VQC should then be compared with classical baselines under identical preprocessing and evaluation conditions. An ablation study should separately remove nonlinear feature engineering, data re-uploading, entanglement, and the classical readout. Multiple random seeds should quantify variability. If the circuit is executed on quantum hardware, the study should report circuit depth, transpilation effects, measurement shots, device identity, calibration date, and noise-mitigation procedure.

A further research direction is the study of resource-performance trade-offs. The relevant questions include how accuracy changes with qubit count, variational depth, circuit connectivity, shot budget, and optimizer choice. These measurements would make the work more informative than a single accuracy number and would clarify whether the VQC offers a practical advantage under a defined resource constraint.

## 9. Conclusion

This paper presented a corrected, reproducible VQC proof of concept for three-class classification on a cybersecurity-inspired nonlinear manifold. The revised benchmark uses concentric radial classes with angular warping and nonlinear nuisance coordinates. The revised model uses a three-qubit data-reuploading circuit, Pauli-Z expectation values, a trainable three-logit softmax readout, training-only standardization, deterministic optimization, and held-out evaluation.

The model achieved 88.89% validation accuracy and 86.11% test accuracy, compared with the original prototype’s approximate chance-level validation behavior. The improvement supports a specific methodological conclusion: VQC experiments are highly sensitive to the definition of the readout, the geometry of the benchmark, the optimization objective, and the evaluation protocol. Correcting those elements can convert an apparently unsuccessful classifier into a measurable proof of concept.

The findings do not demonstrate quantum advantage or real-world cybersecurity effectiveness. The baseline comparison additionally shows that conventional models outperform the present VQC on the synthetic task. This is a more defensible and informative conclusion than a claim of superiority: the study establishes a transparent research prototype for examining hybrid quantum classification under a controlled nonlinear decision structure. A stronger follow-up should add repeated trials, uncertainty estimates, systematic hyperparameter tuning, ablations, and experiments on public intrusion-detection data.

## Data and Code Availability

The executed notebook accompanying this paper contains the synthetic-data generator, preprocessing pipeline, VQC definition, optimizer configuration, classical baseline implementations, comparative evaluation code, classification reports, and diagnostic plots. The baseline comparison table is also exported as a CSV artifact. Because the benchmark is generated procedurally with a fixed seed, it can be recreated without downloading a proprietary dataset. The synthetic nature of the data should be disclosed prominently in any public posting or SSRN submission.

## References

[1]: https://qiskit-community.github.io/qiskit-machine-learning/ "Qiskit Machine Learning Documentation"

[2]: https://arxiv.org/abs/2012.09265 "Variational quantum algorithms"

[3]: https://arxiv.org/abs/1909.05820 "Quantum machine learning in feature Hilbert spaces"

[4]: https://arxiv.org/abs/2101.11020 "Challenges and opportunities in quantum machine learning"

[5]: https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html "Scikit-learn StandardScaler Documentation"

[6]: https://scikit-learn.org/stable/modules/generated/sklearn.metrics.classification_report.html "Scikit-learn Classification Report Documentation"

[7]: https://www.unsw.adfa.edu.au/unsw-canberra-cyber/cybersecurity/ADFA-NB15-Datasets/ "UNSW-NB15 Dataset Information"

[8]: https://www.unb.ca/cic/datasets/ids-2017.html "CIC-IDS2017 Dataset Information"

[9]: https://www.nist.gov/cyberframework "NIST Cybersecurity Framework"

[10]: https://docs.scipy.org/doc/scipy/reference/optimize.minimize-lbfgsb.html "SciPy L-BFGS-B Optimization Documentation"
