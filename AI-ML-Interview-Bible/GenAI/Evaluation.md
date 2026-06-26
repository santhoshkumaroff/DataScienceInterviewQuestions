# Evaluation

## What is it?
Evaluation measures how well LLMs perform. Metrics: BLEU (exact word matches for translation), ROUGE (recall-oriented for summarization), METEOR (improved BLEU considering synonyms), BERTScore (semantic similarity using BERT embeddings), RAGAS (evaluates RAG systems on faithfulness, answer relevance, context precision). LLM-as-judge uses a strong LLM (GPT-4) to rate outputs. Human evaluation is the gold standard.

## Why do we use it?
Without evaluation, you can't improve. Companies need to measure: accuracy (is the answer correct?), safety (harmful content?), consistency (same answer for same question?), latency, and cost. Evaluation drives model selection, prompt optimization, RAG tuning, and fine-tuning decisions.

## Real-Time Example 1
**Chatbot Launch at a Bank** — Step 1: Before launch, evaluate 500 test questions. Step 2: Use LLM-as-judge (GPT-4) to rate answers on correctness, safety, and tone. Step 3: RAGAS evaluation checks if answers are grounded in bank policies. Step 4: Human reviewers sample 10% of cases. Step 5: Found 15% hallucinations → improved RAG chunking → hallucination dropped to 3%. Step 6: Chatbot passes evaluation → launched to customers.

## Real-Time Example 2
**Model Selection for Summarization** — Step 1: Test 5 models (GPT-4, Claude-3, Llama-3-70B, Mistral, Gemini) on 1000 articles. Step 2: Compute ROUGE-L scores. GPT-4: 0.42, Llama-3: 0.40, Mistral: 0.38. Step 3: BERTScore (semantic similarity) shows Claude and GPT-4 are close. Step 4: Human evaluation: "Which summary is better?" → Claude wins 52% vs GPT-4 48%. Step 5: Cost analysis: Llama-3 is 10x cheaper than GPT-4 with similar ROUGE → select Llama-3 for production.

## Concept Example 1
**ROUGE Score for Summarization**
```python
from rouge_score import rouge_scorer

reference = "The cat sat on the mat. It was a sunny day."
generated = "A cat sat on a mat on a sunny day."

scorer = rouge_scorer.RougeScorer(['rouge1', 'rouge2', 'rougeL'], use_stemmer=True)
scores = scorer.score(reference, generated)

print(f"ROUGE-1: {scores['rouge1'].fmeasure:.3f}")  # Unigram overlap
print(f"ROUGE-2: {scores['rouge2'].fmeasure:.3f}")  # Bigram overlap
print(f"ROUGE-L: {scores['rougeL'].fmeasure:.3f}")  # Longest common subsequence

# ROUGE-1: 0.857 (6/7 unigrams match: cat, sat, on, mat, sunny, day)
# ROUGE-2: 0.667 (4/6 bigrams match)
# ROUGE-L: 0.857 (longest common sequence: "cat sat on a mat")
```
**Input:** Reference summary and model-generated summary
**Output:** ROUGE scores measuring n-gram overlap
**Explanation:** ROUGE measures recall of n-grams between generated and reference text. ROUGE-1 (single words): 0.857. ROUGE-2 (word pairs): 0.667. ROUGE-L (longest common subsequence): 0.857. Higher is better, but ROUGE doesn't capture meaning — "cat" and "feline" are different to ROUGE but similar semantically.

## Concept Example 2
**RAGAS Evaluation**
```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_precision
from datasets import Dataset

# Sample RAG evaluation data
data = Dataset.from_dict({
    "question": ["What is the return policy?"],
    "answer": ["You can return items within 30 days with receipt."],
    "contexts": [["Our return policy allows returns within 30 days of purchase with original receipt."]],
    "ground_truth": ["30-day return policy with receipt required."]
})

# Compute RAGAS metrics
result = evaluate(
    data,
    metrics=[faithfulness, answer_relevancy, context_precision]
)

print(f"Faithfulness: {result['faithfulness']:.3f}")       # 1.0 (answer matches context)
print(f"Answer Relevancy: {result['answer_relevancy']:.3f}") # 0.95 (relevant to question)
print(f"Context Precision: {result['context_precision']:.3f}") # 1.0 (context is relevant)

# Faithfulness checks hallucination: does answer invent anything not in context?
# Answer Relevancy: is the answer on-topic?
# Context Precision: is the retrieved context actually useful?
```
**Input:** Question, answer, retrieved contexts, ground truth
**Output:** RAGAS scores for RAG system quality
**Explanation:** Faithfulness measures hallucination — the answer should only contain information from the context. Answer relevancy checks if the answer addresses the question. Context precision measures how much of the retrieved context was actually used.

## Common Mistakes
- Using only automated metrics (BLEU, ROUGE) — they miss semantic quality.
- Not having ground truth data for evaluation.
- Evaluating on too few examples (< 50 samples gives unreliable results).
- Ignoring human evaluation — the user's perception is what matters.
- Not evaluating different aspects (safety, bias, tone, accuracy) separately.
- Using LLM-as-judge without checking if the judge LLM is biased.

## How Interviewers Ask This
- "How do you evaluate LLM outputs?"
- "What is the difference between BLEU and ROUGE?"
- "What is RAGAS and what does it measure?"
- "What is LLM-as-judge evaluation?"
- "How do you evaluate a RAG system?"

## How To Impress The Interviewer
- Explain that no single metric is sufficient — use a combination: automated (ROUGE, BERTScore), LLM-as-judge, and human evaluation.
- Discuss RAGAS in detail: Faithfulness (answer grounded in context), Answer Relevance (answer addresses question), Context Precision (retrieved context is relevant), Context Recall (all relevant context retrieved).
- Mention that BERTScore uses BERT embeddings to compare meaning, not just exact n-grams. It captures synonyms.
- Talk about LLM-as-judge pitfalls: position bias (prefers first output), verbosity bias (longer = better), self-enhancement bias (prefers its own outputs). Mitigation: shuffle outputs, use calibration.
- Know the evaluation pyramid: unit tests → automated metrics → LLM evaluation → human evaluation → A/B testing in production.

## Most Asked Questions

**Q1: How do you evaluate LLMs?**
A: Combination of methods: Automated metrics (ROUGE for summarization, BLEU for translation), LLM-as-judge (GPT-4 rates outputs), Human evaluation (domain experts judge), Task-specific (accuracy, F1). Example: Evaluate a Q&A bot on 1000 test questions using correctness + safety scores.

**Q2: What is BLEU?**
A: Bilingual Evaluation Understudy. Measures n-gram precision (up to 4-grams) between generated and reference text. Range 0-1. Best for translation, poor for creative tasks. Example: "The cat sat" vs "A cat sat" → BLEU=0.67 (2/3 1-grams match, 1/2 2-grams).

**Q3: What is ROUGE?**
A: Recall-Oriented Understudy for Gisting Evaluation. Measures n-gram recall. ROUGE-1 (unigrams), ROUGE-2 (bigrams), ROUGE-L (longest common subsequence). Best for summarization. Example: ROUGE-L measures the longest matching word sequence, preserving order.

**Q4: What is METEOR?**
A: Metric for Evaluation of Translation with Explicit ORdering. Improves on BLEU by considering synonyms (using WordNet), stem matching, and word order. Example: "fast" and "quick" are considered matches in METEOR but not in BLEU.

**Q5: What is BERTScore?**
A: Uses BERT embeddings to compute semantic similarity between generated and reference text. Captures meaning, not just exact words. Range 0-1. Example: "The cat is on the mat" vs "A feline sits on the rug" → BERTScore=0.92, ROUGE would be much lower.

**Q6: What is RAGAS?**
A: RAG Assessment. Evaluates RAG systems on: Faithfulness, Answer Relevancy, Context Precision, Context Recall. Example: Faithfulness=0.95 means 95% of the answer is grounded in retrieved context, 5% may be hallucinated.

**Q7: What is LLM-as-judge?**
A: Using a strong LLM (GPT-4, Claude 3) to evaluate other LLM outputs. Provide criteria and ask to rate. Example: Prompt "Rate this answer on accuracy 1-5. Answer: [output]. Ground truth: [truth]." Fast and scalable but biased.

**Q8: How do you do human evaluation?**
A: Domain experts rate outputs on defined criteria. Gold standard but expensive and slow. Methods: pairwise comparison (A vs B), Likert scale (1-5), best-worst scaling. Example: 3 doctors rate medical Q&A outputs on accuracy and safety.

**Q9: What metrics would you use for a chatbot?**
A: Accuracy (correctness), Safety (harmful content), Fluency (grammar), Consistency (same answer for same question), Latency (response time), User satisfaction. Example: A customer support chatbot is evaluated on resolution rate + CSAT score.

**Q10: How do you evaluate a fine-tuned model?**
A: Compare base model vs fine-tuned on: Task-specific metrics (accuracy on domain task), Perplexity (language quality), Human preference (which output is better), Catastrophic forgetting (performance on general tasks). Example: Fine-tuned medical model scores 90% on medical Q&A (vs 70% base) but drops 5% on general QA.

**Q11: What is perplexity?**
A: Measures how "surprised" the model is by text. Lower is better. Perplexity of 1 = perfect prediction, 100 = poor. Example: GPT-3 has perplexity ~10 on Wikipedia text, meaning it's as confused as choosing among 10 equally likely words.

**Q12: How do you set up an evaluation pipeline?**
A: 1) Create test dataset (ground truth), 2) Run model(s) on test set, 3) Compute automated metrics (ROUGE, BERTScore), 4) Run LLM-as-judge, 5) Sample for human eval, 6) Track metrics over time. Example: CI pipeline runs all 1000 test questions after every prompt change.
