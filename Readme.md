
# AI Personal Knowledge Base  
**Production-Grade Portfolio Project**

---

## What is This Project?

**AI Personal Knowledge Base** is a private, continuously-updating AI system that reads your files, links, cloud drives, and workspace tools, then lets you chat with **all of your personal knowledge** using natural language.

Unlike a normal chatbot, this system:
- understands your **private documents**
- keeps itself **automatically in sync**
- answers with **full document context and user isolation**

---

## Real-World Use Cases

### For Developers / Students
- Upload lecture notes, coding documentation, and interview preparation material
- Ask:
  - *“Explain React hooks from my notes”*
  - *“What is the best sorting algorithm mentioned in my CS class files?”*

### For Professionals
- Legal: *“Find the remote-work policy from the company handbook”*
- Sales: *“What did the Q4 report say about churn risks?”*
- Research: *“Summarize all papers mentioning transformers”*

### For Teams
- Shared private knowledge bases
- Automatic synchronization from Drive and Notion
- Subscription plans with usage limits and unlimited storage for paid users

---

## Complete Tech Stack and Responsibilities

| Component | What it does | Role in this project |
|--------|-------------|---------------------|
| Next.js 15 | Web application + backend APIs | Dashboard, file upload, chat UI, API routes triggering automation |
| **:contentReference[oaicite:0]{index=0}** | Workflow automation engine | Watches file sources, processes documents, creates embeddings, stores vectors |
| **:contentReference[oaicite:1]{index=1}** | Authentication & user management | Login, session handling, per-user data isolation |
| **:contentReference[oaicite:2]{index=2}** | Rate limiting and abuse protection | Enforces free vs paid chat limits |
| **:contentReference[oaicite:3]{index=3}** | Hosting and previews | Deploys the Next.js application |
| **:contentReference[oaicite:4]{index=4}** | CI/CD automation | Builds, tests, and validates on every push |
| **:contentReference[oaicite:5]{index=5}** | Virtual servers | Hosts n8n 24/7 |
| **:contentReference[oaicite:6]{index=6}** | Vector database | Stores embeddings for semantic search |
| **:contentReference[oaicite:7]{index=7}** | Monitoring and observability | Tracks API latency and background job performance |
| **:contentReference[oaicite:8]{index=8}** | External data source | Auto-indexes new and updated files |
| **:contentReference[oaicite:9]{index=9}** | External data source | Auto-syncs workspace pages |
| **:contentReference[oaicite:10]{index=10}** | Metadata store | Tracks document status and versions |


## Complete Workflow (Interview Explanation)

```

1. USER ACTION:
   Upload PDF or link
   → Next.js API
   → n8n webhook

2. n8n PIPELINE:
   Download file
   → extract text
   → chunk
   → embed
   → store in Pinecone

3. BACKGROUND INDEXING:
   Drive trigger
   → same pipeline
   Notion trigger
   → same pipeline

4. CHAT FLOW:
   User question
   → embed query
   → Pinecone similarity search
   → LLM
   → final answer

````

---

## Data Flow With Strict User Isolation

Every stored vector contains metadata for filtering:

```json
{
  "userId": "user_123",
  "source": "google_drive",
  "fileId": "1ABC123",
  "version": "v1.2",
  "chunk": "React hooks let you..."
}
````

All queries to the vector database apply a mandatory `userId` filter.

---

## Step-by-Step Build Guide

Follow the phases in order.

---

### Phase 1 – Project Setup (Day 1 – 2 hours)

```
1. Create Next.js 15 app
   npx create-next-app@latest knowledge-base --typescript

2. Install Clerk
   npm i @clerk/nextjs

3. Configure Clerk dashboard and copy keys into .env.local

4. Add Clerk middleware for protected routes

5. Create a basic dashboard layout and add Clerk UserButton
```

---

### Phase 2 – Frontend UI (Day 2 – 4 hours)

```
6. Create /dashboard page with:
   - file upload dropzone (react-dropzone)
   - Google Drive connect button
   - Notion connect button
   - ingestion status table
   - chat interface

7. Add loading states:
   "Indexing..." → "Ready"

8. Style using TailwindCSS and shadcn/ui
```

---

### Phase 3 – Core APIs (Day 3 – 3 hours)

```
9. Create /api/ingest/route.ts
   - accept file or link
   - read userId from Clerk session
   - POST to n8n webhook
   - return jobId

10. Create /api/chat/route.ts
    - embed query
    - Pinecone similarity search
    - LLM completion

11. Create /api/ingestion-status/route.ts
    - read document status from MongoDB
```

---

### Phase 4 – n8n Setup (Day 4 – 6 hours)

This is the core of the system.

```
12. Create a VPS on Kamatera

13. Install n8n
    docker run -it n8n

14. Create MAIN workflow (webhook trigger):
    - webhook receives fileUrl and userId
    - download file
    - extract text (PDF / OCR)
    - chunk text (500 characters)
    - create embeddings using OpenAI or xAI Grok
    - upsert into Pinecone with userId metadata
    - update MongoDB status to "ready"

15. Create BACKGROUND workflows:
    - Google Drive trigger → same pipeline
    - Notion trigger → same pipeline
```

---

### Phase 5 – Pinecone and MongoDB (Day 5 – 2 hours)

```
16. Create Pinecone index and store API key

17. Create MongoDB Atlas cluster
    create "documents" collection

18. Implement lib/pinecone.ts
    - enforce userId filter for every query

19. Implement document versioning
    - hash file contents
    - skip ingestion when unchanged
```

---

### Phase 6 – Production Features (Day 6 – 3 hours)

```
20. Add ArcJet rate limiting to /api/chat

21. Store subscription metadata in Clerk

22. Document lifecycle:
    pending → processing → ready → failed

23. UI updates status in real time
```

---

### Phase 7 – CI/CD and Monitoring (Day 7 – 2 hours)

```
24. GitHub Actions workflow

name: Deploy
on: [push]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm run lint
      - run: npm run build
```

```
25. Connect repository to Vercel for auto-deployment

26. Add CubeAPM to the Vercel project

27. Install CubeAPM agent on the n8n server
```

---

### Phase 8 – Polish and Demo (Day 8 – 2 hours)

```
28. Finalize document versioning
29. Implement retry for failed ingestions
30. Add export knowledge base feature
31. Record demo video
32. Deploy production build and share live link
```

---

## Repository Structure

```
knowledge-base/
├── app/
│   ├── dashboard/
│   ├── chat/
│   └── api/
│       ├── ingest/
│       ├── chat/
│       └── status/
├── lib/
│   ├── pinecone.ts
│   ├── embeddings.ts
│   ├── arcjet.ts
│   └── auth.ts
├── services/
│   └── n8n.ts
├── .github/workflows/deploy.yml
└── README.md
```

---

## Interview Talking Points

1. Real RAG pipeline with background workers
2. Webhook-driven ingestion architecture with strict user isolation
3. Full document lifecycle: versioning and status tracking
4. Production monitoring using CubeAPM
5. Zero-downtime CI/CD pipeline with automated builds

```
