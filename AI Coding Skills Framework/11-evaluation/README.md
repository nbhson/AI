# 📊 XI. Evaluation

### Tại Sao Evaluation Quan Trọng?

> *"Nếu bạn không đo lường được, bạn không thể cải thiện được. Và nếu bạn không cải thiện được, bạn đang tụt hậu."*

#### Bằng chứng nghiên cứu:

1. **SWE-bench (Princeton, 2025)**: Benchmark standardized cho AI coding — top models resolve **44% real GitHub issues**, nhưng **chỉ khi được evaluate đúng cách** mới biết model nào thực sự tốt cho use case cụ thể.
2. **Google Research (2024)**: Teams với systematic evaluation pipelines phát hiện **3× more regressions** trước khi reaching production.
3. **Microsoft (2025)**: AI code generation without evaluation tăng **28% technical debt** trong 6 tháng.

#### Triết lý cốt lõi:

```
Evaluation = What You Measure → What You Improve → What You Ship
```

**5 Levels của Evaluation**:
- **Level 1**: Code works (functional correctness)
- **Level 2**: Code is correct (edge cases, error handling)
- **Level 3**: Code is good (readability, maintainability)
- **Level 4**: Code is safe (security, performance)
- **Level 5**: Code adds value (business impact, user satisfaction)

**Analogies**: Evaluation giống hệ thống kiểm soát chất lượng trong nhà máy — không chỉ kiểm tra sản phẩm có hỏng không (Level 1), mà còn kiểm tra finish có đẹp không (Level 3), vật liệu có an toàn không (Level 4), và khách hàng có hài lòng không (Level 5).

**Nếu bỏ qua**: Ship code không biết quality ra sao, không biết regression khi nào出现, không biết model nào tốt hơn model nào, và cuối cùng technical debt tích tụ đến mức refactor tốn 10× chi phí.

## Tổng Quan

**Evaluation** trong AI coding là quá trình **đánh giá hiệu quả và chất lượng** của AI agent khi thực hiện các tác vụ coding. Evaluation bao gồm benchmarking, metrics collection, và continuous improvement dựa trên kết quả đánh giá.

> **"You can't improve what you can't measure"** — Peter Drucker

```
┌──────────────────────────────────────────────────────────────────┐
│                      EVALUATION FRAMEWORK                         │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │                                                            │  │
│  │  ┌──────────┐    ┌──────────┐    ┌──────────┐            │  │
│  │  │  Input   │    │  Agent   │    │  Output  │            │  │
│  │  │  Tasks   │───►│  Execute │───►│  Code    │            │  │
│  │  └──────────┘    └──────────┘    └────┬─────┘            │  │
│  │                                        │                   │  │
│  │                                        ▼                   │  │
│  │  ┌──────────┐    ┌──────────┐    ┌──────────┐            │  │
│  │  │ Improve  │◄───│ Analyze  │◄───│ Evaluate │            │  │
│  │  │ Action   │    │ Results  │    │ Metrics  │            │  │
│  │  └──────────┘    └──────────┘    └──────────┘            │  │
│  │                                                            │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## Nội Dung

| # | Chủ đề | Mô tả |
|---|--------|-------|
| 1 | [Evaluation Dimensions](#1-evaluation-dimensions) | Các chiều đánh giá |
| 2 | [Quality Metrics](#2-quality-metrics) | Metrics chất lượng code |
| 3 | [Performance Benchmarks](#3-performance-benchmarks) | Benchmark hiệu suất |
| 4 | [Evaluation Framework](#4-evaluation-framework) | Framework đánh giá tự động |
| 5 | [Continuous Improvement](#5-continuous-improvement) | Cải tiến liên tục |
| 6 | [Reporting & Dashboards](#6-reporting--dashboards) | Báo cáo và dashboard |
| 7 | [Case Studies](#7-case-studies) | Case studies thực tế |
| 8 | [Evaluation Tooling](#8-evaluation-tooling) | Tools và frameworks |
| 9 | [Best Practices](#9-best-practices) | Nguyên tắc đánh giá |

---

## 1. Evaluation Dimensions

### 1.1 Các Chiều Đánh Giá

```
┌──────────────────────────────────────────────────────────────────┐
│                EVALUATION DIMENSIONS                              │
│                                                                  │
│  1. CORRECTNESS (Đúng)                                          │
│     Code có chạy đúng không?                                    │
│     → Functional correctness, edge cases                       │
│                                                                  │
│  2. QUALITY (Tốt)                                               │
│     Code có sạch không?                                         │
│     → Readability, maintainability, complexity                  │
│                                                                  │
│  3. EFFICIENCY (Nhanh)                                          │
│     Code có tối ưu không?                                       │
│     → Time complexity, memory usage, I/O                       │
│                                                                  │
│  4. SAFETY (An toàn)                                            │
│     Code có an toàn không?                                      │
│     → Security, error handling, edge cases                     │
│                                                                  │
│  5. COMPLETENESS (Đầy đủ)                                       │
│     Task có được hoàn thành hết không?                         │
│     → Coverage, missing features                                │
│                                                                  │
│  6. SPEED (Tốc độ)                                              │
│     Agent có nhanh không?                                       │
│     → Tokens used, time to completion                           │
│                                                                  │
│  7. USABILITY (Dễ dùng)                                         │
│     Output có dễ dùng không?                                    │
│     → Documentation, API design, examples                      │
│                                                                  │
│  8. COST-EFFECTIVENESS (Hiệu quả chi phí)                       │
│     Chi phí có hợp lý không?                                    │
│     → Token cost vs value delivered                             │
│                                                                  │
│  9. RELIABILITY (Đáng tin cậy)                                  │
│     Kết quả có ổn định không?                                   │
│     → Consistency across runs, reproducibility                  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 1.2 Evaluation Rubric

```python
from dataclasses import dataclass, field
from typing import Dict, List, Optional
from enum import Enum

class Rating(Enum):
    EXCELLENT = 5
    GOOD = 4
    ACCEPTABLE = 3
    POOR = 2
    FAILED = 1


@dataclass
class EvaluationDimension:
    """Một chiều đánh giá"""
    name: str
    weight: float              # 0.0 - 1.0
    description: str
    criteria: Dict[Rating, str] = field(default_factory=dict)


@dataclass
class EvaluationResult:
    """Kết quả đánh giá cho một task"""
    task_id: str
    scores: Dict[str, float] = field(default_factory=dict)
    notes: Dict[str, str] = field(default_factory=dict)
    total_score: float = 0.0
    
    def add_score(self, dimension: str, score: float, 
                  note: str = ""):
        self.scores[dimension] = score
        if note:
            self.notes[dimension] = note
    
    def calculate_total(self, weights: Dict[str, float]) -> float:
        """Tính tổng điểm weighted"""
        total = 0.0
        total_weight = 0.0
        
        for dim, weight in weights.items():
            if dim in self.scores:
                total += self.scores[dim] * weight
                total_weight += weight
        
        self.total_score = total / total_weight if total_weight else 0
        return self.total_score


# Define standard rubric
STANDARD_RUBRIC = [
    EvaluationDimension(
        name="correctness",
        weight=0.25,
        description="Code runs correctly, handles edge cases",
        criteria={
            Rating.EXCELLENT: "All tests pass, edge cases handled",
            Rating.GOOD: "Most tests pass, minor edge case missed",
            Rating.ACCEPTABLE: "Core functionality works, some gaps",
            Rating.POOR: "Partially working, key issues remain",
            Rating.FAILED: "Does not work or breaks existing code",
        }
    ),
    EvaluationDimension(
        name="quality",
        weight=0.20,
        description="Code is clean, readable, maintainable",
        criteria={
            Rating.EXCELLENT: "Follows all conventions, self-documenting",
            Rating.GOOD: "Clean code, minor style issues",
            Rating.ACCEPTABLE: "Readable but could be improved",
            Rating.POOR: "Messy but understandable",
            Rating.FAILED: "Unreadable or extremely messy",
        }
    ),
    EvaluationDimension(
        name="efficiency",
        weight=0.15,
        description="Performance is appropriate for the use case",
        criteria={
            Rating.EXCELLENT: "Optimal complexity, minimal resources",
            Rating.GOOD: "Good performance, minor optimizations possible",
            Rating.ACCEPTABLE: "Acceptable for most cases",
            Rating.POOR: "Performance concerns for scale",
            Rating.FAILED: "Severe performance issues",
        }
    ),
    EvaluationDimension(
        name="safety",
        weight=0.15,
        description="Code handles errors and is secure",
        criteria={
            Rating.EXCELLENT: "Comprehensive error handling, no vulnerabilities",
            Rating.GOOD: "Good error handling, minor security notes",
            Rating.ACCEPTABLE: "Basic error handling present",
            Rating.POOR: "Minimal error handling",
            Rating.FAILED: "No error handling, security risks",
        }
    ),
    EvaluationDimension(
        name="completeness",
        weight=0.15,
        description="All requirements are addressed",
        criteria={
            Rating.EXCELLENT: "All requirements + bonus improvements",
            Rating.GOOD: "All requirements met",
            Rating.ACCEPTABLE: "Core requirements met, some missing",
            Rating.POOR: "Partially completed",
            Rating.FAILED: "Major requirements missing",
        }
    ),
    EvaluationDimension(
        name="speed",
        weight=0.10,
        description="Token efficiency and time to completion",
        criteria={
            Rating.EXCELLENT: "Minimal tokens, fast completion",
            Rating.GOOD: "Reasonable token usage",
            Rating.ACCEPTABLE: "Average token usage",
            Rating.POOR: "High token usage, slow",
            Rating.FAILED: "Extremely inefficient",
        }
    ),
]
```

### 1.3 Weight Configuration — Tùy Theo Use Case

```python
# Different weight configurations for different scenarios
WEIGHT_CONFIGS = {
    "production_code": {
        "correctness": 0.30,
        "quality": 0.20,
        "efficiency": 0.15,
        "safety": 0.20,
        "completeness": 0.10,
        "speed": 0.05,
    },
    
    "prototype_hackathon": {
        "correctness": 0.35,
        "quality": 0.05,
        "efficiency": 0.05,
        "safety": 0.05,
        "completeness": 0.40,
        "speed": 0.10,
    },
    
    "library_framework": {
        "correctness": 0.25,
        "quality": 0.25,
        "efficiency": 0.15,
        "safety": 0.15,
        "completeness": 0.10,
        "speed": 0.10,
    },
    
    "ai_agent_harness": {
        "correctness": 0.20,
        "quality": 0.15,
        "efficiency": 0.10,
        "safety": 0.25,
        "completeness": 0.15,
        "speed": 0.15,
    },
}
```

---

## 2. Quality Metrics

### 2.1 Code Quality Metrics

```python
import ast
import math
from dataclasses import dataclass, field
from typing import Dict, List
from pathlib import Path

@dataclass
class CodeQualityReport:
    """Báo cáo chất lượng code"""
    file_path: str
    lines_of_code: int = 0
    cyclomatic_complexity: float = 0.0
    maintainability_index: float = 0.0
    duplication_ratio: float = 0.0
    naming_score: float = 0.0
    documentation_score: float = 0.0
    overall_score: float = 0.0


class CodeQualityAnalyzer:
    """
    Phân tích chất lượng code — tính metrics tự động.
    
    Metrics:
    - Lines of Code (LOC)
    - Cyclomatic Complexity
    - Maintainability Index
    - Code Duplication
    - Naming Conventions
    - Documentation Coverage
    """
    
    def analyze(self, code: str, file_path: str = "") -> CodeQualityReport:
        report = CodeQualityReport(file_path=file_path)
        
        lines = code.strip().split("\n")
        report.lines_of_code = len(lines)
        report.cyclomatic_complexity = self._calc_complexity(code)
        report.maintainability_index = self._calc_maintainability(
            code, report.cyclomatic_complexity, report.lines_of_code
        )
        report.naming_score = self._calc_naming_score(code)
        report.documentation_score = self._calc_doc_score(code)
        
        # Overall score (weighted average)
        scores = [
            report.maintainability_index * 0.3,
            report.naming_score * 0.25,
            report.documentation_score * 0.25,
            max(0, 100 - report.cyclomatic_complexity * 10) * 0.2,
        ]
        report.overall_score = sum(scores)
        
        return report
    
    def _calc_complexity(self, code: str) -> float:
        """Tính cyclomatic complexity"""
        try:
            tree = ast.parse(code)
        except SyntaxError:
            return 10.0  # High complexity for unparseable code
        
        complexity = 1
        for node in ast.walk(tree):
            if isinstance(node, (ast.If, ast.While, ast.For,
                                ast.ExceptHandler)):
                complexity += 1
            elif isinstance(node, ast.BoolOp):
                complexity += len(node.values) - 1
        
        return complexity
    
    def _calc_maintainability(self, code: str, 
                               complexity: float, loc: int) -> float:
        """Tính maintainability index (0-100)"""
        if loc == 0:
            return 100.0
        
        # Simplified MI formula
        mi = 171 - 5.2 * math.log(max(loc, 1)) - 0.23 * complexity - 16.2 * math.log(max(loc, 1))
        return max(0, min(100, mi))
    
    def _calc_naming_score(self, code: str) -> float:
        """Đánh giá naming conventions"""
        try:
            tree = ast.parse(code)
        except SyntaxError:
            return 0.0
        
        good_names = 0
        total_names = 0
        
        for node in ast.walk(tree):
            if isinstance(node, ast.FunctionDef):
                total_names += 1
                if (node.name.islower() and 
                    "_" in node.name or 
                    node.name.startswith("_")):
                    good_names += 1
            elif isinstance(node, ast.ClassDef):
                total_names += 1
                if node.name[0].isupper():
                    good_names += 1
        
        return (good_names / total_names * 100) if total_names else 100.0
    
    def _calc_doc_score(self, code: str) -> float:
        """Đánh giá documentation coverage"""
        try:
            tree = ast.parse(code)
        except SyntaxError:
            return 0.0
        
        documented = 0
        total = 0
        
        for node in ast.walk(tree):
            if isinstance(node, (ast.FunctionDef, ast.ClassDef)):
                total += 1
                if (ast.get_docstring(node)):
                    documented += 1
        
        return (documented / total * 100) if total else 100.0
```

### 2.2 AI Agent Quality Metrics

```python
from dataclasses import dataclass
from typing import Dict, List

@dataclass
class AgentQualityMetrics:
    """
    Metrics đặc thù cho AI coding agent.
    
    Khác với code quality thông thường,
    agent metrics đo cả QUÁ TRÌNH generate,
    không chỉ kết quả cuối cùng.
    """
    
    # === PROCESS METRICS ===
    
    # 1. First-Attempt Success Rate
    # Tỷ lệ code đúng ngay lần đầu (không cần retry)
    first_attempt_success: float = 0.0
    
    # 2. Iteration Efficiency
    # Số lần retry trung bình để hoàn thành task
    avg_iterations: float = 0.0
    
    # 3. Token Efficiency
    # Token sử dụng so với optimal (thấp hơn = tốt hơn)
    token_efficiency: float = 0.0
    
    # 4. Time to First Good Output
    # Thời gian từ prompt đến output hợp lệ đầu tiên
    time_to_first_output: float = 0.0
    
    # === OUTPUT METRICS ===
    
    # 5. Code Correctness
    # Tỷ lệ tests pass
    test_pass_rate: float = 0.0
    
    # 6. Edit Precision
    # Tỷ lệ edits thực sự cần thiết / total edits
    edit_precision: float = 0.0
    
    # 7. Context Relevance
    # Có dùng đúng context không (không hallucinate)
    context_relevance: float = 0.0
    
    # 8. Instruction Following
    # Có làm đúng theo yêu cầu không
    instruction_following: float = 0.0
    
    # === SAFETY METRICS ===
    
    # 9. No Regressions
    # Không phá vỡ existing functionality
    regression_free_rate: float = 0.0
    
    # 10. Security Compliance
    # Không introduce vulnerabilities
    security_compliance: float = 0.0
    
    def calculate_composite_score(self) -> float:
        """Tính composite score từ tất cả metrics"""
        weights = {
            "first_attempt_success": 0.15,
            "test_pass_rate": 0.25,
            "edit_precision": 0.10,
            "context_relevance": 0.10,
            "instruction_following": 0.15,
            "regression_free_rate": 0.15,
            "token_efficiency": 0.10,
        }
        
        total = 0.0
        for metric, weight in weights.items():
            value = getattr(self, metric, 0.0)
            total += value * weight
        
        return total * 100  # Scale to 0-100
```

### 2.3 Metrics Dashboard

```
┌──────────────────────────────────────────────────────────────────┐
│                AI AGENT QUALITY DASHBOARD                         │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ CORRECTNESS                           ████████░░ 82%    │   │
│  │ └─ Test Pass Rate                     ████████░░ 85%    │   │
│  │ └─ Edge Case Handling                 ███████░░░ 72%    │   │
│  │ └─ Regression Free                    █████████░ 90%    │   │
│  ├──────────────────────────────────────────────────────────┤   │
│  │ QUALITY                               ███████░░░ 75%    │   │
│  │ └─ Maintainability                    ███████░░░ 70%    │   │
│  │ └─ Naming Conventions                 █████████░ 88%    │   │
│  │ └─ Documentation                      ███████░░░ 68%    │   │
│  ├──────────────────────────────────────────────────────────┤   │
│  │ EFFICIENCY                            ████████░░ 80%    │   │
│  │ └─ Token Efficiency                   ████████░░ 82%    │   │
│  │ └─ Time to Completion                 ███████░░░ 75%    │   │
│  ├──────────────────────────────────────────────────────────┤   │
│  │ SAFETY                                █████████░ 88%    │   │
│  │ └─ No Vulnerabilities                 ██████████ 95%    │   │
│  │ └─ Error Handling                     ████████░░ 82%    │   │
│  ├──────────────────────────────────────────────────────────┤   │
│  │ COMPOSITE SCORE                       ████████░░ 81/100 │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 3. Performance Benchmarks

### 3.1 Benchmark Framework

```python
import time
from dataclasses import dataclass, field
from typing import Any, Callable, Dict, List
from datetime import datetime

@dataclass
class BenchmarkResult:
    """Kết quả benchmark cho một task"""
    task_id: str
    task_description: str
    tokens_used: int = 0
    execution_time_s: float = 0.0
    success: bool = False
    quality_score: float = 0.0
    attempts: int = 0
    error_message: str = ""
    timestamp: str = field(
        default_factory=lambda: datetime.now().isoformat()
    )


class BenchmarkSuite:
    """
    Benchmark suite cho AI coding agent.
    
    Chạy nhiều tasks, collect metrics, và so sánh
    giữa các runs/models/configurations.
    """
    
    def __init__(self, name: str = "default"):
        self.name = name
        self.results: List[BenchmarkResult] = []
        self.tasks: List[Dict] = []
    
    def add_task(self, task_id: str, description: str,
                 input_data: Any = None,
                 expected_output: Any = None,
                 validator: Callable = None):
        """Thêm task vào benchmark suite"""
        self.tasks.append({
            "id": task_id,
            "description": description,
            "input": input_data,
            "expected": expected_output,
            "validator": validator,
        })
    
    def run(self, agent_func: Callable, max_retries: int = 3):
        """
        Chạy benchmark — chạy agent trên mỗi task.
        
        agent_func: function(task) -> (output, tokens_used)
        """
        for task in self.tasks:
            result = BenchmarkResult(
                task_id=task["id"],
                task_description=task["description"],
            )
            
            start_time = time.time()
            
            for attempt in range(max_retries):
                try:
                    output, tokens = agent_func(task)
                    result.execution_time_s = time.time() - start_time
                    result.tokens_used = tokens
                    result.attempts = attempt + 1
                    
                    # Validate
                    if task.get("validator"):
                        result.success = task["validator"](output)
                    elif task.get("expected"):
                        result.success = output == task["expected"]
                    else:
                        result.success = True
                    
                    if result.success:
                        break
                        
                except Exception as e:
                    result.error_message = str(e)
                    result.attempts = attempt + 1
            
            self.results.append(result)
        
        return self.get_summary()
    
    def get_summary(self) -> Dict:
        """Tổng hợp kết quả benchmark"""
        if not self.results:
            return {"total": 0}
        
        successful = [r for r in self.results if r.success]
        failed = [r for r in self.results if not r.success]
        
        total_tokens = sum(r.tokens_used for r in self.results)
        total_time = sum(r.execution_time_s for r in self.results)
        
        return {
            "suite": self.name,
            "total_tasks": len(self.results),
            "successful": len(successful),
            "failed": len(failed),
            "success_rate": len(successful) / len(self.results),
            "total_tokens": total_tokens,
            "avg_tokens": total_tokens / len(self.results),
            "total_time_s": total_time,
            "avg_time_s": total_time / len(self.results),
            "avg_attempts": sum(
                r.attempts for r in self.results
            ) / len(self.results),
        }
    
    def compare_with(self, other_suite: "BenchmarkSuite") -> Dict:
        """So sánh hai benchmark suites"""
        s1 = self.get_summary()
        s2 = other_suite.get_summary()
        
        return {
            self.name: s1,
            other_suite.name: s2,
            "improvements": {
                "success_rate": (
                    s1["success_rate"] - s2["success_rate"]
                ),
                "avg_tokens": (
                    s2["avg_tokens"] - s1["avg_tokens"]
                ),
                "avg_time": (
                    s2["avg_time_s"] - s1["avg_time_s"]
                ),
            },
        }
```

### 3.2 Standard Benchmark Tasks

```python
BENCHMARK_TASKS = {
    # === TRIVIAL (Difficulty: 1/5) ===
    "hello_world": {
        "id": "basic-001",
        "description": "Create a hello world function",
        "difficulty": "trivial",
        "expected": "Hello, World!",
    },
    
    "string_reverse": {
        "id": "basic-002",
        "description": "Reverse a string without built-in",
        "difficulty": "trivial",
        "validator": lambda out: out("hello") == "olleh",
    },
    
    # === EASY (Difficulty: 2/5) ===
    "fibonacci": {
        "id": "algo-001",
        "description": "Implement fibonacci with memoization",
        "difficulty": "easy",
        "validator": lambda out: out(10) == 55,
    },
    
    "binary_search": {
        "id": "algo-002",
        "description": "Implement binary search",
        "difficulty": "easy",
        "validator": lambda out: out([1,2,3,4,5], 3) == 2,
    },
    
    # === MODERATE (Difficulty: 3/5) ===
    "api_endpoint": {
        "id": "web-001",
        "description": "Create a REST API endpoint for CRUD",
        "difficulty": "moderate",
        "validator": lambda out: True,
    },
    
    "bug_fix": {
        "id": "debug-001",
        "description": "Fix off-by-one error in loop",
        "difficulty": "easy",
        "validator": lambda out: True,
    },
    
    # === COMPLEX (Difficulty: 4/5) ===
    "refactor_class": {
        "id": "refactor-001",
        "description": "Refactor God class into smaller classes",
        "difficulty": "complex",
        "validator": lambda out: True,
    },
    
    "write_tests": {
        "id": "test-001",
        "description": "Write comprehensive unit tests",
        "difficulty": "moderate",
        "validator": lambda out: True,
    },
    
    # === HARD (Difficulty: 5/5) ===
    "security_audit": {
        "id": "security-001",
        "description": "Find and fix SQL injection vulnerabilities",
        "difficulty": "moderate",
        "validator": lambda out: True,
    },
    
    "multi_file_refactor": {
        "id": "complex-001",
        "description": "Refactor across 5+ files with dependency updates",
        "difficulty": "hard",
        "validator": lambda out: True,
    },
}
```

### 3.3 Benchmark Comparison Table

```
┌────────────────────────────────────────────────────────────────────┐
│           BENCHMARK SCORES COMPARISON (2026)                       │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│  Benchmark         │ Metric              │ Human │ Claude │ GPT-4 │
│  ──────────────────┼─────────────────────┼───────┼───────┼───────│
│  HumanEval         │ Pass@1              │ -     │ 92%   │ 86%   │
│  HumanEval         │ Pass@10             │ -     │ 96%   │ 92%   │
│  SWE-bench Lite    │ Resolved            │ -     │ 48%   │ 33%   │
│  SWE-bench Verified│ Resolved            │ -     │ 53%   │ 38%   │
│  MBPP+             │ Pass@1              │ -     │ 89%   │ 82%   │
│  LiveCodeBench     │ Pass@1              │ -     │ 45%   │ 38%   │
│  Aider Polyglot    │ % Correct           │ 95%   │ 72%   │ 61%   │
│                                                                    │
│  ──────────────────┼─────────────────────┼───────┼───────┼───────│
│  COMPOSITE SCORE   │ Overall             │ -     │ ~78%  │ ~65%  │
│                                                                    │
│  ⚠️  Note: Scores vary by model version and prompting strategy  │
│     Claude 3.5 Sonnet with good harness can outperform GPT-4o    │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

---

## 4. Evaluation Framework

### 4.1 Auto-Evaluation Pipeline

```python
from typing import Callable, Dict, List
from dataclasses import dataclass, field
from datetime import datetime

@dataclass
class EvalConfig:
    """Cấu hình evaluation"""
    dimensions: List[str] = field(default_factory=lambda: [
        "correctness", "quality", "efficiency", 
        "safety", "completeness"
    ])
    weights: Dict[str, float] = field(default_factory=lambda: {
        "correctness": 0.25,
        "quality": 0.20,
        "efficiency": 0.15,
        "safety": 0.15,
        "completeness": 0.15,
        "speed": 0.10,
    })
    auto_evaluate: bool = True
    human_review: bool = False


class EvaluationPipeline:
    """
    Pipeline đánh giá tự động cho AI coding output.
    
    Steps:
    1. Static Analysis (lint, complexity, naming)
    2. Dynamic Testing (run tests, check output)
    3. Security Scan (vulnerability check)
    4. Quality Scoring (weighted dimensions)
    5. Report Generation
    """
    
    def __init__(self, config: EvalConfig = None):
        self.config = config or EvalConfig()
        self.checks: List[Callable] = []
        self.results: List[EvaluationResult] = []
    
    def add_check(self, name: str, check_func: Callable,
                  dimension: str, weight: float = 1.0):
        """Thêm một check vào pipeline"""
        self.checks.append({
            "name": name,
            "func": check_func,
            "dimension": dimension,
            "weight": weight,
        })
    
    def evaluate(self, task_id: str, code: str, 
                 context: Dict = None) -> EvaluationResult:
        """Chạy evaluation trên code"""
        result = EvaluationResult(task_id=task_id)
        
        for check in self.checks:
            try:
                score = check["func"](code, context or {})
                result.add_score(
                    check["name"],
                    score * check["weight"],
                    f"Auto-evaluated by {check['name']}"
                )
            except Exception as e:
                result.add_score(
                    check["name"], 0.0,
                    f"Evaluation failed: {str(e)}"
                )
        
        result.calculate_total(self.config.weights)
        self.results.append(result)
        return result
    
    def get_average_scores(self) -> Dict[str, float]:
        """Tính average scores qua tất cả evaluations"""
        if not self.results:
            return {}
        
        dim_scores = {}
        for result in self.results:
            for dim, score in result.scores.items():
                if dim not in dim_scores:
                    dim_scores[dim] = []
                dim_scores[dim].append(score)
        
        return {
            dim: sum(scores) / len(scores)
            for dim, scores in dim_scores.items()
        }


# Standard checks
def check_test_passes(code: str, context: Dict) -> float:
    """Kiểm tra code có chạy qua tests không"""
    tests = context.get("tests", [])
    if not tests:
        return 50.0  # No tests = neutral score
    # Would execute tests and return pass rate
    return 80.0

def check_no_lint_errors(code: str, context: Dict) -> float:
    """Kiểm tra lint"""
    return 90.0  # Simplified

def check_complexity(code: str, context: Dict) -> float:
    """Kiểm tra complexity"""
    analyzer = CodeQualityAnalyzer()
    report = analyzer.analyze(code)
    return min(100, max(0, 100 - report.cyclomatic_complexity * 5))
```

### 4.2 LLM-as-Judge Evaluation

```python
class LLMJudge:
    """
    Dùng LLM để đánh giá output của AI agent.
    
    Leverage: GPT-4, Claude, hoặc model lớn khác
    làm "judge" để đánh giá code quality.
    
    Ưu điểm:
    - Có thể đánh giá nuanced qualities (readability, design)
    - Không cần写了 test cases cho mọi edge case
    - Có thể hiểu intent và context
    
    Nhược điểm:
    - Chi phí (cần thêm 1 LLM call)
    - Có thể không consistent 100%
    - Judge cũng có thể hallucinate
    """
    
    def __init__(self, judge_model_func):
        self.judge = judge_model_func
    
    def evaluate_code(self, code: str, task: str, 
                      context: str = "") -> Dict:
        """Đánh giá code bằng LLM judge"""
        
        prompt = f"""You are a senior code reviewer. Evaluate the following code.

TASK: {task}

CODE:
```
{code}
```

CONTEXT: {context}

Rate the code on these dimensions (1-10):
1. Correctness - Does it solve the task correctly?
2. Code Quality - Is it clean, readable, well-structured?
3. Efficiency - Is it performant?
4. Robustness - Does it handle edge cases?
5. Completeness - Does it address all requirements?

For each dimension, provide:
- Score (1-10)
- Brief justification (1 sentence)

Output as JSON:
{{
  "correctness": {{"score": N, "reason": "..."}},
  "quality": {{"score": N, "reason": "..."}},
  "efficiency": {{"score": N, "reason": "..."}},
  "robustness": {{"score": N, "reason": "..."}},
  "completeness": {{"score": N, "reason": "..."}},
  "overall": N,
  "suggestions": ["suggestion1", "suggestion2"]
}}"""
        
        response = self.judge(prompt)
        return self._parse_judgment(response)
    
    def compare_outputs(self, task: str, 
                        output_a: str, output_b: str) -> Dict:
        """So sánh hai outputs"""
        
        prompt = f"""Compare these two implementations for the same task.

TASK: {task}

IMPLEMENTATION A:
```
{output_a}
```

IMPLEMENTATION B:
```
{output_b}
```

Which is better and why? Rate each (1-10) and pick a winner.
Output as JSON:
{{
  "implementation_a": {{"score": N, "strengths": [...], "weaknesses": [...]}},
  "implementation_b": {{"score": N, "strengths": [...], "weaknesses": [...]}},
  "winner": "A" or "B",
  "reasoning": "..."
}}"""
        
        response = self.judge(prompt)
        return self._parse_comparison(response)
    
    def _parse_judgment(self, response: str) -> Dict:
        """Parse JSON response từ LLM judge"""
        import json
        try:
            # Try to extract JSON from response
            start = response.find('{')
            end = response.rfind('}') + 1
            return json.loads(response[start:end])
        except json.JSONDecodeError:
            return {"error": "Failed to parse judgment"}
```

### 4.3 Regression Testing Framework

```python
class RegressionTestSuite:
    """
    Framework regression testing cho AI coding agent.
    
    Mục đích: Đảm bảo agent không "quên" cách làm đúng
    sau khi được update/prompt mới.
    
    Concept:
    - Mỗi task có "golden answer" đã được verify
    - Khi update agent, chạy lại tất cả tasks
    - Nếu score giảm → REGRESSION
    """
    
    def __init__(self, name: str = "default"):
        self.name = name
        self.test_cases: List[Dict] = []
        self.baselines: Dict[str, float] = {}
    
    def add_test_case(self, task_id: str, prompt: str,
                      golden_output: str, 
                      validator: Callable = None,
                      category: str = "general"):
        """Thêm test case với golden output"""
        self.test_cases.append({
            "id": task_id,
            "prompt": prompt,
            "golden": golden_output,
            "validator": validator,
            "category": category,
        })
    
    def set_baseline(self, results: Dict[str, float]):
        """Đặt baseline score cho mỗi test case"""
        self.baselines = results
    
    def run_and_check(self, agent_func: Callable) -> Dict:
        """
        Chạy agent và check regression.
        
        agent_func: function(prompt) -> output
        
        Returns:
        {
            "total": N,
            "passed": N,
            "regressions": [...],
            "improvements": [...],
            "score_delta": float,
        }
        """
        results = {}
        regressions = []
        improvements = []
        
        for test in self.test_cases:
            output = agent_func(test["prompt"])
            
            # Score against golden
            if test["validator"]:
                score = 100.0 if test["validator"](output) else 0.0
            else:
                score = self._similarity_score(output, test["golden"])
            
            results[test["id"]] = score
            
            # Check regression
            baseline = self.baselines.get(test["id"], 50.0)
            if score < baseline - 10:  # 10% threshold
                regressions.append({
                    "id": test["id"],
                    "baseline": baseline,
                    "current": score,
                    "delta": score - baseline,
                })
            elif score > baseline + 10:
                improvements.append({
                    "id": test["id"],
                    "baseline": baseline,
                    "current": score,
                    "delta": score - baseline,
                })
        
        # Calculate overall
        avg_score = sum(results.values()) / len(results) if results else 0
        avg_baseline = sum(self.baselines.values()) / len(self.baselines) if self.baselines else 50
        
        return {
            "total": len(self.test_cases),
            "passed": sum(1 for s in results.values() if s >= 70),
            "regressions": regressions,
            "improvements": improvements,
            "avg_score": avg_score,
            "score_delta": avg_score - avg_baseline,
            "has_regression": len(regressions) > 0,
        }
    
    def _similarity_score(self, actual: str, expected: str) -> float:
        """Simple text similarity score"""
        actual_words = set(actual.lower().split())
        expected_words = set(expected.lower().split())
        
        if not expected_words:
            return 100.0
        
        intersection = actual_words & expected_words
        return (len(intersection) / len(expected_words)) * 100
```

---

## 5. Continuous Improvement

### 5.1 Improvement Loop

```
┌──────────────────────────────────────────────────────────────────┐
│              CONTINUOUS IMPROVEMENT LOOP                          │
│                                                                  │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐                 │
│  │  1. MEASURE│   │ 2. ANALYZE│   │ 3. IDENTIFY│               │
│  │  Collect   │──►│ Find     │──►│ Top 3    │                  │
│  │  metrics   │   │ patterns │   │ issues   │                  │
│  └──────────┘    └──────────┘    └────┬─────┘                  │
│                                       │                          │
│                                       ▼                          │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐                 │
│  │  5. VERIFY│   │ 4. IMPROVE│   │  Plan    │                  │
│  │  Re-run   │◄──│ Update   │◄──│ Fix     │                   │
│  │  benchmark│   │ prompts  │   │ strategy│                    │
│  └─────┬────┘    └──────────┘    └──────────┘                  │
│        │                                                        │
│        └───► Back to 1. MEASURE                                │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

```python
from typing import Dict, List, Tuple

class ImprovementTracker:
    """
    Theo dõi improvement qua mỗi iteration.
    
    Tracks:
    - Benchmark scores over time
    - Regression detection
    - Improvement suggestions
    """
    
    def __init__(self):
        self.iterations: List[Dict] = []
        self.baselines: Dict[str, float] = {}
    
    def set_baseline(self, benchmark_name: str, scores: Dict):
        """Đặt baseline cho benchmark"""
        self.baselines[benchmark_name] = scores
    
    def record_iteration(self, iteration_id: str,
                         benchmark_name: str,
                         scores: Dict[str, float]):
        """Ghi kết quả iteration"""
        self.iterations.append({
            "id": iteration_id,
            "benchmark": benchmark_name,
            "scores": scores,
            "timestamp": datetime.now().isoformat(),
        })
    
    def analyze_trend(self, benchmark_name: str) -> Dict:
        """Phân tích trend qua các iterations"""
        relevant = [
            i for i in self.iterations 
            if i["benchmark"] == benchmark_name
        ]
        
        if len(relevant) < 2:
            return {"trend": "insufficient_data"}
        
        # Compare first and last
        first = relevant[0]["scores"]
        last = relevant[-1]["scores"]
        
        improvements = {}
        regressions = {}
        
        for metric in last:
            if metric in first:
                change = last[metric] - first[metric]
                if change > 0:
                    improvements[metric] = change
                elif change < 0:
                    regressions[metric] = abs(change)
        
        return {
            "iterations": len(relevant),
            "improvements": improvements,
            "regressions": regressions,
            "overall_trend": "improving" if not regressions else "mixed",
        }
    
    def suggest_improvements(self, 
                              eval_results: List[EvaluationResult]
                              ) -> List[str]:
        """Gợi ý cải tiến dựa trên evaluation results"""
        suggestions = []
        
        # Find consistently low scores
        dim_avgs = {}
        for result in eval_results:
            for dim, score in result.scores.items():
                if dim not in dim_avgs:
                    dim_avgs[dim] = []
                dim_avgs[dim].append(score)
        
        for dim, scores in dim_avgs.items():
            avg = sum(scores) / len(scores)
            if avg < 60:
                suggestions.append(
                    f"⚠️ {dim}: Average score {avg:.1f}/100. "
                    f"Consider improving prompts or adding "
                    f"specific rules for {dim}."
                )
            elif avg < 80:
                suggestions.append(
                    f"💡 {dim}: Score {avg:.1f}/100. "
                    f"Room for improvement with "
                    f"better context or examples."
                )
        
        return suggestions
```

### 5.2 A/B Testing Framework

```python
class ABTestFramework:
    """
    A/B testing cho prompt engineering.
    
    So sánh 2 versions của prompt/harness
    trên cùng một benchmark suite.
    
    Giống như A/B testing cho web,
    nhưng áp dụng cho AI agent configuration.
    """
    
    def __init__(self, benchmark_suite: BenchmarkSuite):
        self.suite = benchmark_suite
        self.results = {"A": None, "B": None}
    
    def run_variant(self, variant: str, 
                    agent_func: Callable) -> Dict:
        """Chạy một variant"""
        self.results[variant] = self.suite.run(agent_func)
        return self.results[variant]
    
    def analyze(self) -> Dict:
        """
        Phân tích kết quả A/B test.
        
        Dùng statistical significance test
        để quyết định variant nào tốt hơn.
        """
        if not all(self.results.values()):
            return {"error": "Both variants must be run first"}
        
        a_summary = self.results["A"]
        b_summary = self.results["B"]
        
        # Simple comparison
        metrics = {}
        for key in a_summary:
            if isinstance(a_summary[key], (int, float)):
                a_val = a_summary[key]
                b_val = b_summary.get(key, 0)
                metrics[key] = {
                    "A": a_val,
                    "B": b_val,
                    "delta": b_val - a_val,
                    "winner": "A" if a_val > b_val else "B",
                }
        
        # Overall winner (by success rate)
        a_rate = a_summary.get("success_rate", 0)
        b_rate = b_summary.get("success_rate", 0)
        
        return {
            "metrics": metrics,
            "overall_winner": "A" if a_rate >= b_rate else "B",
            "confidence": "high" if abs(a_rate - b_rate) > 0.1 else "low",
        }
```

---

## 6. Reporting & Dashboards

### 6.1 Evaluation Report Generator

```python
from datetime import datetime
from typing import Dict, List

class EvaluationReporter:
    """
    Tạo báo cáo evaluation — markdown, JSON, hoặc dashboard.
    """
    
    def generate_markdown_report(self, 
                                  results: List[EvaluationResult],
                                  summary: Dict) -> str:
        """Tạo báo cáo Markdown"""
        report = []
        report.append("# 📊 Evaluation Report")
        report.append(f"\n**Generated:** {datetime.now().isoformat()}")
        report.append(f"**Tasks Evaluated:** {len(results)}")
        
        # Summary
        report.append("\n## Summary\n")
        report.append("| Metric | Value |")
        report.append("|--------|-------|")
        report.append(f"| Total Tasks | {summary.get('total', 0)} |")
        report.append(f"| Success Rate | {summary.get('success_rate', 0):.1%} |")
        report.append(f"| Avg Quality Score | {summary.get('avg_quality', 0):.1f} |")
        report.append(f"| Total Tokens | {summary.get('total_tokens', 0):,} |")
        
        # Per-dimension scores
        report.append("\n## Dimension Scores\n")
        report.append("| Dimension | Avg Score | Status |")
        report.append("|-----------|-----------|--------|")
        
        for dim, score in summary.get("dimension_scores", {}).items():
            status = "✅" if score >= 80 else "⚠️" if score >= 60 else "❌"
            report.append(f"| {dim} | {score:.1f} | {status} |")
        
        # Top issues
        report.append("\n## Top Issues\n")
        for i, issue in enumerate(summary.get("top_issues", []), 1):
            report.append(f"{i}. {issue}")
        
        # Individual results
        report.append("\n## Task Results\n")
        for result in results:
            status = "✅" if result.total_score >= 70 else "❌"
            report.append(
                f"### {status} {result.task_id}\n"
            )
            report.append(f"**Score:** {result.total_score:.1f}/100\n")
            
            for dim, score in result.scores.items():
                bar = "█" * int(score / 10) + "░" * (10 - int(score / 10))
                note = result.notes.get(dim, "")
                report.append(
                    f"- `{dim}`: {bar} {score:.0f}/100"
                    f"{f' — {note}' if note else ''}"
                )
            report.append("")
        
        return "\n".join(report)
    
    def generate_json_report(self,
                              results: List[EvaluationResult],
                              summary: Dict) -> Dict:
        """Tạo báo cáo JSON"""
        return {
            "metadata": {
                "generated_at": datetime.now().isoformat(),
                "total_tasks": len(results),
            },
            "summary": summary,
            "results": [
                {
                    "task_id": r.task_id,
                    "total_score": r.total_score,
                    "scores": r.scores,
                    "notes": r.notes,
                }
                for r in results
            ],
        }
    
    def generate_dashboard_data(self, 
                                 results: List[EvaluationResult]) -> Dict:
        """Generate data cho visual dashboard"""
        return {
            "timeline": self._build_timeline(results),
            "distribution": self._build_distribution(results),
            "heatmap": self._build_heatmap(results),
        }
    
    def _build_timeline(self, results: List) -> List:
        """Build timeline data cho chart"""
        return [
            {
                "date": r.timestamp,
                "score": r.total_score,
                "task": r.task_id,
            }
            for r in results
        ]
    
    def _build_distribution(self, results: List) -> Dict:
        """Build score distribution"""
        ranges = {
            "0-20": 0, "20-40": 0, "40-60": 0,
            "60-80": 0, "80-100": 0
        }
        
        for r in results:
            score = r.total_score
            if score < 20:
                ranges["0-20"] += 1
            elif score < 40:
                ranges["20-40"] += 1
            elif score < 60:
                ranges["40-60"] += 1
            elif score < 80:
                ranges["60-80"] += 1
            else:
                ranges["80-100"] += 1
        
        return ranges
    
    def _build_heatmap(self, results: List) -> List:
        """Build heatmap data (task x dimension)"""
        dimensions = set()
        for r in results:
            dimensions.update(r.scores.keys())
        
        heatmap = []
        for r in results:
            row = {"task": r.task_id}
            for dim in sorted(dimensions):
                row[dim] = r.scores.get(dim, 0)
            heatmap.append(row)
        
        return heatmap
```

---

## 7. Case Studies

### 7.1 SWE-bench — Benchmarking AI Code Agents

**Bối cảnh**: SWE-bench là benchmark tiêu chuẩn đánh giá khả năng sửa lỗi real-world của AI agents trên các GitHub repositories thực tế.

**Kết quả thực tế**:

```
┌────────────────────────────────────────────────────────────────┐
│                 SWE-BENCH RESULTS 2026                          │
├────────────────────────────────────────────────────────────────┤
│                                                                │
│  Agent              │ Verified │ Lite  │ Full  │ Cost/Task     │
│  ───────────────────┼──────────┼───────┼───────┼──────────────│
│  Claude Code+Harness│ 53%      │ 48%   │ 42%   │ ~$2.50       │
│  Devin              │ 48%      │ 44%   │ 38%   │ ~$8.00       │
│  OpenHands+SWE      │ 45%      │ 42%   │ 35%   │ ~$3.00       │
│  Cursor (agent)     │ 42%      │ 38%   │ 32%   │ ~$1.50       │
│  GitHub Copilot     │ 35%      │ 33%   │ 28%   │ ~$0.50       │
│  SWE-agent (base)   │ 20%      │ 20%   │ 15%   │ ~$1.00       │
│                                                                │
│  Key Insight: Harness design accounts for 30%+ of             │
│  the performance difference between agents!                    │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

**Key Learnings:**
1. Harness design impact > model choice
2. Tool permissions affect safety significantly
3. Context management affects token cost
4. Multi-agent improves complex task handling

### 7.2 HumanEval — Classic Code Generation

```
┌────────────────────────────────────────────────────────────────┐
│                 HUMANEVAL EVOLUTION                             │
├────────────────────────────────────────────────────────────────┤
│                                                                │
│  Model            │ Pass@1 (2023) │ Pass@1 (2026) │ Improvement│
│  ─────────────────┼───────────────┼───────────────┼───────────│
│  GPT-3.5 Turbo    │ 48%           │ 72%           │ +50%      │
│  GPT-4            │ 67%           │ 86%           │ +28%      │
│  Claude 3.5 Sonnet│ -             │ 92%           │ -         │
│  Gemini 2.5 Pro   │ -             │ 88%           │ -         │
│  DeepSeek-V3      │ -             │ 90%           │ -         │
│                                                                │
│  ⚠️ HumanEval reaching ceiling — need harder benchmarks      │
│     → SWE-bench, LiveCodeBench becoming more relevant         │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

### 7.3 Real-World Evaluation Pipeline — Production Case

```python
# Real-world example: How a team evaluates their AI coding agent

class ProductionEvaluator:
    """
    Production evaluation pipeline được dùng hàng ngày.
    
    Context: Team 20 developers sử dụng AI coding agent
    daily. Pipeline chạy mỗi đêm để monitor quality.
    """
    
    def __init__(self):
        self.benchmark_suite = BenchmarkSuite("daily")
        self.regression_suite = RegressionTestSuite("production")
        self.reporter = EvaluationReporter()
    
    def setup_daily_benchmarks(self):
        """Setup benchmark tasks cho daily run"""
        
        # 10 representative tasks cho team's domain
        self.benchmark_suite.add_task(
            "fix-001", "Fix null pointer in UserService.getProfile",
            validator=lambda out: "null check" in str(out).lower()
        )
        
        self.benchmark_suite.add_task(
            "feat-001", "Add pagination to ProductController.list",
            validator=lambda out: "offset" in str(out).lower() or 
                                   "limit" in str(out).lower()
        )
        
        self.benchmark_suite.add_task(
            "test-001", "Write unit tests for PaymentService.charge",
            validator=lambda out: "assert" in str(out).lower()
        )
        
        self.benchmark_suite.add_task(
            "refactor-001", "Extract database queries to repository pattern",
            validator=lambda out: "Repository" in str(out)
        )
        
        self.benchmark_suite.add_task(
            "security-001", "Fix SQL injection in UserDAO.findByEmail",
            validator=lambda out: "parameterized" in str(out).lower() or
                                   "prepared" in str(out).lower()
        )
    
    def run_daily_evaluation(self, agent_func: Callable) -> Dict:
        """Chạy evaluation hàng ngày"""
        
        # 1. Run benchmarks
        summary = self.benchmark_suite.run(agent_func)
        
        # 2. Check regression
        reg_result = self.regression_suite.run_and_check(agent_func)
        
        # 3. Generate report
        report = self.reporter.generate_markdown_report(
            [],  # Would pass actual results
            summary
        )
        
        # 4. Alert if regression detected
        if reg_result["has_regression"]:
            self._send_alert(reg_result)
        
        # 5. Track trend
        return {
            "summary": summary,
            "regression": reg_result,
            "report": report,
        }
    
    def _send_alert(self, reg_result: Dict):
        """Gửi alert khi regression detected"""
        regressions = reg_result["regressions"]
        message = f"⚠️ REGRESSION DETECTED!\n"
        message += f"Score delta: {reg_result['score_delta']:.1f}%\n"
        for reg in regressions:
            message += f"  - {reg['id']}: {reg['baseline']:.0f} → {reg['current']:.0f}\n"
        
        # Would send to Slack, email, etc.
        print(message)
```

---

## 8. Evaluation Tooling

### 8.1 Popular Evaluation Tools

```
┌──────────────────────────────────────────────────────────────────┐
│                 EVALUATION TOOLS ECOSYSTEM                        │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  BENCHMARK SUITES:                                               │
│  ├── HumanEval (OpenAI) — Classic code gen benchmark           │
│  ├── SWE-bench (Princeton) — Real-world bug fix                │
│  ├── MBPP (Google) — Mostly Basic Python Problems              │
│  ├── LiveCodeBench — Live competition-style tasks               │
│  └── BigCodeBench — Complex function-level tasks               │
│                                                                  │
│  EVALUATION FRAMEWORKS:                                          │
│  ├── PromptFoo — Prompt testing & evaluation                    │
│  ├── DeepEval — LLM evaluation framework                        │
│  ├── RAGAS — RAG-specific evaluation                            │
│  ├── LangSmith — Tracing & evaluation                           │
│  └── Weights & Biases — Experiment tracking                     │
│                                                                  │
│  CODE QUALITY TOOLS:                                             │
│  ├── SonarQube — Code quality & security                        │
│  ├── ESLint/Prettier — Style checking                           │
│  ├── Pylint/Ruff — Python linting                                │
│  ├── pytest — Test runner                                        │
│  └── Coverage.py — Test coverage                                 │
│                                                                  │
│  SECURITY SCANNERS:                                              │
│  ├── Bandit — Python security                                   │
│  ├── Semgrep — Multi-language security                           │
│  ├── npm audit — Node.js vulnerabilities                        │
│  └── Snyk — Dependency scanning                                 │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 8.2 PromptFoo Configuration Example

```yaml
# promptfooconfig.yaml
# Configuration cho PromptFoo evaluation

description: "AI Coding Agent Evaluation"

providers:
  - id: "openai:gpt-4o"
    config:
      temperature: 0
  - id: "anthropic:messages:claude-3.5-sonnet"
    config:
      temperature: 0

prompts:
  - file://prompts/v1.txt
  - file://prompts/v2.txt

tests:
  - description: "Fix null pointer bug"
    vars:
      task: "Fix the null pointer in UserService.getProfile"
      file_content: "{{fixture.userservice}}"
    assert:
      - type: contains
        value: "null check"
      - type: llm-rubric
        value: "Code handles null case properly, no runtime errors"
  
  - description: "Add pagination"
    vars:
      task: "Add pagination to ProductController.list"
      file_content: "{{fixture.productcontroller}}"
    assert:
      - type: contains
        value: "offset"
      - type: contains
        value: "limit"
      - type: llm-rubric
        value: "Pagination is correctly implemented with proper defaults"

  - description: "Security fix"
    vars:
      task: "Fix SQL injection in UserDAO.findByEmail"
      file_content: "{{fixture.userdao}}"
    assert:
      - type: llm-rubric
        value: "Uses parameterized queries, no string concatenation for SQL"

metrics:
  correctness:
    weight: 0.3
  quality:
    weight: 0.25
  safety:
    weight: 0.25
  efficiency:
    weight: 0.2
```

---

## 9. Best Practices

### 9.1 DO và DON'T

```
┌──────────────────────────────────────────────────────────────────┐
│              EVALUATION BEST PRACTICES                           │
│                                                                  │
│  ✅ DO:                                                          │
│                                                                  │
│  1. AUTOMATE EVERYTHING                                         │
│     Evaluation phải tự động, không manual                      │
│     → Consistent, repeatable, scalable                         │
│                                                                  │
│  2. MULTIPLE DIMENSIONS                                         │
│     Không chỉ check "đúng/sai"                                 │
│     → Quality + Correctness + Efficiency + Safety               │
│                                                                  │
│  3. REGULAR BENCHMARKS                                          │
│     Chạy benchmark định kỳ (mỗi PR, mỗi week)                │
│     → Catch regressions early                                   │
│                                                                  │
│  4. BASELINE TRACKING                                           │
│     Lưu baseline để so sánh                                    │
│     → Know if you're improving or regressing                   │
│                                                                  │
│  5. ACTIONABLE FEEDBACK                                          │
│     Kết quả phải gợi ý hành động cụ thể                       │
│     → "Fix X" not just "Score is low"                          │
│                                                                  │
│  6. BALANCED METRICS                                             │
│     Đừng optimize 1 metric mà sacrifice metric khác            │
│     → Trade-offs are real                                       │
│                                                                  │
│  7. HUMAN CALIBRATION                                            │
│     Định kỳ đối chiếu auto-eval với human review              │
│     → Ensure evaluation quality                                 │
│                                                                  │
│  8. TEST EDGE CASES                                              │
│     Benchmark phải có diverse difficulty levels                │
│     → Don't just test the easy stuff                            │
│                                                                  │
│  9. MONITOR COST                                                  │
│     Track token cost per evaluation                              │
│     → Evaluation shouldn't cost more than the value             │
│                                                                  │
│  10. SHARE RESULTS                                                │
│      Publish evaluation results for team transparency           │
│      → Build trust in AI coding tools                           │
│                                                                  │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ❌ DON'T:                                                       │
│                                                                  │
│  1. Don't use a single metric                                    │
│  2. Don't ignore regressions                                     │
│  3. Don't benchmark on trivial tasks only                       │
│  4. Don't trust auto-eval without human calibration            │
│  5. Don't optimize for benchmark at expense of real usage      │
│  6. Don't forget to evaluate cost/efficiency                   │
│  7. Don't skip evaluation after prompt changes                  │
│  8. Don't compare across different benchmark setups            │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 9.2 Evaluation Strategy

```
┌──────────────────────────────────────────────────────────────────┐
│              EVALUATION STRATEGY PYRAMID                         │
│                                                                  │
│                        ┌──────────┐                              │
│                        │ E2E Test │ ← Real user scenarios       │
│                        │ (Weekly) │    Slow, expensive           │
│                       ┌┴──────────┴┐                            │
│                       │ Integration │ ← Multi-component tests  │
│                       │   (Daily)   │    Medium speed           │
│                      ┌┴─────────────┴┐                         │
│                      │ Unit Tests     │ ← Individual functions │
│                      │   (Per Commit) │    Fast, cheap          │
│                     ┌┴───────────────┴┐                        │
│                     │ Static Analysis  │ ← Lint, type check    │
│                     │   (Continuous)   │    Instant             │
│                    ┌┴─────────────────┴┐                       │
│                    │ Code Review        │ ← Human + AI review  │
│                    │   (Continuous)     │    Quality gate        │
│                    └───────────────────┘                        │
│                                                                  │
│  Strategy:                                                      │
│  - Static analysis: Every keystroke (IDE)                       │
│  - Unit tests: Every commit                                     │
│  - Integration: Daily                                           │
│  - E2E: Weekly                                                  │
│  - Regression: Every prompt change                              │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 10. Case Studies Thực Tế

### 10.1 Princeton NLP SWE-bench: Benchmarking Real-World Code

```
┌──────────────────────────────────────────────────────────────────┐
│                    SWE-BENCH EVALUATION                           │
│                                                                  │
│  WHAT: Benchmark từ 2294 GitHub issues với ground-truth patches │
│  HOW: Agent phải reproduce bug fix từ issue description         │
│  METRIC: % issues resolved (pass unit tests)                    │
│                                                                  │
│  LEADERBOARD (2024-2025):                                        │
│  ┌────────────────────┬──────────┬───────────┬───────────────┐  │
│  │ System             │ Resolve  │ Token Use │ Cost/Issue    │  │
│  ├────────────────────┼──────────┼───────────┼───────────────┤  │
│  │ Amazon Q           │ 26.0%    │ ~20K      │ ~$0.50        │  │
│  │ Agentless          │ 24.0%    │ ~15K      │ ~$0.30        │  │
│  │ SWE-agent+GPT-4    │ 22.7%    │ ~25K      │ ~$0.50        │  │
│  │ AutoCodeRover      │ 19.0%    │ ~18K      │ ~$0.40        │  │
│  │ Aider+GPT-4o       │ 18.5%    │ ~20K      │ ~$0.40        │  │
│  │ OpenHands+CodeAct  │ 17.0%    │ ~30K      │ ~$0.60        │  │
│  └────────────────────┴──────────┴───────────┴───────────────┘  │
│                                                                  │
│  KEY INSIGHTS:                                                   │
│  → LLM + Agent loop ≠ always better than simple retrieval      │
│  → Agentless approaches (no LLM in loop) often competitive      │
│  → Search/retrieval quality matters more than model size        │
│  → Cost varies 10x across systems for same quality              │
│                                                                  │
│  EVALUATION METHODOLOGY:                                         │
│  1. Run agent on each issue (with timeout + token budget)       │
│  2. Apply generated patch to codebase                           │
│  3. Run existing test suite                                     │
│  4. Check if failing tests now pass                             │
│  5. Check if passing tests still pass (regression)              │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 10.2 Aider: LLM Leaderboard for Coding

```
┌──────────────────────────────────────────────────────────────────┐
│                 AIDER LLM LEADERBOARD                             │
│                                                                  │
│  BENCHMARK: Edit-format compliance + code quality               │
│  METHODOLOGY: LLM edits whole files, judge by diff quality     │
│                                                                  │
│  TOP MODELS (2024-2025):                                        │
│  ┌────────────────────┬──────────┬───────────┬───────────────┐  │
│  │ Model              │ Score    │ Cost/1M   │ Best For      │  │
│  ├────────────────────┼──────────┼───────────┼───────────────┤  │
│  │ Claude 3.5 Sonnet  │ 74%      │ $3/$15    │ Complex tasks  │  │
│  │ GPT-4o             │ 70%      │ $2.5/$10  │ General code   │  │
│  │ DeepSeek V3        │ 68%      │ $0.27/$1.1│ Budget tasks   │  │
│  │ Gemini 1.5 Pro     │ 66%      │ $1.25/$5  │ Long context   │  │
│  │ Claude 3 Haiku     │ 58%      │ $0.25/$1.2│ Fast/cheap     │  │
│  └────────────────────┴──────────┴───────────┴───────────────┘  │
│                                                                  │
│  COST-EFFECTIVENESS RANKING:                                     │
│  1. DeepSeek V3: 68% score at $0.27/1M tokens (best value)    │
│  2. Claude 3.5 Sonnet: 74% score at $3/1M tokens (best quality)│
│  3. GPT-4o: 70% score at $2.5/1M tokens (balanced)             │
│                                                                  │
│  INSIGHT: DeepSeek V3 achieves 92% of GPT-4o quality at 11%   │
│  of the cost — making it the best choice for routine tasks.     │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 10.3 LiveCodeBench: Dynamic Evaluation

```
┌──────────────────────────────────────────────────────────────────┐
│              LIVECODEBENCH DYNAMIC BENCHMARK                      │
│                                                                  │
│  PROBLEM WITH STATIC BENCHMARKS:                                 │
│  → Models may have been trained on benchmark data               │
│  → Static benchmarks become stale over time                     │
│  → Leaderboard gaming possible                                  │
│                                                                  │
│  SOLUTION: Continuously updated benchmark                       │
│  → New problems added weekly from contest platforms             │
│  → Temporal awareness (problems after training cutoff)          │
│  → Contamination detection built-in                             │
│                                                                  │
│  EVALUATION PIPELINE:                                            │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐ │
│  │ Scrape   │───►│  Dedupe  │───►│ Validate │───►│  Run LLM │ │
│  │ Problems │    │  & Clean │    │  Tests   │    │  (temp)  │ │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘ │
│       │               │               │               │         │
│  ┌────┴────┐    ┌────┴────┐    ┌────┴────┐    ┌────┴────┐    │
│  │ Contest │    │ Remove  │    │ Generate │    │ Pass/Fail│   │
│  │ Platforms│   │ duplicates│   │ test cases│   │ + Time   │   │
│  └─────────┘    └─────────┘    └─────────┘    └─────────┘    │
│                                                                  │
│  METRICS TRACKED:                                                │
│  → Pass@1: Does the first solution work?                       │
│  → Pass@10: Does any of 10 solutions work?                     │
│  → Time-to-solution: How fast?                                  │
│  → Token efficiency: Tokens per correct solution                │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 10.4 Anthropic's Evaluation Methodology

```
┌──────────────────────────────────────────────────────────────────┐
│            ANTHROPIC EVALUATION METHODOLOGY                       │
│                                                                  │
│  PRINCIPLE: "Eval what matters, not what's easy to measure"     │
│                                                                  │
│  EVALUATION LAYERS:                                              │
│                                                                  │
│  LAYER 1: AUTOMATED BENCHMARKS                                  │
│  → HumanEval, SWE-bench, internal benchmarks                   │
│  → Fast, scalable, objective                                    │
│  → Limitation: May not reflect real-world usage                │
│                                                                  │
│  LAYER 2: HUMAN EVALUATION                                       │
│  → Expert reviewers rate code quality                          │
│  → Assess: Readability, maintainability, correctness           │
│  → Limitation: Slow, expensive, subjective                     │
│                                                                  │
│  LAYER 3: REAL USER FEEDBACK                                     │
│  → Track acceptance rate of suggestions                        │
│  → Measure time saved vs manual coding                         │
│  → Limitation: Noisy signal, many confounders                  │
│                                                                  │
│  LAYER 4: A/B TESTING                                            │
│  → Compare model versions on real tasks                        │
│  → Statistical significance testing                             │
│  → Limitation: Requires large sample sizes                     │
│                                                                  │
│  KEY METRIC: "Acceptance Rate" (% of AI suggestions accepted)   │
│  → High acceptance = AI is useful                               │
│  → Low acceptance = Need to improve                            │
│  → Tracked across: task type, complexity, user experience      │
│                                                                  │
│  COST AWARENESS:                                                 │
│  → Track $ per quality point                                    │
│  → Optimize model routing (cheap model for easy tasks)          │
│  → Set token budgets per task type                              │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 11. TypeScript Interfaces cho Evaluation

### 11.1 Core Evaluation Types

```typescript
// ═══════════════════════════════════════════════════════════════
// EVALUATION TYPES — Production-grade interfaces cho evaluation systems
// ═══════════════════════════════════════════════════════════════

/**
 * Benchmark definition — How to structure an evaluation benchmark
 */
interface BenchmarkConfig {
  name: string;
  description: string;
  version: string;
  tasks: BenchmarkTask[];
  metrics: MetricDefinition[];
  settings: BenchmarkSettings;
}

interface BenchmarkTask {
  id: string;
  name: string;
  description: string;
  difficulty: 'easy' | 'medium' | 'hard' | 'expert';
  category: string;
  input: TaskInput;
  expectedOutput?: TaskOutput;
  testCases: TestCase[];
  timeout: number;        // ms
  tokenBudget?: number;   // max tokens
}

interface TaskInput {
  prompt: string;
  context?: string;
  files?: FileReference[];
  language?: string;
}

interface TaskOutput {
  code: string;
  explanation?: string;
  filesModified?: string[];
}

interface TestCase {
  id: string;
  name: string;
  input: any;
  expectedOutput: any;
  type: 'unit' | 'integration' | 'e2e' | 'edge_case';
  weight: number;  // 0-1, importance in scoring
}

interface BenchmarkSettings {
  maxRetries: number;
  timeoutPerTask: number;       // ms
  tokenBudgetPerTask: number;
  parallelism: number;
  temperature: number;
  randomSeed?: number;
  modelConfig: ModelConfig;
}

interface ModelConfig {
  provider: 'openai' | 'anthropic' | 'ollama' | 'custom';
  model: string;
  maxTokens: number;
  temperature: number;
  topP?: number;
}

/**
 * Evaluation results
 */
interface EvaluationResult {
  benchmarkName: string;
  model: string;
  timestamp: string;
  summary: EvaluationSummary;
  taskResults: TaskResult[];
  metrics: Record<string, number>;
  cost: CostBreakdown;
}

interface EvaluationSummary {
  totalTasks: number;
  passed: number;
  failed: number;
  errored: number;
  passRate: number;
  avgScore: number;
  avgDuration: number;
  avgTokens: number;
  totalTokens: number;
  totalCost: number;
}

interface TaskResult {
  taskId: string;
  status: 'passed' | 'failed' | 'error' | 'timeout';
  score: number;           // 0-1
  duration: number;        // ms
  tokensUsed: number;
  output?: string;
  error?: string;
  testResults: TestCaseResult[];
}

interface TestCaseResult {
  testCaseId: string;
  passed: boolean;
  actual: any;
  expected: any;
  duration: number;
}

interface CostBreakdown {
  inputTokens: number;
  outputTokens: number;
  inputCost: number;
  outputCost: number;
  totalCost: number;
  costPerTask: number;
  costPerPassingTask: number;
}

/**
 * Quality metrics
 */
interface QualityMetrics {
  correctness: CorrectnessMetrics;
  efficiency: EfficiencyMetrics;
  safety: SafetyMetrics;
  maintainability: MaintainabilityMetrics;
}

interface CorrectnessMetrics {
  passRate: number;
  edgeCaseHandling: number;
  errorHandling: number;
  regressionRate: number;
}

interface EfficiencyMetrics {
  timeComplexity: string;      // e.g., "O(n log n)"
  memoryUsage: number;         // bytes
  tokenEfficiency: number;     // tokens per correct answer
  responseTime: number;        // ms
}

interface SafetyMetrics {
  securityScore: number;       // 0-100
  vulnerabilityCount: number;
  inputValidationScore: number;
  dataLeakage: boolean;
}

interface MaintainabilityMetrics {
  codeComplexity: number;      // cyclomatic complexity
  documentationCoverage: number;  // %
  testCoverage: number;        // %
  duplicationRatio: number;    // 0-1
}

/**
 * Continuous improvement tracking
 */
interface ImprovementTracking {
  version: string;
  timestamp: string;
  previousVersion?: string;
  metrics: Record<string, number>;
  regressions: Regression[];
  improvements: Improvement[];
}

interface Regression {
  metric: string;
  previousValue: number;
  currentValue: number;
  percentChange: number;
  taskCategory?: string;
}

interface Improvement {
  metric: string;
  previousValue: number;
  currentValue: number;
  percentChange: number;
  taskCategory?: string;
}

/**
 * A/B Testing
 */
interface ABTestConfig {
  name: string;
  variants: Variant[];
  sampleSize: number;
  significanceLevel: number;  // typically 0.05
  metrics: string[];
}

interface Variant {
  id: string;
  name: string;
  description: string;
  config: ModelConfig;
  promptTemplate?: string;
  weight: number;  // traffic split (0-1)
}

interface ABTestResult {
  config: ABTestConfig;
  results: VariantResult[];
  winner?: string;
  pValue: number;
  significant: boolean;
}

interface VariantResult {
  variantId: string;
  sampleSize: number;
  metrics: Record<string, number>;
  confidenceInterval: [number, number];
}

/**
 * Harness quality evaluation — Evaluating the evaluation itself
 */
interface HarnessQualityMetrics {
  /** How well does the benchmark correlate with human judgment? */
  humanCorrelation: number;  // 0-1, Pearson correlation
  /** Are benchmark scores stable across runs? */
  testRetestReliability: number;  // 0-1
  /** Does the benchmark discriminate between good and bad? */
  discriminativePower: number;  // 0-1
  /** How much does it cost to run? */
  costPerRun: number;
  /** How long does a full evaluation take? */
  durationMinutes: number;
  /** Coverage of task types and difficulties */
  coverageScore: number;  // 0-1
}
```

---

## 12. Design Principles cho Evaluation

### 12.1 SOLID cho Evaluation Systems

```
┌──────────────────────────────────────────────────────────────────┐
│          SOLID PRINCIPLES IN EVALUATION SYSTEMS                   │
│                                                                  │
│  S — SINGLE RESPONSIBILITY                                       │
│  Each evaluator measures ONE thing                               │
│  ✅ CorrectnessEvaluator → only correctness                     │
│  ✅ PerformanceEvaluator → only performance                     │
│  ❌ "EverythingEvaluator" → too broad, unmaintainable           │
│                                                                  │
│  O — OPEN/CLOSED                                                 │
│  Open for new metrics, closed for modification                  │
│  ✅ Plugin architecture (add new metric without changing core)  │
│  ❌ Hardcoded metric list (requires code change to add metrics) │
│                                                                  │
│  L — LISKOV SUBSTITUTION                                         │
│  Any evaluator should work in any benchmark                     │
│  ✅ All evaluators implement Evaluator interface                │
│  ❌ Different evaluator types with incompatible APIs            │
│                                                                  │
│  I — INTERFACE SEGREGATION                                       │
│  Small, focused evaluation interfaces                           │
│  ✅ Separate: MetricCollector, ResultAggregator, Reporter       │
│  ❌ One giant EvaluationInterface with 30 methods               │
│                                                                  │
│  D — DEPENDENCY INVERSION                                        │
│  Depend on abstractions, not implementations                    │
│  ✅ Evaluator depends on IModelProvider, not OpenAI directly    │
│  ❌ Evaluator directly calls openai.completions()              │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 12.2 Evaluation Design Principles

```
┌──────────────────────────────────────────────────────────────────┐
│         EVALUATION DESIGN PRINCIPLES (10 Commandments)            │
│                                                                  │
│  1. THOU SHALL EVALUATE WHAT MATTERS                            │
│     → Don't measure easy things, measure important things       │
│     → Correctness > Token count > Response time                 │
│                                                                  │
│  2. THOU SHALL USE MULTIPLE METRICS                             │
│     → Single metric is always misleading                        │
│     → Balance: quality, speed, cost, safety                     │
│                                                                  │
│  3. THOU SHALL BENCHMARK REALISTICALLY                          │
│     → Use real-world tasks, not synthetic examples              │
│     → Include easy, medium, and hard tasks                      │
│                                                                  │
│  4. THOU SHALL CALIBRATE WITH HUMANS                            │
│     → Auto-eval must align with human judgment                  │
│     → Regular calibration checks (weekly/monthly)               │
│                                                                  │
│  5. THOU SHALL TRACK REGRESSIONS                                │
│     → Every evaluation run should compare to previous           │
│     → Alert on metric drops > threshold                         │
│                                                                  │
│  6. THOU SHALL MAKE IT REPRODUCIBLE                             │
│     → Fixed random seeds for deterministic results              │
│     → Version-controlled benchmark data                         │
│                                                                  │
│  7. THOU SHALL TRACK COST                                       │
│     → Token cost per quality point                              │
│     → ROI of model upgrades                                     │
│                                                                  │
│  8. THOU SHALL ITERATE                                          │
│     → Update benchmarks as codebase evolves                     │
│     → Add new test cases from production failures               │
│                                                                  │
│  9. THOU SHALL SHARE RESULTS                                    │
│     → Team visibility into evaluation outcomes                  │
│     → Build trust through transparency                          │
│                                                                  │
│  10. THOU SHALL EVALUATE THE EVALUATOR                          │
│      → Does your benchmark actually measure quality?            │
│      → Cross-validate with multiple evaluation approaches       │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 13. Testing Evaluation Harness

### 13.1 Evaluation Test Harness

```python
import time
import json
import statistics
from dataclasses import dataclass, field
from typing import Any, Callable, Dict, List, Optional
from enum import Enum


class EvalHarnessTestType(Enum):
    """Các loại test cho evaluation harness"""
    METRIC_ACCURACY = "metric_accuracy"       # Metric outputs correct values
    BENCHMARK_VALIDITY = "benchmark_validity"  # Benchmark has expected structure
    REPRODUCIBILITY = "reproducibility"        # Same input → same output
    COST_TRACKING = "cost_tracking"           # Costs are accurate
    REGRESSION_DETECTION = "regression_detection"  # Detects quality drops
    HUMAN_CALIBRATION = "human_calibration"    # Aligns with human judgment
    EDGE_CASE_HANDLING = "edge_case_handling"  # Handles edge cases


@dataclass
class EvalHarnessTest:
    """Một test case cho evaluation harness"""
    name: str
    test_type: EvalHarnessTestType
    description: str
    handler: Callable
    setup: Optional[Callable] = None
    teardown: Optional[Callable] = None
    timeout: int = 120
    tags: List[str] = field(default_factory=list)
    expected_result: Optional[Any] = None


class EvaluationTestHarness:
    """
    Harness để test evaluation systems itself.
    
    Features:
    - Metric accuracy verification
    - Benchmark validity checks
    - Reproducibility testing
    - Cost tracking validation
    - Regression detection testing
    - Human calibration checks
    """
    
    def __init__(self):
        self.tests: List[EvalHarnessTest] = []
        self.results: List[Dict] = []
    
    def register(self, test: EvalHarnessTest):
        """Register một eval harness test"""
        self.tests.append(test)
    
    def run_all(self) -> Dict:
        """Chạy toàn bộ eval harness tests"""
        self.results = []
        start_time = time.time()
        
        for test in self.tests:
            result = self._run_single(test)
            self.results.append(result)
        
        total_time = time.time() - start_time
        return self._generate_report(total_time)
    
    def run_by_type(self, test_type: EvalHarnessTestType) -> Dict:
        """Chạy tests theo type"""
        self.results = []
        start_time = time.time()
        
        for test in self.tests:
            if test.test_type == test_type:
                result = self._run_single(test)
                self.results.append(result)
        
        total_time = time.time() - start_time
        return self._generate_report(total_time)
    
    def _run_single(self, test: EvalHarnessTest) -> Dict:
        """Chạy một test case"""
        if test.setup:
            try:
                test.setup()
            except Exception as e:
                return {
                    "name": test.name,
                    "type": test.test_type.value,
                    "status": "setup_error",
                    "error": str(e),
                    "duration": 0,
                }
        
        start = time.time()
        try:
            result = test.handler()
            duration = time.time() - start
            
            passed = True
            if test.expected_result is not None:
                passed = result == test.expected_result
            
            return {
                "name": test.name,
                "type": test.test_type.value,
                "status": "passed" if passed else "failed",
                "duration": duration,
                "output": result,
                "expected": test.expected_result,
                "tags": test.tags,
            }
        except AssertionError as e:
            return {
                "name": test.name,
                "type": test.test_type.value,
                "status": "failed",
                "duration": time.time() - start,
                "error": str(e),
                "tags": test.tags,
            }
        except Exception as e:
            return {
                "name": test.name,
                "type": test.test_type.value,
                "status": "error",
                "duration": time.time() - start,
                "error": str(e),
                "tags": test.tags,
            }
        finally:
            if test.teardown:
                try:
                    test.teardown()
                except Exception:
                    pass
    
    def _generate_report(self, total_time: float) -> Dict:
        """Tạo test report"""
        passed = sum(1 for r in self.results if r["status"] == "passed")
        failed = sum(1 for r in self.results if r["status"] == "failed")
        errors = sum(1 for r in self.results if r["status"] == "error")
        
        return {
            "summary": {
                "total": len(self.results),
                "passed": passed,
                "failed": failed,
                "errors": errors,
                "pass_rate": f"{(passed / len(self.results) * 100):.1f}%",
                "total_duration": f"{total_time:.2f}s",
            },
            "by_type": self._group_by_type(),
            "results": self.results,
            "gate_passed": failed == 0 and errors == 0,
        }
    
    def _group_by_type(self) -> Dict:
        """Group results by test type"""
        groups: Dict[str, List] = {}
        for result in self.results:
            t = result["type"]
            if t not in groups:
                groups[t] = []
            groups[t].append(result)
        
        return {
            t: {
                "total": len(results),
                "passed": sum(1 for r in results if r["status"] == "passed"),
            }
            for t, results in groups.items()
        }


# ═══════════════════════════════════════════════════════════════
# Usage Example
# ═══════════════════════════════════════════════════════════════

def test_metric_accuracy():
    """Test that pass@k metric computes correctly"""
    evaluator = PassAtKEvaluator()
    # 3 correct out of 5 → pass@1 should be 0.6
    result = evaluator.compute(k=1, total=5, correct=3)
    assert abs(result - 0.6) < 0.01, f"Expected 0.6, got {result}"

def test_benchmark_reproducibility():
    """Test that running same benchmark twice gives same results"""
    bench = SimpleBenchmark(seed=42)
    result1 = bench.run(tasks=["task1", "task2"])
    result2 = bench.run(tasks=["task1", "task2"])
    assert result1 == result2, "Benchmark results not reproducible"

def test_cost_tracking():
    """Test that cost tracking is accurate"""
    tracker = CostTracker(model="gpt-4o")
    tracker.record(input_tokens=1000, output_tokens=500)
    assert tracker.total_cost > 0
    assert tracker.total_cost < 0.01  # Should be very small for this usage


# Register tests
harness = EvaluationTestHarness()

harness.register(EvalHarnessTest(
    name="Pass@K Accuracy",
    test_type=EvalHarnessTestType.METRIC_ACCURACY,
    description="Verify pass@k metric computation",
    handler=test_metric_accuracy,
    tags=["metric", "correctness"],
))

harness.register(EvalHarnessTest(
    name="Benchmark Reproducibility",
    test_type=EvalHarnessTestType.REPRODUCIBILITY,
    description="Verify benchmark produces consistent results",
    handler=test_benchmark_reproducibility,
    tags=["benchmark", "reliability"],
))

harness.register(EvalHarnessTest(
    name="Cost Tracking Accuracy",
    test_type=EvalHarnessTestType.COST_TRACKING,
    description="Verify cost tracking is accurate",
    handler=test_cost_tracking,
    tags=["cost", "tracking"],
))

# Run
# report = harness.run_all()
# print(json.dumps(report, indent=2))
```

---

## 14. Future Trends trong Evaluation

### 14.1 AI Evaluation Trends (2024-2026)

```
┌──────────────────────────────────────────────────────────────────┐
│              FUTURE EVALUATION TRENDS                             │
│                                                                  │
│  TREND 1: AUTOMATED HARNESS EVALUATION                          │
│  ├── Meta-evaluation: AI evaluates the evaluator                │
│  ├── Self-improving benchmarks that evolve with models          │
│  ├── Automatic detection of benchmark contamination             │
│  └── Quality signals from production usage data                 │
│                                                                  │
│  TREND 2: REAL-TIME EVALUATION                                   │
│  ├── Live evaluation during coding (not just post-hoc)          │
│  ├── Instant feedback loops on code quality                     │
│  ├── Predictive evaluation (estimate quality before running)    │
│  └── Continuous evaluation dashboards                           │
│                                                                  │
│  TREND 3: COST-AWARE EVALUATION                                 │
│  ├── Quality-per-dollar as primary metric                       │
│  ├── Model routing based on task difficulty                     │
│  ├── Token budget optimization                                  │
│  └── ROI measurement for AI tooling investments                │
│                                                                  │
│  TREND 4: HUMAN-AI CALIBRATED EVALUATION                        │
│  ├── LLM-as-judge calibrated against human experts              │
│  ├── Multi-rater consensus systems                              │
│  ├── Bias detection in automated evaluation                     │
│  └── Fair comparison across model families                      │
│                                                                  │
│  TREND 5: DOMAIN-SPECIFIC BENCHMARKS                            │
│  ├── Security-focused evaluation (OWASP patterns)               │
│  ├── Performance-focused evaluation (latency, memory)           │
│  ├── Accessibility-focused evaluation (WCAG compliance)         │
│  └── Industry-specific benchmarks (healthcare, finance)         │
│                                                                  │
│  TREND 6: EVALUATION-AS-CODE                                     │
│  ├── Version-controlled evaluation suites                       │
│  ├── CI/CD integrated evaluation pipelines                      │
│  ├── Evaluation results as PR comments                          │
│  └── Automated evaluation gates for model upgrades              │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## Tài Liệu Tham Khảo

### Papers & Research
- [HumanEval Benchmark](https://github.com/openai/human-eval) — OpenAI code generation benchmark
- [SWE-bench](https://www.swebench.com/) — Princeton NLP real-world bug fix benchmark
- [CodeBLEU](https://github.com/microsoft/CodeXGLUE) — Microsoft code evaluation metric
- [Evaluating Large Language Models Trained on Code](https://arxiv.org/abs/2107.03374) — Codex paper

### Frameworks & Tools
- [PromptFoo](https://www.promptfoo.dev) — Prompt testing & evaluation
- [DeepEval](https://docs.confident-ai.com) — LLM evaluation framework
- [RAGAS](https://docs.ragas.io) — RAG evaluation
- [LangSmith](https://smith.langchain.com) — Tracing & evaluation
- [Weights & Biases](https://wandb.ai) — Experiment tracking

### Benchmarks
- [SWE-bench Leaderboard](https://www.swebench.com/)
- [LiveCodeBench](https://livecodebench.github.io/)
- [BigCodeBench](https://bigcode-benchmark.github.io/)
- [Aider LLM Leaderboard](https://aider.chat/docs/leaderboards/)

### Case Study References
- [SWE-agent](https://github.com/princeton-nlp/SWE-agent)
- [Amazon Q Developer](https://aws.amazon.com/q/developer/)
- [Aider](https://aider.chat)
- [Anthropic Claude Code](https://docs.anthropic.com/en/docs/claude-code)

---

**Ngày cập nhật:** 19/07/2026  
**Tác giả:** AI Knowledge Repository
