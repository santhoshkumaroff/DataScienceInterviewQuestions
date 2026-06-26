# LLM Basics

## What is it?
A Large Language Model (LLM) is a type of AI trained on massive text data to understand and generate human-like text. It works by predicting the next word in a sequence. Examples: GPT-4, Claude, Llama. LLMs have billions of parameters (weights) that store knowledge learned during training.

## Why do we use it?
LLMs power chatbots, code assistants, content generation, translation, and summarization. They save time, reduce costs, and enable applications that were impossible before. Companies use them for customer support, internal knowledge bases, and automating repetitive writing tasks.

## Real-Time Example 1
**Customer Support Chatbot** — A telecom company deploys an LLM-powered chatbot. Step 1: Customer types "My internet is down." Step 2: LLM understands the intent. Step 3: It generates a response asking for account details. Step 4: LLM suggests troubleshooting steps. Step 5: If unresolved, it creates a support ticket. The LLM handles 80% of queries without human intervention.

## Real-Time Example 2
**Code Assistant** — A software team uses GitHub Copilot (powered by an LLM). Step 1: Developer starts typing a Python function. Step 2: LLM predicts the next lines of code. Step 3: Developer presses Tab to accept. Step 4: LLM suggests unit tests for the function. Step 5: Developer saves time on boilerplate code. Productivity increases by 55%.

## Concept Example 1
**Next Token Prediction**
```python
from transformers import GPT2Tokenizer, GPT2LMHeadModel

tokenizer = GPT2Tokenizer.from_pretrained("gpt2")
model = GPT2LMHeadModel.from_pretrained("gpt2")

input_text = "The capital of France is"
inputs = tokenizer(input_text, return_tensors="pt")
outputs = model.generate(**inputs, max_new_tokens=5)
print(tokenizer.decode(outputs[0]))
```
**Input:** "The capital of France is"
**Output:** "The capital of France is Paris, the"
**Explanation:** The model predicts "Paris" as the most likely next word because it learned from millions of documents that Paris is the capital of France. Each token is predicted one at a time, using previously generated tokens as context.

## Concept Example 2
**Counting Parameters**
```python
from transformers import GPT2Config, GPT2Model

config = GPT2Config()
model = GPT2Model(config)
total_params = sum(p.numel() for p in model.parameters())
print(f"Total parameters: {total_params:,}")
# GPT-2 Small has 124M parameters
# GPT-3 has 175B parameters
# Llama 3 70B has 70B parameters
```
**Input:** None (model initialization)
**Output:** "Total parameters: 124,441,344"
**Explanation:** Parameters are the weights the model learns during training. More parameters generally mean more knowledge but require more compute. GPT-4 is rumored to have 1.8 trillion parameters.

## Common Mistakes
- Thinking LLMs actually "understand" text — they predict patterns, not think.
- Using LLMs for math without tools — they struggle with exact calculations.
- Ignoring context length limits — LLMs forget information beyond their context window.
- Assuming more parameters always means better performance.
- Not considering inference cost and latency with large models.

## How Interviewers Ask This
- "Explain how an LLM generates text step by step."
- "What is next token prediction?"
- "How are LLMs trained?"
- "What is the difference between GPT-2, GPT-3, and GPT-4?"
- "What are parameters in LLMs?"

## How To Impress The Interviewer
- Explain the difference between pretraining (next token prediction on internet data) and fine-tuning (instruction following on curated data).
- Discuss scaling laws: model performance improves predictably with more parameters, more data, and more compute (Kaplan et al., Chinchilla scaling laws).
- Talk about the compute budget: training GPT-3 cost ~$4.6M, inference requires GPUs with high VRAM.
- Mention that LLMs have a "knowledge cutoff" — they only know data up to their training date.
- Bring up quantization (INT8, FP4) to run LLMs efficiently on consumer hardware.

## Most Asked Questions

**Q1: What is an LLM?**
A: An AI model trained on massive text data to predict and generate text. It uses deep learning with transformer architecture. Example: GPT-3 was trained on 570GB of text from the internet.

**Q2: How does an LLM generate text?**
A: It predicts one token at a time. Given input "I love", it predicts "machine", then given "I love machine", it predicts "learning", and so on. This is called autoregressive generation.

**Q3: What are parameters in an LLM?**
A: Parameters are numerical weights that the model learns during training. They store knowledge. Example: GPT-2 has 1.5B parameters (largest version), Llama 2 has 7B, 13B, and 70B variants.

**Q4: What is the difference between training and inference?**
A: Training is learning from data (updates parameters). Inference is using the trained model to generate text (no parameter updates). Example: ChatGPT was trained once, but millions of users run inference daily.

**Q5: What data are LLMs trained on?**
A: Internet text — web pages (Common Crawl), books, Wikipedia, academic papers, code (GitHub). Example: GPT-3 was trained on 45TB of text data compressed to 570GB.

**Q6: What is a context window?**
A: The maximum amount of text the model can "see" at once. GPT-3: 2048 tokens, GPT-4: 8192-128k tokens. Example: If context window is 4096 tokens, the model can only consider the last ~3000 words.

**Q7: What are tokenization and tokens?**
A: Tokens are pieces of words the model processes. "unbelievable" might become ["un", "believe", "able"]. GPT-3 uses BPE tokenization. 1 token ≈ 0.75 words.

**Q8: What is the difference between GPT and BERT?**
A: GPT is decoder-only (autoregressive, generates left-to-right). BERT is encoder-only (bidirectional, understands context). GPT is best for generation, BERT for classification.

**Q9: What is the cold start problem for LLMs?**
A: Without fine-tuning or RAG, LLMs may produce generic or outdated answers. Example: Asking "What is the weather today?" without RAG will fail because the model doesn't have real-time access.

**Q10: How do companies deploy LLMs in production?**
A: Through APIs (OpenAI, Anthropic), self-hosting (vLLM, TGI, Ollama), or cloud services (AWS Bedrock, GCP Vertex AI). Example: A startup uses OpenAI API for their chatbot to avoid managing GPU infrastructure.

**Q11: What is autoregressive generation?**
A: Generating one token at a time, using each new token as input for the next prediction. Example: "The" -> "The cat" -> "The cat sat" -> "The cat sat on" -> "The cat sat on the mat."

**Q12: What are the limitations of LLMs?**
A: Hallucinations (making up facts), outdated knowledge, no real understanding, high compute cost, bias in training data, lack of common sense in edge cases.
