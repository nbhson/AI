# 📋 VIII. Task

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
└──────────────────────────────────────────────────────────────────┘
```

### 2.2 Task Decomposer

```python
from typing import List, Dict, Optional
from dataclasses import dataclass

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


class TaskDecomposer:
    """
    Phân rã task lớn thành các sub-task có thể thực hiện riêng.
    
    Strategies:
    - Feature-based: Chia theo tính năng
    - Layer-based: Chia theo layer (UI, API, DB)
    - File-based: Chia theo file
    - Test-driven: Viết test trước, rồi code
    """
    
    def decompose(self, task: Task, strategy: str = "auto",
                  project_context: Dict = None) -> List[SubTask]:
        """Phân rã task thành sub-tasks"""
        
        if strategy == "auto":
            strategy = self._suggest_strategy(task)
        
        if strategy == "feature":
            return self._decompose_by_feature(task)
        elif strategy == "layer":
            return self._decompose_by_layer(task)
        elif strategy == "file":
            return self._decompose_by_file(task)
        elif strategy == "tdd":
            return self._decompose_tdd(task)
        else:
            return self._decompose_by_feature(task)
    
    def _suggest_strategy(self, task: Task) -> str:
        """Gợi ý strategy phù hợp"""
        if task.complexity.value <= 2:
            return "simple"
        if "test" in task.description.lower():
            return "tdd"
        if task.category == TaskCategory.GENERATION:
            return "feature"
        if task.category == TaskCategory.MODIFICATION:
            return "file"
        return "feature"
    
    def _decompose_by_feature(self, task: Task) -> List[SubTask]:
        """Chia theo tính năng"""
        subtasks = []
        
        # Step 1: Understand requirements
        subtasks.append(SubTask(
            id=f"{task.id}-01",
            title="Phân tích yêu cầu",
            description=f"Đọc và hiểu yêu cầu: {task.description}",
            order=1,
            estimated_tokens=500,
        ))
        
        # Step 2: Read existing code
        subtasks.append(SubTask(
            id=f"{task.id}-02",
            title="Đọc code hiện tại",
            description="Xem codebase liên quan để hiểu context",
            order=2,
            dependencies=[f"{task.id}-01"],
            estimated_tokens=1000,
        ))
        
        # Step 3: Plan changes
        subtasks.append(SubTask(
            id=f"{task.id}-03",
            title="Lên kế hoạch thay đổi",
            description="Xác định files cần sửa và cách tiếp cận",
            order=3,
            dependencies=[f"{task.id}-02"],
            estimated_tokens=500,
        ))
        
        # Step 4: Implement
        subtasks.append(SubTask(
            id=f"{task.id}-04",
            title="Triển khai code",
            description="Viết code theo kế hoạch",
            order=4,
            dependencies=[f"{task.id}-03"],
            estimated_tokens=task.estimated_tokens,
        ))
        
        # Step 5: Test
        subtasks.append(SubTask(
            id=f"{task.id}-05",
            title="Kiểm tra & validate",
            description="Chạy test, lint, review kết quả",
            order=5,
            dependencies=[f"{task.id}-04"],
            estimated_tokens=500,
        ))
        
        return subtasks
    
    def _decompose_by_layer(self, task: Task) -> List[SubTask]:
        """Chia theo layer: UI → API → Service → DB"""
        return [
            SubTask(f"{task.id}-db", "Database layer",
                    "Thay đổi schema, queries", 1),
            SubTask(f"{task.id}-svc", "Service layer",
                    "Business logic", 2,
                    dependencies=[f"{task.id}-db"]),
            SubTask(f"{task.id}-api", "API layer",
                    "Endpoints, validation", 3,
                    dependencies=[f"{task.id}-svc"]),
            SubTask(f"{task.id}-ui", "UI layer",
                    "Frontend components", 4,
                    dependencies=[f"{task.id}-api"]),
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
                parallel=True,  # Có thể song song nếu không phụ thuộc
            ))
        return subtasks
    
    def _decompose_tdd(self, task: Task) -> List[SubTask]:
        """Test-Driven Development decomposition"""
        return [
            SubTask(f"{task.id}-test", "Viết test cases",
                    "Viết test trước khi code", 1),
            SubTask(f"{task.id}-impl", "Triển khai code",
                    "Viết code để pass test", 2,
                    dependencies=[f"{task.id}-test"]),
            SubTask(f"{task.id}-refactor", "Refactor",
                    "Cải thiện code quality", 3,
                    dependencies=[f"{task.id}-impl"]),
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
    """Score tính toán cho priority"""
    urgency: int        # 1-5: mức độ khẩn cấp
    importance: int     # 1-5: mức độ quan trọng
    effort: int         # 1-5: effort cần thiết (thấp = score cao)
    dependency_count: int  # Số tasks phụ thuộc vào task này
    
    @property
    def total(self) -> float:
        """Eisenhower Matrix + dependency bonus"""
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
        # Estimate from complexity
        effort = task.complexity.value
        
        # Urgency from priority (lower number = more urgent)
        urgency = max(1, 6 - task.priority)
        
        # Importance from category
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
            # Lấy tất cả tasks có in_degree = 0
            ready = [t for t, d in remaining.items() if d == 0]
            if not ready:
                raise ValueError("Unresolvable dependency cycle")
            
            groups.append(ready)
            
            # Giảm in_degree cho neighbors
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
│     Task 100-1000 tokens là lý tưởng                            │
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
│     → Budget awareness,避免 context overflow                     │
│                                                                  │
│  6. TEMPLATE COMMON PATTERNS                                     │
│     Tạo template cho task type thường gặp                       │
│     → Tiết kiệm thời gian, đảm bảo consistency                  │
│                                                                  │
│  7. FAIL GRACEFULLY                                               │
│     Task fail → log rõ lý do, retry hoặc skip                  │
│     → Không block toàn bộ pipeline                              │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## Tài Liệu Tham Khảo

- [LangGraph State Management](https://langchain-ai.github.io/langgraph/)
- [CrewAI Task Management](https://docs.crewai.com/)
- [Eisenhower Matrix](https://www.mindtools.com/pages/article/newHTE_H.htm)