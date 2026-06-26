# Attention

## What is it?
Attention is a mechanism that tells the model which parts of the input are important. Instead of processing every word equally, attention assigns weights (scores) — higher weights to relevant words, lower to irrelevant ones. Scaled Dot-Product Attention computes Query-Key dot products, scales by √d, applies softmax, then multiplies by Values.

## Why do we use it?
Before attention, RNNs processed sequences step-by-step and forgot distant information. Attention solves this by giving the model direct access to any word at any position. It's essential for translation (maintaining context), summarization (picking key points), and all modern LLMs.

## Real-Time Example 1
**Search Engine Query** — Google Search uses attention-like ranking. Step 1: You search "best laptops for programming". Step 2: Attention-like algorithm scores each webpage for relevance. Step 3: Pages with "laptop", "programming", "best" get higher scores. Step 4: Softmax converts scores to probabilities. Step 5: Top results are shown. The mechanism is similar to how attention works in transformers.

## Real-Time Example 2
**Video Captioning** — YouTube uses attention for automatic captions. Step 1: Audio is converted to text (speech recognition). Step 2: Attention aligns spoken words with video timestamps. Step 3: "Next slide please" might attend to timestamp 2:30 where the presenter gestures. Step 4: Captions are synced to video frames. Step 5: Users with hearing impairments get accurate captions.

## Concept Example 1
**Scaled Dot-Product Attention**
```python
import torch
import torch.nn.functional as F

def scaled_dot_product_attention(Q, K, V):
    # Q, K, V shape: (batch, seq_len, d_k)
    scores = torch.matmul(Q, K.transpose(-2, -1))  # (batch, seq_len, seq_len)
    d_k = torch.tensor(K.size(-1), dtype=torch.float32)
    scores = scores / torch.sqrt(d_k)  # Scale by sqrt(d_k)
    attention_weights = F.softmax(scores, dim=-1)
    output = torch.matmul(attention_weights, V)
    return output, attention_weights

# Example: 3 tokens, d_k=4
Q = torch.tensor([[[1, 0, 1, 0], [0, 1, 0, 1], [1, 0, 0, 1]]], dtype=torch.float32)
K = Q.clone()
V = Q.clone()

output, weights = scaled_dot_product_attention(Q, K, V)
print("Attention weights:\n", weights)
# Each row shows how much each token attends to other tokens
```
**Input:** Q, K, V each of shape (1, 3, 4)
**Output:** Attention weights matrix (1, 3, 3), Output (1, 3, 4)
**Explanation:** Token 1 attends to itself (0.5) and token 3 (0.4) the most, indicating high relevance. Token 2 might focus on different tokens. The scaling by √d_k prevents softmax from entering regions with extremely small gradients.

## Concept Example 2
**Multi-Head Attention**
```python
import torch
import torch.nn as nn

class MultiHeadAttention(nn.Module):
    def __init__(self, d_model=512, num_heads=8):
        super().__init__()
        self.num_heads = num_heads
        self.d_k = d_model // num_heads
        self.W_q = nn.Linear(d_model, d_model)
        self.W_k = nn.Linear(d_model, d_model)
        self.W_v = nn.Linear(d_model, d_model)
        self.W_o = nn.Linear(d_model, d_model)

    def forward(self, x):
        batch_size = x.size(0)
        Q = self.W_q(x).view(batch_size, -1, self.num_heads, self.d_k).transpose(1, 2)
        K = self.W_k(x).view(batch_size, -1, self.num_heads, self.d_k).transpose(1, 2)
        V = self.W_v(x).view(batch_size, -1, self.num_heads, self.d_k).transpose(1, 2)

        scores = torch.matmul(Q, K.transpose(-2, -1)) / (self.d_k ** 0.5)
        attn = torch.softmax(scores, dim=-1)
        out = torch.matmul(attn, V).transpose(1, 2).contiguous()
        return self.W_o(out.view(batch_size, -1, self.num_heads * self.d_k))

mha = MultiHeadAttention(d_model=512, num_heads=8)
x = torch.randn(1, 10, 512)
output = mha(x)
print(output.shape)  # torch.Size([1, 10, 512])
```
**Input:** (1, 10, 512) — batch=1, 10 tokens, 512 dimensions
**Output:** (1, 10, 512) — same shape, enriched representations
**Explanation:** We split 512 dimensions into 8 heads of 64 dimensions each. Each head learns different attention patterns (syntax, semantics, position). Outputs are concatenated and projected. This is more powerful than single-head attention.

## Common Mistakes
- Forgetting to scale by √d_k — leads to vanishing gradients in softmax.
- Confusing self-attention (Q=K=V from same sequence) with cross-attention (Q from one, K,V from another).
- Thinking attention is "understanding" — it's mathematical weighting.
- Using single-head when multi-head gives better results.
- Not understanding that attention weights sum to 1 per row (softmax property).

## How Interviewers Ask This
- "Explain attention in transformers."
- "What is scaled dot-product attention?"
- "Why do we need multi-head attention?"
- "What is self-attention vs cross-attention?"
- "How does softmax work in attention?"

## How To Impress The Interviewer
- Explain that scaling by √d_k keeps the variance of dot products at 1, preventing softmax saturation.
- Discuss causal/masked attention: decoder models mask future tokens (upper triangular matrix set to -inf before softmax).
- Mention Flash Attention: reduces memory from O(n²) to O(n) by not materializing the full attention matrix.
- Describe that different attention heads specialize: one head learns POS tags, another co-reference, another syntactic dependencies.
- Know that GPT-3 uses 96 attention heads with d_k=128 each, total d_model=12288.

## Most Asked Questions

**Q1: What is attention in deep learning?**
A: A mechanism that lets models focus on relevant parts of input. It computes weighted combinations where weights represent importance. Example: In translation, "bank" attends to "river" or "money" for correct meaning.

**Q2: What is Scaled Dot-Product Attention?**
A: Computes attention as softmax(QK^T / √d_k)V. Q asks "what do I need?", K says "what I have", V is the actual information. Scaling by √d_k prevents large values. Used in all transformers.

**Q3: What is Self-Attention?**
A: Q, K, V all come from the same sequence. Each word attends to every other word in the same sentence. Example: In "The animal didn't cross the street because it was tired", "it" attends to "animal" to resolve reference.

**Q4: What is Multi-Head Attention?**
A: Running multiple attention mechanisms in parallel (e.g., 8 heads). Each head learns different relationship types. Example: Head 1 learns subject-verb agreement, Head 2 learns adjective-noun pairs, Head 3 learns positional relationships.

**Q5: What is Cross-Attention?**
A: Q comes from one sequence (e.g., decoder), K and V from another (e.g., encoder). Used in encoder-decoder models. Example: T5 uses cross-attention to attend to input text while generating translation.

**Q6: Why scale dot products by √d_k?**
A: Without scaling, dot products grow large with dimension, pushing softmax into regions with tiny gradients. Scaling keeps variance ~1. Example: d_k=512 gives √512≈22.6 scaling factor.

**Q7: What is Masked (Causal) Attention?**
A: Prevents decoder from attending to future tokens. Upper triangular matrix is set to -inf before softmax. Example: Generating "I love AI", when predicting "AI", model cannot "see" it yet.

**Q8: What is the time complexity of self-attention?**
A: O(n² * d) where n is sequence length and d is dimension. For sequence of 4096 tokens, it's ~16M computations. This quadratic cost is a major limitation for long sequences.

**Q9: How does attention handle variable-length input?**
A: Padding with mask ensures padded positions get attention weight of 0. Example: Input "Hello world" (2 tokens) padded to length 10; mask zeros out 8 padding positions.

**Q10: What is the difference between attention and convolution?**
A: Convolution looks at local neighbors (fixed window). Attention looks at all positions (global). CNN is O(k*n), Attention is O(n²). Example: CNN might only see 3 words around each word; attention sees all words.

**Q11: What is Flash Attention?**
A: An optimized attention algorithm using tiling, recomputation, and kernel fusion. Reduces memory from O(n²) to O(n) and speeds up 2-4x. Used in Llama, GPT, and most modern models.

**Q12: What happens if you remove attention from a transformer?**
A: Without attention, each token processes independently with no context. FFN layers alone cannot model relationships. The model becomes a bag-of-words — useless for language tasks.
