---
comments: true
description: Learn how to generate segmentation masks with object detection and SAM2 models for advanced image processing tasks.
keywords: object segmentation, object tracking, Ultralytics YOLO11, SAM2 model, automatic annotation, segmentation masks, AI object detection, real-time image processing, custom dataset, computer vision, advanced image analysis
---

# How to Generate Accurate Segmentation Masks Using Object Detection and SAM2 Models

Segmentation masks are essential for accurate object tracking and analysis, enabling precise identification of objects at the pixel level. By leveraging the power of a fine-tuned [Ultralytics](https://docs.ultralytics.com/) [YOLO11](https://docs.ultralytics.com/models/yolo11/) model alongside the [Segment Anything 2](https://docs.ultralytics.com/models/sam-2/) model, this process can achieve remarkable precision and adaptability.

<figure>
    <img width="1920" src="https://github.com/user-attachments/assets/17e47c34-dad3-434f-abaa-102d7635f862" alt="Featured Image for Instance Segmentation">
    <figcaption>Fig-1: Featured Image for Instance Segmentation</figcaption>
</figure>

## Hardware, Model, and Dataset Information

- **CPU**: Intel® Core™ i5-10400 CPU @ 2.90GHz for efficient processing.  
- **GPU**: NVIDIA RTX 3050, optimized for real-time processing tasks.  
- **RAM and Storage**: Equipped with 64 GB of RAM and a 1TB hard disk for seamless performance.  
- **Model**: A fine-tuned YOLO11 model was employed for object detection.  
- **Dataset**: Proprietary dataset, meticulously annotated in-house by the team for optimal accuracy.  

## Code Example

### Step 1: Prepare the Model
Use a custom-trained YOLO11 model or utilize the [Ultralytics Pretrained Models](https://docs.ultralytics.com/models/) for object detection. 

### Step 2: Auto Annotation using SAM 2

Now, it's time to integrate the SAM2 model for image segmentation.

```{ .py .annotate }
# Install the necessary library
# pip install ultralytics

from ultralytics.data.annotator import auto_annotate

# Automatically annotate images using YOLO and SAM2 models
auto_annotate(data="Path/to/images/directory",
              det_model="yolo11n.pt",
              sam_model="sam2_b.pt")
```

### Step 3: Generate Segmentation Masks

After running the above script, the generated segmentation masks will be saved as `.txt` files in the `images_auto_annotate_labels` folder.

### Step 4: Visualize the Segmentation Masks

Use the following script to overlay segmentation masks on the original images.

```{ .py .annotate }
import cv2
import numpy as np
import os

# Define folder paths
image_folder = "images_directory"   # Path to your images directory
mask_folder = "images_auto_annotate_labels"  # Path to annotation masks directory created by SAM2
output_folder = "output_directory"  # Path to save output images

os.makedirs(output_folder, exist_ok=True)

# Define colors for classes (extend this as needed for more classes)
class_colors = [
    (0, 255, 0),    # Green for Class 0
    (0, 0, 255),    # Red for Class 1
    (255, 0, 0),    # Blue for Class 2
    (255, 255, 0),  # Cyan for Class 3
    (255, 0, 255),  # Magenta for Class 4
    (0, 255, 255),  # Yellow for Class 5
]

for image_file in os.listdir(image_folder): # Process each image in the folder
    image_path = os.path.join(image_folder, image_file)
    mask_file = os.path.join(mask_folder, os.path.splitext(image_file)[0] + ".txt")
    
    if not os.path.exists(mask_file):
        print(f"No mask file found for {image_file}. Skipping...")
        continue

    img = cv2.imread(image_path)  # Load the image
    height, width, _ = img.shape

    with open(mask_file, "r") as f: # Read the mask file
        lines = f.readlines()

    for line in lines:
        data = line.strip().split()  # Parse class ID and points
        class_id = int(data[0])  # Class ID
        color = class_colors[class_id % len(class_colors)]  # Assign color for the class
        
        # Extract normalized x, y points and convert to absolute coordinates
        points = [(float(data[i]) * width, float(data[i + 1]) * height) for i in range(1, len(data), 2)]
        points = np.array(points, dtype=np.int32).reshape((-1, 1, 2))  # Required shape for OpenCV
        
        # Draw the polygon
        cv2.polylines(img, [points], isClosed=True, color=color, thickness=2)  # Contour width = 2
        cv2.fillPoly(img, [points], color=color)  # Fill with same color

    # Save the output
    output_path = os.path.join(output_folder, image_file)
    cv2.imwrite(output_path, img)
    print(f"Processed {image_file} and saved to {output_path}")

print("Processing complete.")
```

<figure>
    <img width="1920" src="https://github.com/user-attachments/assets/607505f6-feda-4453-843a-28e4d9b3f8ff" alt="Image Description">
    <figcaption>
        Fig-2: Instance segmentation using Ultralytics YOLO11 and SAM2 model.
    </figcaption>
</figure>

## Real-World Applications

- **Medical Imaging**: Segment organs, tissues, or anomalies in medical scans for diagnostic and research purposes.  
- **Autonomous Vehicles**: Enhance vision systems by accurately identifying and segmenting objects like pedestrians, vehicles, and road signs in real-time.  
- **Retail Analytics**: Analyze customer behavior by detecting and segmenting products or people in stores to optimize layouts and improve sales strategies.  
- **Wildlife Monitoring**: Track and segment animals in natural habitats for research and conservation efforts.  
- **Robotics**: Enable robotic systems to identify and manipulate objects in unstructured environments using precise segmentation.  
- **Satellite Imagery Analysis**: Segment land types, urban areas, or vegetation in satellite images for environmental monitoring and urban planning.

<figure>
    <img width="1920" src="https://github.com/user-attachments/assets/2db66fc5-454a-42f5-a176-d166ebe89518" alt="Real-World Applications of Instance Segmentation">
    <figcaption>Fig-3: Real-World Applications of Instance Segmentation</figcaption>
</figure>

## Social Resources

Explore more resources, tutorials, and use cases on the [Ultralytics documentation](https://docs.ultralytics.com/guides/instance-segmentation-and-tracking/).

- [Author: LinkedIn Post](https://www.linkedin.com/feed/update/urn:li:activity:7264975021058482177/)