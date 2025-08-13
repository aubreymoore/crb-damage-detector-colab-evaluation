# crb-damage-detector-colab-evaluation

This repo contains an evaluation of output from a [Jupyter notebook running on Google Colab](https://colab.research.google.com/github/aubreymoore/crb-damage-detector-colab/blob/main/detect_and_annotate.ipynb). The code for this notebook and the test images I used for the evaluation are stored in [this GitHub repo](https://github.com/aubreymoore/crb-damage-detector-colab).

The object detector model used by the notebook is imported from
``` 
https://github.com/aubreymoore/code-for-CRB-damage-ai/raw/refs/heads/main/models/3class/train5/weights/best.pt
```
The [training parameters file](https://github.com/aubreymoore/code-for-CRB-damage-ai/blob/main/models/3class/train5/args.yaml) includes:
* model: yolov8n.pt
* imgsz: 960

The model was trained to detect 3 classes 'live', 'dead', and 'vcut'. 


## Results from the crb-damage-detector-colab Jupyter notebook

On 2025-08-12 I tested the notebook with 2 small sets of images:
* 5 images of coconut palm images from from Vanuatu provided by Sulav Paudel at AgResearch New Zealand.
* 3 images of coconut palms from Maui provided by Nicole Ferguson at Hawaii Department of Agriculture.

Note that I reduced the size of all images to a maximum dimension of 960 pixels before testing. 

Results from the tests are saved in the **Vanuatu** and **Maui** folders.

---

## Results for scans of Vanuatu images
[Click here to see detections in tabular form](https://github.com/aubreymoore/crb-damage-detector-colab-evaluation/blob/main/Vanuatu/detections.csv)

---

Vanuatu: IMG_0532_annotated.JPG
* no obvious problems
![](https://github.com/aubreymoore/crb-damage-detector-colab-evaluation/blob/main/Vanuatu/IMG_0532_annotated.JPG)

---

Vanuatu: IMG_0671_annotated.JPG
* dead palms not detected
![](https://github.com/aubreymoore/crb-damage-detector-colab-evaluation/blob/main/Vanuatu/IMG_0671_annotated.JPG)

---

Vanuatu: IMG_0695_annotated.JPG
* vcuts not detected
![](https://github.com/aubreymoore/crb-damage-detector-colab-evaluation/blob/main/Vanuatu/IMG_0695_annotated.JPG)

---

Vanuatu: IMG_0704_annotated.JPG
* vcuts not detected
![](https://github.com/aubreymoore/crb-damage-detector-colab-evaluation/blob/main/Vanuatu/IMG_0704_annotated.JPG)

---

Vanuatu: IMG_0713_annotated.JPG
* vcut not detected

![](https://github.com/aubreymoore/crb-damage-detector-colab-evaluation/blob/main/Vanuatu/IMG_0713_annotated.JPG)

---

## Results for scans of Maui images
[Click here to see detections in tabular form](https://github.com/aubreymoore/crb-damage-detector-colab-evaluation/blob/main/Maui/detections.csv)

---

Maui: Hyatt coconut_annotated.jpg
* vcut not detected
![](https://github.com/aubreymoore/crb-damage-detector-colab-evaluation/blob/main/Maui/Hyatt%20coconut_annotated.jpg)

---

Maui: Kaanapali Golf Course_annotated.jpg
* 2 false positive vcuts
* 1 vcut undetected
![](https://github.com/aubreymoore/crb-damage-detector-colab-evaluation/blob/main/Maui/Kaanapali%20Golf%20Course_annotated.jpg)

---

## Maui: Ulua Beach_annotated.jpg
![](https://github.com/aubreymoore/crb-damage-detector-colab-evaluation/blob/main/Maui/Ulua%20Beach_annotated.jpg)
* no obvious problems

---

## Evaluation

Visual inspection of the 8 annotated images returned by the Colab notebook shows that the model performed poorly on both data sets:
* objects were detected without obvious error in only 1 of 5 of the images from Vanuatu
* objects were detected without obvious error in only 1 of 3 of the images from Maui

These results from both datasets indicate that the model needs to be retrained. An inspection of the training data shows that there are there are major problems with this model which need to be addressed:
* the model is based on yolov8n. This is the smallest YOLOv8 model ("n" stands for nano; larger models include small, medium, large and extra-large). Using a larger base model might result in improvement.
* images are reduced to a maximum dimension of 960 pixels before being scanned. Increasing imgsz to 1920, to match the original size of images in the training set (1920x1080 pixels) may result in improvement.
* there is a massive class imbalance in the training set (see [labels.jpg](https://github.com/aubreymoore/code-for-CRB-damage-ai/blob/main/models/3class/train5/labels.jpg), [confusion_matrix.png](models/3class/train5/confusion_matrix.png) and [confusion_matrix_normalized](models/3class/train5/confusion_matrix_normalized.png). Fixing this class imbalance using augmention and other methods may lead to improvement. 

Poor performance on 2 of 3 images from Maui may be the result of another problem. The current model was trained with roadside survey images showing whole coocnut palms or at least whole crowns of coconut palms. The two problematic images from Maui shows only parts of single fronds. It is possible that there the object detector is not getting enough context from these images.

# Next steps

I will fine tune and retrain the 3-class model using suggestions included in the evaluation section. I will then replace the current model in the notebook, rerun the code, and re-evaluate.
