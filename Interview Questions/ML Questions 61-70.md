61. How do we deal with sparsity issues in recommendation systems? How do we measure its effectiveness? Explain. 
62. Name and define techniques used to find similarities in the recommendation system. 
63. State the limitations of Fixed Basis Function.
64. Define and explain the concept of Inductive Bias with some examples.
65. Explain the term instance-based learning.
66. Keeping train and test split criteria in mind, is it good to perform scaling before the split or after the split? 
67. Define precision, recall and F1 Score?

### Precision
- **Precision** measures the accuracy of positive predictions. It's the ratio of true positive predictions to the total number of positive predictions (both true positives and false positives). It answers the question, "Of all the instances classified as positive, how many are actually positive?"
  - Formula: \( \text{Precision} = \frac{\text{True Positives}}{\text{True Positives} + \text{False Positives}} \)

### Recall
- **Recall** measures the ability of a model to find all the relevant cases within a dataset. It's the ratio of true positive predictions to the total number of actual positives (the sum of true positives and false negatives). It answers the question, "Of all the actual positives, how many were identified correctly?"
  - Formula: \( \text{Recall} = \frac{\text{True Positives}}{\text{True Positives} + \text{False Negatives}} \)

### F1 Score
- **F1 Score** is the harmonic mean of precision and recall. It's a single metric that balances both the precision and recall of a model, which is particularly useful when the costs of false positives and false negatives are very different. The F1 Score is higher when both precision and recall are high, indicating a balanced, accurate, and comprehensive model.
  - Formula: \( \text{F1 Score} = 2 \times \frac{\text{Precision} \times \text{Recall}}{\text{Precision} + \text{Recall}} \)


68. Plot validation score and training score with data set size on the x-axis and another plot with model complexity on the x-axis.
69. What is Bayes’ Theorem? State at least 1 use case with respect to the machine learning context?
70. What is Naive Bayes? Why is it Naive?