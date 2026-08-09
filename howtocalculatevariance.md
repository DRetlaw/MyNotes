The screenshot is explaining how variance is calculated. This is especially relevant to your recent question about Layer Normalization, because LayerNorm uses variance to measure how spread out the values are.

Simple example

Suppose a neuron/token has these values:

[2, 4, 6]
1. Find the mean
μ=
3
2+4+6
	​

=4
2. Subtract the mean
[2−4, 4−4, 6−4]=[−2, 0, 2]

These are called the deviations from the mean.

3. Square them
[−2]
2
, [0]
2
, [2]
2
=[4, 0, 4]

We square them because otherwise the negative and positive deviations would cancel each other.

4. Add them
4+0+4=8
5. Divide by the number of values

For a population:

Variance=
3
8
	​

=2.67

So:

Variance tells us how spread out the values are around their mean.

Why this matters for LayerNorm

If a transformer has a vector like:

x=[2,4,6]

LayerNorm essentially calculates:

μ=4
σ
2
=2.67

and then normalizes each value using:

x
^
i
	​

=
σ
2
+ϵ
	​

x
i
	​

−μ
	​


So the overall idea is:

Values → mean → deviations → variance → normalize

The key distinction to remember is:

Mean → where the values are centered
Variance → how spread out they are
Standard deviation → square root of variance
LayerNorm → uses mean and variance to normalize the values within each token's embedding vector.
