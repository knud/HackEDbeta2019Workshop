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
        * update the `raspsi-config` tool (from within `raspi-config`)
        * exit 
    * `$ sudo apt update && sudo apt dist-upgrade` # might take a while…
* Install dependencies
    * ```
   $ sudo apt install \
      libatlas-base-dev \
      libjasper-dev \
      libqtgui4 \
      python3-pyqt5 \
      libqt4-test
    ```
* [This is needed to see real-time image classification] Install Adafruit 2.8" Capacitive Touch TFT
    * ```
      $ cd ~
      $ wget https://raw.githubusercontent.com/adafruit/Raspberry-Pi-Installer-Scripts/master/adafruit-pitft.sh
      $ chmod +x adafruit-pitft.sh
      $ sudo ./adafruit-pitft.sh
      ```
        * Choose `PiTFT 2.8" capacitive touch`
        * Choose no console to display
        * Choose to mirror the HDMI display
        
## Install TensorFlow Lite 2.0.0

```
$ sudo apt install swig libjpeg-dev zlib1g-dev python3-dev python3-numpy unzip
$ wget https://github.com/PINTO0309/TensorflowLite-bin/raw/master/2.0.0/tflite_runtime-2.0.0-cp37-cp37m-linux_armv7l.whl
$ sudo pip3 install --upgrade tflite_runtime-2.0.0-cp37-cp37m-linux_armv7l.whl

```

###Test using simple image inference
First set up;

```
$ cd ~;mkdir test
$ curl https://raw.githubusercontent.com/tensorflow/tensorflow/master/tensorflow/lite/examples/label_image/testdata/grace_hopper.bmp > ~/test/grace_hopper.bmp
$ curl https://storage.googleapis.com/download.tensorflow.org/models/mobilenet_v1_1.0_224_frozen.tgz | tar xzv -C ~/test mobilenet_v1_1.0_224/labels.txt
$ mv ~/test/mobilenet_v1_1.0_224/labels.txt ~/test/
$ curl http://download.tensorflow.org/models/mobilenet_v1_2018_02_22/mobilenet_v1_1.0_224_quant.tgz | tar xzv -C ~/test
$ cd ~/test

```

Now, run the inference;

```
$ python3 label_image.py --num_threads 4 --image grace_hopper.bmp \
--model_file mobilenet_v1_1.0_224_quant.tflite --label_file labels.txt
```
which will result in something like this,

```
pi@raspberrypi:~/test $ python3 label_image.py --num_threads 4 --image grace_hopper.bmp --model_file mobilenet_v1_1.0_224_quant.tflite --label_file labels.txt
INFO: Initialized TensorFlow Lite runtime.
0.415686: 653:military uniform
0.352941: 907:Windsor tie
0.058824: 668:mortarboard
0.035294: 458:bow tie, bow-tie, bowtie
0.035294: 835:suit, suit of clothes
time:  0.08710479736328125

```

### More complicated test
First set up;
```
$ cd ~;mkdir testmobilenetv2
$ cd testmobilenetv2
$ 
```
