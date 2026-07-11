# ⚙️ VII. Workflow

## Tổng Quan

**Workflow** là quá trình **thiết kế và tổ chức các bước thực hiện** để hoàn thành một tác vụ coding phức tạp. Workflow tốt giúp AI agent hoạt động có hệ thống, dễ debug, và có thể lặp lại một cách đáng tin cậy.

```
┌──────────────────────────────────────────────────────────────────┐
│                         WORKFLOW                                  │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │                                                            │  │
│  │  Trigger ──► Plan ──► Execute ──► Validate ──► Deploy      │  │
│  │    │          │         │            │           │          │  │
│  │    ▼          ▼         ▼            ▼           ▼          │  │
│  │  ┌─────┐  ┌──────┐  ┌──────┐    ┌──────┐   ┌────────┐    │  │
│  │  │Event│  │ Task │  │ Tools│    │ Test │   │ Report │    │  │
│  │  │     │  │ Deps │  │ Calls│    │ Eval │   │ & Log  │    │  │
│  │  └─────┘  └──────┘  └──────┘    └──────┘   └────────┘    │  │
│  │                                                            │  │
│  │  ┌──────────────────────────────────────────────────┐     │  │
│  │  │            State Management & Retry               │     │  │
│  │  └──────────────────────────────────────────────────┘     │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## Nội Dung

| # | Chủ đề | Mô tả |
|---|--------|-------|
| 1 | [Workflow Patterns](#1-workflow-patterns) | Các mẫu workflow phổ biến |
| 2 | [Pipeline Design](#2-pipeline-design) | Thiết kế pipeline xử lý |
| 3 | [State Machine](#3-state-machine) | Quản lý trạng thái workflow |
| 4 | [Error Recovery](#4-error-recovery) | Xử lý lỗi và retry |
| 5 | [Observability](#5-observability) | Logging và monitoring |
| 6 | [Best Practices](#6-best-practices) | Quy tắc thiết kế workflow |

---

## 1. Workflow Patterns

### 1.1 Sequential Workflow (Tuần Tự)

```
┌──────────────────────────────────────────────────────────────────┐
│                   SEQUENTIAL WORKFLOW                             │
│                                                                  │
│  ┌──────┐    ┌──────┐    ┌──────┐    ┌──────┐    ┌──────┐     │
│  │Step 1│───►│Step 2│───►│Step 3│───►│Step 4│───►│Step 5│     │
│  │Parse │    │Analyze│   │Build │    │Test  │    │Deploy│     │
│  └──────┘    └──────┘    └──────┘    └──────┘    └──────┘     │
│                                                                  │
│  Mỗi bước chỉ chạy SAU khi bước trước hoàn thành              │
│  → Đơn giản, dễ debug, nhưng chậm                            │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

```python
class SequentialWorkflow:
    """Workflow chạy tuần tự — mỗi bước đầu vào là output bước trước"""
    
    def __init__(self):
        self.steps = []
        self.state = {}
        self.history = []
    
    def add_step(self, name, func, **kwargs):
        """Thêm một bước vào workflow"""
        self.steps.append({
            "name": name,
            "func": func,
            "kwargs": kwargs,
        })
        return self
    
    def run(self, initial_input):
        """Chạy tuần tự tất cả các bước"""
        current_input = initial_input
        self.state["status"] = "running"
        
        for i, step in enumerate(self.steps):
            step_name = step["name"]
            print(f"▶ Step {i+1}/{len(self.steps)}: {step_name}")
            
            try:
                result = step["func"](current_input, **step["kwargs"])
                self.history.append({
                    "step": step_name,
                    "input": current_input,
                    "output": result,
                    "status": "success",
                })
                current_input = result
                print(f"  ✅ Done")
                
            except Exception as e:
                self.history.append({
                    "step": step_name,
                    "input": current_input,
                    "error": str(e),
                    "status": "failed",
                })
                self.state["status"] = "failed"
                self.state["failed_step"] = step_name
                raise WorkflowError(f"Step '{step_name}' failed: {e}")
        
        self.state["status"] = "completed"
        return current_input


# Usage — Ví dụ: Code Review Pipeline
workflow = SequentialWorkflow()
workflow.add_step("parse_pr", parse_pull_request)
workflow.add_step("analyze_diff", analyze_code_diff)
workflow.add_step("run_linter", run_linter_checks)
workflow.add_step("generate_review", generate_review_comments)
workflow.add_step("post_review", post_to_github)

result = workflow.run({"pr_number": 42})
```

### 1.2 Parallel Workflow (Song Song)

```
┌──────────────────────────────────────────────────────────────────┐
│                    PARALLEL WORKFLOW                              │
│                                                                  │
│            ┌──────┐                                              │
│            │Step A│                                              │
│       ┌───►│Lint  │───┐                                          │
│       │    └──────┘   │                                          │
│  ┌────┤               ├───►  ┌──────────┐    ┌──────┐          │
│  │Fan │    ┌──────┐   │      │  Merge   │───►│Final │          │
│  │Out │───►│Step B│───┤      │  Results │    │Output│          │
│  │    │    │Test  │   │      └──────────┘    └──────┘          │
│  └────┤    └──────┘   │         ▲               ▲              │
│       │    ┌──────┐   │         │   ┌────────┐  │              │
│       └───►│Step C│───┘         └───│  Fan   │  │              │
│            │Build │                  │  In    │  │              │
│            └──────┘                  └────────┘  │              │
│                                                                  │
│  Nhiều bước chạy ĐỒNG THỜI → nhanh hơn                       │
│  Cần merge/kết quả trước khi tiếp tục                         │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

```python
import asyncio
from concurrent.futures import ThreadPoolExecutor, as_completed

class ParallelWorkflow:
    """Workflow chạy song song — nhiều bước cùng lúc"""
    
    def __init__(self, max_workers=4):
        self.parallel_groups = []
        self.max_workers = max_workers
    
    def add_parallel_group(self, steps):
        """
        Thêm một nhóm các bước chạy song song.
        steps: list of (name, func, args)
        """
        self.parallel_groups.append(steps)
        return self
    
    def add_sequential_step(self, name, func):
        """Thêm một bước chạy sau tất cả parallel groups"""
        self.parallel_groups.append([(name, func, ())])
        return self
    
    def run(self, initial_input):
        """Chạy workflow với parallel groups"""
        current_input = initial_input
        
        for group_idx, group in enumerate(self.parallel_groups):
            if len(group) == 1:
                # Single step — chạy trực tiếp
                name, func, args = group[0]
                print(f"▶ Sequential: {name}")
                current_input = func(current_input, *args)
            else:
                # Parallel group — chạy song song
                print(f"▶ Parallel group {group_idx + 1}: "
                      f"{len(group)} tasks")
                results = {}
                
                with ThreadPoolExecutor(
                    max_workers=self.max_workers
                ) as executor:
                    futures = {}
                    for name, func, args in group:
                        future = executor.submit(
                            func, current_input, *args
                        )
                        futures[future] = name
                    
                    for future in as_completed(futures):
                        name = futures[future]
                        try:
                            results[name] = future.result()
                            print(f"  ✅ {name} completed")
                        except Exception as e:
                            print(f"  ❌ {name} failed: {e}")
                            raise
                
                current_input = results  # Gộp kết quả
        
        return current_input


# Usage — Ví dụ: Build Pipeline
workflow = ParallelWorkflow(max_workers=3)
workflow.add_parallel_group([
    ("lint",    run_eslint,    ()),
    ("test",    run_jest,      ()),
    ("build",   run_tsc_build, ()),
])
workflow.add_sequential_step("deploy", deploy_to_staging)

result = workflow.run({"project": "frontend"})
```

### 1.3 Conditional Workflow (Có Điều Kiện)

```python
class ConditionalWorkflow:
    """Workflow với nhánh điều kiện — như if/else trong code"""
    
    def __init__(self):
        self.steps = []
    
    def add_condition(self, name, condition_func, 
                      if_true=None, if_false=None):
        self.steps.append({
            "type": "condition",
            "name": name,
            "condition": condition_func,
            "if_true": if_true,
            "if_false": if_false,
        })
        return self
    
    def add_action(self, name, func):
        self.steps.append({
            "type": "action",
            "name": name,
            "func": func,
        })
        return self
    
    def run(self, data):
        for step in self.steps:
            if step["type"] == "condition":
                result = step["condition"](data)
                branch = step["if_true"] if result else step["if_false"]
                if branch:
                    data = branch.run(data)
            elif step["type"] == "action":
                data = step["func"](data)
        return data


# Usage — Auto-fix or just report?
def severity_check(data):
    """Kiểm tra mức độ nghiêm trọng của issues"""
    return data.get("max_severity", "info") == "critical"

report_only = ConditionalWorkflow()
report_only.add_action("generate_report", generate_report)

auto_fix = ConditionalWorkflow()
auto_fix.add_action("auto_fix_issues", auto_fix_code)
auto_fix.add_action("run_tests", verify_fixes)

main_workflow = ConditionalWorkflow()
main_workflow.add_condition(
    "check_severity",
    severity_check,
    if_true=auto_fix,
    if_false=report_only,
)
```

---

## 2. Pipeline Design

### 2.1 Data Pipeline cho Code Processing

```
┌──────────────────────────────────────────────────────────────────┐
│                  CODE PROCESSING PIPELINE                         │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  INGEST                                                  │    │
│  │  ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐              │    │
│  │  │  Git │  │ File │  │  DB  │  │ API  │              │    │
│  │  │Events│  │Watch │  │      │  │      │              │    │
│  │  └──┬───┘  └──┬───┘  └──┬───┘  └──┬───┘              │    │
│  │     └─────────┴─────────┴─────────┘                    │    │
│  └────────────────────────┬───────────────────────────────┘    │
│                           ▼                                      │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  TRANSFORM                                               │    │
│  │  Parse → Normalize → Enrich → Validate                  │    │
│  └────────────────────────┬───────────────────────────────┘    │
│                           ▼                                      │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  ACT                                                     │    │
│  │  Lint → Test → Build → Review → Deploy                  │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

```python
from dataclasses import dataclass, field
from typing import Any, Callable, Dict, List, Optional
from datetime import datetime
import uuid

@dataclass
class PipelineEvent:
    """Event chạy qua pipeline"""
    id: str = field(default_factory=lambda: str(uuid.uuid4())[:8])
    type: str = ""
    payload: Dict[str, Any] = field(default_factory=dict)
    metadata: Dict[str, Any] = field(default_factory=dict)
    timestamp: str = field(
        default_factory=lambda: datetime.now().isoformat()
    )

@dataclass
class PipelineResult:
    """Kết quả sau khi chạy pipeline"""
    success: bool
    data: Any = None
    errors: List[str] = field(default_factory=list)
    metrics: Dict[str, Any] = field(default_factory=dict)


class Pipeline:
    """
    Pipeline linh hoạt — hỗ trợ transform, filter, và sink.
    
    Usage:
        pipeline = Pipeline("code-analysis")
        pipeline.add_transform(parse_file)
        pipeline.add_transform(analyze_complexity)
        pipeline.add_filter(lambda e: e.payload["complexity"] > 10)
        pipeline.add_sink(store_to_database)
        
        result = pipeline.run(input_events)
    """
    
    def __init__(self, name: str):
        self.name = name
        self.transforms: List[Callable] = []
        self.filters: List[Callable] = []
        self.sinks: List[Callable] = []
        self.hooks = {
            "before": [],
            "after": [],
            "on_error": [],
        }
    
    def add_transform(self, func: Callable) -> "Pipeline":
        self.transforms.append(func)
        return self
    
    def add_filter(self, func: Callable) -> "Pipeline":
        self.filters.append(func)
        return self
    
    def add_sink(self, func: Callable) -> "Pipeline":
        self.sinks.append(func)
        return self
    
    def on(self, event: str, func: Callable) -> "Pipeline":
        self.hooks[event].append(func)
        return self
    
    def run(self, data: Any) -> PipelineResult:
        errors = []
        start_time = datetime.now()
        
        # Before hooks
        for hook in self.hooks["before"]:
            hook(self.name, data)
        
        try:
            # Apply transforms
            for transform in self.transforms:
                data = transform(data)
            
            # Apply filters
            for f in self.filters:
                if not f(data):
                    return PipelineResult(
                        success=False,
                        errors=["Filter rejected data"],
                    )
            
            # Apply sinks
            for sink in self.sinks:
                sink(data)
            
            elapsed = (datetime.now() - start_time).total_seconds()
            
            # After hooks
            for hook in self.hooks["after"]:
                hook(self.name, data)
            
            return PipelineResult(
                success=True,
                data=data,
                metrics={"elapsed_seconds": elapsed},
            )
            
        except Exception as e:
            for hook in self.hooks["on_error"]:
                hook(self.name, e)
            errors.append(str(e))
            return PipelineResult(success=False, errors=errors)
```

---

## 3. State Machine

### 3.1 Finite State Machine cho Agent

```python
from enum import Enum
from dataclasses import dataclass, field
from typing import Dict, Callable, Optional, List

class AgentState(Enum):
    """Các trạng thái của AI coding agent"""
    IDLE = "idle"
    THINKING = "thinking"
    READING_CODE = "reading_code"
    WRITING_CODE = "writing_code"
    RUNNING_TESTS = "running_tests"
    FIXING_ERRORS = "fixing_errors"
    REVIEWING = "reviewing"
    DONE = "done"
    ERROR = "error"


@dataclass
class Transition:
    """Một transition (chuyển trạng thái)"""
    from_state: AgentState
    to_state: AgentState
    condition: Callable
    action: Optional[Callable] = None


class CodingAgentFSM:
    """
    Finite State Machine cho AI coding agent.
    
    Quản lý luồng trạng thái:
    IDLE → THINKING → READING_CODE → WRITING_CODE 
      → RUNNING_TESTS → (DONE | FIXING_ERRORS → RUNNING_TESTS)
    """
    
    def __init__(self):
        self.state = AgentState.IDLE
        self.transitions: List[Transition] = []
        self.history: List[Dict] = []
        self.context: Dict = {}
        self._setup_transitions()
    
    def _setup_transitions(self):
        """Thiết lập các transition rules"""
        self.transitions = [
            Transition(
                from_state=AgentState.IDLE,
                to_state=AgentState.THINKING,
                condition=lambda ctx: ctx.get("task") is not None,
            ),
            Transition(
                from_state=AgentState.THINKING,
                to_state=AgentState.READING_CODE,
                condition=lambda ctx: ctx.get("plan_ready"),
            ),
            Transition(
                from_state=AgentState.READING_CODE,
                to_state=AgentState.WRITING_CODE,
                condition=lambda ctx: ctx.get("code_read"),
            ),
            Transition(
                from_state=AgentState.WRITING_CODE,
                to_state=AgentState.RUNNING_TESTS,
                condition=lambda ctx: ctx.get("code_written"),
            ),
            Transition(
                from_state=AgentState.RUNNING_TESTS,
                to_state=AgentState.DONE,
                condition=lambda ctx: ctx.get("tests_passed"),
            ),
            Transition(
                from_state=AgentState.RUNNING_TESTS,
                to_state=AgentState.FIXING_ERRORS,
                condition=lambda ctx: ctx.get("tests_failed"),
            ),
            Transition(
                from_state=AgentState.FIXING_ERRORS,
                to_state=AgentState.RUNNING_TESTS,
                condition=lambda ctx: True,  # Luôn retry tests
            ),
        ]
    
    def transition(self):
        """Thử chuyển sang trạng thái tiếp theo"""
        for t in self.transitions:
            if t.from_state == self.state and t.condition(self.context):
                old_state = self.state
                if t.action:
                    t.action(self.context)
                self.state = t.to_state
                self.history.append({
                    "from": old_state.value,
                    "to": self.state.value,
                    "context_keys": list(self.context.keys()),
                })
                return True
        return False
    
    def run(self, task: Dict):
        """Chạy FSM cho đến khi hoàn thành hoặc lỗi"""
        self.context["task"] = task
        max_iterations = 20
        
        for i in range(max_iterations):
            print(f"  State: {self.state.value}")
            if self.state == AgentState.DONE:
                return self.context.get("result")
            if self.state == AgentState.ERROR:
                raise RuntimeError(
                    f"Agent error: {self.context.get('error')}"
                )
            if not self.transition():
                print(f"  ⚠️ No transition available from {self.state}")
                break
        
        return self.context.get("result")
```

---

## 4. Error Recovery

### 4.1 Retry Strategies

```python
import time
from functools import wraps
from typing import Type, Tuple

class RetryExhausted(Exception):
    """Khi retry đã hết số lần thử"""
    pass

def retry(
    max_attempts: int = 3,
    backoff_factor: float = 1.0,
    exceptions: Tuple[Type[Exception], ...] = (Exception,),
    on_retry: callable = None,
):
    """
    Decorator retry với exponential backoff.
    
    Args:
        max_attempts: Số lần thử tối đa
        backoff_factor: Hệ số chờ giữa các lần retry
        exceptions: Tuple các exception cần retry
        on_retry: Callback khi retry (log, metric...)
    
    Usage:
        @retry(max_attempts=3, exceptions=(ConnectionError,))
        def call_api():
            return requests.get("https://api.example.com")
    """
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            last_exception = None
            
            for attempt in range(1, max_attempts + 1):
                try:
                    return func(*args, **kwargs)
                    
                except exceptions as e:
                    last_exception = e
                    
                    if attempt < max_attempts:
                        wait_time = backoff_factor * (2 ** (attempt - 1))
                        if on_retry:
                            on_retry(attempt, e, wait_time)
                        print(
                            f"  ⚠️ Attempt {attempt}/{max_attempts} "
                            f"failed: {e}. "
                            f"Retrying in {wait_time:.1f}s..."
                        )
                        time.sleep(wait_time)
            
            raise RetryExhausted(
                f"All {max_attempts} attempts failed. "
                f"Last error: {last_exception}"
            )
        return wrapper
    return decorator


# Usage
@retry(
    max_attempts=3,
    backoff_factor=0.5,
    exceptions=(ConnectionError, TimeoutError),
    on_retry=lambda attempt, err, wait: print(
        f"Retry {attempt}: {err}"
    ),
)
def call_llm(prompt: str) -> str:
    response = ollama.generate(model="gemma3:12b", prompt=prompt)
    return response["response"]
```

### 4.2 Circuit Breaker Pattern

```python
import time
from enum import Enum

class CircuitState(Enum):
    CLOSED = "closed"      # Bình thường
    OPEN = "open"          # Lỗi — ngừng gọi
    HALF_OPEN = "half_open" # Thử lại sau khi chờ

class CircuitBreaker:
    """
    Circuit Breaker — tránh gọi service đang lỗi liên tục.
    
    Khi đủ số lỗi → mở circuit → chờ → thử lại.
    """
    
    def __init__(self, failure_threshold=5, recovery_timeout=30):
        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout
        self.state = CircuitState.CLOSED
        self.failure_count = 0
        self.last_failure_time = 0
    
    def call(self, func, *args, **kwargs):
        if self.state == CircuitState.OPEN:
            if time.time() - self.last_failure_time > self.recovery_timeout:
                self.state = CircuitState.HALF_OPEN
            else:
                raise CircuitOpenError("Circuit is open — skipping call")
        
        try:
            result = func(*args, **kwargs)
            self._on_success()
            return result
        except Exception as e:
            self._on_failure()
            raise
    
    def _on_success(self):
        self.failure_count = 0
        self.state = CircuitState.CLOSED
    
    def _on_failure(self):
        self.failure_count += 1
        self.last_failure_time = time.time()
        if self.failure_count >= self.failure_threshold:
            self.state = CircuitState.OPEN


class CircuitOpenError(Exception):
    pass
```

---

## 5. Observability

### 5.1 Structured Logging

```python
import json
import time
from datetime import datetime
from contextlib import contextmanager
from typing import Any, Dict

class WorkflowLogger:
    """
    Structured logger cho workflow — hỗ trợ JSON output.
    
    Ghi lại mỗi bước với timestamp, duration, status.
    """
    
    def __init__(self, workflow_name: str):
        self.workflow_name = workflow_name
        self.logs = []
    
    def log_step(self, step: str, status: str, 
                 data: Dict[str, Any] = None, duration: float = None):
        entry = {
            "workflow": self.workflow_name,
            "step": step,
            "status": status,
            "timestamp": datetime.now().isoformat(),
            "duration_ms": round(duration * 1000, 2) if duration else None,
            "data": data or {},
        }
        self.logs.append(entry)
        
        emoji = {"success": "✅", "error": "❌", "start": "▶"}
        print(f"{emoji.get(status, '•')} [{status}] {step}"
              f"{f' ({entry[\"duration_ms\"]}ms)' if duration else ''}")
    
    @contextmanager
    def track(self, step_name: str):
        """Context manager để tự động track duration"""
        start = time.time()
        self.log_step(step_name, "start")
        try:
            yield
            duration = time.time() - start
            self.log_step(step_name, "success", duration=duration)
        except Exception as e:
            duration = time.time() - start
            self.log_step(step_name, "error", 
                         data={"error": str(e)}, duration=duration)
            raise
    
    def export_json(self, path: str = None):
        output = json.dumps(self.logs, indent=2, ensure_ascii=False)
        if path:
            with open(path, "w") as f:
                f.write(output)
        return output


# Usage
logger = WorkflowLogger("code-review")

with logger.track("parse_pr"):
    pr_data = parse_pull_request(42)

with logger.track("analyze_diff"):
    analysis = analyze_code_diff(pr_data)

with logger.track("generate_review"):
    review = generate_review(analysis)

logger.export_json("./logs/code-review.json")
```

### 5.2 Metrics Collection

```python
from dataclasses import dataclass, field
from typing import Dict, List

@dataclass
class StepMetrics:
    """Metrics cho một bước trong workflow"""
    name: str
    total_runs: int = 0
    success_count: int = 0
    failure_count: int = 0
    total_duration_ms: float = 0
    
    @property
    def avg_duration_ms(self) -> float:
        return (self.total_duration_ms / self.total_runs 
                if self.total_runs else 0)
    
    @property
    def success_rate(self) -> float:
        return (self.success_count / self.total_runs 
                if self.total_runs else 0)


class MetricsCollector:
    """Thu thập và tổng hợp metrics cho workflow"""
    
    def __init__(self):
        self.steps: Dict[str, StepMetrics] = {}
    
    def record(self, step_name: str, duration_ms: float, 
               success: bool):
        if step_name not in self.steps:
            self.steps[step_name] = StepMetrics(name=step_name)
        
        s = self.steps[step_name]
        s.total_runs += 1
        s.total_duration_ms += duration_ms
        if success:
            s.success_count += 1
        else:
            s.failure_count += 1
    
    def summary(self) -> str:
        lines = ["📊 Workflow Metrics Summary", "=" * 40]
        for name, m in self.steps.items():
            lines.append(
                f"  {name}: "
                f"{m.total_runs} runs, "
                f"{m.success_rate:.0%} success, "
                f"avg {m.avg_duration_ms:.0f}ms"
            )
        return "\n".join(lines)
```

---

## 6. Best Practices

### 6.1 Design Principles

```
┌──────────────────────────────────────────────────────────────────┐
│                 WORKFLOW DESIGN PRINCIPLES                        │
│                                                                  │
│  1. IDEMPOTENCY                                                  │
│     Mỗi bước phải an toàn khi chạy lại nhiều lần              │
│     → Dùng UUID/idempotency key cho mutations                   │
│                                                                  │
│  2. OBSERVABILITY                                                │
│     Mọi bước phải log input/output                              │
│     → Dùng structured logging + metrics                         │
│                                                                  │
│  3. SEPARATION OF CONCERNS                                       │
│     Mỗi bước làm 1 việc duy nhất                               │
│     → Dễ test, dễ thay thế, dễ debug                           │
│                                                                  │
│  4. PROGRESSIVE DISCLOSURE                                       │
│     Bắt đầu đơn giản, thêm complexity khi cần                  │
│     → Sequential trước, Parallel khi bottleneck                  │
│                                                                  │
│  5. FAIL-FAST + RECOVER                                          │
│     Phát hiện lỗi sớm, có strategy recovery                     │
│     → Retry với backoff, circuit breaker                        │
│                                                                  │
│  6. STATE EXTERNALIZATION                                        │
│     Lưu state ra ngoài memory                                   │
│     → Có thể resume từ checkpoint                               │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 6.2 Checklist

```
Workflow Design Checklist:
□ Mỗi step có input/output schema rõ ràng
□ Có structured logging cho mỗi step
□ Có error handling + retry strategy
□ Có timeout cho mỗi step
□ Có metrics để theo dõi performance
□ Có checkpoint để resume khi crash
□ Có validation giữa các steps
□ Có dead letter queue cho events fail
□ Có monitoring alerts cho anomalies
□ Có documentation cho flow tổng thể
```

---

## Tài Liệu Tham Khảo

- [LangChain Expression Language](https://python.langchain.com/docs/expression_language/)
- [Apache Airflow](https://airflow.apache.org/)
- [Temporal Workflow](https://temporal.io/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)