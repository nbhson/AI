# 🤖 IX. Multi-Agent

## Tổng Quan

**Multi-Agent Systems** trong AI coding là kiến trúc **nhiều AI agent làm việc cùng nhau**, mỗi agent có vai trò chuyên biệt, phối hợp để hoàn thành task phức tạp mà một agent đơn lẻ khó xử lý.

```
┌──────────────────────────────────────────────────────────────────┐
│                      MULTI-AGENT SYSTEM                           │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │                                                            │  │
│  │  ┌──────────┐    ┌──────────┐    ┌──────────┐            │  │
│  │  │ Planner  │───►│ Coder    │───►│ Reviewer │            │  │
│  │  │ Agent    │    │ Agent    │    │ Agent    │            │  │
│  │  └──────────┘    └──────────┘    └──────────┘            │  │
│  │       │               │               │                   │  │
│  │       └───────────────┴───────────────┘                   │  │
│  │                       │                                    │  │
│  │              ┌────────┴────────┐                          │  │
│  │              │  Coordinator    │                          │  │
│  │              │  / Orchestrator │                          │  │
│  │              └────────┬────────┘                          │  │
│  │                       │                                    │  │
│  │         ┌─────────────┼─────────────┐                    │  │
│  │         ▼             ▼             ▼                     │  │
│  │    ┌──────────┐  ┌──────────┐  ┌──────────┐             │  │
│  │    │ Tester   │  │ Debugger │  │ DevOps   │             │  │
│  │    │ Agent    │  │ Agent    │  │ Agent    │             │  │
│  │    └──────────┘  └──────────┘  └──────────┘             │  │
│  │                                                            │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## Nội Dung

| # | Chủ đề | Mô tả |
|---|--------|-------|
| 1 | [Agent Roles](#1-agent-roles) | Các vai trò agent phổ biến |
| 2 | [Communication Patterns](#2-communication-patterns) | Mẫu giao tiếp giữa agents |
| 3 | [Orchestration Strategies](#3-orchestration-strategies) | Chiến lược điều phối |
| 4 | [Shared Memory](#4-shared-memory) | Bộ nhớ chung giữa agents |
| 5 | [Conflict Resolution](#5-conflict-resolution) | Giải quyết xung đột |
| 6 | [Implementation Examples](#6-implementation-examples) | Ví dụ thực tế |

---

## 1. Agent Roles

### 1.1 Các Vai Trò Agent

```
┌──────────────────────────────────────────────────────────────────┐
│                     AGENT ROLES TAXONOMY                         │
│                                                                  │
│  COGNITIVE ROLES (Tư duy)                                       │
│  ├── 🎯 Planner       → Phân tích task, lên kế hoạch           │
│  ├── 🧠 Architect     → Thiết kế kiến trúc, quyết định design  │
│  ├── 🔍 Analyst       → Đọc code, phân tích vấn đề             │
│  └── 💡 Strategist    → Chọn approach, ưu tiên                  │
│                                                                  │
│  EXECUTION ROLES (Thực thi)                                     │
│  ├── 💻 Coder         → Viết code, implement solution          │
│  ├── 🔧 Debugger      → Tìm và sửa bugs                        │
│  ├── ⚡ Optimizer     → Tối ưu performance, refactor           │
│  └── 📝 DocWriter     → Viết documentation, comments           │
│                                                                  │
│  QUALITY ROLES (Chất lượng)                                     │
│  ├── 🔎 Reviewer      → Code review, quality check             │
│  ├── 🧪 Tester        → Viết và chạy tests                    │
│  ├── 🔒 Security      → Kiểm tra bảo mật                      │
│  └── 📊 Metricator    → Theo dõi metrics, coverage             │
│                                                                  │
│  OPERATIONS ROLES (Vận hành)                                    │
│  ├── 🚀 Deployer      → CI/CD, deployment                      │
│  ├── 📋 Coordinator   → Điều phối agents khác                  │
│  └── 📢 Notifier      → Báo cáo, thông báo kết quả            │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 1.2 Agent Definition

```python
from dataclasses import dataclass, field
from typing import Any, Callable, Dict, List, Optional
from enum import Enum

class AgentRole(Enum):
    PLANNER = "planner"
    ARCHITECT = "architect"
    ANALYST = "analyst"
    CODER = "coder"
    DEBUGGER = "debugger"
    OPTIMIZER = "optimizer"
    REVIEWER = "reviewer"
    TESTER = "tester"
    DOC_WRITER = "doc_writer"
    COORDINATOR = "coordinator"
    DEPLOYER = "deployer"


@dataclass
class AgentCapability:
    """Khả năng của một agent"""
    name: str
    description: str
    tools_required: List[str] = field(default_factory=list)
    max_concurrent: int = 1


@dataclass
class AgentProfile:
    """Hồ sơ agent — role, capability, constraints"""
    role: AgentRole
    name: str
    system_prompt: str
    capabilities: List[AgentCapability] = field(default_factory=list)
    max_tokens_per_task: int = 4000
    allowed_tools: List[str] = field(default_factory=list)
    
    def can_handle(self, task_type: str) -> bool:
        """Kiểm tra agent có xử lý được task type không"""
        capability_names = [c.name for c in self.capabilities]
        return task_type in capability_names


class Agent:
    """
    Một AI agent với role cụ thể.
    
    Mỗi agent có:
    - System prompt riêng (vai trò)
    - Set of tools được phép
    - Token budget
    - Communication interface
    """
    
    def __init__(self, profile: AgentProfile, llm_func: Callable):
        self.profile = profile
        self.llm = llm_func
        self.memory: List[Dict] = []
        self.status = "idle"
    
    def think(self, context: Dict) -> str:
        """Agent suy nghĩ và trả về plan/response"""
        self.status = "thinking"
        
        messages = [
            {"role": "system", "content": self.profile.system_prompt},
        ]
        
        # Add memory context
        if self.memory:
            memory_text = "\n".join([
                f"[{m['role']}] {m['content']}" 
                for m in self.memory[-5:]  # Last 5 messages
            ])
            messages.append({
                "role": "user",
                "content": f"Previous context:\n{memory_text}"
            })
        
        # Add current task
        messages.append({
            "role": "user",
            "content": context.get("task", "")
        })
        
        response = self.llm(messages)
        self.status = "idle"
        
        # Store in memory
        self.memory.append({"role": "assistant", "content": response})
        
        return response
    
    def act(self, action: str, params: Dict) -> Any:
        """Agent thực hiện hành động (gọi tool)"""
        self.status = "acting"
        # Tool execution logic here
        self.status = "idle"
        return {"action": action, "params": params, "status": "done"}


# Predefined agent profiles
AGENT_PROFILES = {
    AgentRole.PLANNER: AgentProfile(
        role=AgentRole.PLANNER,
        name="Planner",
        system_prompt="""You are a planning agent. Your job is to:
1. Analyze the given task thoroughly
2. Break it down into smaller subtasks
3. Identify dependencies between subtasks
4. Estimate effort for each subtask
5. Create an ordered execution plan

Output format:
- Task analysis
- Subtask list with dependencies
- Execution order
- Risk assessment""",
        capabilities=[
            AgentCapability("task_analysis", "Analyze task requirements"),
            AgentCapability("decomposition", "Break down complex tasks"),
            AgentCapability("scheduling", "Order subtasks"),
        ],
    ),
    
    AgentRole.CODER: AgentProfile(
        role=AgentRole.CODER,
        name="Coder",
        system_prompt="""You are an expert coding agent. Your job is to:
1. Read and understand the codebase context
2. Write clean, efficient, well-documented code
3. Follow project conventions and style guides
4. Handle edge cases and error scenarios
5. Write self-documenting code

Rules:
- Always check existing patterns before writing new code
- Prefer refactoring over adding new complexity
- Add meaningful comments only when logic is complex
- Handle errors gracefully""",
        capabilities=[
            AgentCapability("code_generation", "Write new code"),
            AgentCapability("code_modification", "Modify existing code"),
            AgentCapability("refactoring", "Improve code structure"),
        ],
        max_tokens_per_task=8000,
        allowed_tools=["read_file", "write_to_file", "search_files",
                      "execute_command"],
    ),
    
    AgentRole.REVIEWER: AgentProfile(
        role=AgentRole.REVIEWER,
        name="Reviewer",
        system_prompt="""You are a senior code reviewer. Your job is to:
1. Review code for correctness, security, and performance
2. Check adherence to coding standards
3. Identify potential bugs and edge cases
4. Suggest improvements with specific code examples
5. Verify test coverage

Review checklist:
□ Logic correctness
□ Error handling
□ Security vulnerabilities
□ Performance implications
□ Code readability
□ Test coverage
□ Documentation""",
        capabilities=[
            AgentCapability("code_review", "Review code quality"),
            AgentCapability("security_check", "Security audit"),
            AgentCapability("performance_review", "Performance analysis"),
        ],
    ),
    
    AgentRole.TESTER: AgentProfile(
        role=AgentRole.TESTER,
        name="Tester",
        system_prompt="""You are a QA testing agent. Your job is to:
1. Write comprehensive unit tests
2. Create integration tests
3. Design edge case tests
4. Verify test coverage
5. Report test results clearly

Test strategy:
- Start with happy path
- Add edge cases
- Test error conditions
- Verify boundary values
- Check concurrency issues""",
        capabilities=[
            AgentCapability("unit_testing", "Write unit tests"),
            AgentCapability("integration_testing", "Write integration tests"),
            AgentCapability("test_analysis", "Analyze test results"),
        ],
    ),
}
```

---

## 2. Communication Patterns

### 2.1 Communication Topologies

```
┌──────────────────────────────────────────────────────────────────┐
│                COMMUNICATION TOPOLOGIES                           │
│                                                                  │
│  1. HIERARCHICAL (Chủ tịch)                                     │
│                                                                  │
│              ┌──────────────┐                                   │
│              │ Coordinator  │                                   │
│              └──────┬───────┘                                   │
│          ┌──────────┼──────────┐                                │
│          ▼          ▼          ▼                                 │
│     ┌────────┐ ┌────────┐ ┌────────┐                           │
│     │Agent A │ │Agent B │ │Agent C │                           │
│     └────────┘ └────────┘ └────────┘                           │
│                                                                  │
│     → Coordinator giao task, agents chỉ talk qua coordinator   │
│     → Đơn giản, dễ manage, nhưng bottleneck ở coordinator     │
│                                                                  │
│                                                                  │
│  2. PEER-TO-PEER (Ngang hàng)                                   │
│                                                                  │
│     ┌────────┐     ┌────────┐                                  │
│     │Agent A │◄───►│Agent B │                                  │
│     └───┬────┘     └────┬───┘                                  │
│         │  ╲       ╱   │                                       │
│         │    ╲   ╱     │                                       │
│         │      ╳       │                                       │
│         │    ╱   ╲     │                                       │
│         ▼  ╱       ╲   ▼                                       │
│     ┌────────┐     ┌────────┐                                  │
│     │Agent C │◄───►│Agent D │                                  │
│     └────────┘     └────────┘                                  │
│                                                                  │
│     → Flexible, agents tự communicate                          │
│     → Phức tạp hơn, dễ conflict                               │
│                                                                  │
│                                                                  │
│  3. PIPELINE (Dây chuyền)                                       │
│                                                                  │
│     ┌────────┐    ┌────────┐    ┌────────┐    ┌────────┐      │
│     │Agent A │───►│Agent B │───►│Agent C │───►│Agent D │      │
│     │Plan    │    │Code    │    │Review  │    │Test    │      │
│     └────────┘    └────────┘    └────────┘    └────────┘      │
│                                                                  │
│     → Output của agent này = input của agent tiếp              │
│     → Rõ ràng, dễ track, nhưng tuần tự                        │
│                                                                  │
│                                                                  │
│  4. BLACKBOARD (Bảng đen chung)                                │
│                                                                  │
│     ┌─────────────────────────────────────────┐                │
│     │           SHARED BLACKBOARD              │                │
│     │  ┌──────────────────────────────────┐   │                │
│     │  │ Current State: ...                │   │                │
│     │  │ Shared Memory: ...                │   │                │
│     │  │ Task Queue: ...                   │   │                │
│     │  │ Results: ...                      │   │                │
│     │  └──────────────────────────────────┘   │                │
│     └───────┬──────────┬──────────┬───────────┘                │
│             │          │          │                              │
│         ┌───▼──┐  ┌───▼──┐  ┌───▼──┐                         │
│         │Agent │  │Agent │  │Agent │                          │
│         │  A   │  │  B   │  │  C   │                          │
│         └──────┘  └──────┘  └──────┘                          │
│                                                                  │
│     → Agents đọc/ghi vào blackboard chung                     │
│     → Loose coupling, scalable                                 │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 2.2 Message Protocol

```python
from dataclasses import dataclass, field
from typing import Any, Dict, List, Optional
from datetime import datetime
import uuid

@dataclass
class AgentMessage:
    """Message giữa các agents"""
    id: str = field(default_factory=lambda: str(uuid.uuid4())[:8])
    sender: str = ""
    receiver: str = ""          # "" = broadcast
    type: str = "task"          # task, result, question, feedback
    content: Dict[str, Any] = field(default_factory=dict)
    priority: int = 5
    timestamp: str = field(
        default_factory=lambda: datetime.now().isoformat()
    )
    reply_to: Optional[str] = None


class MessageBus:
    """
    Message Bus — trung gian giao tiếp giữa agents.
    
    Hỗ trợ:
    - Direct messaging (1:1)
    - Broadcast (1:N)
    - Pub/Sub (topic-based)
    - Request/Reply
    """
    
    def __init__(self):
        self.messages: List[AgentMessage] = []
        self.subscribers: Dict[str, List[Callable]] = {}
        self.mailboxes: Dict[str, List[AgentMessage]] = {}
    
    def send(self, message: AgentMessage):
        """Gửi message"""
        self.messages.append(message)
        
        # Direct message
        if message.receiver:
            if message.receiver not in self.mailboxes:
                self.mailboxes[message.receiver] = []
            self.mailboxes[message.receiver].append(message)
        
        # Notify subscribers
        topic = message.type
        for callback in self.subscribers.get(topic, []):
            callback(message)
    
    def receive(self, agent_id: str) -> List[AgentMessage]:
        """Nhận messages cho agent"""
        messages = self.mailboxes.get(agent_id, [])
        self.mailboxes[agent_id] = []
        return messages
    
    def subscribe(self, topic: str, callback: Callable):
        """Subscribe vào topic"""
        if topic not in self.subscribers:
            self.subscribers[topic] = []
        self.subscribers[topic].append(callback)
    
    def broadcast(self, sender: str, content: Dict):
        """Gửi message đến tất cả agents"""
        msg = AgentMessage(
            sender=sender,
            receiver="",
            type="broadcast",
            content=content,
        )
        self.send(msg)
```

---

## 3. Orchestration Strategies

### 3.1 Orchestration Patterns

```python
from enum import Enum
from typing import Dict, List, Optional, Callable
from dataclasses import dataclass, field

class OrchestrationMode(Enum):
    SEQUENTIAL = "sequential"       # Pipeline
    PARALLEL = "parallel"           # Fan-out / Fan-in
    DEBATE = "debate"              # Agents tranh luận
    VOTING = "voting"              # Agents bỏ phiếu
    HIERARCHICAL = "hierarchical"  # Commander → Workers
    ADAPTIVE = "adaptive"          # Tự chọn strategy


class AgentOrchestrator:
    """
    Điều phối nhiều agents — chọn strategy phù hợp.
    
    Modes:
    - Sequential: Agent A → B → C (pipeline)
    - Parallel:   Agent A, B, C cùng làm, gộp kết quả
    - Debate:     Agent A và B tranh luận, C quyết định
    - Voting:     Nhiều agents vote, majority wins
    """
    
    def __init__(self, agents: Dict[str, Agent], 
                 message_bus: MessageBus):
        self.agents = agents
        self.bus = message_bus
        self.mode = OrchestrationMode.SEQUENTIAL
    
    def execute_sequential(self, task: str, 
                           agent_order: List[str]) -> Dict:
        """Pipeline: mỗi agent xử lý output của agent trước"""
        context = {"task": task}
        results = []
        
        for agent_id in agent_order:
            agent = self.agents[agent_id]
            result = agent.think(context)
            results.append({
                "agent": agent_id,
                "result": result,
            })
            context["previous_result"] = result
        
        return {"results": results, "final": results[-1]}
    
    def execute_parallel(self, task: str, 
                         agent_ids: List[str]) -> Dict:
        """Parallel: nhiều agents cùng làm, gộp kết quả"""
        results = []
        
        for agent_id in agent_ids:
            agent = self.agents[agent_id]
            result = agent.think({"task": task})
            results.append({
                "agent": agent_id,
                "result": result,
            })
        
        # Merge results
        merged = self._merge_results(results)
        return {"results": results, "merged": merged}
    
    def execute_debate(self, task: str, 
                       debaters: List[str],
                       judge: str) -> Dict:
        """Debate: agents đưa ý kiến, judge quyết định"""
        opinions = []
        
        # Round 1: Each debater gives opinion
        for agent_id in debaters:
            agent = self.agents[agent_id]
            opinion = agent.think({
                "task": f"Analyze and give your expert opinion: {task}"
            })
            opinions.append({
                "agent": agent_id,
                "opinion": opinion,
            })
        
        # Round 2: Judge evaluates
        judge_agent = self.agents[judge]
        debate_summary = "\n\n".join([
            f"=== {o['agent']} ===\n{o['opinion']}" 
            for o in opinions
        ])
        
        verdict = judge_agent.think({
            "task": f"Evaluate the following expert opinions and "
                    f"provide a final decision:\n\n{debate_summary}"
        })
        
        return {
            "opinions": opinions,
            "verdict": verdict,
        }
    
    def execute_voting(self, task: str, 
                       voter_ids: List[str],
                       quorum: int = None) -> Dict:
        """Voting: agents bỏ phiếu, majority wins"""
        votes = []
        quorum = quorum or len(voter_ids) // 2 + 1
        
        for agent_id in voter_ids:
            agent = self.agents[agent_id]
            vote = agent.think({
                "task": f"Vote on this task (answer APPROVE or REJECT "
                        f"with reasoning): {task}"
            })
            votes.append({"agent": agent_id, "vote": vote})
        
        # Count votes (simplified)
        approve_count = sum(
            1 for v in votes 
            if "APPROVE" in v["vote"].upper()
        )
        
        return {
            "votes": votes,
            "approved": approve_count >= quorum,
            "approve_count": approve_count,
            "quorum": quorum,
        }
    
    def _merge_results(self, results: List[Dict]) -> str:
        """Gộp nhiều kết quả thành một"""
        merged_parts = []
        for r in results:
            merged_parts.append(
                f"--- {r['agent']} ---\n{r['result']}"
            )
        return "\n\n".join(merged_parts)
```

---

## 4. Shared Memory

### 4.1 Shared Memory Architecture

```python
from dataclasses import dataclass, field
from typing import Any, Dict, List, Optional
from datetime import datetime
import threading

@dataclass
class MemoryEntry:
    """Một entry trong shared memory"""
    key: str
    value: Any
    owner: str                 # Agent ID tạo entry
    created_at: str = field(
        default_factory=lambda: datetime.now().isoformat()
    )
    updated_at: str = ""
    tags: List[str] = field(default_factory=list)
    version: int = 1


class SharedMemory:
    """
    Shared Memory cho multi-agent system.
    
    Hỗ trợ:
    - Read/Write từ bất kỳ agent nào
    - Versioning (audit trail)
    - Tags (topic-based filtering)
    - Locking (tránh race condition)
    """
    
    def __init__(self):
        self.store: Dict[str, MemoryEntry] = {}
        self.lock = threading.Lock()
        self.changelog: List[Dict] = []
    
    def write(self, key: str, value: Any, owner: str,
              tags: List[str] = None):
        """Ghi vào shared memory"""
        with self.lock:
            if key in self.store:
                existing = self.store[key]
                existing.value = value
                existing.updated_at = datetime.now().isoformat()
                existing.version += 1
            else:
                self.store[key] = MemoryEntry(
                    key=key,
                    value=value,
                    owner=owner,
                    tags=tags or [],
                )
            
            self.changelog.append({
                "action": "write",
                "key": key,
                "owner": owner,
                "version": self.store[key].version,
                "timestamp": datetime.now().isoformat(),
            })
    
    def read(self, key: str) -> Optional[Any]:
        """Đọc từ shared memory"""
        entry = self.store.get(key)
        return entry.value if entry else None
    
    def search_by_tag(self, tag: str) -> List[MemoryEntry]:
        """Tìm entries theo tag"""
        return [
            entry for entry in self.store.values()
            if tag in entry.tags
        ]
    
    def get_history(self, key: str = None) -> List[Dict]:
        """Lấy lịch sử thay đổi"""
        if key:
            return [
                h for h in self.changelog if h["key"] == key
            ]
        return self.changelog
    
    def snapshot(self) -> Dict[str, Any]:
        """Lấy snapshot toàn bộ memory"""
        return {
            k: v.value for k, v in self.store.items()
        }
```

---

## 5. Conflict Resolution

### 5.1 Conflict Types & Resolution

```
┌──────────────────────────────────────────────────────────────────┐
│                CONFLICT TYPES IN MULTI-AGENT                     │
│                                                                  │
│  1. RESOURCE CONFLICT                                            │
│     Agent A và Agent B都想 sửa cùng 1 file                    │
│     → Resolution: File locking, sequential access               │
│                                                                  │
│  2. OPINION CONFLICT                                             │
│     Agent A suggest refactor, Agent B suggest rewrite           │
│     → Resolution: Debate, voting, or hierarchy                  │
│                                                                  │
│  3. PRIORITY CONFLICT                                            │
│     Agent A cho task X ưu tiên cao, Agent B cho task Y         │
│     → Resolution: Centralized priority queue                    │
│                                                                  │
│  4. STATE CONFLICT                                               │
│     Agent A và B đều update shared state cùng lúc             │
│     → Resolution: Optimistic locking, CRDT                     │
│                                                                  │
│  5. DEADLOCK                                                     │
│     Agent A chờ Agent B, Agent B chờ Agent A                  │
│     → Resolution: Timeout, deadlock detection                   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

```python
class ConflictResolver:
    """
    Giải quyết xung đột giữa agents.
    
    Strategies:
    - Priority-based: Agent có priority cao hơn wins
    - Voting: Majority decides
    - Merge: Combine both changes
    - Delegation: Escalate to coordinator
    """
    
    def __init__(self, coordinator: Agent = None):
        self.coordinator = coordinator
        self.conflicts: List[Dict] = []
    
    def resolve_file_conflict(self, agent_a: str, agent_b: str,
                               file_path: str) -> str:
        """
        Giải quyết conflict khi 2 agents muốn sửa cùng file.
        → Trả về agent nào được quyền sửa trước.
        """
        conflict = {
            "type": "resource",
            "agents": [agent_a, agent_b],
            "resource": file_path,
            "timestamp": datetime.now().isoformat(),
        }
        self.conflicts.append(conflict)
        
        # Strategy: Sequential — first requester wins
        # (simplified, real implementation would use locking)
        return agent_a
    
    def resolve_opinion_conflict(self, opinions: Dict[str, str]) -> str:
        """
        Giải quyết conflict về opinion → dùng voting.
        """
        # If coordinator exists, ask for decision
        if self.coordinator:
            context = {
                "task": f"Resolve conflicting opinions:\n"
                        + "\n".join([
                            f"{agent}: {opinion}"
                            for agent, opinion in opinions.items()
                        ])
            }
            return self.coordinator.think(context)
        
        # Otherwise, majority vote
        from collections import Counter
        votes = Counter(opinions.values())
        return votes.most_common(1)[0][0]
    
    def detect_deadlock(self, agent_wait_graph: Dict[str, str]) -> Optional[List[str]]:
        """
        Phát hiện deadlock trong agent wait graph.
        
        agent_wait_graph: {agent_id: waiting_for_agent_id}
        """
        visited = set()
        path = set()
        
        def dfs(node: str) -> Optional[List[str]]:
            if node in path:
                return [node]
            if node in visited:
                return None
            visited.add(node)
            path.add(node)
            
            next_node = agent_wait_graph.get(node)
            if next_node:
                result = dfs(next_node)
                if result:
                    if node in result:
                        result.append(node)
                    return result
            
            path.discard(node)
            return None
        
        for agent_id in agent_wait_graph:
            result = dfs(agent_id)
            if result:
                return result
        
        return None
```

---

## 6. Implementation Examples

### 6.1 Code Review Multi-Agent System

```python
class CodeReviewSystem:
    """
    Multi-agent system cho code review:
    1. Analyst: phân tích diff
    2. Security Agent: kiểm tra bảo mật
    3. Performance Agent: kiểm tra performance
    4. Reviewer: gộp ý kiến, viết review
    """
    
    def __init__(self, agents: Dict[str, Agent]):
        self.agents = agents
        self.bus = MessageBus()
        self.shared_memory = SharedMemory()
    
    def review_pull_request(self, pr_diff: str, 
                            files: List[str]) -> Dict:
        """Review một pull request"""
        
        # Step 1: Analyst phân tích diff
        analyst = self.agents["analyst"]
        analysis = analyst.think({
            "task": f"Analyze this code diff:\n{pr_diff}"
        })
        self.shared_memory.write(
            "analysis", analysis, "analyst", ["code-review"]
        )
        
        # Step 2: Parallel review
        security_result = self.agents["security"].think({
            "task": f"Security review:\n{analysis}"
        })
        performance_result = self.agents["performance"].think({
            "task": f"Performance review:\n{analysis}"
        })
        
        self.shared_memory.write(
            "security_review", security_result, "security"
        )
        self.shared_memory.write(
            "performance_review", performance_result, "performance"
        )
        
        # Step 3: Reviewer gộp kết quả
        reviewer = self.agents["reviewer"]
        all_reviews = (
            f"=== Analysis ===\n{analysis}\n\n"
            f"=== Security ===\n{security_result}\n\n"
            f"=== Performance ===\n{performance_result}"
        )
        
        final_review = reviewer.think({
            "task": f"Consolidate reviews into a final code review:\n"
                    f"{all_reviews}"
        })
        
        return {
            "analysis": analysis,
            "security": security_result,
            "performance": performance_result,
            "final_review": final_review,
        }
```

### 6.2 Bug Fix Multi-Agent System

```python
class BugFixSystem:
    """
    Multi-agent system cho bug fixing:
    1. Debugger: reproduce & identify root cause
    2. Coder: implement fix
    3. Tester: verify fix + regression tests
    4. Reviewer: approve changes
    """
    
    def __init__(self, agents: Dict[str, Agent]):
        self.agents = agents
        self.bus = MessageBus()
    
    def fix_bug(self, bug_report: str, codebase_context: str) -> Dict:
        """Pipeline: Reproduce → Fix → Test → Review"""
        
        # Step 1: Debug
        debugger = self.agents["debugger"]
        debug_result = debugger.think({
            "task": f"Analyze this bug report and identify root cause:\n"
                    f"{bug_report}\n\nCode context:\n{codebase_context}"
        })
        
        # Step 2: Fix
        coder = self.agents["coder"]
        fix = coder.think({
            "task": f"Fix this bug based on analysis:\n{debug_result}"
        })
        
        # Step 3: Test
        tester = self.agents["tester"]
        test_result = tester.think({
            "task": f"Write and run tests for this fix:\n{fix}"
        })
        
        # Step 4: Review
        reviewer = self.agents["reviewer"]
        review = reviewer.think({
            "task": f"Review this bug fix:\n"
                    f"Bug: {bug_report}\n"
                    f"Fix: {fix}\n"
                    f"Tests: {test_result}"
        })
        
        return {
            "debug": debug_result,
            "fix": fix,
            "tests": test_result,
            "review": review,
            "approved": "APPROVE" in review.upper(),
        }
```

---

## Best Practices

```
┌──────────────────────────────────────────────────────────────────┐
│              MULTI-AGENT BEST PRACTICES                          │
│                                                                  │
│  1. CLEAR ROLE SEPARATION                                       │
│     Mỗi agent 1 việc, không overlap responsibilities          │
│     → Ít conflict, dễ debug                                    │
│                                                                  │
│  2. STRUCTURED COMMUNICATION                                    │
│     Dùng message protocol rõ ràng                              │
│     → Không nói linh tinh, có format chuẩn                     │
│                                                                  │
│  3. SHARED STATE MANAGEMENT                                     │
│     Centralized memory, versioned, with locking                 │
│     → Tránh race conditions                                     │
│                                                                  │
│  4. FAIL-FAST DETECTION                                         │
│     Phát hiện deadlock, infinite loops                          │
│     → Timeout + circuit breaker                                 │
│                                                                  │
│  5. START SIMPLE                                                 │
│     Bắt đầu với 2-3 agents, thêm khi cần                      │
│     → Complexity grows with necessity                           │
│                                                                  │
│  6. OBSERVABLE COMMUNICATION                                    │
│     Log tất cả messages giữa agents                             │
│     → Dễ debug, audit                                           │
│                                                                  │
│  7. HUMAN-IN-THE-LOOP                                           │
│     Cho phép human approve/override decisions                   │
│     → Safety net cho critical decisions                         │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## Tài Liệu Tham Khảo

- [CrewAI Documentation](https://docs.crewai.com/)
- [AutoGen by Microsoft](https://microsoft.github.io/autogen/)
- [LangGraph Multi-Agent](https://langchain-ai.github.io/langgraph/)
- [MetaGPT](https://github.com/geekan/MetaGPT)