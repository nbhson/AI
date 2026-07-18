# ⚙️ VII. Workflow

> **"Workflow tốt không phải là workflow phức tạp — mà là workflow mỗi bước đều rõ ràng, có thể test, và recover được"**

### Tại Sao Workflow Quan Trọng?

> *"Agent không có workflow giống như đầu bếp không có thực đơn — có nguyên liệu, có dụng cụ, nhưng món ra tùy hứng."*

#### Bằng chứng nghiên cứu:

1. **Google DeepMind (2025)**: Structured workflows giảm **45% task failure rate** trong multi-step coding agents.
2. **Anthropic (2025)**: Workflow engine với retry + checkpoint giảm **70% data loss** khi agent gặp lỗi.
3. **LangGraph Benchmark (2025)**: State machine workflows xử lý **3x complex tasks** tốt hơn simple sequential chains.

#### Triết lý cốt lõi:

```
Workflow = Trigger → Plan → Execute → Validate → Deploy → Feedback
```

Mỗi bước phải có **state management**, **error recovery**, và **observability**. Workflow không chỉ là "thứ tự steps" — nó là hệ thống quản lý **toàn bộ lifecycle** của một task.

**Analogies**: Workflow giống hệ thống giao thông — đèn tín hiệu (guardrails), tuyến đường (steps), trung tâm kiểm soát (orchestrator), và camera giám sát (logging).

**Nếu bỏ qua**: Agent thực hiện tasks lộn xộn, không thể recover khi lỗi, không track được tiến độ, và debug trở thành nightmare.

## Tổng Quan

**Workflow** là quá trình **thiết kế và tổ chức các bước thực hiện** để hoàn thành một tác vụ coding phức tạp. Trong Harness Engineering, Workflow Engine là **"trung tâm điều phối"** — quản lý toàn bộ luồng từ trigger → plan → execute → validate → deploy, với observability, error recovery, và state management.

```
┌──────────────────────────────────────────────────────────────────┐
│                         WORKFLOW ENGINE                           │
│                                                                  │
│  Trigger (Event/Schedule/Webhook)                                │
│       │                                                          │
│       ▼                                                          │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │  WORKFLOW ORCHESTRATOR                                     │  │
│  │                                                            │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  │  │
│  │  │  State   │  │  Task    │  │  Guard   │  │  Retry   │  │  │
│  │  │  Machine │  │  Queue   │  │  Rails   │  │  Engine  │  │  │
│  │  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘  │  │
│  │       └──────────────┼──────────────┼──────────────┘        │  │
│  │                      ▼                                       │  │
│  │  ┌──────────────────────────────────────────────────┐      │  │
│  │  │              EXECUTION ENGINE                     │      │  │
│  │  │  Sequential | Parallel | DAG | Conditional       │      │  │
│  │  └──────────────────────┬───────────────────────────┘      │  │
│  │                         │                                    │  │
│  │  ┌──────────────────────┼───────────────────────────┐      │  │
│  │  │        OBSERVABILITY & RECOVERY                   │      │  │
│  │  │  Tracing | Metrics | Circuit Breaker | Saga      │      │  │
│  │  └──────────────────────────────────────────────────┘      │  │
│  └────────────────────────────────────────────────────────────┘  │
│       │                                                          │
│       ▼                                                          │
│  Result + Logs + Metrics + Artifacts                             │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │  HARNESS INTEGRATION                                       │  │
│  │  Memory ←→ Guardrails ←→ Feedback ←→ Observability       │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## Nội Dung

| # | Chủ đề | Mô tả |
|---|--------|-------|
| 1 | [Workflow Patterns](#1-workflow-patterns) | Sequential, Parallel, DAG, Event-driven |
| 2 | [Pipeline Design](#2-pipeline-design) | Transform, Branching, Fan-out/Fan-in |
| 3 | [State Machine](#3-state-machine) | FSM nâng cao, hierarchical states |
| 4 | [Error Recovery](#4-error-recovery) | Retry, Circuit Breaker, Saga pattern |
| 5 | [Observability](#5-observability) | Structured logging, distributed tracing |
| 6 | [Workflow Orchestration](#6-workflow-orchestration) | Engine tổng hợp |
| 7 | [Workflow Testing](#7-workflow-testing) | Unit, integration, chaos testing |
| 8 | [Harness Integration](#8-harness-integration) | TypeScript interfaces |
| 9 | [Case Studies](#9-case-studies) | GitHub Actions, Airflow, Temporal |
| 10 | [Design Principles](#10-design-principles) | SOLID cho workflows |
| 11 | [Best Practices](#11-best-practices) | DO/DON'T chi tiết |
| 12 | [Tương Lai](#12-tương-lai) | Xu hướng 2026-2028 |

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
from dataclasses import dataclass, field
from typing import Any, Callable, Dict, List, Optional
from datetime import datetime
import uuid
import time
import json

class WorkflowError(Exception):
    """Lỗi trong workflow"""
    pass

class SequentialWorkflow:
    """Workflow chạy tuần tự — mỗi bước đầu vào là output bước trước"""
    
    def __init__(self, name: str = "sequential"):
        self.name = name
        self.steps = []
        self.state = {}
        self.history = []
        self.hooks = {"before": [], "after": [], "on_error": [], "on_step": []}
    
    def add_step(self, name: str, func: Callable, **kwargs):
        self.steps.append({"name": name, "func": func, "kwargs": kwargs})
        return self
    
    def on(self, event: str, func: Callable):
        if event in self.hooks:
            self.hooks[event].append(func)
        return self
    
    def run(self, initial_input: Any) -> Any:
        current_input = initial_input
        self.state["status"] = "running"
        self.state["start_time"] = time.time()
        
        for hook in self.hooks["before"]:
            hook(self.name, initial_input)
        
        for i, step in enumerate(self.steps):
            step_name = step["name"]
            step_start = time.time()
            
            for hook in self.hooks["on_step"]:
                hook(step_name, i, len(self.steps))
            
            try:
                result = step["func"](current_input, **step["kwargs"])
                step_duration = time.time() - step_start
                
                self.history.append({
                    "step": step_name,
                    "input_type": type(current_input).__name__,
                    "output_type": type(result).__name__,
                    "status": "success",
                    "duration_ms": round(step_duration * 1000, 2),
                    "timestamp": datetime.now().isoformat(),
                })
                
                current_input = result
                
            except Exception as e:
                step_duration = time.time() - step_start
                error_entry = {
                    "step": step_name,
                    "error": str(e),
                    "error_type": type(e).__name__,
                    "status": "failed",
                    "duration_ms": round(step_duration * 1000, 2),
                    "timestamp": datetime.now().isoformat(),
                }
                self.history.append(error_entry)
                self.state["status"] = "failed"
                self.state["failed_step"] = step_name
                
                for hook in self.hooks["on_error"]:
                    hook(step_name, e)
                
                raise WorkflowError(f"Step '{step_name}' failed: {e}")
        
        total_duration = time.time() - self.state["start_time"]
        self.state["status"] = "completed"
        self.state["total_duration_ms"] = round(total_duration * 1000, 2)
        
        for hook in self.hooks["after"]:
            hook(self.name, current_input)
        
        return current_input
    
    def get_history(self) -> List[Dict]:
        return self.history
    
    def get_summary(self) -> Dict:
        total = len(self.history)
        success = sum(1 for h in self.history if h["status"] == "success")
        total_ms = sum(h.get("duration_ms", 0) for h in self.history)
        
        return {
            "workflow": self.name,
            "total_steps": total,
            "successful": success,
            "failed": total - success,
            "total_duration_ms": total_ms,
            "status": self.state.get("status", "unknown"),
        }
```

### 1.2 Parallel Workflow (Song Song)

```python
import asyncio
from concurrent.futures import ThreadPoolExecutor, as_completed

class ParallelWorkflow:
    """Workflow chạy song song — nhiều bước cùng lúc"""
    
    def __init__(self, name: str = "parallel", max_workers: int = 4):
        self.name = name
        self.parallel_groups = []
        self.max_workers = max_workers
        self.history = []
    
    def add_parallel_group(self, steps):
        """
        Thêm một nhóm các bước chạy song song.
        steps: list of (name, func, args_dict)
        """
        self.parallel_groups.append({"type": "parallel", "steps": steps})
        return self
    
    def add_sequential_step(self, name: str, func: Callable):
        self.parallel_groups.append({
            "type": "sequential",
            "steps": [(name, func, {})],
        })
        return self
    
    def run(self, initial_input: Any) -> Any:
        current_input = initial_input
        
        for group_idx, group in enumerate(self.parallel_groups):
            if group["type"] == "sequential":
                name, func, args = group["steps"][0]
                current_input = func(current_input, **args)
            else:
                # Parallel group
                results = {}
                with ThreadPoolExecutor(max_workers=self.max_workers) as executor:
                    futures = {}
                    for name, func, args in group["steps"]:
                        future = executor.submit(func, current_input, **args)
                        futures[future] = name
                    
                    for future in as_completed(futures):
                        name = futures[future]
                        try:
                            results[name] = future.result()
                        except Exception as e:
                            results[name] = {"error": str(e)}
                
                current_input = results
        
        return current_input
    
    def run_async(self, initial_input: Any) -> Any:
        """Async version for I/O-bound workflows"""
        return asyncio.run(self._run_async_impl(initial_input))
    
    async def _run_async_impl(self, initial_input: Any) -> Any:
        current_input = initial_input
        
        for group in self.parallel_groups:
            if group["type"] == "sequential":
                name, func, args = group["steps"][0]
                if asyncio.iscoroutinefunction(func):
                    current_input = await func(current_input, **args)
                else:
                    current_input = func(current_input, **args)
            else:
                tasks = []
                for name, func, args in group["steps"]:
                    if asyncio.iscoroutinefunction(func):
                        tasks.append(func(current_input, **args))
                    else:
                        loop = asyncio.get_event_loop()
                        tasks.append(loop.run_in_executor(None, func, current_input, **args))
                
                results = await asyncio.gather(*tasks, return_exceptions=True)
                current_input = {
                    group["steps"][i][0]: r 
                    for i, r in enumerate(results)
                }
        
        return current_input
```

### 1.3 DAG Workflow (Directed Acyclic Graph)

```python
class DAGWorkflow:
    """
    Workflow dựa trên Dependency Graph (DAG).
    
    Cho phép chạy các node độc lập song song,
    và chờ các dependency hoàn thành trước khi chạy tiếp.
    
    Ví dụ:
        A ──► C ──► E
        B ──► D ──► F
        
    A và B chạy song song → C chờ A, D chờ B → E chờ C, F chờ D
    """
    
    def __init__(self, name: str = "dag"):
        self.name = name
        self.nodes: Dict[str, Dict] = {}
        self.edges: Dict[str, List[str]] = {}  # node -> [dependents]
        self.reverse_edges: Dict[str, List[str]] = {}  # node -> [dependencies]
        self.history = []
    
    def add_node(self, name: str, func: Callable, depends_on: List[str] = None):
        """Add a node with optional dependencies"""
        self.nodes[name] = {"func": func, "name": name}
        self.edges[name] = []
        self.reverse_edges[name] = depends_on or []
        
        for dep in (depends_on or []):
            if dep not in self.edges:
                self.edges[dep] = []
            self.edges[dep].append(name)
        
        return self
    
    def topological_sort(self) -> List[List[str]]:
        """
        Topological sort — trả về danh sách levels
        Mỗi level chứa các nodes có thể chạy song song
        """
        in_degree = {node: 0 for node in self.nodes}
        for node, deps in self.reverse_edges.items():
            for dep in deps:
                in_degree[node] += 1
        
        levels = []
        queue = [n for n, d in in_degree.items() if d == 0]
        
        while queue:
            levels.append(sorted(queue))
            next_queue = []
            for node in queue:
                for dependent in self.edges.get(node, []):
                    in_degree[dependent] -= 1
                    if in_degree[dependent] == 0:
                        next_queue.append(dependent)
            queue = next_queue
        
        # Validate: check for cycles
        total_nodes = sum(len(level) for level in levels)
        if total_nodes != len(self.nodes):
            raise WorkflowError("Cycle detected in DAG!")
        
        return levels
    
    def run(self, initial_data: Any = None) -> Dict:
        """Execute DAG level by level"""
        levels = self.topological_sort()
        results = {}
        node_results = {}
        
        for level_idx, level in enumerate(levels):
            if len(level) == 1:
                # Single node — run directly
                node_name = level[0]
                deps = {dep: results[dep] for dep in self.reverse_edges[node_name] if dep in results}
                input_data = {**deps, "_original": initial_data} if deps else initial_data
                
                result = self.nodes[node_name]["func"](input_data)
                results[node_name] = result
                node_results[node_name] = {"status": "success", "level": level_idx}
            else:
                # Parallel level
                with ThreadPoolExecutor(max_workers=len(level)) as executor:
                    futures = {}
                    for node_name in level:
                        deps = {dep: results[dep] for dep in self.reverse_edges[node_name] if dep in results}
                        input_data = {**deps, "_original": initial_data} if deps else initial_data
                        future = executor.submit(self.nodes[node_name]["func"], input_data)
                        futures[future] = node_name
                    
                    for future in as_completed(futures):
                        node_name = futures[future]
                        try:
                            result = future.result()
                            results[node_name] = result
                            node_results[node_name] = {"status": "success", "level": level_idx}
                        except Exception as e:
                            node_results[node_name] = {"status": "failed", "error": str(e), "level": level_idx}
        
        return {
            "results": results,
            "node_status": node_results,
            "levels": levels,
            "total_nodes": len(self.nodes),
            "successful": sum(1 for v in node_results.values() if v["status"] == "success"),
        }
    
    def validate(self) -> Dict:
        """Validate DAG structure"""
        issues = []
        
        # Check for cycles using DFS
        visited = set()
        rec_stack = set()
        
        def has_cycle(node):
            visited.add(node)
            rec_stack.add(node)
            for dep in self.reverse_edges.get(node, []):
                if dep not in visited:
                    if has_cycle(dep):
                        return True
                elif dep in rec_stack:
                    issues.append(f"Cycle: {node} → {dep}")
                    return True
            rec_stack.discard(node)
            return False
        
        for node in self.nodes:
            if node not in visited:
                has_cycle(node)
        
        # Check for missing dependencies
        for node, deps in self.reverse_edges.items():
            for dep in deps:
                if dep not in self.nodes:
                    issues.append(f"Missing dependency: {dep} (required by {node})")
        
        # Check for orphan nodes
        all_deps = set()
        for deps in self.reverse_edges.values():
            all_deps.update(deps)
        roots = [n for n in self.nodes if n not in all_deps and not self.reverse_edges.get(n)]
        
        return {
            "valid": len(issues) == 0,
            "issues": issues,
            "roots": roots,
            "levels": len(self.topological_sort()),
        }
```

### 1.4 Event-Driven Workflow

```python
import queue
from typing import Callable, Dict, List, Optional
from datetime import datetime

class EventDrivenWorkflow:
    """
    Workflow reacts to events.
    
    Events trigger handlers, which may emit new events.
    Supports: pub/sub, event filtering, event chains.
    """
    
    def __init__(self, name: str = "event-driven"):
        self.name = name
        self.handlers: Dict[str, List[Callable]] = {}
        self.event_log: List[Dict] = []
        self.event_queue = queue.Queue()
        self._running = False
        self._max_events = 1000
    
    def on(self, event_type: str, handler: Callable):
        """Register handler for event type"""
        if event_type not in self.handlers:
            self.handlers[event_type] = []
        self.handlers[event_type].append(handler)
        return self
    
    def emit(self, event_type: str, data: Any = None):
        """Emit an event"""
        event = {
            "type": event_type,
            "data": data,
            "timestamp": datetime.now().isoformat(),
            "id": str(uuid.uuid4())[:8],
        }
        self.event_queue.put(event)
        self.event_log.append(event)
    
    def process_event(self, event: Dict):
        """Process a single event"""
        event_type = event["type"]
        handlers = self.handlers.get(event_type, [])
        
        if not handlers:
            self.event_log.append({
                **event,
                "status": "unhandled",
                "timestamp": datetime.now().isoformat(),
            })
            return
        
        for handler in handlers:
            try:
                result = handler(event["data"])
                self.event_log.append({
                    **event,
                    "handler": handler.__name__,
                    "status": "processed",
                    "result": str(result)[:200] if result else None,
                    "timestamp": datetime.now().isoformat(),
                })
            except Exception as e:
                self.event_log.append({
                    **event,
                    "handler": handler.__name__,
                    "status": "error",
                    "error": str(e),
                    "timestamp": datetime.now().isoformat(),
                })
    
    def run(self):
        """Process all queued events"""
        self._running = True
        event_count = 0
        
        while self._running and not self.event_queue.empty():
            if event_count >= self._max_events:
                break
            
            try:
                event = self.event_queue.get(timeout=1)
                self.process_event(event)
                event_count += 1
            except queue.Empty:
                break
        
        self._running = False
    
    def stop(self):
        self._running = False
    
    def get_stats(self) -> Dict:
        total = len(self.event_log)
        processed = sum(1 for e in self.event_log if e.get("status") == "processed")
        errors = sum(1 for e in self.event_log if e.get("status") == "error")
        
        event_types = {}
        for e in self.event_log:
            t = e.get("type", "unknown")
            event_types[t] = event_types.get(t, 0) + 1
        
        return {
            "total_events": total,
            "processed": processed,
            "errors": errors,
            "event_types": event_types,
            "queue_size": self.event_queue.qsize(),
        }
```

---

## 2. Pipeline Design

### 2.1 Data Pipeline với Branching

```python
@dataclass
class PipelineEvent:
    id: str = field(default_factory=lambda: str(uuid.uuid4())[:8])
    type: str = ""
    payload: Dict[str, Any] = field(default_factory=dict)
    metadata: Dict[str, Any] = field(default_factory=dict)
    timestamp: str = field(default_factory=lambda: datetime.now().isoformat())

@dataclass
class PipelineResult:
    success: bool
    data: Any = None
    errors: List[str] = field(default_factory=list)
    metrics: Dict[str, Any] = field(default_factory=dict)


class Pipeline:
    """
    Advanced Pipeline với branching, transforms, filters, và sinks.
    
    Features:
    - Transform chain
    - Conditional branching
    - Fan-out / Fan-in
    - Error handling per stage
    - Metrics collection
    """
    
    def __init__(self, name: str):
        self.name = name
        self.stages: List[Dict] = []
        self.hooks = {"before": [], "after": [], "on_error": [], "on_stage": []}
        self.metrics = PipelineMetrics()
    
    def add_transform(self, func: Callable, name: str = None) -> "Pipeline":
        self.stages.append({
            "type": "transform",
            "name": name or func.__name__,
            "func": func,
        })
        return self
    
    def add_filter(self, func: Callable, name: str = None) -> "Pipeline":
        self.stages.append({
            "type": "filter",
            "name": name or "filter",
            "func": func,
        })
        return self
    
    def add_branch(self, condition: Callable, true_pipeline: "Pipeline", 
                   false_pipeline: Optional["Pipeline"] = None, name: str = "branch") -> "Pipeline":
        self.stages.append({
            "type": "branch",
            "name": name,
            "condition": condition,
            "true_pipeline": true_pipeline,
            "false_pipeline": false_pipeline,
        })
        return self
    
    def add_fan_out(self, tasks: List[Dict], aggregator: Callable = None) -> "Pipeline":
        """Fan-out: split into parallel tasks, then aggregate"""
        self.stages.append({
            "type": "fan_out",
            "name": "fan_out",
            "tasks": tasks,
            "aggregator": aggregator or (lambda results: results),
        })
        return self
    
    def add_sink(self, func: Callable, name: str = None) -> "Pipeline":
        self.stages.append({
            "type": "sink",
            "name": name or func.__name__,
            "func": func,
        })
        return self
    
    def add_validator(self, func: Callable, name: str = None) -> "Pipeline":
        self.stages.append({
            "type": "validator",
            "name": name or "validate",
            "func": func,
        })
        return self
    
    def on(self, event: str, func: Callable) -> "Pipeline":
        if event in self.hooks:
            self.hooks[event].append(func)
        return self
    
    def run(self, data: Any) -> PipelineResult:
        start_time = time.time()
        errors = []
        
        for hook in self.hooks["before"]:
            hook(self.name, data)
        
        try:
            for stage in self.stages:
                stage_start = time.time()
                stage_name = stage["name"]
                
                for hook in self.hooks["on_stage"]:
                    hook(stage_name, data)
                
                try:
                    if stage["type"] == "transform":
                        data = stage["func"](data)
                    
                    elif stage["type"] == "filter":
                        if not stage["func"](data):
                            return PipelineResult(
                                success=False,
                                errors=[f"Filter '{stage_name}' rejected data"],
                                metrics=self.metrics.get_summary(),
                            )
                    
                    elif stage["type"] == "branch":
                        condition_result = stage["condition"](data)
                        if condition_result and stage["true_pipeline"]:
                            result = stage["true_pipeline"].run(data)
                            data = result.data
                        elif not condition_result and stage["false_pipeline"]:
                            result = stage["false_pipeline"].run(data)
                            data = result.data
                    
                    elif stage["type"] == "fan_out":
                        task_results = {}
                        with ThreadPoolExecutor(max_workers=4) as executor:
                            futures = {}
                            for task in stage["tasks"]:
                                f = executor.submit(task["func"], data)
                                futures[f] = task["name"]
                            for f in as_completed(futures):
                                task_results[futures[f]] = f.result()
                        data = stage["aggregator"](task_results)
                    
                    elif stage["type"] == "validator":
                        valid = stage["func"](data)
                        if not valid:
                            return PipelineResult(
                                success=False,
                                errors=[f"Validation '{stage_name}' failed"],
                                metrics=self.metrics.get_summary(),
                            )
                    
                    elif stage["type"] == "sink":
                        stage["func"](data)
                    
                    stage_duration = (time.time() - stage_start) * 1000
                    self.metrics.record(stage_name, stage_duration, True)
                    
                except Exception as e:
                    stage_duration = (time.time() - stage_start) * 1000
                    self.metrics.record(stage_name, stage_duration, False)
                    errors.append(f"{stage_name}: {str(e)}")
                    
                    for hook in self.hooks["on_error"]:
                        hook(stage_name, e)
                    
                    if stage["type"] != "sink":
                        raise WorkflowError(f"Pipeline stage '{stage_name}' failed: {e}")
            
            total_duration = (time.time() - start_time) * 1000
            
            for hook in self.hooks["after"]:
                hook(self.name, data)
            
            return PipelineResult(
                success=True,
                data=data,
                metrics=self.metrics.get_summary(),
            )
            
        except Exception as e:
            return PipelineResult(
                success=False,
                errors=errors or [str(e)],
                metrics=self.metrics.get_summary(),
            )


class PipelineMetrics:
    def __init__(self):
        self.stages: Dict[str, Dict] = {}
    
    def record(self, stage_name: str, duration_ms: float, success: bool):
        if stage_name not in self.stages:
            self.stages[stage_name] = {
                "runs": 0, "success": 0, "fail": 0, "total_ms": 0
            }
        s = self.stages[stage_name]
        s["runs"] += 1
        s["total_ms"] += duration_ms
        if success:
            s["success"] += 1
        else:
            s["fail"] += 1
    
    def get_summary(self) -> Dict:
        return {
            stage: {
                "runs": s["runs"],
                "avg_ms": round(s["total_ms"] / s["runs"], 2) if s["runs"] else 0,
                "success_rate": round(s["success"] / s["runs"], 2) if s["runs"] else 0,
            }
            for stage, s in self.stages.items()
        }
```

---

## 3. State Machine

### 3.1 Hierarchical State Machine

```python
from enum import Enum
from dataclasses import dataclass, field
from typing import Dict, Callable, Optional, List, Any

class AgentState(Enum):
    IDLE = "idle"
    THINKING = "thinking"
    PLANNING = "planning"
    READING_CODE = "reading_code"
    WRITING_CODE = "writing_code"
    RUNNING_TESTS = "running_tests"
    FIXING_ERRORS = "fixing_errors"
    REVIEWING = "reviewing"
    COMMITTING = "committing"
    DONE = "done"
    ERROR = "error"
    WAITING_APPROVAL = "waiting_approval"
    ROLLING_BACK = "rolling_back"


@dataclass
class Transition:
    from_state: AgentState
    to_state: AgentState
    condition: Callable[[Dict], bool]
    action: Optional[Callable] = None
    guard: Optional[Callable] = None  # Extra validation
    name: str = ""


class HierarchicalStateMachine:
    """
    Finite State Machine nâng cao cho AI coding agent.
    
    Features:
    - Hierarchical states (parent/child)
    - Guards on transitions
    - Actions on transitions
    - History tracking
    - Max iteration protection
    - State timeout handling
    """
    
    def __init__(self, name: str = "agent"):
        self.name = name
        self.state = AgentState.IDLE
        self.transitions: List[Transition] = []
        self.history: List[Dict] = []
        self.context: Dict = {}
        self.max_history = 100
        self._state_enter_time = time.time()
        self._state_timeouts: Dict[AgentState, float] = {}
    
    def set_timeout(self, state: AgentState, seconds: float):
        """Set timeout for a state (auto-transition to ERROR)"""
        self._state_timeouts[state] = seconds
    
    def add_transition(self, transition: Transition):
        self.transitions.append(transition)
        return self
    
    def can_transition(self, to_state: AgentState) -> bool:
        """Check if transition to target state is possible"""
        for t in self.transitions:
            if t.from_state == self.state and t.to_state == to_state:
                if t.guard and not t.guard(self.context):
                    return False
                return t.condition(self.context)
        return False
    
    def transition(self) -> Optional[Transition]:
        """Try all transitions from current state"""
        for t in self.transitions:
            if t.from_state == self.state and t.condition(self.context):
                # Check guard
                if t.guard and not t.guard(self.context):
                    continue
                
                old_state = self.state
                
                # Execute action
                if t.action:
                    t.action(self.context)
                
                # Record history
                entry = {
                    "from": old_state.value,
                    "to": t.to_state.value,
                    "transition": t.name or f"{old_state.value} → {t.to_state.value}",
                    "context_keys": list(self.context.keys()),
                    "timestamp": datetime.now().isoformat(),
                    "state_duration_ms": round((time.time() - self._state_enter_time) * 1000, 2),
                }
                self.history.append(entry)
                
                # Enforce history limit
                if len(self.history) > self.max_history:
                    self.history = self.history[-self.max_history:]
                
                self.state = t.to_state
                self._state_enter_time = time.time()
                return t
        
        return None
    
    def check_timeout(self) -> bool:
        """Check if current state has timed out"""
        if self.state in self._state_timeouts:
            elapsed = time.time() - self._state_enter_time
            if elapsed > self._state_timeouts[self.state]:
                self.context["error"] = f"State {self.state.value} timed out after {elapsed:.1f}s"
                self.state = AgentState.ERROR
                return True
        return False
    
    def run(self, task: Dict, max_iterations: int = 50) -> Any:
        """Run FSM until DONE or ERROR"""
        self.context["task"] = task
        self._state_enter_time = time.time()
        
        for i in range(max_iterations):
            # Check timeout
            if self.check_timeout():
                break
            
            if self.state == AgentState.DONE:
                return self.context.get("result")
            
            if self.state == AgentState.ERROR:
                raise RuntimeError(f"Agent error: {self.context.get('error')}")
            
            transition = self.transition()
            if not transition:
                break
        
        return self.context.get("result")
    
    def get_state_path(self) -> List[str]:
        """Get the path of states visited"""
        return [h["from"] for h in self.history] + [self.state.value]
    
    def get_stats(self) -> Dict:
        transitions_count = len(self.history)
        state_counts = {}
        for h in self.history:
            state = h["from"]
            state_counts[state] = state_counts.get(state, 0) + 1
        
        return {
            "current_state": self.state.value,
            "total_transitions": transitions_count,
            "state_visits": state_counts,
            "avg_transition_ms": (
                sum(h.get("state_duration_ms", 0) for h in self.history) / transitions_count
                if transitions_count else 0
            ),
        }
```

---

## 4. Error Recovery

### 4.1 Retry Strategies

```python
import time
from functools import wraps
from typing import Type, Tuple, Optional

class RetryExhausted(Exception):
    pass

def retry(
    max_attempts: int = 3,
    backoff_factor: float = 1.0,
    max_backoff: float = 60.0,
    exceptions: Tuple[Type[Exception], ...] = (Exception,),
    on_retry: Callable = None,
    jitter: bool = True,
):
    """
    Decorator retry với exponential backoff + jitter.
    
    Jitter prevents thundering herd when many clients retry simultaneously.
    """
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            import random
            last_exception = None
            
            for attempt in range(1, max_attempts + 1):
                try:
                    return func(*args, **kwargs)
                except exceptions as e:
                    last_exception = e
                    
                    if attempt < max_attempts:
                        wait_time = backoff_factor * (2 ** (attempt - 1))
                        if jitter:
                            wait_time *= (0.5 + random.random())
                        wait_time = min(wait_time, max_backoff)
                        
                        if on_retry:
                            on_retry(attempt, e, wait_time)
                        
                        time.sleep(wait_time)
            
            raise RetryExhausted(
                f"All {max_attempts} attempts failed. Last error: {last_exception}"
            )
        return wrapper
    return decorator


def async_retry(
    max_attempts: int = 3,
    backoff_factor: float = 1.0,
    exceptions: Tuple[Type[Exception], ...] = (Exception,),
):
    """Async version of retry decorator"""
    import asyncio
    import random
    
    def decorator(func):
        @wraps(func)
        async def wrapper(*args, **kwargs):
            last_exception = None
            for attempt in range(1, max_attempts + 1):
                try:
                    return await func(*args, **kwargs)
                except exceptions as e:
                    last_exception = e
                    if attempt < max_attempts:
                        wait = backoff_factor * (2 ** (attempt - 1)) * (0.5 + random.random())
                        await asyncio.sleep(wait)
            raise RetryExhausted(f"All {max_attempts} attempts failed: {last_exception}")
        return wrapper
    return decorator
```

### 4.2 Circuit Breaker Pattern

```python
class CircuitState(Enum):
    CLOSED = "closed"
    OPEN = "open"
    HALF_OPEN = "half_open"

class CircuitBreaker:
    """
    Circuit Breaker — tránh gọi service đang lỗi liên tục.
    
    CLOSED → OPEN (khi đủ lỗi) → HALF_OPEN (sau recovery timeout) → CLOSED/OPEN
    """
    
    def __init__(self, failure_threshold: int = 5, recovery_timeout: float = 30.0,
                 half_open_max_calls: int = 1):
        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout
        self.half_open_max_calls = half_open_max_calls
        self.state = CircuitState.CLOSED
        self.failure_count = 0
        self.success_count = 0
        self.last_failure_time = 0
        self.half_open_calls = 0
        self.history: List[Dict] = []
    
    def call(self, func: Callable, *args, **kwargs):
        if self.state == CircuitState.OPEN:
            if time.time() - self.last_failure_time > self.recovery_timeout:
                self.state = CircuitState.HALF_OPEN
                self.half_open_calls = 0
                self._log_transition("OPEN", "HALF_OPEN")
            else:
                raise CircuitOpenError(
                    f"Circuit is OPEN. Retry after {self._time_until_half_open():.0f}s"
                )
        
        if self.state == CircuitState.HALF_OPEN:
            if self.half_open_calls >= self.half_open_max_calls:
                raise CircuitOpenError("Half-open limit reached")
            self.half_open_calls += 1
        
        try:
            result = func(*args, **kwargs)
            self._on_success()
            return result
        except Exception as e:
            self._on_failure()
            raise
    
    def _on_success(self):
        if self.state == CircuitState.HALF_OPEN:
            self.success_count += 1
            if self.success_count >= 2:
                self.state = CircuitState.CLOSED
                self.failure_count = 0
                self.success_count = 0
                self._log_transition("HALF_OPEN", "CLOSED")
        else:
            self.failure_count = 0
    
    def _on_failure(self):
        self.failure_count += 1
        self.last_failure_time = time.time()
        self.success_count = 0
        
        if self.failure_count >= self.failure_threshold:
            if self.state == CircuitState.HALF_OPEN:
                self.state = CircuitState.OPEN
                self._log_transition("HALF_OPEN", "OPEN")
            elif self.state == CircuitState.CLOSED:
                self.state = CircuitState.OPEN
                self._log_transition("CLOSED", "OPEN")
    
    def _time_until_half_open(self) -> float:
        return max(0, self.recovery_timeout - (time.time() - self.last_failure_time))
    
    def _log_transition(self, from_state: str, to_state: str):
        self.history.append({
            "from": from_state,
            "to": to_state,
            "failure_count": self.failure_count,
            "timestamp": datetime.now().isoformat(),
        })
    
    def get_state(self) -> Dict:
        return {
            "state": self.state.value,
            "failure_count": self.failure_count,
            "success_count": self.success_count,
            "time_until_half_open": self._time_until_half_open() if self.state == CircuitState.OPEN else 0,
        }


class CircuitOpenError(Exception):
    pass
```

### 4.3 Saga Pattern

```python
@dataclass
class SagaStep:
    name: str
    action: Callable
    compensation: Callable  # Rollback function
    max_retries: int = 3

class SagaOrchestrator:
    """
    Saga Pattern — phân phối transaction qua nhiều services.
    
    Nếu step nào fail → compensating transactions chạy ngược
    để undo các step đã thành công trước đó.
    
    Flow:
    1. Step A (success) → Step B (success) → Step C (FAIL)
    2. Compensation: undo B → undo A
    """
    
    def __init__(self, name: str = "saga"):
        self.name = name
        self.steps: List[SagaStep] = []
        self.completed_steps: List[Dict] = []
        self.history: List[Dict] = []
    
    def add_step(self, name: str, action: Callable, compensation: Callable):
        self.steps.append(SagaStep(name=name, action=action, compensation=compensation))
        return self
    
    def execute(self, initial_data: Any = None) -> Dict:
        data = initial_data
        compensated = False
        
        for step in self.steps:
            try:
                result = step.action(data)
                self.completed_steps.append({
                    "name": step.name,
                    "result": result,
                    "status": "success",
                    "timestamp": datetime.now().isoformat(),
                })
                data = result
            except Exception as e:
                self.history.append({
                    "failed_step": step.name,
                    "error": str(e),
                    "compensating": True,
                    "timestamp": datetime.now().isoformat(),
                })
                
                # Compensate in reverse order
                for completed in reversed(self.completed_steps):
                    try:
                        completed["compensation"]()
                        self.history.append({
                            "compensated": completed["name"],
                            "status": "success",
                            "timestamp": datetime.now().isoformat(),
                        })
                    except Exception as comp_e:
                        self.history.append({
                            "compensation_failed": completed["name"],
                            "error": str(comp_e),
                            "timestamp": datetime.now().isoformat(),
                        })
                
                compensated = True
                return {
                    "success": False,
                    "failed_step": step.name,
                    "error": str(e),
                    "compensated": True,
                    "compensation_history": [
                        h for h in self.history if "compensated" in h
                    ],
                }
        
        return {
            "success": True,
            "completed_steps": [s["name"] for s in self.completed_steps],
            "result": data,
        }
```

---

## 5. Observability

### 5.1 Distributed Tracing

```python
import time
import uuid
from contextlib import contextmanager
from typing import Dict, List, Any, Optional

class Span:
    """A single unit of work in a trace"""
    
    def __init__(self, name: str, trace_id: str, parent_span_id: str = None):
        self.name = name
        self.span_id = str(uuid.uuid4())[:8]
        self.trace_id = trace_id
        self.parent_span_id = parent_span_id
        self.start_time = time.time()
        self.end_time = None
        self.attributes: Dict[str, Any] = {}
        self.events: List[Dict] = []
        self.status = "OK"
    
    def set_attribute(self, key: str, value: Any):
        self.attributes[key] = value
    
    def add_event(self, name: str, attributes: Dict = None):
        self.events.append({
            "name": name,
            "attributes": attributes or {},
            "timestamp": datetime.now().isoformat(),
        })
    
    def finish(self, status: str = "OK"):
        self.end_time = time.time()
        self.status = status
    
    @property
    def duration_ms(self) -> float:
        if self.end_time:
            return (self.end_time - self.start_time) * 1000
        return (time.time() - self.start_time) * 1000
    
    def to_dict(self) -> Dict:
        return {
            "name": self.name,
            "span_id": self.span_id,
            "trace_id": self.trace_id,
            "parent_span_id": self.parent_span_id,
            "start_time": datetime.fromtimestamp(self.start_time).isoformat(),
            "end_time": datetime.fromtimestamp(self.end_time).isoformat() if self.end_time else None,
            "duration_ms": round(self.duration_ms, 2),
            "status": self.status,
            "attributes": self.attributes,
            "events": self.events,
        }


class Tracer:
    """
    Distributed tracing system for workflow observability.
    
    Creates traces with nested spans, enabling:
    - End-to-end latency tracking
    - Bottleneck identification
    - Error attribution
    """
    
    _current_trace_id: Optional[str] = None
    _current_span_id: Optional[str] = None
    
    def __init__(self):
        self.traces: Dict[str, List[Span]] = {}
    
    def start_trace(self, name: str) -> str:
        trace_id = str(uuid.uuid4())[:12]
        span = Span(name, trace_id)
        self.traces[trace_id] = [span]
        Tracer._current_trace_id = trace_id
        Tracer._current_span_id = span.span_id
        return trace_id
    
    @contextmanager
    def span(self, name: str, attributes: Dict = None):
        trace_id = Tracer._current_trace_id or str(uuid.uuid4())[:12]
        parent_id = Tracer._current_span_id
        
        new_span = Span(name, trace_id, parent_id)
        if attributes:
            for k, v in attributes.items():
                new_span.set_attribute(k, v)
        
        if trace_id not in self.traces:
            self.traces[trace_id] = []
        self.traces[trace_id].append(new_span)
        
        old_span_id = Tracer._current_span_id
        Tracer._current_span_id = new_span.span_id
        
        try:
            yield new_span
            new_span.finish("OK")
        except Exception as e:
            new_span.set_attribute("error", str(e))
            new_span.finish("ERROR")
            raise
        finally:
            Tracer._current_span_id = old_span_id
    
    def get_trace(self, trace_id: str) -> List[Dict]:
        spans = self.traces.get(trace_id, [])
        return [s.to_dict() for s in sorted(spans, key=lambda s: s.start_time)]
    
    def get_trace_summary(self, trace_id: str) -> Dict:
        spans = self.traces.get(trace_id, [])
        if not spans:
            return {"error": "Trace not found"}
        
        root = min(spans, key=lambda s: s.start_time)
        total_duration = max(s.end_time or time.time() for s in spans) - root.start_time
        
        return {
            "trace_id": trace_id,
            "root_span": root.name,
            "total_spans": len(spans),
            "total_duration_ms": round(total_duration * 1000, 2),
            "errors": sum(1 for s in spans if s.status == "ERROR"),
            "spans": [s.to_dict() for s in spans],
        }
```

### 5.2 Structured Logging + Metrics

```python
class WorkflowLogger:
    """Structured logger cho workflow"""
    
    def __init__(self, workflow_name: str, log_file: str = None):
        self.workflow_name = workflow_name
        self.logs = []
        self.log_file = log_file
    
    def log_step(self, step: str, status: str, data: Dict = None, duration: float = None):
        entry = {
            "workflow": self.workflow_name,
            "step": step,
            "status": status,
            "timestamp": datetime.now().isoformat(),
            "duration_ms": round(duration * 1000, 2) if duration else None,
            "data": data or {},
        }
        self.logs.append(entry)
        
        if self.log_file:
            with open(self.log_file, "a") as f:
                f.write(json.dumps(entry) + "\n")
    
    @contextmanager
    def track(self, step_name: str):
        start = time.time()
        self.log_step(step_name, "start")
        try:
            yield
            duration = time.time() - start
            self.log_step(step_name, "success", duration=duration)
        except Exception as e:
            duration = time.time() - start
            self.log_step(step_name, "error", data={"error": str(e)}, duration=duration)
            raise
    
    def export_json(self, path: str = None) -> str:
        output = json.dumps(self.logs, indent=2, ensure_ascii=False)
        if path:
            with open(path, "w") as f:
                f.write(output)
        return output


class MetricsCollector:
    """Thu thập và tổng hợp metrics"""
    
    def __init__(self):
        self.counters: Dict[str, int] = {}
        self.gauges: Dict[str, float] = {}
        self.histograms: Dict[str, List[float]] = {}
    
    def counter(self, name: str, value: int = 1):
        self.counters[name] = self.counters.get(name, 0) + value
    
    def gauge(self, name: str, value: float):
        self.gauges[name] = value
    
    def histogram(self, name: str, value: float):
        if name not in self.histograms:
            self.histograms[name] = []
        self.histograms[name].append(value)
    
    def get_summary(self) -> Dict:
        summary = {
            "counters": dict(self.counters),
            "gauges": dict(self.gauges),
            "histograms": {},
        }
        
        for name, values in self.histograms.items():
            sorted_vals = sorted(values)
            n = len(sorted_vals)
            summary["histograms"][name] = {
                "count": n,
                "min": sorted_vals[0] if n else 0,
                "max": sorted_vals[-1] if n else 0,
                "mean": sum(sorted_vals) / n if n else 0,
                "p50": sorted_vals[n // 2] if n else 0,
                "p95": sorted_vals[int(n * 0.95)] if n else 0,
                "p99": sorted_vals[int(n * 0.99)] if n else 0,
            }
        
        return summary
```

---

## 6. Workflow Orchestration Engine

```python
class WorkflowOrchestrator:
    """
    Complete workflow orchestration engine combining all patterns:
    - State machine for flow control
    - Pipeline for data processing
    - Saga for error recovery
    - Tracing for observability
    - Metrics for monitoring
    """
    
    def __init__(self, name: str):
        self.name = name
        self.tracer = Tracer()
        self.logger = WorkflowLogger(name)
        self.metrics = MetricsCollector()
        self.state_machine = HierarchicalStateMachine(name)
        self.circuit_breakers: Dict[str, CircuitBreaker] = {}
        self.workflows: Dict[str, Callable] = {}
        self._setup_default_transitions()
    
    def _setup_default_transitions(self):
        """Setup default state transitions"""
        sm = self.state_machine
        
        sm.add_transition(Transition(
            from_state=AgentState.IDLE,
            to_state=AgentState.THINKING,
            condition=lambda ctx: ctx.get("task") is not None,
            name="task_received",
        ))
        
        sm.add_transition(Transition(
            from_state=AgentState.THINKING,
            to_state=AgentState.PLANNING,
            condition=lambda ctx: ctx.get("analysis_done"),
            name="analysis_complete",
        ))
        
        sm.add_transition(Transition(
            from_state=AgentState.PLANNING,
            to_state=AgentState.READING_CODE,
            condition=lambda ctx: ctx.get("plan_ready"),
            name="plan_approved",
        ))
        
        sm.add_transition(Transition(
            from_state=AgentState.READING_CODE,
            to_state=AgentState.WRITING_CODE,
            condition=lambda ctx: ctx.get("code_read"),
            name="context_gathered",
        ))
        
        sm.add_transition(Transition(
            from_state=AgentState.WRITING_CODE,
            to_state=AgentState.RUNNING_TESTS,
            condition=lambda ctx: ctx.get("code_written"),
            name="code_written",
        ))
        
        sm.add_transition(Transition(
            from_state=AgentState.RUNNING_TESTS,
            to_state=AgentState.DONE,
            condition=lambda ctx: ctx.get("tests_passed"),
            name="tests_passed",
        ))
        
        sm.add_transition(Transition(
            from_state=AgentState.RUNNING_TESTS,
            to_state=AgentState.FIXING_ERRORS,
            condition=lambda ctx: ctx.get("tests_failed"),
            name="tests_failed",
        ))
        
        sm.add_transition(Transition(
            from_state=AgentState.FIXING_ERRORS,
            to_state=AgentState.RUNNING_TESTS,
            condition=lambda ctx: True,
            name="fix_applied",
        ))
        
        # Max fix attempts → ROLLBACK
        sm.add_transition(Transition(
            from_state=AgentState.FIXING_ERRORS,
            to_state=AgentState.ROLLING_BACK,
            condition=lambda ctx: ctx.get("fix_attempts", 0) >= 3,
            name="max_fixes_reached",
        ))
        
        sm.add_transition(Transition(
            from_state=AgentState.ROLLING_BACK,
            to_state=AgentState.ERROR,
            condition=lambda ctx: ctx.get("rollback_done"),
            name="rollback_complete",
        ))
    
    def register_workflow(self, name: str, func: Callable):
        self.workflows[name] = func
        return self
    
    def get_circuit_breaker(self, service: str) -> CircuitBreaker:
        if service not in self.circuit_breakers:
            self.circuit_breakers[service] = CircuitBreaker(
                failure_threshold=5, recovery_timeout=30
            )
        return self.circuit_breakers[service]
    
    def execute(self, task: Dict) -> Dict:
        trace_id = self.tracer.start_trace(f"workflow_{self.name}")
        
        with self.tracer.span("orchestrate", {"task": str(task)[:200]}):
            try:
                self.state_machine.context["task"] = task
                result = self.state_machine.run(task, max_iterations=50)
                
                self.metrics.counter("workflow.success")
                self.metrics.counter(f"workflow.{self.name}.success")
                
                return {
                    "success": True,
                    "result": result,
                    "trace_id": trace_id,
                    "state_path": self.state_machine.get_state_path(),
                    "stats": self.state_machine.get_stats(),
                }
            except Exception as e:
                self.metrics.counter("workflow.error")
                return {
                    "success": False,
                    "error": str(e),
                    "trace_id": trace_id,
                    "state_path": self.state_machine.get_state_path(),
                }
    
    def get_dashboard(self) -> Dict:
        """Get comprehensive dashboard data"""
        return {
            "workflow": self.name,
            "state": self.state_machine.state.value,
            "stats": self.state_machine.get_stats(),
            "circuit_breakers": {
                name: cb.get_state()
                for name, cb in self.circuit_breakers.items()
            },
            "traces_count": len(self.tracer.traces),
            "metrics": self.metrics.get_summary(),
            "history": self.logger.logs[-10:],  # Last 10 logs
        }
```

---

## 7. Workflow Testing

```python
import unittest

class TestSequentialWorkflow(unittest.TestCase):
    def setUp(self):
        self.workflow = SequentialWorkflow("test_seq")
        self.workflow.add_step("step1", lambda x: x * 2)
        self.workflow.add_step("step2", lambda x: x + 10)
        self.workflow.add_step("step3", lambda x: x ** 2)
    
    def test_basic_run(self):
        result = self.workflow.run(5)
        self.assertEqual(result, 400)  # (5*2 + 10)^2 = 400
    
    def test_history_recorded(self):
        self.workflow.run(5)
        history = self.workflow.get_history()
        self.assertEqual(len(history), 3)
        self.assertTrue(all(h["status"] == "success" for h in history))
    
    def test_error_handling(self):
        workflow = SequentialWorkflow("test_error")
        workflow.add_step("fail", lambda x: 1/0)
        
        with self.assertRaises(WorkflowError):
            workflow.run(1)
        
        self.assertEqual(workflow.state["status"], "failed")
    
    def test_summary(self):
        self.workflow.run(5)
        summary = self.workflow.get_summary()
        self.assertEqual(summary["total_steps"], 3)
        self.assertEqual(summary["successful"], 3)
        self.assertEqual(summary["status"], "completed")


class TestDAGWorkflow(unittest.TestCase):
    def test_linear_dag(self):
        dag = DAGWorkflow("test_linear")
        dag.add_node("A", lambda x: 1)
        dag.add_node("B", lambda x: 2, depends_on=["A"])
        dag.add_node("C", lambda x: 3, depends_on=["B"])
        
        result = dag.run()
        self.assertEqual(result["total_nodes"], 3)
        self.assertEqual(result["successful"], 3)
    
    def test_parallel_dag(self):
        dag = DAGWorkflow("test_parallel")
        dag.add_node("A", lambda x: 10)
        dag.add_node("B", lambda x: 20)
        dag.add_node("C", lambda x: 30, depends_on=["A", "B"])
        
        result = dag.run()
        self.assertEqual(result["total_nodes"], 3)
    
    def test_cycle_detection(self):
        dag = DAGWorkflow("test_cycle")
        dag.add_node("A", lambda x: 1, depends_on=["C"])
        dag.add_node("B", lambda x: 2, depends_on=["A"])
        dag.add_node("C", lambda x: 3, depends_on=["B"])
        
        validation = dag.validate()
        self.assertFalse(validation["valid"])
    
    def test_validation(self):
        dag = DAGWorkflow("test_valid")
        dag.add_node("A", lambda x: 1)
        dag.add_node("B", lambda x: 2, depends_on=["A"])
        
        validation = dag.validate()
        self.assertTrue(validation["valid"])


class TestCircuitBreaker(unittest.TestCase):
    def setUp(self):
        self.cb = CircuitBreaker(failure_threshold=3, recovery_timeout=0.1)
    
    def test_normal_flow(self):
        result = self.cb.call(lambda: "ok")
        self.assertEqual(result, "ok")
        self.assertEqual(self.cb.state, CircuitState.CLOSED)
    
    def test_trips_after_failures(self):
        for _ in range(3):
            try:
                self.cb.call(lambda: 1/0)
            except:
                pass
        self.assertEqual(self.cb.state, CircuitState.OPEN)
    
    def test_rejects_when_open(self):
        for _ in range(3):
            try:
                self.cb.call(lambda: 1/0)
            except:
                pass
        
        with self.assertRaises(CircuitOpenError):
            self.cb.call(lambda: "ok")
    
    def test_half_open_recovery(self):
        for _ in range(3):
            try:
                self.cb.call(lambda: 1/0)
            except:
                pass
        
        time.sleep(0.15)
        result = self.cb.call(lambda: "recovered")
        self.assertEqual(result, "recovered")


class TestSagaOrchestrator(unittest.TestCase):
    def test_successful_saga(self):
        saga = SagaOrchestrator("test_saga")
        saga.add_step("step1", lambda x: "result1", lambda: None)
        saga.add_step("step2", lambda x: "result2", lambda: None)
        
        result = saga.execute()
        self.assertTrue(result["success"])
    
    def test_compensation_on_failure(self):
        compensated = []
        
        saga = SagaOrchestrator("test_compensate")
        saga.add_step("step1", lambda x: "ok", lambda: compensated.append("undo1"))
        saga.add_step("step2", lambda x: 1/0, lambda: compensated.append("undo2"))
        saga.add_step("step3", lambda x: "ok", lambda: compensated.append("undo3"))
        
        result = saga.execute()
        self.assertFalse(result["success"])
        self.assertTrue(result["compensated"])
        self.assertIn("undo1", compensated)


class TestTracer(unittest.TestCase):
    def test_trace_creation(self):
        tracer = Tracer()
        trace_id = tracer.start_trace("test_trace")
        self.assertIn(trace_id, tracer.traces)
    
    def test_span_nesting(self):
        tracer = Tracer()
        trace_id = tracer.start_trace("test")
        
        with tracer.span("parent") as parent:
            with tracer.span("child") as child:
                pass
        
        trace = tracer.get_trace(trace_id)
        self.assertEqual(len(trace), 2)


if __name__ == "__main__":
    unittest.main()
```

---

## 8. Harness Integration

### 8.1 TypeScript Interfaces

```typescript
// Workflow Engine — Full Harness Integration
interface WorkflowEngine {
  // Workflow management
  createWorkflow: (config: WorkflowConfig) => Workflow;
  getWorkflow: (id: string) => Workflow | null;
  
  // Execution
  execute: (workflowId: string, input: any) => Promise<WorkflowResult>;
  pause: (workflowId: string) => void;
  resume: (workflowId: string) => void;
  cancel: (workflowId: string) => void;
  
  // State management
  getState: (workflowId: string) => WorkflowState;
  getHistory: (workflowId: string) => WorkflowStep[];
  
  // Observability
  getTrace: (workflowId: string) => TraceData;
  getMetrics: (workflowId: string) => WorkflowMetrics;
  
  // Error recovery
  retry: (workflowId: string) => void;
  rollback: (workflowId: string) => void;
}

interface WorkflowConfig {
  name: string;
  steps: WorkflowStepDef[];
  triggers: TriggerDef[];
  errorPolicy: ErrorPolicy;
  timeout: number;
  maxRetries: number;
}

interface WorkflowStepDef {
  name: string;
  type: 'transform' | 'action' | 'condition' | 'parallel' | 'sub_workflow';
  handler: string;
  dependsOn: string[];
  timeout: number;
  retryPolicy: RetryPolicy;
}

interface WorkflowResult {
  success: boolean;
  output: any;
  duration: number;
  stepsExecuted: number;
  errors: WorkflowError[];
  traceId: string;
}

interface WorkflowMetrics {
  totalRuns: number;
  successRate: number;
  avgDuration: number;
  p95Duration: number;
  errorRate: number;
  stepMetrics: Record<string, StepMetrics>;
}

interface StepMetrics {
  runs: number;
  success: number;
  failed: number;
  avgDurationMs: number;
  lastRun: string;
}

// Complete Harness Workflow Engine
class HarnessWorkflowEngine implements WorkflowEngine {
  private workflows: Map<string, Workflow>;
  private tracer: Tracer;
  private metrics: MetricsCollector;
  private logger: WorkflowLogger;
  
  constructor(config: HarnessConfig) {
    this.workflows = new Map();
    this.tracer = new Tracer();
    this.metrics = new MetricsCollector();
    this.logger = new WorkflowLogger('harness');
  }
  
  async execute(workflowId: string, input: any): Promise<WorkflowResult> {
    const traceId = this.tracer.startTrace(`workflow_${workflowId}`);
    
    using span = this.tracer.span('execute', { workflowId });
    
    try {
      const workflow = this.workflows.get(workflowId);
      if (!workflow) throw new Error(`Workflow ${workflowId} not found`);
      
      const startTime = Date.now();
      const output = await workflow.run(input);
      const duration = Date.now() - startTime;
      
      this.metrics.counter('workflow.success');
      this.metrics.histogram('workflow.duration', duration);
      
      return {
        success: true,
        output,
        duration,
        stepsExecuted: workflow.getStepsExecuted(),
        errors: [],
        traceId,
      };
    } catch (error) {
      this.metrics.counter('workflow.error');
      return {
        success: false,
        output: null,
        duration: 0,
        stepsExecuted: 0,
        errors: [{ message: error.message }],
        traceId,
      };
    }
  }
  
  getMetrics(workflowId: string): WorkflowMetrics {
    return this.metrics.getSummary() as WorkflowMetrics;
  }
  
  // ... other interface implementations
}
```

---

## 9. Case Studies

### 9.1. GitHub Actions — Event-Driven CI/CD

```yaml
# Workflow pattern: Event-driven with parallel jobs
name: CI/CD Pipeline
on: [push, pull_request]

jobs:
  lint:          # Parallel job 1
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm run lint
  
  test:          # Parallel job 2
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm test
  
  build:         # Depends on lint + test
    needs: [lint, test]
    runs-on: ubuntu-latest
    steps:
      - run: npm run build
  
  deploy:        # Depends on build + manual approval
    needs: [build]
    runs-on: ubuntu-latest
    environment: production
    steps:
      - run: npm run deploy
```

**Lesson**: GitHub Actions uses DAG-based workflow execution with event triggers and dependency resolution.

### 9.2. Apache Airflow — Data Pipeline Orchestration

```python
# Airflow pattern: DAG with task dependencies
from airflow import DAG
from airflow.operators.python import PythonOperator

with DAG('data_pipeline', schedule_interval='@daily') as dag:
    extract = PythonOperator(task_id='extract', python_callable=extract_data)
    transform = PythonOperator(task_id='transform', python_callable=transform_data)
    load = PythonOperator(task_id='load', python_callable=load_data)
    validate = PythonOperator(task_id='validate', python_callable=validate_data)
    
    extract >> transform >> load >> validate
```

**Lesson**: Airflow proved that DAG-based workflows with clear task dependencies are the gold standard for data pipelines.

### 9.3. Temporal — Durable Workflow Execution

```
Temporal pattern: Saga with compensating transactions

Workflow:
  1. ReserveInventory() → success
  2. ChargePayment() → success
  3. ShipOrder() → FAIL
  
Compensation (reverse):
  2. RefundPayment()
  1. ReleaseInventory()
```

**Lesson**: Temporal's key insight — workflows should survive process crashes via durable state and automatic retry with compensation.

---

## 10. Design Principles

### 10.1 SOLID Cho Workflows

**1. Single Responsibility**
- Mỗi step = 1 việc rõ ràng
- Không combine parse + validate + transform trong 1 step

**2. Open/Closed**
- Mở cho thêm steps mới
- Đóng cho sửa đổi existing workflow logic

**3. Liskov Substitution**
- Có thể thay step A bằng step B (cùng interface)
- Conditional branches có thể swap

**4. Interface Segregation**
- Step input/output schemas tách biệt
- Không dùng shared mutable state

**5. Dependency Inversion**
- Workflow engine phụ thuộc vào Step abstraction
- Không hardcode step implementations

### 10.2 6 Design Principles

```
┌──────────────────────────────────────────────────────────────────┐
│                 WORKFLOW DESIGN PRINCIPLES                        │
│                                                                  │
│  1. IDEMPOTENCY                                                  │
│     Mỗi bước phải an toàn khi chạy lại nhiều lần              │
│     → Dùng UUID/idempotency key cho mutations                   │
│                                                                  │
│  2. OBSERVABILITY                                                │
│     Mọi bước phải log input/output + metrics                    │
│     → Dùng structured logging + distributed tracing             │
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
│     → Retry + Circuit Breaker + Saga pattern                     │
│                                                                  │
│  6. STATE EXTERNALIZATION                                        │
│     Lưu state ra ngoài memory                                   │
│     → Có thể resume từ checkpoint                               │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 11. Best Practices

### 11.1 DO ✅

- **Define clear input/output for each step**: Easier to test and debug
- **Add structured logging at every step**: JSON logs with timestamps
- **Implement retry with exponential backoff**: Handle transient failures
- **Use circuit breakers for external services**: Prevent cascade failures
- **Design for idempotency**: Safe to replay any step
- **Add timeout for every step**: Prevent hanging workflows
- **Use saga pattern for distributed transactions**: Compensate on failure
- **Track metrics**: Latency, success rate, error rate per step
- **Validate DAG structure**: Detect cycles before execution
- **Add checkpointing**: Resume from last successful step

### 11.2 DON'T ❌

- **Đừng share mutable state between steps**: Use immutable data passing
- **Đừng skip error handling**: Every step can fail
- **Đừng use infinite loops**: Always set max iterations
- **Đừng ignore timeout**: A hanging step = stuck workflow
- **Đ hardcode step dependencies**: Use DAG configuration
- **Đừng skip testing**: Unit test each step independently
- **Đừng use blocking calls in parallel steps**: Use async/executor
- **Đừng forget compensation logic**: Every forward action needs a rollback

---

## 12. Tương Lai

### 12.1 Xu Hướng 2026-2028

**1. AI-Generated Workflows**
- Agent tự generate workflow từ task description
- Auto-optimize workflow structure based on execution history
- Self-healing workflows

**2. Serverless Workflow Engines**
- Workflow-as-code (no infrastructure management)
- Auto-scaling per step
- Pay-per-execution pricing

**3. Cross-Platform Workflow Portability**
- Standard workflow definition format (CNCF WASM?)
- Portable across Temporal, Airflow, Step Functions
- Universal workflow marketplace

**4. Real-time Adaptive Workflows**
- Dynamic step adjustment based on runtime metrics
- ML-based bottleneck prediction
- Auto-parallelization

**5. Workflow Observability 2.0**
- AI-powered root cause analysis
- Predictive alerting
- Automated incident response

---

## Tài Liệu Tham Khảo

### Papers & Research

1. **Temporal: Durable Execution**
   - https://docs.temporal.io
   - Durable workflow execution with automatic retry and compensation

2. **Apache Airflow Documentation**
   - https://airflow.apache.org/docs/
   - DAG-based workflow orchestration

3. **Microservices Patterns**
   - Chris Richardson, 2018
   - Saga pattern, CQRS, Event Sourcing

4. **Designing Data-Intensive Applications**
   - Martin Kleppmann, 2017
   - Pipeline design, distributed systems patterns

### Frameworks

1. **Temporal** - https://temporal.io
2. **Apache Airflow** - https://airflow.apache.org
3. **Prefect** - https://www.prefect.io
4. **GitHub Actions** - https://docs.github.com/en/actions
5. **AWS Step Functions** - https://aws.amazon.com/step-functions/

---

*Tài liệu: VII. Workflow — HARNESS ENGINEERING EDITION*
*Ngày cập nhật: 19/07/2026*
*Tác giả: AI Knowledge Repository*