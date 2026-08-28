# XOR Neural Network Training Exercise

## Purpose

This exercise used the XOR neural-network playground to experimentally understand how different training choices affect a neural network.

The main concepts explored were:

- Learning rate
- Epochs
- Number of hidden neurons
- Activation functions
- Non-linearity
- Model failure
- Interaction between hyperparameters

---

# 1. Baseline Network Configuration

The successful baseline configuration used:

```text
Hidden Layer 1
- Neurons: 4
- Activation: Tanh

Output Layer
- Neurons: 1
- Activation: Sigmoid
```

The XOR problem is:

| A | B | XOR |
|---|---|---:|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

The objective of training was to adjust the network's weights so that it correctly learned this XOR pattern.

---

# 2. Experiment 1 — Learning Rate

The first experiment investigated the effect of the learning rate.

The number of epochs was kept constant at **500**, while the learning rate was changed.

## Results

| Learning Rate | Epochs | Loss |
|---:|---:|---:|
| 0.001 | 500 | 0.50748 |
| 0.01 | 500 | 0.02095 |
| 0.1 | 500 | 0.00016 |

## Observation

```text
LR = 0.001 → Loss = 0.50748
LR = 0.01  → Loss = 0.02095
LR = 0.1   → Loss = 0.00016
```

For the Tanh configuration, increasing the learning rate significantly improved convergence within 500 epochs.

### Key lesson

The learning rate controls the size of the weight update:

```text
New Weight = Old Weight - Learning Rate × Gradient
```

Conceptually:

```text
Small learning rate
        ↓
Tiny weight updates
        ↓
Slow learning

Appropriate learning rate
        ↓
Effective weight updates
        ↓
Fast convergence
```

For this particular Tanh-based XOR network, `0.1` performed best among the available learning-rate values.

---

# 3. Experiment 2A — Effect of Epochs

The learning rate was fixed at:

```text
Learning Rate = 0.1
```

The network architecture remained:

```text
Hidden neurons = 4
Hidden activation = Tanh
Output activation = Sigmoid
```

## Results

| Epochs | Loss |
|---:|---:|
| 50 | 0.01528 |
| 100 | 0.00401 |
| 200 | 0.00161 |
| 500 | 0.00016 |

## Observation

```text
50 epochs
Loss = 0.01528

100 epochs
Loss = 0.00401

200 epochs
Loss = 0.00161

500 epochs
Loss = 0.00016
```

As the number of epochs increased, the loss continued to decrease.

### Key lesson

Epochs determine how long the model is allowed to train.

```text
More epochs
      ↓
More weight updates
      ↓
More opportunity to reduce loss
```

However, after a model has already learned the problem well, additional epochs may produce smaller and smaller improvements.

This introduces the idea of **diminishing returns** and, in larger machine-learning problems, concepts such as **early stopping**.

---

# 4. Experiment 2B — Number of Hidden Neurons

The following configuration was used:

```text
Learning Rate = 0.1
Epochs = 500
Hidden activation = Tanh
Output activation = Sigmoid
```

The number of hidden neurons was changed.

The playground did not provide an option for 1 hidden neuron.

## Results

| Hidden Neurons | Loss |
|---:|---:|
| 2 | 0.00076 |
| 4 | 0.00016 |
| 6 | 0.00010 |
| 8 | 0.00010 |

## Observation

Increasing the number of neurons initially improved the final loss:

```text
2 neurons → 0.00076
4 neurons → 0.00016
6 neurons → 0.00010
8 neurons → 0.00010
```

However, the improvement from 6 to 8 neurons was essentially negligible.

### Key lesson

More neurons provide more model capacity:

```text
More neurons
      ↓
More parameters
      ↓
Greater ability to represent complex patterns
```

But:

> More neurons are not automatically better.

For the simple XOR problem, adding many more neurons eventually produces diminishing returns.

---

# 5. Experiment 3 — Activation Functions

The baseline architecture was kept at:

```text
Hidden neurons = 4
Learning Rate = 0.1
Epochs = 500
Output activation = Sigmoid
```

The hidden-layer activation function was changed.

## Results

| Hidden Activation | Loss |
|---|---:|
| Tanh | 0.00016 |
| Sigmoid | 0.00202 |
| ReLU | 0.47741 |
| Linear | 0.69315 |

---

## 5.1 Tanh

```text
Loss = 0.00016
```

Tanh performed extremely well for this XOR configuration.

Tanh provides a non-linear transformation, allowing the network to create the non-linear decision boundary needed for XOR.

---

## 5.2 Sigmoid

```text
Loss = 0.00202
```

Sigmoid also learned XOR successfully.

### Lesson

XOR does not specifically require Tanh.

The important requirement is the introduction of **non-linearity**.

Both Tanh and Sigmoid provide non-linear transformations.

---

## 5.3 ReLU

```text
Loss = 0.47741
```

Initially, ReLU performed poorly with:

```text
Learning Rate = 0.1
Epochs = 500
Hidden neurons = 4
```

This did **not** mean that ReLU cannot solve XOR.

Further experiments were performed.

---

# 6. Experiment 4 — Failure Combination

A deliberately weak configuration was tested:

```text
Hidden neurons = 2
Hidden activation = Linear
Learning Rate = 0.1
Epochs = 500
```

## Result

```text
Epoch 500
Loss = 0.69315
Wrong predictions
```

## Why did it fail?

XOR is not linearly separable.

Conceptually:

```text
Class 1        Class 0

   ●              ●


   ●              ●
```

A single straight line cannot separate the two XOR classes.

Multiple linear transformations are still equivalent to an overall linear transformation.

Conceptually:

```text
Linear Layer
      ↓
Linear Layer
      ↓
Linear Layer

= Overall linear transformation
```

### Key lesson

> Adding more layers or neurons without introducing non-linearity does not give the network the ability to solve a non-linear problem such as XOR.

This is one of the fundamental reasons activation functions are necessary.

---

# 7. Experiment 5 — Investigating the ReLU Result

The original ReLU experiment produced:

```text
Hidden neurons = 4
Activation = ReLU
Output = Sigmoid
Learning Rate = 0.1
Epochs = 500

Loss = 0.47741
```

To investigate this, the learning rate and number of hidden neurons were changed.

## Results

| Hidden Neurons | Learning Rate | Epochs | Loss |
|---:|---:|---:|---:|
| 4 | 0.1 | 500 | 0.47741 |
| 4 | 0.01 | 500 | 0.01550 |
| 4 | 0.001 | 500 | 0.56185 |
| 6 | 0.01 | 500 | 0.00887 |
| 6 | 0.001 | 500 | 0.53915 |

---

# 8. What the ReLU Experiment Demonstrated

For 4 hidden neurons:

```text
LR = 0.001 → Loss = 0.56185
LR = 0.01  → Loss = 0.01550
LR = 0.1   → Loss = 0.47741
```

This creates a useful pattern:

```text
Too small             Suitable              Too large
LR = 0.001             LR = 0.01             LR = 0.1

Slow learning          Good convergence      Poor convergence
Loss 0.56185           Loss 0.01550          Loss 0.47741
```

### Key lesson

This experiment disproved the idea that:

> Higher learning rate is always better.

A better conclusion is:

> The best learning rate depends on the model architecture, activation function, initialization, optimizer, dataset, and training budget.

For this ReLU experiment, `0.01` was much more effective than either `0.001` or `0.1`.

---

# 9. Effect of More ReLU Neurons

Compare:

| Hidden Neurons | Learning Rate | Loss |
|---:|---:|---:|
| 4 | 0.01 | 0.01550 |
| 6 | 0.01 | 0.00887 |

Increasing the number of neurons improved the result.

However, compare the effect of changing the learning rate:

```text
4 neurons, LR = 0.001 → Loss = 0.56185
4 neurons, LR = 0.01  → Loss = 0.01550
```

The learning-rate change had a much larger effect than simply adding two additional neurons.

### Lesson

Hyperparameters interact with each other.

The number of neurons alone does not determine whether training will succeed.

---

# 10. Possible Explanation for the ReLU Failure at LR = 0.1

ReLU behaves as follows:

```text
If x < 0 → ReLU(x) = 0
If x > 0 → ReLU(x) = x
```

A large weight update may push some neurons into regions where their activation is zero for the training inputs.

Conceptually:

```text
Large weight update
        ↓
Neuron input becomes negative
        ↓
ReLU output becomes 0
        ↓
Neuron contributes little or no useful signal
```

This can be related to the **dying ReLU** phenomenon.

However, the playground does not expose the internal weights and activations, so this cannot be confirmed as the exact cause of the poor result.

The confirmed observation is:

> `LR = 0.1` was not an effective learning rate for ReLU in this specific configuration and 500-epoch training budget.

---

# 11. Summary of All Major Experiments

## Learning Rate with Tanh

| Learning Rate | Epochs | Loss |
|---:|---:|---:|
| 0.001 | 500 | 0.50748 |
| 0.01 | 500 | 0.02095 |
| 0.1 | 500 | 0.00016 |

---

## Epochs with Tanh and LR = 0.1

| Epochs | Loss |
|---:|---:|
| 50 | 0.01528 |
| 100 | 0.00401 |
| 200 | 0.00161 |
| 500 | 0.00016 |

---

## Hidden Neurons with Tanh

| Hidden Neurons | Loss |
|---:|---:|
| 2 | 0.00076 |
| 4 | 0.00016 |
| 6 | 0.00010 |
| 8 | 0.00010 |

---

## Activation Functions

| Activation | Loss |
|---|---:|
| Tanh | 0.00016 |
| Sigmoid | 0.00202 |
| ReLU | 0.47741 |
| Linear | 0.69315 |

---

## ReLU Investigation

| Hidden Neurons | LR | Loss |
|---:|---:|---:|
| 4 | 0.001 | 0.56185 |
| 4 | 0.01 | 0.01550 |
| 4 | 0.1 | 0.47741 |
| 6 | 0.001 | 0.53915 |
| 6 | 0.01 | 0.00887 |

---

# 12. Main Lessons Learned

## 1. Learning Rate 🎚️

The learning rate controls the size of weight updates.

```text
Too small
    ↓
Learning is slow

Appropriate
    ↓
Good convergence

Too large
    ↓
Training may become unstable or converge poorly
```

The best learning rate is not universal.

It depends on the particular model and training setup.

---

## 2. Epochs ⏳

Epochs determine how long the model trains.

```text
More epochs
      ↓
More opportunities to update weights
      ↓
Potentially lower training loss
```

Eventually, improvements may become smaller.

---

## 3. Hidden Neurons 🧠

The number of neurons affects model capacity.

```text
Too little capacity
        ↓
May struggle to represent the problem

Enough capacity
        ↓
Can learn the required pattern

Too much capacity
        ↓
May provide little additional benefit
```

For XOR, relatively few neurons are sufficient.

---

## 4. Activation Functions ⚡

Activation functions introduce non-linearity.

This is essential for solving non-linearly separable problems such as XOR.

The Linear experiment demonstrated:

```text
Loss = 0.69315
Wrong predictions
```

while non-linear activations such as Tanh and Sigmoid successfully learned the XOR pattern.

---

## 5. Hyperparameters Interact 🔗

The experiments demonstrated that neural-network training is influenced by multiple factors simultaneously.

```text
                 Training Result
                        ▲
                        │
        ┌───────────────┼───────────────┐
        │               │               │
Learning Rate        Epochs        Architecture
        │               │               │
        └───────────────┼───────────────┘
                        │
                Activation Function
                        │
                        ▼
                  Model Behavior
```

Changing one parameter can change how another parameter behaves.

For example:

```text
Tanh + LR 0.1 → Excellent result

ReLU + LR 0.1 → Poor result

ReLU + LR 0.01 → Good result
```

Therefore:

> Neural-network training is not about finding one universally "best" setting. It is about finding a combination of settings that works well together.

---

# 13. Final Takeaway

This XOR exercise demonstrated the complete basic training loop of a neural network:

```text
Input
   ↓
Forward Pass
   ↓
Prediction
   ↓
Calculate Loss
   ↓
Calculate Gradients
   ↓
Update Weights
   ↑
   │
Repeat for many epochs
```

The experiments showed how different choices affect this process:

```text
Learning Rate → Size of each update

Epochs → Number of learning opportunities

Hidden Neurons → Model capacity

Activation Function → Ability to learn non-linear patterns
```

The most important lesson from the exercise is:

> A neural network does not learn simply because it has many neurons or many layers. Successful learning depends on the interaction between architecture, activation functions, learning rate, initialization, optimization, and training duration.

---

# Suggested Next Step

The next stage is to recreate these same experiments in Python.

That will provide direct control over:

- Learning rates beyond `0.1`
- Arbitrary numbers of epochs
- Random seeds and initialization
- Network architecture
- Activation functions
- Weight values
- Predictions
- Training and loss curves

The XOR playground provided a visual introduction to neural-network training. Recreating the experiments in Python will make the training process observable and programmable at a deeper level.
