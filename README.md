# HackEDbeta2019Workshop
Getting started with Tensorflow 2 on Raspberry Pi.

The following sources are used to make the workshop example.

Katsuya Hyodo's [PINTO0309 TensorflowLite-bin](https://github.com/PINTO0309/TensorflowLite-bin).


## Outline
* Parts List
* Prepare the Raspberry Pi
    * Used a model 3 B+. Model 4 will also work
* Install TensorFlow Lite 2.0.0

## Parts List
* Raspberry Pi 3 B+
* Pi Camera
* Adafruit 2.8 Capacitive TFT display
* Power supply
* Ethernet cable
* ≥ 16 GB SD card

## Prepare the Raspberry Pi
* Install Raspian "Buster"
    * On the SD card, `$ touch /boot/ssh` to enable headless ssh
    * `$ ssh pi@raspberrypi.local`
    * `sudo raspi-config`
        * set a new password
        * enable the Pi Camera
        * enable SPI
        * update the `raspi-config` tool (from within `raspi-config`)
        * exit 
    * `$ sudo apt update && sudo apt dist-upgrade` # might take a while…
* Install dependencies
    * ```
      $ sudo apt install \
      libatlas-base-dev \
      libjasper-dev \
      libqtgui4 \
      python3-pyqt5 \
      libqt4-test \
      virtualenv
      $ pip3 install numpy
      $ pip3 install picamera
    ```
* [This is needed to see real-time image classification] Install Adafruit 2.8" Capacitive Touch TFT
    * ```
      $ # script is from Adafuit via
      $ # wget https://raw.githubusercontent.com/adafruit/Raspberry-Pi-Installer-Scripts/master/adafruit-pitft.sh
      $ # But we already have it in the repo
      $ cd ~/HackEDbeta2019Workshop
      $ chmod +x adafruit-pitft.sh
      $ sudo ./adafruit-pitft.sh
      ```
        * Choose `PiTFT 2.8" capacitive touch`
        * Choose no console to display
        * Choose to mirror the HDMI display
        
## Install TensorFlow Lite 2.0.0

```
$ sudo apt install swig libjpeg-dev zlib1g-dev python3-dev python3-numpy unzip
$ # Wheel file is from PINTO0309 via
$ # wget https://github.com/PINTO0309/TensorflowLite-bin/raw/master/2.0.0/tflite_runtime-2.0.0-cp37-cp37m-linux_armv7l.whl
$ # But we already have it in the repo
$ sudo pip3 install --upgrade tflite_runtime-2.0.0-cp37-cp37m-linux_armv7l.whl

```

### Test using simple image inference
First set up. We could follow the example from PINTO0309, but for convience the files are already in the report. The only difference is that the directory `test` is renamed `testSimpleInference`. Just for reference, the steps from PINTO0309 are below.

```
$ # Don't do this. All the files are in the directory test already.
$ cd ~;mkdir test
$ curl https://raw.githubusercontent.com/tensorflow/tensorflow/master/tensorflow/lite/examples/label_image/testdata/grace_hopper.bmp > ~/test/grace_hopper.bmp
$ curl https://storage.googleapis.com/download.tensorflow.org/models/mobilenet_v1_1.0_224_frozen.tgz | tar xzv -C ~/test mobilenet_v1_1.0_224/labels.txt
$ mv ~/test/mobilenet_v1_1.0_224/labels.txt ~/test/
$ curl http://download.tensorflow.org/models/mobilenet_v1_2018_02_22/mobilenet_v1_1.0_224_quant.tgz | tar xzv -C ~/test
$ cd ~/test

```
Instead, just `$ cd testSimpleInference`.

Now, run the inference;

```
$ python3 label_image.py --num_threads 4 --image grace_hopper.bmp \
--model_file mobilenet_v1_1.0_224_quant.tflite --label_file labels.txt
```
which will result in something like this,

```
pi@raspberrypi:~/HackEDbeta2019Workshop/testSimpleInference $ python3 label_image.py --num_threads 4 --image grace_hopper.bmp --model_file mobilenet_v1_1.0_224_quant.tflite --label_file labels.txt
INFO: Initialized TensorFlow Lite runtime.
0.415686: 653:military uniform
0.352941: 907:Windsor tie
0.058824: 668:mortarboard
0.035294: 458:bow tie, bow-tie, bowtie
0.035294: 835:suit, suit of clothes
time:  0.08710479736328125

```

### More complicated test
A more sophisticated test from MobileNet V2. Here, multiple objects are identified and bounding boxes are placed around those with high confidence.
The same as the test above, all the files needed are already in the repo.

To run the example,
```
$ cd ~/HackEDbeta2019Workshop/testMobileNetv2
$ python3 mobilenetv2ssd.py
```
which will result in something like this,
```
pi@raspberrypi:~/HackEDbeta2019Workshop/testMobileNetv2 $ python3 mobilenetv2ssd.py 
INFO: Initialized TensorFlow Lite runtime.
time:  0.2111055850982666
[[(124, 231), (315, 544), 0.97265625, 'dog'], [(114, 132), (564, 429), 0.953125, 'bicycle'], [(461, 81), (696, 172), 0.87890625, 'car']]
coordinates: (124, 231) (315, 544). class: "dog". confidence: 0.97
coordinates: (114, 132) (564, 429). class: "bicycle". confidence: 0.95
coordinates: (461, 81) (696, 172). class: "car". confidence: 0.88
```
and an output image named `result.jpg`. Open that image and you should see a car, bicycle, and dog all in bounding boxes.

### A live camera example
This example is taken straight from the [Tensorflow Lite examples](https://tensorflow.org/lite/examples).

Since this just works out of the box, there's no point working in the HackEDbeta2019Workshop directory. 

Change to the home directory or wherever you want to have the examples, and then follow the 
[instructions for image classification](https://github.com/tensorflow/examples/blob/master/lite/examples/image_classification/raspberry_pi/README.md). Since it's
so easy, the steps are repeated here for convenience;

```
$ cd ~
$ git clone https://github.com/tensorflow/examples --depth 1
$ cd examples/lite/examples/image_classification/raspberry_pi
$ bash download.sh /tmp
```
Now run the example;
```
$ python3 classify_picamera.py \
    --model /tmp/mobilenet_v1_1.0_224_quant.tflite \
    --labels /tmp/labels_mobilenet_quant_v1_224.txt
```

Note: `$ bash download.sh /tmp` must be excuted after every boot since `/tmp` may be changed.


## Train Your Own Classifier

At this point we switch back to a Linux box where TensorFlow 2 has been installed. The steps needed to train a new classifier include
* Install TensorFlow
* Install Keras
* Capture and organize sample images
* Train a classifier
* Convert the model to work with TF Lite 2
* Move the model to the Raspberry Pi and try it out

