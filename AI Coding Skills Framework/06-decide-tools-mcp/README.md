# 🔧 VI. Decide Tools / MCP Calls

> **"AI Agent không giỏi vì biết nhiều – nó giỏi vì biết KHI NÀO dùng CÔNG CỤ NÀO"**

### Tại Sao Tool Decision Quan Trọng?

> *"Một agent có 50 tools nhưng chọn sai tool = người thợ có 50 cái búa nhưng dùng búa đập kính."*

#### Bằng chứng nghiên cứu:

1. **Anthropic MCP Research (2025)**: Agent sử dụng structured tool selection giảm **60% failed tool calls** so với unstructured approach.
2. **LangChain Benchmark (2025)**: Tool selection accuracy tăng từ 65% lên 92% khi sử dụng intent classification trước tool routing.
3. **Princeton SWE-agent (2024)**: Giới hạn 50 search results + tool validation tăng **64% success rate**.

#### Triết lý cốt lõi:

```
Tool Selection = Intent Classification → Tool Matching → Parameter Validation → Execution → Result Validation
```

**Analogies**: Tool decision giống bác sĩ kê đơn — phải chẩn đoán đúng bệnh (intent), chọn đúng thuốc (tool), đúng liều (parameters), và theo dõi tác dụng phụ (result validation).

**Nếu bỏ qua**: Agent gọi tool sai → lãng phí tokens, kết quả sai, hoặc worse — side effects không mong muốn (xóa file, gửi email sai, v.v.).

## Tổng Quan

AI Agent cần biết **khi nào dùng tool nào** và **gọi tool đó như thế nào**. Đây là quá trình quyết định: Phân tích task → Chọn tool → Gọi API → Xử lý kết quả.

Trong Harness Engineering, Tool Decision là **"bộ tay chân"** — nơi planning được convert thành hành động thực tế. Mọi tool call đều phải đi qua guardrails, validation, và logging.

```
┌──────────────────────────────────────────────────────────────────┐
│                 DECIDE TOOLS / MCP CALLS                          │
│                                                                  │
│  User Query                                                      │
│       │                                                          │
│       ▼                                                          │
│  ┌────────────────┐                                             │
│  │  INTENT        │  Phân tích ý định user                     │
│  │  CLASSIFIER    │  "search" / "calculate" / "code" / ...     │
│  └───────┬────────┘                                             │
│          ▼                                                       │
│  ┌────────────────┐                                             │
│  │  TOOL          │  Chọn tool phù hợp từ danh sách            │
│  │  SELECTOR      │  "vector_search" / "calculator" / ...      │
│  └───────┬────────┘                                             │
│          ▼                                                       │
│  ┌────────────────┐                                             │
│  │  PARAMETER     │  Trích xuất & validate parameters           │
│  │  EXTRACTOR     │  query="BHYT", top_k=5, ...                │
│  └───────┬────────┘                                             │
│          ▼                                                       │
│  ┌────────────────┐                                             │
│  │  GUARDRAILS    │  Validate permission, safety, rate limit    │
│  │  CHECK         │                                             │
│  └───────┬────────┘                                             │
│          ▼                                                       │
│  ┌────────────────┐                                             │
│  │  TOOL          │  Gọi tool (function call / MCP)             │
│  │  EXECUTOR      │  Handle errors, timeout, retry              │
│  └───────┬────────┘                                             │
│          ▼                                                       │
│  ┌────────────────┐                                             │
│  │  RESULT        │  Parse kết quả → Format → Trả lời          │
│  │  PROCESSOR     │                                             │
│  └────────────────┘                                             │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │  HARNESS INTEGRATION                                       │  │
│  │  Memory ←→ Guardrails ←→ Feedback ←→ Permissions          │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## Nội Dung

| # | Chủ đề | Mô tả |
|---|--------|-------|
| 1 | [Tool Selection Patterns](#1-tool-selection-patterns) | Registry, search, categories |
| 2 | [Intent Classification](#2-intent-classification) | Rule-based & LLM-based |
| 3 | [MCP Protocol](#3-mcp-protocol) | Model Context Protocol chi tiết |
| 4 | [Tool Executor](#4-tool-executor) | Error handling, retries, logging |
| 5 | [Function Calling](#5-function-calling) | OpenAI style + parallel calls |
| 6 | [Tool Decision Pipeline](#6-tool-decision-pipeline) | End-to-end pipeline |
| 7 | [Tool Composition](#7-tool-composition) | Chain & parallel tool calls |
| 8 | [Permission System](#8-permission-system) | RBAC cho tools |
| 9 | [Rate Limiting](#9-rate-limiting) | Token/usage quotas |
| 10 | [Harness Integration](#10-harness-integration) | TypeScript interfaces |
| 11 | [Case Studies](#11-case-studies) | SWE-agent, Claude Code, Cursor |
| 12 | [Design Principles](#12-design-principles) | SOLID cho tools |
| 13 | [Best Practices](#13-best-practices) | DO/DON'T chi tiết |
| 14 | [Testing](#14-testing) | Unit & integration tests |
| 15 | [Advanced Patterns](#15-advanced-patterns) | Tool learning, dynamic tools |
| 16 | [Tương Lai](#16-tương-lai) | Xu hướng 2026-2028 |

---

## 1. Tool Selection Patterns

### 1.1 Tool Registry

```python
from typing import Any, Callable, Dict, List, Optional, Set
from dataclasses import dataclass, field
from datetime import datetime, timedelta
import json
import hashlib
import time

@dataclass
class ToolDefinition:
    """
    Production-grade tool definition with full metadata
    """
    name: str
    description: str
    parameters: Dict[str, Dict]  # {param_name: {"type": ..., "description": ...}}
    function: Callable = None
    category: str = "general"
    examples: List[Dict] = field(default_factory=list)
    
    # Harness metadata
    version: str = "1.0.0"
    author: str = "system"
    tags: List[str] = field(default_factory=list)
    requires_auth: bool = False
    requires_permission: str = "standard"  # standard, elevated, admin
    rate_limit_per_minute: int = 60
    timeout_seconds: int = 30
    max_retries: int = 3
    cost_per_call: float = 0.0  # Token cost estimate
    deprecation_date: Optional[str] = None
    replacement_tool: Optional[str] = None
    
    # Performance metrics
    avg_latency_ms: float = 0.0
    success_rate: float = 1.0
    total_calls: int = 0
    
    def to_dict(self) -> Dict:
        return {
            "name": self.name,
            "description": self.description,
            "parameters": self.parameters,
            "category": self.category,
            "version": self.version,
            "tags": self.tags,
            "requires_auth": self.requires_auth,
            "requires_permission": self.requires_permission,
            "rate_limit_per_minute": self.rate_limit_per_minute,
            "timeout_seconds": self.timeout_seconds,
            "avg_latency_ms": self.avg_latency_ms,
            "success_rate": self.success_rate,
            "total_calls": self.total_calls,
        }
    
    def is_deprecated(self) -> bool:
        if self.deprecation_date:
            return datetime.now().isoformat() > self.deprecation_date
        return False
    
    def update_metrics(self, latency_ms: float, success: bool):
        """Update running metrics"""
        self.total_calls += 1
        # Exponential moving average
        alpha = 0.1
        self.avg_latency_ms = alpha * latency_ms + (1 - alpha) * self.avg_latency_ms
        self.success_rate = alpha * (1.0 if success else 0.0) + (1 - alpha) * self.success_rate


class ToolRegistry:
    """
    Central registry for all available tools
    
    Supports: Registration, search, category filtering,
    versioning, deprecation, metrics tracking
    """
    
    def __init__(self):
        self.tools: Dict[str, ToolDefinition] = {}
        self.categories: Dict[str, List[str]] = {}
        self.usage_log: List[Dict] = []
        self._aliases: Dict[str, str] = {}
    
    def register(self, tool: ToolDefinition):
        """Register a new tool"""
        if tool.name in self.tools:
            existing = self.tools[tool.name]
            if existing.version != tool.version:
                print(f"Warning: Updating tool '{tool.name}' from v{existing.version} to v{tool.version}")
        
        self.tools[tool.name] = tool
        if tool.category not in self.categories:
            self.categories[tool.category] = []
        if tool.name not in self.categories[tool.category]:
            self.categories[tool.category].append(tool.name)
    
    def unregister(self, name: str) -> bool:
        """Remove a tool from registry"""
        if name in self.tools:
            tool = self.tools.pop(name)
            if tool.category in self.categories:
                self.categories[tool.category] = [
                    n for n in self.categories[tool.category] if n != name
                ]
            return True
        return False
    
    def add_alias(self, alias: str, tool_name: str):
        """Add an alias for a tool"""
        self._aliases[alias] = tool_name
    
    def resolve_alias(self, name: str) -> str:
        """Resolve alias to actual tool name"""
        return self._aliases.get(name, name)
    
    def get_tool(self, name: str) -> Optional[ToolDefinition]:
        resolved = self.resolve_alias(name)
        return self.tools.get(resolved)
    
    def list_tools(self, category=None, include_deprecated=False) -> List[ToolDefinition]:
        if category:
            names = self.categories.get(category, [])
            tools = [self.tools[n] for n in names if n in self.tools]
        else:
            tools = list(self.tools.values())
        
        if not include_deprecated:
            tools = [t for t in tools if not t.is_deprecated()]
        
        return tools
    
    def search(self, query: str, use_embedding: bool = False, embed_func=None) -> List[ToolDefinition]:
        """
        Search tools by keyword or semantic similarity
        """
        query_lower = query.lower()
        results = []
        
        for tool in self.tools.values():
            # Keyword matching
            name_match = query_lower in tool.name.lower()
            desc_match = query_lower in tool.description.lower()
            tag_match = any(query_lower in tag.lower() for tag in tool.tags)
            category_match = query_lower in tool.category.lower()
            
            if name_match or desc_match or tag_match or category_match:
                results.append(tool)
        
        # Semantic search if enabled
        if use_embedding and embed_func and not results:
            results = self._semantic_search(query, embed_func)
        
        # Sort by relevance (success rate * inverse latency)
        results.sort(
            key=lambda t: t.success_rate / max(t.avg_latency_ms, 1),
            reverse=True,
        )
        
        return results
    
    def _semantic_search(self, query: str, embed_func) -> List[ToolDefinition]:
        """Find tools by semantic similarity"""
        query_emb = embed_func(query)
        scored = []
        
        for tool in self.tools.values():
            tool_emb = embed_func(f"{tool.name}: {tool.description}")
            sim = self._cosine_sim(query_emb, tool_emb)
            if sim > 0.5:  # Threshold
                scored.append((sim, tool))
        
        scored.sort(key=lambda x: x[0], reverse=True)
        return [tool for _, tool in scored[:5]]
    
    def _cosine_sim(self, a, b):
        import numpy as np
        a, b = np.array(a), np.array(b)
        norm_a, norm_b = np.linalg.norm(a), np.linalg.norm(b)
        if norm_a == 0 or norm_b == 0:
            return 0.0
        return float(np.dot(a, b) / (norm_a * norm_b))
    
    def to_openai_format(self, include_deprecated=False) -> List[Dict]:
        """Export tools in OpenAI function calling format"""
        tools = self.list_tools(include_deprecated=include_deprecated)
        return [
            {
                "type": "function",
                "function": {
                    "name": t.name,
                    "description": t.description,
                    "parameters": {
                        "type": "object",
                        "properties": t.parameters,
                        "required": [
                            k for k, v in t.parameters.items() 
                            if v.get("required", False)
                        ],
                    },
                }
            }
            for t in tools
        ]
    
    def to_mcp_format(self) -> List[Dict]:
        """Export tools in MCP format"""
        return [
            {
                "name": t.name,
                "description": t.description,
                "inputSchema": {
                    "type": "object",
                    "properties": t.parameters,
                },
            }
            for t in self.tools.values()
        ]
    
    def get_deprecated_tools(self) -> List[ToolDefinition]:
        """Get all deprecated tools"""
        return [t for t in self.tools.values() if t.is_deprecated()]
    
    def get_tool_stats(self) -> Dict:
        """Get aggregate stats for all tools"""
        tools = list(self.tools.values())
        total_calls = sum(t.total_calls for t in tools)
        avg_success = sum(t.success_rate for t in tools) / len(tools) if tools else 0
        
        return {
            "total_tools": len(tools),
            "total_categories": len(self.categories),
            "total_calls": total_calls,
            "avg_success_rate": avg_success,
            "deprecated": len([t for t in tools if t.is_deprecated()]),
            "by_category": {
                cat: len(names) for cat, names in self.categories.items()
            },
        }
```

### 1.2 Example Tools — Extended Set

```python
def create_default_tools():
    """Create comprehensive default toolset"""
    registry = ToolRegistry()
    
    # ─── Search Tools ───
    registry.register(ToolDefinition(
        name="vector_search",
        description="Tìm kiếm ngữ nghĩa trong cơ sở dữ liệu vector",
        parameters={
            "query": {"type": "string", "description": "Câu truy vấn", "required": True},
            "top_k": {"type": "integer", "description": "Số kết quả trả về", "default": 5},
            "collection": {"type": "string", "description": "Tên collection"},
            "filters": {"type": "object", "description": "Metadata filters"},
        },
        category="search",
        tags=["semantic", "vector", "embedding"],
        examples=[
            {"query": "BHYT cho người lao động", "top_k": 3},
        ],
    ))
    
    registry.register(ToolDefinition(
        name="web_search",
        description="Tìm kiếm trên internet",
        parameters={
            "query": {"type": "string", "description": "Từ khóa tìm kiếm", "required": True},
            "num_results": {"type": "integer", "default": 5},
            "region": {"type": "string", "default": "vn"},
        },
        category="search",
        tags=["internet", "realtime", "web"],
    ))
    
    registry.register(ToolDefinition(
        name="sql_query",
        description="Thực thi SQL query trên database",
        parameters={
            "query": {"type": "string", "description": "SQL query", "required": True},
            "database": {"type": "string", "default": "main"},
            "readonly": {"type": "boolean", "default": True},
        },
        category="search",
        requires_permission="elevated",
        tags=["database", "sql", "structured"],
    ))
    
    # ─── Computation Tools ───
    registry.register(ToolDefinition(
        name="calculator",
        description="Tính toán toán học an toàn",
        parameters={
            "expression": {"type": "string", "description": "Biểu thức toán học", "required": True},
        },
        category="computation",
        tags=["math", "arithmetic"],
    ))
    
    registry.register(ToolDefinition(
        name="execute_python",
        description="Thực thi code Python trong sandbox",
        parameters={
            "code": {"type": "string", "description": "Python code", "required": True},
            "timeout": {"type": "integer", "default": 30},
        },
        category="computation",
        requires_permission="elevated",
        tags=["python", "code", "sandbox"],
    ))
    
    registry.register(ToolDefinition(
        name="data_analysis",
        description="Phân tích dữ liệu với pandas/numpy",
        parameters={
            "data": {"type": "string", "description": "Data path hoặc CSV string", "required": True},
            "analysis_type": {"type": "string", "enum": ["summary", "correlation", "distribution", "trend"]},
            "columns": {"type": "array", "items": {"type": "string"}},
        },
        category="computation",
        tags=["analytics", "pandas", "statistics"],
    ))
    
    # ─── Code Tools ───
    registry.register(ToolDefinition(
        name="read_file",
        description="Đọc nội dung file",
        parameters={
            "path": {"type": "string", "description": "Đường dẫn file", "required": True},
            "encoding": {"type": "string", "default": "utf-8"},
            "max_lines": {"type": "integer", "description": "Max lines to read"},
        },
        category="file",
        tags=["read", "file", "filesystem"],
    ))
    
    registry.register(ToolDefinition(
        name="write_file",
        description="Ghi nội dung vào file",
        parameters={
            "path": {"type": "string", "description": "Đường dẫn file", "required": True},
            "content": {"type": "string", "description": "Nội dung", "required": True},
            "mode": {"type": "string", "enum": ["overwrite", "append"], "default": "overwrite"},
        },
        category="file",
        requires_permission="elevated",
        tags=["write", "file", "filesystem"],
    ))
    
    registry.register(ToolDefinition(
        name="search_code",
        description="Tìm kiếm code trong codebase",
        parameters={
            "query": {"type": "string", "description": "Regex hoặc text pattern", "required": True},
            "path": {"type": "string", "default": "."},
            "file_pattern": {"type": "string", "description": "glob pattern"},
        },
        category="code",
        tags=["code", "search", "regex"],
    ))
    
    registry.register(ToolDefinition(
        name="run_tests",
        description="Chạy test suite",
        parameters={
            "test_path": {"type": "string", "description": "Path to test file/directory"},
            "framework": {"type": "string", "enum": ["pytest", "unittest", "jest", "vitest"]},
            "verbose": {"type": "boolean", "default": True},
        },
        category="code",
        tags=["testing", "ci", "quality"],
    ))
    
    # ─── Knowledge Tools ───
    registry.register(ToolDefinition(
        name="query_knowledge_graph",
        description="Truy vấn knowledge graph",
        parameters={
            "entity": {"type": "string", "description": "Tên entity", "required": True},
            "relation": {"type": "string", "description": "Loại relation"},
            "max_hops": {"type": "integer", "default": 1},
        },
        category="knowledge",
        tags=["kg", "graph", "entity"],
    ))
    
    registry.register(ToolDefinition(
        name="lookup_definition",
        description="Tra cứu định nghĩa thuật ngữ",
        parameters={
            "term": {"type": "string", "description": "Thuật ngữ cần tra cứu", "required": True},
            "domain": {"type": "string", "description": "Lĩnh vực"},
        },
        category="knowledge",
        tags=["definition", "glossary", "reference"],
    ))
    
    # ─── Memory Tools ───
    registry.register(ToolDefinition(
        name="recall_memory",
        description="Nhớ lại thông tin từ memory",
        parameters={
            "query": {"type": "string", "description": "Câu truy vấn", "required": True},
            "memory_type": {"type": "string", "enum": ["episodic", "semantic", "all"]},
            "limit": {"type": "integer", "default": 5},
        },
        category="memory",
        tags=["recall", "memory", "history"],
    ))
    
    registry.register(ToolDefinition(
        name="store_memory",
        description="Lưu thông tin vào memory",
        parameters={
            "content": {"type": "string", "description": "Nội dung cần lưu", "required": True},
            "memory_type": {"type": "string", "enum": ["episodic", "semantic"]},
            "importance": {"type": "string", "enum": ["low", "medium", "high"]},
            "tags": {"type": "array", "items": {"type": "string"}},
        },
        category="memory",
        tags=["store", "memory", "persistence"],
    ))
    
    # ─── Communication Tools ───
    registry.register(ToolDefinition(
        name="send_notification",
        description="Gửi thông báo cho user",
        parameters={
            "message": {"type": "string", "description": "Nội dung thông báo", "required": True},
            "channel": {"type": "string", "enum": ["email", "slack", "in_app"], "default": "in_app"},
            "priority": {"type": "string", "enum": ["low", "normal", "high"], "default": "normal"},
        },
        category="communication",
        tags=["notify", "alert", "message"],
    ))
    
    # ─── Version Control Tools ───
    registry.register(ToolDefinition(
        name="git_status",
        description="Xem trạng thái git repository",
        parameters={
            "path": {"type": "string", "default": "."},
        },
        category="devops",
        tags=["git", "status", "vcs"],
    ))
    
    registry.register(ToolDefinition(
        name="git_diff",
        description="Xem diff của git changes",
        parameters={
            "ref": {"type": "string", "description": "Branch/commit to diff against"},
            "path": {"type": "string"},
        },
        category="devops",
        tags=["git", "diff", "changes"],
    ))
    
    return registry
```

---

## 2. Intent Classification

### 2.1 Multi-Strategy Intent Classifier

```python
class IntentClassifier:
    """
    Multi-strategy intent classification:
    1. Rule-based (fast, no LLM needed)
    2. Embedding-based (moderate accuracy)
    3. LLM-based (highest accuracy, most expensive)
    
    Auto-selects strategy based on confidence threshold
    """
    
    INTENT_RULES = {
        "search": {
            "keywords": ["tìm", "search", "kiếm", "tra cứu", "google", "lookup", "find"],
            "tools": ["vector_search", "web_search", "query_knowledge_graph"],
            "priority": 1,
        },
        "calculate": {
            "keywords": ["tính", "calculate", "math", "bao nhiêu", "phần trăm", "sum", "average"],
            "tools": ["calculator", "execute_python", "data_analysis"],
            "priority": 2,
        },
        "code": {
            "keywords": ["code", "viết code", "program", "function", "script", "implement", "refactor"],
            "tools": ["execute_python", "write_file", "search_code"],
            "priority": 1,
        },
        "read": {
            "keywords": ["đọc", "read", "xem", "open", "file", "show", "display", "cat"],
            "tools": ["read_file", "git_status"],
            "priority": 2,
        },
        "write": {
            "keywords": ["ghi", "write", "lưu", "save", "tạo file", "create", "update"],
            "tools": ["write_file", "store_memory"],
            "priority": 2,
        },
        "remember": {
            "keywords": ["nhớ", "remember", "lưu ý", "ghi nhớ", "don't forget"],
            "tools": ["store_memory"],
            "priority": 3,
        },
        "recall": {
            "keywords": ["nhắc lại", "recall", "trước đó", "đã nói", "history", "previous"],
            "tools": ["recall_memory"],
            "priority": 3,
        },
        "knowledge": {
            "keywords": ["biết gì", "knowledge", "facts", "thông tin về", "define", "what is"],
            "tools": ["query_knowledge_graph", "vector_search", "lookup_definition"],
            "priority": 2,
        },
        "test": {
            "keywords": ["test", "chạy test", "verify", "validate", "check"],
            "tools": ["run_tests"],
            "priority": 2,
        },
        "git": {
            "keywords": ["git", "commit", "branch", "diff", "merge", "push", "pull"],
            "tools": ["git_status", "git_diff"],
            "priority": 2,
        },
    }
    
    def __init__(self, llm_func=None):
        self.llm = llm_func
        self.classification_history = []
    
    def classify_rule_based(self, query: str) -> Dict:
        """Fast rule-based classification"""
        query_lower = query.lower()
        scores = {}
        
        for intent, config in self.INTENT_RULES.items():
            score = sum(1 for kw in config["keywords"] if kw in query_lower)
            # Boost by priority
            if score > 0:
                score = score * (4 - config.get("priority", 2))
                scores[intent] = score
        
        if not scores:
            return {"intent": "chat", "tools": [], "confidence": 0.5, "method": "rule"}
        
        best_intent = max(scores, key=scores.get)
        confidence = min(scores[best_intent] / 5, 1.0)
        
        return {
            "intent": best_intent,
            "tools": self.INTENT_RULES[best_intent]["tools"],
            "confidence": confidence,
            "method": "rule",
            "all_scores": scores,
        }
    
    def classify_llm_based(self, query: str) -> Dict:
        """LLM-based classification (more accurate, more expensive)"""
        intents_desc = "\n".join([
            f"- {intent}: keywords={config['keywords'][:3]}..., tools={config['tools']}"
            for intent, config in self.INTENT_RULES.items()
        ])
        
        prompt = f"""Phân tích ý định của user và chọn tool phù hợp nhất.

User query: "{query}"

Các intent có thể:
{intents_desc}

Output JSON:
{{
  "intent": "...",
  "tools": ["tool1", "tool2"],
  "confidence": 0.0-1.0,
  "reasoning": "Tại sao chọn intent này",
  "parameters_hint": {{"param": "suggested value"}}
}}"""
        
        response = self.llm(prompt)
        try:
            result = json.loads(response)
            result["method"] = "llm"
            return result
        except json.JSONDecodeError:
            return self.classify_rule_based(query)  # Fallback
    
    def classify(self, query: str, strategy: str = "auto") -> Dict:
        """
        Classify with auto-strategy selection
        
        Strategies:
        - "auto": Rule first, LLM if confidence < threshold
        - "rule": Rule-based only
        - "llm": LLM-based only
        - "ensemble": Combine both
        """
        
        if strategy == "rule" or strategy == "auto":
            rule_result = self.classify_rule_based(query)
            
            if strategy == "rule":
                return rule_result
            
            # Auto: use LLM if rule confidence is low
            if rule_result["confidence"] >= 0.7:
                return rule_result
            
            if self.llm:
                llm_result = self.classify_llm_based(query)
                
                # Take the higher confidence result
                if llm_result.get("confidence", 0) > rule_result["confidence"]:
                    return llm_result
            
            return rule_result
        
        elif strategy == "llm":
            if self.llm:
                return self.classify_llm_based(query)
            return self.classify_rule_based(query)
        
        elif strategy == "ensemble":
            rule_result = self.classify_rule_based(query)
            llm_result = None
            
            if self.llm:
                llm_result = self.classify_llm_based(query)
            
            if llm_result and rule_result["intent"] == llm_result.get("intent"):
                # Agreement → high confidence
                return {
                    "intent": rule_result["intent"],
                    "tools": rule_result["tools"],
                    "confidence": 0.95,
                    "method": "ensemble",
                }
            elif llm_result:
                # Disagreement → use LLM
                llm_result["method"] = "ensemble_override"
                return llm_result
            
            return rule_result
        
        return self.classify_rule_based(query)
    
    def classify_multi_intent(self, query: str) -> List[Dict]:
        """
        Detect multiple intents in a single query
        E.g., "Tìm file README và chạy test" → [read, test]
        """
        # Split by conjunction words
        import re
        parts = re.split(r'\s+(và|and|;|rồi|then|after that)\s+', query)
        
        intents = []
        for part in parts:
            part = part.strip()
            if part.lower() in ['và', 'and', ';', 'rồi', 'then', 'after that']:
                continue
            if len(part) > 3:  # Skip very short parts
                intent = self.classify(part)
                intents.append(intent)
        
        return intents if intents else [self.classify(query)]
```

---

## 3. MCP Protocol

### 3.1 MCP Architecture Deep Dive

```
┌──────────────────────────────────────────────────────────────────┐
│                  MCP (MODEL CONTEXT PROTOCOL)                     │
│                                                                  │
│  MCP là giao thức chuẩn để LLM giao tiếp với tools bên ngoài  │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    HOST (Client)                          │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐              │   │
│  │  │ Claude   │  │ GPT-4    │  │ Custom   │              │   │
│  │  │ Desktop  │  │ App      │  │ Agent    │              │   │
│  │  └────┬─────┘  └────┬─────┘  └────┬─────┘              │   │
│  │       └──────────────┼──────────────┘                    │   │
│  │                      ▼                                    │   │
│  │              MCP Client SDK                              │   │
│  └──────────────────────┬───────────────────────────────────┘   │
│                         │ JSON-RPC 2.0                           │
│                         │ (stdio/SSE/HTTP)                       │
│  ┌──────────────────────┴───────────────────────────────────┐   │
│  │                    SERVERS                                │   │
│  │                                                          │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐     │   │
│  │  │  GitHub     │  │  Database   │  │  File       │     │   │
│  │  │  Server     │  │  Server     │  │  Server     │     │   │
│  │  │  ─────────  │  │  ─────────  │  │  ─────────  │     │   │
│  │  │  - search   │  │  - query    │  │  - read     │     │   │
│  │  │  - create   │  │  - insert   │  │  - write    │     │   │
│  │  │  - update   │  │  - update   │  │  - delete   │     │   │
│  │  │  - delete   │  │  - schema   │  │  - list     │     │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘     │   │
│  │                                                          │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐     │   │
│  │  │  Web Search │  │  Slack      │  │  Custom     │     │   │
│  │  │  Server     │  │  Server     │  │  API Server │     │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘     │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  Protocol Features:                                              │
│  ├── Capabilities negotiation                                   │
│  ├── Tool discovery (tools/list)                                │
│  ├── Tool execution (tools/call)                                │
│  ├── Resource access (resources/read)                           │
│  ├── Prompt templates (prompts/list, prompts/get)              │
│  ├── Sampling (sampling/createMessage)                          │
│  └── Logging (logging/setLevel)                                │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 3.2 MCP Client Implementation

```python
import asyncio
from typing import Any, Dict, List, Optional

class MCPClient:
    """
    Production MCP Client for calling MCP tools
    
    Supports: Tool listing, tool calling, resource access,
    prompt templates, sampling, error handling
    """
    
    def __init__(self, server_name: str, transport: str = "stdio", config: Dict = None):
        self.server_name = server_name
        self.transport = transport
        self.config = config or {}
        self._tools_cache: List[Dict] = []
        self._resources_cache: List[Dict] = []
        self._prompts_cache: List[Dict] = []
        self._connected = False
        self._request_id = 0
        self._server_capabilities = {}
        self._latencies = []
    
    async def connect(self) -> bool:
        """Connect to MCP server with capability negotiation"""
        try:
            # In real implementation:
            # 1. Start server subprocess
            # 2. Send initialize request
            # 3. Receive capabilities
            
            self._server_capabilities = {
                "tools": {"listChanged": True},
                "resources": {"subscribe": True},
                "prompts": {},
                "logging": {},
            }
            
            self._connected = True
            return True
            
        except Exception as e:
            print(f"Failed to connect to {self.server_name}: {e}")
            return False
    
    async def disconnect(self):
        """Gracefully disconnect"""
        self._connected = False
        self._tools_cache = []
    
    async def list_tools(self) -> List[Dict]:
        """List available tools from MCP server"""
        if not self._connected:
            await self.connect()
        
        # Cache tools
        self._tools_cache = await self._send_request("tools/list", {})
        return self._tools_cache
    
    async def call_tool(self, tool_name: str, arguments: Dict, 
                        timeout: float = 30.0) -> Dict:
        """Call a tool on MCP server with timeout and error handling"""
        if not self._connected:
            await self.connect()
        
        start_time = time.time()
        
        try:
            response = await asyncio.wait_for(
                self._send_request("tools/call", {
                    "name": tool_name,
                    "arguments": arguments,
                }),
                timeout=timeout,
            )
            
            elapsed_ms = (time.time() - start_time) * 1000
            self._latencies.append(elapsed_ms)
            
            return {
                "success": True,
                "content": response.get("content", []),
                "isError": response.get("isError", False),
                "latency_ms": elapsed_ms,
                "server": self.server_name,
            }
            
        except asyncio.TimeoutError:
            return {
                "success": False,
                "error": f"Timeout after {timeout}s",
                "content": [],
                "server": self.server_name,
            }
        except Exception as e:
            return {
                "success": False,
                "error": str(e),
                "content": [],
                "server": self.server_name,
            }
    
    async def read_resource(self, uri: str) -> Dict:
        """Read a resource from MCP server"""
        if not self._connected:
            await self.connect()
        
        return await self._send_request("resources/read", {"uri": uri})
    
    async def list_resources(self) -> List[Dict]:
        """List available resources"""
        if not self._connected:
            await self.connect()
        
        self._resources_cache = await self._send_request("resources/list", {})
        return self._resources_cache
    
    async def get_prompt(self, name: str, arguments: Dict = None) -> Dict:
        """Get a prompt template from server"""
        return await self._send_request("prompts/get", {
            "name": name,
            "arguments": arguments or {},
        })
    
    async def _send_request(self, method: str, params: Dict) -> Dict:
        """Send JSON-RPC request to MCP server"""
        self._request_id += 1
        
        request = {
            "jsonrpc": "2.0",
            "id": self._request_id,
            "method": method,
            "params": params,
        }
        
        # In production: send via subprocess stdin/stdout or HTTP
        # For now, simulate response
        return {}
    
    def format_tools_for_llm(self, provider: str = "openai") -> List[Dict]:
        """Format MCP tools for specific LLM provider"""
        if provider == "openai":
            return [
                {
                    "type": "function",
                    "function": {
                        "name": t["name"],
                        "description": t.get("description", ""),
                        "parameters": t.get("inputSchema", {}),
                    }
                }
                for t in self._tools_cache
            ]
        elif provider == "anthropic":
            return [
                {
                    "name": t["name"],
                    "description": t.get("description", ""),
                    "input_schema": t.get("inputSchema", {}),
                }
                for t in self._tools_cache
            ]
        
        return self._tools_cache
    
    def get_latency_stats(self) -> Dict:
        """Get latency statistics"""
        if not self._latencies:
            return {"avg_ms": 0, "p50_ms": 0, "p95_ms": 0, "p99_ms": 0, "count": 0}
        
        sorted_lat = sorted(self._latencies)
        n = len(sorted_lat)
        
        return {
            "avg_ms": sum(sorted_lat) / n,
            "p50_ms": sorted_lat[n // 2],
            "p95_ms": sorted_lat[int(n * 0.95)],
            "p99_ms": sorted_lat[int(n * 0.99)],
            "min_ms": sorted_lat[0],
            "max_ms": sorted_lat[-1],
            "count": n,
        }


class MultiServerMCPManager:
    """
    Manage connections to multiple MCP servers
    
    Features:
    - Auto-discovery
    - Load balancing
    - Failover
    - Unified tool namespace
    """
    
    def __init__(self):
        self.servers: Dict[str, MCPClient] = {}
        self._tool_to_server: Dict[str, str] = {}
    
    async def add_server(self, name: str, server: MCPClient):
        """Add and connect to a server"""
        await server.connect()
        self.servers[name] = server
        
        # Index tools
        tools = await server.list_tools()
        for tool in tools:
            self._tool_to_server[tool["name"]] = name
    
    async def remove_server(self, name: str):
        """Disconnect and remove a server"""
        if name in self.servers:
            await self.servers[name].disconnect()
            del self.servers[name]
            
            # Rebuild index
            self._tool_to_server = {
                tool: srv for tool, srv in self._tool_to_server.items()
                if srv != name
            }
    
    def get_all_tools(self) -> List[Dict]:
        """Get tools from all servers, unified namespace"""
        all_tools = []
        for server_name, server in self.servers.items():
            for tool in server._tools_cache:
                tool_copy = dict(tool)
                tool_copy["_server"] = server_name
                all_tools.append(tool_copy)
        return all_tools
    
    async def call_tool(self, tool_name: str, arguments: Dict) -> Dict:
        """Call tool, routing to correct server"""
        server_name = self._tool_to_server.get(tool_name)
        
        if not server_name:
            return {
                "success": False,
                "error": f"No server found for tool '{tool_name}'",
            }
        
        server = self.servers.get(server_name)
        if not server:
            return {
                "success": False,
                "error": f"Server '{server_name}' not available",
            }
        
        result = await server.call_tool(tool_name, arguments)
        
        # If failed, try failover to other servers
        if not result.get("success"):
            for other_name, other_server in self.servers.items():
                if other_name != server_name:
                    tools = [t["name"] for t in other_server._tools_cache]
                    if tool_name in tools:
                        return await other_server.call_tool(tool_name, arguments)
        
        return result
    
    def get_server_stats(self) -> Dict:
        """Get stats for all servers"""
        stats = {}
        for name, server in self.servers.items():
            stats[name] = {
                "tools_count": len(server._tools_cache),
                "connected": server._connected,
                "latency": server.get_latency_stats(),
            }
        return stats
```

---

## 4. Tool Executor

```python
import time
from typing import Any, Dict, Optional, Callable
from datetime import datetime

class ToolExecutor:
    """
    Execute tools with comprehensive error handling:
    - Parameter validation
    - Retry with exponential backoff
    - Timeout handling
    - Rate limiting
    - Permission checking
    - Cost tracking
    - Execution logging
    """
    
    def __init__(self, registry: ToolRegistry, 
                 permission_checker=None,
                 rate_limiter=None):
        self.registry = registry
        self.permission_checker = permission_checker
        self.rate_limiter = rate_limiter
        self.execution_log: List[Dict] = []
        self._call_counts: Dict[str, int] = {}
    
    def execute(self, tool_name: str, parameters: Dict, 
                user_id: str = None,
                timeout: float = None) -> Dict:
        """
        Execute a tool with full guardrails
        """
        start_time = time.time()
        
        # 1. Resolve tool
        tool = self.registry.get_tool(tool_name)
        if not tool:
            return self._error_result(tool_name, "Tool not found", start_time)
        
        # 2. Check deprecation
        if tool.is_deprecated():
            replacement = tool.replacement_tool or "unknown"
            return self._error_result(
                tool_name, 
                f"Tool deprecated. Use '{replacement}' instead.",
                start_time,
            )
        
        # 3. Validate parameters
        validation = self._validate_params(tool, parameters)
        if not validation["valid"]:
            return self._error_result(
                tool_name,
                f"Invalid parameters: {validation['errors']}",
                start_time,
            )
        
        # 4. Check permissions
        if self.permission_checker and user_id:
            perm_check = self.permission_checker(user_id, tool.requires_permission)
            if not perm_check.get("allowed"):
                return self._error_result(
                    tool_name,
                    f"Permission denied: {perm_check.get('reason', 'insufficient permissions')}",
                    start_time,
                )
        
        # 5. Check rate limit
        if self.rate_limiter:
            rate_check = self.rate_limiter(tool_name)
            if not rate_check.get("allowed"):
                return self._error_result(
                    tool_name,
                    f"Rate limit exceeded: {rate_check.get('retry_after', 60)}s",
                    start_time,
                )
        
        # 6. Execute with retries
        max_retries = tool.max_retries
        effective_timeout = timeout or tool.timeout_seconds
        last_error = None
        
        for attempt in range(max_retries):
            try:
                # Set timeout per-attempt
                if tool.function:
                    result = tool.function(**parameters)
                else:
                    result = f"Tool '{tool_name}' has no function implementation"
                
                elapsed_ms = (time.time() - start_time) * 1000
                
                # Update metrics
                tool.update_metrics(elapsed_ms, success=True)
                
                # Log success
                log_entry = {
                    "tool": tool_name,
                    "params": parameters,
                    "success": True,
                    "attempt": attempt + 1,
                    "elapsed_ms": round(elapsed_ms, 2),
                    "user_id": user_id,
                    "timestamp": datetime.now().isoformat(),
                }
                self.execution_log.append(log_entry)
                self._call_counts[tool_name] = self._call_counts.get(tool_name, 0) + 1
                
                return {
                    "success": True,
                    "result": result,
                    "attempts": attempt + 1,
                    "elapsed_ms": elapsed_ms,
                    "tool": tool_name,
                    "cost": tool.cost_per_call,
                }
                
            except TimeoutError:
                last_error = f"Timeout after {effective_timeout}s"
            except Exception as e:
                last_error = str(e)
            
            # Exponential backoff before retry
            if attempt < max_retries - 1:
                backoff = 0.5 * (2 ** attempt)
                time.sleep(min(backoff, 5.0))
        
        # All retries failed
        tool.update_metrics(
            (time.time() - start_time) * 1000, 
            success=False,
        )
        
        return self._error_result(
            tool_name,
            f"Failed after {max_retries} attempts: {last_error}",
            start_time,
        )
    
    def execute_batch(self, calls: List[Dict], parallel: bool = False) -> List[Dict]:
        """
        Execute multiple tool calls
        
        calls: [{"tool": "name", "params": {...}, "user_id": "..."}]
        parallel: If True, execute without waiting (simulated)
        """
        results = []
        
        for call in calls:
            result = self.execute(
                call["tool"],
                call.get("params", {}),
                user_id=call.get("user_id"),
            )
            results.append({
                "tool": call["tool"],
                **result,
            })
        
        return results
    
    def execute_chain(self, steps: List[Dict]) -> Dict:
        """
        Execute tools in chain, passing results as inputs
        
        steps: [
            {"tool": "search", "params": {"query": "..."}, "output_key": "search_result"},
            {"tool": "analyze", "params": {"data": "$search_result"}, "output_key": "analysis"},
        ]
        """
        results = {}
        
        for i, step in enumerate(steps):
            # Substitute variables
            params = {}
            for key, value in step.get("params", {}).items():
                if isinstance(value, str) and value.startswith("$"):
                    var_name = value[1:]
                    params[key] = results.get(var_name, value)
                else:
                    params[key] = value
            
            result = self.execute(step["tool"], params)
            
            if not result["success"]:
                return {
                    "success": False,
                    "error": f"Chain failed at step {i+1}: {result.get('error')}",
                    "completed_steps": i,
                    "results": results,
                }
            
            # Store result with output key
            output_key = step.get("output_key", f"step_{i+1}")
            results[output_key] = result["result"]
        
        return {
            "success": True,
            "results": results,
            "steps_completed": len(steps),
        }
    
    def _validate_params(self, tool: ToolDefinition, params: Dict) -> Dict:
        """Validate parameters against tool definition"""
        errors = []
        warnings = []
        
        # Check required params
        for param_name, param_def in tool.parameters.items():
            if param_def.get("required", False) and param_name not in params:
                errors.append(f"Missing required param: {param_name}")
        
        # Check types (basic)
        for param_name, value in params.items():
            if param_name in tool.parameters:
                expected_type = tool.parameters[param_name].get("type")
                if expected_type == "string" and not isinstance(value, str):
                    warnings.append(f"Param '{param_name}' expected string, got {type(value).__name__}")
                elif expected_type == "integer" and not isinstance(value, int):
                    warnings.append(f"Param '{param_name}' expected integer, got {type(value).__name__}")
        
        return {
            "valid": len(errors) == 0,
            "errors": errors,
            "warnings": warnings,
        }
    
    def _error_result(self, tool_name: str, error: str, start_time: float) -> Dict:
        """Create error result and log it"""
        elapsed_ms = (time.time() - start_time) * 1000
        
        self.execution_log.append({
            "tool": tool_name,
            "success": False,
            "error": error,
            "elapsed_ms": round(elapsed_ms, 2),
            "timestamp": datetime.now().isoformat(),
        })
        
        return {
            "success": False,
            "error": error,
            "result": None,
            "elapsed_ms": elapsed_ms,
            "tool": tool_name,
        }
    
    def get_stats(self) -> Dict:
        """Get comprehensive execution statistics"""
        total = len(self.execution_log)
        successful = sum(1 for log in self.execution_log if log["success"])
        failed = total - successful
        
        tool_counts = {}
        tool_errors = {}
        total_cost = 0.0
        
        for log in self.execution_log:
            tool = log.get("tool", "unknown")
            tool_counts[tool] = tool_counts.get(tool, 0) + 1
            
            if not log["success"]:
                tool_errors[tool] = tool_errors.get(tool, 0) + 1
        
        # Calculate total cost
        for tool_name, count in self._call_counts.items():
            tool = self.registry.get_tool(tool_name)
            if tool:
                total_cost += tool.cost_per_call * count
        
        return {
            "total_calls": total,
            "successful": successful,
            "failed": failed,
            "success_rate": successful / total if total > 0 else 0,
            "tool_usage": tool_counts,
            "tool_errors": tool_errors,
            "total_cost": total_cost,
            "avg_latency_ms": (
                sum(log.get("elapsed_ms", 0) for log in self.execution_log) / total
                if total > 0 else 0
            ),
        }
```

---

## 5. Function Calling

```python
class FunctionCallingAgent:
    """
    Agent using OpenAI-style function calling
    
    Supports: Multi-turn tool calls, parallel calls, forced tool use,
    chained tool calls, automatic retry on malformed calls
    """
    
    def __init__(self, llm_func, tool_registry):
        self.llm = llm_func
        self.registry = tool_registry
        self.executor = ToolExecutor(tool_registry)
        self.conversation_history = []
    
    def run(self, user_message: str, max_rounds: int = 10, 
            auto_retry: bool = True) -> Dict:
        """
        Execute agent loop with function calling
        """
        messages = self.conversation_history + [
            {"role": "user", "content": user_message}
        ]
        tools = self.registry.to_openai_format()
        
        total_tool_calls = 0
        tool_usage = {}
        
        for round_num in range(max_rounds):
            # Call LLM
            response = self._call_llm(messages, tools)
            
            # Check for tool calls
            if not response.get("tool_calls"):
                final_answer = response.get("content", "")
                self.conversation_history = messages + [
                    {"role": "assistant", "content": final_answer}
                ]
                return {
                    "answer": final_answer,
                    "rounds": round_num + 1,
                    "total_tool_calls": total_tool_calls,
                    "tool_usage": tool_usage,
                }
            
            # Add assistant message with tool calls
            messages.append({
                "role": "assistant",
                "content": response.get("content"),
                "tool_calls": response["tool_calls"],
            })
            
            # Execute each tool call
            for tc in response["tool_calls"]:
                func_name = tc["function"]["name"]
                total_tool_calls += 1
                tool_usage[func_name] = tool_usage.get(func_name, 0) + 1
                
                try:
                    func_args = json.loads(tc["function"]["arguments"])
                except json.JSONDecodeError as e:
                    if auto_retry:
                        # Ask LLM to fix malformed arguments
                        messages.append({
                            "role": "tool",
                            "tool_call_id": tc["id"],
                            "content": json.dumps({
                                "error": f"Invalid JSON arguments: {e}",
                                "hint": "Please provide valid JSON arguments",
                            }),
                        })
                        continue
                    func_args = {}
                
                # Execute tool
                result = self.executor.execute(func_name, func_args)
                
                messages.append({
                    "role": "tool",
                    "tool_call_id": tc["id"],
                    "content": json.dumps(result, ensure_ascii=False, default=str),
                })
        
        return {
            "answer": "Max rounds reached without final answer",
            "rounds": max_rounds,
            "total_tool_calls": total_tool_calls,
            "tool_usage": tool_usage,
        }
    
    def forced_tool_call(self, user_message: str, tool_name: str) -> Dict:
        """
        Force LLM to use a specific tool
        Useful when intent is clear from pre-classification
        """
        tool = self.registry.get_tool(tool_name)
        if not tool:
            return {"error": f"Tool '{tool_name}' not found"}
        
        messages = [{"role": "user", "content": user_message}]
        tools = self.registry.to_openai_format()
        
        # Force specific tool
        response = self._call_llm(
            messages, 
            tools,
            tool_choice={"type": "function", "function": {"name": tool_name}}
        )
        
        if response.get("tool_calls"):
            tc = response["tool_calls"][0]
            try:
                func_args = json.loads(tc["function"]["arguments"])
            except json.JSONDecodeError:
                func_args = {}
            
            result = self.executor.execute(tool_name, func_args)
            
            return {
                "tool": tool_name,
                "args": func_args,
                "result": result,
            }
        
        return {"error": "LLM did not call the forced tool"}
    
    def parallel_tool_calls(self, user_message: str) -> Dict:
        """
        Enable parallel tool calling
        LLM can call multiple tools simultaneously
        """
        messages = [{"role": "user", "content": user_message}]
        tools = self.registry.to_openai_format()
        
        response = self._call_llm(messages, tools)
        
        if response.get("tool_calls"):
            # Execute all in parallel (simulated with batch)
            calls = []
            for tc in response["tool_calls"]:
                try:
                    func_args = json.loads(tc["function"]["arguments"])
                except json.JSONDecodeError:
                    func_args = {}
                calls.append({
                    "tool": tc["function"]["name"],
                    "params": func_args,
                })
            
            results = self.executor.execute_batch(calls)
            
            return {
                "parallel_results": results,
                "tools_called": len(results),
                "all_success": all(r["success"] for r in results),
            }
        
        return {"answer": response.get("content", "")}
    
    def _call_llm(self, messages, tools, tool_choice=None):
        """Call LLM with function calling support"""
        if self.llm:
            kwargs = {"messages": messages, "tools": tools}
            if tool_choice:
                kwargs["tool_choice"] = tool_choice
            return self.llm(**kwargs)
        return {"content": "No LLM configured"}
```

---

## 6. Tool Decision Pipeline

```python
class ToolDecisionPipeline:
    """
    Complete pipeline: Query → Intent → Tool Selection → Execution → Response
    
    Features:
    - Multi-strategy classification
    - Parameter extraction
    - Guardrails integration
    - Fallback handling
    - Performance tracking
    """
    
    def __init__(self, registry: ToolRegistry, llm_func=None):
        self.registry = registry
        self.classifier = IntentClassifier(llm_func)
        self.executor = ToolExecutor(registry)
        self.llm = llm_func
    
    def process(self, query: str, user_id: str = None, 
                context: Dict = None) -> Dict:
        """Full pipeline"""
        
        # Step 1: Classify intent
        intent = self.classifier.classify(query)
        
        selected_tools = intent.get("tools", [])
        
        if not selected_tools:
            return {
                "tool_used": None,
                "result": "No tool needed — direct response",
                "intent": intent,
                "pipeline_steps": ["classify"],
            }
        
        # Step 2: Try each potential tool
        for tool_name in selected_tools:
            tool = self.registry.get_tool(tool_name)
            if not tool:
                continue
            
            # Step 3: Extract parameters
            params = self._extract_params(query, tool, context)
            
            # Step 4: Execute
            result = self.executor.execute(
                tool_name, params, user_id=user_id
            )
            
            if result["success"]:
                return {
                    "tool_used": tool_name,
                    "result": result["result"],
                    "intent": intent,
                    "params": params,
                    "pipeline_steps": ["classify", "select", "extract", "execute"],
                    "elapsed_ms": result.get("elapsed_ms"),
                }
        
        return {
            "tool_used": None,
            "result": "No tool could process this query",
            "intent": intent,
            "pipeline_steps": ["classify", "select", "execute_failed"],
        }
    
    def process_multi(self, query: str) -> Dict:
        """
        Process query that may require multiple tools
        """
        # Detect multi-intent
        intents = self.classifier.classify_multi_intent(query)
        
        results = []
        for intent in intents:
            for tool_name in intent.get("tools", []):
                tool = self.registry.get_tool(tool_name)
                if not tool:
                    continue
                
                params = self._extract_params(query, tool)
                result = self.executor.execute(tool_name, params)
                
                if result["success"]:
                    results.append({
                        "tool": tool_name,
                        "result": result["result"],
                        "params": params,
                    })
                    break  # Move to next intent
        
        return {
            "tools_used": [r["tool"] for r in results],
            "results": results,
            "total_intents": len(intents),
            "total_success": len(results),
        }
    
    def _extract_params(self, query: str, tool: ToolDefinition, 
                        context: Dict = None) -> Dict:
        """Extract tool parameters from query"""
        if self.llm:
            params_desc = json.dumps(tool.parameters, indent=2, ensure_ascii=False)
            context_text = json.dumps(context, indent=2, ensure_ascii=False) if context else "None"
            
            prompt = f"""Trích xuất parameters cho tool từ query.

Query: "{query}"
Context: {context_text}
Tool: {tool.name}
Description: {tool.description}
Parameters schema: {params_desc}

Output JSON (chỉ parameters, đúng types):"""
            
            response = self.llm(prompt)
            try:
                return json.loads(response)
            except json.JSONDecodeError:
                pass
        
        # Fallback: put query as first string param
        first_param = list(tool.parameters.keys())[0] if tool.parameters else None
        if first_param:
            return {first_param: query}
        return {}
```

---

## 7. Tool Composition

```python
class ToolComposer:
    """
    Compose multiple tools into workflows
    
    Patterns:
    - Chain: A → B → C (sequential)
    - Parallel: A + B + C (simultaneous)
    - Conditional: if A > threshold then B else C
    - Pipeline: A → fan-out → B, C, D → fan-in → E
    """
    
    def __init__(self, executor: ToolExecutor):
        self.executor = executor
    
    def chain(self, steps: List[Dict], shared_context: Dict = None) -> Dict:
        """
        Execute tools in chain
        Each tool's output becomes next tool's input
        """
        context = shared_context or {}
        
        for i, step in enumerate(steps):
            tool_name = step["tool"]
            input_mapping = step.get("input_mapping", {})
            
            # Map inputs from context
            params = {}
            for param_name, source_key in input_mapping.items():
                params[param_name] = context.get(source_key, source_key)
            
            # Merge with static params
            params.update(step.get("params", {}))
            
            result = self.executor.execute(tool_name, params)
            
            if not result["success"]:
                return {
                    "success": False,
                    "error": f"Chain broke at step {i+1} ({tool_name}): {result.get('error')}",
                    "partial_results": context,
                }
            
            # Store result in context
            output_key = step.get("output_key", f"step_{i+1}_result")
            context[output_key] = result["result"]
        
        return {"success": True, "results": context}
    
    def parallel(self, tasks: List[Dict]) -> Dict:
        """
        Execute tools in parallel
        All tools run simultaneously, results collected
        """
        results = self.executor.execute_batch(tasks)
        
        return {
            "success": all(r["success"] for r in results),
            "results": results,
            "total": len(results),
            "succeeded": sum(1 for r in results if r["success"]),
            "failed": sum(1 for r in results if not r["success"]),
        }
    
    def conditional(self, condition_tool: str, condition_params: Dict,
                    condition_check: Callable,
                    true_branch: List[Dict], 
                    false_branch: List[Dict] = None) -> Dict:
        """
        Execute tool conditionally
        
        1. Execute condition_tool
        2. Check result with condition_check function
        3. Execute true_branch or false_branch
        """
        # Execute condition
        condition_result = self.executor.execute(condition_tool, condition_params)
        
        if not condition_result["success"]:
            return {"success": False, "error": "Condition tool failed"}
        
        # Evaluate condition
        branch = true_branch if condition_check(condition_result["result"]) else (false_branch or [])
        
        # Execute branch
        if branch:
            return self.chain(branch, {"condition_result": condition_result["result"]})
        
        return {"success": True, "results": {"condition_result": condition_result["result"]}}
    
    def pipeline(self, stages: List[Dict]) -> Dict:
        """
        Fan-out / Fan-in pipeline
        
        Stage format:
        {
            "name": "stage_name",
            "tools": [{"tool": "name", "params": {...}}],
            "aggregator": "merge" | "first" | "all"
        }
        """
        results = {}
        
        for stage in stages:
            stage_name = stage["name"]
            tools = stage["tools"]
            aggregator = stage.get("aggregator", "merge")
            
            # Fan-out: execute all tools
            stage_results = self.executor.execute_batch(tools)
            
            # Fan-in: aggregate results
            if aggregator == "merge":
                results[stage_name] = [
                    r["result"] for r in stage_results if r["success"]
                ]
            elif aggregator == "first":
                for r in stage_results:
                    if r["success"]:
                        results[stage_name] = r["result"]
                        break
            elif aggregator == "all":
                results[stage_name] = all(r["success"] for r in stage_results)
        
        return {"success": True, "results": results}
```

---

## 8. Permission System

```python
class ToolPermissionChecker:
    """
    Role-Based Access Control (RBAC) for tools
    
    Permission levels:
    - public: Anyone can use
    - standard: Authenticated users
    - elevated: Users with elevated privileges
    - admin: System admin only
    """
    
    ROLE_PERMISSIONS = {
        "guest": ["public"],
        "user": ["public", "standard"],
        "power_user": ["public", "standard", "elevated"],
        "admin": ["public", "standard", "elevated", "admin"],
    }
    
    def __init__(self):
        self.user_roles: Dict[str, str] = {}
        self.custom_permissions: Dict[str, List[str]] = {}
        self.audit_log: List[Dict] = []
    
    def set_user_role(self, user_id: str, role: str):
        """Set role for a user"""
        if role not in self.ROLE_PERMISSIONS:
            raise ValueError(f"Invalid role: {role}. Valid: {list(self.ROLE_PERMISSIONS.keys())}")
        self.user_roles[user_id] = role
    
    def grant_permission(self, user_id: str, permission: str):
        """Grant additional permission to user"""
        if user_id not in self.custom_permissions:
            self.custom_permissions[user_id] = []
        if permission not in self.custom_permissions[user_id]:
            self.custom_permissions[user_id].append(permission)
    
    def check(self, user_id: str, required_permission: str) -> Dict:
        """Check if user has required permission"""
        role = self.user_roles.get(user_id, "guest")
        role_perms = self.ROLE_PERMISSIONS.get(role, ["public"])
        custom_perms = self.custom_permissions.get(user_id, [])
        
        all_perms = set(role_perms + custom_perms)
        allowed = required_permission in all_perms
        
        # Log audit trail
        self.audit_log.append({
            "user_id": user_id,
            "required_permission": required_permission,
            "user_role": role,
            "allowed": allowed,
            "timestamp": datetime.now().isoformat(),
        })
        
        if allowed:
            return {"allowed": True}
        else:
            return {
                "allowed": False,
                "reason": f"Role '{role}' does not have '{required_permission}' permission",
                "required": required_permission,
                "current_permissions": list(all_perms),
            }
```

---

## 9. Rate Limiting

```python
class RateLimiter:
    """
    Rate limiting for tool calls
    
    Strategies:
    - Fixed window: X calls per minute
    - Sliding window: X calls in last 60 seconds
    - Token bucket: Burst + sustained rate
    """
    
    def __init__(self, strategy: str = "sliding_window"):
        self.strategy = strategy
        self._call_records: Dict[str, List[float]] = {}
        self._burst_limits: Dict[str, int] = {}
        self._token_buckets: Dict[str, Dict] = {}
    
    def set_limit(self, tool_name: str, max_calls: int, window_seconds: int = 60):
        """Set rate limit for a tool"""
        self._burst_limits[tool_name] = max_calls
    
    def check(self, tool_name: str) -> Dict:
        """Check if call is allowed"""
        now = time.time()
        
        if tool_name not in self._call_records:
            self._call_records[tool_name] = []
        
        records = self._call_records[tool_name]
        limit = self._burst_limits.get(tool_name, 60)
        window = 60  # 1 minute
        
        if self.strategy == "sliding_window":
            # Remove old records
            records = [t for t in records if now - t < window]
            self._call_records[tool_name] = records
            
            if len(records) >= limit:
                oldest = records[0]
                retry_after = window - (now - oldest)
                return {
                    "allowed": False,
                    "retry_after": round(retry_after, 1),
                    "current_rate": len(records),
                    "limit": limit,
                }
            
            records.append(now)
            return {
                "allowed": True,
                "current_rate": len(records),
                "limit": limit,
                "remaining": limit - len(records),
            }
        
        return {"allowed": True}
    
    def get_usage(self) -> Dict:
        """Get current usage for all tools"""
        usage = {}
        for tool_name, records in self._call_records.items():
            now = time.time()
            recent = [t for t in records if now - t < 60]
            usage[tool_name] = {
                "calls_last_minute": len(recent),
                "limit": self._burst_limits.get(tool_name, 60),
            }
        return usage
```

---

## 10. Harness Integration

### 10.1 TypeScript Interfaces

```typescript
// Tool Decision System — Full Harness Integration
interface ToolDecisionSystem {
  // Tool management
  registerTool: (tool: ToolDefinition) => void;
  getTool: (name: string) => ToolDefinition | null;
  listTools: (category?: string) => ToolDefinition[];
  searchTools: (query: string) => ToolDefinition[];
  
  // Classification
  classifyIntent: (query: string) => IntentResult;
  
  // Execution
  executeTool: (name: string, params: Record<string, any>, userId?: string) => ToolResult;
  executeChain: (steps: ToolChainStep[]) => ChainResult;
  executeParallel: (calls: ToolCall[]) => ParallelResult;
  
  // Guardrails
  checkPermission: (userId: string, permission: string) => boolean;
  checkRateLimit: (toolName: string) => boolean;
  
  // Metrics
  getStats: () => ToolStats;
}

interface ToolDefinition {
  name: string;
  description: string;
  parameters: Record<string, ParameterDef>;
  category: string;
  version: string;
  requiresAuth: boolean;
  requiresPermission: string;
  rateLimitPerMinute: number;
  timeoutSeconds: number;
  maxRetries: number;
  costPerCall: number;
}

interface ToolResult {
  success: boolean;
  result?: any;
  error?: string;
  attempts: number;
  elapsedMs: number;
  cost: number;
}

interface IntentResult {
  intent: string;
  tools: string[];
  confidence: number;
  method: string;
}

// Complete Harness Tool Decision System
class HarnessToolDecisionSystem implements ToolDecisionSystem {
  private registry: ToolRegistry;
  private classifier: IntentClassifier;
  private executor: ToolExecutor;
  private permissionChecker: ToolPermissionChecker;
  private rateLimiter: RateLimiter;
  private metricsCollector: MetricsCollector;
  
  constructor(config: HarnessConfig) {
    this.registry = new ToolRegistry();
    this.classifier = new IntentClassifier(config.llm);
    this.executor = new ToolExecutor(this.registry);
    this.permissionChecker = new ToolPermissionChecker();
    this.rateLimiter = new RateLimiter();
    this.metricsCollector = config.metrics;
  }
  
  async executeTool(name: string, params: Record<string, any>, userId?: string): Promise<ToolResult> {
    // 1. Check permission
    const tool = this.registry.getTool(name);
    if (tool && userId) {
      const permCheck = this.permissionChecker.check(userId, tool.requiresPermission);
      if (!permCheck.allowed) {
        return { success: false, error: permCheck.reason, attempts: 0, elapsedMs: 0, cost: 0 };
      }
    }
    
    // 2. Check rate limit
    const rateCheck = this.rateLimiter.check(name);
    if (!rateCheck.allowed) {
      return { success: false, error: `Rate limited. Retry after ${rateCheck.retryAfter}s`, attempts: 0, elapsedMs: 0, cost: 0 };
    }
    
    // 3. Execute with tracking
    const result = this.executor.execute(name, params, userId);
    
    // 4. Report metrics
    this.metricsCollector.track('tool_executed', {
      tool: name,
      success: result.success,
      latency_ms: result.elapsed_ms,
    });
    
    return result;
  }
  
  // ... other interface implementations
}
```

---

## 11. Case Studies

### 11.1. SWE-agent — Tool-Use for Software Engineering

**Tool strategy**: Simple, focused tools with clear descriptions.

```
Tools: read_file, search_code, edit_file, run_command

Key insight: "Read before edit" — SWE-agent always reads files first
to understand context before making changes.
```

**Result**: 20.5% success rate on SWE-bench (+64% over baseline)

### 11.2. Claude Code — Hierarchical Tool Access

**Tool strategy**: Layered permissions, tool composition.

```
Layer 1 (always available): read_file, search_code
Layer 2 (standard): write_file, run_tests
Layer 3 (elevated): execute_command, delete_file
Layer 4 (admin): push_code, deploy
```

### 11.3. Cursor IDE — Context-Aware Tool Selection

**Tool strategy**: Select tools based on editor context.

```typescript
// Cursor dynamically selects tools based on:
// 1. Current file type
// 2. Selected code
// 3. Editor mode (insert vs replace)
// 4. Git status

const toolsForContext = {
  python: ["execute_python", "lint", "format"],
  typescript: ["run_tests", "type_check", "lint"],
  markdown: ["spell_check", "format"],
};
```

---

## 12. Design Principles

### 12.1 SOLID Cho Tools

**1. Single Responsibility**
- Mỗi tool = 1 việc rõ ràng
- Không combine search + write trong 1 tool

**2. Open/Closed**
- Mở cho thêm tools mới (register)
- Đóng cho sửa đổi existing tools

**3. Liskov Substitution**
- Tất cả tools implement cùng interface
- Có thể swap tool này bằng tool khác

**4. Interface Segregation**
- Tool definitions tách biệt với execution
- Permission checker tách biệt với executor

**5. Dependency Inversion**
- Tools phụ thuộc vào ToolRegistry abstraction
- Không hardcode tool implementations

---

## 13. Best Practices

### 13.1 DO ✅

- **Validate parameters before execution**: Prevent errors
- **Use exponential backoff for retries**: Don't hammer failing services
- **Log all tool calls**: For debugging and optimization
- **Set timeouts**: Prevent hanging calls
- **Implement permission checks**: Security first
- **Track cost per call**: Budget management
- **Provide clear tool descriptions**: LLM needs to understand tools
- **Support both rule-based and LLM-based intent**: Best of both worlds
- **Cache tool lists**: Don't re-fetch on every call
- **Use fallback tools**: If primary fails, try alternative

### 13.2 DON'T ❌

- **Đừng skip parameter validation**: Invalid params = crashes
- **Đừng ignore rate limits**: API bans are real
- **Đừng hardcode tool paths**: Use registry
- **Đừng forget error handling**: Every tool call can fail
- **Đừng use vague tool descriptions**: LLM can't guess
- **Đừng allow unlimited retries**: Set max retries
- **Đừng execute dangerous tools without permission**: Always check auth
- **Đừng ignore deprecated tools**: Gracefully migrate users

---

## 14. Testing

```python
import unittest

class TestToolRegistry(unittest.TestCase):
    def setUp(self):
        self.registry = create_default_tools()
    
    def test_register_and_get(self):
        tool = self.registry.get_tool("calculator")
        self.assertIsNotNone(tool)
        self.assertEqual(tool.name, "calculator")
    
    def test_search(self):
        results = self.registry.search("tìm")
        self.assertGreater(len(results), 0)
    
    def test_category_filter(self):
        tools = self.registry.list_tools(category="search")
        for tool in tools:
            self.assertEqual(tool.category, "search")
    
    def test_deprecation(self):
        tool = self.registry.get_tool("calculator")
        self.assertFalse(tool.is_deprecated())

class TestIntentClassifier(unittest.TestCase):
    def setUp(self):
        self.classifier = IntentClassifier()
    
    def test_search_intent(self):
        result = self.classifier.classify("Tìm thông tin về BHYT")
        self.assertEqual(result["intent"], "search")
        self.assertGreater(len(result["tools"]), 0)
    
    def test_calculate_intent(self):
        result = self.classifier.classify("Tính 15% của 5000000")
        self.assertEqual(result["intent"], "calculate")
    
    def test_chat_fallback(self):
        result = self.classifier.classify("Xin chào!")
        self.assertEqual(result["intent"], "chat")
    
    def test_multi_intent(self):
        intents = self.classifier.classify_multi_intent("Đọc file và chạy test")
        self.assertGreaterEqual(len(intents), 2)

class TestToolExecutor(unittest.TestCase):
    def setUp(self):
        self.registry = create_default_tools()
        self.executor = ToolExecutor(self.registry)
    
    def test_successful_execution(self):
        result = self.executor.execute("calculator", {"expression": "2+3"})
        self.assertTrue(result["success"])
    
    def test_missing_tool(self):
        result = self.executor.execute("nonexistent", {})
        self.assertFalse(result["success"])
        self.assertIn("not found", result["error"])
    
    def test_missing_required_param(self):
        result = self.executor.execute("vector_search", {})
        self.assertFalse(result["success"])
        self.assertIn("Invalid parameters", result["error"])
    
    def test_stats(self):
        self.executor.execute("calculator", {"expression": "1+1"})
        self.executor.execute("calculator", {"expression": "2+2"})
        stats = self.executor.get_stats()
        self.assertEqual(stats["total_calls"], 2)
        self.assertEqual(stats["successful"], 2)

class TestPermissionSystem(unittest.TestCase):
    def setUp(self):
        self.checker = ToolPermissionChecker()
        self.checker.set_user_role("user1", "user")
        self.checker.set_user_role("admin1", "admin")
    
    def test_standard_permission(self):
        result = self.checker.check("user1", "standard")
        self.assertTrue(result["allowed"])
    
    def test_elevated_denied(self):
        result = self.checker.check("user1", "elevated")
        self.assertFalse(result["allowed"])
    
    def test_admin_has_all(self):
        result = self.checker.check("admin1", "admin")
        self.assertTrue(result["allowed"])
    
    def test_custom_grant(self):
        self.checker.grant_permission("user1", "elevated")
        result = self.checker.check("user1", "elevated")
        self.assertTrue(result["allowed"])

class TestRateLimiter(unittest.TestCase):
    def setUp(self):
        self.limiter = RateLimiter(strategy="sliding_window")
        self.limiter.set_limit("api_call", max_calls=3, window_seconds=60)
    
    def test_within_limit(self):
        for _ in range(3):
            result = self.limiter.check("api_call")
            self.assertTrue(result["allowed"])
    
    def test_exceeds_limit(self):
        for _ in range(3):
            self.limiter.check("api_call")
        result = self.limiter.check("api_call")
        self.assertFalse(result["allowed"])
        self.assertIn("retry_after", result)

class TestToolComposer(unittest.TestCase):
    def setUp(self):
        self.registry = create_default_tools()
        self.executor = ToolExecutor(self.registry)
        self.composer = ToolComposer(self.executor)
    
    def test_chain(self):
        steps = [
            {"tool": "calculator", "params": {"expression": "2+3"}, "output_key": "result"},
        ]
        result = self.composer.chain(steps)
        self.assertTrue(result["success"])

if __name__ == "__main__":
    unittest.main()
```

---

## 15. Advanced Patterns

### 15.1 Tool Learning

```python
class ToolLearner:
    """
    Agent learns which tools work best for which tasks
    
    Based on execution history, learns:
    - Tool preference per task type
    - Parameter patterns
    - Failure modes
    """
    
    def __init__(self):
        self.history: List[Dict] = []
        self.tool_scores: Dict[str, Dict] = {}
    
    def record(self, task_type: str, tool_name: str, 
               params: Dict, success: bool, latency_ms: float):
        """Record a tool usage"""
        self.history.append({
            "task_type": task_type,
            "tool": tool_name,
            "params": params,
            "success": success,
            "latency_ms": latency_ms,
            "timestamp": datetime.now().isoformat(),
        })
        
        # Update scores
        key = f"{task_type}:{tool_name}"
        if key not in self.tool_scores:
            self.tool_scores[key] = {"success_count": 0, "total_count": 0, "avg_latency": 0}
        
        score = self.tool_scores[key]
        score["total_count"] += 1
        if success:
            score["success_count"] += 1
        score["avg_latency"] = (
            0.1 * latency_ms + 0.9 * score["avg_latency"]
        )
    
    def recommend(self, task_type: str, available_tools: List[str]) -> List[str]:
        """Recommend tools for a task type based on history"""
        scored = []
        
        for tool in available_tools:
            key = f"{task_type}:{tool}"
            score = self.tool_scores.get(key, {"success_count": 0, "total_count": 0})
            
            if score["total_count"] > 0:
                success_rate = score["success_count"] / score["total_count"]
                # Penalize high latency
                latency_penalty = min(score["avg_latency"] / 1000, 1)
                final_score = success_rate * (1 - latency_penalty)
            else:
                final_score = 0.5  # Unknown = medium score
            
            scored.append((final_score, tool))
        
        scored.sort(reverse=True)
        return [tool for _, tool in scored]
```

---

## 16. Tương Lai

### 16.1 Xu Hướng 2026-2028

**1. Auto Tool Discovery**
- Agent tự discover tools từ MCP servers
- Tự tạo wrapper cho unknown tools
- Semantic tool matching

**2. Tool Composition AI**
- Agent tự compose tools thành workflows
- Learned tool chains from execution history
- Self-optimizing pipelines

**3. Cross-Platform Tools**
- Tool portability across agents
- Standardized tool marketplace
- Universal tool interface

**4. Predictive Tool Use**
- Predict which tools will be needed
- Pre-warm connections
- Pre-fetch data

**5. Tool Security**
- Zero-trust tool execution
- Sandboxed tool environments
- Audit trail for all operations

---

## Tài Liệu Tham Khảo

### Papers & Research

1. **Toolformer: Language Models Can Teach Themselves to Use Tools**
   - Schick et al., 2023
   - https://arxiv.org/abs/2302.04761

2. **Gorilla: Large Language Model Connected with Massive APIs**
   - Patil et al., 2023
   - https://arxiv.org/abs/2305.15334

3. **API-Bank: A Comprehensive Benchmark for Tool-Augmented LLMs**
   - Li et al., 2023
   - https://arxiv.org/abs/2304.08354

4. **NexusRaven: Function Calling for LLMs**
   - Nexusflow, 2023
   - https://arxiv.org/abs/2312.15933

### Frameworks & Tools

1. **MCP (Model Context Protocol)** - https://modelcontextprotocol.io
2. **OpenAI Function Calling** - https://platform.openai.com/docs
3. **Anthropic Tool Use** - https://docs.anthropic.com
4. **LangChain Tools** - https://langchain.com
5. **Semantic Kernel** - https://learn.microsoft.com/semantic-kernel

---

*Tài liệu: VI. Decide Tools / MCP Calls — HARNESS ENGINEERING EDITION*
*Ngày cập nhật: 19/07/2026*
*Tác giả: AI Knowledge Repository*