# You-Only-Look-Once

In object detection we identify the category of the object along with the locations are represented by bounding boxes and the category information is represented by probability scores.
there are a few object detection algorithms before YOLO like R-CNN and faster R-CNN but these are two stage networks they generate the bounding boxes first and then they classify the boxes
in the second stage, there are many drawbacks for this approach one being a multi-stage approach and each component is trained separately, these networks are very complex and not useful for real-time applications.
![image](https://github.com/shehab-ashraf/You-Only-Look-Once/assets/61033121/ce1785b3-26e9-4f4a-b90e-9030f3f4f539)


YOLO, a new approach to object detection. A single neural network predicts bounding boxes and class probabilities directly from full images in one evaluation. YOLO reframes the object detection problem as a single stage regression problem (all losses we are calculating here are regression losses).

![image](https://github.com/shehab-ashraf/You-Only-Look-Once/assets/61033121/e66cdafd-1fe9-4d31-ba51-afb29b636c15)

The input image whatever the resolution you are having this image will be resized into 448x448 this number is fixed depending on the model architecture choice. Now this image is divided into SxS grid , in the paper they selected 7, so the image is getting divided into 7*7. Each cell in this grid is responsible for predictin one object, but how we know which cell is responsible for detection such object ? If the center of an object falls into a grid cell, that grid cell is responsible for detecting that object. 

Each grid cell predicts B bounding boxes (B=2) and confidence scores for those boxes. These confidence scores reflect how confident the model is that the box contains an object, i.e. any objects in the box, P(Objects).

`Each bounding box consists of 5 predictions: x, y, w, h, and confidence.`

* The (x, y) coordinates represent the center of the box relative to the bounds of the grid cell.
* The width w and height h are predicted relative to the whole image.
* The confidence represents the Intersection Over Union (IOU) between the predicted box and any ground truth box.
  Each grid cell also predicts conditional class probabilities, P(Classi|Object). (Total number of classes=20)

![image](https://github.com/shehab-ashraf/You-Only-Look-Once/assets/61033121/397dec0f-865b-426f-a320-d57c4aa2d36f)


#### Network Architecture
detection network has 24 convolutional layers followed by 2 fully connected layers. Alternating 1 × 1 convolutional layers reduce the features space from preceding layers.

Except for the final layer, all other layers use leaky ReLU as activation function. First 20 convolutional layers are pretrained by ImageNet to have 88% top-5 accuracy for a week. A batch size of 64 is used. Dropout at the first fully connected layer and data augmentation are also used.

![image](https://github.com/shehab-ashraf/You-Only-Look-Once/assets/61033121/a7fc5ff9-1f9a-493b-bfea-097aa488c84c)

#### Loss Function
There are more than one  term in the loss function

![image](https://github.com/shehab-ashraf/You-Only-Look-Once/assets/61033121/7e5b9fc6-042c-46b9-9714-61cd34eb9897)

1. (x, y): The bounding box x and y coordinates is parametrized to be offsets of a particular grid cell location so they are also bounded between 0 and 1. And the sum of square 
   error (SSE) is estimated only when there is object.
2. (w, h): The bounding box width and height are normalized by the image width and height so that they fall between 0 and 1. SSE is estimated only when there is object. Since small 
   deviations in large boxes matter less than in small boxes. square root of the bounding box width w and height h instead of the width and height directly to partially address this 
   problem.
3. (The confidence): the IOU between the predicted box and any ground truth box
4. (Class Probabilities): SSE of class probabilities when there is objects.

#### Reference
1. [2016 CVPR] [YOLOv1] [You Only Look Once: Unified, Real-Time Object Detection](https://arxiv.org/abs/1506.02640)
