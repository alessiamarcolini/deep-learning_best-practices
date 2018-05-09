# Deep Learning Best Practices

Following the work made by Nishant Gaurav (fast.ai student) at http://forums.fast.ai/t/30-best-practices/12344 I'm sharing some Deep Learning best practices I learned following as an International Fellow the courses [Practical Deep Learning for Coders](http://course.fast.ai/) and [Cutting Edge Deep Learning for Coders](http://course.fast.ai/part2.html) (2018 version) taken by Jeremy Howard and Rachel Thomas at fast.ai.

## Dataset
- [ ] Do as much of your work as you can on a small sample of the data
- [ ] Public leaderboard of Kaggle is not a replacement for Validation Set. Jeremy showed how he was ranked in 5th in the private leaderboard of Rossmann Competition, whereas he was not in top 300 in public leaderboard. In another example, The test set of public leaderboard (in Iceberg Satellite image Competition) contained mostly of augmented images. 
- [ ] Look into data. Remove outliers which make sense and there is no other variable to capture those outliers: like in Rossmann Competition, the date&timings for closed stores were not known. There is extra sale before and after close period. So, if you don’t have any data to model the outliers, you need to remove them during training. 
- [ ] Look into training: After training the cats vs dogs we could see that some incorrect classified images were mostly misclassified due to cropping. The solution was data augmentation 
- [ ] Data Augmentation: You cannot use all possible types of augmentation. For best results we need to use the right kind of augmentation. 
- [ ] Test Time Augmentation (TTA): Increases accuracy 
- [ ] Rossman Notebook: Without expanding your date-time column using add_date_part function of fastai library, you can’t capture any trend/cyclical behavior as a function of time at any of these granularities. We’ll add to every table with a date field.
- [ ] Rossman Notebook: many models have problems when missing values are present, so it’s always important to think about how to deal with them. In these cases, we are picking an arbitrary signal value that doesn’t otherwise appear in the data.


## Learning Rate
- [ ] Use learning rate finder and select a learning where convergence of loss is steep. Do not select the biggest possible learning rate. 
- [ ] When using a pretrained model on some dataset like imagenet, you need to use different learning rates when you are using that model for any new dataset. The initial layers need a smaller learning rate, and the deeper layers need a comparatively larger learning rate. When the new dataset is similar to original dataset (e.g. cats vs dogs is similar to imagenet but iceberg satellite image is not) the weights have a ratio of 10. But when using the imagenet of satellite model the successive weights should have a ratio of 3. 
- [ ] Cosine annealing: This now supported by default in pytorch 0.3.18 
- [ ] SGD with restarts: 2 setups work very well. 
- [ ] To tackle Gradient Explosion we use identity matrix for intialization. Also allows higher learning rate.


## Training
- [ ] bn_freeze = True: In case you are using a deeper network anything greater than resnet34 (like resnext50), bn_freeze = True should be used when you unfreeze and your new dataset is very similar to the original data used in pretrained model. Pytorch is probably the only library which offers this much needed switch.
- [ ] On the other hand, when the new dataset is not similar to the original dataset, we start with smaller size 64x64, fit in freezed state, unfreeze and fit. And repeat the process with 128x128 and 256x256 
- [ ] Kaiming He Initialization: Pytorch has this implemented by default. 
- [ ] Adam Optimizer has a better version called AdamW


## Activation functions
- [ ] theoretically softmax and logsoftmax are scaled version of each other, but empirically logsoftmax is better
- [ ] sigmoid instead of softmax for multi-label classification 
- [ ] applying sigmoid in the end when you know the min and max of output (eg. highest sale and lowest sale is known in the Rossmann data) relieves the neural network and training is faster. This is similar to applying softmax when you know the output should be probability 
- [ ] In hidden state to hidden state transition weight matrices tanh is used 


## Architectures
- [ ] In nlp, we have to use slightly different betas in Adam Optimizer 
- [ ] In nlp, we use different dropouts all over the place in a specific LSTM model. These dropouts have to be in a certain ratio. 
- [ ] In nlp, we use also use gradient clipping. There is no reason why this cannot be used in other models 
- [ ] All the NLP models probably need a line of code for regularisation 
- [ ] RNN cell is not used nowadays coz of low learning rate constraint due to gradient explosion. We use GRU
- [ ] In sentiment analysis, transfer learning has outperformed state of the art sentiment analysis models 
- [ ] Stride 2 convolution has same effect as Max Pool
- [ ] Batch normalisation allows us to design resilient deeper networks and learning rate can be made higher. It is similar to dropout in the sense that it changes the meaning of the layers, which a kind of regularisation technique like dropout. Therefore, like dropout it is not done in test mode. 
- [ ] Batch normalisation works best when done after relU
- [ ] Resnet ensures richer input for first layer. 5by5 convolution is used in start, and stride is set to 1. In subsequent layers stride is 2 and 3by3 convolution is used. Padding is important when your activations are smaller like 4by4. 
- [ ] Resnet uses something known as Identity training. It has layer-groups. Each layer-group has a bottleneck layer with stride = 2, which causes reduction in activation size. The rest of the layers in the group just try to predict the error through identity training. This concept is yet to be explored in NLP. 
- [ ] Concatenation of AdaptiveAvg Pooling and AdaptiveMaxPooling is better. 
