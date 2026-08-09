The screenshot is explaining Residual Connection + Layer Normalization in a Transformer.

In simple terms, the flow shown is:

Input → Attention → Add original input → Layer Normalization

For the example in the image:

    Original input
    X=[1.0,2.0,3.0]

    Attention output
    Attention(X)=[0.5,−1.0,1.5]

    Residual connection
    Add the original input back:
    X+Attention(X) =[1.0+0.5,2.0−1.0,3.0+1.5] =[1.5,1.0,4.5]

    Layer Normalization

    Calculate the mean:
    μ=31.5+1.0+4.5​=2.33

    Calculate variance and standard deviation, then normalize each element:
    xnorm​=σx−μ​

    giving approximately:
    [−0.54,−0.86,1.40]

The key idea

The residual connection:
X+Attention(X)​

lets the original information skip around the attention layer and be combined with the attention result.

The LayerNorm then normalizes that combined vector.

