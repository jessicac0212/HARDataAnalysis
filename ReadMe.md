---
title: "ReadMe"
author: "Jessica"
date: "June 4, 2017"
output: html_document
---

## About this repo
THis repo contains the following files:

1. ReadMe.md

2. run_analysis.R: the R script to analyse the "UCI HAR Dataset"

4. HARDataMean.csv: the tidy data set generated by run_analysis.R. It contains the average value of mean and std of each signal in different domains per acitivity per subject.

5. CoodBook.md: explanation of the data source, the variables in the data set HARDataMean.csv, and the corresponding data processing steps. 

## Before using the run_analysis.R script

This run_analysis.R script uses functions from the libraries "dplyr", "tidyr" and "stringr". Therefore, before running this script, pleas make sure these libraries are installed.

In order to run the script run_analysis.R, please put it in the same fold as the fold "UCI HAR Dataset". And make sure "UCI HAR Dataset" contains the following files:

* 'features_info.txt': Shows information about the variables used on the feature vector.

* 'features.txt': List of all features.

* 'activity_labels.txt': Links the class labels with their activity name.

* 'train\\X_train.txt': Training set.

* 'train\\y_train.txt': Training labels.

* 'test\\X_test.txt': Test set.

* 'test\\y_test.txt': Test labels.

* 'train\\subject_train.txt' and 'test\\subject_train.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30. 

## How the run_analysis.R script works
### THis run_analysis.R script works as follows.

1. Read the file ".\\UCI HAR Dataset\\activity_labels.txt", and store the content in a table **tblActivity(id, activity)**. Rename the activities as (walking, walkingupstairs, walkingdownstairs, sitting, standing, laying).
2. Read the file ".\\UCI HAR Dataset\\features.txt", and store the variables in the vector called **vecFeature**, then get rid of any characters other than a-zA-Z0-9, replace them with ".".
3. Generate one data set for the train set.  
        1) Read the train set files, training labels ".\\UCI HAR Dataset\\train\\y_train.txt", subject record ".\\UCI HAR Dataset\\train\\subject_train.txt", and store them in vectors **vecTrainActivityID**, **vecTrainSub**, respectively.   
        2) Read the train set file ".\\UCI HAR Dataset\\x_train.txt", and store it in table **tblTrainSet**. The column headers of tblTrainset is the vector obtained in step 2, **vecFeature**. Keep only the columns with ".mean." and ".std." in their headers since they corresponds to the variables generated by functions mean() and std().
        3) Add **vecTrainSub** and **vecTrainActivityID** to **tblTrainSet** as the first two columns named as **subject** and **activityid**. At this point, the tain data set **tblTrainSet** is **tblTrainSet(subject, activityid, [all the features using mean() or std()])**. 
4. Generate the data set for the test set, using the three sub-steps in step 3, where "Train" is replaced by "Test". Store the result in the table **tblTestSet**. 
5. Merge tblTestSet and tblTrainSet into one dataset named as **HARData** using bind_rows() function.
6. Group **HARData** by **subject** and **activityid**, then summarise each column using function mean(), store the result in a new dataset named as **HARData_mean**.
7. Replace the **activityid** column with **activity**, whose values are the names of the activities, using function inner_join() to joint **tblActivity(id, activity)** and **HARData_mean**, and remove the activityid column. At this point, **HARData_mean** is **HARData_mean(subject, activity, [all the features using mean() or std()])** 
8. Gather all the columns in **HARData_mean** other than **subject**, **activity** into one column named as **measure**, and the measured values are stored in the column **value**.
7. Separate the column **measure** into (**domain**, **signal**, **method**). **Note**: at this point, the values in the column **measure** have the same format as  

        * [domain][feature].[method]..(.[axis]):  
                * domain: one character either "f" or "t".  
                * signal: takes one of the values ("BodyAccJerkMag", "BodyAccJerk", "BodyAccMag", "BodyAcc", "BodyGyroJerkMag", "BodyGyroJerk", "BodyGyroMag", "BodyGyro", "GravityAccMag", "GravityAcc").  
                * method: either "mean" or "std".  
                * axis: axis takes one of the values ("X","Y","Z"). **NOTE**: features ("BodyAccJerkMag", "BodyAccMag", "BodyGyroJerkMag", "BodyGyroMag", "GravityAccMag") does not have the axis attribute.  
Accordingly, the measure column is separated into columns (**domain**, **signal**, **method**, **axis**). Then merge **signal** and **axis** into one column using function past0() to replace **signal**. Replace the values "f" and "t" in **domain** as "freq" and "time", respectively. Upto this point, **HARData_mean** have columns (**subject**, **activity**, **signal**, **domain**, **method**, **value**)
8. Spread the **method** column into **mean** and **std**, where column **value** is used to populate the cells in **mean** and **std**. 
9. Process the typos. **NOTE** that int the "UCI HAR Dataset\\feature.txt" file, the features "fBodyBodyAccJerkMag-mean()","fBodyBodyAccJerkMag-std()", "fBodyBodyGyroMag-mean()", "fBodyBodyGyroMag-std()", "fBodyBodyGyroJerkMag-mean()", "fBodyBodyGyroJerkMag-std()" should be "fBodyAccJerkMag-mean()","fBodyAccJerkMag-std()", "fBodyGyroMag-mean()", "fBodyGyroMag-std()", "fBodyGyroJerkMag-mean()", "fBodyGyroJerkMag-std()".   
10. COnvet values in the column **signal** be lower case. At this point, data set **HARData_mean**(**subject**, **activity**, **signal**, **domain**, **mean**, **std**) is established.
11. Write **HARData_mean** into file ".\\HARDataMean.csv".
                
## Reference
[1] Davide Anguita, Alessandro Ghio, Luca Oneto, Xavier Parra and Jorge L. Reyes-Ortiz. Human Activity Recognition on Smartphones using a Multiclass Hardware-Friendly Support Vector Machine. International Workshop of Ambient Assisted Living (IWAAL 2012). Vitoria-Gasteiz, Spain. Dec 2012               