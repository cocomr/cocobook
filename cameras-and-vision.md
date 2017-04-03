# Cameras and Vision

CoCo provides the interface to various types of Cameras and a series of internal vision based processing that are employed for the tracking and visualization of entities as in Augmented and Mixed Reality Scenarios.

Refer also to the Online Documentation: [http://coco.readthedocs.io/en/latest/computervision/](http://coco.readthedocs.io/en/latest/computervision/)

The Components in this Library are built around the exchange of video frames with the following features:

* Single Images \(type ImageBuffer\)
* Twin Images: e.g. stereo or RGB-D \(type ?\)
* Reference frame: used for the transformations
* Camera calibration info \(type CameraInfo\): used for the calibration

The Images exchanged can be represented via Pixel Formats that comprise the most common without the need to being exhaustive. Pixel Format variability is relevant for reducing the data conversion from sensors, from standard compression libraries and toward given processing types that are interested mainly in some channels of the image.

## Pixel Formats

We support three families of Pixel Formats: RGB, YUV and Depth. In the YUV family we also comprise the Grayscale cases at 8 and 16bit. In the YUV we have only support for I420 in which Y, U and V planes are separates, each 1 byte per pixel but with U and V half the size.

The full listing is the following:

* RGB color: RGB, BGR, RGBA, BGRA
* YUV: I420P, GRAY8,GRAY16
* Depth: DEPTH16, DEPTH32F, INVDEPTH16

## RGB Camera Sources

Overall we can consider the following RGB Camera Sources:

* Core RGB Sources: OpenCV / GStreamer
* Additional Sources \(in this section\)
* Any RGB-D Camera \(see later\)
* ROS imported RGB Camera \(in this section\)
* End-points of the Decompression components \(see later\) 

Two main RGB source are available

* OpenCV based: image\_source
* GStreamer based: gstreamer\_source

The OpenCV source wraps cv::VideoCapture and in the same way allows to specify a string \(imagepath\) or a number \(cameraid\). Example of OpenCV-based source:

```xml
<component>
    <name>imagesrc</name>
    <task>image_source</task>
    <library>cocoar_opencv_camera</library>
    <attributes>
        <attribute name="image_path" value="$(arg image)" type="file"/>
        <attribute name="display" value="0"/>
    </attributes>
</component>
```

Example of GStreamer source with the pipeline specified. Note that "appsink name=sink" is so far required. The output can be RGB, YUV or GRAY8 and it has to be specified manually together with the size \(**\[Dev\]** automatic extraction\).

```xml
<component>
    <name>imagesrc</name>
    <task>GStreamerSource</task>
    <library>cocoar_gstreamer</library>
    <attributes>
        <attribute name="width" value="$(arg width)" />
        <attribute name="height" value="$(arg height)" />                
        <attribute name="gray" value="$(arg gray)" /> 
        <attribute name="pipeline" value="avfvideosrc  ! videoscale ! videoconvert !
         video/x-raw,width=$(arg width),height=$(arg height),format=RGB ! 
          appsink name=sink" unless="$(arg gray)"/>
        <attribute name="pipeline" value="avfvideosrc  ! videoconvert ! 
        video/x-raw,width=$(arg width),height=$(arg height),format=GRAY8 !  
        appsink name=sink" if="$(arg gray)"/>
    </attributes>
</component>
```

Additional we have the following three sources:

* Video 4 Linux Native
* Shared Memory
* **\[Dev\]** libuv for accessing specific cameras under OSX

**\[ROS\] **When using ROS we can read ROS Image sources as CoCo sources, specifying the RGB topic and the CameraInfo topic.

```xml
<component>
    <task>CameraInterfaceTask</task>
    <library>camera_interface</library>
    <attributes>
        <attribute name="rgb_sub_topic" value="/cameras/right_hand_camera/image" />
        <attribute name="camera_info_sub_topic" value="/cameras/right_hand_camera/camera_info" />
    </attributes>
</component>
```



