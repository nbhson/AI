# 📋 IV. Plan & Decompose Task

> **"Mỗi khi agent mắc lỗi phân task, bạn không sửa prompt – bạn xây dựng hệ thống planning ngăn chặn lỗi đó tái diễn"**
> — Mitchell Hashimoto (Applied to Task Planning)

### Tại Sao Plan & Decompose Task Quan Trọng?

> *"A goal without a plan is just a wish. And an AI without decomposition is just a chatbot."*

#### Bằng chứng nghiên cứu:

1. **Microsoft Research (2025)**: AI agents thực hiện complex tasks **without planning** chỉ đạt **32% success rate**, trong khi agents có **structured decomposition** đạt **78%** — improvement 2.4×.
2. **Stanford HAI (2024)**: Task decomposition giảm **60% token usage** cho complex tasks vì agent không "lạc đề" giữa các subtask.
3. **Anthropic (2025)**: Claude Code với task planning resolve **44% more SWE-bench issues** so với prompt-only approach — planning là difference giữa "agent" và "chatbot".

#### Triết lý cốt lõi:

```
Plan & Decompose = Analyze → Prioritize → Sequence → Execute → Validate
```

**5 Levels của Task Planning**:
- **Level 1**: Break task into subtasks (decomposition)
- **Level 2**: Sequence subtasks theo dependency (ordering)
- **Level 3**: Estimate effort cho mỗi subtask (estimation)
- **Level 4**: Identify risks và fallback plans (risk assessment)
- **Level 5**: Monitor progress và replan khi cần (adaptive planning)

**Analogies**: Plan & Decompose giống GPS navigation — không chỉ cho biết đích đến (goal), mà còn phân tích đường đi (decompose), chọn tuyến tối ưu (prioritize), tính thời gian (estimate), và reroute khi có traffic (replan). Without GPS, bạn có thể lái xe cả ngày mà không đến nơi.

**Nếu bỏ qua**: Agent cố gắng làm mọi thứ cùng lúc → context overload, hallucinate khi lacking structure, tạo code không consistent, và cuối cùng浪费 3-5× token so với planned approach.

## Tổng Quan

Khi đối mặt task phức tạp, AI Agent cần **phân tích → lập kế hoạch → chia nhỏ → thực hiện tuần tự**. Đây là kỹ năng cốt lõi biến LLM từ "chatbot" thành "agent".

Trong hệ thống Harness Engineering, Planning & Decomposition là **"bộ não điều khiển"** — quyết định tất cả các thành phần khác (tools, memory, guardrails) được sử dụng như thế nào.

```
┌──────────────────────────────────────────────────────────────────┐
│                  PLAN & DECOMPOSE TASK                            │
│                                                                  │
│  Complex Task                                                    │
│  "Triển khai hệ thống BHYT cho công ty 100 người"              │
│       │                                                          │
│       ▼                                                          │
│  ┌──────────────┐                                               │
│  │  PLANNING    │  Phân tích → Chia task → Đánh giá thứ tự     │
│  └──────┬───────┘                                               │
│         ▼                                                        │
│  ┌──────────────┐                                               │
│  │ DECOMPOSE    │  Task lớn → Sub-tasks → Sub-sub-tasks        │
│  └──────┬───────┘                                               │
│         ▼                                                        │
│  ┌──────────────┐                                               │
│  │  EXECUTE     │  Sub-task 1 → 2 → 3 → ... → Done            │
│  └──────┬───────┘                                               │
│         ▼                                                        │
│  ┌──────────────┐                                               │
│  │  VALIDATE    │  Kiểm tra kết quả → Re-plan nếu cần         │
│  └──────────────┘                                               │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │  HARNESS INTEGRATION                                       │  │
│  │  Tools ←→ Memory ←→ Guardrails ←→ Feedback ←→ Permissions │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## Tại Sao Planning & Decomposition Quan Trọng?

> *"Một agent không có planning giống như một người lái xe không có bản đồ — có thể di chuyển, nhưng chắc chắn sẽ lạc đường."*

### Triết Lý Cốt Lõi

LLMs có khả năng "nói" rất giỏi, nhưng khi đối mặt task phức tạp như **"triển khai hệ thống BHYT cho công ty 100 người"**, chúng thường mắc một trong hai lỗi phổ biến:

**Lỗi 1: Oversimplification** — Agent nhảy thẳng vào code mà không plan → Implement sai requirements → Phải làm lại từ đầu.

**Lỗi 2: Analysis Paralysis** — Agent phân tích quá nhiều mà không bao giờ bắt tay vào làm → User chờ đợi vô tận.

**Giải pháp**: Structured Planning — một hệ thống có framework rõ ràng cho phép agent **nghĩ trước khi làm**, nhưng vẫn **bắt tay vào làm trong thời gian hợp lý**.

### Bằng Chứng Nghiên Cûu

#### LangChain (2025): "ReAct vs Plan-and-Execute"
> Agents sử dụng **Plan-and-Execute pattern** hoàn thành tasks phức tạp với **40% ít hơn retries** so với ReAct (reactive) pattern.

Nguyên nhân: Plan trước → biết cần tool nào → giảm false starts → ít hơn một nửa số tool calls không cần thiết.

#### Microsoft Research (2024): "Task Decomposition in LLM Agents"
> Khi task được chia thành sub-tasks có kích thước **3-7 items** mỗi nhóm, accuracy tăng **52%** so với monolithic task.

**Quy tắc 7±2**: Tương tự Miller's Law trong tâm lý học — con người (và cả LLM) xử lý hiệu quả nhất khi có 5-9 items trong working memory.

#### Devin AI & OpenHands (2025)
> Top coding agents đều sử dụng **hierarchical decomposition**: Task → Epic → Story → Sub-task. Tốc độ hoàn thành **2.8x** so với flat task list.

### Cost-Benefit Analysis

| Chi Phí / Giá Trị | Không Có Planning | Có Planning + Decomposition |
|---|---|---|
| **Retry Rate** | 40-60% tasks cần retry | 10-15% tasks cần retry |
| **Tool Calls** | 3-5x redundant calls | Gần như minimal calls |
| **Token Usage** | Cao (phải re-process context) | Thấp hơn 30-50% |
| **Time to Complete** | Unpredictable, thường chậm | Predictable, typically faster |
| **User Trust** | Thấp vì kết quả bất ngờ | Cao vì có visibility vào plan |

**ROI**: Đầu tư 15-30 giây ban đầu cho planning → Tiết kiệm 5-15 phút retry. Tỷ lệ **1:20**.

### Analogies Minh Họa

**Analogies 1: Thợ Xây và Bản Vẽ**
- **Không planning** = Thợ xây bắt tay ngay vào tường → Cột lệch, mái nghiêng → Phải đập bỏ làm lại
- **Có planning** = Thợ xây đo đạc, vẽ bản vẽ, kiểm tra nền → Xây đúng lần đầu
- **Decomposition** = Bản vẽ lớn → Bản vẽ chi tiết từng tầng → Bản vẽ từng phòng

**Analogies 2: Bếp and Menu**
- **Không planning** = Đầu bếp nấu ngẫu nhiên từ nguyên liệu có sẵn → Món ăn hỗn tạp
- **Có planning** = Đầu bếp lên menu → Phân công: người rửa, người cắt, người nấu → Món ra đúng giờ
- **Decomposition** = Menu lớn → Courses → Dishes → Ingredients + Steps

**Analogies 3: Dự án Software**
- **Không planning** = Developer code ngay không cần spec → Feature creep, bugs
- **Có planning** = Sprint planning → User stories → Tasks → Code
- **Decomposition** = Epic → Feature → Story → Sub-task → Code

### Evolutionary Context

```
┌──────────────────────────────────────────────────────────────────┐
│              EVOLUTION OF AGENT PLANNING                          │
│                                                                  │
│  2022-2023: No Planning (Pure ReAct)                            │
│  └── Agent: Think → Act → Observe → Think → Act...              │
│      Vấn đề: Không biết tổng thể, hay lạc hướng                 │
│                                                                  │
│  2023-2024: Chain-of-Thought (CoT)                              │
│  └── Agent: Think step-by-step before acting                    │
│      Vấn đề: CoT linear, không branch/replan                    │
│                                                                  │
│  2024-2025: Plan-and-Execute                                    │
│  └── Agent: Create full plan → Execute step-by-step             │
│      Vấn đề: Plan cứng nhắc, không adapt khi thay đổi          │
│                                                                  │
│  2026+: Adaptive Hierarchical Planning                          │
│  └── Agent: Plan → Execute → Monitor → Re-plan dynamically      │
│      + Hierarchical decomposition (Task → Epic → Story)         │
│      + Dependency graph + Priority ordering                     │
│      + Automatic replanning when subtask fails                  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### Nếu Bạn Bỏ Qua Planning...

**1. Infinite Loop (Vòng Lặp Vô Tận)**
- Agent thực hiện task A → B → A → B → ... không bao giờ hoàn thành
- Thiếu termination conditions rõ ràng trong plan

**2. Scope Creep (Mở Rộng Phạm Vi)**
- Agent thêm tính năng không cần thiết → Token waste, time waste
- Ví dụ: "Viết hello world" → Agent thêm authentication, logging, i18n...

**3. Wrong Tool Selection**
- Không plan trước → Chọn sai tool cho mỗi step → Phải undo và làm lại
- Ví dụ: Dùng search khi cần write, dùng write khi cần verify

**4. Missing Steps**
- Agent quên steps quan trọng → Kết quả incomplete
- Ví dụ: Deploy code mà không test, không lint, không commit

**5. Cascading Errors**
- Sub-task 1 sai → Sub-task 2-5 đều sai theo → Phải làm lại toàn bộ
- Thiếu validation checkpoints giữa các sub-tasks

### Best Practices (Và Tại Sao)

| Rule | Why |
|---|---|
| Luôn decompose task > 3 steps thành sub-tasks |LLM context window hạn chế, 1 task quá lớn dễ lose focus |
| Đặt termination condition cho mỗi sub-task | Ngăn infinite loops, biết khi nào "done" |
| Thêm validation checkpoint sau mỗi major step | Catch errors early, không để cascade |
| Đánh giá dependency trước khi execute | Task A cần完成 trước Task B → execute tuần tự |
| Limit plan depth ≤ 4 levels | Quá sâu dễ lose context tổng thể |
| Re-plan khi sub-task fail | Thay vì retry盲目, phân tích root cause và adjust plan |

---

## Nội Dung

| # | Chủ đề | Mô tả |
|---|--------|-------|
| 1 | [Task Decomposition Patterns](#1-task-decomposition-patterns) | Các mô hình chia task |
| 2 | [Planning Algorithms](#2-planning-algorithms) | Thuật toán lập kế hoạch |
| 3 | [Agent Workflows](#3-agent-workflows) | Các kiểu workflow của agent |
| 4 | [State Management](#4-state-management) | Quản lý trạng thái |
| 5 | [ReAct Pattern](#5-react-pattern) | Reasoning + Acting |
| 6 | [Harness-Integrated Planning](#6-harness-integrated-planning) | Tích hợp planning với harness |
| 7 | [Case Studies Thực Tế](#7-case-studies-thực-tế) | SWE-agent, Anthropic, Claude Code |
| 8 | [Design Principles](#8-design-principles) | Nguyên tắc thiết kế |
| 9 | [Best Practices](#9-best-practices) | DO/DON'T chi tiết |
| 10 | [Testing Planning Systems](#10-testing-planning-systems) | Kiểm thử hệ thống planning |
| 11 | [Advanced Patterns](#11-advanced-patterns) | Hierarchical, Self-reflective |
| 12 | [Tools & Frameworks](#12-tools--frameworks) | LangGraph, AutoGen, CrewAI |
| 13 | [Tương Lai](#13-tương-lai) | Xu hướng 2026-2028 |

---

## 1. Task Decomposition Patterns

### 1.1 Các Mô Hình Phân Chia

```
┌──────────────────────────────────────────────────────────────────┐
│              TASK DECOMPOSITION PATTERNS                          │
│                                                                  │
│  1. SEQUENTIAL (Tuần tự)                                        │
│  ┌────┐   ┌────┐   ┌────┐   ┌────┐                            │
│  │ T1 │──►│ T2 │──►│ T3 │──►│ T4 │                            │
│  └────┘   └────┘   └────┘   └────┘                            │
│  Input T2 = Output T1                                          │
│                                                                  │
│  2. PARALLEL (Song song)                                        │
│  ┌────┐                                                        │
│  │ T1 │──┐                                                     │
│  └────┘  │  ┌────┐   ┌────┐                                   │
│  ┌────┐  ├─►│Merge│──►│Final│                                  │
│  │ T2 │──┤  └────┘   └────┘                                   │
│  └────┘  │                                                     │
│  ┌────┐  │                                                     │
│  │ T3 │──┘                                                     │
│  └────┘                                                        │
│                                                                  │
│  3. CONDITIONAL (Có điều kiện)                                  │
│  ┌────┐                                                        │
│  │ Q  │──┐                                                     │
│  └────┘  │                                                     │
│          ├── YES ──►┌─────┐                                    │
│          │          │ T_A │                                    │
│          │          └─────┘                                    │
│          └── NO  ──►┌─────┐                                    │
│                     │ T_B │                                    │
│                     └─────┘                                    │
│                                                                  │
│  4. HIERARCHICAL (Phân cấp)                                    │
│           ┌────────┐                                            │
│           │ Task   │                                            │
│           └───┬────┘                                            │
│          ┌────┼────┐                                            │
│       ┌──┴──┐ ┌──┴──┐ ┌──┴──┐                                 │
│       │Sub1 │ │Sub2 │ │Sub3 │                                 │
│       └──┬──┘ └─────┘ └─────┘                                 │
│       ┌──┴──┐                                                   │
│       │Sub1a│                                                   │
│       └─────┘                                                   │
│                                                                  │
│  5. ITERATIVE (Lặp lại)                                        │
│  ┌────────┐                                                    │
│  │Implement│◄──┐                                               │
│  └────┬───┘   │                                               │
│       ▼       │                                               │
│  ┌────────┐   │                                               │
│  │Evaluate │──►│  Until: pass criteria                         │
│  └────────┘   │                                               │
│       │       │                                               │
│       └───────┘                                               │
│                                                                  │
│  6. DAG (Directed Acyclic Graph)                                │
│       ┌────┐                                                    │
│       │ T1 │──┐                                                 │
│       └────┘  │  ┌────┐                                        │
│               ├──│ T3 │──┐                                     │
│       ┌────┐  │  └────┘  │  ┌────┐                            │
│       │ T2 │──┘           ├─│ T5 │──►Done                      │
│       └────┘              │  └────┘                            │
│       ┌────┐  │  ┌────┐  │                                     │
│       │ T4 │──┘  │ T6 │──┘                                     │
│       └────┘     └────┘                                        │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 1.2 Implementation

```python
from enum import Enum
from typing import List, Dict, Any, Optional
from dataclasses import dataclass, field
from datetime import datetime

class TaskStatus(Enum):
    PENDING = "pending"
    IN_PROGRESS = "in_progress"
    COMPLETED = "completed"
    FAILED = "failed"
    BLOCKED = "blocked"
    CANCELLED = "cancelled"

class TaskType(Enum):
    SEQUENTIAL = "sequential"
    PARALLEL = "parallel"
    CONDITIONAL = "conditional"
    HIERARCHICAL = "hierarchical"
    ITERATIVE = "iterative"
    DAG = "dag"

@dataclass
class Task:
    id: str
    name: str
    description: str
    task_type: TaskType = TaskType.SEQUENTIAL
    status: TaskStatus = TaskStatus.PENDING
    subtasks: List['Task'] = field(default_factory=list)
    dependencies: List[str] = field(default_factory=list)
    result: Any = None
    error: Optional[str] = None
    metadata: Dict = field(default_factory=dict)
    max_retries: int = 3
    retry_count: int = 0
    timeout_seconds: int = 300
    created_at: str = field(default_factory=lambda: datetime.now().isoformat())
    completed_at: Optional[str] = None
    token_budget: Optional[int] = None  # Token limit cho task này
    tools_allowed: List[str] = field(default_factory=list)  # Tools được phép dùng
    
    def to_dict(self):
        return {
            "id": self.id,
            "name": self.name,
            "description": self.description,
            "status": self.status.value,
            "type": self.task_type.value,
            "subtasks": [s.to_dict() for s in self.subtasks],
            "dependencies": self.dependencies,
            "retry_count": self.retry_count,
            "token_budget": self.token_budget,
            "created_at": self.created_at,
            "completed_at": self.completed_at,
        }
    
    def mark_completed(self, result):
        self.status = TaskStatus.COMPLETED
        self.result = result
        self.completed_at = datetime.now().isoformat()
    
    def mark_failed(self, error):
        self.status = TaskStatus.FAILED
        self.error = error
    
    def can_retry(self):
        return self.retry_count < self.max_retries
    
    def can_execute(self, completed_tasks: set):
        """Check if all dependencies are satisfied"""
        return all(dep in completed_tasks for dep in self.dependencies)

class TaskPlanner:
    """
    LLM-based task planner
    
    Phân tích task lớn và chia thành sub-tasks
    Hỗ trợ: hierarchical decomposition, dependency detection, complexity estimation
    """
    
    def __init__(self, llm_func=None):
        self.llm = llm_func
    
    def decompose(self, task_description, max_depth=3, current_depth=0):
        """
        Phân tích task và chia thành sub-tasks
        
        Strategy:
        - depth 0-1: Broad decomposition (major phases)
        - depth 2+: Fine-grained decomposition (specific actions)
        """
        if current_depth >= max_depth:
            return [Task(
                id=f"leaf_{current_depth}",
                name=task_description[:50],
                description=task_description,
            )]
        
        if self.llm:
            prompt = f"""Phân tích task sau và chia thành các sub-tasks:

Task: {task_description}
Depth: {current_depth}/{max_depth}

Quy tắc:
- Mỗi sub-task phải cụ thể, có thể thực hiện được
- Xác định dependencies giữa các sub-tasks
- Đánh giá loại (sequential/parallel/conditional)
- Ước tính token budget cho mỗi sub-task
- Liệt kê tools cần thiết cho mỗi sub-task

Output JSON:
{{
  "analysis": "Phân tích task",
  "subtasks": [
    {{
      "name": "...",
      "description": "...",
      "type": "sequential|parallel|conditional",
      "dependencies": [],
      "estimated_tokens": 5000,
      "required_tools": ["tool1", "tool2"]
    }}
  ],
  "estimated_steps": 3
}}"""
            
            response = self.llm(prompt)
            try:
                import json
                data = json.loads(response)
                tasks = []
                for i, st in enumerate(data.get("subtasks", [])):
                    task_type_map = {
                        "sequential": TaskType.SEQUENTIAL,
                        "parallel": TaskType.PARALLEL,
                        "conditional": TaskType.CONDITIONAL,
                    }
                    task = Task(
                        id=f"task_{current_depth}_{i}",
                        name=st["name"],
                        description=st["description"],
                        task_type=task_type_map.get(st.get("type", "sequential"), TaskType.SEQUENTIAL),
                        dependencies=st.get("dependencies", []),
                        token_budget=st.get("estimated_tokens"),
                        tools_allowed=st.get("required_tools", []),
                    )
                    tasks.append(task)
                return tasks
            except (json.JSONDecodeError, KeyError):
                pass
        
        # Fallback: simple decomposition
        return [Task(
            id=f"task_{current_depth}_0",
            name=task_description[:50],
            description=task_description,
        )]
    
    def create_execution_plan(self, root_task):
        """
        Tạo execution plan từ task tree
        
        Returns: Ordered list of tasks to execute with dependency info
        """
        plan = []
        self._traverse(root_task, plan)
        return plan
    
    def _traverse(self, task, plan):
        """DFS traversal to create execution order"""
        if task.task_type == TaskType.SEQUENTIAL:
            plan.append(task)
            for subtask in task.subtasks:
                self._traverse(subtask, plan)
        
        elif task.task_type == TaskType.PARALLEL:
            plan.append(task)  # Parallel marker
            for subtask in task.subtasks:
                self._traverse(subtask, plan)
        
        elif task.task_type == TaskType.ITERATIVE:
            task.metadata["max_iterations"] = 5
            plan.append(task)
            for subtask in task.subtasks:
                self._traverse(subtask, plan)
        
        else:
            plan.append(task)
            for subtask in task.subtasks:
                self._traverse(subtask, plan)
    
    def estimate_complexity(self, task):
        """
        Đánh giá độ phức tạp của task
        
        Returns: Score 1-10 with detailed breakdown
        """
        depth = self._get_depth(task)
        breadth = self._get_breadth(task)
        
        # Advanced scoring
        score = min(10, depth * 2 + breadth)
        
        # Token estimation
        estimated_tokens = self._estimate_tokens(task)
        
        # Recommended strategy
        if score <= 3:
            strategy = "direct"
            reasoning = "Task đơn giản, execute trực tiếp"
        elif score <= 6:
            strategy = "sequential"
            reasoning = "Task trung bình, chia theo thứ tự"
        else:
            strategy = "hierarchical"
            reasoning = "Task phức tạp, cần phân cấp nhiều tầng"
        
        return {
            "score": score,
            "depth": depth,
            "total_subtasks": breadth,
            "complexity": "simple" if score <= 3 else "medium" if score <= 6 else "complex",
            "estimated_tokens": estimated_tokens,
            "recommended_strategy": strategy,
            "reasoning": reasoning,
        }
    
    def _get_depth(self, task, current=0):
        if not task.subtasks:
            return current
        return max(self._get_depth(s, current + 1) for s in task.subtasks)
    
    def _get_breadth(self, task):
        count = 1
        for sub in task.subtasks:
            count += self._get_breadth(sub)
        return count
    
    def _estimate_tokens(self, task):
        """Ước tính số token cần thiết"""
        base_tokens = len(task.description.split()) * 2  # Rough estimate
        for sub in task.subtasks:
            base_tokens += self._estimate_tokens(sub)
        return base_tokens
```

### 1.3 So Sánh Các Pattern

```
┌──────────────────┬──────────┬──────────┬────────────────┬──────────────┐
│ Pattern          │ Speed    │ Quality  │ Token Cost     │ Best For     │
├──────────────────┼──────────┼──────────┼────────────────┼──────────────┤
│ Sequential       │ ⭐⭐⭐⭐⭐  │ ⭐⭐⭐      │ Low            │ Simple tasks │
│ Parallel         │ ⭐⭐⭐⭐   │ ⭐⭐⭐      │ Medium (gộp)   │ Independent  │
│ Conditional      │ ⭐⭐⭐    │ ⭐⭐⭐⭐    │ Low-Medium     │ Dynamic      │
│ Hierarchical     │ ⭐⭐      │ ⭐⭐⭐⭐⭐  │ Medium-High    │ Complex      │
│ Iterative        │ ⭐       │ ⭐⭐⭐⭐⭐  │ High (lặp)     │ Quality-first│
│ DAG              │ ⭐⭐⭐    │ ⭐⭐⭐⭐⭐  │ Medium         │ Dependencies │
└──────────────────┴──────────┴──────────┴────────────────┴──────────────┘
```

---

## 2. Planning Algorithms

### 2.1 LLM-Based Planning (Plan-and-Solve)

```python
class PlanAndSolve:
    """
    Plan-and-Solve prompting strategy
    
    Based on paper: "Plan-and-Solve Prompting" (Wang et al., 2023)
    
    1. Plan: Break down the problem into sub-problems
    2. Solve: Execute each sub-problem
    3. Verify: Check results against original problem
    4. Re-plan: If verification fails, create new plan
    """
    
    def __init__(self, llm_func=None):
        self.llm = llm_func
        self.max_replan_attempts = 3
    
    def plan(self, problem):
        """Step 1: Create a plan"""
        prompt = f"""Hãy lập kế hoạch giải quyết vấn đề sau. 
Đưa ra các bước cụ thể, rõ ràng.

Vấn đề: {problem}

Kế hoạch (mỗi bước trên 1 dòng, bắt đầu bằng số):
1. ...
2. ...
3. ..."""
        
        if self.llm:
            response = self.llm(prompt)
            steps = [line.strip() for line in response.split('\n') 
                    if line.strip() and line.strip()[0].isdigit()]
            return steps
        
        return [f"Bước 1: Phân tích {problem}", "Bước 2: Thực hiện"]
    
    def solve_step(self, step, context=""):
        """Step 2: Solve a single step"""
        prompt = f"""Thực hiện bước sau:

Bước: {step}
Context: {context}

Kết quả:"""
        
        if self.llm:
            return self.llm(prompt)
        return f"Kết quả cho: {step}"
    
    def verify(self, problem, solution):
        """Step 3: Verify solution"""
        prompt = f"""Kiểm tra giải pháp cho vấn đề:

Vấn đề: {problem}
Giải pháp: {solution}

Đánh giá (JSON):
{{
  "complete": true/false,
  "correct": true/false,
  "issues": ["issue1", "issue2"],
  "suggestions": ["suggestion1"]
}}"""
        
        if self.llm:
            response = self.llm(prompt)
            try:
                import json
                return json.loads(response)
            except json.JSONDecodeError:
                pass
        
        return {"complete": True, "correct": True, "issues": [], "suggestions": []}
    
    def replan(self, problem, original_plan, failed_step, error):
        """Create a new plan based on failure"""
        prompt = f"""Kế hoạch ban đầu thất bại. Hãy tạo kế hoạch mới.

Vấn đề: {problem}
Kế hoạch cũ: {original_plan}
Bước thất bại: {failed_step}
Lỗi: {error}

Kế hoạch mới (mỗi bước trên 1 dòng):
"""
        if self.llm:
            response = self.llm(prompt)
            steps = [line.strip() for line in response.split('\n') 
                    if line.strip() and line.strip()[0].isdigit()]
            return steps
        
        return [f"Fix: {failed_step}"]
    
    def run(self, problem):
        """Execute full plan-and-solve cycle with re-planning"""
        plan = self.plan(problem)
        
        for attempt in range(self.max_replan_attempts):
            results = []
            context = ""
            all_succeeded = True
            
            for i, step in enumerate(plan):
                result = self.solve_step(step, context)
                results.append({"step": step, "result": result})
                context += f"\nBước {i+1} đã hoàn thành: {result[:200]}"
            
            # Verify
            verification = self.verify(problem, "\n".join(
                f"{r['step']}: {r['result']}" for r in results
            ))
            
            if verification.get("complete", True) and verification.get("correct", True):
                return {
                    "plan": plan,
                    "results": results,
                    "verification": verification,
                    "attempts": attempt + 1,
                }
            
            # Re-plan based on issues
            if verification.get("issues"):
                plan = self.replan(
                    problem, plan, 
                    verification["issues"][0],
                    str(verification.get("suggestions", []))
                )
        
        return {
            "plan": plan,
            "results": results,
            "verification": verification,
            "attempts": self.max_replan_attempts,
            "status": "max_attempts_reached",
        }
```

### 2.2 Tree of Thoughts (ToT)

```python
class TreeOfThoughts:
    """
    Tree of Thoughts: Explore multiple reasoning paths
    
    Better than linear chain-of-thought for complex problems
    Based on paper: "Tree of Thoughts" (Yao et al., 2023)
    
    Key insight: Not all reasoning paths are equal.
    ToT explores multiple branches and evaluates which path is most promising.
    """
    
    def __init__(self, llm_func=None, branching_factor=3, max_depth=3):
        self.llm = llm_func
        self.branching_factor = branching_factor
        self.max_depth = max_depth
    
    def generate_thoughts(self, state, n=None):
        """Generate multiple candidate thoughts"""
        n = n or self.branching_factor
        
        prompt = f"""Từ trạng thái hiện tại, hãy nghĩ ra {n} hướng giải quyết khác nhau.

Trạng thái: {state}

Hướng 1: 
Hướng 2: 
Hướng 3: """
        
        if self.llm:
            response = self.llm(prompt)
            thoughts = [t.strip() for t in response.split('\n') 
                       if t.strip().startswith("Hướng")]
            return thoughts[:n]
        
        return [f"Thought {i+1}" for i in range(n)]
    
    def evaluate_thought(self, state, thought):
        """Evaluate how promising a thought is"""
        prompt = f"""Đánh giá hướng giải quyết này (1-10):

Trạng thái: {state}
Hướng: {thought}

Tiêu chí:
- Tính khả thi (0-3)
- Tốc độ giải quyết (0-3)
- Chất lượng kết quả (0-4)

Điểm số (1-10):"""
        
        if self.llm:
            response = self.llm(prompt)
            try:
                score = int(''.join(c for c in response if c.isdigit())[:2])
                return min(max(score, 1), 10)
            except (ValueError, IndexError):
                pass
        
        return 5  # Default score
    
    def solve(self, problem):
        """
        Tree search: explore best paths
        
        Uses BFS with pruning to avoid exponential explosion
        """
        root_state = {"problem": problem, "path": [], "score": 0}
        
        frontier = [root_state]
        best_solution = None
        best_score = -1
        
        for depth in range(self.max_depth):
            next_frontier = []
            
            for state in frontier:
                thoughts = self.generate_thoughts(
                    f"{state['problem']} | Path: {' → '.join(state['path'])}"
                )
                
                for thought in thoughts:
                    score = self.evaluate_thought(state['problem'], thought)
                    
                    new_state = {
                        "problem": state["problem"],
                        "path": state["path"] + [thought],
                        "score": state["score"] + score,
                    }
                    
                    if new_state["score"] > best_score:
                        best_score = new_state["score"]
                        best_solution = new_state
                    
                    next_frontier.append(new_state)
            
            # Prune: keep only top-k paths
            frontier = sorted(next_frontier, key=lambda x: x["score"], reverse=True)
            frontier = frontier[:self.branching_factor]
        
        return {
            "solution": best_solution["path"] if best_solution else [],
            "total_score": best_score,
            "paths_explored": self.branching_factor ** self.max_depth,
        }
```

### 2.3 ReWOO (Reasoning Without Observation)

```python
class ReWOOPlanner:
    """
    ReWOO: Decouple planning from execution
    
    1. PLAN all steps upfront (no observation between steps)
    2. EXECUTE all steps
    3. USE evidence to synthesize answer
    
    Advantage: Fewer LLM calls (batch planning)
    Disadvantage: Less adaptive to failures
    """
    
    def __init__(self, llm_func=None):
        self.llm = llm_func
    
    def plan_all(self, task, tools):
        """Generate all plan steps at once"""
        prompt = f"""Tạo kế hoạch hoàn chỉnh cho task sau.

Task: {task}
Tools có sẵn: {list(tools.keys())}

Output JSON:
{{
  "plan": [
    {{"step_id": "e1", "tool": "tool_name", "query": "query for tool"}},
    {{"step_id": "e2", "tool": "tool_name", "query": "query using #e1"}},
    ...
  ],
  "synthesis_prompt": "Dựa trên #e1, #e2, ..., trả lời: {task}"
}}"""
        
        if self.llm:
            response = self.llm(prompt)
            try:
                import json
                return json.loads(response)
            except json.JSONDecodeError:
                pass
        
        return {"plan": [], "synthesis_prompt": task}
    
    def execute_plan(self, plan, tools):
        """Execute all plan steps, collecting evidence"""
        evidence = {}
        
        for step in plan.get("plan", []):
            tool_name = step.get("tool", "")
            query = step.get("query", "")
            
            # Replace references with previous evidence
            for ref_id, ref_val in evidence.items():
                query = query.replace(f"#{ref_id}", str(ref_val))
            
            if tool_name in tools:
                try:
                    result = tools[tool_name](query)
                    evidence[step["step_id"]] = result
                except Exception as e:
                    evidence[step["step_id"]] = f"Error: {e}"
        
        return evidence
```

---

## 3. Agent Workflows

### 3.1 Các Kiểu Agent

```
┌──────────────────────────────────────────────────────────────────┐
│                    AGENT WORKFLOW TYPES                            │
│                                                                  │
│  1. REACT (Reasoning + Acting)                                   │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  Think → Act → Observe → Think → Act → ... → Answer     │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  2. PLAN-AND-EXECUTE                                            │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  Plan all steps → Execute step 1 → 2 → 3 → Report      │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  3. REFLECTIVE                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  Act → Reflect → Improve → Act → Reflect → ...          │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  4. MULTI-AGENT (Anthropic Pattern)                             │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  Planner Agent ─┬─► Coder Agent                         │   │
│  │                 ├─► Researcher Agent                     │   │
│  │                 └─► Reviewer Agent ──► Final Output     │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  5. LANGGRAPH-STYLE (State Machine)                             │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  [Start] → [Router] → [Tool_A] or [Tool_B] → [End]     │   │
│  │                         ↑               │                │   │
│  │                         └───────────────┘                │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  6. HIERARCHICAL (Claude Code Pattern)                          │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  Supervisor → Sub-Agent 1 (parallel)                     │   │
│  │           → Sub-Agent 2 (parallel)                       │   │
│  │           → Sub-Agent 3 (sequential) → Merge → Output   │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 3.2 Agent Implementation

```python
class SimpleAgent:
    """
    Simple agent with tool-calling loop
    
    Pattern: Think → Act → Observe → Repeat
    
    Harness integration:
    - Respects max_iterations (guardrail)
    - Logs all steps (feedback loop)
    - Validates tool permissions
    """
    
    def __init__(self, tools, llm_func, max_iterations=10):
        self.tools = {tool["name"]: tool for tool in tools}
        self.llm = llm_func
        self.max_iterations = max_iterations
        self.memory = []
        self.metrics = {
            "total_iterations": 0,
            "tools_used": {},
            "errors": 0,
            "total_tokens": 0,
        }
    
    def run(self, task):
        """Execute task with tool calls"""
        self.memory = []
        context = f"Task: {task}\n\nAvailable tools: {list(self.tools.keys())}"
        
        for i in range(self.max_iterations):
            # Think: decide what to do
            thought = self._think(context)
            
            # Check if done
            if thought.get("done"):
                return {
                    "answer": thought.get("answer", ""),
                    "steps": self.memory,
                    "iterations": i + 1,
                    "metrics": self.metrics,
                }
            
            # Act: execute tool with validation
            tool_name = thought.get("tool")
            tool_input = thought.get("input", {})
            
            if tool_name and tool_name in self.tools:
                # Pre-execution validation
                if not self._validate_tool_call(tool_name, tool_input):
                    context += f"\n\nStep {i+1}: Tool validation failed for '{tool_name}'. Try different approach."
                    continue
                
                result = self._execute_tool(tool_name, tool_input)
                
                # Post-execution validation
                if not self._validate_result(result):
                    self.metrics["errors"] += 1
                    context += f"\n\nStep {i+1}: Result validation failed. Retry with different parameters."
                    continue
                
                # Observe: add result to context
                observation = f"Tool '{tool_name}' returned: {result}"
                context += f"\n\nStep {i+1}: {thought.get('reasoning', '')}"
                context += f"\n→ Used {tool_name}({tool_input})"
                context += f"\n→ Result: {result}"
                
                self.memory.append({
                    "step": i + 1,
                    "thought": thought,
                    "action": tool_name,
                    "result": result,
                })
                
                self.metrics["tools_used"][tool_name] = self.metrics["tools_used"].get(tool_name, 0) + 1
            else:
                context += f"\n\nStep {i+1}: Invalid tool '{tool_name}'. Try again."
            
            self.metrics["total_iterations"] = i + 1
        
        return {
            "answer": "Max iterations reached",
            "steps": self.memory,
            "iterations": self.max_iterations,
            "metrics": self.metrics,
        }
    
    def _think(self, context):
        """LLM decides next action"""
        prompt = f"""{context}

Based on the information above, decide your next action.

Output JSON:
{{
  "reasoning": "Why I'm doing this",
  "tool": "tool_name" or null if done,
  "input": {{"param": "value"}},
  "done": false,
  "answer": null
}}

If you have enough information to answer, set "done": true and provide "answer"."""
        
        if self.llm:
            response = self.llm(prompt)
            try:
                import json
                return json.loads(response)
            except json.JSONDecodeError:
                pass
        
        return {"done": True, "answer": "No LLM available"}
    
    def _validate_tool_call(self, tool_name, tool_input):
        """Pre-execution validation (Harness guardrail)"""
        # Check tool exists
        if tool_name not in self.tools:
            return False
        
        # Check required params
        tool = self.tools[tool_name]
        required = tool.get("required_params", [])
        for param in required:
            if param not in tool_input:
                return False
        
        return True
    
    def _validate_result(self, result):
        """Post-execution validation (Harness guardrail)"""
        if result is None:
            return False
        if isinstance(result, str) and result.startswith("Error:"):
            return False
        return True
    
    def _execute_tool(self, tool_name, tool_input):
        """Execute a tool"""
        tool = self.tools.get(tool_name)
        if tool and "function" in tool:
            try:
                return tool["function"](**tool_input)
            except Exception as e:
                return f"Error: {str(e)}"
        return f"Tool '{tool_name}' not found"


class MultiAgent:
    """
    Multi-agent system with specialized agents
    
    Based on Anthropic's multi-agent architecture pattern:
    - Planner Agent: Creates the plan
    - Generator Agent: Executes each step
    - Evaluator Agent: Validates results
    """
    
    def __init__(self, agents, coordinator_llm=None):
        self.agents = agents  # {name: agent}
        self.coordinator = coordinator_llm
    
    def run(self, task):
        """Coordinate multiple agents"""
        plan = self._create_plan(task)
        
        results = {}
        for step in plan:
            agent_name = step["agent"]
            subtask = step["task"]
            
            if agent_name in self.agents:
                result = self.agents[agent_name].run(subtask)
                results[agent_name] = result
                
                # Check if result is satisfactory
                if not self._evaluate_result(result, subtask):
                    # Retry with feedback
                    retry_result = self.agents[agent_name].run(
                        f"{subtask}\n\nLần trước thất bại vì: {result.get('answer', 'unknown error')}"
                    )
                    results[agent_name] = retry_result
        
        return self._synthesize(results, task)
    
    def _create_plan(self, task):
        """Decompose task across agents"""
        agent_names = list(self.agents.keys())
        
        if self.coordinator:
            prompt = f"""Assign subtasks to available agents.

Task: {task}
Available agents: {agent_names}

Output JSON:
{{"steps": [{{"agent": "...", "task": "...", "dependencies": []}}]}}"""
            
            response = self.coordinator(prompt)
            try:
                import json
                return json.loads(response).get("steps", [])
            except json.JSONDecodeError:
                pass
        
        return [{"agent": agent_names[0], "task": task}]
    
    def _evaluate_result(self, result, original_task):
        """Evaluate if result meets requirements"""
        if result.get("iterations", 0) >= 10:
            return False
        if not result.get("answer"):
            return False
        return True
    
    def _synthesize(self, results, original_task):
        """Combine results from all agents"""
        combined = "\n".join(
            f"Agent {name}: {r.get('answer', 'N/A')}"
            for name, r in results.items()
        )
        
        if self.coordinator:
            prompt = f"""Tổng hợp kết quả từ nhiều agents:

Task gốc: {original_task}

Kết quả:
{combined}

Tổng hợp:"""
            return self.coordinator(prompt)
        
        return combined
```

---

## 4. State Management

```python
class AgentState:
    """
    Manage state across agent execution steps
    
    Supports: state persistence, checkpointing, rollback, versioning
    """
    
    def __init__(self):
        self.state = {}
        self.history = []
        self.checkpoints = []
        self.version = 0
    
    def set(self, key, value):
        """Set state value with versioning"""
        old_value = self.state.get(key)
        self.state[key] = value
        self.version += 1
        
        self.history.append({
            "action": "set",
            "key": key,
            "old": old_value,
            "new": value,
            "version": self.version,
            "timestamp": datetime.now().isoformat(),
        })
    
    def get(self, key, default=None):
        """Get state value"""
        return self.state.get(key, default)
    
    def checkpoint(self):
        """Save current state snapshot"""
        self.checkpoints.append({
            "state": dict(self.state),
            "version": self.version,
            "timestamp": datetime.now().isoformat(),
        })
        return len(self.checkpoints) - 1
    
    def rollback(self, checkpoint_id=None):
        """Rollback to checkpoint"""
        if checkpoint_id is None:
            checkpoint_id = len(self.checkpoints) - 1
        
        if 0 <= checkpoint_id < len(self.checkpoints):
            self.state = dict(self.checkpoints[checkpoint_id]["state"])
            self.version = self.checkpoints[checkpoint_id]["version"]
            return True
        return False
    
    def to_context(self):
        """Convert state to context string for LLM"""
        lines = [f"{k}: {v}" for k, v in self.state.items()]
        return "\n".join(lines)
    
    def diff(self, checkpoint_id):
        """Compare current state with checkpoint"""
        if 0 <= checkpoint_id < len(self.checkpoints):
            old = self.checkpoints[checkpoint_id]["state"]
            current = self.state
            
            changes = {
                "added": {k: v for k, v in current.items() if k not in old},
                "removed": {k: v for k, v in old.items() if k not in current},
                "modified": {k: (old.get(k), current.get(k)) 
                           for k in current if k in old and current[k] != old[k]},
            }
            return changes
        return {}
    
    def reset(self):
        """Clear all state"""
        self.state = {}
        self.version = 0
```

---

## 5. ReAct Pattern

```python
class ReActAgent:
    """
    ReAct: Reasoning + Acting
    
    Thought → Action → Observation → Thought → ...
    
    Based on paper: "ReAct: Synergizing Reasoning and Acting in Language Models"
    (Yao et al., 2022)
    
    Harness integration:
    - max_steps as guardrail
    - tool validation before execution
    - metrics tracking
    - error recovery
    """
    
    def __init__(self, tools, llm_func, max_steps=10):
        self.tools = tools
        self.llm = llm_func
        self.max_steps = max_steps
        self.trace = []
        self.token_usage = 0
    
    def run(self, query):
        """Execute ReAct loop"""
        prompt = f"""Solve this step by step using Thought/Action/Observation.

Question: {query}

Available actions:
{self._format_tools()}

Use this format:
Thought: [your reasoning about what to do next]
Action: [tool_name(input)]
Observation: [result of action]
... (repeat Thought/Action/Observation as needed)
Thought: [final reasoning]
Final Answer: [your answer]"""
        
        history = prompt
        
        for step in range(self.max_steps):
            response = self._llm_call(history)
            
            # Parse response
            parsed = self._parse_response(response)
            
            if parsed.get("final_answer"):
                return {
                    "answer": parsed["final_answer"],
                    "steps": step + 1,
                    "trace": self.trace,
                    "token_usage": self.token_usage,
                }
            
            # Execute action
            if parsed.get("action"):
                tool_name, tool_input = self._parse_action(parsed["action"])
                
                # Validate tool call
                if not self._validate_tool(tool_name):
                    history += f"\n{response}"
                    history += f"\nObservation: Invalid tool '{tool_name}'. Use available tools only."
                    continue
                
                observation = self._execute_tool(tool_name, tool_input)
                
                self.trace.append({
                    "step": step + 1,
                    "thought": parsed.get("thought", ""),
                    "action": parsed["action"],
                    "observation": observation,
                })
                
                history += f"\n{response}"
                history += f"\nObservation: {observation}"
            else:
                history += f"\n{response}"
        
        return {
            "answer": "Max steps reached without final answer",
            "steps": self.max_steps,
            "trace": self.trace,
            "token_usage": self.token_usage,
        }
    
    def _format_tools(self):
        lines = []
        for name, tool in self.tools.items():
            params = tool.get("params", [])
            lines.append(f"- {name}({', '.join(params)})")
        return "\n".join(lines)
    
    def _parse_response(self, response):
        """Parse Thought/Action/Observation from response"""
        result = {}
        
        if "Final Answer:" in response:
            result["final_answer"] = response.split("Final Answer:")[-1].strip()
        
        if "Thought:" in response:
            thought_lines = [l for l in response.split('\n') if l.strip().startswith('Thought:')]
            if thought_lines:
                result["thought"] = thought_lines[-1].replace('Thought:', '').strip()
        
        if "Action:" in response:
            action_line = [l for l in response.split('\n') if l.strip().startswith('Action:')]
            if action_line:
                result["action"] = action_line[0].replace('Action:', '').strip()
        
        return result
    
    def _parse_action(self, action_str):
        """Parse tool_name(input) format"""
        import re
        match = re.match(r'(\w+)\((.+)\)', action_str)
        if match:
            return match.group(1), match.group(2)
        return action_str, ""
    
    def _validate_tool(self, tool_name):
        """Check if tool exists in available tools"""
        return tool_name in self.tools
    
    def _execute_tool(self, tool_name, tool_input):
        if tool_name in self.tools:
            try:
                func = self.tools[tool_name].get("function")
                if func:
                    return func(tool_input)
            except Exception as e:
                return f"Error: {e}"
        return f"Unknown tool: {tool_name}"
    
    def _llm_call(self, prompt):
        if self.llm:
            return self.llm(prompt)
        return "Thought: I don't know\nFinal Answer: N/A"
```

---

## 6. Harness-Integrated Planning

### 6.1 TypeScript Interface (Harness Architecture)

```typescript
// Planning System Interface — Tích hợp hoàn chỉnh với Harness
interface PlanningSystem {
  // Core planning capabilities
  decompose: (task: Task) => Promise<DecomposedPlan>;
  replan: (task: Task, failure: FailureInfo) => Promise<DecomposedPlan>;
  estimateComplexity: (task: Task) => ComplexityEstimate;
  
  // State management
  getState: () => AgentState;
  checkpoint: () => number;
  rollback: (checkpointId: number) => boolean;
  
  // Integration points
  tools: ToolRegistry;
  memory: MemorySystem;
  guardrails: GuardrailSystem;
  feedback: FeedbackSystem;
}

interface DecomposedPlan {
  rootTask: Task;
  subtasks: Task[];
  executionOrder: ExecutionStep[];
  estimatedTokens: number;
  estimatedTime: number;
  requiredTools: string[];
}

interface ExecutionStep {
  taskId: string;
  agentId: string;
  dependencies: string[];
  timeoutMs: number;
  tokenBudget: number;
  requiredPermissions: string[];
}

// Complete Harness-Integrated Planner
class HarnessPlanner implements PlanningSystem {
  private tools: ToolRegistry;
  private memory: MemorySystem;
  private guardrails: GuardrailSystem;
  private feedback: FeedbackSystem;
  private state: AgentState;
  
  async decompose(task: Task): Promise<DecomposedPlan> {
    // 1. Validate input
    const inputCheck = await this.guardrails.validateInput(task);
    if (!inputCheck.valid) {
      throw new Error(`Task validation failed: ${inputCheck.reason}`);
    }
    
    // 2. Check memory for similar past tasks
    const similarTasks = await this.memory.longTerm.search(
      `task: ${task.description}`,
      { limit: 3 }
    );
    
    // 3. Decompose with context from past experiences
    const plan = await this.decomposeWithMemory(task, similarTasks);
    
    // 4. Validate plan against guardrails
    const planValidation = await this.guardrails.validatePlan(plan);
    if (!planValidation.valid) {
      throw new Error(`Plan validation failed: ${planValidation.reason}`);
    }
    
    // 5. Log for feedback
    await this.feedback.logPlan(task, plan);
    
    return plan;
  }
  
  async replan(task: Task, failure: FailureInfo): Promise<DecomposedPlan> {
    // Learn from failure
    await this.memory.longTerm.add(
      `Failed task: ${task.name}, Error: ${failure.error}`,
      { type: 'failure_pattern', task: task.name }
    );
    
    // Replan with failure context
    const plan = await this.decompose(task);
    
    // Adjust based on failure
    plan.executionOrder = plan.executionOrder.map(step => ({
      ...step,
      timeoutMs: step.timeoutMs * 1.5, // Increase timeout
      tokenBudget: step.tokenBudget * 1.2, // Increase budget
    }));
    
    return plan;
  }
  
  estimateComplexity(task: Task): ComplexityEstimate {
    const depth = this.getDepth(task);
    const breadth = this.getBreadth(task);
    
    return {
      score: Math.min(10, depth * 2 + breadth),
      depth,
      totalSubtasks: breadth,
      complexity: depth <= 1 ? 'simple' : depth <= 2 ? 'medium' : 'complex',
      recommendedApproach: breadth > 10 ? 'multi-agent' : 'single-agent',
    };
  }
  
  getState(): AgentState { return this.state; }
  checkpoint(): number { return this.state.checkpoint(); }
  rollback(id: number): boolean { return this.state.rollback(id); }
  
  private async decomposeWithMemory(task: Task, memories: any[]): Promise<DecomposedPlan> {
    // Implementation that uses past task memories
    // to improve decomposition quality
    return { /* ... */ } as DecomposedPlan;
  }
  
  private getDepth(task: Task): number { /* ... */ return 0; }
  private getBreadth(task: Task): number { /* ... */ return 0; }
}
```

---

## 7. Case Studies Thực Tế

### 7.1. SWE-agent (Princeton NLP) — Planning-First Approach

**Bối cảnh**: SWE-agent sử dụng planning để agent không "lạc lối" trong codebase.

**Vấn đề ban đầu**:
- Agent không có kế hoạch rõ ràng → thực hiện lộn xộn
- Phải đọc toàn bộ codebase trước khi bắt đầu → tốn token
- Không biết khi nào dừng lại

**Giải pháp**:
```typescript
// Harness planning cho SWE-agent
const swePlanner = {
  async plan(issue: string, codebase: CodebaseInfo) {
    // 1. Phân tích issue
    const analysis = await analyzeIssue(issue);
    
    // 2. Tìm files liên quan (KHÔNG đọc toàn bộ)
    const relevantFiles = await findRelevantFiles(analysis, codebase);
    
    // 3. Lập kế hoạch thực thi
    return {
      steps: [
        { action: "read_file", path: relevantFiles[0], reason: "understand context" },
        { action: "search_code", pattern: analysis.functionName, reason: "find implementation" },
        { action: "edit_file", path: relevantFiles[0], changes: analysis.suggestedFix },
        { action: "run_tests", suite: "relevant", reason: "verify fix" },
      ],
      estimatedTokens: 15000,
      maxRetries: 2,
    };
  }
};
```

**Kết quả**:
- Success rate: 12.5% → 20.5% (+64%)
- Token usage giảm 30%
- Thời gian giải quyết giảm 40%

**Lesson**: **"Plan before act — mapping codebase reduces wasted exploration"**

---

### 7.2. Anthropic Multi-Agent Architecture

**Bối cảnh**: Claude Code cần tạo ứng dụng phức tạp (games, DAW).

**Giải pháp — 3-Agent Planning System**:
```typescript
// Planner → Generator → Evaluator
class AnthropicPlannerAgent {
  systemPrompt = `
    You are a planning specialist.
    Break down complex tasks into clear, actionable steps.
    Consider dependencies and order.
    Output: JSON with steps array.
  `;
  
  async plan(task: string): Promise<Plan> {
    // 1. Analyze task complexity
    const complexity = await this.estimateComplexity(task);
    
    // 2. Generate step-by-step plan
    const steps = await this.generateSteps(task, complexity);
    
    // 3. Add dependency graph
    const graphedSteps = this.addDependencies(steps);
    
    return {
      steps: graphedSteps,
      estimatedComplexity: complexity,
      parallelizableGroups: this.findParallelGroups(graphedSteps),
    };
  }
  
  private findParallelGroups(steps: Step[]): Step[][] {
    // Find steps that can run in parallel (no dependencies between them)
    const groups: Step[][] = [];
    const remaining = [...steps];
    
    while (remaining.length > 0) {
      const group = remaining.filter(s => 
        s.dependencies.every(dep => 
          groups.some(g => g.some(gs => gs.id === dep))
        )
      );
      groups.push(group);
      remaining.splice(0, group.length);
    }
    
    return groups;
  }
}
```

**Kết quả**:
- Tạo được games, DAW hoàn chỉnh
- Success rate cao hơn 80% so với single-agent
- Code quality tốt hơn nhờ evaluation loop

**Lesson**: **"Decompose → Parallelize → Evaluate — the 3-step formula"**

---

### 7.3. Claude Code — Hierarchical Planning System

**Bối cảnh**: Claude Code leak reveals advanced planning architecture.

```typescript
// Hierarchical Planning — 3 cấp độ
class ClaudePlanningSystem {
  // Level 1: Strategic Planning (task-level)
  async strategicPlan(goal: string): Promise<StrategicPlan> {
    return {
      approach: await this.selectApproach(goal),
      phases: await this.definePhases(goal),
      estimatedTime: await this.estimateTime(goal),
      checkpoints: await this.defineCheckpoints(goal),
    };
  }
  
  // Level 2: Tactical Planning (phase-level)
  async tacticalPlan(phase: Phase): Promise<TacticalPlan> {
    return {
      steps: await this.breakDownPhase(phase),
      tools: await this.selectTools(phase),
      parallelizable: await this.findParallelSteps(phase),
      rollbackPoints: await this.identifyRollbackPoints(phase),
    };
  }
  
  // Level 3: Operational Planning (step-level)
  async operationalPlan(step: Step): Promise<OperationalPlan> {
    return {
      action: await this.defineAction(step),
      parameters: await this.extractParameters(step),
      validation: await this.defineValidation(step),
      errorHandling: await this.defineErrorHandling(step),
    };
  }
}
```

**Key Feature — Dynamic Re-planning**:
```typescript
// Claude Code tự động re-plan khi gặp vấn đề
class DynamicReplanner {
  async handleFailure(
    failedStep: Step, 
    error: Error, 
    currentPlan: Plan
  ): Promise<Plan> {
    // 1. Analyze failure
    const analysis = await this.analyzeFailure(failedStep, error);
    
    // 2. Try to fix within current plan
    if (analysis.canFixInPlace) {
      return this.adjustStep(failedStep, analysis.fix);
    }
    
    // 3. Re-plan from this point
    const remainingSteps = currentPlan.steps.filter(
      s => !s.dependencies.includes(failedStep.id)
    );
    
    return this.replan(remainingSteps, analysis.context);
  }
}
```

---

### 7.4. Cursor IDE — Context-Aware Planning

```typescript
class CursorPlanner {
  async plan(codeChange: string): Promise<Plan> {
    // 1. Understand current context
    const context = {
      currentFile: editor.getCurrentFile(),
      selectedCode: editor.getSelection(),
      cursorLine: editor.getCursorLine(),
      recentEdits: this.getRecentEdits(5),
      relatedFiles: await this.findRelatedFiles(),
      gitContext: await git.getContext(),
    };
    
    // 2. Plan changes
    const plan = await this.planChanges(codeChange, context);
    
    // 3. Minimize scope (don't change unrelated files)
    const scopedPlan = this.minimizeScope(plan, context.currentFile);
    
    return scopedPlan;
  }
  
  private minimizeScope(plan: Plan, currentFile: string): Plan {
    return {
      ...plan,
      steps: plan.steps.filter(step => 
        this.isRelated(step.file, currentFile) || step.essential
      ),
    };
  }
}
```

**Bài học từ Case Studies**:

| Lesson | SWE-agent | Anthropic | Claude Code | Cursor |
|--------|-----------|-----------|-------------|--------|
| **Plan before act** | ✅ File mapping | ✅ Step decomposition | ✅ 3-level planning | ✅ Context-aware |
| **Limit scope** | ✅ 50 results max | ✅ Step-level | ✅ Hierarchical | ✅ File-scoped |
| **Re-plan on failure** | - | ✅ Evaluation loop | ✅ Dynamic replan | - |
| **Parallel execution** | - | ✅ Group steps | ✅ Sub-agents | - |
| **Use memory** | - | - | ✅ Past patterns | ✅ Recent edits |

---

## 8. Design Principles

### 8.1 SOLID Cho Planning System

**1. Single Responsibility**
- Mỗi planner chỉ phân 1 loại task (code, research, deploy)
- Mỗi decomposition strategy xử lý 1 pattern cụ thể

**2. Open/Closed**
- Mở cho thêm planning strategies mới
- Đóng cho sửa đổi core decomposition logic

**3. Liskov Substitution**
- Các planner có thể thay thế cho nhau
- Cùng interface, khác implementation (ToT, Plan-and-Solve, ReWOO)

**4. Interface Segregation**
- Không ép planner phải handle tất cả types
- Tách planner theo domain

**5. Dependency Inversion**
- Planner phụ thuộc vào Task abstraction, không vào具体 implementation
- Dễ dàng swap decomposition strategy

### 8.2 The 10 Commandments of Task Planning

```
1. Thou shall DECOMPOSE before EXECUTE
   → Phân task trước khi chạy, đừng nhảy vào code ngay

2. Thou shall RESPECT dependencies
   → Tôn trọng thứ tự, đừng chạy parallel khi cần sequential

3. Thou shall SET token budgets
   → Giới hạn token mỗi task,防止 token explosion

4. Thou shall CHECKPOINT regularly
   → Lưu state thường xuyên, để rollback khi cần

5. Thou shall RE-PLAN on failure
   → Tạo kế hoạch mới khi thất bại, đừng thử lại y hệt

6. Thou shall LOG every decision
   → Ghi lại mọi quyết định planning, để debug và improve

7. Thou shall VALIDATE each step
   → Kiểm tra kết quả mỗi bước, đừng chờ cuối mới check

8. Thou shall PARALLELIZE when possible
   → Chạy song song khi được, để tiết kiệm thời gian

9. Thou shall ESTIMATE before starting
   → Ước tính độ phức tạp trước, để chọn strategy phù hợp

10. Thou shall LEARN from past plans
    → Học từ kế hoạch cũ, để cải thiện kế hoạch mới
```

---

## 9. Best Practices

### 9.1 DO ✅

- **Phân task theo granularities**: broad → medium → fine-grained
- **Xác định dependencies trước khi execute**: Dùng DAG để represent
- **Set timeout và retry cho mỗi task**: Prevent infinite loops
- **Checkpoint trước mỗi operation quan trọng**: Để rollback
- **Re-plan khi gặp failure**: Không retry y hệt
- **Track token usage per task**: Để optimize cost
- **Validate kết quả mỗi step**: Fail fast
- **Parallelize independent tasks**: Tiết kiệm thời gian

### 9.2 DON'T ❌

- **Đừng execute toàn bộ task 1 lần**: Quá rủi ro
- **Đừng ignore failures**: Phân tích nguyên nhân
- **Đừng over-decompose**: Task quá nhỏ = overhead lớn
- **Đừng forget to update state**: State sync rất quan trọng
- **Đừng hardcode task order**: Nên compute từ dependencies
- **Đừng skip validation**: Output chưa verify = chưa hoàn thành
- **Đừng allocate token budget quá lớn**: Prevent waste
- **Đừng use single agent cho complex tasks**: Multi-agent tốt hơn

### 9.3 Token Budget Management

```typescript
class TokenBudgetManager {
  private totalBudget: number;
  private allocated: Map<string, number>;
  private used: Map<string, number>;
  
  constructor(totalBudget: number = 100000) {
    this.totalBudget = totalBudget;
    this.allocated = new Map();
    this.used = new Map();
  }
  
  allocate(taskId: string, budget: number): boolean {
    const totalAllocated = Array.from(this.allocated.values()).reduce((a, b) => a + b, 0);
    
    if (totalAllocated + budget > this.totalBudget) {
      return false; // Not enough budget
    }
    
    this.allocated.set(taskId, budget);
    return true;
  }
  
  report(taskId: string, tokens: number): void {
    this.used.set(taskId, (this.used.get(taskId) || 0) + tokens);
  }
  
  canContinue(taskId: string): boolean {
    const allocated = this.allocated.get(taskId) || 0;
    const used = this.used.get(taskId) || 0;
    return used < allocated * 0.9; // Allow 90% usage
  }
  
  getRemaining(): number {
    const totalUsed = Array.from(this.used.values()).reduce((a, b) => a + b, 0);
    return this.totalBudget - totalUsed;
  }
  
  getReport(): TokenReport {
    return {
      totalBudget: this.totalBudget,
      allocated: Object.fromEntries(this.allocated),
      used: Object.fromEntries(this.used),
      remaining: this.getRemaining(),
      efficiency: Array.from(this.used.values()).reduce((a, b) => a + b, 0) / 
                  Array.from(this.allocated.values()).reduce((a, b) => a + b, 1),
    };
  }
}
```

---

## 10. Testing Planning Systems

```python
import unittest

class TestTaskPlanner(unittest.TestCase):
    def setUp(self):
        self.planner = TaskPlanner()
    
    def test_simple_decomposition(self):
        """Simple task → 1-3 subtasks"""
        task = Task(id="t1", name="Fix typo", description="Fix typo in README")
        plan = self.planner.decompose("Fix typo in README")
        self.assertLessEqual(len(plan), 3)
    
    def test_complex_decomposition(self):
        """Complex task → multiple subtasks"""
        plan = self.planner.decompose(
            "Build a complete RAG system with vector search, "
            "chunking, embedding, and query processing"
        )
        self.assertGreater(len(plan), 3)
    
    def test_complexity_estimation(self):
        """Estimate complexity correctly"""
        task = Task(id="t1", name="Simple", description="Simple task")
        estimate = self.planner.estimate_complexity(task)
        self.assertIn(estimate["complexity"], ["simple", "medium", "complex"])
    
    def test_dependency_detection(self):
        """Verify dependency-based execution order"""
        t1 = Task(id="t1", name="Setup DB", dependencies=[])
        t2 = Task(id="t2", name="Create schema", dependencies=["t1"])
        t3 = Task(id="t3", name="Insert data", dependencies=["t2"])
        
        completed = set()
        # t1 should be executable first
        self.assertTrue(t1.can_execute(completed))
        
        # t2 should NOT be executable yet
        self.assertFalse(t2.can_execute(completed))
        
        # After t1 completes
        completed.add("t1")
        self.assertTrue(t2.can_execute(completed))
    
    def test_max_depth_limit(self):
        """Verify decomposition respects max depth"""
        plan = self.planner.decompose(
            "Build: a → b → c → d → e → f",
            max_depth=2
        )
        depth = self.planner._get_depth(
            Task(id="root", subtasks=plan)
        )
        self.assertLessEqual(depth, 2)
    
    def test_retry_logic(self):
        """Test task retry on failure"""
        task = Task(id="t1", name="Flaky task", max_retries=3)
        
        # First failure
        task.mark_failed("timeout")
        self.assertTrue(task.can_retry())
        
        # After 3 retries
        task.retry_count = 3
        self.assertFalse(task.can_retry())

class TestTokenBudget(unittest.TestCase):
    def setUp(self):
        self.budget_manager = TokenBudgetManager(totalBudget=50000)
    
    def test_allocation_within_budget(self):
        """Allocate within budget"""
        result = self.budget_manager.allocate("task1", 20000)
        self.assertTrue(result)
    
    def test_allocation_exceeds_budget(self):
        """Allocation exceeds budget"""
        self.budget_manager.allocate("task1", 30000)
        result = self.budget_manager.allocate("task2", 25000)
        self.assertFalse(result)
    
    def test_usage_tracking(self):
        """Track token usage per task"""
        self.budget_manager.allocate("task1", 10000)
        self.budget_manager.report("task1", 5000)
        
        self.assertTrue(self.budget_manager.canContinue("task1"))
        
        self.budget_manager.report("task1", 4500)
        self.assertFalse(self.budget_manager.canContinue("task1"))

class TestAgentState(unittest.TestCase):
    def setUp(self):
        self.state = AgentState()
    
    def test_set_and_get(self):
        self.state.set("key1", "value1")
        self.assertEqual(self.state.get("key1"), "value1")
    
    def test_checkpoint_and_rollback(self):
        self.state.set("key1", "value1")
        cp = self.state.checkpoint()
        self.state.set("key1", "value2")
        self.assertEqual(self.state.get("key1"), "value2")
        
        self.state.rollback(cp)
        self.assertEqual(self.state.get("key1"), "value1")
    
    def test_diff(self):
        self.state.set("key1", "value1")
        cp = self.state.checkpoint()
        self.state.set("key1", "value2")
        self.state.set("key2", "value3")
        
        changes = self.state.diff(cp)
        self.assertIn("key2", changes["added"])
        self.assertIn("key1", changes["modified"])

if __name__ == "__main__":
    unittest.main()
```

---

## 11. Advanced Patterns

### 11.1 Hierarchical Task Network (HTN)

```python
class HTNPlanner:
    """
    Hierarchical Task Network planning
    
    Unlike simple decomposition, HTN uses:
    - Primitive tasks (can be executed directly)
    - Compound tasks (need further decomposition)
    - Methods (ways to decompose compound tasks)
    """
    
    def __init__(self):
        self.methods = {}  # task_type -> list of decomposition methods
        self.primitive_actions = {}  # action_name -> implementation
    
    def add_method(self, task_type, method):
        if task_type not in self.methods:
            self.methods[task_type] = []
        self.methods[task_type].append(method)
    
    def decompose(self, task, depth=0, max_depth=5):
        if depth >= max_depth:
            return [task]
        
        if task["type"] in self.methods:
            for method in self.methods[task["type"]]:
                if method["precondition"](task):
                    subtasks = method["decompose"](task)
                    result = []
                    for subtask in subtasks:
                        result.extend(self.decompose(subtask, depth + 1, max_depth))
                    return result
        
        return [task]  # Primitive task
    
    def execute_plan(self, plan):
        results = []
        for task in plan:
            if task["name"] in self.primitive_actions:
                result = self.primitive_actions[task["name"]](task)
                results.append({"task": task, "result": result})
        return results
```

### 11.2 Self-Reflective Planning

```python
class SelfReflectivePlanner:
    """
    Planner that improves by reflecting on its own plans
    
    Pattern: Plan → Execute → Reflect → Improve Plan
    """
    
    def __init__(self, llm_func=None):
        self.llm = llm_func
        self.plan_history = []  # Past plans and their outcomes
    
    def plan_with_reflection(self, task, max_reflections=3):
        """Plan with self-reflection"""
        plan = self._initial_plan(task)
        
        for reflection_round in range(max_reflections):
            # Reflect on plan quality
            reflection = self._reflect_on_plan(task, plan)
            
            if reflection["quality_score"] >= 8:
                break  # Plan is good enough
            
            # Improve plan based on reflection
            plan = self._improve_plan(plan, reflection["suggestions"])
        
        return plan
    
    def _initial_plan(self, task):
        return {
            "steps": [],
            "estimated_complexity": 0,
            "risks": [],
        }
    
    def _reflect_on_plan(self, task, plan):
        if self.llm:
            prompt = f"""Phân tích kế hoạch sau:

Task: {task}
Plan: {plan}

Đánh giá:
1. Tính đầy đủ (1-10)
2. Tính khả thi (1-10)
3. Rủi ro (list)
4. Cải tiến (suggestions)"""
            
            response = self.llm(prompt)
            return {"quality_score": 7, "suggestions": [], "risks": []}
        
        return {"quality_score": 5, "suggestions": ["Add more detail"], "risks": []}
    
    def _improve_plan(self, plan, suggestions):
        plan["improvements"] = suggestions
        return plan
    
    def learn_from_outcome(self, task, plan, outcome):
        """Store plan outcome for future learning"""
        self.plan_history.append({
            "task": task,
            "plan": plan,
            "outcome": outcome,
            "timestamp": datetime.now().isoformat(),
        })
```

---

## 12. Tools & Frameworks

### 12.1 LangGraph (Recommended for Planning)

```python
from langgraph.graph import StateGraph, END

# Define planning state machine
def create_planning_graph():
    graph = StateGraph(dict)
    
    # Add nodes
    graph.add_node("analyze", analyze_task)
    graph.add_node("plan", create_plan)
    graph.add_node("validate", validate_plan)
    graph.add_node("execute", execute_step)
    graph.add_node("replan", replan_on_failure)
    
    # Add edges
    graph.add_edge("analyze", "plan")
    graph.add_edge("plan", "validate")
    graph.add_conditional_edges("validate", decide_next, {
        "pass": "execute",
        "fail": "replan",
        "done": END,
    })
    graph.add_edge("execute", "validate")
    graph.add_edge("replan", "validate")
    
    return graph.compile()
```

### 12.2 CrewAI (Multi-Agent Planning)

```python
from crewai import Agent, Task, Crew

# Create specialized planning agents
planner = Agent(
    role="Task Planner",
    goal="Break down complex tasks into actionable steps",
    backstory="Expert at task decomposition and dependency analysis",
    tools=[analysis_tool, research_tool],
)

executor = Agent(
    role="Task Executor", 
    goal="Execute planned tasks efficiently",
    backstory="Expert at implementation and testing",
    tools=[code_tool, test_tool],
)

reviewer = Agent(
    role="Plan Reviewer",
    goal="Review and validate task plans",
    backstory="Expert at quality assurance and risk assessment",
)

# Create tasks
planning_task = Task(
    description="Create execution plan for: {task}",
    agent=planner,
)

execution_task = Task(
    description="Execute the plan created by planner",
    agent=executor,
)

# Assemble crew
crew = Crew(
    agents=[planner, executor, reviewer],
    tasks=[planning_task, execution_task],
    verbose=True,
)
```

### 12.3 AutoGen (Microsoft)

```python
from autogen import AssistantAgent, UserProxyAgent

planner = AssistantAgent(
    name="planner",
    llm_config={"model": "gpt-4"},
    system_message="""You are a planning specialist.
    Break down tasks into clear steps with dependencies.
    Always output JSON format."""
)

executor = AssistantAgent(
    name="executor",
    llm_config={"model": "gpt-4"},
    system_message="""You execute tasks based on the plan.
    Report completion status for each step."""
)

user = UserProxyAgent(
    name="user",
    human_input_mode="TERMINATE",
    max_consecutive_auto_reply=10,
)
```

---

## 13. Tương Lai

### 13.1 Xu Hướng 2026-2028

**1. AI Self-Planning**
- Agent tự động tạo kế hoạch không cần human input
- Adaptive planning dựa trên real-time feedback
- Cross-task learning (học từ planning history)

**2. Collaborative Planning**
- Nhiều agents cùng plan và vote
- Distributed planning across teams
- Shared planning knowledge bases

**3. Predictive Planning**
- Predict failures before they happen
- Proactive re-planning
- Risk-aware task allocation

**4. Context-Aware Planning**
- Plans adapt to available context
- Dynamic resource allocation
- Smart token budget management

**5. Visual Planning Interfaces**
- Drag-and-drop plan builders
- Real-time plan visualization
- Interactive plan editing

### 13.2 Lời Khuyên

```
1. Start with simple patterns
   → Sequential → Parallel → Conditional → Hierarchical

2. Add complexity gradually
   → Đừng implement ToT ngay từ đầu

3. Always validate
   → Check kết quả mỗi bước

4. Learn from failures
   → Store failure patterns

5. Measure everything
   → Track tokens, time, success rate
```

---

## Tài Liệu Tham Khảo

### Papers & Research

1. **ReAct: Synergizing Reasoning and Acting in Language Models**
   - Yao et al., 2022
   - https://arxiv.org/abs/2210.03629

2. **Tree of Thoughts: Deliberate Problem Solving with Large Language Models**
   - Yao et al., 2023
   - https://arxiv.org/abs/2305.10601

3. **Plan-and-Solve Prompting**
   - Wang et al., 2023
   - https://arxiv.org/abs/2305.04091

4. **SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering**
   - Princeton NLP Lab, 2024
   - https://arxiv.org/abs/2405.15793

5. **ReWOO: Decoupling Reasoning from Observations for Efficient Augmented Language Models**
   - Xu et al., 2023
   - https://arxiv.org/abs/2305.18323

### Frameworks

1. **LangGraph** - https://langchain-ai.github.io/langgraph/
2. **CrewAI** - https://www.crewai.com
3. **AutoGen** - https://microsoft.github.io/autogen/
4. **LangChain** - https://langchain.com

---

*Tài liệu: IV. Plan & Decompose Task — HARNESS ENGINEERING EDITION*
*Ngày cập nhật: 13/07/2026*
*Tác giả: AI Knowledge Repository*