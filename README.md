# Getting and Cleaning data course project  

## The reposirity includes the following files:  
  *  README.md 
  *  CodeBook.md -- describing the variables.
  *  run_analysis.R -- the R source code 

## The instructor for the run_analysis.R

### Step 1 Merges the training and the test sets to create one data set 
  1.load all row data
  ```
  # load data relevent to train
X_train <- read.table("./train/X_train.txt")    # X_train.txt store the data about training that contains Triaxial acceleration and Triaxial Angular velocity that have been processed
y_train <- read.table("./train/y_train.txt",col.names = "activity_labels")    # y_train.txt store the activity labels about training data set 
subject_train <- read.table("./train/subject_train.txt",col.names = "subjects")  # subject_train.txt store the identifier of the subject about training data set 
 
# load data relevent to test 
X_test  <- read.table("./test/X_test.txt")
y_test  <- read.table("./test/y_test.txt",col.names = "activity_labels")
subject_test <- read.table("./test/subject_test.txt",col.names = "subjects")

# load features data
features <- read.table("features.txt")  #  A 561-feature vector with time and frequency domain variables

# load activity_labels
activity_labels <- read.table("activity_labels.txt")
 ``` 
  2.merge the data sets to create train and test sets
  ```
train <- cbind(X_train,y_train,subject_train)
test  <- cbind(X_test,y_test,subject_test)
merge_data <- rbind(train,test)

  ```
### Step 2 Extracts only the measurements on the mean and standard deviation for each measurement
  ```
# extract the columns includeing "mean()" or "std()" from the "feature" dataframe
mean_std_features <- features[grep("-mean\\(\\)|-std\\(\\)",features$V2,ignore.case = FALSE),]

# extract the data from merge data 
extract_data <- merge_data[,c(mean_std_features[,1],length(merge_data)-1,length(merge_data))]
  ```

### Step 3 Uses descriptive activity names to name the activities in the data set
  ```
# create a vector named "activity" to store the activity names about the "activity_labels" in the "extract_data" 
for(i in 1:nrow(extract_data)){
  for(j in 1:6){
    if(extract_data[i,ncol(extract_data)-1] == activity_labels[j,1]){
      activity[i] <- activity_labels[j,2]
    }else{
      next()
    }
  }
}

# bind the "extract_data" and "activity"
activityname_data <- cbind(extract_data,activity)
	
  ```
### Step 4 Appropriately labels the data set with descriptive variable names.
  ```
feature_name <- mean_std_features[,2]
feature_name <- as.character(feature_name)
names(activityname_data) <- c(feature_name,"activity_labels","subject_ids","activity_names")

  ```
### Step 5 creates a second, independent tidy data set with the average of each variable for each activity and each subject
  ```
tidy_data <- aggregate(activityname_data[,1:(length(activityname_data)-3)],by = list(activityname_data$activity_labels,activityname_data$subject_ids),FUN="mean")

  ```
### Final step write the data set into a txt format file
  ```
write.table(tidy_data,file="./tidyData.txt",row.name=FALSE)
  ```
