## Introduction'

I am sharing my learnings in the area of real-time object detection, combining NVIDIA DeepStream's robust streaming capabilities with the state-of-the-art Ultralytics YOLOv8 model. 
The core of this application is a GStreamer pipeline, selected for its flexibility and strength in handling complex video data streams. This pipeline integrates a series of critical GStreamer plugins: 
- **Integration of GStreamer Plugins**:
  - Plugins used: 
    - **uridecodebin**: For decoding various media formats.
    - **streammux**: For managing multiple stream inputs.
    - **nvinfer**: For running inference using TensorRT.
    - **nvosd**: For On-Screen Display (OSD) capabilities.
    - **tiler**: For composing a 2D tile layout from various input sources.
    - **eglsink**: For rendering output to a screen using EGL.

Each plugin is carefully chosen and orchestrated to manage video data flow efficiently, maximizing resource utilization and processing speed.


The initial step involves converting YOLOv8 weights into a binary format and the custom parser converts it into a tensorrt engine file. 
It processes the raw output of the YOLO model, decodes the detection tensors, and formats the results into a structure that DeepStream can use for further processing, such as overlaying bounding boxes on the video stream or performing additional analytics.


## Repository Setup

### Step 1: Install Git

```sh
sudo apt install git
```

```sh
https://github.com/Aasthaengg/deepstream6.1_ultralytics.git
```

## Download the model files

You can run the `download-models.sh` script to download `onnx` yolov8 models

```sh
cd models
chmod 0777 download-models.sh
./download-models.sh
```
## Docker image build

This repository is tested and used with `docker`. To setup docker with `nvidia`, [click here](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html).

### 1. Build the image

```sh
cd docker/dgpu

docker build . -t ds-6.1:custom-1
```

### 2. Start a container

```sh
xhost +

export DISPLAY=:0

docker run -it --rm --net=host --runtime nvidia -e DISPLAY=$DISPLAY -v /tmp/.X11-unix/:/tmp/.X11-unix -v /home/aastha/deepstream6.1_ultralytics:/app ds-6.1:custom-1
```

## Running the Application

### 1. Build the application

```sh
make clean && make -j$(nproc)
```

### 2. Start the application


Next, create a file called `inputsources.txt` and paste the path of videos or rtsp url.

```sh
file:///home/aastha/Videos/input_video.mp4
```

Running the application :

```sh
# For barebones
./ds-yolov8

# For system76 hybrid graphics
__NV_PRIME_RENDER_OFFLOAD=1 ./ds-yolov8
```

## Citations

* [ultralytics/ultralytics](https://github.com/ultralytics/ultralytics)