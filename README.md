# Prompt Engineering Playbook

A practical reference for designing, testing, and optimizing prompts for large language models in production and evaluation contexts.

Built from operational experience across frontier model evaluation, RLHF pipelines, and multilingual AI quality assurance.

---

## What This Is

This playbook is a working reference — not a theoretical guide. It focuses on the patterns that matter in practice: reducing hallucinations, improving instruction adherence, and maintaining output stability across varied inputs.

**Use cases covered:**
- System prompt design for deployed AI products
- Evaluation prompt construction for model benchmarking
- Chain-of-thought and reasoning elicitation
- Multilingual prompt adaptation
- Sensitivity testing and regression checks

---

## Repository Structure

```
prompt-engineering-playbook/
│
├── foundations/
│   ├── prompt-anatomy.md            # Components of a well-structured prompt
│   ├── instruction-design.md        # Writing clear, unambiguous instructions
│   └── common-failure-modes.md      # What goes wrong and why
│
├── templates/
│   ├── system-prompt-templates.md   # Reusable system prompt patterns
│   ├── few-shot-templates.md        # Few-shot example structures
│   └── chain-of-thought.md         # CoT elicitation patterns
│
├── testing/
│   ├── sensitivity-testing-guide.md # How to stress-test prompt stability
│   └── regression-checklist.md     # Checks before deploying prompt changes
│
└── domain-examples/
    ├── qa-evaluation-prompts.md     # Prompts for model evaluation tasks
    ├── summarization-prompts.md     # Summarization with quality controls
    └── classification-prompts.md   # Classification and labeling tasks
```

---

## Prompt Anatomy

A production-grade prompt typically contains:

```
[ROLE DEFINITION]
You are a [role] with expertise in [domain].

[CONTEXT]
The user is [description of user context].

[TASK]
Your task is to [specific, unambiguous instruction].

[CONSTRAINTS]
- Constraint 1
- Constraint 2
- Constraint 3

[OUTPUT FORMAT]
Respond in the following format:
[format specification]

[EXAMPLES] (optional but recommended for complex tasks)
Input: ...
Output: ...
```

The more precisely each section is defined, the more stable and predictable the output.

---

## Core Principles

### 1. Specificity over brevity
Vague prompts produce vague outputs. Every ambiguous word in a prompt is a decision the model makes for you — often incorrectly.

| Weak | Stronger |
|------|---------|
| "Summarize this document" | "Summarize this document in 3 bullet points, each under 20 words, focusing on the main argument and two supporting claims" |
| "Check for errors" | "Identify factual inaccuracies in the following text. For each inaccuracy found, state the incorrect claim and explain what makes it inaccurate." |
| "Be helpful and professional" | "Respond in a formal tone appropriate for a business audience. Avoid jargon. Do not use bullet points unless explicitly requested." |

---

### 2. Positive instructions outperform negative ones
Tell the model what to do, not what to avoid. Negative instructions increase the cognitive load and can trigger the behavior you're trying to prevent.

| Negative (weaker) | Positive (stronger) |
|-------------------|---------------------|
| "Don't be vague" | "Give a specific, concrete answer with at least one example" |
| "Don't make up information" | "Only include information you can verify from the provided context. If the answer is not in the context, say so explicitly." |
| "Don't be too long" | "Respond in no more than 150 words" |

---

### 3. Chain-of-Thought for reasoning tasks
For tasks requiring multi-step reasoning, prompt the model to show its work before giving a final answer.

```
Analyze the following claim and determine whether it is supported by the evidence provided.

Think through your analysis step by step:
1. Identify the core claim being made
2. Identify what evidence is provided
3. Assess whether the evidence supports the claim
4. State your conclusion

Claim: [claim]
Evidence: [evidence]
```

This pattern significantly reduces confident-sounding but incorrect conclusions.

---

### 4. Anchor outputs with format examples
When output format matters, show an example. Even a partial example dramatically improves format adherence.

```
For each issue found, respond in this format:

ISSUE: [short label]
LOCATION: [where in the text]
EXPLANATION: [one sentence explaining the problem]
SEVERITY: [High / Medium / Low]

Example:
ISSUE: Unsupported causal claim
LOCATION: Paragraph 2, sentence 3
EXPLANATION: The text states X causes Y but no evidence is provided for this relationship.
SEVERITY: High
```

---

## Sensitivity Testing

Prompt sensitivity refers to how much a model's output changes when the prompt is slightly modified — in wording, order, or framing — without changing the underlying intent.

**Why it matters:** A sensitive prompt is an unreliable prompt. In production, small variations in user phrasing should not produce dramatically different quality outputs.

### Sensitivity Test Protocol

For a given prompt, generate variants by:

1. **Paraphrasing the instruction** — same meaning, different words
2. **Reordering sections** — move context before or after the task
3. **Changing persona framing** — "You are an expert in X" vs. no role definition
4. **Modifying politeness level** — neutral vs. direct vs. polite phrasing
5. **Adding or removing examples** — test with zero, one, and two examples

Compare outputs across all variants. If conclusions or quality differ significantly, the prompt requires hardening.

---

## Common Failure Modes

| Failure | Cause | Fix |
|---------|-------|-----|
| Hallucinated facts | No grounding instruction | Add: "Only use information from the provided context" |
| Ignored format | Format instruction too vague | Add a concrete example of expected output |
| Partial answers | Multi-part question not clearly separated | Number each sub-question explicitly |
| Overconfident tone | No hedging instruction | Add: "Express appropriate uncertainty when evidence is limited" |
| Language drift (multilingual) | System prompt in different language than input | Always write system prompts in the target language |
| Overnarrating | No conciseness constraint | Add: "Do not explain your reasoning unless asked" |

---

## Related Repositories

- [llm-evaluation-framework](https://github.com/ilya-garaeff/llm-evaluation-framework)
- [multilingual-ai-qa-toolkit](https://github.com/ilya-garaeff/multilingual-ai-qa-toolkit)
- [rag-ops-notes](https://github.com/ilya-garaeff/rag-ops-notes)

---

*Maintained by [Ilya Garaeff](https://ilyagaraeff.com) — AI/ML Operations Specialist*
