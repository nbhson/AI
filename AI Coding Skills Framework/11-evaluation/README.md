# 📊 XI. Evaluation

## Tổng Quan

**Evaluation** trong AI coding là quá trình **đánh giá hiệu quả và chất lượng** của AI agent khi thực hiện các tác vụ coding. Evaluation bao gồm benchmarking, metrics collection, và continuous improvement dựa trên kết quả đánh giá.

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
            Rating.PARTIAL: "Partially working, key issues remain",
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
            Rating.PARTIAL: "Messy but understandable",
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
            Rating.PARTIAL: "Performance concerns for scale",
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
            Rating.PARTIAL: "Minimal error handling",
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
            Rating.PARTIAL: "Partially completed",
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
            Rating.PARTIAL: "High token usage, slow",
            Rating.FAILED: "Extremely inefficient",
        }
    ),
]
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
    "hello_world": {
        "id": "basic-001",
        "description": "Create a hello world function",
        "difficulty": "trivial",
        "expected": "Hello, World!",
    },
    
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
    
    "api_endpoint": {
        "id": "web-001",
        "description": "Create a REST API endpoint for CRUD",
        "difficulty": "moderate",
        "validator": lambda out: True,  # Manual review
    },
    
    "bug_fix": {
        "id": "debug-001",
        "description": "Fix off-by-one error in loop",
        "difficulty": "easy",
        "validator": lambda out: True,
    },
    
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
    
    "security_audit": {
        "id": "security-001",
        "description": "Find and fix SQL injection vulnerabilities",
        "difficulty": "moderate",
        "validator": lambda out: True,
    },
}
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
```

---

## Best Practices

```
┌──────────────────────────────────────────────────────────────────┐
│              EVALUATION BEST PRACTICES                           │
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
└──────────────────────────────────────────────────────────────────┘
```

---

## Tài Liệu Tham Khảo

- [HumanEval Benchmark](https://github.com/openai/human-eval)
- [SWE-bench](https://www.swebench.com/)
- [CodeBLEU](https://github.com/microsoft/CodeXGLUE)