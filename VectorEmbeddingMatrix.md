Text → Tokenizer → Token IDs → Token Embedding Matrix → Embedding Vectors

For example:

"Good morning to everyone"

might become token IDs such as:

[5, 112, 71, 896]

Then those IDs are used as row indices into the token embedding matrix.

For GPT-2:

Vocabulary size = 50,257
Embedding dimension = 768
Token embedding matrix = 50,257 × 768
Each row corresponds to one token
Each token ID selects one row

So if:

Token IDs = [5, 112, 71, 896]

then:

ID 5    → Embedding Matrix row 5    → vector of 768 numbers
ID 112  → Embedding Matrix row 112  → vector of 768 numbers
ID 71   → Embedding Matrix row 71   → vector of 768 numbers
ID 896  → Embedding Matrix row 896  → vector of 768 numbers

The final output is therefore:

4 tokens × 768 dimensions

        768
     ┌─────────┐
ID 5 │ vector  │
ID112│ vector  │
ID 71│ vector  │
ID896│ vector  │
     └─────────┘
       4 × 768

Your note “the number of Tokenizer Table's rows matches the number of rows in the Token Embedding Matrix” is essentially correct: both use the same vocabulary of 50,257 token IDs.

If you want, I can also explain the exact difference between the Tokenizer vocabulary table and the 50,257 × 768 embedding matrix using this same diagram.


