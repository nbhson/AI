# 📋 IV. Plan & Decompose Task

## Tổng Quan

Khi đối mặt task phức tạp, AI Agent cần **phân tích → lập kế hoạch → chia nhỏ → thực hiện tuần tự**. Đây là kỹ năng cốt lõi biến LLM từ "chatbot" thành "agent".

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
└──────────────────────────────────────────────────────────────────┘
```

## Nội Dung

| # | Chủ đề | Mô tả |
|---|--------|-------|
| 1 | [Task Decomposition Patterns](#1-task-decomposition-patterns) | Các mô hình chia task |
| 2 | [Planning Algorithms](#2-planning-algorithms) | Thuật toán lập kế hoạch |
| 3 | [Agent Workflows](#3-agent-workflows) | Các kiểu workflow của agent |
| 4 | [State Management](#4-state-management) | Quản lý trạng thái |
| 5 | [ReAct Pattern](#5-react-pattern) | Reasoning + Acting |

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

class TaskType(Enum):
    SEQUENTIAL = "sequential"
    PARALLEL = "parallel"
    CONDITIONAL = "conditional"
    HIERARCHICAL = "hierarchical"
    ITERATIVE = "iterative"

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
    
    def to_dict(self):
        return {
            "id": self.id,
            "name": self.name,
            "status": self.status.value,
            "type": self.task_type.value,
            "subtasks": [s.to_dict() for s in self.subtasks],
        }

class TaskPlanner:
    """
    LLM-based task planner
    
    Phân tích task lớn và chia thành sub-tasks
    """
    
    def __init__(self, llm_func=None):
        self.llm = llm_func
    
    def decompose(self, task_description, max_depth=3, current_depth=0):
        """
        Phân tích task và chia thành sub-tasks
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

Quy tắc:
- Mỗi sub-task phải cụ thể, có thể thực hiện được
- Xác định dependencies giữa các sub-tasks
- Đánh giá loại (sequential/parallel/conditional)

Output JSON:
{{
  "analysis": "Phân tích task",
  "subtasks": [
    {{"name": "...", "description": "...", "type": "sequential|parallel|conditional", "dependencies": []}},
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
        
        Returns: Ordered list of tasks to execute
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
        
        Returns: Score 1-10
        """
        depth = self._get_depth(task)
        breadth = self._get_breadth(task)
        
        # Simple scoring
        score = min(10, depth * 2 + breadth)
        return {
            "score": score,
            "depth": depth,
            "total_subtasks": breadth,
            "complexity": "simple" if score <= 3 else "medium" if score <= 6 else "complex",
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
```

---

## 2. Planning Algorithms

### 2.1 LLM-Based Planning (Plan-and-Solve)

```python
class PlanAndSolve:
    """
    Plan-and-Solve prompting strategy
    
    1. Plan: Break down the problem
    2. Solve: Execute each step
    3. Verify: Check results
    """
    
    def __init__(self, llm_func=None):
        self.llm = llm_func
    
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

Đánh giá:
- Đầy đủ? (yes/no)
- Chính xác? (yes/no)
- Cần bổ sung? (nếu có, ghi rõ)"""
        
        if self.llm:
            return self.llm(prompt)
        return "Đã kiểm tra"
    
    def run(self, problem):
        """Execute full plan-and-solve cycle"""
        # Step 1: Plan
        steps = self.plan(problem)
        
        # Step 2: Execute each step
        results = []
        context = ""
        for i, step in enumerate(steps):
            result = self.solve_step(step, context)
            results.append({"step": step, "result": result})
            context += f"\nBước {i+1} đã hoàn thành: {result[:100]}"
        
        # Step 3: Verify
        verification = self.verify(problem, "\n".join(
            f"{r['step']}: {r['result']}" for r in results
        ))
        
        return {
            "plan": steps,
            "results": results,
            "verification": verification,
        }
```

### 2.2 Tree of Thoughts (ToT)

```python
class TreeOfThoughts:
    """
    Tree of Thoughts: Explore multiple reasoning paths
    
    Better than linear chain-of-thought for complex problems
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
        """
        root_state = {"problem": problem, "path": [], "score": 0}
        
        # BFS-like exploration
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
            
            frontier = next_frontier
        
        return {
            "solution": best_solution["path"] if best_solution else [],
            "total_score": best_score,
        }
```

---

## 3. Agent Workflows

### 3.1 Các Kiểu Agent

```
┌──────────────────────────────────────────────────────────────────┐
│                    AGENT WORKFLOW TYPES                            │
│                                                                  │
│  1. RE-AGENT (Reasoning + Acting)                               │
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
│  4. MULTI-AGENT                                                 │
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
└──────────────────────────────────────────────────────────────────┘
```

### 3.2 Agent Implementation

```python
class SimpleAgent:
    """
    Simple agent with tool-calling loop
    
    Pattern: Think → Act → Observe → Repeat
    """
    
    def __init__(self, tools, llm_func, max_iterations=10):
        self.tools = {tool["name"]: tool for tool in tools}
        self.llm = llm_func
        self.max_iterations = max_iterations
        self.memory = []
    
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
                }
            
            # Act: execute tool
            tool_name = thought.get("tool")
            tool_input = thought.get("input", {})
            
            if tool_name and tool_name in self.tools:
                result = self._execute_tool(tool_name, tool_input)
                
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
            else:
                context += f"\n\nStep {i+1}: Invalid tool '{tool_name}'. Try again."
        
        return {
            "answer": "Max iterations reached",
            "steps": self.memory,
            "iterations": self.max_iterations,
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
    """
    
    def __init__(self, agents, coordinator_llm=None):
        self.agents = agents  # {name: agent}
        self.coordinator = coordinator_llm
    
    def run(self, task):
        """Coordinate multiple agents"""
        # Coordinator decides which agent to use
        plan = self._create_plan(task)
        
        results = {}
        for step in plan:
            agent_name = step["agent"]
            subtask = step["task"]
            
            if agent_name in self.agents:
                result = self.agents[agent_name].run(subtask)
                results[agent_name] = result
        
        # Synthesize final answer
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
        
        # Simple: assign to first agent
        return [{"agent": agent_names[0], "task": task}]
    
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
    
    Supports: state persistence, checkpointing, rollback
    """
    
    def __init__(self):
        self.state = {}
        self.history = []
        self.checkpoints = []
    
    def set(self, key, value):
        """Set state value"""
        old_value = self.state.get(key)
        self.state[key] = value
        
        self.history.append({
            "action": "set",
            "key": key,
            "old": old_value,
            "new": value,
            "timestamp": datetime.now().isoformat(),
        })
    
    def get(self, key, default=None):
        """Get state value"""
        return self.state.get(key, default)
    
    def checkpoint(self):
        """Save current state"""
        self.checkpoints.append({
            "state": dict(self.state),
            "timestamp": datetime.now().isoformat(),
        })
        return len(self.checkpoints) - 1
    
    def rollback(self, checkpoint_id=None):
        """Rollback to checkpoint"""
        if checkpoint_id is None:
            checkpoint_id = len(self.checkpoints) - 1
        
        if 0 <= checkpoint_id < len(self.checkpoints):
            self.state = dict(self.checkpoints[checkpoint_id]["state"])
            return True
        return False
    
    def to_context(self):
        """Convert state to context string for LLM"""
        lines = [f"{k}: {v}" for k, v in self.state.items()]
        return "\n".join(lines)
    
    def reset(self):
        """Clear all state"""
        self.state = {}
```

---

## 5. ReAct Pattern

```python
class ReActAgent:
    """
    ReAct: Reasoning + Acting
    
    Thought → Action → Observation → Thought → ...
    
    Based on paper: "ReAct: Synergizing Reasoning and Acting in Language Models"
    """
    
    def __init__(self, tools, llm_func, max_steps=10):
        self.tools = tools
        self.llm = llm_func
        self.max_steps = max_steps
    
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
                    "history": history,
                }
            
            # Execute action
            if parsed.get("action"):
                tool_name, tool_input = self._parse_action(parsed["action"])
                observation = self._execute_tool(tool_name, tool_input)
                
                history += f"\n{response}"
                history += f"\nObservation: {observation}"
            else:
                history += f"\n{response}"
        
        return {"answer": "Max steps reached", "steps": self.max_steps}
    
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

*Tài liệu: IV. Plan & Decompose Task*
*Ngày tạo: 2026-07-11*