datascienceproject
==================

Hello,

I am very sorry for the trouble, but I am having a ton of trouble with Git and RStudio and pushing my run_analysis.R code to this repository. Somehow when I post this repository URL into RStudio, I get a message of "No such file or directory exists".

Since I know very little of working in GitHub, and since I need to submit this now, I am just going to post my run.analysis.R code at the bottom of this file and I hope that you will be gracious grader and understand.

-----------------------------
README

The goal of this project was to accomplish the following:

You should create one R script called run_analysis.R that does the following. 
Merges the training and the test sets to create one data set.
Extracts only the measurements on the mean and standard deviation for each measurement. 
Uses descriptive activity names to name the activities in the data set
Appropriately labels the data set with descriptive variable names. 
Creates a second, independent tidy data set with the average of each variable for each activity and each subject. 

The data files used in this project were:
- 'README.txt'
- 'features_info.txt': Shows information about the variables used on the feature vector.
- 'features.txt': List of all features.
- 'activity_labels.txt': Links the class labels with their activity name.
- 'train/X_train.txt': Training set.
- 'train/y_train.txt': Training labels.
- 'test/X_test.txt': Test set.
- 'test/y_test.txt': Test labels.

The following files are available for the train and test data. Their descriptions are equivalent. 
- 'train/subject_train.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30. 



I first read all the files and assigned appropriate column names to each one. I then used cbind and rbind to make the master dataset with the training data, the test data, the subject ID, and the activity they were performing. I then used the grepl function to reduce my dataset to only include the mean and std dev columns, not including ones that were mean frequency. Then I merged the activity labels to the activity ids in the data to give them appropriate context. I then cleaned up the column names using gsub to make their names clear and descriptive- turning words like 'Acc' into acceleration, making 't' to be 'Time', and to clean up a duplicate typo of 'BodyBody' in the column. Lastly, I used the aggregate function to find the means of all the variables based on each subjectid and each activity. Then I wrote the tidy set to a text file.

I know I was supposed to create a codebook as well but ran out of time to do so. I have attached the R code I ran below. Again, I apologize for not being able to push it to GitHub in an R file, but I hope you will be understanding. Thank you.

---------------
run_analysis.R
---------------

#Step1

#Read in the necessary files for the test and training data set

xtrain <- read.table("X_train.txt")

ytrain <- read.table("Y_train.txt")

subjecttrain <- read.table("subject_train.txt")

xtest <- read.table("X_test.txt")

ytest <- read.table("Y_test.txt")

subjecttest <- read.table("subject_test.txt")

activitylabels <- read.table("activity_labels.txt")

features <- read.table("features.txt")


#assign column names to the test and train observations using the features data

colnames(xtrain)=features[,2]

colnames(xtest)=features[,2]



#assign column names to the subject and activity columns

colnames(ytrain)="activityid"

colnames(ytest)="activityid"

colnames(subjecttrain)="subjectid"

colnames(subjecttest)="subjectid"



#combine the columns of the subjects, activities, and results

traindata <- cbind(subjecttrain,ytrain,xtrain)

testdata <- cbind(subjecttest,ytest,xtest)



#combine the test and training data

masterdata <- rbind(traindata,testdata)



#Step2

#assign a vector to the column names of the data


mastercolumns = colnames(masterdata)


#Use a logical vector to only keep the columns with subject, activity, and mean, and standard dev

#did not include columns with mean freq

relevantcolumns = ( grepl("subjectid",mastercolumns) | grepl("activityid",mastercolumns) | 
grepl("-mean..",mastercolumns) & !grepl("-meanFreq..",mastercolumns) | grepl("-std..",mastercolumns) & 
!grepl("-std()..-",mastercolumns))



#set the vector to TRUE

masterdata = masterdata[relevantcolumns==TRUE]





#Step3



#assign column names for the activities

names(activitylabels)[1]<-"activityid"

names(activitylabels)[2]<-"activity"



#merge the descriptive activity names to the data set

masterdata = merge(masterdata, activitylabels)





#Step4



#clean up column names to make them descriptive

#spell out words fully and remove typos

names(masterdata) <- gsub("^t","Time",names(masterdata))

names(masterdata) <- gsub("^f","Frequency",names(masterdata))

names(masterdata) <- gsub("Acc","Acceleration",names(masterdata))

names(masterdata) <- gsub("Mag","Magnitude",names(masterdata))

names(masterdata) <- gsub("Gyro","Gyroscope",names(masterdata))

names(masterdata) <- gsub("mean","Mean",names(masterdata))

names(masterdata) <- gsub("std","StdDev",names(masterdata))

names(masterdata) <- gsub("-","",names(masterdata))

names(masterdata) <- gsub("BodyBody","Body",names(masterdata))



#Step5


#create new tidy data with activity name removed

masterdatanoactivity = masterdata[,names(masterdata) != 'activity']


#calculate the means of each variable by each subject and each activity

tidydataset = aggregate(masterdatanoactivity[,names(masterdatanoactivity) != c('activityid','subjectid')],by=list(activityid=masterdatanoactivity$activityid, subjectid=masterdatanoactivity$subjectid),mean)


#add the activity label column back in the data set

tidydataset = merge(tidydataset, activitylabels,by='activityid',all.x=TRUE)


#write the dataset as a txt file

write.table(tidydataset, "tidydataset.txt", row.names=FALSE)

