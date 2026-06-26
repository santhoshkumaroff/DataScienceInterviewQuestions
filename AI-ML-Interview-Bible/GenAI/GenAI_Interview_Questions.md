# GenAI Interview Questions (80+)

## BERT vs GPT

**Q1: What is the difference between BERT and GPT architectures?**
A: BERT is encoder-only (bidirectional — sees both left and right context). GPT is decoder-only (autoregressive — predicts left to right). BERT is best for understanding tasks (classification, NER, QA). GPT is best for generation (chat, text completion, code). Example: BERT for sentiment analysis, GPT for writing emails.

**Q2: Why is BERT bidirectional but GPT is unidirectional?**
A: BERT uses masked language modeling — it masks random words and predicts them using context from both sides. GPT uses causal language modeling — it predicts the next word using only previous words. Example: BERT sees "The [MASK] sat on the mat" and predicts "cat". GPT sees "The cat sat on" and predicts "the".

**Q3: When would you choose BERT over GPT?**
A: For non-generative tasks: classification, named entity recognition, relation extraction, question answering (extractive). BERT is smaller, faster, and more accurate for understanding. Example: Classifying customer emails as complaint/refund/inquiry — BERT is better and cheaper than GPT.

**Q4: What is the NSP (Next Sentence Prediction) task in BERT?**
A: During training, BERT predicts if two sentences are consecutive. This helps BERT understand sentence relationships. Used for QA and NLI tasks. Example: "I went to the store" + "I bought milk" = consecutive. "I went to the store" + "The Eiffel Tower is tall" = not consecutive.

**Q5: Can GPT do classification tasks?**
A: Yes, through prompting/prompt engineering. Instead of a classification head, you ask "Classify this sentiment: positive/negative." GPT works but is more expensive and slower than BERT. Example: BERT classifies in 10ms with 96% accuracy; GPT-4 classifies in 500ms with 97% accuracy — tradeoff.

## Positional Encoding

**Q6: Why do transformers need positional encoding?**
A: Transformers process all tokens in parallel (unlike RNNs), so they have no inherent sense of word order. Positional encoding adds position information. Without it, "I love you" and "you love I" produce identical representations.

**Q7: How does sinusoidal positional encoding work?**
A: Uses sine and cosine functions of different frequencies. Each position gets a unique pattern. pos=0: [sin(0), cos(0), sin(0), cos(0)...], pos=1: [sin(1/10000^0), cos(1/10000^0)...]. This allows the model to learn relative positions.

**Q8: What is learned positional encoding?**
A: Instead of fixed sine/cosine functions, the model learns position embeddings during training. Used in BERT and GPT. Example: Learn a unique embedding for position 0, 1, 2, ... up to max sequence length.

**Q9: What is rotary positional encoding (RoPE)?**
A: A newer method that rotates query and key vectors based on position. Used in Llama, Mistral, GPT-NeoX. Benefits: better relative position encoding, natural decay of token relationships with distance, better extrapolation to longer sequences.

**Q10: How does ALiBi (Attention with Linear Biases) work?**
A: Instead of adding position to embeddings, ALiBi adds a bias to attention scores based on distance between tokens. Tokens far apart get a larger negative bias (less attention). Benefits: efficient, works well for extrapolation to longer sequences.

## Tokenization

**Q11: What is tokenization in NLP?**
A: Converting text into smaller units (tokens) that the model can process. Example: "unbelievable" → ["un", "believe", "able"]. Different from word splitting: "I'm" → ["I", "'m"].

**Q12: What is BPE (Byte-Pair Encoding)?**
A: A tokenization algorithm that starts with individual characters and iteratively merges the most frequent pairs. Used by GPT models. Example: "low" "lower" "lowest" → merges "low" into one token because it appears frequently.

**Q13: What is WordPiece tokenization?**
A: Similar to BPE but merges based on likelihood (not frequency). Used by BERT. Adds "##" prefix for subwords. Example: "unbelievable" → ["un", "##believe", "##able"]. The "##" indicates continuation.

**Q14: What is SentencePiece tokenization?**
A: A tokenizer that treats text as raw bytes (no pre-tokenization needed). Supports multiple languages without language-specific preprocessing. Used by T5, Llama, Alpaca. Example: Processes Japanese text directly without space-based pre-tokenization.

**Q15: How does tokenization affect model performance?**
A: Vocabulary size affects model size, speed, and handling of rare words. Larger vocabulary = larger embedding layer but better coverage. Example: GPT-2 has 50k tokens, Llama 2 has 32k tokens. Rare technical terms might be split into many subwords.

**Q16: What is a BOS/EOS token?**
A: Beginning-of-sequence and End-of-sequence tokens. Special tokens marking start and end of text. The model uses them to know when generation should start and stop. Example: Input becomes [BOS] "The cat sat" [EOS].

**Q17: What is padding in tokenization?**
A: Adding special [PAD] tokens to make all sequences in a batch the same length. The attention mask tells the model to ignore padding tokens. Example: Batch of sequences length 5, 10, 15 → all padded to 15 with [PAD].

## Quantization

**Q18: What is model quantization?**
A: Reducing the precision of model weights (e.g., 32-bit → 4-bit) to reduce memory usage and speed up inference. Tradeoff: small accuracy loss for 4x+ memory reduction. Example: Llama 2 70B needs 140GB in FP16, only 35GB in 4-bit.

**Q19: What is GPTQ?**
A: Post-training quantization method that compresses weights to 4-bit or 3-bit. Uses Optimal Brain Quantization (OBQ) with Hessian-based weight updates. Good for GPU inference. Example: Llama 2 7B fits in 4GB VRAM with GPTQ.

**Q20: What is AWQ?**
A: Activation-Aware Weight Quantization. Better than GPTQ by considering activation distributions, not just weights. Preserves important weights (with large activations). Example: AWQ 4-bit models are more accurate than GPTQ 4-bit at the same size.

**Q21: What is GGUF?**
A: A file format for quantized models designed for CPU inference (llama.cpp). Supports various quantization levels (Q2_K to Q8_0). Example: Llama 2 7B as Q4_K_M GGUF runs on a laptop CPU at ~5 tokens/second.

**Q22: What is the difference between INT8, FP16, and FP4 quantization?**
A: INT8 (8-bit integer, 2x memory reduction), FP16 (16-bit float, 2x reduction vs FP32), FP4 (4-bit, 8x reduction vs FP32). Lower bits = smaller but less accurate. Example: FP16 is standard for training, INT8/FP4 for inference.

**Q23: What is quantization-aware training (QAT)?**
A: Training the model with simulated quantization effects, so it learns to be robust to quantization. More accurate than post-training quantization (PTQ) but requires training. Example: QAT models lose only 0.5% accuracy vs PTQ losing 2%.

## Context Windows

**Q24: What is a context window in LLMs?**
A: The maximum number of tokens the model can process in one forward pass. Limits how much context the model can use. Example: GPT-3: 2048 tokens (~1500 words). GPT-4-128k: 128k tokens (~96000 words).

**Q25: How do you extend the context window?**
A: 1) Positional interpolation (change position encoding frequencies), 2) NTK-aware scaling, 3) YaRN (Yet another RoPE extensioN method), 4) Fine-tuning on longer sequences. Example: Mistral extended 8k to 32k using sliding window attention.

**Q26: What is sliding window attention?**
A: Each token only attends to a fixed window of nearby tokens (e.g., 4096 tokens), not the full sequence. Reduces O(n^2) to O(n*k). Used by Mistral, Mixtral. Example: 128k context with 4k window — each token only sees 4k neighbors but information propagates through layers.

**Q27: What is the "lost in the middle" problem?**
A: LLMs tend to focus on information at the beginning and end of the context, ignoring middle parts. Example: Given 50 documents, models answer questions best when the relevant info is in positions 1-5 or 45-50. Middle information is often missed.

## Flash Attention

**Q28: What is Flash Attention?**
A: An optimized attention algorithm that computes attention without materializing the full NxN attention matrix. Uses tiling, recomputation, and kernel fusion. Reduces memory from O(n^2) to O(n) and speeds up 2-4x.

**Q29: How does Flash Attention achieve better memory usage?**
A: By tiling (processing attention in blocks that fit in fast SRAM instead of slow HBM), and recomputation (recomputing attention during backward pass instead of storing). Example: For 4096 sequence, Flash Attention uses 16MB memory vs 256MB for standard attention.

**Q30: What is Flash Attention 2?**
A: Improved version with better parallelism, fewer non-matmul operations, and better work distribution. Up to 2x faster than Flash Attention 1. Used in Llama 3, GPT-4, and most modern models.

## Speculative Decoding

**Q31: What is speculative decoding?**
A: A technique to speed up LLM inference by using a small "draft" model to generate multiple tokens, then the large "target" model verifies them in parallel. If correct, you get multiple tokens for the cost of one.

**Q32: How does speculative decoding work?**
A: Step 1: Small model (e.g., 125M params) quickly drafts 5 tokens. Step 2: Large model (e.g., 70B) validates all 5 tokens in one forward pass. Step 3: Tokens that pass verification are accepted (usually 3-4 of 5). Step 4: Repeat. Results in 2-3x speedup with identical output distribution.

## KV Cache

**Q33: What is KV cache in LLM inference?**
A: During autoregressive generation, the Key and Value matrices for previous tokens are cached. Each new token only computes attention with the cached K,V (not recomputing from scratch). Transforms O(n^3) generation to O(n^2).

**Q34: How much memory does KV cache use?**
A: For a 70B model with batch size 1 and 4096 context: KV cache = 2 (K and V) x layers (80) x heads (64) x d_head (128) x seq_len (4096) x 2 bytes = ~5GB per sequence. For batch 64: 320GB.

**Q35: What is KV cache quantization?**
A: Compressing the KV cache to 4-bit or 8-bit to reduce memory usage. Since the KV cache can be gigabytes for long sequences, quantizing it from FP16 to INT8 halves memory. Example: KIVI (KV cache INT8 quantization) reduces memory by 2x with minimal quality loss.

**Q36: What is continuous batching?**
A: Dynamically adding new requests to the running batch as other requests finish, instead of waiting for a full batch to complete. Maximizes GPU utilization. Used by vLLM, TGI. Example: 10 requests arrive at different times — instead of processing each sequentially or waiting for all 10, continuously batch as they come.

## Temperature and Top-P

**Q37: What is temperature in LLMs?**
A: Controls the randomness of token selection by scaling the logits before softmax. Low (0-0.3): deterministic, picks highest probability token. High (0.7-2): more diverse, flattens probability distribution. Example: temp=0 always gives "The cat sat on the mat". temp=1 might give "A feline rested upon the rug."

**Q38: What is top-k sampling?**
A: Only consider the top k most probable tokens at each step. k=40 means only the 40 highest probability tokens are candidates. More focused than full sampling but less flexible than top-p.

**Q39: What is top-p (nucleus) sampling?**
A: Select the smallest set of tokens whose cumulative probability exceeds p (e.g., 0.9). Dynamically adjusts candidate count based on confidence. When distribution is sharp, few tokens. When flat, many tokens. Example: top_p=0.9 picks 3 tokens if very confident, 50 tokens if uncertain.

**Q40: What is the difference between greedy decoding and beam search?**
A: Greedy picks the highest probability token at each step (fast, might miss best sequence). Beam search keeps k candidate sequences (beams) and selects the overall best (slower, better quality). Example: k=4 beam search considers 4 alternative continuations at each step.

**Q41: What is repetition penalty?**
A: A penalty applied to tokens that have already appeared in the generated text, reducing the probability of repeating. Common values: 1.0 (no penalty), 1.1 (mild), 1.2 (strong). Example: Without penalty, model might repeat "and then" many times.

## Distillation

**Q42: What is knowledge distillation?**
A: Training a small "student" model to mimic a large "teacher" model. The student learns from the teacher's soft probability distributions (rich information) rather than hard labels. Example: DistilBERT is 40% smaller but retains 97% of BERT's performance.

**Q43: What are the benefits of distillation?**
A: Smaller model size (2-10x reduction), faster inference (2-5x speedup), lower cost, easier deployment. Tradeoff: slight accuracy loss. Example: DistilGPT-2 is half the size of GPT-2, runs 2x faster, with minimal quality drop.

## Model Deployment

**Q44: What is vLLM?**
A: A high-throughput LLM inference engine. Uses PagedAttention (virtual memory for KV cache) and continuous batching. Achieves 10-20x higher throughput than naive implementations. Example: vLLM serves Llama 2 7B at 100 requests/second on a single A100.

**Q45: What is PagedAttention?**
A: vLLM's memory management technique. Treats KV cache blocks like virtual memory pages — non-contiguous physical memory, on-demand allocation. Eliminates fragmentation, enables efficient batching. Example: Instead of pre-allocating max KV cache for each sequence, allocate only needed blocks.

**Q46: What is Ollama?**
A: A tool for running LLMs locally on CPU/GPU. Supports GGUF format models (Llama, Mistral, Gemma). Simple CLI: "ollama run llama2". Handles model downloading, quantization, and serving. Example: Developers run Llama 2 locally for prototyping without cloud costs.

**Q47: What is TGI (Text Generation Inference)?**
A: Hugging Face's inference server. Supports continuous batching, tensor parallelism, quantization (GPTQ, AWQ), streaming. Same API as OpenAI. Example: Deploy Mistral 7B with TGI, get OpenAI-compatible API endpoint.

**Q48: What is TensorRT-LLM?**
A: NVIDIA's inference optimization framework. Compiles and optimizes LLMs for NVIDIA GPUs. Supports in-flight batching, quantization, attention optimizations. Highest performance on NVIDIA hardware. Example: Llama 2 70B runs 3x faster with TensorRT-LLM vs naive PyTorch.

**Q49: How do you choose between cloud API and self-hosting?**
A: Cloud API (OpenAI, Anthropic): no infrastructure, pay per token, always updated. Self-hosting (vLLM, Ollama): lower cost at scale, data privacy, full control, lower latency. Example: Startup with 100 users/day uses OpenAI API. Enterprise with 1M users/day self-hosts Llama 3.

## LangGraph

**Q50: What is LangGraph?**
A: LangChain's library for building stateful, multi-step agent workflows as directed graphs. Nodes are functions/agents, edges define transitions. Supports conditional branching, loops, parallel execution, and state persistence.

**Q51: What are the key concepts in LangGraph?**
A: State (shared data across steps), Nodes (processing steps), Edges (transitions between nodes), Conditional edges (decide next step based on state). Example: Research → (if enough data) Write → Review → (if approved) Output, (if rejected) back to Research.

**Q52: How is LangGraph different from LangChain chains?**
A: Chains are linear and fixed (A → B → C). LangGraph supports cycles, branching, and dynamic decisions. Example: A chain always does "search → write". LangGraph can loop "search → evaluate → (if need more) search again".

## Hugging Face

**Q53: What is Hugging Face?**
A: A platform for ML models, datasets, and spaces. Hosts 500k+ models (transformers, diffusers, etc.). Provides libraries (transformers, datasets, accelerate, PEFT) for training and deploying models. Example: Download Llama 2 from Hugging Face Hub, fine-tune with PEFT, deploy with TGI.

**Q54: What is the Hugging Face Transformers library?**
A: Python library providing pre-trained models (BERT, GPT, Llama, T5) with unified API. Pipeline abstraction: pipeline("sentiment-analysis"), pipeline("text-generation"). Handles tokenization, model loading, inference.

**Q55: What is the Hugging Face Hub?**
A: Central repository for models, datasets, and spaces. Model cards document usage, licenses, and benchmarks. Git-based version control. Example: Find "mistralai/Mistral-7B-v0.1" → read model card → download with transformers → fine-tune → push back.

## Tokenization & Vocabulary

**Q56: What is the typical vocabulary size for LLMs?**
A: GPT-2: 50,257 tokens. GPT-4: ~100k tokens (rumored). Llama 2: 32,000 tokens. SentencePiece: 32k-128k. Larger vocabulary = better tokenization of rare words but larger embedding layer.

**Q57: How do you handle out-of-vocabulary (OOV) words?**
A: Subword tokenization handles OOV by splitting unknown words into known subwords. Example: "unfathomable" not in vocabulary → ["un", "fathom", "able"]. All words can be represented with subwords.

**Q58: What is byte-level tokenization?**
A: Tokenization at the byte level (UTF-8 bytes). Handles any text in any language without unknown tokens. Used by GPT-2, GPT-3. Example: "é" becomes its UTF-8 bytes [195, 169].

## Advanced Architecture

**Q59: What is mixture of experts (MoE)?**
A: Architecture where the model has many "expert" sub-networks, but only a subset is activated for each token. More parameters with same compute. Example: Mixtral 8x7B has 47B total parameters but only uses 13B per token — runs like a 13B model with 47B model quality.

**Q60: How does MoE work?**
A: A router/gate network decides which experts to use for each token (typically top-2). Each expert is a standard FFN. Tokens are processed by their assigned experts, outputs are combined. Example: Token "cat" might route to "grammar expert" and "animal expert."

**Q61: What is the difference between dense and sparse models?**
A: Dense models (GPT, Llama) use all parameters for every token. Sparse models (MoE) use only a subset. Sparse models have more total parameters but similar compute per token. Example: Dense 7B uses 7B params per token. MoE 7B (8 experts) uses ~1B per token.

## Production Best Practices

**Q62: How do you handle rate limiting for LLM APIs?**
A: Implement exponential backoff (client side), queue systems (server side), request batching, and caching for identical requests. Example: If 429 error, wait 1s → retry → 2s → retry → 4s → etc.

**Q63: What is LLM caching and why is it important?**
A: Cache identical or similar requests to avoid recomputation. Exact match cache (redis), semantic cache (vector DB — similar queries return cached response). Example: "What is the capital of France?" → cached → instant response, no API call.

**Q64: How do you monitor LLMs in production?**
A: Track: latency (p50, p95, p99), throughput (req/s), error rate, token usage (cost), user satisfaction, safety violations, hallucination rate. Example: Grafana dashboard showing GPT-4 latency: p50=500ms, p99=2s.

**Q65: What is a canary deployment for LLMs?**
A: Roll out new prompts/models to a small percentage of users (e.g., 5%) before full deployment. Monitor for issues. Example: Test new system prompt on 5% of traffic → if hallucination rate increases, rollback.

**Q66: How do you handle PII in production LLM systems?**
A: 1) Input scanning (before sending to LLM), 2) Output scanning (before showing to user), 3) Fine-tuning data cleaning, 4) Never log raw LLM outputs, 5) Use PII redaction models (presidio). Example: "My SSN is 123-45-6789" → "My SSN is [REDACTED]".

**Q67: What is shadow deployment?**
A: Running a new model alongside the production model, comparing outputs without affecting users. Useful for evaluating new models safely. Example: Run GPT-4 alongside current GPT-3.5 for 1 week, compare quality before switching.

**Q68: How do you AB test LLM prompts/models?**
A: Split users into groups, serve different prompts/models, measure metrics (satisfaction, task completion, cost). Use statistical significance before rolling out. Example: Group A gets "Summarize in 3 bullets" prompt, Group B gets "Summarize in 1 paragraph" — measure which leads to better user outcomes.

## Sampling Strategies

**Q69: What is typical sampling?**
A: Combine top-k, top-p, and temperature. Example: temperature=0.7, top_p=0.9, top_k=40. Most common configuration for creative tasks.

**Q70: What is contrastive search?**
A: Selects tokens that are both probable AND dissimilar to previous tokens. Reduces repetition. Parameter alpha controls the tradeoff. Example: Instead of repeating "the the the", selects diverse tokens.

**Q71: What is Mirostat sampling?**
A: Dynamically adjusts temperature during generation to maintain a target perplexity (surprisingness). Prevents repetition and degeneration. Used by llama.cpp.

## Advanced Topics

**Q72: What is attention sink?**
A: A phenomenon where LLMs attend heavily to the first token (often BOS token) regardless of content. This "sinks" excessive attention. Used in "streaming LLM" to maintain stable generation with sliding window.

**Q73: What is streaming LLM?**
A: A technique for LLMs to handle infinite-length text by combining a sliding window (recent tokens) with an attention sink (first few tokens). No need for position extrapolation. Example: Model processes 1M tokens using 4k window + 4 sink tokens.

**Q74: What is prefix caching?**
A: Caching KV cache for common prompt prefixes (e.g., system prompts). When many requests share the same prefix, the cache is reused — saving 30-50% computation. Example: 100 users with same system prompt → KV cache for system prompt computed once, reused 100 times.

**Q75: What are embedding lookups vs inference?**
A: Embedding lookup (one forward pass through embedding layer) gives a fixed vector. Inference (full model forward pass) generates tokens. Example: text-embedding-ada-002 is an embedding lookup (fast, cheap). GPT-4 generation is inference (slow, expensive).

**Q76: What is a logit bias?**
A: Adding a bias to specific token logits before sampling. Can force or forbid tokens. Example: logit_bias={234: 100} (token 234 = "Yes") makes model more likely to say "Yes". Used for steering outputs without prompt changes.

**Q77: What is constrained decoding?**
A: Forcing the LLM to follow a specific format (JSON, regex, schema) during generation. Instead of post-processing, constrain the token selection to valid tokens only. Example: Outline JSON schema, only allow tokens that produce valid JSON at each step.

**Q78: What is the difference between zero-shot and few-shot inference costs?**
A: Few-shot uses more input tokens (examples take up context), costing more. Example: Zero-shot uses 50 input tokens. Few-shot with 5 examples uses 500 input tokens — 10x cost for each inference.

**Q79: What is prompt caching?**
A: OpenAI/VLLM feature that caches processed prompt prefixes. If the same prefix appears again, computation is reduced. Example: System prompt of 500 tokens → cached → subsequent requests with same system prompt skip the first 500 tokens of processing.

**Q80: How do you handle long document QA?**
A: 1) Map-reduce (summarize chunks, combine), 2) Refine (iterate through chunks, refine answer), 3) Hierarchical (tree of summaries), 4) Agent-based (agent decides which chunks to read). Example: 100-page document → 50 chunks of 2 pages each → summarize each → combine summaries.

**Q81: What is the scaling law for inference compute?**
A: Inference cost scales linearly with: model size (parameters), sequence length (input + output tokens), batch size. Formula: FLOPs ≈ 2 * N * (L_input + L_output) where N = params. Example: Llama 2 70B generating 100 tokens: ~14 trillion FLOPs.

**Q82: What is the difference between prefill and decode in inference?**
A: Prefill phase: process input prompt in parallel (compute-intensive, all tokens at once). Decode phase: generate tokens one by one (memory-bound, one token at a time). Example: 1000 token prompt processed in ~100ms (prefill), then generates 100 tokens at ~50ms each (decode).

**Q83: What is chunked prefill?**
A: Splitting long prompts into chunks during prefill to prevent OOM. Each chunk is processed separately, then attention results are combined. Example: 128k prompt → 4 chunks of 32k → process each chunk → combine.

**Q84: How do you reduce first-token latency?**
A: 1) Shorter prompts, 2) Prompt caching, 3) Chunked prefill with parallel processing, 4) GPU with high memory bandwidth, 5) Speculative decoding for short outputs. Example: Reduce 1000 token prompt to 200 tokens → first token in 50ms instead of 300ms.

**Q85: What is the role of temperature in chain-of-thought reasoning?**
A: Low temperature (0-0.3) is best for reasoning tasks. Higher temperature makes the model "creative" which hurts logical reasoning. Example: Math reasoning at temp=0: 95% accuracy. At temp=1: 60% accuracy.

**Q86: How do you choose between GPT-4, Claude, Llama, and Gemini?**
A: GPT-4: best overall, most expensive, good for complex tasks. Claude: longer context (200k), better at analysis, safer. Llama: open-source, self-hostable, good for sensitive data. Gemini: multimodal, Google ecosystem integration. Example: Internal document QA with sensitive data → self-host Llama. Creative writing → GPT-4.

**Q87: What is the future of LLMs?**
A: Trends: longer context (1M+ tokens), multimodal (text + image + video + audio), smaller models (3B-7B that match 70B), personalized models, agentic systems, on-device deployment, specialized domain models. Example: GPT-5 may have 10M token context, native video understanding, and agent capabilities.
