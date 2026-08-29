# GPT-2 Small — Matrices, Shapes, and Training vs Inference

## Model dimensions

| Parameter | Value |
|---|---:|
| Vocabulary size | 50,257 |
| Context length | 1,024 |
| Embedding dimension (`d_model`) | 768 |
| Transformer layers | 12 |
| Attention heads | 12 |
| Head dimension (`d_head`) | 64 |

## Matrices and tensors

The table below separates **stored model parameters** from **runtime tensors** and shows what happens to each during training and inference.

| Matrix / Tensor | Shape in this example | Type | Training stage | Inference stage |
|---|---:|---|---|---|
| Token embedding `E` | `(50,257, 768)` | Model weight | Learned/updated during backpropagation | Fixed; used for token lookup |
| Positional embedding `P` | `(1,024, 768)` | Model weight | Learned/updated during backpropagation | Fixed; used for position lookup |
| Token embeddings | `(L, 768)` | Runtime tensor | Created from token IDs | Created from token IDs |
| Positional embeddings | `(L, 768)` | Runtime tensor | Created from position IDs | Created from position IDs |
| Hidden representation `X` | `(L, 768)` | Runtime tensor | Token + positional embeddings; then passed through Transformer blocks | Same calculation; weights are fixed |
| LayerNorm parameters | `(768)` each | Model weights | Learned/updated | Fixed |
| Query projection `W_Q` | `(768, 768)` | Model weight | Learned/updated | Fixed |
| Key projection `W_K` | `(768, 768)` | Model weight | Learned/updated | Fixed |
| Value projection `W_V` | `(768, 768)` | Model weight | Learned/updated | Fixed |
| Query `Q` | `(L, 768)` | Runtime tensor | `X_norm × W_Q`; participates in attention and backpropagation | `X_norm × W_Q`; calculated using fixed weights |
| Key `K` | `(L, 768)` | Runtime tensor | `X_norm × W_K`; participates in attention and backpropagation | `X_norm × W_K`; cached during generation |
| Value `V` | `(L, 768)` | Runtime tensor | `X_norm × W_V`; participates in attention and backpropagation | `X_norm × W_V`; cached during generation |
| Q per attention head | `(L, 64)` | Runtime tensor | Split from Q into 12 heads | Split from Q into 12 heads |
| K per attention head | `(L, 64)` | Runtime tensor | Split from K into 12 heads | Split from K into 12 heads; previous K stored in KV cache |
| V per attention head | `(L, 64)` | Runtime tensor | Split from V into 12 heads | Split from V into 12 heads; previous V stored in KV cache |
| Attention scores `QKᵀ` | `(L, L)` per head | Runtime tensor | Calculated for the training sequence | Calculated for the current token against cached keys |
| Causal mask | `(L, L)` | Runtime tensor / operation | Prevents each token from seeing future tokens | Ensures autoregressive generation |
| Attention probabilities | `(L, L)` per head | Runtime tensor | Softmax of masked/scaled attention scores | Softmax of masked/scaled attention scores |
| Attention output per head | `(L, 64)` | Runtime tensor | Attention probabilities × V | Attention probabilities × cached V |
| Concatenated attention output | `(L, 768)` | Runtime tensor | 12 heads concatenated | 12 heads concatenated |
| Output projection `W_O` | `(768, 768)` | Model weight | Learned/updated | Fixed |
| Attention output after `W_O` | `(L, 768)` | Runtime tensor | Projected and added through residual connection | Projected and added through residual connection |
| FFN weights | `(768, 3,072)` and `(3,072, 768)` | Model weights | Learned/updated | Fixed |
| FFN intermediate representation | `(L, 3,072)` | Runtime tensor | Calculated during forward pass | Calculated during inference |
| Final hidden representation | `(L, 768)` | Runtime tensor | Used to calculate logits and loss | Used to calculate next-token logits |
| Language-model output weights | `(768, 50,257)` conceptually | Model weight | Learned/updated (GPT-2 ties this with token embeddings) | Fixed |
| Logits | `(L, 50,257)` during full-sequence processing | Runtime tensor | Compared with target tokens to calculate loss | Used to select/sample the next token |

> **Note:** `L` means sequence length. For the simple example `"The cat sat"`, `L = 3`.

## Training vs inference

| Training / Inference | Training | Inference |
|---|---|---|
| Model weights | Updated | Fixed |
| Token embedding `E` | Updated | Fixed |
| Positional embedding `P` | Updated | Fixed |
| `W_Q`, `W_K`, `W_V` | Updated | Fixed |
| Q, K, V | Calculated as runtime tensors | Calculated as runtime tensors |
| Attention scores | Calculated | Calculated |
| Causal mask | Applied | Applied |
| KV cache | Generally not required | **Used for autoregressive generation** |
| Loss | Calculated | Not calculated for normal generation |
| Backpropagation | Yes | No |
| Optimizer | Updates parameters | Not used |
| Main objective | Learn model parameters | Generate the next token |

## QKV flow

For a sequence of length `L`:

```text
X
│
├── LayerNorm
│
├── × W_Q ──→ Q
│
├── × W_K ──→ K
│
└── × W_V ──→ V
```

Shapes:

```text
X_norm  = (L, 768)

W_Q     = (768, 768)
W_K     = (768, 768)
W_V     = (768, 768)

Q       = (L, 768)
K       = (L, 768)
V       = (L, 768)
```

Then Q, K and V are divided across 12 attention heads:

```text
Q → 12 × (L, 64)
K → 12 × (L, 64)
V → 12 × (L, 64)
```

For one head:

```text
Q       = (L, 64)
Kᵀ      = (64, L)

QKᵀ     = (L, L)

Attention probabilities = (L, L)

V       = (L, 64)

Attention output = (L, 64)
```

The 12 head outputs are concatenated:

```text
12 × (L, 64)
       ↓
   (L, 768)
       ↓
   × W_O
       ↓
   (L, 768)
```

## The key idea

### Stored in GPT-2

```text
E
P
W_Q
W_K
W_V
W_O
FFN weights
LayerNorm parameters
...
```

These are **model parameters**.

### Created during a forward pass

```text
X
Q
K
V
attention scores
attention probabilities
attention outputs
logits
...
```

These are **runtime activations/tensors**.

During **training**, the model uses the runtime tensors to calculate the loss and backpropagates the error to update the stored parameters.

During **inference**, the same stored parameters are used to calculate the runtime tensors, but the parameters remain fixed and no backpropagation occurs.

## KV cache during inference

For autoregressive generation, previously calculated keys and values are retained:

```text
Prompt:
The cat sat

K cache:
K(The), K(cat), K(sat)

V cache:
V(The), V(cat), V(sat)
```

When the next token `on` is processed, GPT-2 calculates:

```text
Q(on)
K(on)
V(on)
```

and appends the new `K(on)` and `V(on)` to the cache.

The new query can then attend to:

```text
K(The), K(cat), K(sat), K(on)
```

using the corresponding values:

```text
V(The), V(cat), V(sat), V(on)
```

This avoids recalculating the keys and values for all previous tokens at every generation step.
