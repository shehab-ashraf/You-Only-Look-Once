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
There are more than one  term in the loss function and all of these losses are going to be sum-squared-error :

1. midpoint loss, we take the midpoint x-value subtracting with the predicted and taking that squared plus the y-value minus 
   y-predicted and again taking that squared and we will do that if there's and object in the cell i and also if the bounding box j if responsible for outputting that
   bounding box.
   
   ![image](https://github.com/shehab-ashraf/You-Only-Look-Once/assets/61033121/34246f23-5601-48dc-bdd1-5bbc0b4e89c9)

2. for the width and hight losses we do the same thing pretty much, we have the width minus the width prediction squared the only difference here is
   that we have the square root, let's say we have a very large bounding box and we take those subtract and we take it squared for very large
   bounding boxes that squared is going to become very large but for small bounding boxes the losses not going to be as large if you would have
   a large bounding box so, what they do is that they take the square root to make sure that we prioritize smaller bounding boxes equally much
   as we do for large bounding boxes.

   ![image](https://github.com/shehab-ashraf/You-Only-Look-Once/assets/61033121/f234f30a-83d9-47ae-88a6-f8e5d521a5c4)


3. probability that there's a box in the cell.

   ![image](https://github.com/shehab-ashraf/You-Only-Look-Once/assets/61033121/edc511bf-6c2c-42b9-a91f-124d50efddaf)

4. if no object in a cell.

   ![image](https://github.com/shehab-ashraf/You-Only-Look-Once/assets/61033121/cb882747-8f9f-4eaa-a69d-ced6efae7b85)

5. classes loss to make sure that we actually can say what object is in this cell. we do this for each cell, we look if there is an object here
   and then go through each class and again we take the sum-squared-error.

   ![image](https://github.com/shehab-ashraf/You-Only-Look-Once/assets/61033121/ca48cf33-b6cc-4a83-a421-5957f2930c8b)
   

#### Results
<img src="https://github.com/shehab-ashraf/You-Only-Look-Once/assets/61033121/16643c15-5900-49aa-b994-524ff37e12e1" width="200" />
<img src="https://github.com/shehab-ashraf/You-Only-Look-Once/assets/61033121/c864c6bc-8589-497f-b209-e9660d339f2d" width="200" />
<img src="https://github.com/shehab-ashraf/You-Only-Look-Once/assets/61033121/49b6c05d-c9ea-4553-9236-d82cd762ebb2" width="200" />



#### Limitations of YOLO
YOLO can only predict a limited number of bounding boxes per grid cell, 2 in the original research paper. And though that number can be increased, only one class prediction can be made per cell, limiting the detections when multiple objects appear in a single grid cell. Thus, it struggles with bounding groups of small objects, such as flocks of birds, or multiple small objects of different classes.



#### Reference
1. [2016 CVPR] [YOLOv1] [You Only Look Once: Unified, Real-Time Object Detection](https://arxiv.org/abs/1506.02640)
