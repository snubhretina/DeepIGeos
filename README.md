# :brain: DeepIGeoS
Implementation the DeepIGeoS Paper
> :page_facing_up: [DeepIGeoS: A Deep Interactive Geodesic Framework for Medical Image Segmentation (2018)](https://ieeexplore.ieee.org/abstract/document/8370732)
 
> additional page : [Notion page](https://www.notion.so/modulabs/HIT-LAB-783449555af449f89c0b2f2dc4a5a72b?p=9f00609e62a24d32af95a7416703119f) (in Korean :kr:) 

## 👨🏻‍💻 Contributors
[이영석](https://github.com/younnggsuk), [이주호](https://github.com/wngh577), [이준호](https://github.com/junhoning), [정경중](https://github.com/KyeongJoong), [손소연](https://github.com/soyounson), [조현우](https://github.com/hwcho1130)

## 👨🏻‍💻 Contributors for fundus image segmentation thanks for contributor
[김주영](https://github.com/gamju)

## :mag: Prerequisites
Please check environments and requirements before you start. If required, we recommend you to either upgrade versions or install them for smooth running.

![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)
![PyTorch](https://img.shields.io/badge/PyTorch-%23EE4C2C.svg?style=for-the-badge&logo=PyTorch&logoColor=white)
![Qt](https://img.shields.io/badge/Qt-%23217346.svg?style=for-the-badge&logo=Qt&logoColor=white)

### ☺︎ Environments
`Ubuntu 16.04`  
`Python 3.7.11`

### ☺︎ Requirements
```
dotmap
GeodisTK
opencv-python
tensorboard
torch
torchio
torchvision
tqdm
PyQt5
```

## :film_strip: Datasets
Download the BraTS 2021 dataset form web [BraTS 2021](https://arxiv.org/pdf/2107.02314.pdf) using `load_datasets.sh`.  

```
$ bash load_datasets.sh
```

## :computer: Train

### ☺︎ P-Net
```
$ python train_pnet.py -c configs/config_pnet.json
```

### ☺︎ R-Net
```
$ python train_rnet.py -c configs/config_rnet.json
```

### ☺︎ Tensorboard
```
$ tensorboard --logdir experiments/logs/
```

## :computer: Run

### ☺︎ Simple QT Application
To operate DeepIGeos with simple mouse click interaction, We create a QT based application. You can just run DeepIGeoS with the main code titled 'main_deepigeos.py' as shown below.
```
$ python main_deepigeos.py
```

## :dna: Results

### ☺︎ with the Simulated Interactions
After the simulated user interaction, the result follows rules below : 

Simulations were generated on three slices with the largest mis-segments in each axes; sagittal, coronal, and axial.
1. To find mis-segmented regions, the automatic segmentations by P-net are compared with the ground truth.
2. Then the user interactions on the each mis-segmented region are simulated by **n** pixels, which is randomly sampled, in the region. (Suppose the size of one connected under-segmented or over-segmentedregion is **Nm**, we set **n** for that region to **0** if **Nm < 30** and **[Nm/100]** otherwise)

<div>
  <img alt="Sagittal" src=./assets/sagittal.gif width="34.7%">
  <img alt="Coronal" src=./assets/coronal.gif width="30%">
  <img alt="Axial" src=./assets/axial.gif width="25%">
</div>

- **Yellow border** : Ground Truth Mask
- **Green area** : P-Net Prediction Mask
- **Red area** : R-Net Refinement Mask

### ☺︎ with the User Interaction
Results with user interactions are below : 

https://user-images.githubusercontent.com/36390128/159625917-1528c01b-fd4a-4fcc-9f20-f6d070fd4822.mp4

1. Click `LOAD IMG` button to load image
2. Click `P-NET` button to operate automatic segmentation
3. Check the automatic segmentation results
4. Point where to refine results by mouse click 
    - Circle : Under-segmented region
    - Square : Over-segmented region
5. Click `R-NET` button to operate refinement
6. Check refined segmentation results

<div>
  <img alt="pnet_mask" src=https://user-images.githubusercontent.com/36390128/160960826-0b2e0670-b19c-4bda-a682-73fa12de712e.png width="27%">
  <img alt="rnet_mask" src=https://user-images.githubusercontent.com/36390128/160960832-a97c6902-ebdd-47b3-a154-f39700f4f68a.png width="27%">
  <img alt="gt_mask" src=https://user-images.githubusercontent.com/36390128/160960814-c05363ec-4c4a-4b95-95b5-d73ab2c9ea7a.png width="27%">
</div>

- **Green Volume** : P-Net Prediction Mask
- **Orange Volume** : R-Net Refinement Mask
- **Blue Volume** : Ground Truth Mask

## :page_facing_up: Background of DeepIGeoS
For the implementation of the DeepIGeoS paper, all steps, we understood and performed are described in the following subsections. 

### ☺︎ Abstract
- Consider a deep CNN-based interactive framework for the 2D and 3D medical image segmentation
- Present a new way to combine user interactions with CNNs based on geodesic distance maps
- Propose a resolution-preserving CNN structure which leads to a more detailed segmentation result compared with traditional CNNs with resolution loss
- Extend the current RNN-based CRFs for segmentation so that the back-propagatable CRFs can use user interactions as hard constraints and all the parameters of potential functions can be trained in an end-to-end way.

### ☺︎ Architecture

<div>
  <img src="./assets/architecture.png" alt=architecture width="80%">
  <p><i><a href="https://arxiv.org/abs/1707.00652" target="_blank" rel="noopener noreferrer">https://arxiv.org/abs/1707.00652</a></i></p>
</div>

Two stage pipeline : P-Net(obtains automatically initial segmentation) + R-Net(refines initial segmentation w/ small # of user interactions that we encode as geodesic distance maps)

- P-Net : use CNN to obtain an **initial automatic segmentation**
- R-Net: refine the segmentation by taking as input **the original image, the initial segmentation and geodesic distance maps** based on foreground/background user interactions.


### ☺︎ CRF-Net

<div>
  <img width="50%" alt="crf-rnn" src="./assets/crf-rnn.png">
  <p><i><a href="https://arxiv.org/abs/1502.03240" target="_blank" rel="noopener noreferrer">https://arxiv.org/abs/1502.03240</a></i></p>
</div>

The CRF-Net(f) is connected to P-Net and also the CRF-Net(fu) is connected to R-Net.
- CRF-Net(f): extend [CRF based on RNN](https://arxiv.org/abs/1502.03240) so that the pairwise potentials can be freeform functions.
- CRF-Net(fu): integrate user interactions in our CRF-Net(f) in the interactive refinement context.
- **But here CRF-Nets are not implemented for simplicity**

### ☺︎ Geodesic Distance Maps

<div>
  <img width="50%" alt="geodesicmap" src="./assets/geodesicmap.png">
  <p><i><a href="https://towardsdatascience.com/preserving-geodesic-distance-for-non-linear-datasets-isomap-d24a1a1908b2" target="_blank" rel="noopener noreferrer">https://towardsdatascience.com/preserving-geodesic-distance-for-non-linear-datasets-isomap-d24a1a1908b2</a></i></p>
</div>

The interactions with the same label are converted into a distance map.
- The euclidean distance treats each direction equally and it does not take the image context into account.
- In contrast, the geodesic distance helps to better differentiate neighboring pixels with different appearances, and improves label consistency in homogeneous regions. 
 

### ☺︎ BraTS Dataset

<div>
  <img width="50%" alt="brats" src="./assets/brats.png">
  <p><i><a href="https://arxiv.org/pdf/2107.02314.pdf" target="_blank" rel="noopener noreferrer">https://arxiv.org/pdf/2107.02314.pdf</a></i></p>
</div>

We only consider T2-FLAIR(panel C) images in the [BraTS 2021](https://arxiv.org/pdf/2107.02314.pdf) and segment the whole tumor.
- The BraTS dataset describes a retrospective collection of brain tumor mpMRI scans acquired from multiple different institutions under standard clinical conditions, but with different equipment and imaging protocols, resulting in a vastly heterogeneous image quality reflecting diverse clinical practice across different institutions. Inclusion criteria comprised pathologically confirmed diagnosis and available MGMT promoter methylation status. These data have been updated, since BraTS 2020, increasing the total number of cases from 660 to 2,000.
