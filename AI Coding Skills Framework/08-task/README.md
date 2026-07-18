# 📋 VIII. Task

### Tại Sao Task Management Quan Trọng?

> *"Agent không quản lý task giống như đầu bếp_ORDER_100 món cùng lúc — nhận hết nhưng nấu không kịp, món nào cũng dở."*

#### Bằng chứng nghiên cứu:

1. **Anthropic (2025)**: Task decomposition giảm **40% completion time** và **55% error rate** trong complex coding tasks.
2. **OpenAI (2025)**: Structured task tracking tăng **35% accuracy** trong multi-step reasoning compared to unstructured approaches.
3. **Microsoft Research (2024)**: Task prioritization framework giảm **30% resource waste** trong AI-assisted development workflows.

#### Triết lý cốt lõi:

```
Task = Analyze → Classify → Prioritize → Decompose → Execute → Track → Report
```

**Analogies**: Task management giống quản lý bệnh viện — phân loại bệnh nhân (classify), ưu tiên cấp cứu (prioritize), kê toa (plan), theo dõi điều trị (track), và xuất viện (complete).

**Nếu bỏ qua**: Agent overwhelm bởi quá nhiều tasks, deliver sai thứ tự, lãng phí compute resources, và user frustration.

## Tổng Quan

**Task Management** trong AI coding là quá trình **phân tích, chia nhỏ, ưu tiên và theo dõi** các tác vụ coding. Task tốt giúp AI agent tập trung vào đúng việc, tránh overload, và deliver kết quả chất lượng.

```
┌──────────────────────────────────────────────────────────────────┐
│                         TASK MANAGEMENT                           │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │                                                            │  │
│  │  ┌──────────┐    ┌──────────┐    ┌──────────┐            │  │
│  │  │  Input   │    │  Parse & │    │  Task    │            │  │
│  │  │  Request │───►│  Classify│───►│  Queue   │            │  │
│  │  └──────────┘    └──────────┘    └────┬─────┘            │  │
│  │                                        │                   │  │
│  │       ┌────────────────────────────────┘                   │  │
│  │       ▼                                                    │  │
│  │  ┌──────────┐    ┌──────────┐    ┌──────────┐            │  │
│  │  │  Plan &  │    │  Execute │    │  Validate│            │  │
│  │  │  Assign  │───►│  & Track │───►│  & Close │            │  │
│  │  └──────────┘    └──────────┘    └──────────┘            │  │
│  │                                                            │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## Nội Dung

| # | Chủ đề | Mô tả |
|---|--------|-------|
| 1 | [Task Classification](#1-task-classification) | Phân loại task theo kiểu |
| 2 | [Task Decomposition](#2-task-decomposition) | Phân rã task lớn thành nhỏ |
| 3 | [Priority & Scheduling](#3-priority--scheduling) | Ưu tiên và lên lịch |
| 4 | [Task State Management](#4-task-state-management) | Quản lý trạng thái task |
| 5 | [Dependency Management](#5-dependency-management) | Quản lý phụ thuộc giữa tasks |
| 6 | [Task Templates](#6-task-templates) | Mẫu task phổ biến |
| 7 | [Estimation Techniques](#7-estimation-techniques) | Kỹ thuật ước lượng task |
| 8 | [Anti-Patterns & Solutions](#8-anti-patterns--solutions) | Các lỗi thường gặp |
| 9 | [Real-World Workflows](#9-real-world-workflows) | Quy trình thực tế |
| 10 | [Token Budget Management](#10-token-budget-management) | Quản lý ngân sách token |

---

## 1. Task Classification

### 1.1 Phân Loại Task Coding

```
┌──────────────────────────────────────────────────────────────────┐
│                    TASK TYPE HIERARCHY                            │
│                                                                  │
│  Code Generation                                                 │
│  ├── New Feature        → Tạo tính năng mới                    │
│  ├── Code Snippet       → Tạo đoạn code ngắn                   │
│  ├── Boilerplate        → Tạo template/mẫu                     │
│  └── API Endpoint       → Tạo REST/GraphQL endpoint             │
│                                                                  │
│  Code Modification                                                │
│  ├── Refactor            → Cải thiện cấu trúc code             │
│  ├── Bug Fix             → Sửa lỗi                              │
│  ├── Optimization        → Tối ưu performance                   │
│  └── Migration           → Di chuyển code/platform              │
│                                                                  │
│  Code Analysis                                                    │
│  ├── Code Review         → Đánh giá code quality                │
│  ├── Debugging           → Tìm nguyên nhân lỗi                  │
│  ├── Profiling           → Phân tích performance                 │
│  └── Security Audit      → Kiểm tra bảo mật                     │
│                                                                  │
│  Documentation                                                    │
│  ├── README              → Tài liệu dự án                       │
│  ├── API Docs            → Tài liệu API                         │
│  ├── Comments            → Inline comments                      │
│  └── Changelog           → Nhật ký thay đổi                     │
│                                                                  │
│  Testing                                                          │
│  ├── Unit Test           → Test đơn vị                          │
│  ├── Integration Test    → Test tích hợp                         │
│  ├── E2E Test            → Test đầu cuối                        │
│  └── Test Fixtures       → Dữ liệu test                         │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 1.2 Task Classification Engine

```python
from dataclasses import dataclass, field
from typing import List, Optional
from enum import Enum
import re

class TaskCategory(Enum):
    GENERATION = "generation"
    MODIFICATION = "modification"
    ANALYSIS = "analysis"
    DOCUMENTATION = "documentation"
    TESTING = "testing"
    UNKNOWN = "unknown"

class TaskComplexity(Enum):
    TRIVIAL = 1     # 1 file, < 50 LOC
    SIMPLE = 2      # 1-2 files, < 200 LOC
    MODERATE = 3    # 3-5 files, 200-500 LOC
    COMPLEX = 4     # 5-15 files, 500-2000 LOC
    EPIC = 5        # 15+ files, 2000+ LOC


@dataclass
class Task:
    """Đại diện cho một coding task"""
    id: str
    title: str
    description: str
    category: TaskCategory = TaskCategory.UNKNOWN
    complexity: TaskComplexity = TaskComplexity.SIMPLE
    priority: int = 5                    # 1=highest, 10=lowest
    tags: List[str] = field(default_factory=list)
    dependencies: List[str] = field(default_factory=list)
    estimated_tokens: int = 0
    files_involved: List[str] = field(default_factory=list)


class TaskClassifier:
    """
    Phân loại task tự động dựa trên keywords và patterns.
    
    Usage:
        classifier = TaskClassifier()
        task = classifier.classify(
            "Fix the authentication bug in login handler"
        )
        print(task.category)  # TaskCategory.MODIFICATION
        print(task.complexity)  # TaskComplexity.SIMPLE
    """
    
    PATTERNS = {
        TaskCategory.GENERATION: [
            r"tạo|create|generate|build|new|thêm|add",
            r"function|class|component|endpoint|api",
            r"from scratch|từ đầu|mới",
        ],
        TaskCategory.MODIFICATION: [
            r"sửa|fix|bug|error|broken",
            r"refactor|cải thiện|improve|optimize",
            r"thay đổi|change|update|migrate",
        ],
        TaskCategory.ANALYSIS: [
            r"kiểm tra|check|review|analyze|inspect",
            r"tìm|find|debug|trace|profile",
            r"security|audit|vulnerability",
        ],
        TaskCategory.DOCUMENTATION: [
            r"viết docs|write.*doc|readme|changelog",
            r"document|tài liệu|hướng dẫn|guide",
            r"comment|annotate",
        ],
        TaskCategory.TESTING: [
            r"test|viết test|write.*test|spec",
            r"mock|fixture|assert|coverage",
            r"integration|e2e|unit test",
        ],
    }
    
    COMPLEXITY_SIGNALS = {
        TaskComplexity.TRIVIAL: [
            r"1 dòng|one line|typo|formatting",
            r"rename|đổi tên|thay màu",
        ],
        TaskComplexity.SIMPLE: [
            r"fix.*bug|sửa lỗi đơn",
            r"thêm field|add field|add property",
            r"1 file|single file",
        ],
        TaskComplexity.MODERATE: [
            r"feature|tính năng|feature mới",
            r"refactor|cải trúc|restructure",
            r"multi.?file|nhiều file",
        ],
        TaskComplexity.COMPLEX: [
            r"module|system|hệ thống|architecture",
            r"migration|di chuyển|migrate",
            r"performance.*optimization|tối ưu lớn",
        ],
        TaskComplexity.EPIC: [
            r"redesign|thiết kế lại|rebuild",
            r"major.*overhaul|nâng cấp lớn",
            r"multiple.*module|nhiều module",
        ],
    }
    
    def classify(self, text: str) -> Task:
        text_lower = text.lower()
        
        # Classify category
        category_scores = {}
        for cat, patterns in self.PATTERNS.items():
            score = sum(
                1 for p in patterns 
                if re.search(p, text_lower)
            )
            category_scores[cat] = score
        
        category = max(category_scores, 
                      key=category_scores.get,
                      default=TaskCategory.UNKNOWN)
        if category_scores.get(category, 0) == 0:
            category = TaskCategory.UNKNOWN
        
        # Classify complexity
        complexity_scores = {}
        for comp, patterns in self.COMPLEXITY_SIGNALS.items():
            score = sum(
                1 for p in patterns 
                if re.search(p, text_lower)
            )
            complexity_scores[comp] = score
        
        complexity = max(complexity_scores,
                        key=complexity_scores.get,
                        default=TaskComplexity.SIMPLE)
        if complexity_scores.get(complexity, 0) == 0:
            complexity = TaskComplexity.SIMPLE
        
        return Task(
            id=f"task-{hash(text) % 10000:04d}",
            title=text[:100],
            description=text,
            category=category,
            complexity=complexity,
        )
```

### 1.3 Decision Tree: Chọn Strategy Xử Lý Task

```
┌──────────────────────────────────────────────────────────────────┐
│              TASK HANDLING DECISION TREE                          │
│                                                                  │
│  Task arrived                                                    │
│       │                                                          │
│       ▼                                                          │
│  ┌─────────────┐     YES    ┌──────────────────┐                │
│  │ Is it clear? │──────────►│ Can be done in   │                │
│  │ (clear scope)│          │ < 500 tokens?     │                │
│  └──────┬──────┘          └────────┬─────────┘                  │
│         │ NO                       │ YES        NO               │
│         ▼                          ▼            ▼                │
│  ┌──────────────┐         ┌────────────┐  ┌──────────────┐     │
│  │  Clarify     │         │  Execute   │  │  Decompose   │     │
│  │  requirements│         │  directly  │  │  into sub-   │     │
│  │  first       │         │  (1 shot)  │  │  tasks       │     │
│  └──────────────┘         └────────────┘  └──────┬───────┘     │
│                                                   │              │
│                                    ┌──────────────┼──────────┐  │
│                                    ▼              ▼          ▼  │
│                             ┌──────────┐  ┌──────────┐ ┌────┐ │
│                             │Sequential│  │ Parallel │ │TDD │ │
│                             │ Pipeline │  │ Fan-out  │ │    │ │
│                             └──────────┘  └──────────┘ └────┘ │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 2. Task Decomposition

### 2.1 Patterns Phân Rã Task

```
┌──────────────────────────────────────────────────────────────────┐
│               TASK DECOMPOSITION PATTERNS                         │
│                                                                  │
│  Pattern 1: SEQUENTIAL DECOMPOSITION                            │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐                 │
│  │ Parent   │───►│ Sub 1    │───►│ Sub 2    │───► ...          │
│  │ Task     │    │ (must    │    │ (needs   │                   │
│  │          │    │  finish) │    │  sub 1)  │                   │
│  └──────────┘    └──────────┘    └──────────┘                 │
│                                                                  │
│  Pattern 2: PARALLEL DECOMPOSITION                              │
│  ┌──────────┐    ┌──────────┐                                  │
│  │ Parent   │───►│ Sub A    │ ─┐                                │
│  │ Task     │    │ (indep.) │   │    ┌──────────┐              │
│  │          │    ├──────────┤   ├───►│  Merge   │              │
│  │          │───►│ Sub B    │ ─┤    │  Results │              │
│  │          │    │ (indep.) │   │    └──────────┘              │
│  │          │    ├──────────┤   │                               │
│  │          │───►│ Sub C    │ ─┘                                │
│  │          │    │ (indep.) │                                   │
│  └──────────┘    └──────────┘                                  │
│                                                                  │
│  Pattern 3: HIERARCHICAL DECOMPOSITION                          │
│  ┌──────────┐                                                   │
│  │ Epic     │                                                   │
│  └────┬─────┘                                                   │
│       ├── Feature 1                                             │
│       │    ├── Sub-task 1.1                                     │
│       │    └── Sub-task 1.2                                     │
│       └── Feature 2                                             │
│            ├── Sub-task 2.1                                     │
│            ├── Sub-task 2.2                                     │
│            └── Sub-task 2.3                                     │
│                                                                  │
│  Pattern 4: MAP-REDUCE DECOMPOSITION                            │
│  ┌──────────┐    ┌──────────────────────────────┐              │
│  │ Input    │───►│ MAP: Process each item in    │              │
│  │ Data     │    │       parallel                │              │
│  │          │    ├──────────────────────────────┤              │
│  │          │    │ REDUCE: Combine results       │──► Output    │
│  └──────────┘    └──────────────────────────────┘              │
│                                                                  │
│  Pattern 5: SPIKE-THEN-EXECUTE                                  │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐                 │
│  │  Spike   │───►│ Research │───►│ Execute  │                 │
│  │ (timebox)│    │ & Decide │    │ with     │                  │
│  │ 1-2 hrs  │    │ approach │    │ confidence│                 │
│  └──────────┘    └──────────┘    └──────────┘                 │
│                                                                  │
│  Pattern 6: VERTICAL SLICE                                      │
│  ┌──────────────────────────────────────────┐                  │
│  │  Full Stack Feature (thin slice)         │                  │
│  │  DB → API → Service → UI → Test          │                  │
│  │  Each slice = working increment          │                  │
│  └──────────────────────────────────────────┘                  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 2.2 Task Decomposer

```python
from typing import List, Dict, Optional
from dataclasses import dataclass, field

@dataclass
class SubTask:
    """Sub-task con trong task decomposition"""
    id: str
    title: str
    description: str
    order: int
    parallel: bool = False
    estimated_tokens: int = 0
    dependencies: List[str] = field(default_factory=list)
    files_to_modify: List[str] = field(default_factory=list)
    verification_criteria: str = ""


class TaskDecomposer:
    """
    Phân rã task lớn thành các sub-task có thể thực hiện riêng.
    
    Strategies:
    - feature-based: Chia theo tính năng
    - layer-based: Chia theo layer (UI, API, DB)
    - file-based: Chia theo file
    - test-driven: Viết test trước, rồi code
    - vertical-slice: Mỗi slice = full stack working increment
    - spike-then-execute: Research trước, rồi implement
    """
    
    def decompose(self, task: Task, strategy: str = "auto",
                  project_context: Dict = None) -> List[SubTask]:
        """Phân rã task thành sub-tasks"""
        
        if strategy == "auto":
            strategy = self._suggest_strategy(task, project_context)
        
        if strategy == "feature":
            return self._decompose_by_feature(task)
        elif strategy == "layer":
            return self._decompose_by_layer(task)
        elif strategy == "file":
            return self._decompose_by_file(task)
        elif strategy == "tdd":
            return self._decompose_tdd(task)
        elif strategy == "vertical_slice":
            return self._decompose_vertical_slice(task)
        elif strategy == "spike":
            return self._decompose_spike(task)
        else:
            return self._decompose_by_feature(task)
    
    def _suggest_strategy(self, task: Task, 
                          context: Dict = None) -> str:
        """Gợi ý strategy phù hợp dựa trên task characteristics"""
        context = context or {}
        
        # Trivial tasks - don't decompose
        if task.complexity.value <= 1:
            return "simple"
        
        # Test-related tasks
        if "test" in task.description.lower():
            return "tdd"
        
        # Generation tasks - feature-based
        if task.category == TaskCategory.GENERATION:
            if len(task.files_involved) > 3:
                return "vertical_slice"
            return "feature"
        
        # Modification tasks - file-based or layer-based
        if task.category == TaskCategory.MODIFICATION:
            if len(task.files_involved) > 5:
                return "layer"
            return "file"
        
        # Complex/uncertain tasks - spike first
        if task.complexity.value >= 4:
            if context.get("uncertain_approach", False):
                return "spike"
            return "layer"
        
        return "feature"
    
    def _decompose_by_feature(self, task: Task) -> List[SubTask]:
        """Chia theo tính năng — full workflow"""
        subtasks = []
        
        subtasks.append(SubTask(
            id=f"{task.id}-01",
            title="Phân tích yêu cầu",
            description=f"Đọc và hiểu yêu cầu: {task.description}",
            order=1,
            estimated_tokens=500,
            verification_criteria="Có danh sách requirements rõ ràng",
        ))
        
        subtasks.append(SubTask(
            id=f"{task.id}-02",
            title="Đọc code hiện tại",
            description="Xem codebase liên quan để hiểu context",
            order=2,
            dependencies=[f"{task.id}-01"],
            estimated_tokens=1500,
            verification_criteria="Đã identify affected files và patterns",
        ))
        
        subtasks.append(SubTask(
            id=f"{task.id}-03",
            title="Lên kế hoạch thay đổi",
            description="Xác định files cần sửa và cách tiếp cận",
            order=3,
            dependencies=[f"{task.id}-02"],
            estimated_tokens=800,
            verification_criteria="Có plan cụ thể với file list và approach",
        ))
        
        subtasks.append(SubTask(
            id=f"{task.id}-04",
            title="Triển khai code",
            description="Viết code theo kế hoạch",
            order=4,
            dependencies=[f"{task.id}-03"],
            estimated_tokens=task.estimated_tokens or 3000,
            verification_criteria="Code đã viết xong, không có syntax errors",
        ))
        
        subtasks.append(SubTask(
            id=f"{task.id}-05",
            title="Kiểm tra & validate",
            description="Chạy test, lint, review kết quả",
            order=5,
            dependencies=[f"{task.id}-04"],
            estimated_tokens=1000,
            verification_criteria="Tests pass, lint clean, no regressions",
        ))
        
        return subtasks
    
    def _decompose_by_layer(self, task: Task) -> List[SubTask]:
        """Chia theo layer: DB → Service → API → UI"""
        return [
            SubTask(f"{task.id}-db", "Database layer",
                    "Thay đổi schema, queries, migrations", 1,
                    estimated_tokens=1500,
                    verification_criteria="Schema updated, migrations run"),
            SubTask(f"{task.id}-svc", "Service layer",
                    "Business logic, validation", 2,
                    dependencies=[f"{task.id}-db"],
                    estimated_tokens=2000,
                    verification_criteria="Service logic complete, unit tests pass"),
            SubTask(f"{task.id}-api", "API layer",
                    "Endpoints, request/response, error handling", 3,
                    dependencies=[f"{task.id}-svc"],
                    estimated_tokens=1500,
                    verification_criteria="API endpoints working, OpenAPI spec updated"),
            SubTask(f"{task.id}-ui", "UI layer",
                    "Frontend components, forms, display", 4,
                    dependencies=[f"{task.id}-api"],
                    estimated_tokens=2000,
                    verification_criteria="UI renders correctly, forms submit"),
            SubTask(f"{task.id}-test", "Integration tests",
                    "End-to-end flow tests", 5,
                    dependencies=[f"{task.id}-ui"],
                    estimated_tokens=1500,
                    verification_criteria="All integration tests pass"),
        ]
    
    def _decompose_by_file(self, task: Task) -> List[SubTask]:
        """Chia theo file — mỗi file là 1 sub-task"""
        subtasks = []
        for i, filepath in enumerate(task.files_involved, 1):
            subtasks.append(SubTask(
                id=f"{task.id}-f{i:02d}",
                title=f"Modify {filepath}",
                description=f"Thay đổi file {filepath}",
                order=i,
                files_to_modify=[filepath],
                parallel=True,
                estimated_tokens=1000,
                verification_criteria=f"File {filepath} updated correctly",
            ))
        return subtasks
    
    def _decompose_tdd(self, task: Task) -> List[SubTask]:
        """Test-Driven Development decomposition"""
        return [
            SubTask(f"{task.id}-test-design", "Thiết kế test cases",
                    "Viết test cases dựa trên requirements", 1,
                    estimated_tokens=1000,
                    verification_criteria="Test cases cover all scenarios"),
            SubTask(f"{task.id}-test-write", "Viết tests",
                    "Viết test code (sẽ fail lúc đầu)", 2,
                    dependencies=[f"{task.id}-test-design"],
                    estimated_tokens=1500,
                    verification_criteria="Tests compile, run, and fail (RED)"),
            SubTask(f"{task.id}-impl", "Triển khai code",
                    "Viết code để pass tests", 3,
                    dependencies=[f"{task.id}-test-write"],
                    estimated_tokens=task.estimated_tokens or 2000,
                    verification_criteria="All tests pass (GREEN)"),
            SubTask(f"{task.id}-refactor", "Refactor",
                    "Cải thiện code quality", 4,
                    dependencies=[f"{task.id}-impl"],
                    estimated_tokens=1000,
                    verification_criteria="Code clean, tests still pass"),
        ]
    
    def _decompose_vertical_slice(self, task: Task) -> List[SubTask]:
        """Vertical slice — mỗi slice = full stack working feature"""
        return [
            SubTask(f"{task.id}-slice-core", "Core slice",
                    "Implement core logic + minimal API", 1,
                    estimated_tokens=3000,
                    verification_criteria="Core flow works end-to-end"),
            SubTask(f"{task.id}-slice-data", "Data slice",
                    "Database + repository layer", 2,
                    dependencies=[f"{task.id}-slice-core"],
                    estimated_tokens=2000,
                    verification_criteria="Data persistence working"),
            SubTask(f"{task.id}-slice-api", "API slice",
                    "Full API with validation + error handling", 3,
                    dependencies=[f"{task.id}-slice-data"],
                    estimated_tokens=2000,
                    verification_criteria="All API endpoints functional"),
            SubTask(f"{task.id}-slice-ui", "UI slice",
                    "Frontend integration", 4,
                    dependencies=[f"{task.id}-slice-api"],
                    estimated_tokens=2500,
                    verification_criteria="UI fully functional"),
            SubTask(f"{task.id}-slice-test", "Test slice",
                    "Integration + E2E tests", 5,
                    dependencies=[f"{task.id}-slice-ui"],
                    estimated_tokens=2000,
                    verification_criteria="Full test coverage"),
        ]
    
    def _decompose_spike(self, task: Task) -> List[SubTask]:
        """Spike-then-execute — research trước khi implement"""
        return [
            SubTask(f"{task.id}-spike", "Spike: Research & Decide",
                    "Timebox 1-2h research, compare approaches", 1,
                    estimated_tokens=3000,
                    verification_criteria="Chosen approach documented with rationale"),
            SubTask(f"{task.id}-poc", "Proof of Concept",
                    "Minimal implementation to validate approach", 2,
                    dependencies=[f"{task.id}-spike"],
                    estimated_tokens=2000,
                    verification_criteria="POC works, approach validated"),
            SubTask(f"{task.id}-impl", "Full Implementation",
                    "Implement complete solution", 3,
                    dependencies=[f"{task.id}-poc"],
                    estimated_tokens=task.estimated_tokens or 4000,
                    verification_criteria="Feature complete"),
            SubTask(f"{task.id}-validate", "Validation",
                    "Test, review, verify", 4,
                    dependencies=[f"{task.id}-impl"],
                    estimated_tokens=1500,
                    verification_criteria="All tests pass, code reviewed"),
        ]
```

---

## 3. Priority & Scheduling

### 3.1 Priority Model

```python
from dataclasses import dataclass
from typing import List
from datetime import datetime

@dataclass
class PriorityScore:
    """Score tính toán cho priority — Eisenhower Matrix + dependency bonus"""
    urgency: int        # 1-5: mức độ khẩn cấp
    importance: int     # 1-5: mức độ quan trọng
    effort: int         # 1-5: effort cần thiết (thấp = score cao)
    dependency_count: int  # Số tasks phụ thuộc vào task này
    
    @property
    def total(self) -> float:
        """Weighted score với quick-wins bonus"""
        base = self.urgency * 0.4 + self.importance * 0.4
        effort_bonus = (6 - self.effort) * 0.1   # Quick wins bonus
        dep_bonus = min(self.dependency_count * 0.1, 0.3)
        return base + effort_bonus + dep_bonus


class TaskScheduler:
    """
    Lên lịch tasks dựa trên priority, dependency, và token budget.
    
    Rules:
    1. Luôn làm task có dependency cao trước
    2. Quick wins (thấp effort, cao priority) nên làm trước
    3. Respect token budget per session
    """
    
    def __init__(self, max_tokens_per_session: int = 50000):
        self.max_tokens = max_tokens_per_session
    
    def schedule(self, tasks: List[Task]) -> List[List[Task]]:
        """
        Tạo lịch thực hiện — trả về list các batch.
        Mỗi batch là nhóm tasks có thể chạy cùng lúc.
        """
        # Sort by priority score descending
        scored = [(t, self._score(t)) for t in tasks]
        scored.sort(key=lambda x: x[1].total, reverse=True)
        
        batches = []
        current_batch = []
        current_tokens = 0
        
        for task, score in scored:
            task_tokens = task.estimated_tokens or 1000
            
            if current_tokens + task_tokens > self.max_tokens:
                if current_batch:
                    batches.append(current_batch)
                current_batch = [task]
                current_tokens = task_tokens
            else:
                current_batch.append(task)
                current_tokens += task_tokens
        
        if current_batch:
            batches.append(current_batch)
        
        return batches
    
    def _score(self, task: Task) -> PriorityScore:
        """Tính priority score cho task"""
        effort = task.complexity.value
        urgency = max(1, 6 - task.priority)
        
        importance_map = {
            TaskCategory.MODIFICATION: 5,   # Bug fix = high
            TaskCategory.TESTING: 4,
            TaskCategory.GENERATION: 3,
            TaskCategory.ANALYSIS: 3,
            TaskCategory.DOCUMENTATION: 2,
        }
        importance = importance_map.get(task.category, 3)
        
        return PriorityScore(
            urgency=urgency,
            importance=importance,
            effort=effort,
            dependency_count=len(task.dependencies),
        )
```

---

## 4. Task State Management

### 4.1 Task Lifecycle

```
┌──────────────────────────────────────────────────────────────────┐
│                    TASK LIFECYCLE                                 │
│                                                                  │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐                 │
│  │ PENDING  │───►│ PLANNING │───►│IN_PROGRESS│                │
│  │          │    │          │    │          │                   │
│  │ Chưa bắt │    │ Đang lên │    │ Đang làm │                   │
│  │ đầu      │    │ kế hoạch │    │          │                   │
│  └──────────┘    └──────────┘    └────┬─────┘                 │
│                                       │                         │
│                    ┌──────────────────┤                         │
│                    ▼                  ▼                         │
│              ┌──────────┐    ┌──────────┐                     │
│              │BLOCKED   │    │ TESTING  │                      │
│              │          │    │          │                      │
│              │ Bị chặn  │    │ Đang test│                      │
│              └────┬─────┘    └────┬─────┘                      │
│                   │              │                              │
│                   ▼              ▼                              │
│              ┌──────────┐    ┌──────────┐                     │
│              │PENDING   │    │ REVIEW   │                     │
│              │(retry)   │    │          │                      │
│              └──────────┘    └────┬─────┘                      │
│                                  │                              │
│                    ┌─────────────┼─────────────┐               │
│                    ▼             ▼             ▼               │
│              ┌──────────┐  ┌──────────┐  ┌──────────┐        │
│              │  DONE    │  │  FAILED  │  │CANCELLED │        │
│              │          │  │          │  │          │         │
│              │ Hoàn thành│ │ Thất bại │  │ Bị hủy   │        │
│              └──────────┘  └──────────┘  └──────────┘        │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 4.2 Task State Manager

```python
from enum import Enum
from typing import Dict, List, Optional, Callable
from dataclasses import dataclass, field
from datetime import datetime

class TaskStatus(Enum):
    PENDING = "pending"
    PLANNING = "planning"
    IN_PROGRESS = "in_progress"
    BLOCKED = "blocked"
    TESTING = "testing"
    REVIEW = "review"
    DONE = "done"
    FAILED = "failed"
    CANCELLED = "cancelled"


@dataclass
class TaskState:
    """Trạng thái hiện tại của task"""
    task_id: str
    status: TaskStatus = TaskStatus.PENDING
    started_at: Optional[str] = None
    completed_at: Optional[str] = None
    progress: float = 0.0        # 0.0 → 1.0
    attempts: int = 0
    last_error: Optional[str] = None
    notes: List[str] = field(default_factory=list)


class TaskStateManager:
    """
    Quản lý lifecycle của tasks — tracks status changes,
    enforces valid transitions, và emits events.
    """
    
    VALID_TRANSITIONS = {
        TaskStatus.PENDING: [
            TaskStatus.PLANNING, TaskStatus.CANCELLED
        ],
        TaskStatus.PLANNING: [
            TaskStatus.IN_PROGRESS, TaskStatus.CANCELLED
        ],
        TaskStatus.IN_PROGRESS: [
            TaskStatus.TESTING, TaskStatus.BLOCKED,
            TaskStatus.FAILED, TaskStatus.DONE,
        ],
        TaskStatus.BLOCKED: [
            TaskStatus.IN_PROGRESS, TaskStatus.CANCELLED,
            TaskStatus.PENDING,
        ],
        TaskStatus.TESTING: [
            TaskStatus.REVIEW, TaskStatus.IN_PROGRESS,
            TaskStatus.FAILED,
        ],
        TaskStatus.REVIEW: [
            TaskStatus.DONE, TaskStatus.IN_PROGRESS,
            TaskStatus.FAILED,
        ],
        TaskStatus.FAILED: [
            TaskStatus.PENDING, TaskStatus.CANCELLED,
        ],
    }
    
    def __init__(self):
        self.states: Dict[str, TaskState] = {}
        self.listeners: List[Callable] = []
    
    def create(self, task_id: str) -> TaskState:
        state = TaskState(task_id=task_id)
        self.states[task_id] = state
        return state
    
    def transition(self, task_id: str, new_status: TaskStatus,
                   note: str = "") -> TaskState:
        """Chuyển trạng thái task — kiểm tra validity"""
        state = self.states.get(task_id)
        if not state:
            raise ValueError(f"Task {task_id} not found")
        
        valid = self.VALID_TRANSITIONS.get(state.status, [])
        if new_status not in valid:
            raise InvalidTransitionError(
                f"Cannot transition from {state.status.value} "
                f"to {new_status.value}"
            )
        
        old_status = state.status
        state.status = new_status
        
        if new_status == TaskStatus.IN_PROGRESS and not state.started_at:
            state.started_at = datetime.now().isoformat()
            state.attempts += 1
        
        if new_status in (TaskStatus.DONE, TaskStatus.FAILED, 
                         TaskStatus.CANCELLED):
            state.completed_at = datetime.now().isoformat()
        
        if note:
            state.notes.append(
                f"[{datetime.now().isoformat()}] {note}"
            )
        
        # Notify listeners
        for listener in self.listeners:
            listener(task_id, old_status, new_status)
        
        return state
    
    def on_transition(self, callback: Callable):
        self.listeners.append(callback)


class InvalidTransitionError(Exception):
    pass
```

---

## 5. Dependency Management

### 5.1 Task Dependency Graph

```python
from collections import defaultdict, deque
from typing import List, Dict, Set, Optional

class TaskDependencyGraph:
    """
    Directed Acyclic Graph (DAG) cho task dependencies.
    
    Features:
    - Thêm/bỏ dependencies
    - Topological sort (execution order)
    - Parallel groups (tasks chạy cùng lúc)
    - Cycle detection
    """
    
    def __init__(self):
        self.adj: Dict[str, Set[str]] = defaultdict(set)
        self.tasks: Dict[str, Task] = {}
    
    def add_task(self, task: Task):
        self.tasks[task.id] = task
        for dep_id in task.dependencies:
            self.adj[dep_id].add(task.id)
    
    def has_cycle(self) -> bool:
        """DFS-based cycle detection"""
        visited = set()
        in_stack = set()
        
        def dfs(node: str) -> bool:
            visited.add(node)
            in_stack.add(node)
            for neighbor in self.adj[node]:
                if neighbor not in visited:
                    if dfs(neighbor):
                        return True
                elif neighbor in in_stack:
                    return True
            in_stack.discard(node)
            return False
        
        return any(dfs(t) for t in self.tasks if t not in visited)
    
    def topological_sort(self) -> List[str]:
        """Trả về execution order (topological sort)"""
        in_degree = {t: 0 for t in self.tasks}
        for node in self.adj:
            for neighbor in self.adj[node]:
                in_degree[neighbor] += 1
        
        queue = deque([t for t, d in in_degree.items() if d == 0])
        order = []
        
        while queue:
            node = queue.popleft()
            order.append(node)
            for neighbor in self.adj[node]:
                in_degree[neighbor] -= 1
                if in_degree[neighbor] == 0:
                    queue.append(neighbor)
        
        if len(order) != len(self.tasks):
            raise ValueError("Cycle detected in task dependencies")
        
        return order
    
    def parallel_groups(self) -> List[List[str]]:
        """Tách thành các nhóm có thể chạy song song"""
        in_degree = {t: 0 for t in self.tasks}
        for node in self.adj:
            for neighbor in self.adj[node]:
                in_degree[neighbor] += 1
        
        groups = []
        remaining = dict(in_degree)
        
        while remaining:
            ready = [t for t, d in remaining.items() if d == 0]
            if not ready:
                raise ValueError("Unresolvable dependency cycle")
            
            groups.append(ready)
            
            for task_id in ready:
                del remaining[task_id]
                for neighbor in self.adj[task_id]:
                    if neighbor in remaining:
                        remaining[neighbor] -= 1
        
        return groups
```

---

## 6. Task Templates

### 6.1 Common Task Templates

```python
TASK_TEMPLATES = {
    "bug_fix": {
        "title": "Fix: {bug_description}",
        "subtasks": [
            {"title": "Reproduce the bug", "tokens": 500},
            {"title": "Read and understand affected code", "tokens": 1000},
            {"title": "Identify root cause", "tokens": 800},
            {"title": "Implement fix", "tokens": 1500},
            {"title": "Write regression test", "tokens": 1000},
            {"title": "Verify fix in all scenarios", "tokens": 500},
        ],
        "estimated_total_tokens": 5300,
    },
    
    "new_feature": {
        "title": "Feature: {feature_name}",
        "subtasks": [
            {"title": "Analyze requirements", "tokens": 500},
            {"title": "Read existing code patterns", "tokens": 1000},
            {"title": "Design approach", "tokens": 800},
            {"title": "Implement core logic", "tokens": 3000},
            {"title": "Add error handling", "tokens": 800},
            {"title": "Write tests", "tokens": 1500},
            {"title": "Update documentation", "tokens": 500},
        ],
        "estimated_total_tokens": 8100,
    },
    
    "code_review": {
        "title": "Review: {target}",
        "subtasks": [
            {"title": "Read all changed files", "tokens": 2000},
            {"title": "Check code style & conventions", "tokens": 500},
            {"title": "Analyze logic & correctness", "tokens": 1500},
            {"title": "Review for security issues", "tokens": 1000},
            {"title": "Write review comments", "tokens": 1000},
        ],
        "estimated_total_tokens": 6000,
    },
    
    "refactor": {
        "title": "Refactor: {target_module}",
        "subtasks": [
            {"title": "Analyze current structure", "tokens": 1000},
            {"title": "Identify code smells", "tokens": 800},
            {"title": "Plan refactoring steps", "tokens": 500},
            {"title": "Extract functions/classes", "tokens": 2000},
            {"title": "Update imports & references", "tokens": 1000},
            {"title": "Run existing tests", "tokens": 500},
            {"title": "Verify behavior unchanged", "tokens": 500},
        ],
        "estimated_total_tokens": 6300,
    },
    
    "debug_investigation": {
        "title": "Debug: {issue_description}",
        "subtasks": [
            {"title": "Gather error info and logs", "tokens": 800},
            {"title": "Read stack trace and affected files", "tokens": 1500},
            {"title": "Add strategic logging/breakpoints", "tokens": 1000},
            {"title": "Reproduce with minimal test case", "tokens": 800},
            {"title": "Identify root cause", "tokens": 1000},
            {"title": "Implement fix", "tokens": 1500},
            {"title": "Add regression test", "tokens": 800},
        ],
        "estimated_total_tokens": 7400,
    },
    
    "performance_optimization": {
        "title": "Optimize: {target}",
        "subtasks": [
            {"title": "Profile current performance", "tokens": 1000},
            {"title": "Identify bottlenecks", "tokens": 800},
            {"title": "Research optimization strategies", "tokens": 1000},
            {"title": "Implement optimizations", "tokens": 2000},
            {"title": "Benchmark before/after", "tokens": 800},
            {"title": "Verify no regressions", "tokens": 500},
        ],
        "estimated_total_tokens": 6100,
    },
    
    "migration": {
        "title": "Migrate: {source} → {target}",
        "subtasks": [
            {"title": "Analyze source and target", "tokens": 1500},
            {"title": "Create migration plan", "tokens": 800},
            {"title": "Write migration scripts", "tokens": 2000},
            {"title": "Test migration on sample data", "tokens": 1000},
            {"title": "Run full migration", "tokens": 1500},
            {"title": "Verify and cleanup", "tokens": 800},
        ],
        "estimated_total_tokens": 7600,
    },
}


def create_task_from_template(template_name: str, **kwargs) -> Task:
    """Tạo task từ template"""
    template = TASK_TEMPLATES.get(template_name)
    if not template:
        raise ValueError(f"Unknown template: {template_name}")
    
    title = template["title"].format(**kwargs)
    
    return Task(
        id=f"task-{hash(title) % 10000:04d}",
        title=title,
        description=f"Task from template: {template_name}",
        category=TaskCategory.UNKNOWN,
        estimated_tokens=template["estimated_total_tokens"],
    )
```

---

## 7. Estimation Techniques

### 7.1 Token Estimation Model

```
┌──────────────────────────────────────────────────────────────────┐
│              TOKEN ESTIMATION GUIDE                               │
│                                                                  │
│  Task Type              │ Estimated Tokens │ Notes              │
│  ───────────────────────┼──────────────────┼───────────────────│
│  Bug Fix (simple)       │ 1,000 - 3,000    │ Single file       │
│  Bug Fix (complex)      │ 3,000 - 8,000    │ Multi-file        │
│  New Feature (small)    │ 3,000 - 6,000    │ 1-2 files         │
│  New Feature (large)    │ 6,000 - 15,000   │ 3-5 files         │
│  Refactor (simple)      │ 2,000 - 5,000    │ Rename, extract   │
│  Refactor (complex)     │ 5,000 - 12,000   │ Restructure       │
│  Code Review            │ 2,000 - 5,000    │ Per PR            │
│  Write Tests            │ 2,000 - 6,000    │ Unit + edge cases │
│  Documentation          │ 1,000 - 3,000    │ README, API docs  │
│  Debug Investigation    │ 3,000 - 10,000   │ Investigation     │
│  Migration              │ 5,000 - 20,000   │ Data + code       │
│                                                                  │
│  Context Window Budget (128K model):                             │
│  ├── System prompt:     ~5K tokens                              │
│  ├── Project context:   ~30K tokens                             │
│  ├── Task context:      ~20K tokens                             │
│  ├── Working memory:    ~50K tokens                             │
│  └── Reserve:           ~23K tokens (safety buffer)             │
│                                                                  │
│  Rules of Thumb:                                                 │
│  • 1 LOC ≈ 5-10 tokens (code)                                   │
│  • 1 line of comment ≈ 8-12 tokens                              │
│  • 1 function definition ≈ 20-50 tokens                         │
│  • 1 class definition ≈ 50-100 tokens                           │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 7.2 Effort Estimation Algorithm

```python
from enum import Enum
from typing import Dict, List, Tuple

class EffortEstimator:
    """
    ước lượng effort cho coding tasks dựa trên historical data
    và task characteristics.
    """
    
    # Base tokens per complexity level
    BASE_TOKENS = {
        TaskComplexity.TRIVIAL: 500,
        TaskComplexity.SIMPLE: 2000,
        TaskComplexity.MODERATE: 5000,
        TaskComplexity.COMPLEX: 12000,
        TaskComplexity.EPIC: 25000,
    }
    
    # Multipliers per category
    CATEGORY_MULTIPLIERS = {
        TaskCategory.GENERATION: 1.0,
        TaskCategory.MODIFICATION: 0.8,
        TaskCategory.ANALYSIS: 0.6,
        TaskCategory.DOCUMENTATION: 0.4,
        TaskCategory.TESTING: 0.7,
    }
    
    # File involvement bonus
    FILE_BONUS_PER_FILE = 500
    
    def estimate_tokens(self, task: Task) -> int:
        """Ước lượng số tokens cần thiết"""
        base = self.BASE_TOKENS.get(task.complexity, 2000)
        multiplier = self.CATEGORY_MULTIPLIERS.get(task.category, 1.0)
        file_bonus = len(task.files_involved) * self.FILE_BONUS_PER_FILE
        
        return int(base * multiplier + file_bonus)
    
    def estimate_duration(self, task: Task) -> Dict[str, float]:
        """
        ước lượng thời gian thực hiện (phút).
        Giả định: ~100 tokens/minute processing speed.
        """
        tokens = self.estimate_tokens(task)
        processing_minutes = tokens / 100
        
        # Add overhead for coordination, review
        overhead = 1.2 if len(task.dependencies) > 2 else 1.1
        
        total_minutes = processing_minutes * overhead
        
        return {
            "processing_minutes": processing_minutes,
            "overhead_factor": overhead,
            "total_minutes": total_minutes,
            "total_hours": total_minutes / 60,
        }
    
    def estimate_batch(self, tasks: List[Task]) -> Dict[str, float]:
        """Ước lượng cho cả batch tasks"""
        total_tokens = sum(self.estimate_tokens(t) for t in tasks)
        estimates = [self.estimate_duration(t) for t in tasks]
        
        return {
            "total_tokens": total_tokens,
            "total_minutes": sum(e["total_minutes"] for e in estimates),
            "task_count": len(tasks),
            "avg_tokens_per_task": total_tokens / len(tasks) if tasks else 0,
        }
```

---

## 8. Anti-Patterns & Solutions

### 8.1 Common Anti-Patterns

```
┌──────────────────────────────────────────────────────────────────┐
│              TASK MANAGEMENT ANTI-PATTERNS                        │
│                                                                  │
│  ❌ ANTI-PATTERN 1: KILLER TASK                                  │
│     Task quá lớn (> 20K tokens), bao trùm nhiều features       │
│     → SOLUTION: Decompose thành sub-tasks < 5K tokens mỗi cái  │
│                                                                  │
│  ❌ ANTI-PATTERN 2: AMBIGUOUS TASK                               │
│     Task description mơ hồ: "improve the code"                  │
│     → SOLUTION: Thêm acceptance criteria cụ thể                │
│                                                                  │
│  ❌ ANTI-PATTERN 3: TASK CHURN                                    │
│     Task liên tục thay đổi scope mid-execution                  │
│     → SOLUTION: Lock scope, tạo task mới cho changes            │
│                                                                  │
│  ❌ ANTI-PATTERN 4: DEPENDENCY DEADLOCK                          │
│     Task A chờ B, B chờ A → không bao giờ finish               │
│     → SOLUTION: Cycle detection + forced break                   │
│                                                                  │
│  ❌ ANTI-PATTERN 5: CONTEXT DRIFT                                │
│     Task chạy quá lâu → agent forgets original intent           │
│     → SOLUTION: Timebox tasks, refresh context periodically     │
│                                                                  │
│  ❌ ANTI-PATTERN 6: MISSING ACCEPTANCE CRITERIA                  │
│     Task done nhưng không biết có đúng yêu cầu không           │
│     → SOLUTION: Mỗi task phải có "definition of done"           │
│                                                                  │
│  ❌ ANTI-PATTERN 7: OVER-DECOMPOSITION                           │
│     Chia quá nhỏ → overhead > benefit                           │
│     → SOLUTION: Minimum 500 tokens per sub-task                 │
│                                                                  │
│  ❌ ANTI-PATTERN 8: IGNORING TOKEN BUDGET                        │
│     Task ước lượng 5K tokens nhưng context window chỉ còn 3K   │
│     → SOLUTION: Always check token budget before starting       │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 8.2 Anti-Pattern Detector

```python
class AntiPatternDetector:
    """
    Phát hiện anti-patterns trong task management.
    """
    
    def detect(self, task: Task, context: Dict = None) -> List[Dict]:
        """Kiểm tra task và trả về list anti-patterns"""
        issues = []
        context = context or {}
        
        # Check 1: Killer Task
        if task.estimated_tokens > 20000:
            issues.append({
                "pattern": "KILLER_TASK",
                "severity": "HIGH",
                "message": f"Task estimated {task.estimated_tokens} tokens. "
                          f"Decompose into smaller tasks (< 5K each).",
                "suggestion": "Use TaskDecomposer with 'feature' or 'layer' strategy",
            })
        
        # Check 2: Ambiguous Task
        vague_keywords = [
            "improve", "optimize", "clean", "better",
            "refactor", "make it work", "fix it"
        ]
        if any(kw in task.description.lower() for kw in vague_keywords):
            if not task.verification_criteria:
                issues.append({
                    "pattern": "AMBIGUOUS_TASK",
                    "severity": "MEDIUM",
                    "message": "Task description is vague without clear criteria",
                    "suggestion": "Add specific acceptance criteria and expected outcomes",
                })
        
        # Check 3: Missing Token Estimate
        if task.estimated_tokens == 0:
            issues.append({
                "pattern": "MISSING_ESTIMATE",
                "severity": "LOW",
                "message": "No token estimate provided",
                "suggestion": "Use EffortEstimator to get initial estimate",
            })
        
        # Check 4: Too Many Dependencies
        if len(task.dependencies) > 5:
            issues.append({
                "pattern": "DEPENDENCY_OVERLOAD",
                "severity": "MEDIUM",
                "message": f"Task has {len(task.dependencies)} dependencies",
                "suggestion": "Consider reducing dependencies or splitting task",
            })
        
        # Check 5: No Files Involved (for modification tasks)
        if (task.category == TaskCategory.MODIFICATION 
            and len(task.files_involved) == 0):
            issues.append({
                "pattern": "MISSING_FILE_SCOPE",
                "severity": "MEDIUM",
                "message": "Modification task has no files identified",
                "suggestion": "Scan codebase to identify affected files",
            })
        
        return issues
```

---

## 9. Real-World Workflows

### 9.1 Feature Implementation Workflow

```
┌──────────────────────────────────────────────────────────────────┐
│           FEATURE IMPLEMENTATION WORKFLOW                         │
│                                                                  │
│  Phase 1: UNDERSTAND (5-10 min)                                  │
│  ┌────────────────────────────────────────────────────────┐     │
│  │ 1. Read requirements/spec                              │     │
│  │ 2. Search existing code for similar features           │     │
│  │ 3. Identify affected files and modules                 │     │
│  │ 4. Check for existing patterns to follow               │     │
│  │ 5. Ask clarifying questions if needed                  │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
│  Phase 2: PLAN (5-10 min)                                        │
│  ┌────────────────────────────────────────────────────────┐     │
│  │ 1. Design the approach (which files to change)         │     │
│  │ 2. Identify risks and edge cases                       │     │
│  │ 3. Estimate token budget                               │     │
│  │ 4. Create sub-task list with dependencies              │     │
│  │ 5. Choose decomposition strategy                       │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
│  Phase 3: IMPLEMENT (variable)                                   │
│  ┌────────────────────────────────────────────────────────┐     │
│  │ 1. Start with data models / types                      │     │
│  │ 2. Implement core business logic                       │     │
│  │ 3. Add API/interface layer                             │     │
│  │ 4. Add error handling                                  │     │
│  │ 5. Write tests (TDD if complex)                        │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
│  Phase 4: VERIFY (5-10 min)                                      │
│  ┌────────────────────────────────────────────────────────┐     │
│  │ 1. Run linter (flake8, ruff)                           │     │
│  │ 2. Run type checker (mypy)                             │     │
│  │ 3. Run tests (pytest)                                  │     │
│  │ 4. Check for regressions                               │     │
│  │ 5. Verify acceptance criteria met                      │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 9.2 Debug Investigation Workflow

```
┌──────────────────────────────────────────────────────────────────┐
│             DEBUG INVESTIGATION WORKFLOW                          │
│                                                                  │
│  Step 1: REPRODUCE                                                │
│  ┌────────────────────────────────────────────────────────┐     │
│  │ • Get exact error message and stack trace              │     │
│  │ • Find minimal reproduction steps                      │     │
│  │ • Note environment details (OS, versions, config)      │     │
│  │ • Check if intermittent or consistent                  │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
│  Step 2: INVESTIGATE                                              │
│  ┌────────────────────────────────────────────────────────┐     │
│  │ • Read stack trace → identify entry point              │     │
│  │ • Search for error message in codebase                 │     │
│  │ • Read affected code top-to-bottom                     │     │
│  │ • Check recent git changes (git log --oneline -20)     │     │
│  │ • Add debug logging if needed                          │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
│  Step 3: HYPOTHESIZE                                              │
│  ┌────────────────────────────────────────────────────────┐     │
│  │ • Form hypothesis: "The bug is in X because Y"        │     │
│  │ • Test hypothesis with minimal code change             │     │
│  │ • If wrong, form new hypothesis and repeat             │     │
│  │ • Track hypotheses tried (avoid cycling)               │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
│  Step 4: FIX                                                      │
│  ┌────────────────────────────────────────────────────────┐     │
│  │ • Implement minimal fix (not over-engineer)            │     │
│  │ • Verify fix resolves the original issue               │     │
│  │ • Check fix doesn't break other functionality          │     │
│  │ • Add regression test                                  │     │
│  │ • Document root cause in commit message                │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
│  Step 5: PREVENT                                                  │
│  ┌────────────────────────────────────────────────────────┐     │
│  │ • Add type hints to prevent similar type errors        │     │
│  │ • Add input validation if applicable                   │     │
│  │ • Consider if similar bugs exist elsewhere             │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 9.3 Refactoring Workflow

```
┌──────────────────────────────────────────────────────────────────┐
│              REFACTORING WORKFLOW                                  │
│                                                                  │
│  ⚠️ RULE: Never refactor AND add features at the same time      │
│                                                                  │
│  Step 1: CHARACTERIZE CURRENT BEHAVIOR                           │
│  ┌────────────────────────────────────────────────────────┐     │
│  │ • Ensure existing tests pass                           │     │
│  │ • If no tests → write characterization tests first     │     │
│  │ • Document current behavior (even if wrong)            │     │
│  │ • Take "before" snapshots (metrics, benchmarks)        │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
│  Step 2: IDENTIFY CODE SMELLS                                    │
│  ┌────────────────────────────────────────────────────────┐     │
│  │ • Long methods (> 30 lines)                            │     │
│  │ • Large classes (> 300 lines)                          │     │
│  │ • Duplicate code (> 3 similar blocks)                  │     │
│  │ • Deep nesting (> 3 levels)                            │     │
│  │ • God objects (doing too many things)                  │     │
│  │ • Feature envy (method uses other class's data more)   │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
│  Step 3: PLAN REFACTORING STEPS                                  │
│  ┌────────────────────────────────────────────────────────┐     │
│  │ • Order refactoring steps from least to most risky     │     │
│  │ • Each step should be small and testable               │     │
│  │ • Run tests after EACH step                            │     │
│  │ • Commit after each successful step                    │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
│  Step 4: APPLY REFACTORING (Small Steps)                         │
│  ┌────────────────────────────────────────────────────────┐     │
│  │ • Extract Method / Extract Class                       │     │
│  │ • Rename for clarity                                   │     │
│  │ • Move methods to correct classes                      │     │
│  │ • Replace conditional with polymorphism                │     │
│  │ • Remove dead code                                     │     │
│  │ • Simplify conditional expressions                     │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
│  Step 5: VERIFY                                                   │
│  ┌────────────────────────────────────────────────────────┐     │
│  │ • All tests still pass?                                │     │
│  │ • Behavior unchanged? (characterization tests)         │     │
│  │ • Metrics improved? (LOC, complexity, readability)     │     │
│  │ • No performance regression?                           │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 10. Token Budget Management

### 10.1 Context Window Budget Allocation

```
┌──────────────────────────────────────────────────────────────────┐
│           TOKEN BUDGET ALLOCATION (128K context window)          │
│                                                                  │
│  ┌────────────────────────────────────────────────────────┐     │
│  │  System Prompt (fixed)              │  ~5,000 tokens  │     │
│  │  ───────────────────────────────────┤                  │     │
│  │  Project Context (dynamic)          │  ~30,000 tokens │     │
│  │  • File structure                   │  ~2,000         │     │
│  │  • Key code files                   │  ~20,000        │     │
│  │  • Config files                     │  ~3,000         │     │
│  │  • Documentation                    │  ~5,000         │     │
│  │  ───────────────────────────────────┤                  │     │
│  │  Task Context (dynamic)             │  ~20,000 tokens │     │
│  │  • Task description                 │  ~1,000         │     │
│  │  • Related code                     │  ~15,000        │     │
│  │  • Error logs                       │  ~4,000         │     │
│  │  ───────────────────────────────────┤                  │     │
│  │  Working Memory (growing)           │  ~50,000 tokens │     │
│  │  • Generated code                   │  ~30,000        │     │
│  │  • Intermediate results             │  ~10,000        │     │
│  │  • Tool outputs                     │  ~10,000        │     │
│  │  ───────────────────────────────────┤                  │     │
│  │  Reserve (safety buffer)            │  ~23,000 tokens │     │
│  │  • Response generation              │  ~15,000        │     │
│  │  • Overflow protection              │  ~8,000         │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                  │
│  ⚠️ When budget < 20% remaining:                                │
│     1. Summarize older context                                  │
│     2. Remove non-essential files from context                  │
│     3. Complete current sub-task, then start fresh               │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 10.2 Token Budget Manager

```python
class TokenBudgetManager:
    """
    Quản lý ngân sách token cho task execution.
    Prevents context overflow và optimizes token usage.
    """
    
    def __init__(self, total_budget: int = 128000):
        self.total_budget = total_budget
        self.allocated = {
            "system": 5000,
            "project_context": 0,
            "task_context": 0,
            "working_memory": 0,
        }
        self.min_reserve = 20000
    
    @property
    def remaining(self) -> int:
        used = sum(self.allocated.values())
        return self.total_budget - used - self.min_reserve
    
    @property
    def usage_percent(self) -> float:
        used = sum(self.allocated.values())
        return (used / self.total_budget) * 100
    
    def can_fit(self, estimated_tokens: int) -> bool:
        """Kiểm tra có đủ budget cho task không"""
        return self.remaining >= estimated_tokens
    
    def allocate_for_task(self, task: Task) -> bool:
        """Cấp budget cho task — trả False nếu không đủ"""
        needed = task.estimated_tokens or 2000
        if not self.can_fit(needed):
            return False
        
        self.allocated["task_context"] += needed
        return True
    
    def add_to_working_memory(self, tokens: int):
        """Thêm tokens vào working memory"""
        self.allocated["working_memory"] += tokens
    
    def summarize_context(self) -> str:
        """Tóm tắt tình trạng budget"""
        lines = [
            "=== Token Budget Status ===",
            f"Total:     {self.total_budget:,}",
            f"System:    {self.allocated['system']:,}",
            f"Project:   {self.allocated['project_context']:,}",
            f"Task:      {self.allocated['task_context']:,}",
            f"Working:   {self.allocated['working_memory']:,}",
            f"Reserve:   {self.min_reserve:,}",
            f"Remaining: {self.remaining:,}",
            f"Usage:     {self.usage_percent:.1f}%",
        ]
        
        if self.usage_percent > 80:
            lines.append("⚠️ WARNING: High context usage!")
        if self.remaining < 5000:
            lines.append("🚨 CRITICAL: Very low remaining budget!")
        
        return "\n".join(lines)
    
    def suggest_compaction(self) -> List[str]:
        """Gợi ý cách giảm token usage"""
        suggestions = []
        
        if self.allocated["working_memory"] > 30000:
            suggestions.append(
                "Summarize older working memory entries"
            )
        
        if self.allocated["project_context"] > 20000:
            suggestions.append(
                "Remove non-essential project files from context"
            )
        
        if self.usage_percent > 85:
            suggestions.append(
                "Complete current sub-task and start new session"
            )
        
        return suggestions
```

---

## Best Practices

```
┌──────────────────────────────────────────────────────────────────┐
│                TASK MANAGEMENT BEST PRACTICES                     │
│                                                                  │
│  1. ONE TASK = ONE RESPONSIBILITY                                │
│     Mỗi task chỉ làm 1 việc rõ ràng                           │
│     → Dễ estimate, dễ track, dễ review                         │
│                                                                  │
│  2. SIZE MATTERS                                                  │
│     Task 100-5000 tokens là lý tưởng                            │
│     Quá nhỏ → overhead, quá lớn → dễ fail                      │
│                                                                  │
│  3. VISIBLE STATE                                                 │
│     Luôn biết task đang ở trạng thái gì                         │
│     → State machine + logging                                    │
│                                                                  │
│  4. EXPLICIT DEPENDENCIES                                        │
│     Ghi rõ task nào phụ thuộc task nào                         │
│     → DAG + topological sort                                     │
│                                                                  │
│  5. ESTIMATE & TRACK TOKENS                                      │
│     Theo dõi token usage per task                                │
│     → Budget awareness, avoid context overflow                   │
│                                                                  │
│  6. TEMPLATE COMMON PATTERNS                                     │
│     Tạo template cho task type thường gặp                       │
│     → Tiết kiệm thời gian, đảm bảo consistency                  │
│                                                                  │
│  7. FAIL GRACEFULLY                                               │
│     Task fail → log rõ lý do, retry hoặc skip                  │
│     → Không block toàn bộ pipeline                              │
│                                                                  │
│  8. USE VERIFICATION CRITERIA                                    │
│     Mỗi sub-task phải có "definition of done"                   │
│     → Rõ ràng khi task hoàn thành                               │
│                                                                  │
│  9. DETECT ANTI-PATTERNS EARLY                                   │
│     Check killer tasks, ambiguous tasks, dependency overload    │
│     → Fix before starting execution                              │
│                                                                  │
│  10. CHOOSE RIGHT DECOMPOSITION STRATEGY                         │
│      Auto-detect based on task type and complexity              │
│      → Feature, Layer, File, TDD, Vertical Slice, Spike         │
│                                                                  │
│  11. MANAGE TOKEN BUDGET                                         │
│      Check remaining budget before each sub-task                │
│      → Prevent context overflow, plan session breaks            │
│                                                                  │
│  12. FOLLOW WORKFLOW PATTERNS                                     │
│      Feature → Understand, Plan, Implement, Verify              │
│      Debug → Reproduce, Investigate, Hypothesize, Fix           │
│      Refactor → Characterize, Identify, Plan, Apply, Verify     │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## Tài Liệu Tham Khảo

- [LangGraph State Management](https://langchain-ai.github.io/langgraph/)
- [CrewAI Task Management](https://docs.crewai.com/)
- [Eisenhower Matrix](https://www.mindtools.com/pages/article/newHTE_H.htm)
- [Martin Fowler - Refactoring](https://refactoring.com/)
- [Google Engineering Practices](https://google.github.io/eng-practices/review/)
- [Token Estimation - tiktoken](https://github.com/openai/tiktoken)