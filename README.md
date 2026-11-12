# DefectIQ-AI — AI-Powered Steel Surface Defect Detection

DefectIQ-AI is an AI-powered steel surface inspection application that detects defects in steel images, evaluates their severity, and recommends an inspection decision.

The application uses YOLO object detection and a Streamlit dashboard to make steel defect inspection easier to understand and manage.

## Features

- Upload steel surface images for inspection.
- Detect defects using a trained YOLO model.
- Display detected defect locations and confidence scores.
- Evaluate defect severity.
- Recommend an inspection decision:
  - `ACCEPT`
  - `REWORK`
  - `REJECT`
  - `MANUAL_REVIEW`
- Display inspection results through a Streamlit dashboard.
- Store inspection records using SQLite.

## Technologies Used

| Technology | Purpose |
|---|---|
| Python | Main programming language |
| YOLOv8 (Ultralytics) | Steel defect detection |
| Streamlit | Web application and dashboard |
| OpenCV | Image processing |
| NumPy and Pandas | Data handling |
| SQLite | Inspection record storage |
| PyYAML | Configuration management |
| Pytest | Testing |

## Project Structure

```text
DefectIQ-AI/
│
├── app.py
├── config.yaml
├── requirements.txt
├── specifications.py
├── reference.csv
│
├── models/
│   └── best.pt
│
├── scripts/
│   ├── prepare_dataset.py
│   └── check_labels.py
│
├── src/
│   ├── detector.py
│   ├── pipeline.py
│   ├── severity.py
│   └── storage.py
│
├── tests/
│
├── ACKNOWLEDGEMENTS.md
└── README.md
```

## Installation and Setup

### 1. Clone the repository

```bash
git clone https://github.com/Akshaya0518/DefectIQ-AI.git
cd DefectIQ-AI
```

### 2. Create a virtual environment

```bash
python -m venv .venv
```

### 3. Activate the virtual environment

For Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

If PowerShell blocks activation, use the environment's Python executable directly.

### 4. Install dependencies

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

## Model Weights

The trained YOLO model is included in the repository:

```text
models/best.pt
```

The application uses this model to detect steel surface defects.

No additional model download or training is required to run the application.

## Running the Application

After installing the dependencies, run:

```bash
streamlit run app.py
```

The Streamlit application will open in your browser.

Upload a steel surface image and run the inspection to view the detected defects and inspection decision.

## Dataset Preparation

### Dataset Used

The project uses the NEU-DET steel surface defect dataset, containing 1,800 images with six defect categories.

The dataset includes images and XML annotations.

### Defect Categories

1. Crazing
2. Inclusion
3. Patches
4. Pitted Surface
5. Rolled-in Scale
6. Scratches

### Dataset Directory Structure

The dataset is not included in this GitHub repository. After obtaining and extracting the dataset, arrange it in the following structure:

```text
data/
└── NEU-DET/
    └── NEU-DET/
        └── NEU-DET/
            ├── IMAGES/
            └── ANNOTATIONS/
```

The dataset preparation script converts the XML annotations into YOLO format and divides the images into training, validation, and test sets.

Run:

```bash
python scripts/prepare_dataset.py
```

The prepared dataset will be generated in:

```text
data/neu_yolo/
```

### Dataset Split

| Split | Number of Images |
|---|---:|
| Training | 1,260 |
| Validation | 270 |
| Testing | 270 |
| **Total** | **1,800** |

## Model Training

The project uses YOLOv8n for steel surface defect detection.

The model was trained using the prepared NEU-DET dataset with the following configuration:

| Parameter | Value |
|---|---|
| Model | YOLOv8n |
| Epochs | 50 |
| Image size | 640 × 640 |
| Batch size | 4 |
| Device | CPU |
| Dataset | NEU-DET |

### Training Command

To reproduce the training process after preparing the dataset, run:

```bash
yolo detect train model=yolov8n.pt data=data/neu_yolo/data.yaml epochs=50 imgsz=640 batch=4 workers=0 project=training_results name=neu_yolo device=cpu
```

The trained model's best weights are saved as:

```text
runs/detect/training_results/neu_yolo/weights/best.pt
```

To use newly trained weights in the application, copy them into the models folder:

```powershell
Copy-Item ".\runs\detect\training_results\neu_yolo\weights\best.pt" ".\models\best.pt" -Force
```

**Note:** Training is not required to run the application because the trained `models/best.pt` file is already included.

## Model Evaluation Results

The following results were obtained during validation on the 270-image validation split.

| Evaluation Metric | Result |
|---|---:|
| Precision | 0.672 |
| Recall | 0.696 |
| mAP@50 | 0.738 |
| mAP@50–95 | 0.424 |

These results represent the model's validation performance and should not be interpreted as guaranteed performance on real industrial production images.

## Inspection Pipeline

The application follows this workflow:

```text
Steel Surface Image
        |
        v
YOLO Defect Detection
        |
        v
Defect Severity Evaluation
        |
        v
Inspection Decision
        |
        v
SQLite Record Storage
        |
        v
Streamlit Dashboard
```

The detection component identifies defects, the severity component evaluates them using configured rules, and the application displays the final inspection result.

## Testing

Run the project's tests using:

```bash
python -m pytest tests -v
```

## Limitations and Future Improvements

- The model is initially trained using the NEU-DET dataset.
- Performance on real industrial images requires additional testing and plant-specific validation.
- Severity scores and inspection decisions use team-defined rules and are not learned factory acceptance standards.
- Further training with more diverse steel surface images could improve generalization.
- Industrial deployment requires validation against actual manufacturing quality standards.

## Dataset and Project Acknowledgements

The NEU-DET dataset is used for model training and evaluation. Refer to `ACKNOWLEDGEMENTS.md` for dataset attribution and licensing information.

Please check the dataset's original source and applicable terms before redistribution or commercial use.

## License

Check the applicable licenses for the source code, dataset, and third-party libraries before redistributing or using this project commercially.