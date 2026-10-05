# **Face Recognition System Security Evaluation**

This project focuses on **assessing the resilience of a face recognition-based access control system** against adversarial attacks and investigating effective defensive strategies. The project involves several key stages, starting with the selection of **100 identities from the VGG-Face2 dataset’s training set** and the creation of a test set comprising at least **1000 images** (with a minimum of 10 images per identity). 

## **Notebooks**
Below are detailed explanations of the various Python modules included in the folder:

- **`nn1.ipynb`**: Provides the code for producing the security evaluation curves for the NN1 network.
- **`nn2_resnet50.ipynb`**: Provides the code used to evaluate the performance of the nn2 classifier on a clean test set.
- **`transferability.ipynb`**: Provides the code for analyzing the transferability of attacks from the NN1 network to the NN2 network.
- **`defense.ipynb`**: Provides the code for constructing the adversarial samples dataset and training adversarial sample detectors.

Each notebook is furthermore divided in specific sections, so that it could be helpful for the user who wants to run all the code within.

## **Folders**
- **`attacks`**: This folder contains all the plots of the adversarial attacks performed on both the nn1 and nn2 networks, each organized in their respective subdirectories for clarity and comparison.

-**`datasets`**: This directory includes all the datasets used for generating adversarial attacks as well as for evaluating the performance of the models and detectors. 

### **Initial Phase: Accuracy Evaluation**

The initial phase evaluates the **accuracy of a face recognition network** (referred to as `NN1`) on this constructed test set. This phase is crucial for establishing a baseline performance measure against which the impact of adversarial attacks will be assessed.


### **Adversarial Attacks**

Subsequently, **adversarial examples** will be generated using the `ART` library, targeting `NN1`. The impact of these adversarial examples will be assessed using **Security Evaluation Curves**. These curves will provide a visual representation of the system's vulnerability to adversarial attacks.


### **Transferability Evaluation**

An additional classifier, trained on the VGG-Face2 dataset, will be chosen and evaluated on the "clean" test set to study the **transferability of adversarial examples** to this second classifier (`NN2`). This step is essential for understanding how adversarial examples affect different models and to what extent adversarial vulnerabilities are shared across classifiers.


### **Defense Mechanisms**

Finally, the project includes the **implementation and evaluation of at least one defense mechanism**. The effectiveness of these defense strategies will be measured by their ability to mitigate the impact of adversarial attacks and maintain the system's accuracy.

## **Group 9 - Members**
- Lamb Giovanni
- Orlando Palma 
- Saturnino Fabrizio 
- Zottarelli Egidio