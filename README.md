# EEG-SSVEP Signal Classification with MLP Neural Networks

## Project Overview

This project demonstrates the classification of Steady-State Visual Evoked Potentials (SSVEP) from Electroencephalography (EEG) data. The objective is to identify which of two visual stimulus frequencies (8 Hz or 10 Hz) an individual is focusing on, using a Multi-Layer Perceptron (MLP) neural network.

The main application of this technology is in Brain-Computer Interfaces (BCI), allowing a user to control a system (such as a virtual keyboard or a drone) with their gaze alone.

## Dataset

https://bci.med.tsinghua.edu.cn/

## Key Project Steps

* **EEG Signal Preprocessing:** Cleaning and filtering raw data to remove noise.
* **Feature Extraction:** Using the Fast Fourier Transform (FFT) to extract relevant features in the frequency domain.
* **Channel and Frequency Selection:** Focusing on channels in the occipital region and on the stimulus frequencies and their harmonics.
* **MLP Implementation:** Building and training a neural network for the classification task.
* **Performance Analysis:** Evaluating the model across three distinct scenarios to test its robustness and generalization capability.

## Methodology

The processing and classification pipeline followed these steps:

1.  **Data Loading:** Data from 35 subjects was loaded from `.mat` files. Each file contains 5-second EEG recordings for 40 different stimulus frequencies, with 6 repetitions each.

2.  **Preprocessing:**
    * **Temporal Trimming:** 500ms from the beginning and end of each signal was removed to eliminate transition artifacts.
    * **Common Average Reference (CAR) Filter:** A spatial filter was applied to reduce noise common to all channels by subtracting the average of the signals from all electrodes from each individual signal.

3.  **Windowing:** Each 5-second signal was divided into 5 windows of 1 second (250 samples). This increased the number of training samples by a factor of 5, serving as a form of data augmentation.

4.  **Feature Extraction with FFT:**
    * The Fast Fourier Transform (FFT) was applied to each 1-second window to convert the signal from the time domain to the frequency domain.
    * The main feature extracted was the **signal power** at the stimulus frequencies (8 Hz and 10 Hz) and their harmonics (16 Hz, 20 Hz, 24 Hz, etc.), where the SSVEP response manifests with greater intensity.

5.  **Channel and Frequency Selection:**
    * 9 channels from the brain's occipital region were selected (`Pz, PO5, PO3, POz, PO4, PO6, O1, Oz, O2`), as they are the most relevant for processing visual stimuli.
    * For classification, the focus was on distinguishing between the **8 Hz** and **10 Hz** stimuli.

6.  **Normalization:** Features were normalized for each subject individually, ensuring that the data scale did not negatively influence model training.

## Evaluation Scenarios and Results

Three scenarios were implemented to evaluate the performance of the MLP classifier:

### Scenario 1: Subject-Dependent Model

An MLP model was trained and tested for each of the 35 subjects, using only their own data. This scenario simulates an application calibrated for a specific user.

* **Average Accuracy:** `86.19% ± 14.22%`

### Scenario 2: Generalized Model (Leave-One-Subject-Out - LOSO)

A single model was trained on data from 34 subjects and tested on the one left out. The process was repeated 35 times, with each subject serving as the test subject once. This scenario evaluates the model's ability to generalize to new users without calibration.

* **Average Accuracy:** `88.81% ± 7.91%`

### Scenario 3: Generalized Model with "Outlier" Exclusion

In this scenario, the 6 subjects with the worst results from Scenario 1 were excluded from the training set. A new generalized model was trained on the remaining 29 subjects and tested on those same 29 subjects (using the LOSO approach).

* **Average Accuracy (on the remaining 29 subjects):** `92.30% ± 6.01%`

This last scenario demonstrates that it is possible to create an even more robust generalized model by removing data from users who are "outliers" (possibly due to low signal quality or low SSVEP response).

## How to Run the Project

1.  **Prerequisites:** Ensure you have Python 3 and the following libraries installed:
    ```bash
    tensorflow
    scikit-learn
    numpy
    matplotlib
    seaborn
    scipy
    ```
    You can install them with the command:
    `pip install tensorflow scikit-learn numpy matplotlib seaborn scipy`

2.  **Data Structure:**
    * Create a folder to store the data (e.g., `SSVEP_data`).
    * Download the `.mat` files from the SSVEP dataset and place them inside this folder.
    * In the notebook, update the `FOLDER_PATH` variable in the first code cell to your data folder's path.

3.  **Execution:**
    * Open the notebook `Projeto_final_v3.ipynb` in an environment like Jupyter Notebook or JupyterLab.
    * Execute the cells in order to reproduce the entire analysis pipeline and the results.

## Technologies Used

* **Language:** Python 3
* **Environment:** Jupyter Notebook
* **Main Libraries:**
    * TensorFlow (with Keras) for building the MLP neural network.
    * Scikit-learn for preprocessing, evaluation metrics, and normalization.
    * NumPy for multi-dimensional array manipulation.
    * SciPy for FFT calculations and `.mat` file reading.
    * Matplotlib and Seaborn for visualizing the results.
