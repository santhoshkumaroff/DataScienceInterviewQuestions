# Transformers

## What is it?
The Transformer is a neural network architecture introduced in "Attention Is All You Need" (2017). It uses self-attention instead of recurrence (RNNs) or convolution (CNNs). Key components: Encoder (reads input), Decoder (generates output), Self-Attention (weighs word relationships), Feed-Forward layers, Layer Normalization, and Positional Encoding.

## Why do we use it?
Transformers are the foundation of all modern LLMs. They process entire sequences in parallel (unlike RNNs), enabling training on massive datasets. They handle long-range dependencies well and scale efficiently. Every major AI model (GPT, BERT, T5, Llama, Claude) uses transformers.

## Real-Time Example 1
**Machine Translation** — Google Translate uses a Transformer (T5-based). Step 1: Input "I love AI" in English. Step 2: Encoder processes all words simultaneously and creates context vectors. Step 3: Decoder generates "J'aime l'IA" in French. Step 4: Self-attention ensures "love" maps to "aime" despite different word positions. Step 5: Output is returned to user in milliseconds.

## Real-Time Example 2
**Document Summarization** — A legal firm uses a BART (Transformer-based) model. Step 1: Upload a 50-page contract. Step 2: Encoder processes all text. Step 3: Decoder generates a 1-page summary. Step 4: Self-attention highlights critical clauses. Step 5: The summary saves lawyers hours of reading. Accuracy is 95%+ for key terms.

## Concept Example 1
**Simple Transformer Encoder**
```python
import torch
import torch.nn as nn

# Simplified single-head attention
class SimpleAttention(nn.Module):
    def __init__(self, d_model=512):
        super().__init__()
        self.query = nn.Linear(d_model, d_model)
        self.key = nn.Linear(d_model, d_model)
        self.value = nn.Linear(d_model, d_model)

    def forward(self, x):
        Q = self.query(x)
        K = self.key(x)
        V = self.value(x)
        scores = torch.matmul(Q, K.transpose(-2, -1)) / (x.size(-1) ** 0.5)
        attention = torch.softmax(scores, dim=-1)
        return torch.matmul(attention, V)

# Example usage
x = torch.randn(1, 10, 512)  # batch=1, seq_len=10, d_model=512
attn = SimpleAttention()
output = attn(x)
print(output.shape)  # torch.Size([1, 10, 512])
```
**Input:** Random tensor (1 batch, 10 tokens, 512 dimensions)
**Output:** Same shape tensor with context-aware representations
**Explanation:** The attention mechanism computes how much each token should "attend" to every other token. Token 1 might attend heavily to token 5 if they're related. All computations happen in parallel.

## Concept Example 2
**Positional Encoding**
```python
import torch
import math

def positional_encoding(seq_len, d_model):
    pe = torch.zeros(seq_len, d_model)
    for pos in range(seq_len):
        for i in range(0, d_model, 2):
            pe[pos, i] = math.sin(pos / (10000 ** (i / d_model)))
            pe[pos, i + 1] = math.cos(pos / (10000 ** (i / d_model)))
    return pe

pe = positional_encoding(50, 512)
print(pe.shape)  # torch.Size([50, 512])
print(pe[0, :4])  # Position 0: [sin(0), cos(0), sin(0), cos(0)]
print(pe[1, :4])  # Position 1: different values
```
**Input:** Sequence length 50, model dimension 512
**Output:** A matrix of shape (50, 512) with position-specific patterns
**Explanation:** Since transformers process all tokens in parallel (not sequentially like RNNs), they need positional encoding to know word order. Sine/cosine functions create unique patterns for each position.

## Common Mistakes
- Thinking transformers use recurrence — they don't, they process in parallel.
- Forgetting about positional encoding — without it, "cat sat on mat" and "mat sat on cat" look identical.
- Ignoring the quadratic complexity of self-attention — O(n²) for sequence length n.
- Using transformers for very long sequences without optimization (Flash Attention, sparse attention).
- Confusing encoder-only (BERT), decoder-only (GPT), and encoder-decoder (T5) architectures.

## How Interviewers Ask This
- "Explain the transformer architecture."
- "What is self-attention and why is it important?"
- "Why do transformers need positional encoding?"
- "Compare encoder-only vs decoder-only vs encoder-decoder models."
- "What is the complexity of self-attention?"

## How To Impress The Interviewer
- Explain that self-attention has O(n²) complexity because each token attends to every other token.
- Discuss Flash Attention: optimizes attention computation using tiling and kernel fusion, reducing memory from O(n²) to O(n).
- Mention that decoder-only models (GPT) use masked self-attention to prevent looking at future tokens.
- Talk about KV cache: during inference, cached Key and Value matrices speed up generation from O(n³) to O(n²).
- Bring up scaling: GPT-3 uses 96 decoder layers, 96 attention heads, d_model=12288.

## Most Asked Questions

**Q1: What is the Transformer architecture?**
A: A neural network using self-attention mechanisms instead of recurrence. Components: self-attention, feed-forward networks, layer normalization, positional encoding. Introduced in 2017 by Vaswani et al.

**Q2: What are the three types of Transformer models?**
A: Encoder-only (BERT — understanding), Decoder-only (GPT — generation), Encoder-Decoder (T5, BART — translation/summarization). Example: BERT is used for classification, GPT for chatbots, T5 for translation.

**Q3: What is self-attention?**
A: A mechanism allowing each token to "attend" to all other tokens. It computes Query (what I'm looking for), Key (what I offer), Value (what I contain). Example: "The dog chased the cat" — "chased" attends to "dog" (subject) and "cat" (object).

**Q4: What is multi-head attention?**
A: Running multiple self-attention mechanisms in parallel (e.g., 12 heads). Each head learns different relationships. One head learns syntax, another learns semantics. Example: BERT-base has 12 heads, GPT-3 has 96 heads.

**Q5: Why is positional encoding needed?**
A: Transformers have no inherent sense of word order (unlike RNNs). Positional encoding adds position information using sine/cosine functions. Without it, "I love you" and "you love I" produce the same representation.

**Q6: What is Layer Normalization?**
A: Normalizes inputs across feature dimensions, stabilizing training. Applied before or after self-attention (Pre-LN vs Post-LN). Example: GPT-2 uses Pre-LN for more stable training.

**Q7: What is the Feed-Forward Network (FFN)?**
A: Two linear layers with a ReLU/GELU activation in between. FFN processes each token independently. Example: In GPT-3, FFN has 12288 → 49152 → 12288 dimensions.

**Q8: What is the difference between training and inference in transformers?**
A: Training uses all tokens simultaneously (teacher forcing). Inference generates auto-regressively (one token at a time). Inference uses KV cache to avoid recomputing.

**Q9: What are the limitations of transformers?**
A: O(n²) attention complexity, high memory usage, fixed context window, training cost. Example: Sequence of 100k tokens requires 10B attention computations.

**Q10: How do transformers handle long sequences efficiently?**
A: Flash Attention (memory-efficient attention), sliding window attention, sparse attention, Longformer, BigBird. Example: Mistral uses sliding window attention with window size 4096.

**Q11: What is the KV Cache?**
A: During inference, previous Key and Value matrices are cached so they don't need recomputation. Improves speed from O(n³) to O(n²). Example: Generating 100 tokens without cache computes attention 100 times; with cache, only computes for the latest token.

**Q12: How does scaling work in transformers?**
A: Increasing layers (depth), model dimension (width), or attention heads. GPT-3: 96 layers, d_model=12288, 96 heads. Scaling laws show predictable improvement with more parameters and data.
