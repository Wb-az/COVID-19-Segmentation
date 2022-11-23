<h1><div style="text-align: center;">  COVID-19 Detection and Instance semantic segmentation in CT image slices.</div></h1>


## 1. Introduction

Medical classification has widely benefited from recent developments in computer vision, especially deep artificial neural networks. This work is the continuation of the COVID-19 classification study, which has already been published in the [Journal of Imaging]&#40;https://www.mdpi.com/2313-433X/8/9/237#cite&#41;. Here we evaluated the performance of the Mask-RCNN deep learning neural network to segment lesions of COVID-19 on chest CT 2D images. The kernel of the feature extractor of the Mask-RCNN-ResNet-50-FPN was resized for the segmentation of COVID-19 in infected lungs, together with the creation of custom anchors for detection. We additionally built Mask-RCNN-MobileNet-v3-large-FPN. The fine-tuned Mask-RCNN-ResNet-50 -FPN was the base to compare whether the proposed models surpassed the base model's performance segmentation and detection.)


## 2. Experimental Design

The experimental setup consisted of the combination of the Mask-RCNN architecture with modification to the ResNet-50 feature extractor:

- Resize kernels from the first convolutional layer of the feature extractor - the original 
  dimensions were size 7 x 7. The kernels were resized to 3 x 3 and 5 x 5. 
- Customed anchors - standard size is (32, 64, 128, 256, 512) and aspect ratios are (0.5, 1.0, 2.0). 
  The custom sizes were anchor sizes= (8, 16, 32, 64, 128, 256) and aspect ratios=(0.25, 0.5, 1.0, 
  1.5, 
  2.0). 

In addition, we built Mask-RCNN-MobileNet-large-v3-FPN with the following features:
- anchor sizes = (8, 16, 32, 64, 128, 256) and 
aspect_ratios = ((0.25, 0.5, 1.0, 2.0),) 

Table 1 shows the experimental design for this piece of work.



<h3><div style="text-align: center;"> Experimental Design </div></h3>

<div align="center">


| Exp |     Model      |                Net                | Resized </br>Kernel | Sizes | Customed</br>Anchors |
|-----|:--------------:|:---------------------------------:|:-------------------:|:-----:|:--------------------:|
| 1   |   Base model   |      Mask-RCNN-ResNet-50-FPN      |          -          |   -   |          -           |
| 2   |    Mask-CA     |      Mask-RCNN-ResNet-50-FPN      |          -          |   -   |         True         |
| 3   |    Mask-RK3    |      Mask-RCNN-ResNet-50-FPN      |        True         | 3 x 3 |          -           |
| 4   |  Mask-RK3-CA   |      Mask-RCNN-ResNet-50-FPN      |        True         | 3 x 3 |         True         |
| 5   |    Mask-RK5    |      Mask-RCNN-ResNet-50-FPN      |        True         | 5 x 5 |          -           |
| 6   |  Mask-RK5-CA   |      Mask-RCNN-ResNet-50-FPN      |        True         | 5 x 5 |         True         |
| 7   | Mask-Mobile-CA | Mask-RCNN-MobileNet-v3-large-FPN  |          -          |   -   |          -           |
</div>

## 3. Datasets
The COVID-19 data was ingested from the sources below:

1. COVID-19 Lung CT Lesion Segmentation Challenge - 2020, 199 patients https://covid-segmentation.
grand-challenge.org/COVID-19-20/

2. COVID-19 CT Lung and Infection Segmentation Dataset, 20 patients (only infection mask used) 
https://zenodo.org/record/3757476#.YTdEx55Kg1h

3. Medseg AI - SIRM ( dataset 100 scans from 48 patients - https://medicalsegmentation.com/covid19/

4. MosMedData Dataset COVID19_1110, 50 patients https://mosmed.ai/datasets/covid19_1110/

Except for the MosMedData, all datasets were compressed NIfTI volumes (nii.gz). All images were put together in one dataset.


## 4. Methods

### 4.1 Data Processing

The 3D volumes were sliced on plane z (axial) and converted to 2D images. All sliced images were put 
together in one dataset.

The sliced dataset was split into three subsets using the three-way holdout method with ratios 
of 80:10:10 for training, validation, and testing. 

**Associated code**
``` utility\convert_nii2png.py``` and ```utility\nii_vis_dataset.ipyn ``` 


### 4.2 Training and Validation

We trained and validated the models for 30 to 35 epochs and recorded the loss, mean average precision for detection and segmentation (mAP) for the training and validation subsets.

**Associated code**
- ```covid_dataset.py```
- ```detection``` folder
- ```detection``` folder
- `````` utility\plotting.py``````
- ```maskrcnn_main.ipynb```


### 4.3 Evaluation
The models were evaluated with the COCO style metric mean average precision (mAP or AP) for the 
segmentation and detection of COVID-19. The mAP at intersections over union (IoU)of 0.50, 0.75 
and the range (0.5, 0.95, 05) was computed at each training epoch on the validation subset. The 
performance of each trained model was also evaluated on the test dataset.


## 5. Results

| Exp | mAPb      | APb @ <br/>IoU=0.50 | APb @ <br/>IoU=0.75 | mAPseg    | mAPseg <br/>@ IoU=0.50 | mAPseg <br/>@ IoU=0.75 | 
|-----|-----------|---------------------|---------------------|-----------|------------------------|------------------------|
| 1   | 0.3608    | 0.6856              | 0.3328              | 0.3256    | 0.6848                 | 0.2588                 | 
| 2   | 0.424     |  0.726              | 0.434               | 0.368     | 0.714                  | 0.314                  | 
| 3   | __0.5961__|  __0.8713__        | __0.6606___         | __0.5219__| __0.8644__             | __0.5625__             | 
| 4	| __0.6169__| __0.8305__          | __0.6797__          | __0.5178__| __0.8223__             | __0.5872__               | 
| 5   | 0.4804    | 0.7869              | 0.5068              | 0.4275    | 0.7888                 | 0.4298                 | 
| 6   | 0.5671    | 0.7853              | 0.6259              | 0.4671    | 0.7769                 | 0.515                  | 
| 7   | 0.3177    | 0.6203              | 0.2898              | 0.2648    | 0.5947                 | 0.2124                 |



Our results showed that models with backbone ResNet-50-FPN and resized kernel to 3 x 3 reached a higher performance than the base model,  resized to 5 x 5 kernel with or without custom anchors.

### 5.1 Inference example

(<img src='figures/predictions_exp_3_maskrcnn-resnet50-fpn_r.png' height='200'/>)

(<img src='/figures/predictions_exp_4_maskrcnn-resnet50-fpn_ra.png' height='200'/>)

(<img src='/figures/predictions_5_maskrcnn-resnet50-fpn_r.png' height='200'/>)

(<img src='figures/predictions_6_maskrcnn-resnet50-fpn_r.png' height='200'/>)


### Installation
- Set up a Python 3.8 environment or higher
- Install Pycocotools `` pip install cython`` and
```pip install -U 'git+https://github.com/cocodataset/cocoapi.git#subdirectory=PythonAPI```

