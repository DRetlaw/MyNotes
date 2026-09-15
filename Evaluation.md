
https://cwpc.in/machine-learning-model-evaluation-metrics-explained-efec02abc738

1. Accuracy
Accuracy = Correct predictions / Total predictions
If the model gets 90 out of 100 predictions correct Accuracy = 90%
Best used when the dataset is balanced.

2. Precision
Precision measures how many of the predicted positives were actually positive.
Useful when false positives must be avoided.
Example: Spam detection

3. Recall
Recall measures how many of the actual positives were correctly identified.

4. F1-Score Balance between Precision and Recall
In other words, F1 score is a performance metric that combines a classification model's precision and recall into a single number. Best used when the dataset is imbalanced.

5. Confusion Matrix
A 2x2 table of all prediction results

6. ROC-AUC
How well does the model separate classes?
    ROC Curve - True Positive Rate vs False Positive Rate
    AUC Score - Area under ROC curve (0.5 = poor, 1.0 = perfect)
Higher AUC = better model performance.

https://medium.com/@pur4v/understanding-llm-evaluation-metrics-bleu-rouge-exact-match-and-bertscore-716487e40bdd

BLEU (Bilingual Evaluation Understudy) - best for translation
ROUGE (Recall-Oriented Understudy for Gisting Evaluation) - best for summarization
Exact Match (EM) - Factual QA, Classification
BERTScore - Paraphrasing and open end response
Unit tests - code generation



