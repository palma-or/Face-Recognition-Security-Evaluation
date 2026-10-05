# 🛡️ Face Recognition System Security Evaluation
This project focuses on **assessing the resilience of a deep learning-based face recognition system** against adversarial attacks, and investigating effective defensive strategies. By applying subtle, often imperceptible perturbations to input images, the project demonstrates how machine learning models can be deceived, explores the transferability of these attacks across different architectures, and implements a robust defense mechanism to mitigate these vulnerabilities.

---

## 📚 Project Overview & Methodology

The evaluation is structured into four primary phases, systematically analyzing the threat model and the corresponding countermeasures.

### 1. Preliminary Assessment & Dataset Construction
To ensure an unbiased evaluation, we constructed a representative test set and established a strong baseline.
* **Dataset:** We extracted a clean test set of 1,000 images, randomly selecting 10 samples for each of the 100 distinct identities chosen from the VGG-Face2 dataset.
* **Preprocessing:** Images were aligned and cropped using the MTCNN algorithm. We configured MTCNN with a `margin=32` and `selection_method="center_weighted_size"` to prioritize the largest and most central faces, resizing outputs to 160x160 pixels and normalizing values between -1 and 1.
* **Surrogate Model (NN1):** The primary target is an **Inception ResNetV1** (FaceNet) model. With the MTCNN preprocessing applied, this baseline model achieved an impressive initial accuracy of **95.80%** on our clean test set.

### 2. Adversarial Attack Generation (Grey-Box)
Using the **Adversarial Robustness Toolbox (ART)** via a Keras wrapper, we generated adversarial examples targeting NN1. We utilized `Categorical-Crossentropy` as a surrogate loss function since the original `Triplet Loss` is non-standard in ART. We evaluated both *Targeted* and *Untargeted* attacks, generally restricting maximum perturbation ($L_{\infty}$) to 5-10%:

* **FGSM (Fast Gradient Sign Method):** Evaluated with $\epsilon$ from 0 to 0.1. The untargeted variant aggressively dropped correct predictions from 958 to just 11. Targeted FGSM struggled to force the specific target class, achieving only 172 successful targeted misclassifications at maximum perturbation.
* **BIM (Basic Iterative Method):** Applied iteratively with step constraints. The targeted attack was highly successful here: at $\epsilon=0.05$, correct predictions plummeted to 0, and all 1,000 samples were successfully forced into the target class.
* **PGD (Projected Gradient Descent):** Projecting perturbations onto an $\epsilon$-ball, this attack proved exceptionally lethal. In the targeted scenario, it reached 1,000/1,000 target misclassifications with relatively low perturbation values, demonstrating precise output manipulation.
* **Carlini-Wagner (C&W):** We tested both aggressive (`max_iter=1`, `learning_rate=0.1`) and cautious (`max_iter=7`, `learning_rate=0.05`) approaches using $L_2$ and $L_{\infty}$ norms. The untargeted cautious approach successfully balanced minimal perturbation (0.0500) with severe accuracy degradation.
* **DeepFool:** This geometric approach calculates the optimal direction to cross decision boundaries. Testing with `nb_grads=12` yielded the best compromise, offering highly precise perturbations (average 0.15) while keeping the majority of images within the $L_{\infty}$ constraints.

### 3. Transferability Evaluation
To understand the real-world threat, we tested the transferability of the generated adversarial samples on a completely different secondary classifier (**NN2**).
* **Target Model (NN2):** A 50-layer **ResNet50** architecture from the Keras `vggface` library, pre-trained on VGGFace, boasting a clean baseline accuracy of 94.40%.
* **Preprocessing Adaptation:** Adversarial images were rescaled to [0, 255], resized to 224x224, and reordered via the `preprocess_input` function before feeding into NN2.
* **Findings:** The NN2 model showed high resilience to *Targeted* attacks transferred from NN1 (e.g., targeted BIM failed completely at 100 iterations). However, *Untargeted* attacks transferred exceptionally well. Untargeted BIM, for instance, dropped NN2's accuracy from 944 to just 76 correctly classified samples.

### 4. Defense Mechanisms: Adversarial Detector
To secure the system, we implemented a robust **Adversarial Sample Detector**.
* **Dataset & Architecture:** We trained a binary classifier using a ResNet50 backbone (initialized with ImageNet weights) on a perfectly balanced dataset of 2,000 images: 1,000 clean samples and 1,000 adversarial samples (mixed attacks, targeted and untargeted). Training ran for 30 epochs with a batch size of 32.
* **Performance Metrics:** The detector achieved an outstanding **F1-score of 0.93**. It successfully identified 92% of the adversarial samples (True Negatives) while maintaining a low False Positive rate, misclassifying only 6.5% of clean images as adversarial.
* **System Improvement:** Integrating this detector as a defensive filter dramatically restored the original classifier's reliability. Overall accuracy against adversarial datasets surged from a highly compromised 48.75% to a secure **87.44%**.

---

## 📁 Repository Structure

The codebase is organized to cleanly separate the core evaluation notebooks from the generated data, output visual analytics, and the trained defense models.

### 📓 Core Notebooks
* `nn1.ipynb`: Code for generating adversarial attacks (FGSM, BIM, PGD, C&W, DeepFool) via ART, calculating test set performance, and plotting security evaluation curves for the Inception ResNetV1 model.
* `nn2_resnet50.ipynb`: Code to load and evaluate the baseline performance of the secondary ResNet50 classifier on the clean test set.
* `transferability.ipynb`: Pipeline for passing the adversarial images generated in `nn1` through the `nn2` model to analyze attack transferability.
* `defense.ipynb`: Contains the dataset generation logic, architecture setup, and training loop for the ResNet50 binary adversarial detector.

### 📂 Directories
* **`attacks/`**: Stores all exported data and plots (.pkl, .png, .jpg), including Security Evaluation Curves (SEC) and Perturbation Curves (PER), systematically organized by attack type (BIM, CW, DeepFool, FGSM, PGD) and target model (`nn1` or `nn2`).
* **`datasets/`**: Contains the raw and processed data used throughout the project.
* **`detector/`**: Contains the artifacts of the trained ResNet50 adversarial sample detector, including the saved weights, loss/accuracy logs, and cross-validation indices.
* **`paper/`**: Contains the official project documentation (`Report.pdf`) and the assignment specifications.

---

**Authors (Group 9):** Giovanni Lamb, Palma Orlando, Fabrizio Saturnino, Egidio Zottarelli
**Institution:** University of Salerno, Department of Information Engineering, Electrical Engineering and Applied Mathematics
