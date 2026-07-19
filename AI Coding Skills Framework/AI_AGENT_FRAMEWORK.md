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
├── AI_AGENT_FRAMEWORK.md                   ← TRANG CHỦ
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

Với **11 modules** b覆盖 toàn diện từ Retrieval, Context, Memory, Planning, Prompting, Tooling, Workflow, Task Management, Multi-Agent, Automation đến Evaluation — bạn có thể xây dựng **hàng chục dự án thực tế**. Dưới đây là **20 dự án** được phân theo cấp độ và lĩnh vực.

---

### 🟢 Cấp Độ Cơ Bản — Bắt Đầu Ngay (Module 01-05)

#### 1. 📚 Personal Knowledge Base (PKB)

- **Mô tả**: Hệ thống lưu trữ & tìm kiếm kiến thức cá nhân từ notes, bookmarks, code snippets
- **Tech Stack**: ChromaDB + nomic-embed-text + Python CLI
- **⏱️ Thời gian build**: 2-3 ngày (CLI cơ bản), 1 tuần (có Web UI)
- **Module áp dụng**:
  - Module 01 — Vector Search + Hybrid Search (BM25 + Semantic)
  - Module 03 — Write-back Memory, KB Maintenance (CRUD)
  - Module 06 — Tool Selection (CLI tool, search tool)
- **Tính năng**: Import markdown/notes → Chunk → Embed → Semantic search CLI
- **Kết quả**: `pkb search "cách deploy Docker"` → tìm đúng trong 1000+ notes

#### 2. 📄 Document Q&A Chatbot

- **Mô tả**: Chatbot trả lời câu hỏi từ tài liệu của riêng bạn (PDF, docs, wiki)
- **Tech Stack**: Ollama (gemma3:12b + nomic-embed-text) + FAISS/ChromaDB + FastAPI
- **⏱️ Thời gian build**: 3-5 ngày (endpoint cơ bản), 1-2 tuần (chat UI + multi-format)
- **Module áp dụng**:
  - Module 01 — Document Processing, Chunking, Vector Search
  - Module 02 — Context Window Management, Prompt Templates
  - Module 05 — Prompt Builder, Output Format Control
- **Tính năng**: Upload PDF → Auto-chunk → Embed → Chat với tài liệu
- **Kết quả**: Chatbot trả lời đúng từ tài liệu nội bộ, giảm hallucination

#### 3. 🔍 Hybrid Search Engine

- **Mô tả**: Search engine kết hợp semantic + keyword search với re-ranking
- **Tech Stack**: BM25 (Python) + FAISS + Cross-encoder reranker
- **⏱️ Thời gian build**: 3-5 ngày (BM25 + Vector), 1 tuần (thêm reranking + optimization)
- **Module áp dụng**:
  - Module 01 — Vector Search, BM25, Hybrid Search, Re-ranking
  - Module 02 — Context Compression (tóm tắt kết quả)
- **Tính năng**: Query → Parallel BM25 + Vector → Merge → Rerank → Top-K
- **Kết quả**: Search chính xác hơn 40% so với chỉ BM25 hoặc chỉ Vector

#### 4. 📊 Markdown to Structured Data

- **Mô tả**: Tự động chuyển đổi tài liệu Markdown thành database có cấu trúc
- **Tech Stack**: LLM (gemma3:12b) + SQLite + Python
- **⏱️ Thời gian build**: 1-2 ngày
- **Module áp dụng**:
  - Module 05 — Prompt Builder, Output Format Control (JSON/Table)
  - Module 03 — Report Generation, Write-back Memory
- **Tính năng**: Input Markdown docs → LLM extract entities/relations → Store as structured data
- **Kết quả**: Tự động tạo database từ docs, hỗ trợ query có cấu trúc

#### 5. 🧪 AI Code Review Bot (Đơn Giản)

- **Mô tả**: Bot review code khi push lên GitHub, gợi ý improvements
- **Tech Stack**: GitHub Webhooks + LLM + Python
- **⏱️ Thời gian build**: 2-3 ngày
- **Module áp dụng**:
  - Module 05 — Prompt Builder (review prompts), Guardrails (safety)
  - Module 10 — Git Automation (hooks, webhooks)
- **Tính năng**: PR created → Bot reviews → Comment suggestions on PR
- **Kết quả**: Auto code review, bắt bugs sớm,uggest best practices

---

### 🟢 Cấp Độ Daily-Use — Hữu Ích Mỗi Ngày (Module 01-10)

> **Đây là các dự án nhỏ, có thể build trong 1-3 ngày, nhưng dùng MỖI NGÀY tại công việc.**

#### 6. 📋 Auto Daily Work Log

- **Mô tả**: Tự động ghi nhận công việc hàng ngày từ git commits, Slack messages, calendar — tạo daily report
- **Tech Stack**: LLM + Git API + Calendar API + Markdown/JSON output
- **Module áp dụng**:
  - Module 01 — Multi-Source Retrieval (git, calendar, Slack, Jira)
  - Module 03 — Write-back Memory (lưu daily log), Report Generation
  - Module 05 — Prompt Builder (report template), Output Format (Markdown table)
  - Module 10 — Scheduled Tasks (cron mỗi cuối ngày)
- **Flow**:
  ```
  18:00 PM → Cron trigger
    → Git: List commits today (git log --since="today")
    → Calendar: List meetings attended
    → Jira/Trello: Tasks updated
    → Slack: Key messages summary (optional)
    → LLM: Synthesize into daily report
    → Output: Markdown daily log + Send to Slack/Email
  ```
- **Kết quả**: Không bao giờ quên ghi công việc cuối ngày. Báo cáo tự động, chuyên nghiệp.
- **Sample output**:
  ```markdown
  ## Daily Report — 2025-01-15
  
  ### ✅ Completed
  - [PROJ-123] Fix login bug — 3 commits, merged PR #456
  - [PROJ-124] Implement user search API — 2 commits, PR #457 pending review
  
  ### 📅 Meetings
  - Sprint Planning (10:00-10:30)
  - Code Review with Team (14:00-14:30)
  
  ### 🔄 In Progress
  - [PROJ-125] Dashboard redesign — 60% complete
  
  ### 📌 Blockers
  - Waiting for API credentials from DevOps team
  ```

#### 7. 📝 Smart Standup Bot

- **Mô tả**: Tự động generate câu trả lời standup (What did you do? What will you do? Blockers?) từ dữ liệu real
- **Tech Stack**: LLM + Git + Jira API + Calendar
- **Module áp dụng**:
  - Module 01 — Retrieve昨天 activities from multiple sources
  - Module 05 — Prompt Builder (standup template), Chain-of-Thought (summarize)
  - Module 10 — Scheduled Tasks (cron mỗi sáng 9:00)
- **Flow**:
  ```
  09:00 AM → Bot gửi tin nhắn:
    "🌅 Good morning! Here's your standup for today:"
    
    What I did yesterday:
    - Fixed auth bug (commit abc123)
    - Reviewed PR #456
    
    What I'll do today:
    - Continue dashboard redesign (from Jira backlog)
    - Pair programming with @dev2 on API migration
    
    Blockers: None
  ```
- **Kết quả**: Standup trong 30 giây thay vì 15 phút suy nghĩ.

#### 8. 📧 AI Email/Message Drafter

- **Mô tả**: Tạo email drafts từ bullet points — professional, appropriate tone,多语言
- **Tech Stack**: LLM + Gmail API / Slack API
- **Module áp dụng**:
  - Module 05 — Prompt Builder (email templates, tone control), Few-shot Examples
  - Module 02 — Context Window (保持 email thread context)
- **Flow**:
  ```
  User: "draft email to boss: project delayed 2 weeks, need more resources, will update timeline"
    → LLM generates professional email with:
      - Appropriate greeting/closing
      - Structured paragraphs
      - Action items highlighted
      - Diplomatic tone
  ```
- **Kết quả**: Viết email chuyên nghiệp từ vài keywords, tiết kiệm 10-15 phút/email.

#### 9. 🔍 Codebase Q&A (Ask Your Code)

- **Mô tả**: Hỏi đáp với codebase — "function X làm gì?", "tại sao có bug này?", "ở đâu gọi function Y?"
- **Tech Stack**: LLM + AST Parser + Embedding + Code indexing
- **Module áp dụng**:
  - Module 01 — Vector Search (code chunks), Knowledge Graph (call graph)
  - Module 02 — Build Context (code + comments + git history)
  - Module 06 — Tool Selection (read_file, search_code, grep)
- **Flow**:
  ```
  User: "handlePayment() function làm gì và bị gọi ở đâu?"
    → Search: Find handlePayment definition
    → Search: Find all callers
    → Context: Build with code + tests + comments
    → LLM: Explain logic + list callers + potential issues
  ```
- **Kết quả**: Hiểu codebase nhanh hơn 5x, đặc biệt khi onboarding project mới.

#### 10. 📊 PR Summary & Changelog Generator

- **Mô tả**: Tóm tắt PR thành changelog entries, release notes tự động
- **Tech Stack**: LLM + Git diff + GitHub API
- **Module áp dụng**:
  - Module 01 — Retrieve PR context, linked issues
  - Module 05 — Prompt Builder (changelog template), Output Format
  - Module 10 — Git Automation (auto-generate on merge)
- **Flow**:
  ```
  PR merged → Webhook trigger
    → LLM analyzes diff + PR description + linked issues
    → Generates changelog entry:
    
    ### Added
    - User search API with fuzzy matching (#457)
    
    ### Fixed  
    - Login timeout on slow networks (#456)
    
    ### Changed
    - Upgraded auth library to v2.1
  ```
- **Kết quả**: Release notes tự động, không ai phải gõ tay changelog.

#### 11. 🗓️ Meeting Notes → Action Items

- **Mô tả**: Tự động extract action items từ meeting notes/recordings
- **Tech Stack**: LLM + Speech-to-Text (optional) + Task Manager API
- **Module áp dụng**:
  - Module 02 — Context Compression (tóm tắt meeting)
  - Module 05 — Prompt Builder (action item extraction), Output Format (JSON)
  - Module 08 — Task Classification, Priority
- **Flow**:
  ```
  Meeting notes / transcript input
    → LLM extracts:
      - Action items với assignees
      - Decisions made
      - Follow-up topics
      - Deadlines
    → Auto-create tasks in Jira/Trello/Notion
  ```
- **Kết quả**: Không bỏ lỡ action items, mỗi meeting đều có kết quả rõ ràng.

#### 12. 💡 Git Commit Message AI

- **Mô tả**: Tự động tạo commit message chuẩn từ git diff
- **Tech Stack**: LLM + Git hooks (commit-msg)
- **Module áp dụng**:
  - Module 05 — Prompt Builder (Conventional Commits template)
  - Module 10 — Git Automation (pre-commit hooks)
- **Flow**:
  ```
  git commit → pre-commit hook
    → Analyze staged changes (git diff --cached)
    → LLM generates:
      feat(auth): add OAuth2 login with Google
    
      - Add Google OAuth2 provider
      - Store refresh token in session
      - Handle token refresh on expiry
    
      Closes #123
  ```
- **Kết quả**: Commit messages chuẩn, consistent, có linked issues.

#### 13. 📖 Code Review Checklist Generator

- **Mô tả**: Tự động tạo checklist review tùy theo loại thay đổi (API change, DB migration, UI...)
- **Tech Stack**: LLM + Git diff analysis
- **Module áp dụng**:
  - Module 01 — Retrieve past review patterns, team conventions
  - Module 04 — Task Decomposition (break review into checklist items)
  - Module 05 — Prompt Builder (checklist templates)
- **Flow**:
  ```
  PR opened → Analyze changed files
    → Detect: "This PR modifies database schema"
    → Generate targeted checklist:
      ☐ Migration is reversible?
      ☐ Backward compatible?
      ☐ Indexes added for new queries?
      ☐ Seed data updated?
      ☐ Documentation updated?
  ```
- **Kết quả**: Code review toàn diện hơn, không bỏ sót edge cases.

---

### 📊 Tóm Tắt Daily-Use Projects

| # | Dự án | Tần suất dùng | Thời gian build | Tiết kiệm |
|---|-------|---------------|-----------------|------------|
| 6 | Auto Daily Work Log | **Mỗi ngày** | 2-3 ngày | 15-20 phút/ngày |
| 7 | Smart Standup Bot | **Mỗi ngày** | 1-2 ngày | 10-15 phút/ngày |
| 8 | AI Email Drafter | **3-5 lần/ngày** | 1 ngày | 10-15 phút/lần |
| 9 | Codebase Q&A | **5-10 lần/ngày** | 2-3 ngày | 5-10 phút/lần |
| 10 | PR Changelog Generator | **Mỗi PR merge** | 1 ngày | 10-15 phút/PR |
| 11 | Meeting Notes → Actions | **Sau mỗi meeting** | 1-2 ngày | 15-20 phút/meeting |
| 12 | Git Commit Message AI | **Mỗi commit** | 0.5 ngày | 2-3 phút/commit |
| 13 | Code Review Checklist | **Mỗi PR** | 1 ngày | 5-10 phút/PR |

> **💰 Tổng thời gian tiết kiệm ước tính: 1-2 GIỜ/ngày** — ~20-40 giờ/tháng!
> 
> Ít nhất 1-2 dự án trong số này sẽ **trả giá khóa học** trong tuần đầu tiên sử dụng.

---

### 🟡 Cấp Độ Trung Bình — Cần Nhiều Modules (Module 01-08)

#### 14. 🏗️ Enterprise RAG Platform

- **Mô tả**: Hệ thống RAG production-grade với multi-source retrieval, caching, monitoring
- **Tech Stack**: Ollama + Qdrant + FastAPI + Redis (cache) + Prometheus
- **⏱️ Thời gian build**: 2-3 tuần (MVP), 4-6 tuần (production-ready với monitoring)
- **Module áp dụng**:
  - Module 01 — Hybrid Search, Multi-Source Retrieval, Knowledge Graph
  - Module 02 — Hierarchical Context, Context Compression, Multi-turn
  - Module 03 — Memory Consolidation, Event Sourcing
  - Module 05 — Prompt Templates, Few-shot Examples, Guardrails
  - Module 07 — Pipeline Design, Error Recovery, Observability
- **Tính năng**: Multi-source ingestion → Hybrid search → Context assembly → Cached responses → Monitored pipeline
- **Kết quả**: Production RAG system xử lý 10K+ documents, <200ms latency

#### 15. 💬 Customer Support AI Agent

- **Mô tả**: Agent hỗ trợ khách hàng với khả năng tra cứu knowledge base, ticket system, escalation
- **Tech Stack**: LLM + Vector DB + Ticket API + WebSocket
- **⏱️ Thời gian build**: 2-3 tuần (basic chat + KB), 4-6 tuần (multi-turn + escalation + ticketing)
- **Module áp dụng**:
  - Module 01 — Retrieve from KB + order history + FAQ
  - Module 02 — Multi-turn Context, Context Window Management
  - Module 04 — Plan & Decompose (phân tích vấn đề → tìm giải pháp)
  - Module 05 — Prompt Builder (customer-facing prompts), Guardrails
  - Module 07 — Workflow (qualification → resolution → follow-up)
- **Tính năng**: Chat → Classify intent → Retrieve relevant docs → Resolve or Escalate
- **Kết quả**: Giải quyết 80% tickets tự động, escalation thông minh

#### 16. 📝 Smart Documentation Generator

- **Mô tả**: Tự động sinh documentation cho codebase từ source code + comments
- **Tech Stack**: LLM + AST Parser + Git + Markdown
- **⏱️ Thời gian build**: 1-2 tuần (single language), 3-4 tuần (multi-language + auto-commit)
- **Module áp dụng**:
  - Module 01 — Retrieve code context, existing docs
  - Module 02 — Build Context (code + comments + usage examples)
  - Module 05 — Prompt Builder (doc generation templates), Output Format
  - Module 10 — Git Automation (auto-commit docs), Code Generation
- **Tính năng**: Scan codebase → Extract API signatures → Generate docs → Auto-commit
- **Kết quả**: Documentation luôn updated, giảm 90% thời gian viết docs

#### 17. 🗓️ AI Task Manager

- **Mô tả**: Hệ thống quản lý task thông minh — phân loại, ưu tiên, decomposition, scheduling
- **Tech Stack**: LLM + SQLite + CLI/Web UI + Calendar API
- **⏱️ Thời gian build**: 1-2 tuần (CLI), 3-4 tuần (Web UI + calendar integration)
- **Module áp dụng**:
  - Module 04 — Task Decomposition, Planning Algorithms (Plan-and-Solve)
  - Module 05 — Prompt Builder (classification prompts)
  - Module 08 — Task Classification, Priority & Scheduling, Dependency Management
- **Tính năng**: Input task description → Auto-classify → Decompose → Prioritize → Schedule → Track
- **Kết quả**: Task phức tạp được tự động chia nhỏ và lên lịch thông minh

#### 18. 🔄 Memory-Augmented Chatbot

- **Mô tả**: Chatbot có bộ nhớ dài hạn — nhớ preferences, lịch sử trò chuyện, facts về user
- **Tech Stack**: LLM + Vector DB + SQLite (fact store) + Session management
- **⏱️ Thời gian build**: 2-3 tuần (basic memory), 4-6 tuần (consolidation + multi-session)
- **Module áp dụng**:
  - Module 02 — Multi-turn Context, Hierarchical Context
  - Module 03 — Write-back Memory, Memory Consolidation (merge/dedupe facts)
  - Module 05 — Prompt Builder (system prompt với remembered context)
- **Tính năng**: Chat → Extract facts → Store → Recall in future conversations
- **Kết quả**: Bot nhớ user preferences, personalize responses theo thời gian

---

### 🔴 Cấp Độ Nâng Cao — Hệ Thống Phức Táp (Module 04-11)

#### 19. 🤖 AI Coding Agent (Tự Xây Dựng)

- **Mô tả**: Tự xây dựng agent类似 Cline/Cursor — đọc code, phân task, viết code, review, test, commit
- **Tech Stack**: LLM + MCP Servers + Git + File System + Terminal
- **⏱️ Thời gian build**: 4-6 tuần (MVP đọc + viết code), 2-3 tháng (full pipeline review + test + commit)
- **Module áp dụng**:
  - Module 04 — ReAct Pattern (Thought → Action → Observation loop), Agent Workflows
  - Module 06 — Tool Selection, MCP Protocol, Tool Executor with Error Handling
  - Module 07 — State Machine (idle → planning → coding → reviewing → testing)
  - Module 08 — Task Decomposition, Dependency Management
- **Tính năng**: User request → Plan → Read files → Write code → Run tests → Fix errors → Commit
- **Kết quả**: Agent tự động hoàn thành coding tasks từ description

#### 20. 🏢 Multi-Agent Development Team

- **Mô tả**: Hệ thống nhiều agents phối hợp: Planner → Architect → Coder → Reviewer → Tester → Deployer
- **Tech Stack**: LLM + MCP + Message Queue (Redis/RabbitMQ) + Shared Memory
- **⏱️ Thời gian build**: 2-3 tháng (2 agents cơ bản), 4-6 tháng (full 6-agent pipeline)
- **Module áp dụng**:
  - Module 08 — Task Classification, Decomposition, Priority & Scheduling, Dependency DAG
  - Module 09 — Agent Roles, Communication Patterns, Orchestration, Shared Memory, Conflict Resolution
  - Module 07 — Workflow Patterns, Pipeline Design, Error Recovery
- **Tính năng**: Feature request → Planner decomposes → Architect designs → Coder implements → Reviewer reviews → Tester tests → Deployer deploys
- **Kết quả**: Full development pipeline tự động, mỗi agent là specialist

#### 21. 🔄 Self-Healing CI/CD Pipeline

- **Mô tả**: Pipeline CI/CD tự động — build, test, review, deploy, và tự sửa lỗi khi fail
- **Tech Stack**: GitHub Actions + LLM + Docker + Kubernetes + Prometheus + Grafana
- **⏱️ Thời gian build**: 2-3 tuần (basic auto-fix), 4-6 tuần (full self-healing + monitoring)
- **Module áp dụng**:
  - Module 07 — Error Recovery (retry, circuit breaker), Observability (logging, metrics)
  - Module 10 — CI/CD Pipelines, Git Automation, Monitoring & Alerts, Code Generation
  - Module 11 — Performance Benchmarks, Continuous Improvement
- **Tính năng**: Push → Build → Test → AI Review → Auto-fix failures → Deploy → Monitor → Rollback if needed
- **Kết quả**: Pipeline tự phục hồi 90% failures, giảm downtime

#### 22. 📈 AI-Powered Analytics Dashboard

- **Mô tả**: Dashboard tự động phân tích data, generate insights, cảnh báo anomalous
- **Tech Stack**: LLM + Pandas/SQL + Chart Library + Scheduler
- **⏱️ Thời gian build**: 2-3 tuần (single data source), 4-6 tuần (multi-source + anomaly detection)
- **Module áp dụng**:
  - Module 01 — Multi-Source Retrieval (data from multiple DBs/APIs)
  - Module 03 — Report Generation, Event Sourcing
  - Module 05 — Prompt Builder (analysis prompts), Output Format (charts, tables)
  - Module 10 — Scheduled Tasks, Monitoring & Alerts
  - Module 11 — Quality Metrics, Reporting & Dashboards
- **Tính năng**: Schedule phân tích → Query data → AI generates insights → Dashboard update → Alert on anomalies
- **Kết quả**: Automated business intelligence, proactive anomaly detection

#### 23. 🏥 Domain-Specific Research Assistant (Y tế/Legal/Finance)

- **Mô tả**: Trợ lý nghiên cứu chuyên ngành — trả lời câu hỏi từ papers, regulations, financial reports
- **Tech Stack**: LLM + Vector DB + Knowledge Graph + Citation tracking
- **⏱️ Thời gian build**: 3-4 tuần (basic Q&A), 6-8 tuần (CoT reasoning + citation tracking)
- **Module áp dụng**:
  - Module 01 — Knowledge Graph (entity relationships), Multi-Source Retrieval
  - Module 02 — Hierarchical Context (summary → detail), Context Compression
  - Module 04 — Planning Algorithms (research decomposition)
  - Module 05 — Guardrails (safety filters), Chain-of-Thought (reasoning)
  - Module 11 — Evaluation Dimensions (correctness, safety, faithfulness)
- **Tính năng**: Research question → Decompose sub-questions → Retrieve from papers/docs → Reason with CoT → Answer with citations
- **Kết quả**: Expert-level research assistance với verifiable sources

---

### 🟣 Cấp Độ Expert — Hệ Thống Tổng Hợp (Tất Cả Modules)

#### 24. 🌐 Autonomous AI Development Platform

- **Mô tả**: Nền tảng cho phép người dùng mô tả feature bằng ngôn ngữ tự nhiên, AI tự động phát triển
- **Tech Stack**: Full stack — LLM + MCP + Docker + CI/CD + Monitoring
- **⏱️ Thời gian build**: 3-6 tháng (MVP), 6-12 tháng (production-grade)
- **Module áp dụng**: **TẤT CẢ modules 01-11**
  - 01-03: Knowledge retrieval & memory management
  - 04-05: Planning & prompt engineering
  - 06-07: Tool selection & workflow orchestration
  - 08-09: Task management & multi-agent coordination
  - 10-11: Automation & continuous evaluation
- **Tính năng**: "Build a blog app with auth, posts, comments" → Auto-plan → Auto-code → Auto-test → Auto-deploy
- **Kết quả**: Nền tảng low-code/no-code với AI agent backbone

#### 25. 🧠 Adaptive Learning System

- **Mô tả**: Hệ thống học tập thích ứng — track knowledge gaps, gợi ý nội dung, spaced repetition
- **Tech Stack**: LLM + Vector DB + Scheduler + Analytics
- **⏱️ Thời gian build**: 2-3 tháng (basic tracking), 4-6 tháng (full adaptive + spaced repetition)
- **Module áp dụng**:
  - Module 01 — Retrieve learning materials, knowledge graph
  - Module 02 — Build personalized context for each learner
  - Module 03 — Write-back Memory (track what user learned/forgot)
  - Module 04 — Plan learning paths (adaptive difficulty)
  - Module 08 — Task Management (learning tasks, scheduling)
  - Module 11 — Evaluation (track progress, identify gaps)
- **Tính năng**: User learns → Track mastery → Identify gaps → Suggest next topics → Spaced repetition schedule
- **Kết quả**: Personalized learning path, optimized retention

#### 26. 🔐 AI Security Audit System

- **Mô tả**: Hệ thống audit security tự động — scan code, detect vulnerabilities, suggest fixes
- **Tech Stack**: LLM + SAST tools + Knowledge Graph + CI/CD
- **⏱️ Thời gian build**: 3-4 tuần (single scanner), 2-3 tháng (multi-agent + auto-fix + compliance)
- **Module áp dụng**:
  - Module 01 — Retrieve CVE database, security knowledge
  - Module 05 — Guardrails (safety, prompt injection prevention)
  - Module 07 — Workflow (scan → analyze → report → fix → verify)
  - Module 09 — Multi-agent (Scanner agent + Analyzer agent + Fixer agent)
  - Module 10 — CI/CD integration, Git Automation
  - Module 11 — Evaluation (security metrics, compliance)
- **Tính năng**: Code push → Security scan → AI analysis → Fix suggestions → Auto-PR fixes
- **Kết quả**: Automated security pipeline, compliance reporting

#### 27. 📱 Cross-Platform App Generator

- **Mô tả**: Tạo app đa nền tảng từ spec — Web + Mobile + API từ 1 specification
- **Tech Stack**: LLM + Code generation templates + MCP + Multi-repo management
- **⏱️ Thời gian build**: 2-3 tháng (single platform), 4-6 tháng (multi-platform parallel generation)
- **Module áp dụng**:
  - Module 04 — Plan & Decompose (feature → frontend + backend + API)
  - Module 06 — Tool Selection (choose framework, libraries)
  - Module 07 — Workflow (parallel generation for each platform)
  - Module 09 — Multi-agent (Frontend agent + Backend agent + API agent)
  - Module 10 — Code Generation Automation, Git Automation
- **Tính năng**: App spec → Architect designs API → Parallel code gen → Cross-platform build → Unified test
- **Kết quả**: Tạo MVP cho 3 platforms từ 1 spec trong vài giờ

#### 28. 🏗️ Infrastructure-as-Code AI Assistant

- **Mô tả**: AI assistant cho DevOps — tự động generate Terraform/Docker/K8s configs từ requirements
- **Tech Stack**: LLM + MCP (Docker, K8s, Cloud APIs) + Git + Terraform
- **⏱️ Thời gian build**: 2-3 tháng (single cloud), 4-6 tháng (multi-cloud + validation + apply)
- **Module áp dụng**:
  - Module 01 — Retrieve infrastructure docs, best practices
  - Module 04 — Plan infrastructure (networking → compute → storage → monitoring)
  - Module 05 — Prompt Builder (IaC templates), Guardrails (security policies)
  - Module 06 — Tool Selection (Docker, K8s, AWS/GCP/Azure MCP tools)
  - Module 07 — Workflow (plan → generate → validate → apply → verify)
  - Module 10 — CI/CD for infrastructure, Monitoring
- **Tính năng**: "Deploy a scalable web app" → Generate Terraform + Docker + K8s + CI/CD configs
- **Kết quả**: Infrastructure provisioning tự động,遵循 best practices

---

### 📊 Tổng Quan Dự Án & Module

| # | Dự án | Cấp độ | Modules áp dụng | Độ phức tạp |
|---|-------|--------|-----------------|-------------|
| 1 | Personal Knowledge Base | 🟢 Cơ bản | 01, 03, 06 | ⭐⭐ |
| 2 | Document Q&A Chatbot | 🟢 Cơ bản | 01, 02, 05 | ⭐⭐ |
| 3 | Hybrid Search Engine | 🟢 Cơ bản | 01, 02 | ⭐⭐⭐ |
| 4 | Markdown to Structured Data | 🟢 Cơ bản | 03, 05 | ⭐⭐ |
| 5 | AI Code Review Bot | 🟢 Cơ bản | 05, 10 | ⭐⭐ |
| 6 | Enterprise RAG Platform | 🟡 Trung bình | 01, 02, 03, 05, 07 | ⭐⭐⭐⭐ |
| 7 | Customer Support AI Agent | 🟡 Trung bình | 01, 02, 04, 05, 07 | ⭐⭐⭐⭐ |
| 8 | Smart Documentation Generator | 🟡 Trung bình | 01, 02, 05, 10 | ⭐⭐⭐ |
| 9 | AI Task Manager | 🟡 Trung bình | 04, 05, 08 | ⭐⭐⭐ |
| 10 | Memory-Augmented Chatbot | 🟡 Trung bình | 02, 03, 05 | ⭐⭐⭐ |
| 11 | AI Coding Agent | 🔴 Nâng cao | 04, 06, 07, 08 | ⭐⭐⭐⭐⭐ |
| 12 | Multi-Agent Dev Team | 🔴 Nâng cao | 07, 08, 09 | ⭐⭐⭐⭐⭐ |
| 13 | Self-Healing CI/CD | 🔴 Nâng cao | 07, 10, 11 | ⭐⭐⭐⭐ |
| 14 | AI Analytics Dashboard | 🔴 Nâng cao | 01, 03, 05, 10, 11 | ⭐⭐⭐⭐ |
| 15 | Domain Research Assistant | 🔴 Nâng cao | 01, 02, 04, 05, 11 | ⭐⭐⭐⭐ |
| 16 | Autonomous Dev Platform | 🟣 Expert | 01-11 (tất cả) | ⭐⭐⭐⭐⭐ |
| 17 | Adaptive Learning System | 🟣 Expert | 01, 02, 03, 04, 08, 11 | ⭐⭐⭐⭐⭐ |
| 18 | AI Security Audit System | 🟣 Expert | 01, 05, 07, 09, 10, 11 | ⭐⭐⭐⭐⭐ |
| 19 | Cross-Platform App Generator | 🟣 Expert | 04, 06, 07, 09, 10 | ⭐⭐⭐⭐⭐ |
| 20 | Infrastructure-as-Code AI | 🟣 Expert | 01, 04, 05, 06, 07, 10 | ⭐⭐⭐⭐⭐ |

---

### 🗺️ Ma Trận Module → Dự Án

> Mỗi module phục vụ bao nhiêu dự án — cho thấy giá trị của từng phần kiến thức.

| Module | Tên Module | Số dự án sử dụng | Dự án tiêu biểu |
|--------|-----------|------------------|-----------------|
| **01** | Retrieve Memory & Knowledge | **15/20** | Enterprise RAG, Research Assistant |
| **02** | Build Context | **12/20** | Customer Support Agent, Memory Chatbot |
| **03** | Update Memory Store | **10/20** | PKB, Memory Chatbot, Analytics Dashboard |
| **04** | Plan & Decompose | **10/20** | AI Coding Agent, Research Assistant |
| **05** | Prompt Builder | **15/20** | Gần như tất cả dự án |
| **06** | Decide Tools / MCP | **7/20** | AI Coding Agent, IaC Assistant |
| **07** | Workflow | **10/20** | Enterprise RAG, Multi-Agent, CI/CD |
| **08** | Task Management | **5/20** | AI Task Manager, Multi-Agent Dev Team |
| **09** | Multi-Agent | **5/20** | Multi-Agent Dev Team, Security Audit |
| **10** | Automation | **9/20** | CI/CD, Code Gen, Git Automation |
| **11** | Evaluation | **6/20** | Self-Healing CI/CD, Research Assistant |

---

### 📈 Lộ Trình Thực Hành Theo Cấp Độ

```
🟢 CẤP ĐỘ CƠ BẢN (1-2 tuần mỗi dự án)
═══════════════════════════════════════
Tuần 1-2:  #1 Personal Knowledge Base
           → Học: Vector Search, Hybrid Search, KB Maintenance
           
Tuần 3-4:  #2 Document Q&A Chatbot
           → Học: Chunking, Embedding, Context Building, Prompt Templates
           
Tuần 5-6:  #3 Hybrid Search Engine
           → Học: BM25 + Vector融合, Re-ranking, Context Compression

───────────────────────────────────────

🟡 CẤP ĐỘ TRUNG BÌNH (2-3 tuần mỗi dự án)
═══════════════════════════════════════
Tuần 7-9:  #6 Enterprise RAG Platform
           → Học: Pipeline Design, Error Recovery, Observability
           
Tuần 10-12: #7 Customer Support AI Agent
           → Học: Multi-turn Context, Planning, Workflow

───────────────────────────────────────

🔴 CẤP ĐỘ NÂNG CAO (3-4 tuần mỗi dự án)
═══════════════════════════════════════
Tuần 13-16: #11 AI Coding Agent
            → Học: ReAct Pattern, MCP Protocol, State Machine
           
Tuần 17-20: #12 Multi-Agent Development Team
            → Học: Agent Roles, Orchestration, Shared Memory

───────────────────────────────────────

🟣 CẤP ĐỘ EXPERT (4-6 tuần mỗi dự án)
═══════════════════════════════════════
Tuần 21-26: #16 Autonomous AI Development Platform
            → Học: Tổng hợp TẤT CẢ modules thành hệ thống hoàn chỉnh
```

---

### 💼 Cơ Hội Nghề Nghiệp & Mức Lương

| Vai trò | Kỹ năng cốt lõi từ Framework | Mức lương VN (2025) | Mức lương Global |
|---------|------------------------------|--------------------|--------------------|
| **AI Engineer** | Module 01-07: RAG, Agent, Workflow | 30-80M/tháng | $100K-200K/năm |
| **MLOps / AI Platform Engineer** | Module 07, 10, 11: Pipeline, CI/CD, Monitoring | 25-60M/tháng | $120K-180K/năm |
| **Backend Developer (AI-focused)** | Module 01-06: RAG integration, API design | 20-50M/tháng | $80K-150K/năm |
| **DevOps/SRE with AI** | Module 07, 10, 11: Self-healing, Automation | 25-60M/tháng | $100K-160K/năm |
| **Tech Lead (AI Products)** | Module 04, 08, 09: Planning, Multi-agent orchestration | 40-100M/tháng | $150K-250K/năm |
| **AI Freelancer / Consultant** | Toàn bộ framework: Build custom AI solutions | 50-200M/tháng (project-based) | $100-300/giờ |

---

### 💡 Tips Để Bắt Đầu

```
1. BẮT ĐẦU NHỎ
   ├── Đừng cố build "Autonomous Dev Platform" ngay
   ├── Bắt đầu với #1 (PKB) hoặc #2 (Document Q&A)
   └── Hiểu rõ fundamentals trước khi phức tạp hóa

2. BUILD IN PUBLIC
   ├── Viết blog về quá trình build mỗi dự án
   ├── Share trên GitHub để nhận feedback
   └── Tạo portfolio ấn tượng cho employer

3. ITERATE & COMBINE
   ├── Bắt đầu đơn giản → thêm features theo modules
   ├── Kết hợp 2-3 dự án nhỏ thành dự án lớn hơn
   └── VD: PKB (#1) + Doc Q&A (#2) = Enterprise RAG (#6)

4. FOCUS ON REAL PROBLEMS
   ├── Build giải quyết vấn đề THỰC SỰ của bạn
   ├── VD: Automate docs cho codebase công ty
   ├── VD: Chatbot trả lời câu hỏi từ SRS/PRD
   └── Real problems = Real portfolio = Real job opportunities
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
