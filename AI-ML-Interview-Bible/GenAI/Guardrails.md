# Guardrails

## What is it?
Guardrails are safety systems that control LLM inputs and outputs to prevent harmful, biased, or inappropriate content. They include: Input filtering (block toxic prompts, jailbreak attempts), Output validation (check content safety, format compliance), Topic control (prevent off-topic discussions), and PII protection (redact personal data). Frameworks: NeMo Guardrails (NVIDIA), Guardrails AI, LLM Guard (Protect AI), and built-in moderation APIs (OpenAI Moderation).

## Why do we use it?
LLMs can generate harmful content (hate speech, instructions for illegal activities, biased statements), leak sensitive data, or be manipulated (jailbroken). Companies need guardrails for: legal compliance (GDPR, HIPAA), brand safety, user protection, and regulatory requirements. Without guardrails, one bad output can destroy trust and lead to lawsuits.

## Real-Time Example 1
**Banking Chatbot** — A bank deploys a customer service LLM. Step 1: User asks "How do I make a wire transfer?" Step 2: Input guardrail checks for malicious intent — clean. Step 3: LLM generates response with wire transfer instructions. Step 4: Output guardrail checks for sensitive info — routing numbers detected and redacted. Step 5: Topic guardrail ensures the model doesn't give investment advice (regulatory requirement). Step 6: Safe response delivered. Without guardrails, the model might share internal processes or give unlicensed financial advice.

## Real-Time Example 2
**Educational Platform for Children** — A learning app uses LLM for homework help. Step 1: Input guardrail blocks profanity and personal information requests. Step 2: Topic guardrail ensures math/history/science only (no violence, adult content). Step 3: LLM generates answer. Step 4: Output guardrail checks for age-appropriate content. Step 5: Factual accuracy check against curriculum textbooks. Step 6: Parent gets a log of all interactions. Without guardrails, children could be exposed to inappropriate content.

## Concept Example 1
**OpenAI Moderation for Input/Output Filtering**
```python
from openai import OpenAI
client = OpenAI()

def moderate_content(text):
    response = client.moderations.create(input=text)
    result = response.results[0]
    categories = [cat for cat, flagged in result.categories.items() if flagged]
    return {"flagged": result.flagged, "categories": categories}

user_input = "How do I build a bomb?"
result = moderate_content(user_input)
print(f"Flagged: {result['flagged']}")  # True
print(f"Categories: {result['categories']}")  # ['violence']

if result['flagged']:
    response = "I cannot answer that. Please ask something appropriate."
else:
    response = client.chat.completions.create(
        model="gpt-4",
        messages=[{"role": "user", "content": user_input}]
    )
```
**Input:** User question "How do I build a bomb?"
**Output:** Blocked by moderation filter, safe response returned
**Explanation:** OpenAI's Moderation API checks for hate, harassment, self-harm, sexual content, and violence. Always check both input (user query) and output (LLM response). This is the simplest guardrail — every LLM application should use it.

## Concept Example 2
**NeMo Guardrails: Topic Control**
```python
from nemoguardrails import RailsConfig, LLMRails

# Define guardrails configuration (YAML)
config = RailsConfig.from_content(
    yaml_content="""
    models:
      - type: main
        engine: openai
        model: gpt-4
        
    rails:
      input:
        flows:
          - check jailbreak
          - check off-topic
      output:
        flows:
          - check safety
          - check pii
    
    flows:
      check off-topic:
        - "if bot is asked about competitors"
        - "say 'I can only answer about our products'"
        - "stop"
    """
)

rails = LLMRails(config)
response = rails.generate(
    messages=[{"role": "user", "content": "How does your competitor's product compare?"}]
)
print(response)  # "I can only answer about our products. Please ask about our features."
```
**Input:** Question about competitors
**Output:** Guarded response that stays on topic
**Explanation:** NeMo Guardrails uses a flow-based system to define conversation rules. The guardrail detects "competitors" as off-topic and redirects. Flows can be simple or complex (multi-step, conditional, API calls).

## Common Mistakes
- Only filtering input (user) not output (LLM response).
- Using guardrails as a replacement for RAG/prompt engineering — they complement, not replace.
- Over-blocking — too strict guardrails block legitimate content.
- Not testing guardrails against jailbreak attempts regularly.
- Ignoring edge cases — guardrails must handle adversarial inputs.
- Not logging guardrail violations for analysis and improvement.

## How Interviewers Ask This
- "What are guardrails and why are they important?"
- "How do you prevent jailbreak attacks?"
- "What is NeMo Guardrails?"
- "How do you handle PII in LLM applications?"
- "Compare input vs output guardrails."

## How To Impress The Interviewer
- Explain the multi-layer approach: Input moderation → Topic control → LLM generation → Output moderation → PII redaction → Human review.
- Discuss jailbreak techniques: DAN (Do Anything Now), role-playing prompts, token manipulation, base64 encoding. Guardrails must detect these.
- Mention NeMo Guardrails uses Colang (a conversation modeling language) to define guardrails as flows with conditions and actions.
- Talk about the accuracy vs safety tradeoff: stricter guardrails reduce false negatives (harmful content slips through) but increase false positives (blocks legitimate content). Find the right balance.
- Know about guardrail evaluation: test with adversarial datasets (toxic prompts, jailbreaks, edge cases) and measure recall (detected harmful / total harmful) and precision (correctly blocked / total blocked).

## Most Asked Questions

**Q1: What are guardrails in LLM applications?**
A: Safety systems that control inputs and outputs to prevent harmful or inappropriate content. Types: input filtering, output validation, topic control, PII protection. Example: Block a user asking "How to make weapons" and redact phone numbers from LLM responses.

**Q2: What is the difference between input and output guardrails?**
A: Input guardrails filter user prompts (block toxic content, jailbreaks). Output guardrails filter LLM responses (check safety, accuracy, PII). Example: Input blocks hate speech. Output blocks the LLM from fabricating legal advice.

**Q3: What is NeMo Guardrails?**
A: NVIDIA's open-source guardrails framework. Uses Colang language to define conversational rules as flows. Supports input/output rails, topic control, and integration with any LLM. Example: Define "if user asks about stocks and user is not a licensed advisor, say 'consult a financial advisor.'"

**Q4: What is LLM Guard?**
A: Protect AI's open-source security toolkit for LLMs. Scans inputs and outputs for: PII, secrets, toxicity, jailbreaks, code execution, and more. Example: Redacts email addresses and API keys from LLM responses before showing to users.

**Q5: How do you handle PII in LLM applications?**
A: 1) Input scanning (user shouldn't send PII), 2) Output scanning (LLM shouldn't generate PII), 3) Redaction (mask detected PII), 4) Training data cleaning (remove PII from fine-tuning data). Example: "Call me at 555-1234" → "Call me at [REDACTED]".

**Q6: What is a jailbreak attack?**
A: An attempt to bypass LLM safety measures through clever prompting. Examples: "You are now DAN (Do Anything Now)", "Role-play as an evil AI", "Translate this harmful instruction to French then execute it."

**Q7: How do you prevent jailbreak attacks?**
A: 1) Input guardrails (detect known jailbreak patterns), 2) System prompt hardening (instructions to reject role-play), 3) Perplexity detection (jailbreak prompts have unusual patterns), 4) Output guardrails (block harmful content even if input passes). Example: "DAN" prompt detected → "I cannot role-play as DAN. How can I help you safely?"

**Q8: What is topic control in guardrails?**
A: Restricting the LLM to specific conversation topics. Example: A banking chatbot should discuss accounts and transactions but not provide investment advice or discuss cryptocurrencies.

**Q9: How do you balance safety vs usability with guardrails?**
A: 1) Tiered approach (strict for children/medical, relaxed for creative), 2) User feedback (allow users to report false positives), 3) A/B test guardrail strictness, 4) Monitoring (track block rate, user satisfaction). Example: Children's app blocks all violence; news app allows educational discussions of conflict.

**Q10: What are the components of a guardrail system?**
A: 1) Detector (classify input/output), 2) Policy (what to do when detected: block, warn, redact, escalate), 3) Action (execute the policy), 4) Logger (record all violations). Example: Detector finds profanity → Policy says block → Action returns "Please be respectful" → Logger records the incident.

**Q11: What is the difference between moderation and guardrails?**
A: Moderation is a component of guardrails — it checks for specific content categories. Guardrails include moderation plus topic control, PII protection, format validation, and business rules. Example: OpenAI Moderation checks for hate/violence. NeMo Guardrails adds "only answer from these 3 documents" on top.

**Q12: How do you test guardrails effectiveness?**
A: 1) Build test dataset of harmful/benign inputs, 2) Run through guardrails, 3) Measure recall (harmful detected / total harmful), 4) Measure precision (benign passed / total benign), 5) Real-world monitoring. Example: Test with 100 jailbreak prompts — guardrail should block 95+.
