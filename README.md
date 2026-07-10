# 📧 AI Customer Support Email Agent
### Automated Email Support using n8n • RAG • Pinecone • Google Gemini

An intelligent customer support automation workflow built using **n8n**, **Google Gemini**, **OpenAI Embeddings**, and **Pinecone**.

The workflow automatically monitors incoming customer emails, classifies support requests, retrieves the most relevant FAQ documents using **Retrieval-Augmented Generation (RAG)**, evaluates retrieval confidence through a custom **Retrieval Confidence Gate**, and generates grounded responses before replying via Gmail.

Unlike traditional AI chatbots that answer every query, this workflow validates retrieval quality before invoking the LLM, reducing hallucinations, improving reliability, and lowering inference costs.

---

## 🚀 Features

- 📧 Automatic Gmail monitoring
- 🧠 Intent classification using Google Gemini
- 📚 Semantic FAQ retrieval with OpenAI Embeddings
- 🗄️ Pinecone Vector Database integration
- 📈 Retrieval Confidence Gate (Similarity Threshold)
- 🤖 Context-grounded response generation
- ✉️ Automatic email replies
- ❌ Hallucination prevention
- ⚡ Cost-optimized LLM usage

---

# 🏗️ Workflow Architecture

<p align="center">
<img src="screenshots/workflow.png" width="1000">
</p>

---

# 🔄 System Flow

```text
Customer Email
      │
      ▼
 Gmail Trigger
      │
      ▼
Intent Classification
 (Google Gemini)
      │
      ├──────────────┐
      │              │
Support Query      Other Email
      │              │
      ▼              ▼
Generate Query Embedding      Ignore
      │
      ▼
Pinecone Semantic Search
      │
      ▼
Retrieve Top-5 Similar FAQ Chunks
      │
      ▼
JavaScript Similarity Evaluation
      │
      ▼
Retrieval Confidence Gate
      │
      ▼
Similarity ≥ Threshold ?
      │
 ┌────┴────┐
 │         │
Yes        No
 │          │
 ▼          ▼
Gemini LLM   Fallback Response
 │
 ▼
Grounded Response
 │
 ▼
Reply to Customer
```

---

# 📖 Workflow Breakdown

## 1️⃣ Gmail Trigger

The workflow continuously listens for new emails using the Gmail Trigger node.

Each incoming email automatically starts a new workflow execution.

---

## 2️⃣ Intent Classification

Incoming emails are classified using **Google Gemini**.

Possible outcomes:

- Customer Support
- Other

Only customer support emails continue to the RAG pipeline.

Filtering irrelevant emails early reduces unnecessary vector searches and LLM API calls.

---

## 3️⃣ Semantic Retrieval

For customer support requests:

1. The customer query is converted into an embedding using **OpenAI text-embedding-3-small**.
2. Pinecone performs a semantic similarity search.
3. The **Top-5** most relevant FAQ chunks are retrieved.

Unlike keyword search, semantic retrieval identifies documents based on contextual meaning.

---

# ⭐ Retrieval Confidence Gate

This project introduces a **Retrieval Confidence Gate**, inspired by production-grade Retrieval-Augmented Generation systems.

After Pinecone returns the Top-5 FAQ chunks, a JavaScript node:

- sorts documents by similarity score
- selects the highest-ranked chunk
- extracts the similarity score
- compares it against a predefined confidence threshold

Example:

| Retrieved Chunk | Similarity |
|-----------------|-----------:|
| FAQ Chunk 1 | **0.94** |
| FAQ Chunk 2 | 0.89 |
| FAQ Chunk 3 | 0.85 |
| FAQ Chunk 4 | 0.61 |
| FAQ Chunk 5 | 0.42 |

Configured Threshold

```text
0.82
```

Decision

```text
Highest Similarity = 0.94

0.94 > 0.82

✓ Retrieval Accepted
```

If the similarity score is below the threshold, the workflow assumes that the knowledge base does not contain sufficient information and avoids generating an AI response.

Instead, a fallback email is returned.

---

# 💡 Why Retrieval Confidence Matters

Many RAG applications directly pass retrieved documents to an LLM regardless of retrieval quality.

Weak retrieval often leads to hallucinated responses.

The Retrieval Confidence Gate acts as a validation layer before generation.

Benefits include:

- Reduced hallucinations
- Lower LLM costs
- Improved response accuracy
- Better customer trust
- More production-ready workflow

---

# 🤖 Grounded Response Generation

When retrieval confidence is high:

- Only the highest-confidence FAQ chunk is passed to Google Gemini.
- The LLM is instructed to answer **only using the provided context**.
- No external knowledge is allowed.

This ensures every generated response is grounded in verified documentation.

---

# 📬 Automated Email Reply

If retrieval succeeds:

```
Customer Email
        │
        ▼
Retrieve FAQ
        │
        ▼
Generate Grounded Answer
        │
        ▼
Reply via Gmail
```

Otherwise:

```
Customer Email
        │
        ▼
Low Similarity
        │
        ▼
Fallback Response
```

Example fallback:

> We couldn't find enough information to answer your question. Please contact our support team for further assistance.

---

# 🛠️ Tech Stack

| Component | Technology |
|-----------|------------|
| Workflow Automation | n8n |
| LLM | Google Gemini |
| Embedding Model | OpenAI text-embedding-3-small |
| Vector Database | Pinecone |
| Email Integration | Gmail API |
| Programming | JavaScript |

---

# 📂 Repository Structure

```text
n8n-rag-customer-support-agent
│
├── workflow
│   └── customer-support-rag.json
│
├── screenshots
│   ├── workflow.png
│   ├── architecture.png
│   └── demo.gif
│
├── docs
│   └── faq.pdf
│
└── README.md
```

---

# 🎯 Future Improvements

- Multi-document answer synthesis
- Hybrid Search (Keyword + Semantic)
- Dynamic similarity thresholds
- LLM-based retrieval evaluation
- Answer verification before email delivery
- Multi-turn conversation memory
- Multiple knowledge base support
- Analytics dashboard
- Support ticket escalation

---

# 📚 Skills Demonstrated

- Retrieval-Augmented Generation (RAG)
- Vector Databases
- Semantic Search
- Prompt Engineering
- Workflow Automation
- LLM Integration
- Embedding Models
- Pinecone
- Production AI Workflow Design
- Retrieval Evaluation

---

# 📸 Demo

### Workflow

<img src="screenshots\Workflow.png" width="1000">

### Example Response

<img src="screenshots\email_reply.png" width="1000">

