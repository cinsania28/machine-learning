# Welcome to my project

For full documentation visit [mkdocs.org](https://www.mkdocs.org).

## Commands

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

***Project layout***

## Import datasets
	data1 <- read.csv("pml-training.csv", stringsAsFactors = FALSE, na.strings = c("", "NA"))
	data2 <- read.csv("pml-testing.csv", stringsAsFactors = FALSE, na.strings = c("", "NA"))

## Ensure 'classe' columns exist in data1
	if (!"classe" %in% colnames(data1)) 

## Drop unnecessary columns
	c("user_name", "raw_timestamp_part_1", "raw_timestamp_part_2", "cvtd_timestamp", "new_window")
	data1 <- data1[, !(colnames(data1) %in% drop_cols)]
	data2 <- data2[, !(colnames(data2) %in% drop_cols)]

## Remove columns with too many missing values (except classe)
	classe_column <- data1$classe  # Preserve classe
	data1 <- data1[, colSums(is.na(data1)) == 0]
	data1$classe <- classe_column  # Restore classe
	data2 <- data2[, colSums(is.na(data2)) == 0]

## Ensure consistency: Keep only common columns + classe in data1
	common_cols <- intersect(colnames(data1), colnames(data2))
	data1 <- data1[, c(common_cols, "classe")]
	data2 <- data2[, common_cols]  # data2 doesn't have classe

## Convert classe to factor
	data1$classe <- as.factor(data1$classe)

## Split into training and testing sets
	set.seed(123)
	index1 <- sample(1:nrow(data1), size = 0.7 * nrow(data1))
	train_data <- data1[index1, ]
	test_data <- data1[-index1, ]

## Train Random Forest model
	rf_model <- randomForest(classe ~ ., data = train_data, ntree = 500, mtry = 5)

## Print model summery
	print(rf_model)

## Predict on test set
	test_predictions <- predict(rf_model, newdata = test_data)

##  Evaluate accuracy
	accuracy <- mean(test_predictions == test_data$classe)
	print(paste("Model Accuracy on Test Data:", round(accuracy * 100, 2), "%"))

## Ensure test data matches training data structure (excluding classe)
	data2 <- data2[, colnames(train_data)[colnames(train_data) != "classe"]]

## Handle missing values in data2 by replacing them with column medians
	for (col in names(data2)) 
	{data2[[col]][is.na(data2[[col]])] <- median(data2[[col]], na.rm = TRUE)}

## Make final predictions on data2
	final_predictions <- predict(rf_model, newdata = data2)

## Print predictions
	print(final_predictions)

## Final predictions
	1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
	A  A  A  A  A  A  A  A  A  A  A  A  B  A  A  A  A  B  A  B 
	Levels: A B C D E

***"Model Accuracy on Test Data: 99.97 %"***
	
