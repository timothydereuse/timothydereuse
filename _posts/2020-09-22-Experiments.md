---
published: true
---
## (Sept-Oct 2020: Bidirectional Transformer)

These experiments involve training a bidirectional transformer (similar to the BERT architecture) on an error correction task.

Base parameters:

- Sequence Length: 40
- Num. Layers: 6
- Num. Attention Heads: 4
- Hidden Dimension: 256
- Masked sequence elements: 15%
- Randomized sequence elements: 1.5%

![attn v duration.png]({{site.baseurl}}/_posts/attn v duration.png)

![attn v pitch.png]({{site.baseurl}}/_posts/attn v pitch.png)

![layers v duration.png]({{site.baseurl}}/_posts/layers v duration.png)

![layers v pitch.png]({{site.baseurl}}/_posts/layers v pitch.png)




