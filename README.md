# Multilayer Perceptron for Charge Collection Efficiency (CCE) Prediction

This repository contains the code and analysis for predicting Charge Collection Efficiency (CCE) in thimble ionisation chambers using a Multilayer Perceptron (MLP). This work was submitted as a final MSc project, achieving a module mark of 75%.

## Overview
The primary goal of this model is to fit a non-linear regression to predict CCE based on two inputs from experimental data: dose per pulse and a polarity factor. An MLP was selected because it excels with continuous, non-linear data that lacks spatial or time-stepped dependencies.

## Data Processing
The pipeline ingests experimental dosimetry data with the following preprocessing steps:
* **Extraction:** Data is read from a CSV file and loaded into Pandas DataFrames.
* **Scaling:** Dose, Polarity, and CCE values are centered around 0 using scikit-learn's `StandardScaler`.
* **Balancing:** Lower CCE values are undersampled to ensure a more even data distribution.
* **Splitting:** The dataset is divided into a 70-30 train-test split, preserving enough testing data to capture the full scope without sacrificing the training size.

## Model Architecture
The neural network employs a funnel-style, 3-layer MLP architecture to condense high-dimensional hidden spaces:
* **Input Layer:** 2 features (Dose and Polarity).
* **Hidden Layer 1:** Dense layer with 256 units and Swish activation.
* **Hidden Layer 2:** Dense layer with 64 units and Swish activation.
* **Hidden Layer 3:** Dense layer with 32 units and Swish activation.
* **Output Layer:** 1 unit predicting CCE.

> **Note:** The Swish activation function was chosen over LeakyReLU for its superior handling of negative values and gradient flow. Dropout was omitted as the architecture's depth did not lead to overfitting.

## Training Configuration
To optimize performance and prevent overfitting, the training loop utilizes specific Keras callbacks:
* **ReduceLROnPlateau:** Adjusts the learning rate when metrics stagnate.
* **EarlyStopping:** Halts training when the model stops improving.
* **Iteration:** The model is trained 50 separate times to reduce variance and bypass static seed initialization.

## Results & Discussion
* **Accuracy:** The model demonstrated high precision, achieving a mean absolute error (MAE) of **1.184% ± 1.346%** across the 50 training runs.
* **Tolerance:** The best performing seed resulted in an MAE of **0.189%**, with 100% of its predictions falling within a **± 2%** tolerance of the true CCE values.
* **Residual Analysis:** Plotting the residuals revealed a sinusoidal pattern, indicating an underlying periodic relationship in the physics that the current MLP architecture misses.
* **Future Improvements:** Future iterations could implement a Physics-Informed Neural Network (PINN) to embed this periodic behavior directly into the loss function for more accurate gradient descent.

## References
* Dataset: *Charge collection efficiency of thimble ionization chambers exposed to ultra-high dose per pulse* by Jose Paz-Martín (2025).
