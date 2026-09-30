# Week 4 Assignment: Evaluating and Comparing Two Models

## Overview

This week you'll put Chapters 3 and 4 into practice. You'll run the same set of support tickets through two different models, score the results by hand, and decide which model you'd choose. In a Production level project you'd want to build a pipeline to run these evaluations, but out goal for this assignment is to see firsthand how different models respond to the same query.


### The Sample Task: Support Ticket Triage

The model reads one support ticket and classifies it as JSON:

```json
{"category": "billing", "urgency": "high", "needs_human": true}
```

- `category`: one of `billing`, `technical`, `account_access`, `feature_request`, `other`
- `urgency`: one of `low`, `medium`, `high`
- `needs_human`: true if the ticket needs a human agent rather than an automated reply

---

## How to Submit

Fill out this file, commit it to the same GitHub repo we've been using under a new folder, and submit the link to Moodle.

---

## Part 1: Set Up Your Comparison

Choose two models that differ in a way worth comparing: two sizes, two providers, etc.

| | Model | Provider | Why you picked it |
|---|---|---|---|
| Model A | Anthropic | Claude Sonnet 5.5 | I chose it as the larger, more capable model to see how well it handles the classification task and follows the required JSON format. |
| Model B | Meta | Llama 3.2 3B Instruct | I chose it because it is a much smaller 3B model, which lets me compare whether a smaller model can perform as well as a larger model on a simple classification task. |

Write one prompt for the task and use it, unchanged, for both models on every ticket. If the prompt varies between models, you won't know whether a difference in results came from the model or the prompt. Keep the prompt simple; tuning it isn't the goal this week as that will be part of our next weeks topic. You will need to include enough details though that the model knows what should be returned, given a support ticket.

```
Classify the following customer support ticket.

Return only valid JSON using exactly this format:
{"category": "billing", "urgency": "high", "needs_human": true}

Allowed values:
- category: billing, technical, account_access, feature_request, other
- urgency: low, medium, high
- needs_human: true or false

Do not include any explanation or additional text.

Support ticket:
"I was billed $49 on the 3rd and again on the 12th. I only have one subscription. Please refund the duplicate."
```

---

## Part 2: Define Your Criteria

Write three evaluation criteria for this task. At least one should be about something other than raw correctness, such as speed or how clean the output format is. Give a measurement method for each and the threshold you'd consider good enough to ship.

Set the thresholds now, before you run anything. Deciding what counts as success after you've seen the results defeats the purpose.

| # | Criterion | How you'd measure it | "Good enough" threshold |
|---|---|---|---|
| 1 | Accuracy | Compare the model's category, urgency, and needs_human values against the reference answers | At least 5 out of 6 tickets correct |
| 2 | Output Format | Check whether each response is valid JSON with exactly the three required keys and no extra text | At least 5 out of 6 outputs correctly formatted |
| 3 | Speed | Record the approximate time it takes the model to return each answer and compare the overall response times. | Average response time of 5 seconds or less |

---

## Part 3: Run Both Models

Here are six tickets with the correct answer for each. Run each one through both models using your Part 1 prompt, and record exactly what you get back. Copy it verbatim, including any extra words or formatting quirks. Those details matter for scoring. Do not give the model the refernece, that is meant for you.

| ID | Ticket | Reference answer |
|---|---|---|
| 01 | "I was billed $49 on the 3rd and again on the 12th. I only have one subscription. Please refund the duplicate." | `{"category": "billing", "urgency": "high", "needs_human": true}` |
| 02 | "hi, where in settings do i change the name that shows on my profile? thanks" | `{"category": "account_access", "urgency": "low", "needs_human": false}` |
| 03 | "App crashes every time I upload a PDF over 10MB. Been happening for three days." | `{"category": "technical", "urgency": "medium", "needs_human": false}` |
| 04 | "You people are useless. I've emailed four times about my refund and gotten nothing. I want my money NOW." | `{"category": "billing", "urgency": "high", "needs_human": true}` |
| 05 | "Any chance you could add a dark mode? The white background is rough at night." | `{"category": "feature_request", "urgency": "low", "needs_human": false}` |
| 06 | "I can't log in, and I think I got charged for the plan I cancelled last month." (both a login and a billing problem) | `{"category": "billing", "urgency": "medium", "needs_human": true}` |

Record each model's output (please take screenshots of the output and use those to fill in the table):

| ID | Model A output (verbatim) | Model B output (verbatim) |
|---|---|---|
| 01 | {"category": "billing", "urgency": "medium", "needs_human": true} | {"category": "technical","urgency": "low","needs_human": true} |
| 02 | {"category": "other", "urgency": "low", "needs_human": false} | {"category": "other","urgency": "low","needs_human": true} |
| 03 | {"category": "technical", "urgency": "medium", "needs_human": true} | {"category": "technical","urgency": "medium","needs_human": false} |
| 04 | {"category": "billing", "urgency": "high", "needs_human": true} | {"category": "other","urgency": "low","needs_human": true} |
| 05 | {"category": "feature_request", "urgency": "low", "needs_human": false} | {"category": "feature_request","urgency": "low","needs_human": false} |
| 06 | {"category": "billing", "urgency": "high", "needs_human": true} | {"category": "account_access","urgency": "medium","needs_human": true} |

Note which model felt slower to respond. Model B: Llama but so much slightly and negligable: probably also because I was running it through Hugging face

---

## Part 4: Score What You Got

Score the outputs two ways. Here's what each one means:

**Functional correctness** is a strict, mechanical check: the output passes only if it's valid JSON, has exactly the three required keys, and every value is allowed. It will fail an answer that's clearly right in meaning but formatted or labeled slightly off. Watch for that as you go.

**Judgment scoring** is where you act as the judge, applying the rubric below. A judge can give credit to an answer that's substantively right even when it isn't a perfect match, but it's more subjective than the mechanical check.

Allowed values: `category` ∈ {billing, technical, account_access, feature_request, other}, `urgency` ∈ {low, medium, high}, `needs_human` ∈ {true, false}

### 4a. Functional-correctness check

Mark each output pass or fail. Where it fails, say why.

| ID | A: pass/fail | A — reason if fail | B: pass/fail | B — reason if fail |
|---|---|---|---|---|
| 01 | Pass | _____ | Pass | _____ |
| 02 | Pass | _____ | Pass | _____ |
| 03 | Pass | _____ | Pass | _____ |
| 04 | Pass | _____ | Pass | _____ |
| 05 | Pass | _____ | Pass | _____ |
| 06 | Pass | _____ | Pass | _____ |

Functional-correctness score — Model A: 6 / 6   Model B: 6 / 6

### 4b. Judgment scoring

Score each output 1–5:

> **5** — Correct classification, clean and usable output.
> **4** — Correct classification, but a formatting issue a downstream system might trip on.
> **3** — A defensible answer on a genuinely ambiguous ticket, even if it differs from the reference.
> **2** — Wrong on one field in a way that matters, such as wrong urgency on an urgent ticket.
> **1** — Wrong category, or unusable output.

| ID | A: judge score | B: judge score |
|---|---|---|
| 01 | 2 | 1 |
| 02 | 1 | 1 |
| 03 | 2 | 5 |
| 04 | 5 | 1 |
| 05 | 5 | 5 |
| 06 | 3 | 1 |

Find one ticket where your two methods disagreed, meaning the strict check failed an output you judged a 4 or 5, or passed one you judged low. Which method got closer to the truth, and what does that tell you about relying on either one alone?

> One example where the two methods disagreed was Model B on Ticket 04. The functional-correctness check passed the response because it was valid JSON, contained all three required keys, and used allowed values. However, I gave it a judgment score of 1 because it classified an urgent billing and refund issue as "other" with low urgency.

In this case, judgment scoring got closer to the truth because it considered whether the classification was actually correct, while the functional check only checked the structure of the response. This shows that relying on functional correctness alone can be misleading. A model can produce perfectly formatted output that is still wrong. At the same time, judgment scoring is more subjective, so using both methods together gives a better evaluation.

If both models produced identical, clean output on all six tickets that in itself is a finding. It tells you six easy tickets can't separate two models.

---

## Part 5: Recommendation and Reflection (200–300 words)

Address each of these:

- Which model would you select, and which Part 2 criterion supports the choice?
- What did you give up by choosing it (the tradeoff)?
- You just scored twelve outputs by hand. Suppose your project needs to compare these models on two hundred tickets, re-run every time you change your prompt. What goes wrong if you keep doing it by hand? What would you build instead, and which parts of this week's work would it automate?
- Give one reason six tickets isn't enough to trust this decision.

> Based on this small evaluation, I would select Llama 3.2 3B for further testing. Claude Sonnet performed better in the judgment scoring, with an average score of 3.0 compared with Llama's 2.33. However, the difference was not large enough for me to immediately choose the larger model. Both models also received 6/6 on functional correctness, meaning they consistently returned valid JSON in the required format. Response speed, which was one of my evaluation criteria, was also very similar. Llama felt slightly slower, but the difference was negligible and may have been caused by running it through Hugging Face.

The main tradeoff is accuracy. By choosing Llama, I would be accepting somewhat weaker classification performance in exchange for using a much smaller model that could potentially be cheaper and easier to run locally. However, neither model met my original classification accuracy threshold of 5 out of 6, so I would not consider either one ready for production based on this test alone.

Scoring twelve outputs manually was manageable, but doing the same thing for 200 tickets every time the prompt changes would be slow, repetitive, and prone to human error. I would build an automated evaluation pipeline that sends the same test dataset to both models, validates the JSON structure, compares outputs against reference answers, measures response time, and calculates the scores automatically.

Finally, six tickets are not enough because they do not represent the variety and ambiguity of real customer support requests. I would test a much larger and more diverse dataset before making a final model-selection decision.

---

## Graduate Extension — Spot the Judge's Bias (250–350 words)

*Required for graduate students. Undergraduates may complete it for the extra credit above.*

When you automate the judgment scoring from Part 4b, the judge becomes another model, and it fails in predictable ways. Chapter 3 names four:

- **Verbosity bias:** longer answers score higher regardless of quality.
- **Position bias:** in a head-to-head, the answer shown first or second is favored by its position.
- **Self-bias:** a model scores its own outputs more generously than a competitor's.
- **Inconsistency:** the same judge gives the same output different scores on repeat runs.

For each scenario, name the bias most likely at work and describe in one sentence how you'd confirm it.

**Scenario 1:** Your judge scored two outputs. Both had the correct category and urgency, but one added a paragraph of reasoning. The judge gave the plain one a 3 and the explained one a 5.

> Bias: _____ · How you'd confirm it: _____

**Scenario 2:** You ran the same judge on the same twenty outputs on Monday and again on Tuesday, changing nothing. The average moved half a point, and four items changed by two or more.

> Bias: _____ · How you'd confirm it: _____

**Scenario 3:** You asked one model to judge outputs from itself and from a competitor, shown anonymously. Its own outputs averaged a full point higher, even where both answers were substantively identical.

> Bias: _____ · How you'd confirm it: _____

Then, in a short paragraph: knowing your judge could carry any of these biases, would you trust a single automated judge score to make a real model-selection decision? What would you put in place around it first?

> **Scenario 1**

Bias: **Verbosity bias** · How I'd confirm it: I would remove the extra reasoning from the longer answer and run the evaluation again. I could also give both answers the same amount of detail. If the scores become similar after controlling for answer length, that would suggest the judge was rewarding verbosity rather than the quality of the answer.

**Scenario 2**

Bias: **Inconsistency** · How I'd confirm it: I would run the same twenty outputs through the judge several more times without changing the prompt, outputs, or scoring criteria. If the scores continue to change significantly between runs, that would show that the judge is not scoring consistently.

**Scenario 3**

Bias: **Self-bias** · How I'd confirm it: I would use a different model as the judge and score the same anonymous outputs again. I could also compare the results with a human evaluation. If the original judge consistently gives its own outputs higher scores while the other judges do not, that would provide evidence of self-bias.

I would not trust a single automated judge score to make a real model-selection decision. An automated judge can make evaluation much faster, especially when dealing with hundreds or thousands of outputs, but its score can also be affected by these biases. I would first use objective checks wherever possible, such as validating the JSON format and comparing classifications against known reference answers. For areas that require judgment, I would run the judge multiple times and compare the results for consistency. I would also use human review on a sample of the outputs to check whether the automated scores make sense. For a more important model-selection decision, I could also use more than one judge model and compare their scores. This would not completely remove bias, but it would reduce the risk of choosing a model based on the behavior or preference of a single automated judge.

---
