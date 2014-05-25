Project_cleaningdata
====================

Project_cleaningdata - peer assessment
## merge train and test sets
## files are in train and test folders
## there is a data file, a label file and 
## there is a sample file for train/test
## need to row bind the train and test files for each of these catergories

## assume you are in the directory where the UCI HAR Dataset is unzipped
dirname <-  getwd()

feature_names <- read.table("features.txt")
activity_label <- read.table("activity_labels.txt", )

train_dir <- paste0 ( dirname , "/train")
test_dir <- paste0 ( dirname , "/test")

## combine train test for subject data
subject_train_data  <- read.table(paste0(train_dir, "/subject_train.txt"), header=FALSE,col.names="subject_id" )
subject_test_data  <- read.table(paste0(test_dir, "/subject_test.txt"), header=FALSE,col.names="subject_id" )
subject_train_test  <- rbind(subject_train_data,subject_test_data )

## combine train test for all features (X) data
X_train_data  <- read.table(paste0(train_dir, "/X_train.txt"), header=FALSE, col.names= feature_names[,2] )
X_test_data  <- read.table(paste0(test_dir, "/X_test.txt"), header=FALSE,col.names= feature_names[,2] )
X_train_test  <- rbind(X_train_data,X_test_data )


## combine train test for all   (y) data
y_train_data  <- read.table(paste0(train_dir, "/y_train.txt"), header=FALSE)  ##,col.names= "activity_label" )
y_test_data  <- read.table(paste0(test_dir, "/y_test.txt"), header=FALSE)  ##,col.names= "activity_label" )
y_train_test  <- rbind(y_train_data,y_test_data )

## merge the activity labels and activity numbers for each subject
y_train_test_with_labels <- merge(activity_label, y_train_test, by="V1")
names(y_train_test_with_labels)[2] <- "activity_label"
names(y_train_test_with_labels)

## select only rows with mean and std
mean_rows <- agrep("mean", feature_names[ ,2])
std_rows<- agrep("std", feature_names[ ,2])
mean_std_rows <- sort(cbind( t(mean_rows), t(std_rows) ))

## select data only for mean and std values
X_train_test_final <- X_train_test[ ,mean_std_rows ]  
X_train_test_final_with_activity <- cbind(y_train_test_with_labels[,2], X_train_test_final  )
names(X_train_test_final_with_activity)[1] <- "activity_label" 

attach(X_train_test_final_with_activity)

### aggregating data - not complete

aggdata <-aggregate(X_train_test_final_with_activity, by = list(activity_label) , FUN=mean, na.rm=TRUE)

aggdata<- sapply(  X_train_test_final_with_activity,  activity_label  , FUN=mean)
  print(aggdata)
