CodeBook.md describes the variables, the data, and any transformations or work that I performed to clean up the data
========================================================

Dataset downloaded: getdata-projectfiles-UCI HAR Dataset.zip from 
https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip 
on May 5, 2014 9:14PM Pacific time

Description of data available at:
http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones 

UCI HAR Dataset: Folder name with all unzipped downloaded data

Transformations: none. All data loaded with read.table default options.

```
Datasets created by reading .txt files:
trainX    loaded X_train.txt
testX     loaded X_test.txt
trainy    loaded y_train.txt
testy     loaded y_test.txt
trainSub  loaded subject_train.txt
testSub   loaded subject_test.txt
features  loaded features.txt
labels    loaded activity_labels.txt
```

```
key output datasets:
prefin_data   dataset with foramtting, and all relevant columns
output        final tidy data output
```

```
intermediate columns created:
features[,3]  logical filter for keyword 'mean()'
features[,4]  logical filter for keyword 'meanFreq()'
features[,5]  logical filter for keyword 'std()'
features[,6]  one combined logical vector of columns to include and exclude
mean_sdCols   name of relevant columns for mean and standard deviation
mean_sd       subset of main data X, with only releavnt columns with mean and standard deviation
act           activity type id and name for each row in X
act_label     activity type name for each row in X
lenSub        variable for # of subjects to decide output dimensions
uniqueSub     list of unique subjects to fill output datasets
lenActivity   variable for # of activities
```


