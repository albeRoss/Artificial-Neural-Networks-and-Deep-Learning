requirements: 
----------------------------------------------------------------
tensorflow 2.0.0
---------------------------------------------------------------

##################################################################
##  LINEAR COMBINATION OF U-NETs + RESIDUAL DENSE BLOCKS	##
##  BEST SCORE: 0.64051 					##
##  NO TRANSFER LEARNING					##
##################################################################

- no augmentation
- Adam : lr=10^-3
- Dice Loss
- batch size: 16
- 

EXPERIMENTS:

1) Lattari notebook U-net without skip connections didn't perform well ( maximum ~0.3)
	changed Sparse Cross entropy to Binary Cross entropy and last layer of the network 

2) Added skip connection to it and batch-normalisations performed better (~0.4) 

3) (transfer learning) MobileNetV2(imagenet weights)  encoder and handcrafted decoder (similar to the one provided in the notebook) without fine-tuning (~0.5)

4) handcrafted U-net model without dense residual blocks and dropout layers added around bottleneck (~0.55) 

5) handcrafted U-net model with dense residual blocks without dropouts and augmentations(see params at the bottom) (tried without dropout because of shift variance problem) 
	the residual blocks improved the performance to ~0.60. 

6) (transfer learning) Resnet(imagenet weights): for this I tried with and without augmentations and fine-tuning and dense blocks. At the end it overfitted after 20 iterations in every experiment I did. 

7) At this point I tried many variants of the nets builded so far, changing batch size, number of skip connections, number of filters and augmentations. No improvements.
	The best model was the last builded with params reported at the bottom ( no augmentation)
 
I switched to Dice Loss that revealed to be more consistent with the IoU I get on public leaderboard and in validation.

8) Next was the handcrafted U-net model in the notebook with dense residual blocks with dropouts all around and many more batch normalisation layers
	best score after ~80 it was ~0.6117 IoU. 

9) Then I tried many combinations: 
	Average of the best performing models:
		- (resnet) + (models with augmentation) + (model without augmentations)
		- (resnet) + (models with augmentation)
		- (models with augmentation) + (model without augmentations)
		- same model different iterations 

Best scoring iterations of last model (up to 100 it): 


||	iteration || val_IoU	||
----------------------------------
||	70	  || 0.7292	||
||	80 	  || 0.7300	||
||	88 	  || 0.7330	||	
|| 	90 	  || 0.7303	||
||	98	  || 0.7370 	||
||	99	  || o.7317	||


At prediction step, I combine the scores of the same model trained at different iterations. The scores are weighted by normalised IoU. 

so for each tensor of every test image: 
	    	
		tf.math.add_n(
		[prediction_noaug7292*(0.7292/tot),
		prediction_noaug7370*(0.7370/tot),
		prediction_noaug7317*(0.7317/tot),
		prediction_noaug7300*(0.7300/tot),
		prediction_noaug7303*(0.7303/tot), 
		prediction_noaug7330*(0.7330/tot)])

where tot is the sum of all the val_IoUs


leaderboard score: 0.64051

for checkpoints: https://polimi365-my.sharepoint.com/:f:/g/personal/10652220_polimi_it/Ery2R-9B0p5IpBn7eMuH40IBgao5Wxh6_NxTTwuU7NSl-w?e=QT79K5



tried augmentations:

rotation_range=2, 5, 10
width_shift_range=2, 5, 10 
height_shift_range=5, 10
zoom_range=0.1, 0.5
validation_split=0.2, 0.3 
horizontal_flip=True, #fixed
vertical_flip=True, #fixed
fill_mode='reflect', 'nearest'
rescale=1./255