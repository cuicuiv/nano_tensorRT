
## Build and Run

### Detection

1. generate .wts from pytorch with .pt, or download .wts from model zoo
```
git clone -b v7.0 https://github.com/ultralytics/yolov5.git
git clone -b yolov5-v7.0 https://github.com/wang-xinyu/tensorrtx.git
cd yolov5/
wget https://github.com/ultralytics/yolov5/releases/download/v7.0/yolov5s.pt
cp [PATH-TO-TENSORRTX]/yolov5/gen_wts.py .
python gen_wts.py -w yolov5s.pt -o yolov5s.wts
# A file 'yolov5s.wts' will be generated.
```

2. build tensorrtx/yolov5 and run

```
cd [PATH-TO-TENSORRTX]/yolov5/
# Update kNumClass in src/config.h if your model is trained on custom dataset
mkdir build
cd build
cp [PATH-TO-ultralytics-yolov5]/yolov5s.wts . 
cmake ..
make

./yolov5_det -s [.wts] [.engine] [n/s/m/l/x/n6/s6/m6/l6/x6 or c/c6 gd gw]  // serialize model to plan file
./yolov5_det -d [.engine] [image folder]  // deserialize and run inference, the images in [image folder] will be processed.

# For example yolov5s
./yolov5_det -s yolov5s.wts yolov5s.engine s
./yolov5_det -d yolov5s.engine ../images

# For example Custom model with depth_multiple=0.17, width_multiple=0.25 in yolov5.yaml
./yolov5_det -s yolov5_custom.wts yolov5.engine c 0.17 0.25
./yolov5_det -d yolov5.engine ../images
```

3. Check the images generated, _zidane.jpg and _bus.jpg

4. Optional, load and run the tensorrt model in Python

```
// Install python-tensorrt, pycuda, etc.
// Ensure the yolov5s.engine and libmyplugins.so have been built
python yolov5_det_trt.py

// Another version of python script, which is using CUDA Python instead of pycuda.
python yolov5_det_trt_cuda_python.py
```

<p align="center">
<img src="https://user-images.githubusercontent.com/15235574/78247927-4d9fac00-751e-11ea-8b1b-704a0aeb3fcf.jpg" height="360px;">
</p>

### Classification

```
# Download ImageNet labels
wget https://github.com/joannzhang00/ImageNet-dataset-classes-labels/blob/main/imagenet_classes.txt

# Build and serialize TensorRT engine
./yolov5_cls -s yolov5s-cls.wts yolov5s-cls.engine s

# Run inference
./yolov5_cls -d yolov5s-cls.engine ../images
```

### Instance Segmentation

```
# Build and serialize TensorRT engine
./yolov5_seg -s yolov5s-seg.wts yolov5s-seg.engine s

# Download the labels file
wget -O coco.txt https://raw.githubusercontent.com/amikelive/coco-labels/master/coco-labels-2014_2017.txt

# Run inference with labels file
./yolov5_seg -d yolov5s-seg.engine ../images coco.txt
```

<p align="center">
<img src="https://user-images.githubusercontent.com/10251537/211291625-1b912483-b6a6-4e92-80c1-434d165b6776.jpg" height="360px;">
</p>

# INT8 Quantization

1. Prepare calibration images, you can randomly select 1000s images from your train set. For coco, you can also download my calibration images `coco_calib` from [GoogleDrive](https://drive.google.com/drive/folders/1s7jE9DtOngZMzJC1uL307J2MiaGwdRSI?usp=sharing) or [BaiduPan](https://pan.baidu.com/s/1GOm_-JobpyLMAqZWCDUhKg) pwd: a9wh

2. unzip it in yolov5/build

3. set the macro `USE_INT8` in src/config.h and make

4. serialize the model and test


## More Information

See the readme in [home page.](https://github.com/wang-xinyu/tensorrtx)

