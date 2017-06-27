# ros-image-recorder

Image Record Package for ROS.

## Why is this ros package special?
 
1. Images shouldn't have to be published in a precise interval.
  + If you have a node which computes heavily using image, drawing results on a input image might be delayed.
  + Images can be Published in a different fps with output video.
  + Images can be published in a variable interval.

2. Multiple image topics can be merged in a grid view.
  + Log your robot's data in multiple video files can be frustrating, if you have a multiple cameras and multiple calculation nodes.
  + Publishing Interval(fps) can be different for camera nodes.

3. etc.

## image_recorder node

This node use opencv to save video, subscribe multiple image-publishing nodes.

### Parameters

+ ~output_width(int: default 640) : Output video width.
+ ~output_height(int: default 480) : Output video height.
+ ~output_fps(int: default 30) : Output video fps(frame per seconds).
+ ~output_format(str: default xvid) : Output video format in fourcc format. See [FourCC Identifier](https://www.fourcc.org/codecs.php)
+ ~output_path(str) : Output Video File Path. eg: /home/ildoonet/Documents/video.mp4
  + [timestamp] : will be replaced with real timestamp. eg. grid_[timestamp].mp4 ---> grid_20170626_185926.mp4

+ ~source1(str) : Incoming Video Frame Info in Format(topic,target_x,target_y,target_w,target_h)
  + eg: "/cv_camera/image_raw,0,0,320,240"
  + Source image will be resized automatically at target_w, target_h
  + Resized image will be pasted at the position of (target_x, target_y)
  + ~source{n} : n is {1, 2, 3, ...}.

### Examples

#### 2 by 2 Grid with 3 Camera/Image Topics

![2x2 grid sample](/samples/2x2grid.png)

```xml
<node name="video_recorder" pkg="video_recorder" type="recorder.py" output="screen" required="true">

    <param name="output_width" type="int" value="640" />
    <param name="output_height" type="int" value="480" />
    <param name="output_path" value="/workdir/result.avi" />

    <param name="source1" value="/videofile/image_raw,0,0,320,240" />         <!-- left-top -->
    <param name="source2" value="/openpose/image_raw,320,0,320,240" />        <!-- right-top -->
    <param name="source3" value="/deepdrone/facetrack_img,0,240,320,240" /> . <!-- right-bottom -->

</node>
```

### Tips

+ Install H264 Codec for Ubuntu
 
```
$ sudo apt-get install ubuntu-restricted-extras
$ sudo apt-get install libavcodec54 libav-tools ffmpeg
```
