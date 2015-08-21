# Tidy Mean and Standard Deviation Data from the UCI HAR Data Set 

## The Data

This script uses the data from UC Irvine's Human Activity Recognition Using Smartphones Data Set.  The data used is here:
  
https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

More information about the data can be found here:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones
## The Script
This repository contains a script called `run_analysis.R`.
### Requirements
The data files should be in the working directory when you run the script.

The script depends on the following packages:
- dplyr
- reshape2
### Step by step breakdown
Load required packages.
``` R
library(dplyr)
library(reshape2)
```
Read files. Must have data in working directory.
``` R
subject_train   <- read.table("train/subject_train.txt")
X_train         <- read.table("train/X_train.txt")
Y_train         <- read.table("train/y_train.txt")
subject_test    <- read.table("test/subject_test.txt")
X_test          <- read.table("test/X_test.txt")
Y_test          <- read.table("test/y_test.txt")
features        <- read.table("features.txt")
activity_labels <- read.table("activity_labels.txt")
```
#### Use descriptive activity names to name the activities in the data set.
The activity names from the `activity_labels` file are applied to the variables from the `Y_` files.
``` R
activity_train <- data.frame(Activity = activity_labels$V2[Y_train$V1])
activity_test  <- data.frame(Activity = activity_labels$V2[Y_test$V1])
```

#### Appropriately label the data set with descriptive variable names.
In the step above the column for activities was labeled as "Activity".

The data column from the `subject_` files is labeled as "Subject".

The data columns from the `X_` files are labeled with the descriptions from the `features` file.
``` R
colnames(subject_train) <- c("Subject")
colnames(subject_test) <- c("Subject")
colnames(X_train)       <- features$V2
colnames(X_test)       <- features$V2
```
Join the columns from each set.
``` R
train <- bind_cols(subject_train, activity_train, X_train)
test  <- bind_cols(subject_test, activity_test, X_test)
```
#### Merge the training and the test sets to create one data set.
The sets are combined with rbind.
``` R
data  <- rbind(train, test)
```
#### Extract only the measurements on the mean and standard deviation for each measurement. 
Here the script takes  the "Subject" and "Activity" columns as well as columns with names containing "mean()" or "std()". 

The original data has some other columns with "Mean" in their names which are angle of means but are not means themselves. These columns are not included in our final data.

This step also writes the tidy data to a file.
``` R
cols <- c(1,2, grep("(mean|std)\\(\\)", colnames(data)))
tidyData <- data[cols]
write.table(tidyData, file="tidydata.txt", row.name=FALSE) 
```
#### Create a second, independent tidy data set with the average of each variable for each activity and each subject.
Data is melted and recast as a set of the means of the columns per subject per activity with reshaper2.

This data is written to its own independent file.
``` R
meltData <- melt(tidyData,id = c("Subject","Activity"))
avgData <- dcast(meltData, Subject + Activity ~ variable, mean)
write.table(avgData, file="averages.txt", row.name=FALSE) 
```
## Code Book

The data contains the following columns
- Subject - *An id of the subject who carried out the experiment.*
- Activity - *The descriptive name of the activity performed.*

The rest of the columns are from the original dataset which describes them like this.

>The features selected for this database come from the accelerometer and gyroscope 3-axial raw signals tAcc-XYZ and tGyro-XYZ. These time domain signals (prefix 't' to denote time) were captured at a constant rate of 50 Hz. Then they were filtered using a median filter and a 3rd order low pass Butterworth filter with a corner frequency of 20 Hz to remove noise. Similarly, the acceleration signal was then separated into body and gravity acceleration signals (tBodyAcc-XYZ and tGravityAcc-XYZ) using another low pass Butterworth filter with a corner frequency of 0.3 Hz. 
>
>Subsequently, the body linear acceleration and angular velocity were derived in time to obtain Jerk signals (tBodyAccJerk-XYZ and tBodyGyroJerk-XYZ). Also the magnitude of these three-dimensional signals were calculated using the Euclidean norm (tBodyAccMag, tGravityAccMag, tBodyAccJerkMag, tBodyGyroMag, tBodyGyroJerkMag). 
>
>Finally a Fast Fourier Transform (FFT) was applied to some of these signals producing fBodyAcc-XYZ, fBodyAccJerk-XYZ, fBodyGyro-XYZ, fBodyAccJerkMag, fBodyGyroMag, fBodyGyroJerkMag. (Note the 'f' to indicate frequency domain signals). 
>
>These signals were used to estimate variables of the feature vector for each pattern:  
>
>The set of variables that were estimated from these signals are: 
>mean(): Mean value
>std(): Standard deviation

- tBodyAcc-mean()-X
- tBodyAcc-mean()-Y
- tBodyAcc-mean()-Z
- tBodyAcc-std()-X
- tBodyAcc-std()-Y
- tBodyAcc-std()-Z
- tGravityAcc-mean()-X
- tGravityAcc-mean()-Y
- tGravityAcc-mean()-Z
- tGravityAcc-std()-X
- tGravityAcc-std()-Y
- tGravityAcc-std()-Z
- tBodyAccJerk-mean()-X
- tBodyAccJerk-mean()-Y
- tBodyAccJerk-mean()-Z
- tBodyAccJerk-std()-X
- tBodyAccJerk-std()-Y
- tBodyAccJerk-std()-Z
- tBodyGyro-mean()-X
- tBodyGyro-mean()-Y
- tBodyGyro-mean()-Z
- tBodyGyro-std()-X
- tBodyGyro-std()-Y
- tBodyGyro-std()-Z
- tBodyGyroJerk-mean()-X
- tBodyGyroJerk-mean()-Y
- tBodyGyroJerk-mean()-Z
- tBodyGyroJerk-std()-X
- tBodyGyroJerk-std()-Y
- tBodyGyroJerk-std()-Z
- tBodyAccMag-mean()
- tBodyAccMag-std()
- tGravityAccMag-mean()
- tGravityAccMag-std()
- tBodyAccJerkMag-mean()
- tBodyAccJerkMag-std()
- tBodyGyroMag-mean()
- tBodyGyroMag-std()
- tBodyGyroJerkMag-mean()
- tBodyGyroJerkMag-std()
- fBodyAcc-mean()-X
- fBodyAcc-mean()-Y
- fBodyAcc-mean()-Z
- fBodyAcc-std()-X
- fBodyAcc-std()-Y
- fBodyAcc-std()-Z
- fBodyAccJerk-mean()-X
- fBodyAccJerk-mean()-Y
- fBodyAccJerk-mean()-Z
- fBodyAccJerk-std()-X
- fBodyAccJerk-std()-Y
- fBodyAccJerk-std()-Z
- fBodyGyro-mean()-X
- fBodyGyro-mean()-Y
- fBodyGyro-mean()-Z
- fBodyGyro-std()-X
- fBodyGyro-std()-Y
- fBodyGyro-std()-Z
- fBodyAccMag-mean()
- fBodyAccMag-std()
- fBodyBodyAccJerkMag-mean()
- fBodyBodyAccJerkMag-std()
- fBodyBodyGyroMag-mean()
- fBodyBodyGyroMag-std()
- fBodyBodyGyroJerkMag-mean()
- fBodyBodyGyroJerkMag-std()
