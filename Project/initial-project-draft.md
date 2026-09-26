# MealScout Agent --- Initial Project Draft

## Section 1: Problem Statement

Finding somewhere to eat can take more time than expected when a person
has a limited budget, dietary restrictions, or does not want to travel
far. A normal restaurant search can show nearby restaurants, but the
user still has to open different menus, compare prices, check distance,
look for food that meets their dietary needs, and estimate whether the
final cost will stay within their budget.

MealScout Agent will help users find specific meals from nearby
restaurants based on constraints they provide in natural language. For
example, a user could say, "I only have \$20, I want takeout within 5
miles, and I do not eat pork." MealScout would search nearby
restaurants, check available menu items, compare suitable options,
estimate the total cost including tax, and return meals that fit those
requirements.

This is more than a basic search problem because the system has to
understand several constraints at the same time and use different tools
to complete the task. Search is only one step. The agent may need to
search for restaurants, inspect menus, check distance, evaluate dietary
information, calculate the estimated total cost, compare valid options,
and help build an order. It should also know when information is missing
or uncertain instead of making assumptions, especially for dietary
restrictions or allergies.

## Section 2: Target Users

MealScout is for people who want to find food nearby without spending
time checking several restaurant websites or menus themselves. It could
be especially useful for students, people on a limited budget, people
with dietary restrictions, or anyone who wants a quick meal that meets
specific requirements.

A typical user might open MealScout when they are hungry and know what
limits they have, but do not know exactly where or what to order. They
could provide a budget, maximum travel distance, dietary restrictions,
food preferences, and whether they want pickup. The agent would then
look for meals that fit those constraints and explain the best matching
options.

A user would likely use MealScout again if it consistently finds valid
meals quickly, stays within the requested total budget, and does not
make the user repeat information. They might stop using it if prices are
inaccurate, restaurants are too far away, the agent recommends food that
violates a dietary restriction, or it invents menu information that it
cannot verify.

## Section 3: Candidate Approach

My original Week 1 idea was an AI agent that would help customers order
pizza. After thinking more about the difference between an agent and a
normal search or ordering system, I modified the idea into MealScout
Agent. Instead of working with only one pizza restaurant, MealScout
would search across nearby restaurants and help the user find a meal
based on several constraints such as budget, distance, dietary
restrictions, and food preferences. I made this change because it gives
the agent a clearer reason to use multiple tools and make decisions
across several steps instead of only taking an order from a fixed menu.

My current approach would use an LLM with prompting and
agent/tool-calling capabilities. The model would interpret the user's
natural-language request and decide which tools it needs. Possible tools
could include restaurant search, menu lookup, distance checking,
dietary-information checking, tax or total-cost calculation, and order
creation. I may also use retrieval if menu or restaurant information
needs to be stored and searched locally.

For the model, I am currently considering a hosted model because tool
calling and natural-language understanding will be important. The exact
model is not decided yet because I still need to compare capability, API
cost, and how easily different models can work with tools.

The hardest part will probably be getting reliable restaurant menu,
price, and dietary information. Restaurant information online can be
incomplete or outdated. Dietary restrictions are also important because
the model should not guess that a meal is safe just from its name. For
the first version, the agent should clearly state when dietary
information cannot be verified.

To keep the project realistic for one term, the main version will focus
on finding, evaluating, and comparing meals and then building an order
for the user to confirm. Actual payment or placing orders through
third-party restaurant services will be treated as a stretch goal. If
live restaurant data becomes too difficult to access reliably, I can use
a controlled set of restaurant and menu data while keeping the same
agent workflow.

## Section 4: First-Draft Evaluation Plan

I plan to evaluate MealScout using a set of test scenarios with
different combinations of budget, distance, dietary restrictions, and
food preferences. For example, one test could ask for a meal under \$20
within 5 miles with no pork, while another could ask for a vegetarian
meal under \$15 within 3 miles.

The main measurements will be constraint satisfaction and successful
task completion. For each test, I can check whether the recommended meal
is actually within the requested distance, whether the estimated total
including tax stays within the budget, whether the menu item exists in
the available data, and whether the agent follows the dietary
requirement. I can also track whether the agent uses the correct tools
and whether it asks for clarification when important information is
missing.

I will probably need to create my own test set because the scenarios
need known expected results. A starting goal would be for at least 90%
of the test scenarios to return a valid meal when a valid option exists,
without recommending items that break the user's stated budget or
dietary constraints.

One difficult area to measure will be dietary safety when restaurant
information is incomplete. Because of this, I also want to test whether
the agent correctly says that it cannot verify a dietary requirement
instead of guessing. Another challenge will be changing menu prices and
restaurant availability if I use live data, so the evaluation may need a
fixed test dataset for repeatable results.
