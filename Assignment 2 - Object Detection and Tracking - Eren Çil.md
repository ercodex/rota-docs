 This document contains the steps I did to complete assignment 2. I collected data from various resources, like YouTube videos and other datasets. Then, I evaluated the model on this new dataset (which the model never seen, probably). After that, I created some visuals and calculated average for IoU in order to check model performance.

We need a lot of images. So let's start.

First, I searched on YouTube. Finding how to search about what I want was not clear at first. So, I went right into public datasets. Some screenshots were containing the name of the videos. I searched these videos and started taking screenshots.

In assignment, It is wanted from me to collect videos. I took it as a second step. And since videos are formed from tens of thousands of images, first step is to detect from images. I am planning to evaluate the model on a video though.

After some cleaning, dataset is over. I collected nearly 750 images from different sources. Sources will be provided if wanted. Next part is, labeling.

After searching about apps, I decided to use "VoTT", an open source and free labeling software developed by Microsoft. It was very useful and provided some great features. There are multiple output formats, it let's you manage the project etc. 

It took 7-8 hours to label these images. And since I aim to write the model with PyTorch, I exported it in Pascal format. But labeling software (VoTT) don't have the output type that YOLO supports (yaml). And since the model we have is a YOLO variant, I need to make some adjustments to achieve the bounding box coordinates. 

So, I used this Python code to pull true bounding box coordinates from .xml files and then calculate the IoU between model predictions.

~~~
from ultralytics import YOLO
import cv2
import torch
import xml.etree.ElementTree as ET
import pandas as pd
import os

def parse_pascal_voc(xml_file):
    tree = ET.parse(xml_file)
    root = tree.getroot()

    gt_boxes = []
    
    for obj in root.findall(".//object"):
        bbox = obj.find("bndbox")
        
        if bbox is not None:
            x1 = bbox.find("xmin")
            y1 = bbox.find("ymin")
            x2 = bbox.find("xmax")
            y2 = bbox.find("ymax")
            
            if x1 is not None and y1 is not None and x2 is not None and y2 is not None:

                try:
                    x1_value = x1.text if x1.text else '0'
                    y1_value = y1.text if y1.text else '0'
                    x2_value = x2.text if x2.text else '0'
                    y2_value = y2.text if y2.text else '0'
  
                    gt_boxes.append([
                        float(x1_value),
                        float(y1_value),
                        float(x2_value),
                        float(y2_value)
                    ])

  

                    print(f"gt_boxes test: {gt_boxes}")
                
                except ValueError as e:
                    print(f"ValueError: {e} in {xml_file}")
                    continue

                except Exception as e:
                    print(f"Unexpected error: {e} in {xml_file}")
                    continue

    return gt_boxes

def calculate_iou(boxA, boxB):
    xA = max(boxA[0], boxB[0])
    yA = max(boxA[1], boxB[1])
    xB = min(boxA[2], boxB[2])
    yB = min(boxA[3], boxB[3])
    
    # If there's no intersection, return 0
    if xB <= xA or yB <= yA:
        return 0.0

    interArea = (xB - xA) * (yB - yA)
    boxAArea = (boxA[2] - boxA[0]) * (boxA[3] - boxA[1])
    boxBArea = (boxB[2] - boxB[0]) * (boxB[3] - boxB[1])

    iou = interArea / float(boxAArea + boxBArea - interArea)
    
    return iou

# Check if CUDA (GPU) is available
device = 'cuda' if torch.cuda.is_available() else 'cpu'
print(f"Prediction process runs on {device}")


# Load a pretrained YOLO model
model = YOLO("best.pt").to(device)

# Directories for images and annotations
image_dir = r"C:\fixed-wing-uav-dataset\labeled-processed-images\fixed-wing-uav-dataset-PascalVOC-export\JPEGImages"

xml_dir = r"C:\fixed-wing-uav-dataset\labeled-processed-images\fixed-wing-uav-dataset-PascalVOC-export\Annotations"

output_csv = "iou_results.csv"

# Collect all image files (jpeg, jpg, png)

image_files = [f for f in os.listdir(image_dir) if f.lower().endswith((".jpeg", ".jpg", ".png"))]
  
data_list = []

for image_file in image_files:
    image_path = os.path.join(image_dir, image_file)
    xml_path = os.path.join(xml_dir, os.path.splitext(image_file)[0] + ".xml")
    print(f"Processing image {image_file}")
    
    if not os.path.exists(xml_path):
        print(f"Skipping {image_file}, no matching XML file found.")
        continue

    # Perform object detection
    results = model(image_path)
    
    # Extract bounding boxes and class labels
    boxes = results[0].boxes.xyxy.cpu().numpy()
    confidences = results[0].boxes.conf.cpu().numpy()
    classes = results[0].boxes.cls.cpu().numpy()
    labels = results[0].names  # Class labels

    # Load ground truth bounding boxes
    gt_boxes = parse_pascal_voc(xml_path)
    
    # Compute IoU scores
    ious = []
    for box in boxes:

        x1, y1, x2, y2 = box[:4]
        best_iou = 0
        for gt_box in gt_boxes:
        
            iou = calculate_iou([x1, y1, x2, y2], gt_box)
            best_iou = max(best_iou, iou)

        ious.append(best_iou)

    # Save results to list
    for box, confidence, cls, iou in zip(boxes, confidences, classes, ious):
        x1, y1, x2, y2 = box
        label = labels[int(cls)]
        data_list.append([image_file, label, confidence, x1, y1, x2, y2, iou])

    # Draw bounding boxes on the image
    image = cv2.imread(image_path)
    for box, confidence, cls, iou in zip(boxes, confidences, classes, ious):
        x1, y1, x2, y2 = box
        label = labels[int(cls)]
        color = (0, 255, 0)  # Green color for bounding box

        # Draw rectangle around the detected object
        cv2.rectangle(image, (int(x1), int(y1)), (int(x2), int(y2)), color, 2)

        # Add label text with confidence score and IoU
        cv2.putText(image, f'{label} {confidence:.2f} IoU: {iou:.2f}', (int(x1), int(y1) - 10),

                    cv2.FONT_HERSHEY_SIMPLEX, 0.6, color, 2)

    # Save the image with bounding boxes
    output_image_path = os.path.join("output_images", image_file)
    cv2.imwrite(output_image_path, image)

# Save all results to CSV

df = pd.DataFrame(data_list, columns=["image", "label", "confidence", "x1", "y1", "x2", "y2", "iou"])

df.to_csv(output_csv, index=False)
~~~

After labeling, I used the custom YOLO model from last year and used it on dataset that I gathered. It was good, but not enough. Here are the results:

![[IoU.png]]

As you can see, there is room for improvement at IoU rates between %40 - %80. And they make up half of the data! Also model performed very bad on more than %10 of the data, like with the rates of 0-0.1

If that wasn't enough, average IoU is... %0.613.

Finally, I tested the model on a video. Overall confidence is %73.6
You can see the predictions here, https://www.youtube.com/watch?v=x19Cp2NfP8w

Conclusion:
This assignment is completed on dataset version(1.0). In the future versions, there will be:
- More data

- Negative sampling

- Data will be augmented. Data size will go up to its 3x-4x size
	- Augmentation techniques -> Max. 15 degree rotations, Brightness, Zoom, Contrast

- More evaluation metrics to get better insight on both data and model

- Some techniques will be applied to determine the patterns where model performs bad. And with information, specific negative samples will be added to improve model performance. Bad scenarios will be handled.

- Simultaneously, the custom CNN layers will be tested to achieve better results. Like I said, we don't need to use YOLO if we create and train our own model. Which is a plus for us. A model with a well-detailed logical background will give a better performance.
