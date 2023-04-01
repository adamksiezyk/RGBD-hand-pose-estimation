# RGBD-hand-pose-estimation

### Abstract

The aim of the project is to estimate hand pose. The available data consists of RGB images and RGB-D images. The latter are created by adding an additional channel to the RGB image that provides information about the distance of the object from the camera. This approach was used to clearly mark the position of the hand in the image without the need for additional data segmentation.

The hand pose estimation on RGB-D images was implemented by detecting the positions of fingertips, also known as keypoints. A neural network was used for this purpose, trained using supervised techniques with RGB-D images and previously marked characteristic points.

In the estimation on RGB images, the detection of key points on the hand, including the wrist and various finger elements, was also used. In both cases, Hourglass-type networks were used.

### Dataset

The EgoDexter dataset was chosen as the data set. It is a collection of images with annotated characteristic points. The authors of the dataset are scientists from the Department of Computer Science at the Max Planck Institute in Germany.

The set consists of 1485 RGB images with dimensions of 480x640, the same number of depth images, and saved reference positions of fingertip points. The images were captured using an Intel RealSense SR300 camera. It has two lenses for recording color and depth channels. The lenses are slightly shifted relative to each other, resulting in different angles of view for RGB and depth data. To compensate for this shift, a camera matrix was combined. The fingertip position is saved for the depth channel. The effect of the shift is visible in Figure 1.

![Fig1](images/rgb_depth_kps.jpg?raw=true "Fig. 1 Keypoints marked on RGB and depth image")

The entire available dataset was divided into training, validation, and test sets in the proportions presented in Table 1.

### Data transformation

To transform the RGB image to match the depth camera perspective a camera matrix transformation is used.

$H = K_d T_c^{-1} R_c^{-1} K_c^{-1}$

$X_d = H X_c$

where:
- $R$ - rotation matrix
- $T$ - translation matrix
- $K_c$ - RGB camera matrix
- $K_d$ - depth camera matrix

![Fig2](images/rgb_to_depth_transform.jpg?raw=true "Fig. 2 RGB, depth and keypoints overlayed before and after transformatino")

### Neural Network architecture

The chosen network model is a simple version of the Hourglass network. The layers and structure of the network are presented in figure 4.
The model was compiled with a loss function that utilizes mean squared error and with the RMSprop optimizer.

![Fig4](images/model.png?raw=true "Fig. 4 Neural Network architecture")

### Results

At the model output, we expect a tensor with dimensions of Nx480x640x5. The first dimension represents the number of images, the next two represent the resolution, and the last dimension corresponds to the number of fingers. This means that we will get one image for each finger with the resolution of the original image and a highlighted heat map near the fingertip.

Very good results have been achieved. The estimation of finger positions is very accurate. Additionally, the model returns regular heat maps from which the correct coordinates can be read directly.

An example evaluation is presented in Figure 3.

![Fig3](images/results.jpg?raw=true "Fig. 3 Estimated keypoints heatmaps")
