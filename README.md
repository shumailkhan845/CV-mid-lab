# HOG vs SIFT on Blood-Cell Images

## Computer Vision Lab Midterm Project

This project compares two classical computer vision feature extraction techniques, **Histogram of Oriented Gradients (HOG)** and **Scale-Invariant Feature Transform (SIFT)**, for classifying blood-cell images from the BloodMNIST dataset.

A linear Support Vector Machine (SVM) is used as the classifier. The experiment also evaluates the robustness of both methods under image rotation and Gaussian noise.

## Objectives

- Load and preprocess the BloodMNIST dataset.
- Extract image features using HOG and SIFT.
- Train and evaluate a linear SVM classifier.
- Select the SVM regularization parameter using validation data.
- Compare classification performance using accuracy, macro-F1, and confusion matrices.
- Evaluate robustness on clean, rotated, and noisy images.
- Document the experiment and AI assistance used.

## Dataset

The dataset was loaded using the `medmnist` Python package.

Original dataset sizes:

| Split | Images |
|---|---:|
| Training | 11,959 |
| Validation | 1,712 |
| Testing | 3,421 |

Four blood-cell classes were selected:

| Class | Label | Training | Validation | Testing |
|---|---:|---:|---:|---:|
| Eosinophil | 1 | 100 | 30 | 30 |
| Monocyte | 5 | 100 | 30 | 30 |
| Neutrophil | 6 | 100 | 30 | 30 |
| Platelet | 7 | 100 | 30 | 30 |
| **Total** | — | **400** | **120** | **120** |

A random seed of `50` was used for reproducible sampling.

## Preprocessing

The images were processed using the following steps:

1. Convert RGB images to grayscale.
2. Resize images to `128 × 128` pixels.
3. Convert pixel values to floating-point numbers.
4. Normalize pixel intensities to the range `[0, 1]`.

## Feature Extraction

### 1. Histogram of Oriented Gradients (HOG)

HOG represents image structure by calculating gradient orientations within local cells and normalizing groups of neighboring cells.

| Parameter | Value |
|---|---|
| Image size | 128 × 128 |
| Cell size | 8 × 8 pixels |
| Orientation bins | 9 |
| Block size | 2 × 2 cells |
| Feature length | 8,100 |

Feature length calculation:

- Cells per dimension: `128 / 8 = 16`
- Blocks per dimension: `16 - 2 + 1 = 15`
- Features per block: `2 × 2 × 9 = 36`
- Total features: `15 × 15 × 36 = 8,100`

### 2. Scale-Invariant Feature Transform (SIFT)

SIFT extracts local image descriptors around keypoints. Natural keypoint detection was examined, and fixed-grid keypoints were used to produce consistent-length feature vectors.

Natural keypoint statistics:

| Statistic | Result |
|---|---:|
| Average keypoints per image | 72.365 |
| Minimum keypoints | 7 |
| Maximum keypoints | 227 |
| Images with zero keypoints | 0 |

Fixed-grid configuration:

| Parameter | Value |
|---|---|
| Grid size | 4 × 4 |
| Number of keypoints | 16 |
| Descriptor length | 128 |
| Feature length | 2,048 |

Feature length calculation:

`16 keypoints × 128 values = 2,048 features`

## Classification

A linear Support Vector Machine (SVM) was used to classify the extracted features.

The regularization parameter `C` was selected using validation data from the candidates:

`C = 0.1, 1, 10`

### HOG validation results

| C | Accuracy | Macro-F1 |
|---:|---:|---:|
| 0.1 | 66.67% | 0.6663 |
| 1 | 67.50% | 0.6741 |
| 10 | 67.50% | 0.6741 |

Selected parameter: **C = 1**

The values `C = 1` and `C = 10` tied on the reported validation metrics. The implementation selected `C = 1` because it was evaluated first.

### SIFT validation results

| C | Accuracy | Macro-F1 |
|---:|---:|---:|
| 0.1 | 80.83% | 0.8065 |
| 1 | 80.83% | 0.8065 |
| 10 | 80.83% | 0.8065 |

Selected parameter: **C = 0.1**

All three candidates produced identical reported validation metrics, so the implementation selected the first candidate.

## Robustness Evaluation

The selected models were evaluated on three test conditions:

- **Clean:** Original preprocessed test images.
- **Rotated:** Images rotated by 20 degrees.
- **Noisy:** Gaussian noise added with standard deviation `σ = 0.05`.

The models were kept frozen during robustness testing.

### Test accuracy

| Model | Clean | Rotated | Noisy |
|---|---:|---:|---:|
| HOG | 69.17% | 58.33% | 51.67% |
| SIFT | 75.00% | 74.17% | 75.83% |

### Accuracy loss

Accuracy loss is calculated as clean accuracy minus transformed-image accuracy.

| Model | Rotation loss | Noise loss |
|---|---:|---:|
| HOG | 10.83 percentage points | 17.50 percentage points |
| SIFT | 0.83 percentage points | -0.83 percentage points |

The negative noise-loss value for SIFT means that its accuracy increased slightly on this particular noisy test subset. It does not establish that adding noise generally improves SIFT performance.

## Results and Discussion

SIFT achieved higher validation accuracy than HOG:

- HOG validation accuracy: **67.50%**
- SIFT validation accuracy: **80.83%**

SIFT also achieved higher clean test accuracy:

- HOG clean test accuracy: **69.17%**
- SIFT clean test accuracy: **75.00%**

Under rotation and Gaussian noise, HOG experienced larger accuracy reductions. SIFT maintained accuracy between 74.17% and 75.83% across the three test conditions.

These results suggest that SIFT was more effective and robust for this particular experiment. However, the experiment used a limited subset of four classes, so the results should not be generalized to every blood-cell dataset or classification task.

## Limitations

- Only four of the original eight classes were selected.
- The training subset contained 400 images.
- Grayscale preprocessing removed color information.
- Only three SVM regularization values were evaluated.
- Robustness was tested using one rotation angle and one Gaussian-noise level.
- Some SIFT SVM fits produced a convergence warning, indicating that the optimization settings should be reviewed.

## Technologies Used

- Python
- NumPy
- OpenCV
- MedMNIST
- scikit-image
- scikit-learn
- Matplotlib
- Seaborn
- Jupyter Notebook

## Reproducibility

The experiment uses a fixed random seed of `50` for subset sampling. Reproducing the results also requires compatible package versions, consistent preprocessing, and the same model configuration.

## AI Usage

AI assistance was used to help understand feature extraction, debug implementation issues, organize experiments, and interpret evaluation metrics. Numerical results in this README are based on the executed notebook outputs.

## Conclusion

This project demonstrates a practical comparison of HOG and SIFT for classical image classification. HOG produced 8,100 features per image, while fixed-grid SIFT produced 2,048 features per image. SIFT achieved better validation and clean-test accuracy and was less affected by the tested image transformations.

The experiment highlights the importance of feature representation, validation-based hyperparameter selection, and robustness evaluation in computer vision.

