# RNSA

This is a shortened selection of the notebooks I used to generate my submission to the Kaggle RNSA pneumonia detection challenge.

Competition page - 
https://www.kaggle.com/c/rsna-pneumonia-detection-challenge

I achieved a score of 1.50 in both stages 1 and 2 with no retraining between stages on this competition in its unique metric.  For reference the rank 1 score was 2.55 and the best public kernel (prior to competiton end) was at around 1.64 - a imagenet pretrained fine tuned Mask-RCNN model.  

For a single-shot one stage non-ensembled model I was pretty happy with this performance, although there is clearly a lot of room for improvement.

## Model

The model I used was using a simple U-Net encoder decoder architecture as the skeleton design and then iteratively swapping out modules to improve performance.  The final design elements I incorporated are

1. Vortex Pooling (1)
2. Spacial and Channel Wise Squeeze and Excitation (2)
3. Dense Blocks (3)
4. Inception Downscaling Blocks (4)
5. eLU Activation Functions (5)

The model was using downscaled images to 256x256 with gaussian noise (early training only), gaussian blur (early training only), rotation, horizontal flips as the image distortions used for regularization.  It was initialized using he normal initialization (6) and trained with exponential learning rate annealing (0.8^n) where n refers to the epoch number.  This was trained for approximately 20 hours on a Nvidia GTX1080 (8Gb GRAM).  This model was implemented in Keras with a Tensorflow-gpu backend.

### Model Architecture

1. Vortex Pooling Block
2. Inception Downscaling Block
3. Dense Block (3 layers)
4. Inception Downscaling Block
5. Dense Block (4 layers)
6. Inception Downscaling Block
7. Dense Block (5 layers)
8. Inception Downscaling Block
9. Dense Block (6 layers)
10. Transposed Convolution Upscaling
11. Dense Block (5 layers)
12. Transposed Convolution Upscaling
13. Dense Block (4 layers)
14. Transposed Convolution Upscaling
15. Dense Block (3 layers)
16. Vortex Pooling Block
17. 1x1 Convolution Single Filter + Sigmoid Activation (Output)

Each layer was using eLU activations and each block used Batch Normalizations and Spacial and Channel Squeeze and Excitation. <br>  

Note this architecture was found after a lot of iterations and was the handcrafted network I found to yield the best performance.  A neural architecture search method would likely yield better results, however the manual search and going through the process was invaluable experience from a personal learning perspective.

## Overall Thoughts
This was my first major kaggle competiton entry and overall I was quite happy with my performance, this was meant as a learning and benchmarking excersize primarily and I definitely learned a lot from this experience.  The insights into the competiton development pipeline and experience with new data types and experimentation was invaluable so that next time I can iterate upon versions faster and get a better result.  Also benchmarking my system was very valuable and this was the primary reason I decided to go for a custom model and train it from scratch as opposed to transferring and fine tuning a pretrained model.

##  Areas for Improvement
There is an awful lot that could have been done to achieve a better score here, even within hardware constraints.  Some obvious improvements are - 
* Many competition top placements were earned by simply linearly downscaling the prediction boxes by 10-20%, this could have easily yielded be a much higher score.
* Using a 2-stage model (such as an RCNN variant) or a model decoder with auxilary outputs
* Using more/different data augmentation to boost the generalization capabilities
* Experimenting with Deformable convolutions 
* Experimenting with Depthwise Seperable convolutions
* Using an ensemble of models (as well as a secondary layer of models built from the outputs of the first).


## Sources
(1) https://arxiv.org/abs/1804.06242 - Vortex Pooling: Improving Context Representation in Semantic Segmentation <br>
(2) https://arxiv.org/abs/1803.02579 - Concurrent Spatial and Channel Squeeze & Excitation in Fully Convolutional Networks <br>
(3) https://arxiv.org/abs/1608.06993 - Densely Connected Convolutional Networks <br>
(4) https://arxiv.org/abs/1602.07261 - Inception-v4, Inception-ResNet and the Impact of Residual Connections on Learning <br>
(5) https://arxiv.org/pdf/1511.07289.pdf - Fast and Accurate Deep Network Learning by Exponential Linear Units <br>
(6) https://keras.io/initializers/
