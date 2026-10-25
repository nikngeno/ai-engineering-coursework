# Week 7 Assignment — Agents Course + Your Own Agent

**Chapter:** Huyen, *AI Engineering*, Ch. 6 — Agents & Memory (pg. 275–305)
**External lesson:** Hugging Face *AI Agents Course*, Unit 1: Introduction to Agents — https://huggingface.co/learn/agents-course/unit1
**Points:** 100
**Due:** End of Week 7

---

## Submission Instructions

1. Copy this file into your Assignment 1 repo, keeping the filename **`week7-agents-lab.md`**.
2. Fill in every blank (`_____`) and bracketed placeholder directly in the file.
3. Make sure your Hugging Face Space is public so I can open it.
4. Push your commit, then submit a link to the file as instructed for this course.

**Name:** Nicholas Ngeno
**Link to your Hugging Face Space:** https://huggingface.co/spaces/nikngeno/nik_agent
**Link to (or screenshot of) your Unit 1 certificate:** https://huggingface.co/spaces/agents-course/unit_1_quiz/discussions/771

---

## Overview

This week you'll work through the first unit of Hugging Face's free AI Agents Course, then build on it. Unit 1 covers what agents are, how LLMs act as an agent's "brain," how agents use tools, and the Think → Act → Observe cycle. It ends with building a first agent using `smolagents`, publishing it as a Hugging Face Space, and taking a quiz that earns the course's **Certificate of Fundamentals of Agents**.

Then you'll make the agent your own: give it tools that do something you care about, push it on multi-step tasks, and study how it fails.

## Learning Objectives

- Explain how an agent plans, calls tools, and uses observations to decide its next step.
- Build a working agent with multiple tools, including one you write yourself.
- Identify and categorize agent failures using the failure modes in Huyen Ch. 6.
- Decide whether an agent (as opposed to RAG or a single prompt) is the right design for a given problem.

---

## Setup

1. Create a free Hugging Face account if you don't have one.
2. Start at the Unit 1 link above and work through the unit in order.
3. When you reach the hands-on tutorial ("Let's Create Our First Agent Using smolagents"), duplicate the template Space as instructed and follow along.

If you store any access token or API key, add it as a **Space secret**, never in your code. Your Space is public, and anyone can read its files.

---

## Part 1: Complete Unit 1 and Earn the Certificate (25 pts)

Work through all of Unit 1, including the tutorial, and **pass the end-of-unit quiz** to earn the Certificate of Fundamentals of Agents. Follow the quiz link in the course itself, since the certification app has moved before.

Put the certificate link or screenshot at the top of this file.

**In 2–3 sentences, explain the Think → Act → Observe cycle in your own words, using something your agent actually did as the example:** The Think → Act → Observe cycle means the agent decides what to do, calls a tool, and uses the result to choose its next step. My agent called plan_study_sessions to check whether 12 study hours could fit into 3 days with a 3-hour daily limit. The tool returned 4 hours per day and marked the plan infeasible, but the agent then used the wrong dictionary key to read the result, causing an error.
---

## Part 2: Build Your Agent (40 pts)

> Working submission copy. Local runs use Ollama `llama3.2:3b`. The public Space and any hosted-model results still need verification. Review the reflection to ensure it matches your own experience of the reading and course.

Extend the agent in your duplicated Space so that it has:

- **At least 2 tools** in total
- **At least 1 custom tool that you write yourself** (not a pre-made one). Tools related to your term project are recommended, but anything useful works: a unit converter, a calculator for a formula from another class, a lookup over a small dataset, a text formatter, etc.

**Your custom tool:**

| | Value |
|---|---|
| Tool name | `plan_study_sessions` |
| What it does | Divides study hours across available days, checks the daily limit, and reports whether the work fits. |
| Inputs and output | Inputs: `total_hours` (float), `days_available` (positive int), `max_hours_per_day` (float). Output: dictionary containing `hours_per_day`, `capacity_hours`, `shortfall_hours`, `feasible`, and the supplied hours/days. |

Paste your custom tool's code here:

```python
import math
from smolagents import tool

@tool
def plan_study_sessions(total_hours: float, days_available: int, max_hours_per_day: float) -> dict:
    """Divide study work evenly across available days and check daily capacity.

    Use this to calculate or compare study schedules, not to create calendar events.
    An infeasible result means the work cannot fit within the daily limit.

    Args:
        total_hours: Finite number of study hours required, greater than zero.
        days_available: Positive whole number of study days; include today only if the user allows it.
        max_hours_per_day: Finite positive daily study limit in hours, not minutes.

    Returns:
        Dictionary with keys total_hours, days_available, hours_per_day,
        capacity_hours, shortfall_hours, feasible. Use hours_per_day for workload.
    """
    if not math.isfinite(total_hours) or total_hours <= 0:
        raise ValueError("total_hours must be finite and greater than zero.")
    if isinstance(days_available, bool) or not isinstance(days_available, int) or days_available <= 0:
        raise ValueError("days_available must be a positive whole number.")
    if not math.isfinite(max_hours_per_day) or max_hours_per_day <= 0:
        raise ValueError("max_hours_per_day must be finite and greater than zero.")
    capacity = days_available * max_hours_per_day
    return {
        "total_hours": total_hours,
        "days_available": days_available,
        "hours_per_day": round(total_hours / days_available, 4),
        "capacity_hours": round(capacity, 4),
        "shortfall_hours": round(max(0.0, total_hours - capacity), 4),
        "feasible": total_hours <= capacity,
    }

```

**What did you write in the tool's name, description, and argument descriptions to help the agent know when and how to use it? Did you have to revise them?** The name describes the action: planning study sessions. The description says it calculates workloads and checks capacity, and explicitly says it does not create calendar events. The argument descriptions specify hours rather than minutes, positive whole days, and whether today counts. Yes, the description needed revision. In the initial run, the agent indexed a nonexistent workload key even though the tool returned `hours_per_day`. I revised the return description to list every exact dictionary key and identify `hours_per_day` as the workload. The other registered tool, `get_current_time_in_timezone`, supplies the current local date/time.

Now give your agent **3 multi-step tasks**, meaning tasks that require more than one tool call or more than one step to complete. At least one task should require using **two different tools** together.

| # | Task (short) | Tools the agent called, in order | Number of steps | Succeeded? (yes / partly / no) |
|---|---|---|---|---|
| 1 | Compare 12 hours across 3 days versus 6 days, with a 3-hour daily limit. | None in hosted retry: model request rejected before code generation | 1 attempted action; 0 tool calls | No |
| 2 | Read Los Angeles time, calculate the deadline four days later, and plan 8 hours across four study days. | None: model request rejected before code generation | 1 attempted action; 0 tool calls | No |
| 3 | Test 15 hours across 3 days at 4 hours/day, then revise to 5 days if needed. | None: model request rejected before code generation | 1 attempted action; 0 tool calls | No |

---

> These rows report actual hosted attempts using `Qwen/Qwen3-Coder-30B-A3B-Instruct`; see `agent_app/hosted_lab_runs/task1.json` through `task3.json`. Hugging Face rejected each request because the model was unsupported by the account's enabled providers. Local retries of tasks 1 and 2 timed out. The initial local task 1 run did execute `plan_study_sessions` once before the KeyError analyzed below. No completed run has yet demonstrated two different tools together. Resolve model access, rerun all three prompts, and replace this table with those logs before submitting for full credit.

## Part 3: Failure Analysis (15 pts)

Find **one clear failure**. If none of your tasks failed, try to make the agent fail: give it an ambiguous request, a task no tool can handle, bad input for your custom tool, or a task that needs many steps.

Paste the relevant part of the agent's step log here (trim it to the steps that matter):

```
Actual excerpt from the initial local llama3.2:3b run of task1 (before the tool description revision).
Copied from lab-run-console.txt; formatting shortened to the code and error that matter.

Step 1 executed:
plan_1 = plan_study_sessions(total_hours=12, days_available=3, max_hours_per_day=3)
print("Plan 1:")
print(f"Daily workload: {plan_1['daily\tworkload']}")

Observation:
Plan 1:
Code execution failed: Could not index {'total_hours': 12, 'days_available': 3,
'hours_per_day': 4.0, 'capacity_hours': 9, 'shortfall_hours': 3, 'feasible': False}
with 'daily\tworkload': KeyError: 'daily\tworkload'

The model also wrote plan_2['daily_workload'] later in the same code block,
but execution stopped at the first KeyError, before the second tool call.
The first action step took 220.25 seconds. This excerpt demonstrates the error,
not the eventual outcome of that interrupted initial run.

```

**What went wrong?** The initial comparison run called the correct tool, but then tried to read a dictionary key that the tool does not return. The tool calculated 4 hours/day, 9 hours of capacity, and a 3-hour shortfall correctly. The generated Python raised a KeyError while trying to display the workload, so that code block stopped before it reached the second tool call. This excerpt shows a failed action step, not the outcome of a completed run.

**Which kind of failure is it, using the categories in Huyen Ch. 6? Explain why it fits that category.** This is a planning failure in the generated execution code: the agent chose an invalid field when interpreting a tool result. It is closest to Huyen's wrong-parameter/invalid-tool-use examples, although the specific mistake is an output-field lookup rather than a bad input argument. It is not a numerical bug in the tool, because the returned calculation was correct. Huyen separates planning failures from tool failures and efficiency issues in her [Agents chapter adaptation](https://huyenchip.com/2025/01/07/agents.html).

**What change would you make to prevent it (tool description, prompt, a new tool, a limit on steps, etc.)?** I revised the return description to list the exact output keys, especially `hours_per_day`. I would also have the agent print and inspect the returned dictionary before extracting fields. A short example using `result['hours_per_day']` would make the interface clearer. The shorter system prompt reduces unrelated examples and preserves the code/observation loop. A fresh run is needed to establish whether these changes prevent recurrence; a description change alone is not proof of a fix.

---

## Part 4: Reflection (20 pts, 250–350 words)

Answer all four:

- What did the course's explanation of agents make clearer than the textbook did, or vice versa? Point to something specific.
- How much did your tool's description matter to whether the agent used it correctly? What does that suggest about designing tools for agents?
- Last week you built a RAG pipeline. For a question like "what does our policy say about X?" versus "book me a meeting with X," which design fits each, and why?
- Your Milestone 1 is due this week. Would your project benefit from being an agent, or is a simpler design better? Justify your answer.

**Your reflection:**

The Hugging Face course presents agents through the Think, Act, Observe cycle, while Huyen explains the larger design choices behind planning, tool selection, and evaluation. The course's code-and-observation format makes the handoff concrete: the model proposes Python, a tool executes it, and the returned observation becomes evidence for the next decision. Huyen's distinction between planning failures and tool failures helps explain why a correct calculation can still lead to a failed agent step.

The study-planning tool shows why descriptions matter. Its arguments identify the units, positive-input requirements, and meaning of an available day. However, the initial agent run still tried to access a nonexistent workload key. The calculation was correct, but the generated code misunderstood the output interface. Listing the exact return keys makes that interface clearer. This suggests tool design includes input validation, predictable outputs, and examples, as well as implementing the underlying function. Descriptions help, but actual execution logs are necessary to evaluate their effectiveness.

For a question such as “what does our policy say about X?”, RAG fits because the main job is retrieving relevant policy passages and producing a supported answer. Booking a meeting requires actions: identifying the person, checking availability, choosing a suitable time, and creating the event. That needs tools and an action workflow. Retrieval alone cannot create the meeting.

My MealScout project could benefit from an agent because finding a meal under a budget may require restaurant search, menu lookup, distance checking, and total-cost calculation. Different results may require different next steps. However, I would start with a controlled menu dataset and a small, predictable workflow. RAG could support menu and policy questions, while deterministic tools check prices and constraints. Missing dietary information should remain unknown, and the user should confirm an order before any purchase action. This keeps the first version manageable while preserving a reason for tool use.

---

## Part 5: Graduate Extension — MCP Fundamentals (15 pts)

*Graduate students required. Undergraduates are encouraged to read Unit 1 as optional background.*

Complete **Unit 1 of Hugging Face's MCP Course** (MCP Fundamentals, Architecture and Core Concepts) — https://huggingface.co/learn/mcp-course/unit1/introduction — and take its end-of-unit quiz.

**Link to (or screenshot of) your MCP Unit 1 quiz result:** Not required — undergraduate submission.

In 200–300 words: look at the tools your agent uses. Which of them, if any, would make more sense exposed through an MCP server instead of defined directly in the agent's code? What would you gain (reuse, access to live systems, other clients using the same tools), and what would you give up (setup, security, trust in the server)? Tie your answer to the host / client / server roles from the unit.

**Your analysis:** Not required — undergraduate submission.

---

## Grading

| Component | Undergraduate | Graduate |
|---|---|---|
| Unit 1 and certificate (Part 1) | 25 pts | 20 pts |
| Build your agent (Part 2) | 40 pts | 35 pts |
| Failure analysis (Part 3) | 15 pts | 15 pts |
| Reflection (Part 4) | 20 pts | 15 pts |
| Graduate extension (Part 5) | — | 15 pts |
| **Total** | **100 pts** | **100 pts** |

### Rubric

| Level | Criteria |
|---|---|
| **Full credit** | Certificate earned, and the Think → Act → Observe explanation uses the student's own agent. The public Space runs with at least 2 tools, including a working custom tool with a clear description. All 3 tasks are logged, and at least one uses two tools together. The failure is backed by a log excerpt, categorized correctly with reasoning, and paired with a specific fix. Reflection connects the student's own results to Huyen Ch. 6. |
| **Partial credit** | Certificate missing, or the Space doesn't run. Only pre-made tools are used, or the custom tool doesn't work. Fewer than 3 tasks, or tasks that need only a single step. The failure is described without a log, not categorized, or the fix is vague. Reflection restates the chapter instead of the results. |
| **No credit** | Not submitted, the Space link doesn't work or is private, or results appear fabricated (e.g., logs that don't match what the Space produces). |

---

## A Note on Scope

eekweek7-agents-labeek
Also note: **Project Milestone 1 is due this week.** Unit 1 is a big unit, so start early in the week and don't leave the course and the milestone for the same night.
