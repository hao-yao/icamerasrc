# icamerasrc

This repository supports MIPI cameras through the IPU6/IPU6EP/IPU6SE on Intel Tigerlake/Alderlake/Jasperlake platforms. There are 4 repositories that provide the complete setup:

- https://github.com/intel/ipu6-drivers - kernel drivers for the IPU and sensors
- https://github.com/intel/ipu6-camera-bins - IPU firmware and proprietary image processing libraries
- https://github.com/intel/ipu6-camera-hal - HAL for processing of images in userspace
- https://github.com/intel/icamerasrc/tree/icamerasrc_slim_api (branch:icamerasrc_slim_api) - Gstreamer src plugin

## Content of this repository:
* gstreamer src plugin 'icamerasrc'

## Build instructions:
* Prerequisites: ipu6-camera-bins and ipu6-camera-hal installed 
* Prerequisites: libdrm-dev libva-dev libgstreamer-plugins-bad1.0-dev

```sh
export CHROME_SLIM_CAMHAL=ON
./autogen.sh
./configure --prefix=/usr
make
# binary install
sudo make install
# build rpm package and then install
make rpm
rpm -ivh --force --nodeps icamerasrc-*.rpm
```

NOTE:
For gstreamer version > 1.22.0, add support for drm dma buffer.
It depends on libdrm, please install it when build icamerasrc
and add option "--enable-gstdrmformat=yes" to enable drm dma buffer.
```sh
sudo apt install libdrm-dev
./configure --prefix=/usr --enable-gstdrmformat=yes
```

## Pipeline examples
* Ensure `${GST_PLUGIN_PATH}` includes icamerasrc installation path
* Testpattern generator (no sensor)
```
sudo -E gst-launch-1.0 icamerasrc device-name=tpg_ipu6 ! video/x-raw,format=YUY2,width=1280,height=720 ! videoconvert ! ximagesink
```

* For laptops with 11th Gen Intel Core Procesors (with ov01a1s/hm11b1 sensor):
```
sudo -E gst-launch-1.0 icamerasrc buffer-count=7 ! video/x-raw,format=YUY2,width=1280,height=720 ! videoconvert ! ximagesink
```

* For laptops with 12th/13th Gen Intel Core Procesors (with ov01a10/ov02c10/ov2740/hm2170/hi556 sensor):
```
sudo -E gst-launch-1.0 icamerasrc buffer-count=7 ! video/x-raw,format=NV12,width=1280,height=720 ! videoconvert ! ximagesink
```

* Sensor ov8856
```
sudo -E gst-launch-1.0 icamerasrc device-name=ov8856-wf af-mode=2 ! video/x-raw,format=YUY2,width=1280,height=720 ! videoconvert ! ximagesink
```

* Sensor ov13b10
```
sudo -E gst-launch-1.0 icamerasrc device-name=ov13b10-wf af-mode=2 ! video/x-raw,format=NV12,width=1280,height=720 ! videoconvert ! ximagesink
```

* Sensor ov13858
```
sudo -E gst-launch-1.0 icamerasrc device-name=ov13858-uf af-mode=2 ! video/x-raw,format=NV12,width=1280,height=720 ! videoconvert ! ximagesink
```

* Sensor ar0234
```
sudo -E gst-launch-1.0 icamerasrc device-name=ar0234 ! video/x-raw,format=NV12,width=1280,height=960 ! videoconvert ! glimagesink
sudo -E gst-launch-1.0 icamerasrc device-name=ar0234 io-mode=dma_mode ! 'video/x-raw(memory:DMABuf),drm-format=NV12,width=1280,height=960' ! glimagesink
```
