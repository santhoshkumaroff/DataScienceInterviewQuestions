# Fine-Tuning

## What is it?
Fine-tuning is taking a pre-trained LLM and training it further on specific data to improve performance on particular tasks. Full fine-tuning updates all model parameters. PEFT (Parameter-Efficient Fine-Tuning) updates only a small subset. LoRA (Low-Rank Adaptation) adds small trainable matrices. QLoRA adds quantization to reduce memory. Fine-tuning changes the model's behavior; RAG changes the context it sees.

## Why do we use it?
Pre-trained models know general language but not your specific domain. Fine-tuning adapts the model to your style, terminology, and requirements. Companies fine-tune for: custom tone (friendly vs formal), domain expertise (medical, legal), format adherence (JSON output), and reducing hallucinations on specific topics.

## Real-Time Example 1
**Medical Note Generation** — Step 1: Hospital collects 10,000 doctor-patient conversation transcripts with corresponding clinical notes. Step 2: They fine-tune Llama 2 7B using LoRA on this data. Step 3: After fine-tuning, the model generates clinical notes from conversations. Step 4: Output follows hospital format (SOAP: Subjective, Objective, Assessment, Plan). Step 5: Doctors review instead of writing from scratch, saving 10 minutes per patient.

## Real-Time Example 2
**E-Commerce Product Descriptions** — Step 1: A retailer has 50,000 products with descriptions. Step 2: Fine-tune GPT-3.5 (or Llama 3) on their product catalog. Step 3: Model learns their brand voice (e.g., "sustainable", "eco-friendly", "premium"). Step 4: New products only need specs — model generates full description. Step 5: 500 new products get descriptions daily, consistent brand voice without manual writing.

## Concept Example 1
**LoRA Fine-Tuning with Hugging Face**
```python
from transformers import AutoModelForCausalLM, AutoTokenizer, TrainingArguments
from peft import LoraConfig, get_peft_model
from datasets import Dataset

# Load base model
model_name = "microsoft/phi-2"
model = AutoModelForCausalLM.from_pretrained(model_name)
tokenizer = AutoTokenizer.from_pretrained(model_name)

# LoRA configuration (only 0.1% of parameters are trainable)
lora_config = LoraConfig(
    r=8,                # rank
    lora_alpha=32,      # scaling factor
    target_modules=["q_proj", "v_proj"],  # which layers to adapt
    lora_dropout=0.05,
    bias="none",
)

model = get_peft_model(model, lora_config)
print(f"Trainable params: {model.num_parameters(only_trainable=True):,}")
print(f"Total params: {model.num_parameters():,}")

# Training data
train_data = Dataset.from_dict({
    "input": ["What is AI?", "Explain ML"],
    "output": ["AI is artificial intelligence.", "ML is machine learning."]
})
# Training would proceed with trainer.train()
```
**Input:** Base model (phi-2, 2.7B params) + training data
**Output:** Model with 95% fewer trainable params (only ~2M instead of 2.7B)
**Explanation:** LoRA adds small rank-8 matrices to attention layers. Instead of updating 2.7B parameters, we update only 2M. This reduces GPU memory from ~16GB to ~6GB for phi-2. Training time drops from days to hours.

## Concept Example 2
**Full Fine-Tuning vs PEFT Comparison**
```python
# Full fine-tuning: update ALL parameters
# GPU memory needed ≈ model_size × 4 (for AdamW) × 2 (for activations)
# For 7B model: 7B × 4 bytes × 2 = ~56GB GPU memory
# Example: Llama 2 7B needs 2-4 A100 (80GB) GPUs

# PEFT (LoRA): update only adapter weights
# GPU memory ≈ model_size + adapter_size × 4
# For 7B model with LoRA: 7B × 4 bytes (in 4-bit) + 2M × 4 = ~4GB GPU memory
# Example: QLoRA can fine-tune Llama 2 7B on a single RTX 3090 (24GB)

# QLoRA (Quantized LoRA): model in 4-bit + LoRA
# Memory: 7B × 0.5 bytes (4-bit) + LoRA params ≈ 3.5GB
print("Full FT: 56GB GPU, 7 days training")
print("LoRA: 16GB GPU, 2 hours training")
print("QLoRA: 6GB GPU, 3 hours training")
```
**Input:** Comparison metrics for different fine-tuning methods
**Output:** Cost/compute differences between methods
**Explanation:** Full fine-tuning changes the model fundamentally but costs a lot. LoRA adds "adapters" — small modules that learn task-specific patterns while the base model stays frozen. QLoRA also quantizes the base model to 4-bit, reducing memory by 8x.

## Common Mistakes
- Fine-tuning when RAG would suffice (cheaper, faster, more flexible).
- Overfitting on small datasets (model memorizes rather than generalizes).
- Catastrophic forgetting — model loses general knowledge after fine-tuning.
- Not including diverse data (model learns narrow patterns).
- Using too high learning rate (destroys pre-trained weights).
- Fine-tuning for tasks the base model already does well.

## How Interviewers Ask This
- "What is fine-tuning and when would you use it?"
- "Compare full fine-tuning vs LoRA vs QLoRA."
- "When would you choose RAG over fine-tuning?"
- "What is catastrophic forgetting?"
- "How does LoRA work technically?"

## How To Impress The Interviewer
- Explain LoRA's math: Instead of updating W (d×d), learn A×B where A (d×r) and B (r×d), r << d. W_new = W + α/r × AB.
- Discuss QLoRA: uses 4-bit NormalFloat quantization, double quantization, and paged optimizers to fit large models on small GPUs.
- Mention instruction tuning: fine-tuning on instruction-response pairs (like Alpaca, Dolly) to make models follow instructions.
- Talk about dataset quality: 1000 high-quality examples beat 10000 noisy ones. Use supervised fine-tuning (SFT) with curated data.
- Know about RLHF (Reinforcement Learning from Human Feedback) — training a reward model, then using PPO to align LLM with human preferences.

## Most Asked Questions

**Q1: What is fine-tuning?**
A: Training a pre-trained model further on specific data to adapt it for particular tasks. Example: Fine-tune GPT on legal documents to generate contracts that follow specific legal formats.

**Q2: What is the difference between full fine-tuning and PEFT?**
A: Full FT updates all parameters (expensive, needs lots of GPU). PEFT updates only a small fraction (cheap, fast). Example: Full FT of Llama 2 7B needs 56GB GPU; LoRA needs 16GB.

**Q3: What is LoRA?**
A: Low-Rank Adaptation. Adds small trainable matrices (rank r) to attention layers. Typically r=8 or r=16. Only 0.1-1% parameters are trainable. Example: Fine-tuning a 7B model with r=8 trains only ~4M parameters.

**Q4: What is QLoRA?**
A: Quantized LoRA. Model is stored in 4-bit (NF4 format) while LoRA adapters are trained in 16-bit. Reduces memory by ~4x. Example: Fine-tune a 70B model on a single 48GB GPU vs needing 8 GPUs without QLoRA.

**Q5: When should you use RAG vs fine-tuning?**
A: RAG for: dynamic/updatable knowledge, private data, reducing hallucinations. Fine-tuning for: style/tone, domain adaptation, output format, task-specific behavior. Example: Customer FAQ → RAG. Writing like Shakespeare → Fine-tuning.

**Q6: What is catastrophic forgetting?**
A: When fine-tuning on new tasks causes the model to lose previously learned abilities. Example: Fine-tuning a model only on medical texts makes it bad at general conversation. Mitigation: use LoRA (keeps base model intact) or include diverse data.

**Q7: What is instruction tuning?**
A: Fine-tuning on (instruction, response) pairs to make models better at following instructions. Example: Dataset like "Explain quantum physics simply" → "[simplified explanation]". Used for models like Alpaca, Vicuna.

**Q8: What is RLHF?**
A: Reinforcement Learning from Human Feedback. 3 steps: 1) Collect human preferences, 2) Train a reward model, 3) Fine-tune LLM with PPO to maximize reward. Example: ChatGPT was trained with RLHF to be helpful, harmless, honest.

**Q9: What is the typical learning rate for fine-tuning?**
A: Very small — 1e-5 to 2e-5 for full FT, 1e-4 to 5e-4 for LoRA. Higher rates destroy pre-trained weights. Example: Full FT 7B model at 2e-5 for 3 epochs. LoRA at 3e-4 for 5 epochs.

**Q10: How much data is needed for fine-tuning?**
A: Surprisingly little. 100-1000 high-quality examples can work for LoRA. Full FT might need 10k+. Quality > quantity. Example: Stanford Alpaca fine-tuned Llama on 52k examples generated by GPT-3.5.

**Q11: What are the risks of fine-tuning?**
A: Overfitting (model memorizes), catastrophic forgetting (loses general knowledge), data poisoning (bad data degrades model), bias amplification. Mitigation: validation split, evaluate on diverse benchmarks, use PEFT.

**Q12: How do you evaluate fine-tuned models?**
A: Compare on: task-specific metrics (accuracy, F1), perplexity (language quality), human evaluation (preference), LLM-as-judge (GPT-4 rates outputs). Example: Fine-tuned summarization model evaluated on ROUGE-L vs base model.
