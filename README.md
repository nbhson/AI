# .ai-tools — Agentic Workspace Configuration

Bộ cấu hình workspace cho AI Agent (Cline, Copilot, Cursor...) khi làm việc với dự án.

---

## 📖 Mối liên hệ với AI Coding Skills Framework

> **Hai phần này HOÀN TOÀN RIÊNG BIỆT nhưng có mối liên hệ logic.**

```
AI Coding Skills Framework          .ai-tools/                  Status
(Kiến thức & Nguyên lý)            (Cấu hình & Triển khai)
─────────────────────────────────────────────────────────────────────────
Module 01 (Retrieve Memory)   ───►  knowledge/                  ⚠️ PARTIAL
Module 02 (Build Context)     ───►  knowledge/ + AGENTS.md      ⚠️ PARTIAL
Module 03 (Update Memory)     ───►  ❌ CHƯA CÓ                  ❌ MISSING
Module 04 (Plan & Decompose)  ───►  workflows/feature-delivery  ✅ DONE
Module 05 (Prompt Builder)    ───►  templates/ + rules/         ⚠️ PARTIAL
Module 06 (Tools/MCP)         ───►  skills/ (SKILL.md files)    ✅ DONE
Module 07 (Workflow)          ───►  workflows/ (bug-fix, etc.)  ✅ DONE
Module 08 (Task Management)   ───►  hooks/ (phase-1 → phase-5)  ⚠️ PARTIAL
Module 09 (Multi-Agent)       ───►  ❌ CHƯA CÓ                  ❌ MISSING
Module 10 (Automation)        ───►  hooks/ + rules/             ⚠️ PARTIAL
Module 11 (Evaluation)        ───►  reports/, report-templates/ ✅ DONE
```

| Thuộc tính | AI Coding Skills Framework | .ai-tools/ |
|------------|---------------------------|------------|
| **Mục đích** | Tài liệu học tập — giáo trình lý thuyết | Bộ cấu hình thực tế — files điều khiển AI agent |
| **Nội dung** | 11 module: Retrieve, Context, Prompt, Workflow, Multi-Agent, Evaluation... | Skills, Rules, Workflows, Knowledge, Hooks, Templates |
| **Đối tượng** | Developers muốn **hiểu nguyên lý** AI Agent | AI Agent (Cline/Copilot/Cursor) đọc trực tiếp khi coding |
| **Kết quả** | Hiểu "tại sao" và "cách làm" | AI agent hành xử đúng chuẩn dự án |
| **Cần thêm gì** | Chỉ cần đọc | Cần kiến thức về project-specific rules, team workflows |

### Phân tích chi tiết — Module nào đã có, module nào thiếu?

#### ✅ Đã có tương ứng (đủ)

| Module | Framework | .ai-tools/ | Ghi chú |
|--------|-----------|------------|---------|
| **04 - Plan & Decompose** | Task decomposition, planning algorithms | `workflows/feature-delivery.md`, `workflows/bug-fix.md` | Workflow đã có các bước: Discovery → Plan → Approval → Implement |
| **06 - Tools/MCP** | Tool selection, MCP protocol, intent classification | `skills/SKILL.md` files | Mỗi SKILL.md định nghĩa kỹ năng cụ thể cho AI agent |
| **07 - Workflow** | Pipeline design, state machine, error recovery | `workflows/*.md` + `hooks/` | 5 phases hooks + workflow per task type |
| **11 - Evaluation** | Quality metrics, benchmarks, reporting | `reports/`, `report-templates/`, `templates/engineering-report-template.md` | Reports và templates đã có |

#### ⚠️ Có một phần (partial)

| Module | Framework | .ai-tools/ hiện tại | Thiếu |
|--------|-----------|---------------------|-------|
| **01 - Retrieve Memory** | Vector search, BM25, hybrid search, knowledge graph | `knowledge/*.md` — static markdown files | ❌ Không có vector DB, embedding search, BM25 index. Chỉ có **static knowledge docs** |
| **02 - Build Context** | Context window management, compression, templates | `knowledge/` được load khi start + `AGENTS.md` có context flow | ❌ Không có context compression, sliding window, token budget management |
| **05 - Prompt Builder** | Prompt templates, few-shot, CoT, guardrails | `templates/*.md` (report templates) + `rules/*.md` | ❌ Không có dynamic prompt templates, few-shot examples, chain-of-thought prompting |
| **08 - Task Management** | Task classification, decomposition, dependency DAG | `hooks/` (5 phases) + workflow steps | ❌ Không có task state management, dependency graph, priority scheduling |
| **10 - Automation** | CI/CD, code generation, git automation, monitoring | `hooks/` + `rules/coding-style.md` | ❌ Không có CI/CD pipeline config, git hooks automation, monitoring setup |

#### ❌ Chưa có (missing)

| Module | Framework | Hiện trạng trong .ai-tools/ |
|--------|-----------|-----------------------------|
| **03 - Update Memory Store** | Write-back memory, memory consolidation, event sourcing | **Hoàn toàn chưa có.** Không có cơ chế lưu trữ lại kiến thức mới, merge/dedupe facts, hay event sourcing |
| **09 - Multi-Agent** | Agent roles, communication, orchestration, shared memory | **Hoàn toàn chưa có.** Không có multi-agent config, agent-to-agent communication, shared memory |

### Tại sao thiếu?

```
AI Coding Skills Framework .ai-tools/
(Kiến thức)                 (Triển khai thực tế)
                            
Retrieve Memory  ──?──►     knowledge/*.md = static docs
                            ❌ Không có: vector store, embedding, BM25 index
                            
Build Context    ──?──►     AGENTS.md = orchestration flow
                            ❌ Không có: context compression, token budget
                            
Update Memory    ──?──►     ❌ Không có gì
                            ❌ Không có: write-back, consolidation, event log
                            
Multi-Agent      ──?──►     ❌ Không có gì
                            ❌ Không có: agent definitions, communication protocol
```

**Lý do:** Hiện tại .ai-tools/ tập trung vào **workflow orchestration** (cách AI agent thực hiện task) và **coding rules** (quy tắc coding). Nó **chưa address** phần:
- **Memory management** (lưu/truy xuất/xử lý knowledge động)
- **Context optimization** (quản lý context window hiệu quả)
- **Multi-agent coordination** (phối hợp nhiều agents)

Đây là những phần cần **triển khai thêm** nếu muốn .ai-tools/ cover đầy đủ 11 modules của Framework.

---

## 📁 Cấu trúc thư mục

```
.ai-tools/
├── README.md                          ← TRANG CHỦ (file này)
│
├── .agents/                           ← Cấu hình cho Cline / general agents
│   ├── AGENTS.md                      ← Orchestration workflow chính
│   ├── GEMINI.md                      ← Config riêng cho Gemini
│   │
│   ├── knowledge/                     ← Kiến thức dự án (bắt buộc load)
│   │   ├── project-overview.md        ← Tổng quan dự án
│   │   ├── frontend-architecture.md   ← Kiến trúc Frontend (Angular)
│   │   ├── module-map.md              ← Map các modules
│   │   ├── design-system.md           ← Design system & UI patterns
│   │   ├── code-formatting-workflow.md← Quy tắc format code
│   │   ├── pr-creation-workflow.md    ← Quy trình tạo PR
│   │   └── pre-commit.sh             ← Script pre-commit
│   │
│   ├── rules/                         ← Quy tắc coding (bắt buộc tuân thủ)
│   │   ├── angular.md                 ← Quy tắc Angular
│   │   ├── coding-style.md            ← Code reuse, type safety
│   │   ├── scss.md                    ← Quy tắc SCSS
│   │   └── templates.md              ← Quy tắc sử dụng templates
│   │
│   ├── skills/                        ← Kỹ năng chuyên sâu
│   │   ├── angular-architecture/SKILL.md
│   │   └── styling-standards/SKILL.md
│   │
│   ├── templates/                     ← Templates cho reports & tickets
│   │   ├── bug-report-template.md
│   │   ├── engineering-report-template.md
│   │   ├── jira-ticket-review-template.md
│   │   ├── pull-request-template.md
│   │   └── technical-design-template.md
│   │
│   └── workflows/                     ← Quy trình thực hiện theo loại task
│       ├── bug-fix.md
│       ├── code-review.md
│       ├── feature-delivery.md
│       ├── jira-review.md
│       └── refactor.md
│
└── .github/                           ← Cấu hình cho GitHub Copilot
    ├── copilot-instructions.md        ← Instructions cho Copilot
    │
    ├── agents/                        ← Agent definitions
    │   └── strict-rules.agent.md
    │
    ├── hooks/                         ← Lifecycle hooks (5 phases)
    │   ├── README.md
    │   ├── session.json
    │   ├── phase-1-understanding.hook.md
    │   ├── phase-2-planning.hook.md
    │   ├── phase-3-execution-formatting.hook.md
    │   ├── phase-4-validation-pr.hook.md
    │   └── phase-5-report-generation.hook.md
    │
    ├── knowledge/                     ← Knowledge base cho Copilot
    │   └── core-engineering-guidelines.md
    │
    ├── report-templates/              ← Templates cho reports
    │   ├── bug-report.md
    │   ├── engineering-report.md
    │   ├── jira-ticket-review.md
    │   ├── pull-request.md
    │   └── technical-design.md
    │
    ├── reports/                       ← Thư mục output reports
    │   └── .gitkeep
    │
    └── skills/                        ← Skills definitions cho Copilot
        ├── angular-architecture/SKILL.md
        ├── bug-fix/SKILL.md
        ├── code-review/SKILL.md
        ├── feature-delivery/SKILL.md
        ├── post-code-change/SKILL.md
        ├── pre-pull-request/SKILL.md
        ├── refactor/SKILL.md
        ├── report-generation/SKILL.md
        ├── styling-standards/SKILL.md
        └── unit-test/SKILL.md
```

---

## 🧩 Giải thích từng thành phần

### 1. Workflows — Quy trình thực hiện

Mỗi workflow định nghĩa **chuỗi bước bắt buộc** cho một loại task:

```
Request → Classify → Load Workflow → Execute Steps → Report
```

| Workflow | Khi nào dùng | Steps chính |
|----------|---------------|-------------|
| `feature-delivery.md` | Tạo feature mới | Knowledge → Rules → Discovery → Plan → Approval → Implement → Validate → PR |
| `bug-fix.md` | Sửa bug | Knowledge → Rules → Investigation → Plan → Approval → Fix → Validate → PR |
| `refactor.md` | Refactor code | Knowledge → Rules → Analysis → Plan → Approval → Refactor → Validate → PR |
| `code-review.md` | Review PR | Load PR changes → Apply rules → Report |
| `jira-review.md` | Review Jira ticket | Load ticket → Analyze → Report |

**Nguyên tắc chung:** AI agent **KHÔNG BAO GIỜ** bắt đầu coding ngay. Luôn phải: Load Knowledge → Apply Rules → Plan → Approval → Implement → Validate → Report.

### 2. Rules — Quy tắc coding

| Rule | Phạm vi | Nội dung chính |
|------|---------|----------------|
| `coding-style.md` | Tất cả | Code reuse, check `/core` trước khi tạo mới, type safety, no `any` |
| `angular.md` | Angular | Component structure, file suffixes, lazy loading, signals |
| `scss.md` | Styling | SCSS imports, mixins, variables, no duplication |
| `templates.md` | Documentation | Markdown templates cho reports & tickets |

### 3. Skills — Kỹ năng chuyên sâu

Mỗi SKILL.md định nghĩa **hướng dẫn chi tiết** cho một kỹ năng cụ thể:

- **angular-architecture**: Container vs. Presentation pattern, RxJS data loading
- **styling-standards**: SCSS conventions, glass panels, responsive patterns
- **bug-fix**: Cách reproduce, analyze, fix, validate bugs
- **code-review**: Checklist khi review code
- **feature-delivery**: Quy trình end-to-end từ plan đến PR
- **unit-test**: Cách viết tests với Vitest
- **refactor**: Nguyên tắc refactor an toàn

### 4. Knowledge — Kiến thức dự án

Các files `.md` chứa kiến thức về dự án mà AI agent **bắt buộc load** trước khi thực hiện bất kỳ task nào:

- `project-overview.md` — Tổng quan dự án, tech stack
- `frontend-architecture.md` — Kiến trúc Angular, state management
- `module-map.md` — Map các modules và relationships
- `design-system.md` — Design tokens, UI patterns
- `code-formatting-workflow.md` — Quy tắc format code

### 5. Hooks — Lifecycle hooks

5 phases chạy tự động qua mỗi task:

```
Phase 1: Understanding    → Hiểu request
Phase 2: Planning         → Lên kế hoạch
Phase 3: Execution        → Thực hiện + Format code
Phase 4: Validation       → Validate + Tạo PR
Phase 5: Report           → Tạo báo cáo
```

### 6. Templates — Templates cho outputs

| Template | Dùng cho |
|----------|----------|
| `bug-report-template.md` | Báo cáo bug |
| `engineering-report-template.md` | Báo cáo kỹ thuật sau khi implement |
| `technical-design-template.md` | Thiết kế kỹ thuật (implementation plan) |
| `pull-request-template.md` | Mô tả PR |
| `jira-ticket-review-template.md` | Review Jira ticket |

---

## 🔄 Flow hoạt động

```
Developer Request
       │
       ▼
┌─────────────────┐
│  Classify Type   │  ← feature / bug / refactor / review
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Load Workflow   │  ← .agents/workflows/<type>.md
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Load Knowledge  │  ← .agents/knowledge/*.md
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Apply Rules     │  ← .agents/rules/*.md
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Plan & Approval │  ← Chờ user approve
└────────┬────────┘
         │ Approved
         ▼
┌─────────────────┐
│  Implement       │  ← Code theo rules + skills
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Validate        │  ← Test + Build + Visual check
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Report + PR     │  ← Tạo report + Pull Request
└─────────────────┘
```

---

## 🔗 Liên kết

| Resource | Path |
|----------|------|
| AI Coding Skills Framework | `../AI Coding Skills Framework/AI_AGENT_FRAMEWORK.md` |
| Orchestration Workflow | `.agents/AGENTS.md` |
| GitHub Copilot Config | `.github/copilot-instructions.md` |

---

> **Ghi chú:** Thư mục `.ai-tools/` được thiết kế để copy vào bất kỳ dự án nào cần AI agent hỗ trợ. Chỉ cần customize `knowledge/`, `rules/`, và `workflows/` cho phù hợp với dự án của bạn.