# Week 2 Assignment: Hugging Face Hub Scavenger Hunt

**Graduate Extension Included**

## Overview

Same fields I walked through in Monday's demo: parameter count/size, architecture family, license, tokenizer/vocab size. Pick 3 models, record those fields, run a tokenizer comparison across languages, check context window against this week's reading, then write a short reflection tying it back to a real project decision.

*Same order I used in Monday's demo: parameter count/size near the top of the card, architecture family in the description, license in the metadata, tokenizer/vocab size in tokenizer_config.json (or just test the model directly in a tokenizer tool).*

## How to Submit

1. Fill out this file directly (replace the `_____` placeholders and bracketed instructions with your answers).
2. Commit this file to the same GitHub repo you created for Assignment 1, using this exact filename: `week2-tokenizer-model-comparison.md`.
3. Push your commit, then submit a link to the file as instructed for this course.

---

## Part 1: Choose 3 Models

1. Go to huggingface.co/models.
2. Pick 3 models that actually make a meaningful comparison — not three near-identical variants of the same model. At least 2 different organizations/families, ideally a mix of sizes (small under ~3B, mid-size, larger).
3. Pick based on your own interests. Got a project idea? Use models you'd actually consider for it.

## Part 2: Record Your Findings

Where to find each field, if you get stuck:
- **Parameter count / size** — near the top of the card, sometimes right in the model's name (e.g. "7B" = 7 billion parameters).
- **Architecture family** — in the description text, or config.json under "Files and Versions."
- **License** — shown as a tag near the top, and always in the YAML metadata block.
- **Tokenizer / vocab size** — check tokenizer_config.json or config.json under "Files and Versions" for vocab_size. Can't find it? Note "not published" — that's a useful observation on its own.

| Model | Link | Parameter count / size | Architecture family | License | Tokenizer / vocab size |
|---|---|---|---|---|---|
| Model 1: meta-llama/Llama-3.1-8B-Instruct | https://huggingface.co/meta-llama/Llama-3.1-8B-Instruct | 8B | optimized transformer architecture | LLAMA 3.1 COMMUNITY LICENSE AGREEMENT | 128,256 |
| Model 2: GPT-2 | https://huggingface.co/openai-community/gpt2 | 124M | Decoder-only Transformer | MIT License | 50,257 |
| Model 3: DeepSeek-R1 | https://huggingface.co/deepseek-ai/DeepSeek-R1 | 684B | Multi-head Latent Attention (MLA) and DeepSeekMoE | MIT License | 129,280  |

## Part 3: Tokenizer Comparison Exercise

Use a tokenizer tool that supports multiple model families (tiktokenizer.vercel.app works) and test all 3 models with the same three inputs:

- **Test sentence (use this exact sentence for all 3 models):** "I love learning about artificial intelligence."
- **Language A:** translate the test sentence into a Latin-script European language — Spanish, French, German, whatever. Same translation across all 3 models.
- **Language B:** translate it into a non-Latin-script language — Japanese, Arabic, Korean, Hindi, your call. Same translation across all 3 models.

| Model | Test sentence tokens | Language A used | Language A tokens | Language B used | Language B tokens |
|---|---|---|---|---|---|
| Model 1 | meta-llama/Llama-3 | 7 | Spanish | 9 | Arabic | 13 |
| Model 2 | gpt2 | 7 | Spanish | 12 | Arabic | 30|
| Model 3 | DeepSeek-R1 | 7 | Spanish | 9 | Arabic | 12 |

## Part 4: Context Window Check

For each model, look up its context window — the max tokens it can handle in one request. Usually on the card or in the config file.

| Model | Context window (tokens) | Source (URL or where you found it) |
|---|---|---|
| Model 1 | 128k | https://huggingface.co/meta-llama/Llama-3.1-8B-Instruct?|
| Model 2 | 1024 | https://huggingface.co/openai-community/gpt2/blob/main/config.json |
| Model 3 | 128k | https://huggingface.co/deepseek-ai/DeepSeek-R1|

**Now do the math for at least one model:** Chapter 2 is roughly 62 pages. Using ~500–600 words/page and ~0.75 words/token, estimate the total token count. Would the whole reading fit in that model's context window in one API call, with room left for a response? Show your work and your conclusion.

> **Model: meta-llama/Llama-3.1-8B-Instruct**

Chapter 2 is about **62 pages**, with approximately **500–600 words per page**.

**Step 1: Estimate the number of words**

62 × 500 = **31,000 words**

62 × 600 = **37,200 words**

So Chapter 2 contains approximately **31,000–37,200 words**.

**Step 2: Convert words to tokens**

Using the estimate of **0.75 words per token**:

31,000 ÷ 0.75 ≈ **41,333 tokens**

37,200 ÷ 0.75 = **49,600 tokens**

Therefore, Chapter 2 would require approximately **41,333–49,600 tokens**.

**Step 3: Compare with the model's context window**

Llama 3.1 has a context window of about **128K (131,072) tokens**.

131,072 − 49,600 = **81,472 tokens remaining** in the higher estimate.

**Conclusion:** Yes, the entire Chapter 2 reading should fit into Llama 3.1's context window in a single API call. Even using the higher estimate of about 49,600 tokens, there would still be roughly **81,000 tokens available** for the instructions, system prompt, and model response.


## Part 5: Comparison Reflection (300–400 words)

Answer all four:

- What's the biggest difference between your 3 models — size, architecture, license, tokenizer, something else?
- If you had to pick one for a real project, which one and why? Don't just say "the biggest one" — factor in license restrictions and whether the project actually needs that much size.
- Would your pick change for a multilingual or cost-sensitive use case, based on what you found in Part 3? Why or why not?
- Would your pick change for a use case involving long documents (full reports, long transcripts), based on the context window math in Part 4? Why or why not?

> **1. What's the biggest difference between your 3 models — size, architecture, license, tokenizer, something else?**

The biggest difference for me is **size and architecture**. GPT-2 is the smallest with about 124 million parameters. Llama 3.1 has 8 billion parameters, while DeepSeek-R1 is much bigger with about 671 billion total parameters, although only 37 billion are active at a time.

The licenses are also different. GPT-2 and DeepSeek-R1 use the MIT license, which is more open. Llama 3.1 uses Meta's Community License, which has more rules around how the model can be used. So when choosing a model, I would not only look at how powerful it is, but also whether its license works for my project.

**2. If you had to pick one for a real project, which one and why?**

I would choose **Llama 3.1 8B Instruct** for most projects. It seems like a good middle ground. GPT-2 is much smaller, but it is also older and not as capable as the newer models. DeepSeek-R1 is very powerful, but it is also much larger than what I would need for a normal application.

For something like a chatbot, question-answering system, or summarization tool, I do not think I would need a model as large as DeepSeek-R1. Llama 3.1 should be able to handle those tasks while using fewer resources. I would just need to make sure that Meta's license allows what I want to do with the project.

**3. Would your pick change for a multilingual or cost-sensitive use case, based on what you found in Part 3? Why or why not?**

For a multilingual project, I would still probably choose between **Llama 3.1 and DeepSeek-R1**. From my tokenizer test, both models handled English and Spanish with similar token counts. They both used 7 tokens for English and 9 for Spanish. For Arabic, Llama used 13 tokens while DeepSeek used 12.

This showed me that the language being used can affect how many tokens are needed. For a cost-sensitive project, I would probably stick with **Llama 3.1 8B** because it is much smaller than DeepSeek-R1 and should still be good enough for many common tasks. I would rather use a smaller model that does the job than pay for a much larger model that I do not really need.

**4. Would your pick change for a use case involving long documents based on the context window math in Part 4? Why or why not?**

I would still choose **Llama 3.1** for long documents. Llama 3.1 and DeepSeek-R1 both have a context window of about 128K tokens, while GPT-2 only supports about 1,024 tokens.

In Part 4, I estimated that Chapter 2 would be around **41,333 to 49,600 tokens**. This means the whole chapter could fit inside Llama 3.1's context window in one request and still leave plenty of room for the model's response.

Since Llama 3.1 can already handle a document that large, I would not choose DeepSeek-R1 just because it is bigger. I would only choose DeepSeek-R1 if I needed stronger reasoning for the task, not just a larger context window.


## Part 6: Graduate Extension — Paper / Technical Report Analysis (300–400 words)

*Graduate students required.*

Pick one of your 3 models that has a linked paper or technical report on its card (most do). Read enough of it to answer:

- One real detail from the paper that's not on the model card — training data composition, a specific benchmark, a stated limitation, whatever you find.
- At least one limitation or tradeoff the authors admit to themselves.
- Your own take: does reading the paper change how much you'd trust this model for a real project vs. just reading the card? Why or why not?

> [Write your analysis here]

## Grading (10 pts total)

| Component | Undergrad | Grad |
|---|---|---|
| Findings table (Part 2, incl. tokenizer field) | 3 pts | 3 pts |
| Tokenizer comparison exercise (Part 3) | 2 pts | 1 pt |
| Context window check (Part 4) | 2 pts | 1 pt |
| Comparison reflection (Part 5) | 3 pts | 2 pts |
| Graduate extension (Part 6) | — | 3 pts |
| **Total** | **10 pts** | **10 pts** |

*If a model's license, architecture, or vocab size isn't clearly labeled, say so in your reflection — not every card is well documented, and noticing that is a useful takeaway on its own.*
