Exercise Training model
https://chatgpt.com/c/6a9fb2f9-990c-83ee-8aec-8ede9f7d4da6

https://www.playwithml.com/projects/iris-flower


Baseline run
============
Epoch #: 200 Loss: 0.07712

Evaluation:
Accuracy: 0.78

F1-Score: 0.78

Precision: 0.78

Recall (Sensitivity): 0.78

Confusion Matrix
                Predicted Positive          Predicted Negative
Actual Positive	True Positives (TP): 35	    False Negatives (FN): 10
Actual Negative	False Positives (FP): 10    True Negatives (TN): 80


Prediction
Test Data 1 (Setosa)
Sepal Length (cm): 5.1
Sepal Width (cm): 3.5
Petal Length (cm): 1.4
Petal Width (cm): 0.2

Setosa
Setosa: 99.0%
Versicolor: 1.0%
Virginica: 0.0%


Test Data 2 (Versicolor)
Sepal Length (cm): 7
Sepal Width (cm): 3.2
Petal Length (cm): 4.7
Petal Width (cm): 1.4

Versicolor
Setosa: 3.1%
Versicolor: 96.9%
Virginica: 0.0%

Test Data 3 (Virginica)
Sepal Length (cm): 6.3
Sepal Width (cm): 3.3
Petal Length (cm): 6
Petal Width (cm): 2.5

Virginica
Setosa: 0.0%
Versicolor: 34.3%
Virginica: 65.7%


Experiment 1A
=============
Change: Neurons 2 -> 2
Epoch #: 200 Loss: 0.85377

Accuracy: 0.00

F1-Score: 0.00

Precision: 0.00

Recall (Sensitivity): 0.00

Confusion Matrix
Predicted Positive	Predicted Negative
Actual Positive	True Positives (TP): 0	False Negatives (FN): 45
Actual Negative	False Positives (FP): 45	True Negatives (TN): 45

Test Data 1 (Setosa)
Setosa
Setosa: 48.2%
Versicolor: 47.0%
Virginica: 4.8%

Test Data 2 (Versicolor)
Setosa
Setosa: 48.2%
Versicolor: 47.0%
Virginica: 4.8%

Test Data 3 (Virginica)
Setosa
Setosa: 48.2%
Versicolor: 47.0%
Virginica: 4.8%

Experiment 1B
=============
Change: Neurons 3 -> 3

Epoch #: 200 Loss: 0.18234

Accuracy: 1.00

F1-Score: 1.00

Precision: 1.00

Recall (Sensitivity): 1.00

Confusion Matrix
Predicted Positive	Predicted Negative
Actual Positive	True Positives (TP): 45	False Negatives (FN): 0
Actual Negative	False Positives (FP): 0	True Negatives (TN): 90

Prediction
Test Data 1 (Setosa)
Sepal Length (cm): 5.1
Sepal Width (cm): 3.5
Petal Length (cm): 1.4
Petal Width (cm): 0.2

Setosa
Setosa: 100.0%
Versicolor: 0.0%
Virginica: 0.0%


Test Data 2 (Versicolor)
Sepal Length (cm): 7
Sepal Width (cm): 3.2
Petal Length (cm): 4.7
Petal Width (cm): 1.4

Versicolor
Setosa: 0.0%
Versicolor: 100.0%
Virginica: 0.0%

Test Data 3 (Virginica)
Sepal Length (cm): 6.3
Sepal Width (cm): 3.3
Petal Length (cm): 6
Petal Width (cm): 2.5

Virginica
Setosa: 0.0%
Versicolor: 34.3%
Virginica: 65.7%

Virginica
Setosa: 0.4%
Versicolor: 3.2%
Virginica: 96.5%

Experiment 1C
=============
Change: Neurons 4 -> 4

Epoch #: 200 Loss: 0.02393

Accuracy: 0.64

F1-Score: 0.64

Precision: 0.64

Recall (Sensitivity): 0.64

Confusion Matrix
Predicted Positive	Predicted Negative
Actual Positive	True Positives (TP): 29	False Negatives (FN): 16
Actual Negative	False Positives (FP): 16	True Negatives (TN): 74

Test Data 1 (Setosa)
Setosa
Setosa: 99.2%
Versicolor: 0.8%
Virginica: 0.0%

Test Data 2 (Versicolor)
Versicolor
Setosa: 0.0%
Versicolor: 99.2%
Virginica: 0.8%

Test Data 3 (Virginica)
Virginica
Setosa: 0.0%
Versicolor: 0.7%
Virginica: 99.3%


Experiment 1D
=============
Change: Neurons 5 -> 5

Test Data 1 (Setosa)
Test Data 2 (Versicolor)
Test Data 3 (Virginica)

Experiment 1E
=============
Change: Neurons 6 -> 6

Test Data 1 (Setosa)
Test Data 2 (Versicolor)
Test Data 3 (Virginica)
