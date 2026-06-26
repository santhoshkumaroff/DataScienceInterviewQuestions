# 🎯 Interview Strategy: Before, During, and After

> "Luck is what happens when preparation meets opportunity." — Seneca

This guide covers everything you need to do **before**, **during**, and **after** your AI/ML interview.

---

## BEFORE THE INTERVIEW

### 1. Research the Company (2-3 days before)
- Go to the company website. Read "About Us", "Products", and "Blog".
- Find what AI/ML products they build. Do they use computer vision? NLP? Recommendation systems?
- Check their tech stack on LinkedIn or job descriptions.
- Read recent news about the company.

> **Example:** If the company is in healthcare AI, expect questions about medical data, privacy, and imbalanced datasets (rare diseases).

### 2. Understand the Job Description (1-2 days before)
- Write down every skill mentioned in the JD.
- Next to each skill, rate yourself from 1-5.
- For skills rated 1-3, watch a quick YouTube refresher.

> **Example JD:** "Experience with NLP, Transformers, Python, SQL"
> - NLP: 4/5 ✅
> - Transformers: 3/5 → Watch a 15-min video on attention
> - Python: 5/5 ✅
> - SQL: 4/5 ✅

### 3. Prepare Your Stories (1 day before)
You need **3-4 stories** that demonstrate:
- A technical challenge you solved
- A time you worked in a team
- A project where you made an impact
- A failure and what you learned

Structure each story using **STAR** (Situation, Task, Action, Result). More details in HOW_TO_IMPRESS_INTERVIEWERS.md.

### 4. Practice Coding
- Solve 10-15 LeetCode Easy/Medium problems focused on arrays, strings, hash maps, and trees.
- Implement 3 ML algorithms from scratch (Linear Regression, KNN, Logistic Regression) using only NumPy.
- Practice writing SQL queries — especially JOINs, GROUP BY, window functions.

### 5. Revise ML Concepts
Create a cheat sheet of:

| Concept | One-liner |
|---------|-----------|
| Overfitting | Model memorizes training data, fails on new data |
| Regularization | Adds penalty to prevent overfitting (L1, L2) |
| Bias-Variance tradeoff | Simple = high bias, complex = high variance |
| Cross-validation | Split data into folds, train on k-1, test on 1 |
| Precision | Of all positive predictions, how many were correct |
| Recall | Of all actual positives, how many did we catch |
| F1 | Harmonic mean of precision and recall |

Be ready to explain each concept in **60 seconds**.

### 6. Prepare for HR Questions
- "Tell me about yourself" — 60-second summary: education → skills → projects → why this company
- "Why AI/ML?" — Your honest story
- "Strengths and weaknesses" — Be honest but show improvement
- "Where do you see yourself in 5 years?" — Show ambition and learning

---

## DURING THE INTERVIEW

### 0-5 Minutes: First Impression
- Smile. Firm handshake (if in-person). Confident greeting.
- "Hi, I'm [Name]. Thank you for having me today. I'm excited to discuss how I can contribute to [Company Name]."

### 5-30 Minutes: Technical Questions
**If you know the answer:**
- Pause 2 seconds, think, then answer clearly.
- Use examples. "For example, in my project I used cross-validation because..."

**If you don't know the answer:**
- Do NOT panic. Do NOT stay silent.
- Say: "That's a great question. I haven't worked with [topic] directly, but based on my understanding of [related concept], I think..."

**If you need to code:**
- First explain your approach verbally before writing code.
- Talk while you code. "I'm going to create a hash map here because we need O(1) lookups."
- Test your code mentally with a small example.

### 30-45 Minutes: Project Discussion
- Pick ONE project you know inside out.
- Walk through: problem → approach → challenges → results.
- Mention metrics clearly. "Our model achieved 92% accuracy and 0.87 F1 score."
- When asked "What would you do differently?" — be honest but show growth.

(Full guide in PROJECT_DISCUSSION_GUIDE.md)

### 45-60 Minutes: Your Questions
Always ask 2-3 smart questions:

| Good Questions | Avoid These |
|----------------|-------------|
| "What does a typical day look like for this role?" | "What does the company do?" |
| "What are the biggest challenges the team is facing?" | "How many vacation days?" |
| "How do you measure success in the first 90 days?" | "When will I get promoted?" |
| "What tools and frameworks does the team use?" | "Do I have to work late?" |

### Closing
- Thank them for their time.
- Say: "Based on our conversation, I'm even more excited about this role. I believe my skills in [X] and [Y] align well with what the team needs."
- Ask about next steps: "What is the timeline for the next round?"

---

## AFTER THE INTERVIEW

### Within 2 Hours
- **Thank you email** — send to each interviewer:
```
Subject: Thank You — [Your Name] — [Role Name] Interview

Dear [Interviewer Name],

Thank you for your time today. I really enjoyed learning about [something specific you discussed].

Our conversation about [specific topic] confirmed my excitement for this role. I'm confident I can contribute to [Company Name] with my skills in [skill 1] and [skill 2].

Looking forward to hearing about the next steps.

Best regards,
[Your Name]
```

### Within 24 Hours
- Write down every question you were asked while it's fresh.
- Note what you answered well and what you could improve.
- Update your cheat sheet with any gaps.

### If You Get Rejected
- Reply asking for feedback: "Would you have 5 minutes to share any feedback that could help me improve?"
- Do NOT argue. Do NOT take it personally.
- Use the feedback to improve. Every "no" gets you closer to "yes."

### If You Get Selected
- Celebrate! 🎉
- Negotiate politely if the offer is lower than expected.
- Get everything in writing before resigning from your current role.

---

## Quick Checklist Before Every Interview

- [ ] Researched the company
- [ ] Reviewed the job description
- [ ] Prepared 3 STAR stories
- [ ] Practiced 5 LeetCode problems
- [ ] Revised ML cheat sheet
- [ ] Prepared 3 questions for interviewer
- [ ] Charged laptop / stable internet (if remote)
- [ ] Quiet room / good background (if remote)
- [ ] Water bottle nearby
- [ ] Thank you email template ready
