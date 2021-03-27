# 국토부 모듈 정리

[0. Main 실행코드](#Main-실행코드)

[1. LiDAR 장애물 회피](#LiDAR-장애물-회피)

[2. YOLO](#YOLO)

[3. GPS](#GPS)

[4. Pure Pursuit](#Pure-Pursuit)

[5. usb_cam](#usb_cam)

[6. race(차량제어)](#race(차량제어))

----

## Main 실행코드

- pkg: [**gps**]
- launch: go.launch



## LiDAR 장publish애물 회피

- pkg: [**avoid_obstacle**]
- launch: avoid_abs.launch
  - [**sick_scan**] - sick_lms_1xx.launch
  - [**obstacle_detector**] - nodelets.launch
  - [**avoid_obstacle**] - dynamic_obstacle.py



## YOLO

- pkg: [**darknet_ros**]

- launch: darknet_ros.launch

  - YOLO Config 파일

    - [**darknet_ros**] - yolo_network_config/weights
    - publish[**darknet_ros**] - yolo_network_config/cfg

  - ROS 파라미터 load

    - [**darknet_ros**] - config/ros.yaml

      ```yaml
      publishsubscribers:
        camera_reading:
          topic: /camera/rgb/image_raw
          queue_size: 1
      
      actions:
        camera_reading:
          name: /darknet_ros/check_for_objects
      
      publishers:
        object_detector:
          topic: /darknet_ros/found_object
          queue_size: 1
          latch: false
      
        bounding_boxes:
          topic: /darknet_ros/bounding_boxes
          queue_size: 1
          latch: false
      
        detection_image:
          topic: /darknet_ros/detection_image
          queue_size: 1
          latch: true
      
      image_view:
        enable_opencv: true
        wait_key_delay: 1
        enable_console_output: true
      ```

    - [**darknet_ros**] - config/yolov3.yaml

      ```yaml
      yolo_model:
        config_file:
          name: yolov3.cfg
        weight_file:
          name: yolov3_3.weights
        threshold:
          value: 0.6
        detection_classes:
          names:
            - 3 red
            - 3 yellow
            - 3 green
            - 3 left
            - 4 red
            - 4 green
            - 4 yellow
            - 4 red left
            - 4 left go
            - 4 red yellow
      ```

  - src 위치

    - [**darknet_ros**] - src/yolo_object_detector_node.cpp
    - [**darknet_ros**] - src/YoloObjectDetector.cpppure_pursuit



## GPS

- pkg: [**ublox_gps**]

- src 위치

  - _gps_front_ : [**gps**] - [**ublox_gps**] - src/node.cpp

- config: [**gps**] - [**ublox_gps**] - config/nmea1.yaml

  ```yaml
  rate: 8
  frame_id: gps_front
  device: /dev/ttyGPS1
  
  gnss:
    gps: true
    glonass: true
    galileo : true
    beidou : true
  ```



## Pure Pursuit

- pkg: [**pure_pursuit**]
- src 위치
  - _pure_pursuit_ : [**gps**] - [**pure_pursuit**] - src/pure_pursuit_node.cpp
  - _coordinate2pos_ : [**gps**] - [**pure_pursuit**] - src/coordinate2pos.cpp
    - Subscribe  /utmk_coordinate
    - Publish  /current_pose
  - _wgs84_to_utmk_ : [**gps**] - [**utmk_coordinate**] - src/wgs84_to_utmk.py
    - Subscribe  /gps_front/fix
    - Publish  /utmk_coordinate