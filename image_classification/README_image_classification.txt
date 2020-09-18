
######################   REQUIREMENTS     #####################
tensorflow 2.0.0 or more
###############################################################

SEED = 777

Study case:  

At first, I tried a handcrafted CNN model containing: 

-a Convolutional block: 

	- tf.keras.layers.Conv2D(filters=num_filters,
                	         kernel_size=(3, 3), # fixed 
                	         strides=(1, 1), # fixed
                	         padding='same') # fixed 
	- tf.keras.layers.ReLU() 
	- tf.keras.Dropout(0.2) # fixed
	- tf.keras.layers.MaxPool2D(pool_size=(2, 2)) # [here I tried average pooling, but didn't get as much as MaxPool experiments]


-a Dense layer of n neurones
-a fixed dense layer of num_classes neurons with softmax activation function  

Parameters were: 

depth: number of times the convolutional block was repeated. 
start_f : number of starting features to consider for the first convolutional block.
increment_f : multiplicative factor of start_f in order to increase the volume extend of each convolution
pool_size: parameter of max pooling layer used to reduce spatial extend of the previous volume. 
n : (512, 600, 640) number of neurones of the dense last but one layer 

Augmentations parameters: 

rotation_range = 10 , 20 
validation_split=0.2,
width_shift_range = 10, 20
heigh_shift_range = 10, 20 
zoom_range = 0.1 , 0.3
horizontal flip = True # fixed
vertical_flip = True, False 
fill_mode='constant' # fixed
cval=0

generator parameters:

target_size of images (256,256), (300,300), (320,320)
batch_size = 4, 8


###############################################################
Best handcrafted model did 0.59 accuracy with: 
###############################################################

target_size of images = (320,320)
batch_size = 8

rotation_range = 20 
width_shift_range = 20
heigh_shift_range = 20 
zoom_range = 0.3
horizontal flip = True # fixed
vertical_flip = True
fill_mode='constant' # fixed
cval=0

depth: 7 
start_f : 8
increment_f : 2
pool_size: 2,2 
n :  640

Due to time consuming trainings I didn't resort to cross validation, hoping data augmentation would limit overfitting
A cross validated model would definitely performed better than this, maybe considering richer augmentations, maybe not due to small dataset. 


###############################################################
Then I resort to trasfer learning
###############################################################

---------------------MOBILENET---------------------------------

I tried tf.keras.applications.MobileNet(weights='imagenet', include_top=False )first since it has fewer trainable_params than others , so faster training. 
We mainly tried to search in previously defined space, finetuning last layers. 

we attached MobileNet to: 

-a Dense layer of 640 neurones # fixed
-a fixed dense layer of num_classes neurons with softmax activation function

target_size of images = (320,320)
batch_size = 8

rotation_range = 10
width_shift_range = 10
heigh_shift_range = 10 
zoom_range = 0.3
horizontal flip = True # fixed
vertical_flip = True
fill_mode='constant' # fixed
cval=0



###############################################################
Best MobileNet model got 0.89 accuracy with:
###############################################################

fine_tuning = last 7 layers. ( I tried till 10 and got worse so I stopped going deeper) 

Due to time consuming trainings I didn't resort to cross validation, hoping data augmentation would limit overfitting
A cross validated model would definitely performed better than this, maybe considering richer augmentations, maybe not due to small dataset. 


------------------------INCEPTIONV3---------------------------

Finally, I tried tf.keras.applications.InceptionV3(weights='imagenet', include_top=False). Many more trainable parameters. 
We tried to search in previously defined space, finetuning last layers again.

we attached MobileNet to: 

-a Dense layer of 640 neurones # fixed
-a fixed dense layer of num_classes neurons with softmax activation function

target_size of images = (320,320)
batch_size = 8

rotation_range = 10
width_shift_range = 10
heigh_shift_range = 10 
zoom_range = 0.3
horizontal flip = True # fixed
vertical_flip = True
fill_mode='constant' # fixed
cval=0 


###############################################################
Best IncetpionV3 model got 0.928 accuracy with:
###############################################################


Due to time consuming trainings I didn't resort to cross validation, hoping data augmentation would limit overfitting
A cross validated model would definitely performed better than this, maybe considering richer augmentations, maybe not due to small dataset. 



