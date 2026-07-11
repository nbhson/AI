 (n# 🔧 VI. Decide Tools / MCP Calls

## Tổng Quan

AI Agent cần biết **khi nào dùng tool nào** và **gọi tool đó như thế nào**. Đây là quá trình quyết định: Phân tích task → Chọn tool → Gọi API → Xử lý kết quả.

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
│  │  TOOL          │  Gọi tool (function call / MCP)             │
│  │  EXECUTOR      │  Handle errors, timeout, retry              │
│  └───────┬────────┘                                             │
│          ▼                                                       │
│  ┌────────────────┐                                             │
│  │  RESULT        │  Parse kết quả → Format → Trả lời          │
│  │  PROCESSOR     │                                             │
│  └────────────────┘                                             │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 1. Tool Selection Patterns

### 1.1 Tool Registry

```python
from typing import Any, Callable, Dict, List, Optional
from dataclasses import dataclass, field
import json

@dataclass
class ToolDefinition:
    name: str
    description: str
    parameters: Dict[str, Dict]  # {param_name: {"type": ..., "description": ...}}
    function: Callable = None
    category: str = "general"
    examples: List[Dict] = field(default_factory=list)

class ToolRegistry:
    """
    Central registry for all available tools
    
    Supports: Registration, search, category filtering
    """
    
    def __init__(self):
        self.tools: Dict[str, ToolDefinition] = {}
        self.categories: Dict[str, List[str]] = {}
    
    def register(self, tool: ToolDefinition):
        """Register a new tool"""
        self.tools[tool.name] = tool
        if tool.category not in self.categories:
            self.categories[tool.category] = []
        self.categories[tool.category].append(tool.name)
    
    def get_tool(self, name: str) -> Optional[ToolDefinition]:
        return self.tools.get(name)
    
    def list_tools(self, category=None) -> List[ToolDefinition]:
        if category:
            names = self.categories.get(category, [])
            return [self.tools[n] for n in names if n in self.tools]
        return list(self.tools.values())
    
    def search(self, query: str) -> List[ToolDefinition]:
        """Search tools by keyword"""
        query_lower = query.lower()
        results = []
        for tool in self.tools.values():
            if (query_lower in tool.name.lower() or 
                query_lower in tool.description.lower()):
                results.append(tool)
        return results
    
    def to_openai_format(self) -> List[Dict]:
        """Export tools in OpenAI function calling format"""
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
            for t in self.tools.values()
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
```

### 1.2 Example Tools

```python
def create_default_tools():
    """Create a set of default tools"""
    registry = ToolRegistry()
    
    # ─── Search Tools ───
    registry.register(ToolDefinition(
        name="vector_search",
        description="Tìm kiếm ngữ nghĩa trong cơ sở dữ liệu",
        parameters={
            "query": {"type": "string", "description": "Câu truy vấn", "required": True},
            "top_k": {"type": "integer", "description": "Số kết quả trả về", "default": 5},
            "collection": {"type": "string", "description": "Tên collection"},
        },
        category="search",
    ))
    
    registry.register(ToolDefinition(
        name="web_search",
        description="Tìm kiếm trên internet",
        parameters={
            "query": {"type": "string", "description": "Từ khóa tìm kiếm", "required": True},
            "num_results": {"type": "integer", "default": 5},
        },
        category="search",
    ))
    
    # ─── Calculation Tools ───
    registry.register(ToolDefinition(
        name="calculator",
        description="Tính toán toán học",
        parameters={
            "expression": {"type": "string", "description": "Biểu thức toán học", "required": True},
        },
        category="computation",
    ))
    
    # ─── Code Tools ───
    registry.register(ToolDefinition(
        name="execute_python",
        description="Thực thi code Python",
        parameters={
            "code": {"type": "string", "description": "Python code", "required": True},
            "timeout": {"type": "integer", "default": 30},
        },
        category="code",
    ))
    
    # ─── File Tools ───
    registry.register(ToolDefinition(
        name="read_file",
        description="Đọc nội dung file",
        parameters={
            "path": {"type": "string", "description": "Đường dẫn file", "required": True},
        },
        category="file",
    ))
    
    registry.register(ToolDefinition(
        name="write_file",
        description="Ghi nội dung vào file",
        parameters={
            "path": {"type": "string", "description": "Đường dẫn file", "required": True},
            "content": {"type": "string", "description": "Nội dung", "required": True},
        },
        category="file",
    ))
    
    # ─── Knowledge Graph Tools ───
    registry.register(ToolDefinition(
        name="query_knowledge_graph",
        description="Truy vấn knowledge graph",
        parameters={
            "entity": {"type": "string", "description": "Tên entity", "required": True},
            "max_hops": {"type": "integer", "default": 1},
        },
        category="knowledge",
    ))
    
    # ─── Memory Tools ───
    registry.register(ToolDefinition(
        name="recall_memory",
        description="Nhớ lại thông tin từ memory",
        parameters={
            "query": {"type": "string", "description": "Câu truy vấn", "required": True},
            "memory_type": {"type": "string", "enum": ["episodic", "semantic", "all"]},
        },
        category="memory",
    ))
    
    registry.register(ToolDefinition(
        name="store_memory",
        description="Lưu thông tin vào memory",
        parameters={
            "content": {"type": "string", "description": "Nội dung cần lưu", "required": True},
            "memory_type": {"type": "string", "enum": ["episodic", "semantic"]},
            "importance": {"type": "string", "enum": ["low", "medium", "high"]},
        },
        category="memory",
    ))
    
    return registry
```

---

## 2. Intent Classification

```python
class IntentClassifier:
    """
    Classify user intent to determine which tool category to use
    """
    
    INTENT_RULES = {
        "search": {
            "keywords": ["tìm", "search", "kiếm", "tra cứu", "google"],
            "tools": ["vector_search", "web_search", "query_knowledge_graph"],
        },
        "calculate": {
            "keywords": ["tính", "calculate", "math", "bao nhiêu", "phần trăm"],
            "tools": ["calculator", "execute_python"],
        },
        "code": {
            "keywords": ["code", "viết code", "program", "function", "script"],
            "tools": ["execute_python", "write_file"],
        },
        "read": {
            "keywords": ["đọc", "read", "xem", "open", "file"],
            "tools": ["read_file"],
        },
        "write": {
            "keywords": ["ghi", "write", "lưu", "save", "tạo file"],
            "tools": ["write_file", "store_memory"],
        },
        "remember": {
            "keywords": ["nhớ", "remember", "lưu ý", "ghi nhớ"],
            "tools": ["store_memory"],
        },
        "recall": {
            "keywords": ["nhắc lại", "recall", "trước đó", "đã nói"],
            "tools": ["recall_memory"],
        },
        "knowledge": {
            "keywords": ["biết gì", "knowledge", "facts", "thông tin về"],
            "tools": ["query_knowledge_graph", "vector_search"],
        },
    }
    
    def classify(self, query):
        """Classify intent from query"""
        query_lower = query.lower()
        
        scores = {}
        for intent, config in self.INTENT_RULES.items():
            score = sum(1 for kw in config["keywords"] if kw in query_lower)
            if score > 0:
                scores[intent] = score
        
        if not scores:
            return {"intent": "chat", "tools": [], "confidence": 0.5}
        
        best_intent = max(scores, key=scores.get)
        confidence = min(scores[best_intent] / 3, 1.0)
        
        return {
            "intent": best_intent,
            "tools": self.INTENT_RULES[best_intent]["tools"],
            "confidence": confidence,
        }
    
    def classify_with_llm(self, query, llm_func):
        """LLM-based classification (more accurate)"""
        tools_desc = "\n".join([
            f"- {intent}: {config['tools']}"
            for intent, config in self.INTENT_RULES.items()
        ])
        
        prompt = f"""Phân tích ý định của user và chọn tool phù hợp.

User query: "{query}"

Các intent có thể:
{tools_desc}

Output JSON:
{{"intent": "...", "tools": ["tool1", "tool2"], "confidence": 0.9, "reasoning": "..."}}"""
        
        response = llm_func(prompt)
        try:
            return json.loads(response)
        except json.JSONDecodeError:
            return self.classify(query)  # Fallback
```

---

## 3. MCP (Model Context Protocol)

### 3.1 MCP Overview

```
┌──────────────────────────────────────────────────────────────────┐
│                  MCP (MODEL CONTEXT PROTOCOL)                     │
│                                                                  │
│  MCP là giao thức chuẩn để LLM giao tiếp với tools bên ngoài  │
│                                                                  │
│  ┌──────────┐    MCP Protocol    ┌──────────────────┐          │
│  │          │◄──────────────────►│  MCP Server       │          │
│  │   LLM   │                    │  ──────────────── │          │
│  │  Client  │    Tools:          │  - GitHub API     │          │
│  │          │    - search_code   │  - Database       │          │
│  │          │    - create_issue  │  - File System    │          │
│  │          │    - read_file     │  - Web Search     │          │
│  │          │    - ...           │  - Custom API     │          │
│  └──────────┘                    └──────────────────┘          │
│                                                                  │
│  Architecture:                                                   │
│  ├── Client (Host): IDE, CLI, App                              │
│  ├── Server (Tool Provider): Runs tools locally/remotely       │
│  └── Protocol: JSON-RPC over stdio/SSE/HTTP                   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 3.2 MCP Tool Execution

```python
class MCPClient:
    """
    MCP Client for calling MCP tools
    
    Supports: Tool listing, tool calling, resource access
    """
    
    def __init__(self, server_name, transport="stdio"):
        self.server_name = server_name
        self.transport = transport
        self._tools_cache = []
        self._connected = False
    
    async def connect(self):
        """Connect to MCP server"""
        # In real implementation, this would start the server process
        # and establish JSON-RPC communication
        self._connected = True
        print(f"Connected to MCP server: {self.server_name}")
    
    async def list_tools(self):
        """List available tools from MCP server"""
        if not self._connected:
            await self.connect()
        
        # Simulate MCP tool listing
        # Real: send JSON-RPC request "tools/list"
        return self._tools_cache
    
    async def call_tool(self, tool_name, arguments):
        """Call a tool on MCP server"""
        if not self._connected:
            await self.connect()
        
        # Simulate MCP tool call
        # Real: send JSON-RPC request "tools/call"
        request = {
            "jsonrpc": "2.0",
            "id": 1,
            "method": "tools/call",
            "params": {
                "name": tool_name,
                "arguments": arguments,
            }
        }
        
        # In production: send via subprocess or HTTP
        # response = await self._send_request(request)
        
        return {
            "content": [
                {"type": "text", "text": f"Result from {tool_name}"}
            ]
        }
    
    async def read_resource(self, uri):
        """Read a resource from MCP server"""
        request = {
            "jsonrpc": "2.0",
            "id": 2,
            "method": "resources/read",
            "params": {"uri": uri},
        }
        return {"contents": []}
    
    def format_tools_for_llm(self):
        """Format MCP tools for LLM function calling"""
        return [
            {
                "type": "function",
                "function": {
                    "name": t["name"],
                    "description": t["description"],
                    "parameters": t.get("inputSchema", {}),
                }
            }
            for t in self._tools_cache
        ]
```

---

## 4. Tool Executor with Error Handling

```python
import time
from typing import Any, Dict, Optional

class ToolExecutor:
    """
    Execute tools with error handling, retries, and logging
    """
    
    def __init__(self, registry: ToolRegistry, max_retries=3, timeout=30):
        self.registry = registry
        self.max_retries = max_retries
        self.timeout = timeout
        self.execution_log = []
    
    def execute(self, tool_name: str, parameters: Dict) -> Dict:
        """
        Execute a tool with retry logic
        """
        tool = self.registry.get_tool(tool_name)
        if not tool:
            return {
                "success": False,
                "error": f"Tool '{tool_name}' not found",
                "result": None,
            }
        
        # Validate parameters
        validation = self._validate_params(tool, parameters)
        if not validation["valid"]:
            return {
                "success": False,
                "error": f"Invalid parameters: {validation['errors']}",
                "result": None,
            }
        
        # Execute with retries
        last_error = None
        for attempt in range(self.max_retries):
            try:
                start_time = time.time()
                
                # Execute
                if tool.function:
                    result = tool.function(**parameters)
                else:
                    result = f"Tool '{tool_name}' has no function implementation"
                
                elapsed = time.time() - start_time
                
                # Log success
                log_entry = {
                    "tool": tool_name,
                    "params": parameters,
                    "success": True,
                    "attempt": attempt + 1,
                    "elapsed": round(elapsed, 3),
                }
                self.execution_log.append(log_entry)
                
                return {
                    "success": True,
                    "result": result,
                    "attempts": attempt + 1,
                    "elapsed": elapsed,
                }
                
            except Exception as e:
                last_error = str(e)
                if attempt < self.max_retries - 1:
                    time.sleep(0.5 * (attempt + 1))  # Exponential backoff
        
        # All retries failed
        self.execution_log.append({
            "tool": tool_name,
            "params": parameters,
            "success": False,
            "error": last_error,
            "attempts": self.max_retries,
        })
        
        return {
            "success": False,
            "error": f"Failed after {self.max_retries} attempts: {last_error}",
            "result": None,
        }
    
    def execute_batch(self, calls: list) -> list:
        """
        Execute multiple tool calls (for parallel execution)
        
        calls: [{"tool": "name", "params": {...}}, ...]
        """
        results = []
        for call in calls:
            result = self.execute(call["tool"], call.get("params", {}))
            results.append({
                "tool": call["tool"],
                **result,
            })
        return results
    
    def _validate_params(self, tool: ToolDefinition, params: Dict) -> Dict:
        """Validate parameters against tool definition"""
        errors = []
        
        for param_name, param_def in tool.parameters.items():
            if param_def.get("required", False) and param_name not in params:
                errors.append(f"Missing required param: {param_name}")
        
        return {"valid": len(errors) == 0, "errors": errors}
    
    def get_stats(self) -> Dict:
        """Get execution statistics"""
        total = len(self.execution_log)
        successful = sum(1 for log in self.execution_log if log["success"])
        
        tool_counts = {}
        for log in self.execution_log:
            tool = log["tool"]
            tool_counts[tool] = tool_counts.get(tool, 0) + 1
        
        return {
            "total_calls": total,
            "successful": successful,
            "failed": total - successful,
            "success_rate": successful / total if total > 0 else 0,
            "tool_usage": tool_counts,
        }
```

---

## 5. Function Calling (OpenAI Style)

```python
class FunctionCallingAgent:
    """
    Agent using OpenAI-style function calling
    
    Supports: Multi-turn tool calls, parallel calls, forced tool use
    """
    
    def __init__(self, llm_func, tool_registry):
        self.llm = llm_func
        self.registry = tool_registry
        self.executor = ToolExecutor(tool_registry)
    
    def run(self, user_message, tools=None, max_rounds=10):
        """
        Execute agent loop with function calling
        """
        messages = [{"role": "user", "content": user_message}]
        tools = tools or self.registry.to_openai_format()
        
        for round_num in range(max_rounds):
            # Call LLM
            response = self._call_llm(messages, tools)
            
            # Check for tool calls
            if not response.get("tool_calls"):
                return {
                    "answer": response.get("content", ""),
                    "rounds": round_num + 1,
                    "tool_calls_made": sum(
                        len(m.get("tool_calls", [])) 
                        for m in messages if m["role"] == "assistant"
                    ),
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
                func_args = json.loads(tc["function"]["arguments"])
                
                result = self.executor.execute(func_name, func_args)
                
                messages.append({
                    "role": "tool",
                    "tool_call_id": tc["id"],
                    "content": json.dumps(result, ensure_ascii=False),
                })
        
        return {"answer": "Max rounds reached", "rounds": max_rounds}
    
    def _call_llm(self, messages, tools):
        """Call LLM with function calling support"""
        if self.llm:
            return self.llm(messages=messages, tools=tools)
        return {"content": "No LLM configured"}
    
    def forced_tool_call(self, user_message, tool_name):
        """
        Force LLM to use a specific tool
        Useful when intent is clear
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
            func_args = json.loads(tc["function"]["arguments"])
            return self.executor.execute(tool_name, func_args)
        
        return {"error": "LLM did not call the forced tool"}
```

---

## 6. Complete Tool Decision Pipeline

```python
class ToolDecisionPipeline:
    """
    Complete pipeline: Query → Intent → Tool Selection → Execution → Response
    """
    
    def __init__(self, registry, llm_func=None):
        self.registry = registry
        self.classifier = IntentClassifier()
        self.executor = ToolExecutor(registry)
        self.llm = llm_func
    
    def process(self, query):
        """Full pipeline"""
        # Step 1: Classify intent
        intent = self.classifier.classify(query)
        
        # Step 2: Select tool
        selected_tools = intent["tools"]
        
        if not selected_tools:
            return {
                "tool_used": None,
                "result": "No tool needed, chat directly",
                "intent": intent,
            }
        
        # Step 3: For each potential tool, try to extract params and execute
        for tool_name in selected_tools:
            tool = self.registry.get_tool(tool_name)
            if not tool:
                continue
            
            # Extract parameters using LLM
            params = self._extract_params(query, tool)
            
            # Execute
            result = self.executor.execute(tool_name, params)
            
            if result["success"]:
                return {
                    "tool_used": tool_name,
                    "result": result["result"],
                    "intent": intent,
                    "params": params,
                }
        
        return {
            "tool_used": None,
            "result": "No tool could process this query",
            "intent": intent,
        }
    
    def _extract_params(self, query, tool):
        """Extract tool parameters from query"""
        if self.llm:
            params_desc = json.dumps(tool.parameters, indent=2)
            
            prompt = f"""Trích xuất parameters cho tool từ query.

Query: "{query}"
Tool: {tool.name}
Description: {tool.description}
Parameters: {params_desc}

Output JSON (chỉ parameters):"""
            
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

## 7. Labs Thực Hành

### Lab 1: Tool Registration & Calling

```python
# python 06-decide-tools-mcp/lab_tools.py

registry = create_default_tools()

# List all tools
for tool in registry.list_tools():
    print(f"  {tool.name}: {tool.description}")

# Search
results = registry.search("tìm")
print(f"\nSearch 'tìm': {[t.name for t in results]}")

# Classify intent
classifier = IntentClassifier()
intents = [
    "Tìm thông tin về BHYT",
    "Tính 15% của 5000000",
    "Đọc file config.json",
    "Nhớ rằng tôi thích màu xanh",
]
for q in intents:
    intent = classifier.classify(q)
    print(f"\n'{q}' → {intent['intent']} (tools: {intent['tools']})")
```

### Lab 2: Tool Execution

```python
# python 06-decide-tools-mcp/lab_executor.py

registry = create_default_tools()
executor = ToolExecutor(registry)

# Execute calculator
result = executor.execute("calculator", {"expression": "2 + 3 * 4"})
print(f"Calculator: {result}")

# Execute with error
result = executor.execute("nonexistent_tool", {})
print(f"Missing tool: {result}")

# Stats
print(f"\nStats: {executor.get_stats()}")
```

---

*Tài liệu: VI. Decide Tools / MCP Calls*
*Ngày tạo: 2026-07-11*