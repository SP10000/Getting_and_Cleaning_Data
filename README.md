# Getting_and_Cleaning_Data
Getting and Cleaning Data Assignment

This file below explains the script that is produced for this assignment.
The data files are sourced from wearable computing and are collected from the accelerometers from the Samsung Galaxy S smartphone.


### Activity 1: Merge the training and the test sets to create one data set ###
### First the datafiles should be downloaded ###


fileDirectory <- "C://Users//648700//Desktop//Coursera"
setwd(fileDirectory)
if(!file.exists("./datapacks")){dir.create("./datapacks")}
FileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(FileUrl,destfile="./datapacks/Datapack.zip")

### Now unzip the datapack to the right folder ###
unzip(zipfile="./datapacks/Datapack.zip",exdir="./datapacks")


### Load 'data.table' package ###
library(data.table)


### Read the data into R ###
### First read Testing tables ###
subject_test <- read.table("./datapacks//UCI HAR Dataset//test//subject_test.txt")
x_test <- read.table("./datapacks//UCI HAR Dataset//test//X_test.txt")
y_test <- read.table("./datapacks//UCI HAR Dataset//test//y_test.txt")


### Secondly read the Training tables ###
subject_train <- read.table("./datapacks//UCI HAR Dataset//train//subject_train.txt")
x_train <- read.table("./datapacks//UCI HAR Dataset//train//X_train.txt")
y_train <- read.table("./datapacks//UCI HAR Dataset//train//y_train.txt")


### Now read activity table ###
activity_labels = read.table("./datapacks/UCI HAR Dataset/activity_labels.txt")


### Finally, read the features table ###
features <- read.table("./datapacks/UCI HAR Dataset/features.txt")


### Giving the tables the right columnnames ###
### Since all data is now read, its should be made a more combined and tidy dataset ###
### First make the variables of x_test and x_train more concrete, by combining with the features table ###
colnames(x_test) <- features[,2]
colnames(x_train) <- features[,2]


### Secondly, the y_test and y_train should have a column name to show that it is representing an ID ###
colnames(y_test) <- "ActivityIDcode"
colnames(y_train) <- "ActivityIDcode"

colnames(subject_test) <- "SubjectIDcode"
colnames(subject_train) <- "SubjectIDcode"


### Finally, the activitylabels table should have column names ###

colnames(activity_labels) <- c("ActivityIDcode", "ActivityDescription")


### Now the seperated tables should be combined to one table for train and test, after which they can be completely combined###
total_test <- cbind(y_test, subject_test, x_test)
total_train <- cbind(y_train, subject_train, x_train)

### Making it one final table ###
Complete_table <- rbind(total_train, total_test)




### Activity 2: Extract only the measurements on the mean and standard deviation for each measurement.###
### First each measurment should be defined ###
Measurements <- colnames(Complete_table)


### We only want the mean and standard deviation ###
Mean_and_standard_deviation <- (grepl("ActivityIDcode", Measurements) |
                                grepl("SubjectIDcode", Measurements) |
                                grepl("Mean", Measurements) |
                                grepl("Standard_Deviation", Measurements)
                                )
                                
                                
### Storing the result in a collection variable ###                                
Mean_and_std_collection <- Complete_table[ , Mean_and_standard_deviation == TRUE]                                





### Activity 4: Using descriptive activity names to name the activities in the data set ###
Total_Set_With_Activity_Label <- merge(Mean_and_std_collection, activity_labels,
                              by='ActivityIDcode',
                              all.x=TRUE)





### Activity 5: Creating a second, independent tidy data set with the average of each variable for each activity and each subject ###
New_data_set <- aggregate(. ~SubjectIDcode + ActivityIDcode, Total_Set_With_Activity_Label, mean)
New_data_set_sorted <- New_data_set[order(New_data_set$SubjectIDcode, New_data_set$ActivityIDcode),]


### Finally, write the new data set ###
write.table(New_data_set_sorted, "New_data_set_sorted.txt", row.names= FALSE)
