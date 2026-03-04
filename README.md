# GCP Marker Detection in Aerial Images

## Overview

This project aims to detect **Ground Control Point (GCP) markers** in aerial drone images and predict:

1. The **center location (x, y)** of the marker in pixel coordinates  
2. The **shape of the marker** (Cross, Square, or L-Shaped)

The solution uses a **YOLOv8 object detection model** to detect markers and classify their shapes.

---

## Approach

The dataset provides aerial images along with annotations containing:

1. Marker center coordinates `(x, y)`  
2. Marker shape `(Cross, Square, L-Shaped)`

Since **YOLO requires bounding box annotations**, the provided center points were converted into small bounding boxes centered at the marker location.

Each marker center `(x, y)` was converted into a bounding box of size **40 × 40 pixels**.

The annotations were then converted into **YOLO format**:
	class_id x_center y_center width height



After conversion, the dataset was organized into the **standard YOLO directory structure** for training.

---

## Model

The model used for training is:

**YOLOv8n (Ultralytics)**

This lightweight model provides good performance while keeping training and inference efficient.

### Training Configuration

   epochs: 30
   image size: 640
   optimizer: default YOLOv8 settings


YOLOv8 automatically applies several augmentations during training such as:

- Mosaic augmentation  
- Scaling  
- Horizontal flipping  

These augmentations improve model robustness for aerial imagery.

---

## Dataset Characteristics

Some observations about the dataset:

1. Images are **high resolution aerial drone images (~2048 × 1365)**  
2. **GCP markers are small objects** within large images  
3. The dataset is **class imbalanced**, with most samples belonging to the **Square** class  
4. Some entries in the annotation file were missing `verified_shape` and were skipped during dataset preparation  

---

## Inference

After training, the model was used to detect markers in the **test dataset**.

For each predicted bounding box, the center location was calculated using:

	x = (x1 + x2) / 2
	y = (y1 + y2) / 2


The predicted class ID was mapped to the corresponding marker shape.

The results were saved in:
	predictions.json


The output format matches the training annotation structure.

---

## Model Weights

The trained model weights can be downloaded using the link provided in:

	weights_link.txt


---

## Running the Project

### 1. Install dependencies
   pip install ultralytics opencv-python

### 2. Train the model
	python train.py

### 3. Run inference
	python predict.py

predictions.json

---

## Output Format

Example entry in `predictions.json`:

```json
{
  "Kemerton/.../DJI_1234.JPG": {
    "mark": {
      "x": 2459.2,
      "y": 960.4
    },
    "verified_shape": "Cross"
  }
}

##Challenges
1. Center-only annotations

The dataset provides only marker center coordinates.
This was addressed by generating bounding boxes around the center points for training.

2. Small objects

Markers occupy a very small region of the aerial image.
Training images were resized to 640 pixels to balance accuracy and computational efficiency.

3. Class imbalance

The dataset contains significantly more Square markers than other classes.

##Repository Contents:

notebook.ipynb
predictions.json
README.md
weights_link.txt

