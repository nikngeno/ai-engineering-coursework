# Day 2: Document Q&A with RAG

**Name:** Nicholas Ngeno  
**Link to your completed Kaggle notebook:** https://www.kaggle.com/code/nikngeno/day-2-document-q-a-with-rag-nick

---

## Overview

This week you'll work through a RAG lesson built by Google and Kaggle, then make it your own. Part 1 is completing their RAG question-answering codelab as written. In Part 2, you'll point that same pipeline at your own documents and test it with questions you write yourself. The reflection connects what you built back to Huyen's Chapter 6.

## Learning Objectives

- Build and run a working RAG pipeline: embed documents, store them, retrieve relevant passages, and generate grounded answers.
- Adapt an existing pipeline to a new set of documents.
- Evaluate retrieval separately from generation, so you can tell which part failed.
- Connect a hands-on implementation to the RAG architecture described in the textbook.

---

## Setup

1. Open the Kaggle Learn Guide linked above and go to **Day 2**.
2. Create a free Kaggle account if you don't have one. Kaggle may ask you to verify your account with a phone number before it allows internet access in notebooks, which the codelabs need.
3. Get a free Gemini API key from Google AI Studio.
4. Store your key using **Kaggle Secrets**, as the codelab instructs. **Never paste your key into a notebook cell.** Your notebook will be shared, and anyone who sees the key can use it.

If a cell fails, check the course's troubleshooting guide for the codelabs before spending a long time debugging.

---

## Part 1: Complete the RAG Codelab (30 pts)

Find the Day 2 codelab that builds a **RAG question-answering system over documents**. Copy it into your own Kaggle account and run it from top to bottom, with every cell executing successfully.

Record what the pipeline uses:

| | Value |
|---|---|
| Embedding model | gemini-embedding-001 |
| Where the embeddings are stored (vector store/database) | ChromaDB |
| Generation model | gemini-3.8-flash |
| Number of passages retrieved per query | 1 |

**In 2–3 sentences, describe what happens between the moment a question is asked and the moment an answer comes back:**

When a user asks a question, the question is converted into an embedding and compared with the document embeddings stored in ChromaDB. The most relevant passages are retrieved and added to the prompt, and Gemini then uses that context to generate an answer.

---

## Part 2: Make It Yours (40 pts)

In your copy of the notebook, **replace the sample documents with 3–5 short documents of your own.** Documents related to your term project are recommended. Course materials, public documentation for a tool you use, or articles on a topic you know well also work. Avoid anything private or sensitive.

Keep the rest of the pipeline the same. Your notebook should show your documents, your questions, and the outputs.

### Your Documents

| | Value |
|---|---|
| What the documents are | Short documents describing the planned features, requirements, and behavior of my MealScout Agent project |
| Number of documents | 5 |
| Why you chose them | I chose them because MealScout is my term project, so using its requirements lets me test RAG on information that could realistically be used by the agent. |

Write **5 test questions** and run each through the pipeline. Your set must include:

- **2 keyword questions** that use exact names, terms, numbers, or codes from your documents
- **2 paraphrase questions** that ask about something in your documents without using its wording
- **1 unanswerable question** whose answer is **not** in your documents

| # | Question (short) | Type | Retrieved the right passage? (Yes / No / N/A) | Generated answer (correct / partly / wrong / correctly declined) |
|---|---|---|---|---|
| 1 | What search radius can a MealScout user specify? | Keyword | Yes | Correct |
| 2 | Does MealScout include estimated tax when checking a user's budget? | Keyword | Yes | Correct |
| 3 | How does MealScout prevent recommending food that may not meet someone's eating requirements? | Paraphrase | Yes | Correct |
| 4 | What information should MealScout show before someone decides to place an order? | Paraphrase | Yes | Correct |
| 5 | What payment processor will MealScout use for online orders? | Unanswerable | N/A | Correctly declined |

### Weakest Result

**Pick one question where the result wasn't fully correct (or, if everything worked, the one that came closest to failing). Was the weak point retrieval or generation? How can you tell from the notebook's output?**

The question that came closest to failing was, “What payment processor will MealScout use for online orders?” The weak point was retrieval because the documents did not contain any information about a payment processor, but ChromaDB still returned the passage about placing a takeout order because it was the closest semantic match. I could tell this from the notebook output because the retrieved passage discussed ordering but did not actually answer the question.

---

## Part 3: Reflection (30 pts, 250–350 words)

Answer all four:

- Huyen describes two families of retrievers: term-based and embedding-based. Which kind does the codelab use? Based on your keyword questions, where might the other kind have done better or worse?
- How did the pipeline handle your unanswerable question? What would happen in a real application if it handled that badly, and what would you change to fix it?
- The codelab was designed to work well on its own sample documents. What, if anything, got harder when you switched to yours?
- Your project evaluation plan is due next week with Milestone 1. Does your project need RAG? If so, what would the documents be, and if not, why not?

### Your Reflection

The codelab uses an embedding-based retriever. Each document and query is converted into an embedding, and ChromaDB retrieves the passage that is most semantically similar to the question. A term-based retriever might have performed better on some of my keyword questions, such as the question about “5 miles” or “estimated tax,” because those exact terms appear in the documents. However, it would probably perform worse on paraphrase questions where the wording of the question is different from the wording in the document. The embedding-based approach was able to recognize similarity in meaning even when the exact words were different.

The unanswerable question asked which payment processor MealScout will use. None of my documents contained that information, but the retriever still returned the passage about placing a takeout order because it was the closest match. In a real application, this could become a problem if the generation model treats a related passage as evidence and invents an answer. I would reduce this risk by adding instructions that the model should only answer when the retrieved context supports the answer and should clearly say when there is not enough information. I could also use a similarity threshold to reject weak retrieval results.

When I replaced the original Googlecar documents with my MealScout documents, retrieval became slightly harder because several documents discussed related concepts such as price, ordering, location, and restaurant selection. This made some passages semantically similar even when only one directly answered the question.

MealScout could use RAG, but it should not depend on RAG for everything. RAG would be useful for restaurant menus, dietary information, ordering policies, and other text-based information. Current prices, distance, availability, and taxes should instead come from live APIs or other current data sources.
