# Multi-Scale Continuous CRFs as Sequential Deep Networks for Monocular Depth Estimation
By Dan Xu, Elisa Ricci, Wanli Ouyang, Xiaogang Wang and Nicu Sebe

<p align="center">
  <img src="examples/images/framework.jpg" width="800"/>
</p>

## Introduction
CCRF-CNN is a continuous CRFs model implemented with neural networks for structured fusion of multi-scale predictions which is applied in monocular depth estimation and was accepted at CVPR 2017. </br>
The currently published version implements a multi-scale cascade continuous CRF model. The model is implemented as a CNN layer and can be also applicable to other continuous regression problems. </br>
The code is implemented under Caffe and has been tested under the configurations of Ubuntu 14.04 and CUDA 8.0.</br>
Links: [<a href='https://arxiv.org/pdf/1704.02157.pdf'>CVPR Paper</a>][<a href='https://arxiv.org/abs/1803.00891'>TPAMI Paper</a>][<a href='https://youtu.be/4mdqh6YGhgE'>Oral Presentation</a>]
## Trained Model, Deploy file and Testing Script
To take a practice of our method, please download a trained model (ResNet50 as front-end) on the standard training set and the deploy network file from <a href="https://drive.google.com/open?id=0ByWGxNo3TouJRDFPdWF4UWFubVk">Google Drive</a>.

The structured fusion of multiple Scales of the predictions are performed using the developed MultiStageMeanField Caffe layer together with the continuous mean-field updating implementation. The number of mean-field interations could be specified, and the caffe prototxt definition is as follows:

<pre>##Structured fusion of two scales using Continous-CRF-CNN##
layer {
  name: "inference1" # set the name to be the same as in the training phase to 
  load parameters in the testing phase #
  type: "MultiContinuousMeanfield" bottom: "upscore-map3" bottom: "upscore-map4" bottom: "data" 
  top: "predicted-map1"
  param { lr_mult: 10000 # learning rate for weight of smoothness }
  param { lr_mult: 10000 # learning rate for weight of appearance }
  multi_stage_meanfield_param { num_iterations: 6 theta_alpha: 30 theta_beta: 15 theta_gamma: 3 
  spatial_weight: 3 bilateral_weight: 5 } 
  }
  
##Structured fusion of two scales using Continous-CRF-CNN (the output of inference 1 
will be used as an input scale in this phase)##
layer {
  name: "inference2" #if you set name "inference1", code will load parameters from caffemodel#
  type: "MultiContinuousMeanfield" bottom: "predicted-map1" bottom: "upscore-map5" bottom: "data" 
  top: "predicted-map2"
  param { lr_mult: 10000 # learning rate for weight of smoothness }
  param { lr_mult: 10000 # learning rate for weight of appearance }
  multi_stage_meanfield_param { num_iterations: 6 theta_alpha: 30 theta_beta: 15 theta_gamma: 3 
  spatial_weight: 3 bilateral_weight: 5 } 
  }
  
 ... To stack more for fusing more scales ...</pre>
 
 An example testing script can be found from examples/CCRF-CNN/nyud_test.py.
 
## Results on NYUD-V2
The results with ResNet-50 as a backbone and trained on the standard training set with 795 training images. 
Examples as follows:
<p align="center">
  <img src="examples/images/nyu_results.png" width="750"/>
</p>
Full test results can be directly downloaded from <a href='https://drive.google.com/drive/folders/1urvpMZ-_sVToiowVDFZzDAefJlqlB_oC?usp=sharing'>Here</a>.
 
## Results on Make3D
<p align="center">
  <img src="examples/images/make3D.jpg" width="850"/>
</p>
Results on Make3D test dataset can be downloaded from <a href='https://drive.google.com/drive/folders/12yV3j4h86bR4yXjJuEXFMUzi9YJXO-hB?usp=sharing'>Here</a>. 
The groundtruth depth map can be downloaded from <a href='https://drive.google.com/drive/folders/1FLDWLIu0v3rzlgJyTtyWgVcopQMCj2CR?usp=sharing'>Here</a>.

## Citation
Please consider citing the following papers if the code is helpful in your research work:
<pre>@inproceedings{xu2017multi,
  title={Multi-Scale Continuous CRFs as Sequential Deep Networks for Monocular Depth Estimation},
  author={Xu, Dan and Ricci, Elisa and Ouyang, Wanli and Wang, Xiaogang and Sebe, Nicu},
  booktitle={CVPR},
  year={2017}
}
@article{xu2018monocular,
  title={Monocular Depth Estimation using Multi-Scale Continuous CRFs as Sequential Deep Networks},
  author={Xu, Dan and Ricci, Elisa and Ouyang, Wanli and Wang, Xiaogang and Sebe, Nicu},
  journal={IEEE TPAMI},
  year={2018}
}
</pre>

