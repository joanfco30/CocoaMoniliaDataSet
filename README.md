# CocoaMoniliaDataSet
**CocoaMoniliaDataset v1** is an annotated RGB image dataset created to support a computer vision task for the detection of *MoniMoniliophthora roreri* in cocoa pods. **CocoaMoniliaDataset v1** involved the labeling of four classes: (1) Healthy cocoa pods labeled as **h0**, (2) First cycle of Monilia (Humps) labeled as **m1**, (3) It is a combination of the second and third cycle of the disease (Brown and oily spots) labeled as **m2**, and (4) Fourth cycle of Monilia (Sporulation) labeled as **m3**. The annotation formats are provided in **COCO 1.0**, **YOLO**, and **Segmentation Maks 1.1**.

## The dataset is available at the Zenodo repository

Repository name:  Zenodo

Data identification number:  [10.5281/zenodo.17156051](https://zenodo.org/records/17156052).

Direct URL to data: [https://zenodo.org/records/17156052](https://zenodo.org/records/17156052) 

## **CocoaMoniliaDataset v1** structure
The repository follows the next structure.
```
CocoaMoniliaDataSet/
│
├── cocoapods_images/
│     ├── h0/
│     ├── m1/
│     ├── m2/
│     ├── m3/
│
│── COCO_annotations/
│     ├── instances_h0.json
│     ├── instances_m1.json
│     ├── instances_m2.json
│     ├── instances_m3.json
│
│── mask_segmentation/
│     ├── SegmentationClassh0/
│     ├── segmentationObjecth0/
│     ├── SegmentationClassm1/
│     ├── segmentationObjectm1/
│     ├── SegmentationClassm2/
│     ├── segmentationObjectm2/
│     ├── SegmentationClassm3/
│     ├── segmentationObjectm3/
│  
│── YOLO_annotations/
      ├── h0/
      ├── m1/
      ├── m2/
      ├── m3/
```
Each directory contains images and annotations, along with their respective class labels. 

## Class Definitions


| Label  | Class name |Description |
| ------------- | ------------- |------------- |
|h0  | Healty  | Cocoa pods without visible *Monilia* symptoms  |
| m1  | Humps  | First cycle of Monilia   | 
| m2  | Brown/oily spots  | Second-third cycle of *Monilia*  | 
| m3  | Sporulation | Fourth cycle pf *Monilia*  | 


## Download and unzip the dataset.
```
!wget -O dataset.zip "https://zenodo.org/records/17156052/files/CocoaMoniliaDataSet.zip?download=1"
!unzip dataset.zip
```

## Example for loading COCO Annotation format

```ruby
from pycocotools.coco import COCO
coco = COCO("COCO_annotations/instances_m2.json")
image_ids = coco.getImgIds()
ann_ids = coco.getAnnIds(imgIds=image_ids[0])
annotations = coco.loadAnns(ann_ids)
```
## Example for loading COCO Annotation to convert to YOLO object detection format

In YOLO object detection format, each image has a corresponding txt file with a single line with the box coordinates for each bounding box. The format of each row is the following:

```
<class_id> <center_x> <center_y> <width> <height> 
```
### The next code is a way to iterate over annotations to convert COCO format to YOLO object detection.

```ruby
for ann in coco["annotations"]:
  image_id = ann["image_id"]
  cat_id = ann["category_id"]
  if format_ == "bbox":
    bbox  = ann[format_]
    x_min, y_min, w_box, h_box = bbox
    image_info = images[image_id]
    img_w, img_h = image_info["width"], image_info["height"]

    cx = (x_min + w_box / 2) / img_w
    cy = (y_min + h_box / 2) / img_h
    w_norm = w_box / img_w
    h_norm = h_box / img_h

    yolo_line = f"{cat_id - 1} {cx:.6f} {cy:.6f} {w_norm:.6f} {h_norm:.6f}
```

## Example for loading COCO Annotation to convert YOLO instance segmentation

In YOLO instance segmentation, each image has a corresponding txt file with coordinates that outline each object in the image. The format of each row is the following:

```
<class-index> <x1> <y1> <x2> <y2> ... <xn> <yn>
```

### The next code is a way to iterate over annotations to convert COCO format to YOLO instance segmentation.

```ruby
if isinstance(segmentation, list):
  for seg in segmentation:
    if len(seg) < 6:
      continue  
                  
    norm_coords = []
    for i in range(0, len(seg), 2):
      x = seg[i] / img_w
      y = seg[i + 1] / img_h
      norm_coords.append(f"{x:.6f} {y:.6f}")

    line = f"{cat_id - 1} " + " ".join(norm_coords)  
    yolo_lines.append(line)
```

### Methods to split the dataset


<img width="697" height="117" alt="image" src="https://github.com/user-attachments/assets/2c573c83-bb2a-44dd-9800-65331baad92e" />

[CocoaMoniliaDataSet.ipynb](https://github.com/joanfco30/CocoaMoniliaDataSet/blob/main/CocoaMoniliaDataSet.ipynb) notebook includes  methods to split the dataset into training, validation, and test for both object detection and instance segmentation. The methods create a dataset structure, as is illustrated below. By default, YOLO expects this structure. 


```
│── DataSet_split_seg/
│     ├─ images/
│     │    ├── train/
│     │    ├── test/
│     │    ├── val/
│     │
│     │
│     ├─ labels/
           ├── train/
           ├── test/
           ├── val/
```
