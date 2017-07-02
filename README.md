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
+ ~output_topic(str) : Broadcast Output Video, if provided. eg. /image_recorder/image_raw

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

    <param name="output_topic" value="$(arg video_topic)" />

    <param name="source1" value="/videofile/image_raw,0,0,320,240" />         <!-- left-top -->
    <param name="source2" value="/openpose/image_raw,320,0,320,240" />        <!-- right-top -->
    <param name="source3" value="/deepdrone/facetrack_img,0,240,320,240" />   <!-- right-bottom -->

</node>

<node name="image_view_grid" pkg="image_view" type="image_view" respawn="false" output="screen" required="true">
    <remap from="image" to="$(arg video_topic)"/>
    <param name="autosize" value="true" />
</node>
```

![3x3 grid sample - not same size](/samples/3x3grid_multi_size.png)

```xml
<node name="video_recorder" pkg="video_recorder" type="recorder.py" output="screen" required="true">

    <param name="output_width" type="int" value="640" />
    <param name="output_height" type="int" value="480" />
    <param name="output_path" value="/workdir/result.avi" />

    <param name="output_topic" value="$(arg video_topic)" />

    <param name="source1" value="/videofile/image_raw,0,0,640,480" />         <!-- left-top with big size(640x480) -->
    <param name="source2" value="/openpose/image_raw,640,0,320,240" />        <!-- right-top (320x240) -->
    <param name="source3" value="/deepdrone/facetrack_img,640,240,320,240" />   <!-- right-bottom (320x240) -->
    <param name="source4" value="/deepdrone/tracking_img,0,480,320,240" />    <!-- left-bottom (320x240) -->

</node>

<node name="image_view_grid" pkg="image_view" type="image_view" respawn="false" output="screen" required="true">
    <remap from="image" to="$(arg video_topic)"/>
    <param name="autosize" value="true" />
</node>
```
