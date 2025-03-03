<div align="center">
    <img src="docs/NYC_MOCT.png" alt="NYC MOCT Logos" width="250">
    <img src="docs/nyc-dot-logo.png" alt="NYC DOT Logo" width="250">
    <img src="docs/NYU-Emblem.png" alt="NYU CUSP logo" width="250">
</div>


## Abstract

*Data on pedestrian traffic flows and counts can be extremely useful for city planning. Which is why NYC has held a bi-annual pedestrian count in 114 key locations around the city. However this method is very limited, time consuming and expensive. The use of low-power, cost-effective AI chips such as Google’s Coral Edge TPU can reduce cost and flexibility of pedestrian counting. We successfully deployed a pedestrian counting algorithm based on the YOLOv5 object detection model and a SORT algorithm for object tracking on the Google Coral Dev Board. We achieved industry standard rates of accuracy between 85%-95% while deploying in a low-power setting. This proved significant due to the general tradeoff between accuracy and power efficiency. Accuracy was determined by running the model on existing NYC DOT Pedestrian Unit footage with matching count data. By running inference directly on the device we preserve pedestrian privacy and facilitate installation and deployment. This prototype represents an improvement to the current DOT counting process as it vastly improves data granularity (from 15 minute aggregates to anonymized individual tracking), data turnaround (from 4-6 weeks to live data), and future scalability and flexibility. Future work should aim to use transfer learning to train a pedestrian identifier from scratch and expand the use case to include bikes, cars, and micromobile modes of transport.*

## Team Members and Project Website

[Project Website And Dashboard](https://lpcv.netlify.app/)

- Abdulaziz Alaql
- Alec Bardey
- Turbold Baatarchuluu
- Branden DuPont

## Goal

Many City agencies are involved in the use, planning, and design of public space but good data on pedestrian flows can be hard to come by. Manual counts take coordination and planning as well as staff costs. Computer-vision (CV) counting technologies are being tested in the city now but it is already clear that the infrastructure requirements (tapping into electricity and mounting to light poles) will be a limiting factor in using this technology more broadly and particularly for shorter-term studies where the infrastructure investment is not worth the time and effort. A low-cost, battery-powered CV sensor can help fill the data gap and allow agencies to utilize privacy-protected automated counts in short-term deployments with minimal infrastructure requirements.
In recent years, many hardware manufacturers have created development boards that support low-power computer vision (LPCV) applications. In addition, there has also been a fair amount of research done within academia to create low-power models for LPCV. This proposal aims to take advantage of recent technology advances to develop a hardware device that can be battery operated and utilized by New York City agencies to count pedestrians as they move through public space in the city. 
The NYC Department of Transportation Pedestrian Unit currently leads the pedestrian counting program in NYC. Most of the available pedestrian count data is manually counted from videos collected by contractors hired by the DOT. Building a low-power pedestrian counting device that is inexpensive, scalable, and accurate would help cut major costs for this important program. It would also grant horizontal flexibility in deployment at various locations in addition to vertical flexibility in scaling the program. As will be discussed, this work observes a tradeoff between computational efficiency and accuracy. Constructing a device that is efficient and low-power remains the most significant feature of this work. 

## Background

Good data on pedestrian flows can be some of the most difficult yet most important information streams about a city. Computer vision (CV) counting techniques have been tested as a means of automating this data stream. However, there proves to be critical economic and infrastructural limitations in deploying widespread CV counting technologies across a city. Recent developments in hardware have enabled the utilization of low-power computer vision (LPCV) on more mobile devices[^1]. This project aims to construct a low-power computer vision pedestrian counting device that is battery powered, long-lasting (2-4 weeks), and scalable.

In the past few years we have witnessed rapid and widespread shifting of our urban spaces due in large part to an ongoing environmental and epidemiological revolution. Recent literature on computer-vision pedestrian counting has sought to address some of these challenges by exploring mechanisms to automate social distancing requirements, for example.[^8] As cities seek to harden their infrastructure via technology, computer vision pedestrian counting has been used to address potential safety concerns.[^3],[^4],[^7]  Researchers have explored the use of computer vision as a means of pedestrian re-identification over varying distances and periods of time.[^3],[^4],[^7]  Computer vision has also been used to create neural networks as a means of pedestrian detection and counting for a variety of purposes.[^6],[^7] Generally there has been a wide scope of applications of computer vision for pedestrian counting. Some researchers have created labeled datasets through the use of multi-camera joint systems to attain more accurate measurements of pedestrian flow.[^2],[^3] Others have explored pedestrian counting and labeling in different environments and settings, like nighttime and crowded areas.[^5],[^6]

Identification of pre-labelled pedestrian datasets was critical in the early stages of this project for training and testing. The P-DESTRE dataset developed by Kumar et al. (2020) uses Unmanned Aerial Vehicles (UAVs) in a controlled outdoor environment for identification and reidentification of pedestrians.[^4] The Oxford Town Centre dataset is a widely cited dataset spanning over 10 years of implementation in pedestrian identification.[^8],[^11],[^12] It is sourced from a static CCTV in a busy pedestrian friendly plaza. It has 4501 annotated frames, which makes it ideal for training and testing.  

Individual privacy remains a large concern when identification is inherent to the practice. Several papers have addressed privacy concerns by training a model on a video game, Grand Theft Auto V, to fair success.[^3],[^10] Others have deployed schemes to maintain pedestrian privacy directly into their identification model.[^9] Concerns about data privacy anonymization further warrant the use of pre-labeled datasets (either gathered with consent or already anonymized). By conducting inference on the Edge via the Google Coral Dev Board we maintain privacy as machine learning and counting occurs on the device alone. 

For our pedestrian detection algorithm we elected to implement the You Only Look Once (YOLO) algorithm established by Redmon et al. (2016).[^14] The paper that established this methodology, model, and convolutional neural network (CNN) ultimately set forth the commonplace practice of solving the object detection problem as a regression problem predicting spatial bounding boxes with a given class property. There has been a great deal of development into this algorithm specifically. For example, Jee (2021) compared YOLOv3 to YOLOv3-Tiny in pedestrian identification on the Oxford Town Center dataset. The TinyML model was found to be preferable with slightly less accuracy but much greater speed.[^12] This serves as grounded and promising evidence for the use of the YOLO in a TinyML application. This rapidly changing field has seen recent developments in accuracy and speed with YOLOv7.[^13] For this project we elected to use the YOLOv5 framework due to the availability of trained PyTorch and Edge TPU models. 

For a large portion of this project we elected to implement and modify an existing pedestrian identification repository from Mikel Broström titled Real-time multi-camera multi-object tracker using YOLOb5 and StrongSORT with OSNet[^Mikel]. This well-documented repository was fairly simple to implement locally and on the Edge. However, the DeepSORT and later StrongSORT algorithm used for object tracking required an additional CNN. While this improved accuracy of the tracker, the additional neural network and model weights made the overall performance computationally expensive and power intensive. Our use case requires low-power implementation for scalability. Thus, we implemented a variation of the Norfair library from Tryolabs[^Norfair], which used a regular SORT algorithm for object tracking. The SORT algorithm (or Simple and Online Real Time Tracking) was first implemented by Bewley et al. (2017). By implementing a kalman filter and Hungarian Algorithm, SORT achieved comparable accuracy to state of the art models at 20x the speed.[^15] Tracking pedestrians is fundamental to counting them in order to identify unique objects as they enter and exit. 

The weights and class identities we utilized in the Norfair framework are trained on the COCO dataset established by Lin et al. (2014). The COCO dataset contains 91 object classes in 328,000 images. 

We were able to successfully implement the Norfair framework with the YOLOv5 model weights locally and on the Edge via the Google Coral Dev Board. By building a low-power and remote pedestrian counting device, we provide the City with a scalable and efficient means of tracking pedestrians at various intersections.

## Methodology

<div align="center">
    <img src="docs/Blank diagram.png" alt="MLProcess">
</div>

**note that the following clips are from private DOT footage and therefore are blurred to protect individual privacy. Reach out to the NYC DOT Pedestrian Unit if you wish to request access to this footage.*

The pedestrian counter was validated on pedestrian footage provided by the NYC DOT Pedestrian Unit as part of their bi-annual pedestrian counting program. This program has existed since 2007 and counts pedestrians at 114 different locations across NYC. These counts were previously manual counts (typically a DOT employee with a clipboard at an intersection) but their most recent vendor sets up cameras to facilitate counting. The outbreak of COVID-19 reinvigorated interest and funding for pedestrian counting in NYC to track pandemic and economic recovery. The cameras installed by the DOT vendor typically recorded about 7-12 hours of footage in a day. While the location varies they tried to cover both sides of the street with one camera and were placed about 7-10 feet high. Typical counts are aggregated to 15 minute intervals and would take an average of four to six weeks to collect after gathering the pedestrian footage. 
Below are three iterations of our model output and performance spanning work from May 2022 through August 2022. By displaying our initial attempts we aim to elucidate challenges we faced and our means of addressing them.

<div align="center">
    <img src="docs/June.gif" alt="June Progress">
</div>

The first iteration of our model implemented a threshold based counter by modifying an existing object tracker and detector found here[^Mikel]. The output video includes a total count seen in the top left corner of the video above. Counts increase when pedestrians are identified in the zone between the two green lines. This initial attempt proved quite accurate in pedestrian detection and tracking. However, it was inefficient and battery-intensive when deployed on the Google Coral Dev Board. As mentioned in the Background above, both the solid accuracy and inefficiency on the device resulted from the additional Convolutional Neural Network (CNN) required for the StrongSORT algorithm. This algorithm is effective at maintaining previous identifications even when paths cross. Insofar as our project priority was low-power this strategy was abandoned in favor of the traditional SORT algorithm. SORT requires no secondary CNN and makes predictions of new object location based on past detection and object velocity. 

For a great explanation about the distinction between DeepSORT/StrongSORT and SORT read this[^Sort_blog]. 

Other issues with this iteration pertained to our implementation of the threshold counter. Our model suffered when tracking objects between the lines and at the periphery of the video. Whenever there was an occlusion a pedestrian would often be counted as a new individual and artificially increase the count. As far as scalability, this approach would also require a unique threshold for each location, which would impair deployment. 

<div align="center">
    <img src="docs/July.gif" alt="July Progess">
</div>

This iteration of our model saw us shift away from StrongSORT to a traditional SORT algorithm, while continuing to use YOLOv5 for our object detection. From this point forward the model is an implementation of Norfair library from Tryolabs found here[^Norfair]. Only a single CNN (YOLOv5) is used in the model which reduces size, improves speed, and lowers power consumption. Tracking, as mentioned above, uses the SORT algorithm. This involves two key steps: Kalman filter and the Hungarian Algorithm. The Kalman filter uses object velocity to predict its location in a new frame, then updates its beliefs after observing the new position. The Kalman filter is used to measure overlap (Intersection over Union - IOU) between predictions and detections. Then the Hungarian Algorithm helps assign predicted tracks to existing objects/ detections. 

While this output represents an improvement in speed and power consumption over the previous iteration, it still suffers from inaccuracies. This version of the algorithm sometimes counted people in reflections, double counted individuals as they crossed paths, and had steep drop offs in accuracy at the video peripheries. 

<div align="center">
    <img src="docs/August.gif" alt="August Progress">
</div>

This video represents output from our final model iteration. It is a direct progression from the previous implementation. The first major change is in the size of the video. We implemented a coordinate checker to allow the user to crop the input video.[^Coordinate_checker] This permits scalable and flexible implementation as the input video region of interest can change in different deployment scenarios. The crop seen in this video cut out certain occlusions and eliminated detection errors at the image periphery. Furthermore, because fewer pixels are passed for model inference, this iteration yielded improvements in accuracy, power consumption, and speed. It is also easy to notice the increased speed of the output video. This is due to the implementation of a frame rate skip. While every frame is initially captured, inference (detection and tracking) is run on every nth frame (default at 2 but can be changed by user when running model). This greatly improves performance speed without suffering a loss in accuracy. This iteration also involved a slight change to the distance calculation in the Kalman filter predictions. The predictions are now based on frobenius distances instead of euclidean distances. This represents a shift from a vector to matrix distance normalization. 

This version of the algorithm also represents the culmination of parameter tuning. The parameters we optimized were confidence threshold, IOU threshold, and initialization delay. These parameters were validated on existing DOT footage (exact statistics available in Performance section below). Confidence threshold represents the percent confidence of the model in its detections. We found performance to be best when this threshold is set between 0.6 and 0.8. The IOU threshold affected the SORT algorithm’s tracked predictions. Model performance was best when the IOU threshold was set at 0.6. Finally, initialization delay indicated the number of frames to wait before detecting, counting, and tracking a new object. This was significant to avoid detections of reflections, people crossing the video at the periphery, and other misidentifications. The ideal value for the initialization delay was 15. 
We also added in a feature to write the anonymous pedestrian count data to a .csv every x frames (with a default at 50). This prevents data loss and ensures an accurate and live data feed. This saved .csv is passed to the data dashboard for visualization and representation. 

## Performance

**Table 1:** Overall performance accuracy of model on validation footage from DOT
| Location | Samples | Avg. Percent Error | Med. Percent Error |
|:----|:----|:----:|:----:|
|Canal & Baxter|7 15-min videos / 1.75 hours|15.32%|12.05%|
|Canal & Lafayette|7 15-min videos / 1.75 hours (Day & Night)|6.65%|6.64%|
|34th & 75th|7 15-min videos / 1.75 hours (Day & Night)|12.54%|13.69%|

**Table 2:** Power consumption and battery lifetime of different YOLOv5 model weights based on 10,000 mAH battery 
| YOLOv5 Model Image Size | Speed | Power Consumption | Battery Lifetime |
|:----|:----:|:----:|:----:|
|`yolov5s-int8-96_edgetpu.tflite`|19.48 fps|5.026V  0.355A|28.2 hours|
|`yolov5s-int8-192_edgetpu.tflite`|16.24 fps|5.026V  0.360A|28 hours|
|`yolov5s-int8-224_edgetpu.tflite`|13.8 fps|5.032V  0.342A|29.25 hours|
|`yolov5m6-int8-256_edgetpu.tflite`|9.44 fps|5.006V  0.376A|26.58 hours|

**Assume battery lifetime will increase in deployment environment on Google Coral Micro(currently unavailable due to supply chain issues)*

In evaluating Table 2 one may come to the conclusion that the `yolov5s-int8-96_edgetpu.tflite` model weights were best. However, this table does not include accuracy assessments. The small input image size in `yolov5s-int8-96_edgetpu.tflite` and `yolov5s-int8-192_edgetpu.tflite` led to major drop offs in accuracy that rendered the algorithm effectively useless. The accuracy rates observed in Table 2 are from testing with the `yolov5s-int8-224_edgetpu.tflite` model weight. 

The accuracy rates displayed in Table 1 are in line with industry standard accuracy rates of 85%-95%. This range is based on our conversation with the NYC DOT Pedestrian Unit and their previous experience with other vendors conducting object detection and tracking for pedestrian counting. None of these other parties achieved this accuracy on a low-powered device. 

Outside of speed and accuracy, this model represents major improvements to the state of the art in pedestrian counting. First, it is scalable. The deployment of this application on the low-powered Google Coral Dev Board permits easy installation with a removable battery and long periods of inferencing. It is also an immediate upgrade to the DOT’s previous timeline to attain pedestrian counts in four to six weeks. This device is able to provide live counts of pedestrians at a given intersection. Furthermore, as the YOLOv5 model is trained on the expansive COCO dataset there is opportunity for various object counts like cars and bicycles by only slightly changing the deployment call. Finally, the counts are a major improvement in data granularity. Previous DOT counts were aggregated at 15 minute intervals. By consistently updating and appending to .csv, our model achieves nearly live counts and tracks of individual pedestrians while maintaining anonymity.

## Next Steps

This project benefited immensely from the vibrant and open world of computer vision. We encourage anyone to clone this repository, make suggestions, and ask questions. Due to the timeline of this project there remain a variety of elements and features we would love to see in future iterations. 

Primarily, future work should focus on retraining weights specific for the pedestrian counting use case. The object detector used in this project, YOLOv5, is trained on the widely applicable COCO dataset. We may observe improvements in accuracy if the YOLOv5 weights are trained on a specific camera angle and existing relevant footage. This should also implement the state of the art YOLO version (now YOLOv7)[^Yolov7]. The labels generated by this model can be used for effective transfer learning. We would also recommend testing different detector classification algorithms like PoseNet[^PoseNet].

A full .pytorch to edgetpu .tflite model conversion pipeline can be found here.[^TensorRT]

In this project we conducted no post-processing of the collected count data. We encourage downloading of the output_data.csv in the outputs folder to create heat maps of pedestrian locations and tables with explicit tracking and directionality information. This could provide valuable information to any city agency about how the public space is used. Further work may also seek to expand the use case to include bikes and wheelchairs to assess accessibility in the public space. 

Other work should focus on improvements to power consumption. This may include threading the video on input to speed up the model and potentially decrease power consumption (like this[^pyimagesearch] or this[^pythonic-cv]). Future deployments of this algorithm should take place on the most updated Google Coral Dev Board. 

## Deployment Instructions

### Install:

We recommend creating a new conda environment before installation and deployment
```bash
conda create --name LPCV
conda activate LPCV
```

Clone repo and install [requirements.txt](https://github.com/ultralytics/yolov5/blob/master/requirements.txt) in a
[**Python>=3.6.0**](https://www.python.org/) environment

```bash
git clone https://github.com/Puturbold/LPCV-NYU-CAPSTONE.git  # clone
cd LPCV-NYU-CAPSTONE
pip install -r requirements.txt  # install
```

### How to run:  
```sh
python yolov5pedestrian.py --classes 0 --video video_file_path
```

| options| description|
|----|----|
|`--detector_path`|Yolov5 model path. Default is "yolov5m6.pt"|
|`--img_size`|YOLOv5 inference size (pixels). Default is "720"|
|`--conf_thres`|YOLOv5 object confidence threshold. Default is "0.25", pedestrian counter works best between 0.6-0.8|
|`--iou_thresh`|YOLOv5 IOU threshold for NMS. Default is "0.45", pedestrian counter works best at 0.6|
|`--classes`|Filter by class: --classes 0, or --classes 0 2 3, see COCO_classes.txt for full list|
|`--device`|Inference device: "cpu" or "cuda". Default is none, run default for edge deployment|
|`--track_points`|Track points: 'centroid' or 'bbox'. Default is "centroid"|
|`--video`|Put the video path - or 0 for camera. Default is 0, run default for edge deployment|
|`--init_delay`|Detection Initialization Delay -  must be less than hit_counter_max (15). Pedestrian counter works best at 15|
|`--save_frame_rate`|Number of frames between updates to output dataframe|
|`--frame_rate_skip`|Number of frames to skip - 1 indicates no skipping - default 2|

Following key commands only work for Coordinate checker UI
|key|description|
|----|----|
|`ESC`|Exit program|
|`p` or `<space>`|Pause / resume movie|

### Command line examples:  

```sh
python yolov5pedestrian.py --conf_thres 0.6 --iou_thresh 0.6 --classes 0 --video movie.mp4 --init_delay 15
python yolov5pedestrian.py --conf_thres 0.6 --iou_thresh 0.6 --classes 0 --video 0 --init_delay 15
python yolov5pedestrian.py --conf_thres 0.6 --iou_thresh 0.6 --classes 0 --device cuda --video movie.mp4 --init_delay 15 --frame_rate_skip 4
```

### Tuning and Testing Guide:

Tracked parameters log can be accessed by running mlflow server on local machine with following command and following locally hosted url

```sh
mlflow ui
```

Running yolov5pedestrian.py on CUDA device requires an cuda enable computer with required cuda software. Reference NVIDIA installation page https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html

### Video Cropping Guide:

After running yolov5pedestrian.py the coordinate checker window will pop-up. The user must identify the specific region of interest for the pedestrian counter by selecting two points on the video feed. The specific region of interest is dependent on the deployment scenario.

#### Notes on cropped image size and model input weights:
Accuracy of pedestrian counter can be effected by the input video resolution and cropped region of interest (ROI) dimensions. Current .tflite model weight takes input size 256x256, therefore the model will reshape all input images to fit 256x256. During deployment, user should be cautious of input images size that are too large or two small. 

## References

[^1]:Alyamkin, S., Ardi, M., Berg, A. C., Brighton, A., Chen, B., Chen, Y., ... & Zhuo, S. (2019). Low-power computer vision: Status, challenges, and opportunities. IEEE Journal on Emerging and Selected Topics in Circuits and Systems, 9(2), 411-421.

[^2]:Chavdarova, T., Baqué, P., Bouquet, S., Maksai, A., Jose, C., Bagautdinov, T., ... & Fleuret, F. (2018). Wildtrack: A multi-camera hd dataset for dense unscripted pedestrian detection. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 5030-5039).

[^3]:Kohl, P., Specker, A., Schumann, A., & Beyerer, J. (2020). The mta dataset for multi-target multi-camera pedestrian tracking by weighted distance aggregation. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition Workshops (pp. 1042-1043).

[^4]:Kumar, S. A., Yaghoubi, E., Das, A., Harish, B. S., & Proença, H. (2020). The p-destre: A fully annotated dataset for pedestrian detection, tracking, and short/long-term re-identification from aerial devices. IEEE Transactions on Information Forensics and Security, 16, 1696-1708.

[^5]:Jia, X., Zhu, C., Li, M., Tang, W., & Zhou, W. (2021). LLVIP: A Visible-infrared Paired Dataset for Low-light Vision. In Proceedings of the IEEE/CVF International Conference on Computer Vision (pp. 3496-3504).

[^6]:Zhang, L., Shi, M., & Chen, Q. (2018, March). Crowd counting via scale-adaptive convolutional neural network. In 2018 IEEE Winter Conference on Applications of Computer Vision (WACV) (pp. 1113-1121). IEEE.

[^7]:Li, W., Zhao, R., Xiao, T., & Wang, X. (2014). Deepreid: Deep filter pairing neural network for person re-identification. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 152-159).

[^8]:Yang, D., Yurtsever, E., Renganathan, V., Redmill, K. A., & Özgüner, Ü. (2021). A vision-based social distancing and critical density detection system for COVID-19. Sensors, 21(13), 4608.

[^9]:Yang, H., Zhou, Q., Ni, J., Li, H., & Shen, X. (2020). Accurate image-based pedestrian detection with privacy preservation. IEEE Transactions on Vehicular Technology, 69(12), 14494-14509.

[^10]:Fabbri, M., Brasó, G., Maugeri, G., Cetintas, O., Gasparini, R., Ošep, A., ... & Cucchiara, R. (2021). MOTSynth: How Can Synthetic Data Help Pedestrian Detection and Tracking?. In Proceedings of the IEEE/CVF International Conference on Computer Vision (pp. 10849-10859).

[^11]:Benfold, B., & Reid, I. (2011, June). Stable multi-target tracking in real-time surveillance video. In CVPR 2011 (pp. 3457-3464). IEEE.

[^12]:Jee, C. Y. (2021). Social Distancing Detector for Pedestrians Using Deep Learning Algorithms (Doctoral dissertation, Tunku Abdul Rahman University College).

[^13]:Wang, C. Y., Bochkovskiy, A., & Liao, H. Y. M. (2022). YOLOv7: Trainable bag-of-freebies sets new state-of-the-art for real-time object detectors. arXiv preprint arXiv:2207.02696.

[^14]:Redmon, J., Divvala, S., Girshick, R., & Farhadi, A. (2016). You only look once: Unified, real-time object detection. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 779-788).

[^15]:Bewley, A., Ge, Z., Ott, L., Ramos, F., & Upcroft, B. (2016, September). Simple online and realtime tracking. In 2016 IEEE international conference on image processing (ICIP) (pp. 3464-3468). IEEE.

[^16]:Lin, T. Y., Maire, M., Belongie, S., Hays, J., Perona, P., Ramanan, D., ... & Zitnick, C. L. (2014, September). Microsoft coco: Common objects in context. In European conference on computer vision (pp. 740-755). Springer, Cham.

[^Mikel]:Mikel Broström (2022). Real-time multi-camera multi-object tracker using YOLOv5 and StrongSORT with OSNet. https://github.com/mikel-brostrom/Yolov5_StrongSORT_OSNet

[^Norfair]:Joaquín Alori, Alan Descoins, KotaYuhara, David, facundo-lezama, Braulio Ríos, fatih, shafu.eth, Agustín Castro, & David Huh. (2022). tryolabs/norfair: v1.0.0 (v1.0.0). Zenodo. https://doi.org/10.5281/zenodo.6596178 (https://github.com/tryolabs/norfair)

[^Sort_blog]:Daniel Pleus. (March 2022). Object Tracking - SORT and DeepSort. https://www.linkedin.com/pulse/object-tracking-sort-deepsort-daniel-pleus/?trk=pulse-article_more-articles_related-content-card

[^Coordinate_checker]:Yasunori Shimura. (August 2021). X,Y Coordinates checker. https://github.com/yas-sim/coordinates-checker

[^Yolov7]:Wang, Chien-Yao and Bochkovskiy, Alexey and Liao, Hong-Yuan Mark. (2022). Trainable bag-of-freebies sets new state-of-the-art for real-time object detectors. https://github.com/WongKinYiu/yolov7

[^PoseNet]:PoseNet  https://github.com/tensorflow/tfjs-models/tree/master/pose-detection

[^pyimagesearch]:Adrian Rosebrock. (February 2017). Faster video file FPS with cv2.VideoCapture and OpenCV https://pyimagesearch.com/2017/02/06/faster-video-file-fps-with-cv2-videocapture-and-opencv/

[^pythonic-cv]:Pythonic-cv https://github.com/ES-Alexander/pythonic-cv

[^TensorRT]:Yolov5 tensorRT export https://github.com/ultralytics/yolov5/issues/251