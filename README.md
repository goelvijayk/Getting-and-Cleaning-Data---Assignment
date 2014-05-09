Getting and cleaning data Course project
========================================================
This code explains how script works.

Data was downloaded as zip file manually, and manually extracted into a folder automatically names from zip file. the R script is placed in folder that has the extracted folder as one entity called 'UCI HAR Dataset'.

```r
trainX <- read.table("./UCI HAR Dataset/train/X_train.txt")
testX<- read.table("./UCI HAR Dataset/test/X_test.txt")
trainy<- read.table("./UCI HAR Dataset/train/y_train.txt")
testy<- read.table("./UCI HAR Dataset/test/y_test.txt")
trainSub<- read.table("./UCI HAR Dataset/train/subject_train.txt")
testSub<- read.table("./UCI HAR Dataset/test/subject_test.txt")
```

Then all relevant text files (X, y and Subject) are read into individual datasets for both test and train. Then test and train datasets are bound together into 3 dataframes. 

```r
X<- rbind(trainX, testX)
y<- rbind(trainy,testy)
Sub<- rbind(trainSub, testSub)
names(Sub)<- "Subject" #readable column name
```


Then features dataset is read, and logical vector created for matching keywords 'mean' and 'std'. This creates inclusion of few extra elements with meanFreq in their name, which are explicitly removed separately. Using logical vector for which columns are included, and those column names, the final dataset is named (later).
```r
features <- read.table("./UCI HAR Dataset/features.txt")
features[,3]<- grepl("mean()",features[,2]) #filter for mean
features[,4]<- !grepl("meanFreq()", features[,2]) #meanFreq showed up for mean, hence separately removed
features[,5]<- grepl("std()", features[,2]) #filter for standard deviation
features[,6]<- as.logical(features[,3]*features[,4] + features[,5]) #create one logical vector of columns to include and exclude. Easier to modify if logic has to be changed later.
mean_sdCols<- features[features[,6],2] #extract actual columns names for mean and standard deviation; will be used to name columns of X later
mean_sd<- X[, features[,6]] #Create subset of X for needed columns
names(mean_sd)<- mean_sdCols # assign column names
```
For descriptive activity names, activity type and activity labels are joined, and activity descriptive labels column is included as a column in main dataset later.

```r
labels<- read.table("./UCI HAR Dataset/activity_labels.txt")
act<- merge(y, labels, all = TRUE) #act = activity fields merged. has extra columns. intermediate dataset.
act_label<- as.data.frame(act[,2]) #use only needed data with right format.
names(act_label) <- "activityType" #Create a column name of activity types for reference.
prefin_data <- cbind(Sub, act_label, mean_sd) #create prefinal dataset
```

To create one final output with summary, one data frame is created with all subject and activity type combinations. This is done since question asked for each activity type and each subject. Then the relevant dataset is subsetted in loop to match subject and activity type. For match, the row is updated with means.

```r
lenSub<- length(unique(Sub[,1])) #variable for # of subjects to decide output dimensions
uniqueSub<-sort(unique(Sub[,1])) #variable for list of unique subjects to fill output datasets
lenActivity <- length(labels[,1]) #variable for # of activities

output<- data.frame(matrix(NA, nrow=lenSub*lenActivity, ncol=ncol(mean_sd)+2)) #create data structure for final output
output[, 1:2] <- merge(uniqueSub, labels)[,c(1,3)] #populate first couple of columns with subject and activity Type
names(output)<- names(prefin_data) #add columns names to final dataset

# fill averages - for each combination of subject and activity, calculate column mean, and update in output dataset
# cases where subject did not do certain type of activity, it leaves the row with NaN
# Since question  specified 'each activity' and 'each subject', hence have included rows for NaN too, where certain subjects did not do particular activity types 
for (i in 1:nrow(output))
{    
    output[i, 3:68] <- colMeans(prefin_data[prefin_data[,1]==output[i,1] 
                                            & prefin_data[,2]==output[i,2],3:68]
                                , na.rm = TRUE)
}
```
