
# nnU-Net Placenta Segmentation Project Documentation

---

## Table of Contents
- [Environment Setup](#environment-setup)
- [Original File Structure](#original-file-structure)
- [Processing Files into Standard MRI Dataset](#processing-files-into-standard-mri-dataset)
- [nnU-Net Folder Structure](#nnu-net-folder-structure)
- [Dataset Preparation](#dataset-preparation)
- [Running nnU-Net](#running-nnu-net)
  - [Data Conversion and Preprocessing](#1-data-conversion-and-preprocessing)
  - [Model Training](#2-model-training)
  - [Model Inference](#3-model-inference)
  - [Model Evaluation](#4-model-evaluation)
  - [Resuming Training](#5-resuming-training)
- [Notes](#notes)
- [References](#references)

---

## Environment Setup
1. Install **Pytorch**.
2. Install **Anaconda**.
3. Install other tools (e.g., **PyCharm** or **VSCode**).

---

## Original File Structure
Assume the segmentation folder is named **ForSeg**, where each subfolder (e.g., **ForSegSub**) follows the format:
**“organ_type_patientID_week”**, e.g., `PII_01_WK20`.

Each subfolder contains:
1. **Original MRI File**: `b0_img.nii`
2. **Segmentation Label File**: `seg.nii`

Example paths:
- Original MRI: `data\Raw\ForSeg\PII_01_WK20\b0_img.nii`
- Label: `data\Raw\ForSeg\PII_01_WK20\seg.nii`

---

## Processing Files into Standard MRI Dataset
Run the scripts in `dataSet\Processed_Code` to:
1. Convert all `nii` files to compressed `nii.gz` format (e.g., `Raw/ForSeg` → `Processed/PII_GZ`).
2. Extract compressed MRI and segmentation files into `Processed/PII_Full/images` and `Processed/PII_Full/labels`, with conversion logs recorded in `PII_Full`.
3. Split data into training (80%) and testing (20%) sets as `imagesTr`, `imagesTs`, `labelsTr`, and `labelsTs`.
4. Update the dataset descriptor file `dataset.json` in `PII_Splite`.

---

## nnU-Net Folder Structure
Create the following structure:
```
nnUNetFrame
│-- dataset
│   │-- nnUNet_processed
│   │-- nnUNet_raw
│   │   │-- nnUNet_cropped_data
│   │   │-- nnUNet_raw_data
│   │-- nnUNet_trained_models
│-- nnUnet
```

Download nnU-Net into the `dataset` folder:
The final path should be `nnUNetFrame/nnUnet`.

---

## Dataset Preparation
1. Rename the target dataset folder as **TaskXX_NN**, where **XX** is the ID and **NN** is the name (e.g., `Task01_Placenta`).
2. Copy the following files into `TaskXX_NN`:
   - `imagesTr`, `imagesTs`, `labelsTr`, `labelsTs`, `dataset.json`.
3. Place `TaskXX_NN` into `nnUNet_raw/nnUNet_raw_data`.

---

## Running nnU-Net
Run the following scripts in `nnUNetFrame/nnUNet/nnunetv2`:

### 1. Data Conversion and Preprocessing
- Edit parameters in `dataset_conversion`:
  ```
  python convert_MSD_dataset.py
  ```
- Run the following script in `experiment_planning` (`00x` is the dataset ID):
  ```
  python plan_and_preprocess_entrypoints.py -d 00x --verify_dataset_integrity
  ```

### 2. Model Training
- Run in the `run` folder:
  ```
  python run_training.py 00x 3d_fullres foldNum
  ```

### 3. Model Inference
- Run in the `inference` folder, specifying the output directory:
  ```
  python predict_from_raw_data.py
  ```

### 4. Model Evaluation
- Run in the `evaluation` folder:
  ```
  python evaluate.py
  ```

### 5. Resuming Training
- Resume training using pre-trained weights:
  ```
  python run_training.py Dataset008_20300019 3d_fullres 5 -pretrained_weights C:/path_to_checkpoint/checkpoint_latest.pth
  ```

---

## Notes
1. Run each script in its respective folder and ensure parameters are correctly configured.
2. Follow the sequence of data conversion, training, inference, and evaluation.
3. Adjust hyperparameters and strategies based on project needs.

---

## References
1. Isensee, F., Schell, M., Pflueger, I., Brugnara, G., Bonekamp, D., Neuberger, U., ... & Maier-Hein, K. H. (2021). nnU-Net: a self-configuring method for deep learning-based biomedical image segmentation. *Nature Methods*, 18(2), 203-211.
2. Official nnU-Net GitHub Repository: [https://github.com/MIC-DKFZ/nnUNet](https://github.com/MIC-DKFZ/nnUNet)
