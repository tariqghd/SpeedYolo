# OpenISS keras-yolo3
[![DOI](https://zenodo.org/badge/doi/10.5281/zenodo.5042269.svg)](https://doi.org/10.5281/zenodo.5042269)

This repo is a clone from [qqwweee/keras-yolo3](https://github.com/qqwweee/keras-yolo3) hosted on GitHub, with modification to adjust to person re-identification
model only (from YOLOv3 object detection model). The prediction code is obtained from this [repo](https://github.com/gustavovaliati/keras-yolo3) and adapted to
current implementation. The result figure is generated by using the code ported from this [repo](https://github.com/Cartucho/mAP).

## Introduction

A Keras implementation of YOLOv3 (Tensorflow backend) inspired by [allanzelener/YAD2K](https://github.com/allanzelener/YAD2K).

This is a part of the Eric Lai's ML portion of the [OpenISS](https://github.com/OpenISS/OpenISS) project for his
master's thesis:

* [Haotao Lai](https://github.com/laihaotao), [*An OpenISS Framework Specialization for Person Re-identification*](https://spectrum.library.concordia.ca/985788/), Master's thesis, August 2019, Concordia University, Montreal

---

## Environment 
 Images and videos can be from any source, but we provided you with a video and images sample in folders video and image. 
 This demo will run in an interactive way for both image and video.
 1) For image you provide the server with image path/name and return the classes of images.
 2) For video you will provide video path/name in the script argument and get a live video that dispaly object in each frame and finally the code will generate a new classified video stored in your video folder.  


## Speed Login Configuration 
1. As interactive login is requred in this project that show you live video you need to enable ssh login with -X support. Please check this [link](https://www.concordia.ca/ginacody/aits/support/faq/xserver.html) to do that.
2. If you didn't know how to login to speed and prepare working environment please check the manual in the follwing [link](https://github.com/NAG-DevOps/speed-hpc/blob/master/doc/speed-manual.pdf) section 2.

After you loged in change your working directory to `/speed-scratch/$USER` diectory.
```
cd /speed-scratch/$USER/
```

## Development Environment Preperation. 
The pre-requisites to prepare the virtual development environment using anaconda is located in *environment.yml*. You can check [speed manual](https://github.com/NAG-DevOps/speed-hpc/blob/master/doc/speed-manual.pdf) section 3 for more inforamtion.
1. Make sure you are in speed-scratch directory. Then Download Yolo project from [Github website](https://github.com/tariqghd/SpeedYolo) to your speed-scratch proper diectory. 
```
cd /speed-scratch/$USER/
git clone https://github.com/tariqghd/SpeedYolo.git
```
2. Starting by loading anaconda module 
```
module load anaconda/default
```
3.  First switch to the project directoy. Create anaconda virtual environment, and configure development librires. The name of the environment can by any name here as an example named YOLOInteractive. Activate the conda environment YOLOInteractive.
<!-- 
conda configuration will be from the .yml file.
conda env create -f environment.yml -p /speed-scratch/$USER/YOLO 
conda deactivate
conda env remove -p /speed-scratch/$USER/YOLO
-->

```
cd /speed-scratch/$USER/SpeedYolo
conda create -p /speed-scratch/$USER/YOLOInteractive
conda activate /speed-scratch/$USER/YOLOInteractive
```
4. Install all required librires you need to upgrade pip and install opencv-contrib-python library 

```
conda install python=3.5
conda install tensorflow-gpu=1.10.0
conda install Keras=2.1.5
conda install Pillow
conda install matplotlib
conda install -c menpo opencv
pip install --upgrade pip 
pip install opencv-contrib-python

```

5. Validate conda environemnt and installed packeges using following commands. Make sure the version of python and keras are same as requred.
```
conda info --env
conda list
```
if you need to delete the created virtual environment 
```
conda deactivate
conda env remove -p /speed-scratch/$USER/YOLOInteractive
```

## Quick Start

1. Make sure you are inside the project directoy 
```
 cd /speed-scratch/$USER/SpeedYolo/
```
2. Download YOLOv3 weights from [YOLO website](http://pjreddie.com/darknet/yolo/).
```
wget https://pjreddie.com/media/files/yolov3.weights
```
2. Convert the Darknet YOLO model to a Keras model.
```
python convert.py yolov3.cfg yolov3.weights model_data/yolo.h5
```
3. Run YOLO detection, using exsiting samples.
```
python yolo_video.py [OPTIONS...] --image, for image detection mode, OR
python yolo_video.py [video_path] [output_path (optional)]
```
For image run follwing command and the program will ask you about the image path and name:
```
python yolo_video.py --model model_data/yolo.h5 --classes model_data/coco_classes.txt --image 
```
For video 
```
python yolo_video.py --input video/v1.avi --output vido/001.avi
```
## Run Script 
File `yolo_submit.sh` is the speed script to run video example to run it you follow these steps:
1. Since this job is an interactive job we need to keep `ssh -X` option enabled and `xming` server in your windows  working. 
2. The `qsub` is not the proper command since we have to keep direct ssh connection to the computational node, so `qlogin` will be used. 
3. Enter `qlogin` in the `speed-submit`. The `qlogin` will find an approriate  computational node then it will allow you to have direct `ssh -X' login to that node. Make sure you are in the right directory and activate conda environment again.

```
qlogin 
cd /speed-scratch/$USER/SpeedYolo
conda activate /speed-scratch/$USER/YOLOInteractive
```
4. Before you run the script you need to add permission access to the project files, then start run the script `./yolo_submit.sh`    
```
chmod +rwx *
./yolo_submit.sh
```
5. A pop up window will show a classifed live video. 

Please note that since we have limited number of node with GPU support `qlogin` is not allowed to direct you to login to these server you will be directed to the availabel computation nodes in the cluster with CPU support only. 


For Tiny YOLOv3, just do in a similar way, just specify model path and anchor path with `--model model_file` and `--anchors anchor_file`.

## performance comparison 
Time is in seconds, run Yolo inactive session with different hardware configurations. 

|    1GPU       |    2GPU       |    32CPU       |
| ------------- | ------------- |----------------|
|   17.15       |   23.33       |     60.42      |
|   17.54       |   14.21       |     60.18      |
|   17.18       |   17.25       |     60.47      |

### Usage
Use --help to see usage of yolo_video.py:
```
usage: yolo_video.py [-h] [--model MODEL] [--anchors ANCHORS]
                     [--classes CLASSES] [--gpu_num GPU_NUM] [--image]
                     [--input] [--output]

positional arguments:
  --input        Video input path
  --output       Video output path

optional arguments:
  -h, --help         show this help message and exit
  --model MODEL      path to model weight file, default model_data/yolo.h5
  --anchors ANCHORS  path to anchor definitions, default
                     model_data/yolo_anchors.txt
  --classes CLASSES  path to class definitions, default
                     model_data/coco_classes.txt
  --gpu_num GPU_NUM  Number of GPU to use, default 1
  --image            Image detection mode, will ignore all positional arguments
```
---

4. MultiGPU usage: use `--gpu_num N` to use N GPUs. It is passed to the [Keras multi_gpu_model()](https://keras.io/utils/#multi_gpu_model).

## Run script on speed  


## Training

1. Generate your own annotation file and class names file.
    One row for one image;
    Row format: `image_file_path box1 box2 ... boxN`;
    Box format: `x_min,y_min,x_max,y_max,class_id` (no space).
    For VOC dataset, try `python voc_annotation.py`
    Here is an example:
    ```
    path/to/img1.jpg 50,100,150,200,0 30,50,200,120,3
    path/to/img2.jpg 120,300,250,600,2
    ...
    ```

2. Make sure you have run `python convert.py -w yolov3.cfg yolov3.weights model_data/yolo_weights.h5`
    The file model_data/yolo_weights.h5 is used to load pretrained weights.

3. Modify train.py and start training.
    `python train.py`
    Use your trained weights or checkpoint weights with command line option `--model model_file` when using yolo_video.py
    Remember to modify class path or anchor path, with `--classes class_file` and `--anchors anchor_file`.

If you want to use original pretrained weights for YOLOv3:
    1. `wget https://pjreddie.com/media/files/darknet53.conv.74`
    2. rename it as darknet53.weights
    3. `python convert.py -w darknet53.cfg darknet53.weights model_data/darknet53_weights.h5`
    4. use model_data/darknet53_weights.h5 in train.py

---

## Some issues to know

1. The test environment is
    - Python 3.5.2
    - Keras 2.1.5
    - tensorflowgpu 1.10.0

2. Default anchors are used. If you use your own anchors, probably some changes are needed.

3. The inference result is not totally the same as Darknet but the difference is small.

4. The speed is slower than Darknet. Replacing PIL with opencv may help a little.

5. Always load pretrained weights and freeze layers in the first stage of training. Or try Darknet training. It's OK if there is a mismatch warning.

6. The training strategy is for reference only. Adjust it according to your dataset and your goal. And add further strategy if needed.

7. For speeding up the training process with frozen layers train_bottleneck.py can be used. It will compute the bottleneck features of the frozen model first and then only trains the last layers. This makes training on CPU possible in a reasonable time. See [this](https://blog.keras.io/building-powerful-image-classification-models-using-very-little-data.html) for more information on bottleneck features.
