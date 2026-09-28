Is the shape of the matrices for the multiplication correct? To clarify, if the key matrix was not transposed we wouldn't be multiplying cat with cat, cat with on and cat with mat for example. In other words, the rows of query matrix and columns of key matrix represent the token embeddings.

am i correct to assume that intransformer architecture the dimension 0 of Weighted Query matrix is about tense for example, then the dimension 0 of weighted matrix Key and weighted matrix Value is also the same i.e. tense. I'm aware that dimension 0 can be mapped to clean, isolated concept like tense. I'm interested to know if the concept will be the same for dimension 0 for all 3 matrices.

To help tailor this to your research, could you share:Are you working on a mechanistic interpretability project (like using probes or steering vectors)?

