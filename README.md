# ML5G-PHY Challenge Report - Redline Team
### October 28, 2020


### Redline Team Members:
### • Ali Arad,                           
### • Felix Dujmenovic,            
### • Brad Stimpson,                   
### • Manbir Kaur               

### Contacts: aarad@rdlcom.com, fdujmenovic@rdlcom.com  



# 1	INTRODUCTION

This document briefly describes the approach we took to tackle this channel estimation challenge. The proposed solution is implemented using MATLAB. The source code of the algorithm as well as the modified script we used to generate the training datasets are provided along with this document. We also provided link to a OneDrive folder where the estimated channel matrix files are available for download.

# 2	TRAINING DATASET GENERATION

Due to memory limitation, we could not directly use the MATLAB scripts provided to generate training dataset for 10,000 channels. To resolve this issue, we modified the script to generate the training datasets for 1000 channels at a time. We generated 10 sets of training data, each one containing 1000 channels. For each set, we created two MAT files: one containing received pilot symbols (r) and the other one containing the channel matrix (H) for 1000 channels. Our modified script generated the following files for each data set case:

-	RXpilot_SNRx_Nc1000_y_R.mat	R array containing Rx pilots for 1000 channels
-	RXpilot_SNRx_Nc1000_y_H.mat	Hall array containing H matrix for 1000 channels

where x is the data_set value (1, 2 or 3) corresponding to SNR of training sets, and y is the set number (1 to 10) for the 10 sets each with 1000 channels.

Training datasets for each of the 9 test scenarios are stored in separate folders named as “datasetx_n”, where x indicates the SNR index as described above, and n is the number of training symbols (Ntrain).

Our modified script for training data generation is included in this repository ("gen_RXtraining_SNR_Raymobtime_nset.m" file). We also included the revised function provided for channel matrix generation ("gen_channel_ray_tracing_rev.m" file).

# 3	CHANNEL ESTIMATION ALGORITHM

Our proposed solution is based on K-Nearest Neighbor (KNN) machine learning algorithm. Here is the basic idea behind this algorithm: For training channels we have the received pilots (r) and the channel matrix (H). For test channels, we only have r and want to estimate H based on the existing training data. This estimation problem is similar to a regression problem where we have 10,000 sets of (r, H) data points for training channels, and we want to estimate the H for a test channel using its r. The algorithm tries to find the nearest neighbors for the test channel r (referred to as r_test here) among the existing training channel r’s (r_training’s). After finding the right neighborhood for the test channel using KNN algorithm on r, the proposed solution speculates that the same neighborhood is also valid for H. In other words, the algorithm assumes that the training channels that are the nearest neighbors to a test channel based on r are also its nearest neighbors based on H. Based on this speculation, the algorithm estimates the H of a test channel (H_test) based on the H’s of training channels in its neighborhood determined based on its r. The basic concept of the proposed solution is demonstrated in Figure 1.
