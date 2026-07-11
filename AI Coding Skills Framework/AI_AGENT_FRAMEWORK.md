# 🧠 Memory & Context trong AI — Hướng Dẫn Học Tập

> **Framework này hướng dẫn cách XÂY DỰNG AI AGENT** — từ thiết kế bộ nhớ, xử lý context, lập kế hoạch, phối hợp đa agent, đến tự động hóa và đánh giá. Đây KHÔNG phải hướng dẫn cách sử dụng AI thông thường.

---

## 🎯 Đối Tượng

| Nhóm | Mục đích |
|------|----------|
| **AI Engineers** | Thiết kế và triển khai AI Agent systems |
| **Backend Developers** | Tích hợp LLM + RAG vào ứng dụng |
| **DevOps / SRE** | Triển khai và monitor AI pipelines |
| **Tech Leads** | Đánh giá và chọn kiến trúc AI phù hợp |
| **Researchers** | Tham khảo pattern và best practices |

## 📋 Tiên Quyết

| Kiến thức | Mức độ | Ghi chú |
|-----------|--------|---------|
| Python | Cơ bản → Trung bình | Ngôn ngữ chính của framework |
| LLM Concepts | Cơ bản | Transformer, Token, Prompt |
| REST API | Cơ bản | Giao tiếp với Ollama, MCP servers |
| Git | Cơ bản | Quản lý code và automation |

## 🛠️ Cách Sử Dụng

### Bước 1: Cài đặt môi trường
```bash
# Cài Ollama (chạy LLM & Embedding local)
curl -fsSL https://ollama.com/install.sh | sh

# Tải model
ollama pull gemma3:12b        # LLM chính
ollama pull nomic-embed-text  # Embedding model
```

### Bước 2: Học theo lộ trình
Bắt đầu từ **Phase 1** (Core Skills) và tiến dần đến **Phase 6** (Evaluation). Mỗi module có README.md chi tiết với code examples.

### Bước 3: Thực hành
- Clone repo và chạy thử code examples trong từng module
- Thay đổi parameters để quan sát kết quả
- Kết hợp các modules để build pipeline hoàn chỉnh

## ⚡ Quick Reference

| Bạn muốn... | Học module |
|-------------|------------|
| Tìm kiếm thông tin từ docs/database | [01 - Retrieve Memory](#part-i-retrieve-memory--knowledge) |
| Quản lý context window của LLM | [02 - Build Context](#part-ii-build-context) |
| Lưu trữ knowledge mới | [03 - Update Memory Store](#part-iii-update-memory--knowledge-store) |
| Phân task và lên kế hoạch | [04 - Plan & Decompose](#part-iv-plan--decompose-task) |
| Viết prompt hiệu quả | [05 - Prompt Builder](#part-v-prompt-builder) |
| Chọn và gọi tools (MCP) | [06 - Decide Tools / MCP](#part-vi-decide-tools--mcp-calls) |
| Tổ chức workflow tự động | [07 - Workflow](#part-vii-workflow) |
| Quản lý task tracking | [08 - Task Management](#part-viii-task-management) |
| Phối hợp nhiều agents | [09 - Multi-Agent](#part-ix-multi-agent-systems) |
| Tự động hóa CI/CD, Git | [10 - Automation](#part-x-automation) |
| Đánh giá hiệu quả | [11 - Evaluation](#part-xi-evaluation) |

---

## Cấu Trúc Học Tập

```
AI/
├── LEARNING_MEMORY_AND_CONTEXT.md          ← TRANG CHỦ
│
│  ── CORE SKILLS (Kỹ Năng Cốt Lõi) ──
├── 01-retrieve-memory-knowledge/           ← ĐỌC
├── 02-build-context/                       ← XỬ LÝ
├── 03-update-memory-store/                 ← GHI
├── 04-plan-decompose-task/                 ← LẬP KẾ HOẠCH
├── 05-prompt-builder/                      ← XÂY DỰNG PROMPT
├── 06-decide-tools-mcp/                    ← QUYẾT ĐỊNH DỤNG CỤ
│
│  ── ADVANCED SKILLS (Kỹ Năng Nâng Cao) ──
├── 07-workflow/                            ← QUY TRÌNH
├── 08-task/                                ← QUẢN LÝ TASK
├── 09-multi-agent/                         ← HỆ THỐNG ĐA AGENT
├── 10-automation/                          ← TỰ ĐỘNG HÓA
└── 11-evaluation/                          ← ĐÁNH GIÁ
```

## Lộ Trình Học

```
┌──────────────────────────────────────────────────────────────────────┐
│                    AI AGENT LEARNING PATH                             │
│                                                                      │
│  ── CORE SKILLS ────────────────────────────────────────            │
│                                                                      │
│  Phase 1: THU THẬP THÔNG TIN                                        │
│  ┌─────────────────────┐    ┌─────────────────────┐                 │
│  │ 01 Retrieve Memory   │───►│ 02 Build Context     │                │
│  │ & Knowledge          │    │                      │                │
│  └─────────────────────┘    └──────────┬───────────┘                │
│                                         │                            │
│  Phase 2: XỬ LÝ & TRẢ LỜI               │                            │
│                                         ▼                            │
│  ┌─────────────────────┐    ┌─────────────────────┐                 │
│  │ 05 Prompt Builder    │◄───│ 06 Decide Tools /    │                │
│  └─────────────────────┘    └──────────────────────┘                │
│                                                                      │
│  Phase 3: LƯU TRỮ & QUẢN LÝ                                        │
│  ┌─────────────────────┐    ┌─────────────────────┐                 │
│  │ 03 Update Memory     │───►│ 04 Plan & Decompose  │                │
│  └─────────────────────┘    └─────────────────────┘                │
│                                                                      │
│  ── ADVANCED SKILLS ───────────────────────────────────            │
│                                                                      │
│  Phase 4: TỔ CHỨC & VẬN HÀNH                                      │
│  ┌─────────────────────┐    ┌─────────────────────┐                 │
│  │ 07 Workflow          │───►│ 08 Task Management   │                │
│  │ ─ Pipeline Design    │    │ ─ Classification     │                │
│  │ ─ State Machine      │    │ ─ Decomposition      │                │
│  │ ─ Error Recovery     │    │ ─ Priority/Schedule  │                │
│  │ ─ Observability      │    │ ─ Dependency Graph   │                │
│  └─────────────────────┘    └─────────────────────┘                │
│                                                                      │
│  Phase 5: PHỐI HỢP & TỰ ĐỘNG                                      │
│  ┌─────────────────────┐    ┌─────────────────────┐                 │
│  │ 09 Multi-Agent       │───►│ 10 Automation        │                │
│  │ ─ Agent Roles        │    │ ─ CI/CD Pipelines    │                │
│  │ ─ Communication      │    │ ─ Code Generation    │                │
│  │ ─ Orchestration      │    │ ─ Git Automation     │                │
│  │ ─ Shared Memory      │    │ ─ Monitoring/Alerts  │                │
│  └─────────────────────┘    └─────────────────────┘                │
│                                                                      │
│  Phase 6: ĐÁNH GIÁ & CẢI TIẾN                                     │
│  ┌──────────────────────────────────────────────────────┐          │
│  │ 11 Evaluation                                            │          │
│  │ ─ Quality Metrics  ─ Benchmarks  ─ Continuous Improve  │          │
│  └──────────────────────────────────────────────────────┘          │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
```

## Môi Trường Thực Hành

| Component | Model / Tool | Chạy |
|-----------|-------------|------|
| LLM | `gemma3:12b` | Local via Ollama |
| Embedding | `nomic-embed-text` (768D) | Local via Ollama |
| Vector Store | FAISS / ChromaDB | Local |
| BM25 | Custom Python | Local |
| MCP Server | GitHub MCP, Custom tools | Local |

## Chi Tiết Từng Phần

### Part I: Retrieve Memory & Knowledge
> Làm sao tìm đúng thông tin?

| # | Topic | Mô tả |
|---|-------|-------|
| 1.1 | [Vector Search](01-retrieve-memory-knowledge/README.md#1-vector-search) | Semantic similarity với embeddings |
| 1.2 | [BM25 Search](01-retrieve-memory-knowledge/README.md#2-bm25-search) | Keyword matching, term frequency |
| 1.3 | [Hybrid Search](01-retrieve-memory-knowledge/README.md#3-hybrid-search) | Kết hợp Vector + BM25 |
| 1.4 | [Knowledge Graph](01-retrieve-memory-knowledge/README.md#4-knowledge-graph) | Entity relationships, graph traversal |
| 1.5 | [Multi-Source Retrieval](01-retrieve-memory-knowledge/README.md#5-multi-source-retrieval) | Fusing results từ nhiều nguồn |

### Part II: Build Context
> Làm sao tổ chức thông tin hiệu quả?

| # | Topic | Mô tả |
|---|-------|-------|
| 2.1 | [Context Window Management](02-build-context/README.md#1-context-window-management) | Token limits, sliding window |
| 2.2 | [Context Compression](02-build-context/README.md#2-context-compression) | Tóm tắt, extract key info |
| 2.3 | [Prompt Templates](02-build-context/README.md#3-prompt-templates) | System/user/assistant roles |
| 2.4 | [Hierarchical Context](02-build-context/README.md#4-hierarchical-context) | Summary → Detail structure |
| 2.5 | [Multi-turn Context](02-build-context/README.md#5-multi-turn-context) | Conversation memory management |

### Part III: Update Memory & Knowledge Store
> Làm sao lưu lại thông tin mới?

| # | Topic | Mô tả |
|---|-------|-------|
| 3.1 | [Write-back Memory](03-update-memory-store/README.md#1-write-back-memory) | Ghi sự kiện mới vào memory |
| 3.2 | [Memory Consolidation](03-update-memory-store/README.md#2-memory-consolidation) | Merge, dedupe facts |
| 3.3 | [Report Generation](03-update-memory-store/README.md#3-report-generation) | Tạo output có cấu trúc |
| 3.4 | [KB Maintenance](03-update-memory-store/README.md#4-kb-maintenance) | CRUD knowledge base |
| 3.5 | [Event Sourcing](03-update-memory-store/README.md#5-event-sourcing-pattern) | Full audit trail |

### Part IV: Plan & Decompose Task
> Làm sao chia nhỏ task phức tạp?

| # | Topic | Mô tả |
|---|-------|-------|
| 4.1 | [Task Decomposition](04-plan-decompose-task/README.md#1-task-decomposition-patterns) | Sequential, parallel, hierarchical |
| 4.2 | [Planning Algorithms](04-plan-decompose-task/README.md#2-planning-algorithms) | Plan-and-Solve, ToT |
| 4.3 | [Agent Workflows](04-plan-decompose-task/README.md#3-agent-workflows) | ReAct, Multi-agent, State Machine |
| 4.4 | [State Management](04-plan-decompose-task/README.md#4-state-management) | Checkpoint, rollback |
| 4.5 | [ReAct Pattern](04-plan-decompose-task/README.md#5-react-pattern) | Thought → Action → Observation |

### Part V: Prompt Builder
> Làm sao viết prompt tốt nhất?

| # | Topic | Mô tả |
|---|-------|-------|
| 5.1 | [Prompt Templates](05-prompt-builder/README.md#1-prompt-templates) | Reusable templates với variables |
| 5.2 | [Few-shot Examples](05-prompt-builder/README.md#2-few-shot-examples) | Dynamic example selection |
| 5.3 | [Chain-of-Thought](05-prompt-builder/README.md#3-chain-of-thought-cot) | CoT variants |
| 5.4 | [Guardrails](05-prompt-builder/README.md#4-guardrails) | Safety filters, validation |
| 5.5 | [Output Format](05-prompt-builder/README.md#5-output-format-control) | JSON, Markdown, Table |

### Part VI: Decide Tools / MCP Calls
> Khi nào dùng tool nào?

| # | Topic | Mô tả |
|---|-------|-------|
| 6.1 | [Tool Selection](06-decide-tools-mcp/README.md#1-tool-selection-patterns) | Registry, search, categories |
| 6.2 | [Intent Classification](06-decide-tools-mcp/README.md#2-intent-classification) | Rule-based & LLM-based |
| 6.3 | [MCP Protocol](06-decide-tools-mcp/README.md#3-mcp-model-context-protocol) | Model Context Protocol |
| 6.4 | [Tool Executor](06-decide-tools-mcp/README.md#4-tool-executor-with-error-handling) | Error handling & retry |

---

### Part VII: Workflow
> Làm sao tổ chức quy trình thực hiện?

| # | Topic | Mô tả |
|---|-------|-------|
| 7.1 | [Workflow Patterns](07-workflow/README.md#1-workflow-patterns) | Sequential, parallel, conditional |
| 7.2 | [Pipeline Design](07-workflow/README.md#2-pipeline-design) | Transform, filter, sink |
| 7.3 | [State Machine](07-workflow/README.md#3-state-machine) | FSM cho agent |
| 7.4 | [Error Recovery](07-workflow/README.md#4-error-recovery) | Retry, circuit breaker |
| 7.5 | [Observability](07-workflow/README.md#5-observability) | Logging, metrics |

### Part VIII: Task Management
> Làm sao quản lý và theo dõi tasks?

| # | Topic | Mô tả |
|---|-------|-------|
| 8.1 | [Task Classification](08-task/README.md#1-task-classification) | Phân loại theo category & complexity |
| 8.2 | [Task Decomposition](08-task/README.md#2-task-decomposition) | Feature, layer, file, TDD |
| 8.3 | [Priority & Scheduling](08-task/README.md#3-priority--scheduling) | Eisenhower matrix, token budget |
| 8.4 | [Task State Management](08-task/README.md#4-task-state-management) | Lifecycle, valid transitions |
| 8.5 | [Dependency Management](08-task/README.md#5-dependency-management) | DAG, topological sort |

### Part IX: Multi-Agent Systems
> Làm sao phối hợp nhiều agents?

| # | Topic | Mô tả |
|---|-------|-------|
| 9.1 | [Agent Roles](09-multi-agent/README.md#1-agent-roles) | Planner, Coder, Reviewer, Tester |
| 9.2 | [Communication Patterns](09-multi-agent/README.md#2-communication-patterns) | Hierarchical, P2P, pipeline |
| 9.3 | [Orchestration](09-multi-agent/README.md#3-orchestration-strategies) | Sequential, parallel, debate, voting |
| 9.4 | [Shared Memory](09-multi-agent/README.md#4-shared-memory) | Versioned, tagged, locked |
| 9.5 | [Conflict Resolution](09-multi-agent/README.md#5-conflict-resolution) | File lock, voting, deadlock detection |

### Part X: Automation
> Làm sao tự động hóa các task lặp đi lặp lại?

| # | Topic | Mô tả |
|---|-------|-------|
| 10.1 | [Automation Patterns](10-automation/README.md#1-automation-patterns) | Event, scheduled, reactive, self-healing |
| 10.2 | [CI/CD Pipelines](10-automation/README.md#2-cicd-pipelines) | GitHub Actions, deploy pipeline |
| 10.3 | [Code Generation](10-automation/README.md#3-code-generation-automation) | Scaffolding, templates |
| 10.4 | [Scheduled Tasks](10-automation/README.md#4-scheduled-tasks) | Cron, interval, daily |
| 10.5 | [Git Automation](10-automation/README.md#5-git-automation) | Hooks, commit conventions |
| 10.6 | [Monitoring & Alerts](10-automation/README.md#6-monitoring--alerts) | Thresholds, dashboards |

### Part XI: Evaluation
> Làm sao đánh giá hiệu quả AI coding?

| # | Topic | Mô tả |
|---|-------|-------|
| 11.1 | [Evaluation Dimensions](11-evaluation/README.md#1-evaluation-dimensions) | Correctness, quality, safety, speed |
| 11.2 | [Quality Metrics](11-evaluation/README.md#2-quality-metrics) | Complexity, maintainability |
| 11.3 | [Performance Benchmarks](11-evaluation/README.md#3-performance-benchmarks) | Benchmark suite, compare |
| 11.4 | [Evaluation Framework](11-evaluation/README.md#4-evaluation-framework) | Auto-eval pipeline |
| 11.5 | [Continuous Improvement](11-evaluation/README.md#5-continuous-improvement) | Trend analysis, suggestions |
| 11.6 | [Reporting & Dashboards](11-evaluation/README.md#6-reporting--dashboards) | Markdown/JSON reports |

---

## 🧠 Hiểu Framework Này

> **Framework này là "bộ não orchestration" — còn AI model là "bộ não suy luận".**

Framework dạy bạn **cách ghép các mảnh lại** — model chỉ là 1 mảnh trong puzzle. Bạn có thể dùng **Ollama chạy local miễn phí** hoặc **API trả phí** tùy budget.

### Flow minh họa: RAG Chatbot cần gì?

```
Tài liệu của bạn (PDF, docs, DB)
        ↓
   [Module 01] Retrieve — tìm đoạn liên quan
        ↓
   [Module 02] Build Context — sắp xếp thông tin
        ↓
   [Module 05] Prompt Builder — tạo prompt
        ↓
        ↓
   ══════════════════════════════
   ║   AI MODEL (Ollama/GPT)   ║  ← PHẦN CẦN MODEL
   ║   Đọc context + trả lời   ║
   ══════════════════════════════
        ↓
      Câu trả lời
```

### Phân biệt rõ: Phần nào cần model?

| Phần | Cần model? | Mô tả |
|------|-----------|-------|
| **Vector Search** (Module 01) | ✅ Cần **Embedding model** | Biến text thành vector để so sánh |
| **BM25 Search** (Module 01) | ❌ Không cần | Thuần toán học, đếm từ khóa |
| **Generate Answer** (Module 05) | ✅ Cần **LLM** | gemma3:12b, GPT-4o, Claude... |
| **Prompt Building** (Module 05) | ❌ Không cần | Chỉ là sắp xếp text |
| **Workflow** (Module 07) | ❌ Không cần | Chỉ là orchestration logic |
| **CI/CD Automation** (Module 10) | ❌ Không cần | GitHub Actions, scripts |

### 2 loại model cần thiết

| Loại | Vai trò | Local (miễn phí) | API (trả phí) |
|------|---------|-------------------|---------------|
| **Embedding model** | Biến text thành vector (dùng cho search) | `nomic-embed-text` qua Ollama | OpenAI text-embedding-3-small |
| **LLM** (Large Language Model) | Tạo câu trả lời | `gemma3:12b`, `llama3`, `qwen2.5` qua Ollama | GPT-4o, Claude, Gemini |

### Tóm lại

- **~60% framework** = code thuần, không cần model (search, workflow, orchestration)
- **~40% framework** = cần model (embedding + generate)

Framework dạy bạn **cách ghép các mảnh lại** — model chỉ là 1 mảnh trong puzzle. Bạn có thể dùng **Ollama chạy local miễn phí** hoặc **API trả phí** tùy budget.

---

## 🚀 Sau Khi Học Xong — Dự Án Thực Tế

Sau khi học xong **AI Coding Skills Framework**, bạn có thể xây dựng:

### 1. RAG System (Retrieval-Augmented Generation)

- Xây dựng chatbot trả lời câu hỏi từ tài liệu của riêng bạn (docs, PDF, database)
- Kết hợp Module 01 (Retrieve), 02 (Context), 03 (Memory Store), 05 (Prompt Builder)
- Ứng dụng: Chatbot hỗ trợ khách hàng, trợ lý nội bộ, search tài liệu thông minh

### 2. AI Coding Agent

- Tự xây dựng agent类似 Cline/Cursor — tự động đọc code, phân task, viết code, review, test
- Kết hợp Module 04 (Plan), 06 (Tools/MCP), 07 (Workflow), 09 (Multi-Agent)
- Ứng dụng: Auto-generate code, auto-fix bugs, auto-refactor

### 3. CI/CD Pipeline tự động với AI

- Tự động review code, generate test, deploy khi push lên GitHub
- Kết hợp Module 10 (Automation) + Module 11 (Evaluation)
- Ứng dụng: GitHub Actions với AI review, auto-deploy thông minh

### 4. Multi-Agent System

- Xây dựng hệ thống nhiều agent phối hợp: Planner → Coder → Reviewer → Tester → Deployer
- Kết hợp Module 09 (Multi-Agent) + Module 08 (Task Management)
- Ứng dụng: Tự động hóa toàn bộ quy trình phát triển phần mềm

### 5. Knowledge Base cá nhân

- Lưu trữ và truy xuất kiến thức từ nhiều nguồn (docs, notes, codebase)
- Kết hợp Module 01 (Retrieve) + Module 03 (Memory Store) + Module 06 (Tools)

### Tổng quan dự án & module

| Dự án | Module áp dụng | Ứng dụng |
|-------|---------------|----------|
| **RAG System** | 01, 02, 03, 05 | Chatbot trả lời câu hỏi từ tài liệu riêng |
| **AI Coding Agent** | 04, 06, 07, 09 | Tự động đọc code, phân task, viết code, review |
| **CI/CD với AI** | 10, 11 | Auto-review code, generate test, deploy |
| **Multi-Agent System** | 08, 09 | Planner → Coder → Reviewer → Tester → Deployer |
| **Knowledge Base cá nhân** | 01, 03, 06 | Lưu trữ & truy xuất kiến thức từ nhiều nguồn |

### Cơ hội nghề nghiệp

| Vai trò | Kỹ năng áp dụng |
|---------|-----------------|
| **AI Engineer** | Thiết kế & triển khai AI Agent systems |
| **Backend Developer** | Tích hợp LLM + RAG vào ứng dụng |
| **DevOps/SRE** | AI-powered CI/CD & monitoring |
| **Tech Lead** | Đánh giá & chọn kiến trúc AI phù hợp |
| **Freelancer** | Build AI solutions cho khách hàng |

### Lộ trình thực hành gợi ý

```
Phase 1-3 (Core):      Build 1 RAG chatbot đơn giản
Phase 4-6 (Core):      Thêm planning & tool selection
Phase 7-8 (Advanced):  Tổ chức thành workflow tự động
Phase 9-10 (Advanced): Mở rộng thành multi-agent system
Phase 11:              Đánh giá & tối ưu hóa hệ thống
```

---

## 💡 Ví Dụ Thực Tế

### Ví dụ 1: Xây dựng RAG Pipeline đơn giản

```python
# Bước 1: Chunk documents
from pathlib import Path

def chunk_text(text, chunk_size=500, overlap=50):
    chunks = []
    start = 0
    while start < len(text):
        chunks.append(text[start:start + chunk_size])
        start = start + chunk_size - overlap
    return chunks

# Bước 2: Embed chunks với Ollama
import requests

def embed_text(text):
    response = requests.post("http://localhost:11434/api/embed", json={
        "model": "nomic-embed-text",
        "input": text
    })
    return response.json()["embeddings"][0]

# Bước 3: Tạo vector store đơn giản
import numpy as np

class SimpleVectorStore:
    def __init__(self):
        self.vectors = []
        self.documents = []
    
    def add(self, doc, vector):
        self.documents.append(doc)
        self.vectors.append(vector)
    
    def search(self, query_vec, top_k=3):
        scores = []
        q = np.array(query_vec)
        for i, v in enumerate(self.vectors):
            score = np.dot(q, np.array(v)) / (np.linalg.norm(q) * np.linalg.norm(np.array(v)))
            scores.append((i, score))
        scores.sort(key=lambda x: x[1], reverse=True)
        return [(self.documents[i], s) for i, s in scores[:top_k]]

# Bước 4: Generate câu trả lời
def generate_answer(query, context_docs):
    context = "\\n".join([f"- {doc}" for doc in context_docs])
    prompt = f"""Dựa vào thông tin sau, trả lời câu hỏi.

Thông tin:
{context}

Câu hỏi: {query}

Trả lời:"""
    response = requests.post("http://localhost:11434/api/generate", json={
        "model": "gemma3:12b",
        "prompt": prompt,
        "stream": False
    })
    return response.json()["response"]
```

### Ví dụ 2: Multi-Agent Workflow

```
┌──────────────────────────────────────────────────────────────────┐
│                  MULTI-AGENT CODING WORKFLOW                      │
│                                                                  │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐       │
│  │  Planner      │───►│  Coder        │───►│  Reviewer    │       │
│  │  (Phân tích)  │    │  (Viết code)  │    │  (Review)    │       │
│  └──────────────┘    └──────────────┘    └──────────────┘       │
│         │                   │                    │                │
│         │                   │                    ▼                │
│         │                   │            ┌──────────────┐        │
│         │                   │            │  Tester       │        │
│         │                   │            │  (Test code)  │        │
│         │                   │            └──────────────┘        │
│         │                   │                    │                │
│         │                   │                    ▼                │
│         │                   │            ┌──────────────┐        │
│         └───────────────────┴───────────►│  Deployer     │        │
│                                         │  (Triển khai) │        │
│                                         └──────────────┘        │
└──────────────────────────────────────────────────────────────────┘
```

---

## 📊 Tóm Tắt Kiến Thức

| Kỹ năng | Level 1 (Cơ bản) | Level 2 (Trung bình) | Level 3 (Nâng cao) |
|---------|-------------------|----------------------|---------------------|
| **Search** | Keyword search (BM25) | Vector search | Hybrid + Re-ranking |
| **Context** | Simple concatenation | Sliding window | Hierarchical + Compression |
| **Memory** | File-based | Vector store | Event sourcing + Consolidation |
| **Planning** | Sequential tasks | Parallel + Dependencies | Tree of Thought |
| **Tools** | Manual tool call | Rule-based selection | Intent classification + MCP |
| **Workflow** | Linear pipeline | State machine | Error recovery + Observability |
| **Multi-Agent** | Two agents | Orchestrated team | Shared memory + Conflict resolution |
| **Automation** | Simple scripts | CI/CD pipelines | Self-healing systems |
| **Evaluation** | Manual testing | Automated metrics | Continuous improvement loop |

---

## 🔗 Liên Kết Hữu Ích

| Nguồn | Mô tả |
|-------|-------|
| [Ollama](https://ollama.com) | Chạy LLM & Embedding local |
| [FAISS](https://github.com/facebookresearch/faiss) | Vector search library |
| [ChromaDB](https://www.trychroma.com) | Embedded vector database |
| [MCP Protocol](https://modelcontextprotocol.io) | Model Context Protocol |
| [LangChain](https://langchain.com) | Framework cho LLM applications |
| [LlamaIndex](https://www.llamaindex.ai) | Data framework cho LLM |

---

> **Ghi chú**: Mỗi module trong framework đều có README.md chi tiết với code examples. Bạn có thể đọc từng module riêng lẻ hoặc học theo lộ trình từ Phase 1 đến Phase 6.
