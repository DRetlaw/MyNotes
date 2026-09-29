Teacher forcing
Time steps
Dense and sparse vectors
Overfitting
Underfitting
F1 score
Precision
Distillation


Teacher forcing is a training technique for sequential models like Transformers where the ground-truth token from the training dataset is fed as the input for the next time step, instead of the model's own prediction.

a time step = one item's position in a sequence (one word, one data point, one moment in time). It's just a way of saying "this is the Nth thing in the sequence."

Sparse vectors optimize keyword matching and precision.
Dense vectors optimize for semantic meaning and conceptual recall.

Overfitting - memorizing without understanding. he model is too complex and learns the noise instead of the general pattern.
Underfitting - you did not study enough. The model is too simple to capture the underlying patterns in the data.
Overfitting vs. Underfitting are two core problems in machine learning where a model fails to generalize to new, unseen data.

F1 score is a performance metric that combines a classification model's precision and recall into a single number

Distillation in AI is a machine learning technique used to transfer the knowledge and capabilities of a large, complex "teacher" model into a smaller, faster, and more affordable "student" model

Attention Weights: The final percentages or probabilities obtained by passing the raw attention scores through a softmax function.\(\text{Weights} = \text{softmax}\left(\frac{Q K^T}{\sqrt{d_k}}\right)\) University of Southern California

Attention Scores: The raw dot product of a query (Q) and a key (K), usually scaled down by the square root of the key dimension (\[\sqrt{d_{k}}\]).