# 🔄 XII. Loop Engineering

> **"Hệ thống không có feedback loop giống như con tàu không có la bàn — có thể đi nhanh, nhưng không biết mình đang đi đâu."**

### Câu Chuyện Mở Đầu

Bạn thuê một đầu bếp mới. Ngày đầu tiên, anh ta nấu món — bạn nếm, nói *"Quá mặn"*. Ngày hôm sau, anh ta nấu lại — bạn nếm, nói *"Ít mặn hơn nhưng thiếu ngọt"*. Ngày thứ ba, món ăn gần như hoàn hảo.

Quy trình **"nấu → nếm → phản hồi → nấu lại"** chính là **Feedback Loop** — và nó cũng chính là **bản chất của Loop Engineering**.

**AI Agent cũng cần những vòng lặp như vậy.** Khi agent viết code, nó cần:
1. **Think** — Phân tích và lên kế hoạch
2. **Act** — Viết code / thực hiện hành động
3. **Observe** — Kiểm tra kết quả (test, lint, review)
4. **Learn** — Điều chỉnh dựa trên kết quả

Nhưng trong thực tế, nhiều hệ thống AI chỉ dừng ở bước 1-2: **viết code rồi hy vọng** — không có vòng lặp tự cải thiện. Kết quả? Code có lỗi, agent lặp lại cùng một sai lầm, và người dùng phải interven manually.

**Giải pháp**: Loop Engineering — kỹ thuật thiết kế các **vòng lặp có cấu trúc** để AI agent tự học, tự sửa, và tự cải thiện liên tục.

### Tại Sao Loop Engineering Quan Trọng?

> *"Kẻ chiến thắng không phải là người không bao giờ thất bại — mà là người không bao giờ thất bại 2 lần vì cùng một lý do."*

#### 3 Bằng Chứng Khoa Học

| # | Nghiên Cứu | Phát Hiện Quan Trọng |
|---|-----------|----------------------|
| 1 | **DeepMind (2025)** | Agents với structured feedback loops giảm **52% lỗi lặp lại** so với agents không có loop |
| 2 | **Anthropic (2025)** | Self-refine loops trong Claude Code tăng **38% code quality** trên benchmark SWE-bench |
| 3 | **Stanford HAI (2026)** | Iterative evaluation loops giúp coding agents tiết kiệm **40% token** bằng cách phát hiện lỗi sớm trong inner loop thay vì outer loop |

#### Triết lý cốt lõi:

```
Loop Engineering = Vòng lặp có cấu trúc → Tự cải thiện liên tục → Kết quả tốt hơn mỗi lần
```

**2 Loại Loop cơ bản:**

```
┌─────────────────────────────────────────────────────────────┐
│                    INNER LOOP                                │
│         (Agent tự cải thiện trong 1 task)                    │
│                                                              │
│    Think ──► Act ──► Observe ──► Reflect                     │
│      ▲                                    │                  │
│      └────────────────────────────────────┘                  │
│                                                              │
│    Think → Write code → Run test → Fix if fail               │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                    OUTER LOOP                                │
│         (Hệ thống cải thiện qua nhiều tasks)                 │
│                                                              │
│    Task 1 ──► Task 2 ──► Task 3 ──► Task N                 │
│      │            │            │            │                 │
│      ▼            ▼            ▼            ▼                 │
│    Feedback    Feedback    Feedback    Feedback              │
│      │            │            │            │                 │
│      └────────────┴────────────┴────┬───────┘                │
│                                     ▼                         │
│                            LEARNING & OPTIMIZATION            │
└─────────────────────────────────────────────────────────────┘
```

**Analogies**: Loop Engineering giống hệ thống miễn dịch của cơ thể — Inner Loop là phản ứng tức thì khi vi khuẩn xâm nhập (viêm, sốt), Outer Loop là hệ thống miễn dịch thích ứng (tạo kháng thể mới, tăng cường miễn dịch).

**Nếu bỏ qua**: Agent lặp lại cùng một lỗi → lãng phí token → code quality giảm → người dùng mất niềm tin.

## Tổng Quan

**Loop Engineering** là kỹ thuật **thiết kế và quản lý các vòng lặp (loops)** trong hệ thống AI agent — bao gồm feedback loops, retry loops, evaluation loops, và self-improvement loops. Đây là **nền tảng cho khả năng tự cải thiện** của agent: từ inner loop (sửa lỗi trong 1 task) đến outer loop (học hỏi qua nhiều tasks).

> **"Sự tiến hóa không xảy ra một lần — nó xảy ra qua hàng triệu thế hệ. AI cũng vậy: nó tiến hóa qua hàng triệu feedback loops."**

```
┌──────────────────────────────────────────────────────────────────┐
│                    LOOP ENGINEERING                               │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    INNER LOOPS                            │   │
│  │                                                          │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌────────┐  │   │
│  │  │ Reasoning │  │ Execution│  │Validation│  │Reflect │  │   │
│  │  │   Loop    │  │   Loop   │  │   Loop   │  │  Loop  │  │   │
│  │  └────┬─────┘  └────┬─────┘  └────┬─────┘  └───┬────┘  │   │
│  │       └──────────────┼──────────────┼────────────┘       │   │
│  │                      ▼                                    │   │
│  │              ┌───────────────┐                            │   │
│  │              │   SELF-REFINE │                            │   │
│  │              └───────────────┘                            │   │
│  └──────────────────────────────────────────────────────────┘   │
│       │                                                          │
│       ▼                                                          │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    OUTER LOOPS                            │   │
│  │                                                          │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌────────┐  │   │
│  │  │ Feedback │  │ Learning │  │Optimization│ │ Adapt  │  │   │
│  │  │  Loop    │  │   Loop   │  │   Loop    │  │  Loop  │  │   │
│  │  └────┬─────┘  └────┬─────┘  └────┬─────┘  └───┬────┘  │   │
│  │       └──────────────┼──────────────┼────────────┘       │   │
│  │                      ▼                                    │   │
│  │              ┌───────────────┐                            │   │
│  │              │   CONTINUOUS  │                            │   │
│  │              │  IMPROVEMENT  │                            │   │
│  │              └───────────────┘                            │   │
│  └──────────────────────────────────────────────────────────┘   │
│       │                                                          │
│       ▼                                                          │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │  HARNESS INTEGRATION                                       │  │
│  │  Memory ←→ Guardrails ←→ Tools ←→ Evaluation ←→ Workflow  │  │
│  └────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────┘
```

## Nội Dung

| # | Chủ đề | Mô tả |
|---|--------|-------|
| 1 | [Loop Taxonomy](#1-loop-taxonomy) | Phân loại các loại loop trong AI Agent |
| 2 | [Inner Loop — Reasoning & Self-Refine](#2-inner-loop--reasoning--self-refine) | Think → Act → Observe → Reflect |
| 3 | [Execution Loop — Retry & Error Recovery](#3-execution-loop--retry--error-recovery) | Retry patterns, circuit breaker, adaptive retry |
| 4 | [Validation Loop — Test & Verify](#4-validation-loop--test--verify) | Write → Test → Fix → Re-test |
| 5 | [Feedback Loop — Learn from Results](#5-feedback-loop--learn-from-results) | Metrics, patterns, auto-optimization |
| 6 | [Outer Loop — Continuous Improvement](#6-outer-loop--continuous-improvement) | Learning across tasks, prompt evolution |
| 7 | [Self-Improvement Patterns](#7-self-improvement-patterns) | Self-consistency, self-reflection, meta-learning |
| 8 | [Loop Orchestration Engine](#8-loop-orchestration-engine) | Tổng hợp tất cả loops |
| 9 | [Case Studies](#9-case-studies) | Claude Code, Cursor, Devin |
| 10 | [Loop Anti-Patterns](#10-loop-anti-patterns) | Các lỗi thường gặp và cách tránh |
| 11 | [Best Practices](#11-best-practices) | Nguyên tắc thiết kế loops |
| 12 | [Tương Lai](#12-tương-lai) | Xu hướng 2026-2028 |

---

## 1. Loop Taxonomy

### 1.1 Phân Loại Các Loại Loop

Trong AI Agent, có **4 loại loop chính**, phân theo **scope** và **tần suất**:

```
┌─────────────────────────────────────────────────────────────────────┐
│                     LOOP TAXONOMY                                    │
│                                                                     │
│  Scope: RỘNG ───────────────────────────────────────────────► HẸP  │
│                                                                     │
│  ┌─────────────┐  ┌──────────────┐  ┌────────────┐  ┌──────────┐  │
│  │   OUTER     │  │   LEARNING   │  │  EXECUTION │  │  INNER   │  │
│  │    LOOP     │  │    LOOP      │  │    LOOP    │  │   LOOP   │  │
│  │             │  │              │  │            │  │          │  │
│  │ Cross-task  │  │ Per-task     │  │ Per-action │  │ Per-step │  │
│  │ improvement │  │ adaptation   │  │ recovery   │  │ reasoning│  │
│  │             │  │              │  │            │  │          │  │
│  │ Weeks/Months│  │ Hours/Days   │  │ Seconds    │  │ ms       │  │
│  └─────────────┘  └──────────────┘  └────────────┘  └──────────┘  │
│                                                                     │
│  Tần suất: ÍT ─────────────────────────────────────────────► NHIỀU │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2 Bảng So Sánh Chi Tiết

| Loop Type | Scope | Tần suất | Mục tiêu | Ví dụ |
|-----------|-------|----------|----------|-------|
| **Inner Loop** | 1 step | ms | Think → Act → Reflect | Chain-of-Thought, self-check |
| **Execution Loop** | 1 action | seconds | Execute → Retry → Succeed | Retry with backoff, circuit breaker |
| **Learning Loop** | 1 task | minutes/hours | Execute → Evaluate → Adapt | Self-refine, test-driven iteration |
| **Outer Loop** | N tasks | days/weeks | Learn → Optimize → Evolve | Prompt versioning, metric tracking |

### 1.3 Mối Quan Hệ Giữa Các Loop

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  OUTER LOOP (Cross-task)                                        │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                                                           │  │
│  │  LEARNING LOOP (Per-task)                                 │  │
│  │  ┌─────────────────────────────────────────────────────┐  │  │
│  │  │                                                     │  │  │
│  │  │  EXECUTION LOOP (Per-action)                        │  │  │
│  │  │  ┌───────────────────────────────────────────────┐  │  │  │
│  │  │  │                                               │  │  │  │
│  │  │  │  INNER LOOP (Per-step reasoning)              │  │  │  │
│  │  │  │  Think ──► Act ──► Observe ──► Reflect        │  │  │  │
│  │  │  │     ▲                              │          │  │  │  │
│  │  │  │     └──────────────────────────────┘          │  │  │  │
│  │  │  │                                               │  │  │  │
│  │  │  │  Write ──► Test ──► Fix ──► Re-test           │  │  │  │
│  │  │  │     ▲                              │          │  │  │  │
│  │  │  │     └──────────────────────────────┘          │  │  │  │
│  │  │  └───────────────────────────────────────────────┘  │  │  │
│  │  │                                                     │  │  │
│  │  │  Execute ──► Evaluate ──► Adapt ──► Retry/Complete  │  │  │
│  │  │     ▲                                         │      │  │  │
│  │  │     └─────────────────────────────────────────┘      │  │  │
│  │  └─────────────────────────────────────────────────────┘  │  │
│  │                                                           │  │
│  │  Task 1 ──► Task 2 ──► Task 3 ──► ... ──► Optimize       │  │
│  │     │            │            │                 │          │  │
│  │     ▼            ▼            ▼                 ▼          │  │
│  │  Metrics      Patterns    Prompts           Framework     │  │
│  │                                                           │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 2. Inner Loop — Reasoning & Self-Refine

Inner Loop là vòng lặp **nhanh nhất và cơ bản nhất** — xảy ra trong quá trình suy nghĩ của AI. Agent "nói chuyện với chính mình" trước khi hành động.

### 2.1 Chain-of-Thought Loop (Think → Act → Reflect)

```python
from dataclasses import dataclass, field
from typing import Any, Callable, Dict, List, Optional
import time


@dataclass
class Thought:
    """Một bước suy nghĩ trong Inner Loop"""
    step: int
    content: str
    confidence: float  # 0.0 - 1.0
    alternatives: List[str] = field(default_factory=list)
    timestamp: str = ""


@dataclass
class Reflection:
    """Phản hồi sau khi quan sát kết quả"""
    assessment: str
    should_continue: bool
    adjustments: List[str] = field(default_factory=list)
    score: float = 0.0  # 0.0 - 1.0


class InnerReasoningLoop:
    """
    Inner Loop: Think → Act → Observe → Reflect
    
    Agent suy nghĩ trước khi hành động, quan sát kết quả,
    và phản hồi để điều chỉnh.
    """
    
    def __init__(self, max_reflections: int = 3, confidence_threshold: float = 0.7):
        self.max_reflections = max_reflections
        self.confidence_threshold = confidence_threshold
        self.thoughts: List[Thought] = []
        self.reflections: List[Reflection] = []
        self.iteration = 0
    
    def think(self, task: str, context: Dict) -> Thought:
        """Agent suy nghĩ về task trước khi hành động"""
        # Phân tích task, xem xét context
        alternatives = self._generate_alternatives(task, context)
        best = self._select_best(alternatives)
        
        thought = Thought(
            step=self.iteration,
            content=best,
            confidence=self._assess_confidence(task, best, context),
            alternatives=alternatives,
            timestamp=time.strftime("%Y-%m-%d %H:%M:%S"),
        )
        self.thoughts.append(thought)
        return thought
    
    def act(self, thought: Thought, executor: Callable) -> Any:
        """Thực hiện hành động dựa trên suy nghĩ"""
        return executor(thought.content)
    
    def observe(self, action_result: Any, expected: Any = None) -> Dict:
        """Quan sát kết quả của hành động"""
        if expected is not None:
            match = action_result == expected
        else:
            match = action_result is not None and "error" not in str(action_result).lower()
        
        return {
            "result": action_result,
            "match": match,
            "observation": f"Result {'matches' if match else 'does not match'} expectation",
        }
    
    def reflect(self, thought: Thought, observation: Dict) -> Reflection:
        """Phản hồi dựa trên kết quả quan sát"""
        if observation["match"]:
            return Reflection(
                assessment="Action succeeded as expected",
                should_continue=False,
                score=thought.confidence,
            )
        
        return Reflection(
            assessment=f"Action failed: {observation['observation']}",
            should_continue=True,
            adjustments=[
                f"Reconsider approach (confidence was {thought.confidence:.2f})",
                f"Current thought: {thought.content[:100]}...",
            ],
            score=thought.confidence * 0.5,
        )
    
    def run(self, task: str, context: Dict, executor: Callable, 
            expected: Any = None) -> Dict:
        """Chạy Inner Loop hoàn chỉnh"""
        self.iteration = 0
        
        for i in range(self.max_reflections):
            self.iteration = i
            
            # Think
            thought = self.think(task, context)
            
            # Confidence check — skip act if very confident
            if thought.confidence >= self.confidence_threshold and i > 0:
                break
            
            # Act
            result = self.act(thought, executor)
            
            # Observe
            observation = self.observe(result, expected)
            
            # Reflect
            reflection = self.reflect(thought, observation)
            self.reflections.append(reflection)
            
            if not reflection.should_continue:
                break
            
            # Update context with reflection for next iteration
            context["previous_reflection"] = reflection.assessment
        
        return {
            "iterations": len(self.thoughts),
            "final_thought": self.thoughts[-1] if self.thoughts else None,
            "reflections": len(self.reflections),
            "success": not self.reflections or not self.reflections[-1].should_continue,
        }
    
    def _generate_alternatives(self, task: str, context: Dict) -> List[str]:
        """Generate alternative approaches"""
        return [f"Approach A for {task}", f"Approach B for {task}"]
    
    def _select_best(self, alternatives: List[str]) -> str:
        return alternatives[0] if alternatives else ""
    
    def _assess_confidence(self, task: str, approach: str, context: Dict) -> float:
        base = 0.5
        if context.get("previous_reflection"):
            base *= 0.7  # Decrease after failure
        return min(base, 1.0)
```

### 2.2 Self-Refine Pattern

Self-Refine là pattern mà **LLM tự cải thiện output của chính mình** qua nhiều vòng lặp:

```python
class SelfRefineLoop:
    """
    Self-Refine: Generate → Critique → Refine → Repeat
    
    Agent tạo output, tự phê bình, và cải thiện.
    """
    
    def __init__(self, max_rounds: int = 3):
        self.max_rounds = max_rounds
        self.history: List[Dict] = []
    
    def generate(self, task: str, context: Dict, 
                 previous_output: str = None, 
                 previous_critique: str = None) -> str:
        """Generate or refine output"""
        if previous_output is None:
            prompt = f"Task: {task}\nContext: {context}\n\nGenerate the best response."
        else:
            prompt = (
                f"Task: {task}\n\n"
                f"Previous output:\n{previous_output}\n\n"
                f"Critique:\n{previous_critique}\n\n"
                f"Please improve the output based on the critique."
            )
        
        # In real implementation, this calls LLM
        return f"Generated output for: {task}"
    
    def critique(self, task: str, output: str, context: Dict) -> Dict:
        """Critique the current output"""
        prompt = (
            f"Task: {task}\n\n"
            f"Output to critique:\n{output}\n\n"
            f"Provide specific, actionable feedback for improvement."
        )
        
        # In real implementation, this calls LLM
        return {
            "quality_score": 0.6,
            "strengths": ["Clear structure"],
            "weaknesses": ["Missing edge cases", "No error handling"],
            "suggestions": ["Add try-catch blocks", "Handle null inputs"],
            "should_refine": True,  # Continue if quality < threshold
        }
    
    def run(self, task: str, context: Dict) -> Dict:
        """Run Self-Refine loop"""
        current_output = None
        current_critique = None
        
        for round_num in range(self.max_rounds):
            # Generate
            current_output = self.generate(task, context, current_output, current_critique)
            
            # Critique
            critique_result = self.critique(task, current_output, context)
            
            self.history.append({
                "round": round_num + 1,
                "output_preview": current_output[:200],
                "quality_score": critique_result["quality_score"],
                "weaknesses": critique_result["weaknesses"],
            })
            
            # Check if should stop
            if not critique_result["should_refine"] or critique_result["quality_score"] >= 0.9:
                break
            
            current_critique = "\n".join(critique_result["suggestions"])
        
        return {
            "final_output": current_output,
            "total_rounds": len(self.history),
            "quality_progression": [h["quality_score"] for h in self.history],
            "history": self.history,
        }
```

### 2.3 Metacognitive Loop (Think About Thinking)

```python
class MetacognitiveLoop:
    """
    Metacognitive Loop: Agent suy nghĩ về quá trình suy nghĩ của mình.
    
    Kiểm tra: "Có phải tôi đang suy nghĩ sai hướng không?"
    """
    
    def __init__(self):
        self.strategies_used: List[str] = []
        self.strategy_scores: Dict[str, List[float]] = {}
    
    def select_strategy(self, task: Dict) -> str:
        """Chọn chiến lược dựa trên kinh nghiệm"""
        task_type = task.get("type", "general")
        
        # Check if we have history for this task type
        if task_type in self.strategy_scores:
            avg_scores = {
                strategy: sum(scores) / len(scores)
                for strategy, scores in self.strategy_scores.items()
                if strategy.startswith(task_type)
            }
            if avg_scores:
                best_strategy = max(avg_scores, key=avg_scores.get)
                return best_strategy
        
        return f"default_strategy_for_{task_type}"
    
    def evaluate_strategy(self, strategy: str, result: Dict):
        """Đánh giá chiến lược đã dùng"""
        score = 1.0 if result.get("success") else 0.0
        
        if strategy not in self.strategy_scores:
            self.strategy_scores[strategy] = []
        self.strategy_scores[strategy].append(score)
    
    def should_switch_strategy(self, task_type: str) -> bool:
        """Kiểm tra có nên chuyển chiến lược không"""
        recent_scores = []
        for strategy, scores in self.strategy_scores.items():
            if strategy.startswith(task_type) and len(scores) >= 2:
                recent_scores.extend(scores[-3:])
        
        if not recent_scores:
            return False
        
        avg = sum(recent_scores) / len(recent_scores)
        return avg < 0.5  # Switch if average score is low
```

---

## 3. Execution Loop — Retry & Error Recovery

Execution Loop xử lý các lỗi trong quá trình thực thi — đảm bảo agent không bỏ cuộc quá sớm nhưng cũng không retry vô hạn.

### 3.1 Adaptive Retry Engine

```python
import time
import random
from enum import Enum


class RetryStrategy(Enum):
    FIXED = "fixed"
    EXPONENTIAL = "expponential"
    LINEAR = "linear"
    ADAPTIVE = "adaptive"


class AdaptiveRetryEngine:
    """
    Retry Engine thông minh — tự điều chỉnh retry strategy
    dựa trên loại lỗi và lịch sử retry.
    """
    
    def __init__(self, max_retries: int = 5, strategy: RetryStrategy = RetryStrategy.ADAPTIVE):
        self.max_retries = max_retries
        self.strategy = strategy
        self.retry_history: list = []
        self.error_patterns: dict = {}
    
    def calculate_delay(self, attempt: int, error_type: str) -> float:
        """Tính thời gian chờ giữa các retry"""
        base_delay = 1.0
        
        if self.strategy == RetryStrategy.FIXED:
            return base_delay
        
        elif self.strategy == RetryStrategy.EXPONENTIAL:
            return base_delay * (2 ** attempt)
        
        elif self.strategy == RetryStrategy.LINEAR:
            return base_delay * (attempt + 1)
        
        elif self.strategy == RetryStrategy.ADAPTIVE:
            # Adaptive: increase more for repeated errors
            repeat_count = self.error_patterns.get(error_type, 0)
            multiplier = 1.0 + (repeat_count * 0.5)
            return base_delay * (2 ** attempt) * multiplier
        
        return base_delay
    
    def should_retry(self, error: Exception, attempt: int) -> bool:
        """Quyết định có nên retry không"""
        error_type = type(error).__name__
        
        # Never retry on permanent errors
        permanent_errors = {"PermissionError", "SyntaxError", "ValueError"}
        if error_type in permanent_errors:
            return False
        
        # Track error pattern
        self.error_patterns[error_type] = self.error_patterns.get(error_type, 0) + 1
        
        # If same error occurred too many times, stop
        if self.error_patterns[error_type] > 3:
            return False
        
        return attempt < self.max_retries
    
    def execute_with_retry(self, func, *args, **kwargs):
        """Execute function with adaptive retry"""
        last_error = None
        
        for attempt in range(self.max_retries + 1):
            try:
                result = func(*args, **kwargs)
                # Success — record
                self.retry_history.append({
                    "attempt": attempt,
                    "success": True,
                })
                return result
            
            except Exception as e:
                last_error = e
                
                if not self.should_retry(e, attempt):
                    break
                
                delay = self.calculate_delay(attempt, type(e).__name__)
                self.retry_history.append({
                    "attempt": attempt,
                    "success": False,
                    "error": str(e),
                    "delay": delay,
                })
                time.sleep(delay)
        
        raise last_error
    
    def get_stats(self) -> dict:
        total = len(self.retry_history)
        successes = sum(1 for r in self.retry_history if r["success"])
        return {
            "total_attempts": total,
            "immediate_successes": successes,
            "retries_needed": total - successes,
            "error_patterns": dict(self.error_patterns),
        }
```

### 3.2 Circuit Breaker Loop

```python
class CircuitState(Enum):
    CLOSED = "closed"      # Normal — calls go through
    OPEN = "open"          # Broken — calls blocked
    HALF_OPEN = "half_open"  # Testing — 1 probe call allowed


class CircuitBreakerLoop:
    """
    Circuit Breaker — ngăn agent gọi tool/service đang lỗi liên tục.
    
    State machine:
    CLOSED (normal) → OPEN (too many failures) → HALF_OPEN (test) → CLOSED/OPEN
    """
    
    def __init__(self, failure_threshold: int = 5, recovery_timeout: float = 30.0):
        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout
        self.state = CircuitState.CLOSED
        self.failure_count = 0
        self.success_count = 0
        self.last_failure_time = 0.0
        self.state_history: list = []
    
    def call(self, func, *args, **kwargs):
        """Execute through circuit breaker"""
        self._check_state_transition()
        
        if self.state == CircuitState.OPEN:
            remaining = self._time_until_half_open()
            raise CircuitBreakerOpenError(
                f"Circuit OPEN. Retry in {remaining:.0f}s"
            )
        
        try:
            result = func(*args, **kwargs)
            self._on_success()
            return result
        except Exception as e:
            self._on_failure()
            raise
    
    def _check_state_transition(self):
        """Check if we should transition states"""
        if self.state == CircuitState.OPEN:
            if time.time() - self.last_failure_time > self.recovery_timeout:
                self._transition_to(CircuitState.HALF_OPEN)
    
    def _on_success(self):
        if self.state == CircuitState.HALF_OPEN:
            self.success_count += 1
            if self.success_count >= 2:
                self._transition_to(CircuitState.CLOSED)
                self.failure_count = 0
        else:
            self.failure_count = 0
    
    def _on_failure(self):
        self.failure_count += 1
        self.last_failure_time = time.time()
        self.success_count = 0
        
        if self.failure_count >= self.failure_threshold:
            self._transition_to(CircuitState.OPEN)
    
    def _transition_to(self, new_state: CircuitState):
        old = self.state.value
        self.state = new_state
        self.state_history.append({
            "from": old,
            "to": new_state.value,
            "timestamp": time.strftime("%Y-%m-%d %H:%M:%S"),
        })
    
    def _time_until_half_open(self) -> float:
        return max(0, self.recovery_timeout - (time.time() - self.last_failure_time))


class CircuitBreakerOpenError(Exception):
    pass
```

### 3.3 Timeout Loop

```python
class TimeoutLoop:
    """
    Timeout Loop — đảm bảo mỗi retry không chạy vô hạn.
    Kết hợp retry với timeout để tạo execution loop an toàn.
    """
    
    def __init__(self, max_retries: int = 3, timeout_seconds: float = 30.0):
        self.max_retries = max_retries
        self.timeout = timeout_seconds
    
    def execute_with_timeout(self, func, *args, **kwargs):
        """Execute với retry + timeout"""
        import signal
        
        def timeout_handler(signum, frame):
            raise TimeoutError("Execution timed out")
        
        last_error = None
        
        for attempt in range(self.max_retries):
            # Set timeout
            old_handler = signal.signal(signal.SIGALRM, timeout_handler)
            signal.alarm(int(self.timeout))
            
            try:
                result = func(*args, **kwargs)
                signal.alarm(0)  # Cancel alarm
                return result
            except TimeoutError:
                last_error = TimeoutError(
                    f"Attempt {attempt + 1}: timed out after {self.timeout}s"
                )
            except Exception as e:
                last_error = e
            finally:
                signal.signal(signal.SIGALRM, old_handler)
                signal.alarm(0)
        
        raise last_error
```

---

## 4. Validation Loop — Test & Verify

Validation Loop đảm bảo output của agent **thực sự hoạt động** trước khi accepted.

### 4.1 Test-Driven Loop

```python
class TestDrivenLoop:
    """
    Test-Driven Loop: Write → Test → Fix → Re-test
    
    Agent viết code, chạy test, sửa lỗi nếu có, lặp lại.
    """
    
    def __init__(self, max_fix_rounds: int = 5):
        self.max_fix_rounds = max_fix_rounds
        self.rounds: list = []
    
    def write_code(self, task: str, context: dict, 
                   previous_code: str = None, 
                   errors: list = None) -> str:
        """Viết code hoặc fix code"""
        if previous_code is None:
            prompt = f"Write code for: {task}"
        else:
            prompt = (
                f"Fix the following code. Errors:\n{errors}\n\n"
                f"Code:\n{previous_code}\n\n"
                f"Provide the corrected version."
            )
        
        # In real implementation: calls LLM
        return f"# Code for {task}"
    
    def run_tests(self, code: str, test_suite: list) -> dict:
        """Chạy test suite trên code"""
        passed = 0
        failed = 0
        failures = []
        
        for test in test_suite:
            try:
                # In real implementation: actually run tests
                result = True  # placeholder
                if result:
                    passed += 1
                else:
                    failed += 1
                    failures.append(f"Test {test}: failed")
            except Exception as e:
                failed += 1
                failures.append(f"Test {test}: {str(e)}")
        
        return {
            "total": len(test_suite),
            "passed": passed,
            "failed": failed,
            "success_rate": passed / len(test_suite) if test_suite else 0,
            "failures": failures,
            "all_passed": failed == 0,
        }
    
    def run(self, task: str, context: dict, test_suite: list) -> dict:
        """Run complete Test-Driven Loop"""
        current_code = None
        current_errors = None
        
        for round_num in range(self.max_fix_rounds):
            # Write/Fix
            code = self.write_code(task, context, current_code, current_errors)
            
            # Test
            test_result = self.run_tests(code, test_suite)
            
            self.rounds.append({
                "round": round_num + 1,
                "code_preview": code[:100],
                "tests_passed": test_result["passed"],
                "tests_failed": test_result["failed"],
                "success_rate": test_result["success_rate"],
            })
            
            if test_result["all_passed"]:
                return {
                    "success": True,
                    "final_code": code,
                    "total_rounds": round_num + 1,
                    "rounds": self.rounds,
                }
            
            # Prepare for fix
            current_code = code
            current_errors = test_result["failures"]
        
        return {
            "success": False,
            "final_code": current_code,
            "total_rounds": self.max_fix_rounds,
            "remaining_errors": current_errors,
            "rounds": self.rounds,
        }
```

### 4.2 Lint → Fix Loop

```python
class LintFixLoop:
    """
    Lint-Fix Loop: Write → Lint → Fix → Re-lint
    
    Tự động sửa lint errors cho đến khi clean.
    """
    
    def __init__(self, max_rounds: int = 5):
        self.max_rounds = max_rounds
    
    def lint(self, code: str) -> dict:
        """Chạy linter trên code"""
        errors = []
        
        # Check for common lint issues
        lines = code.split("\n")
        for i, line in enumerate(lines, 1):
            if len(line) > 120:
                errors.append({
                    "line": i,
                    "rule": "line-length",
                    "message": f"Line too long ({len(line)} > 120)",
                })
            if line.endswith(" "):
                errors.append({
                    "line": i,
                    "rule": "trailing-whitespace",
                    "message": "Trailing whitespace",
                })
        
        return {
            "error_count": len(errors),
            "errors": errors,
            "clean": len(errors) == 0,
        }
    
    def fix(self, code: str, lint_errors: list) -> str:
        """Fix lint errors"""
        lines = code.split("\n")
        
        for error in lint_errors:
            rule = error["rule"]
            line_idx = error["line"] - 1
            
            if 0 <= line_idx < len(lines):
                if rule == "line-length":
                    # Break long line
                    lines[line_idx] = lines[line_idx][:120]
                elif rule == "trailing-whitespace":
                    lines[line_idx] = lines[line_idx].rstrip()
        
        return "\n".join(lines)
    
    def run(self, code: str) -> dict:
        """Run lint-fix loop"""
        rounds = []
        current_code = code
        
        for round_num in range(self.max_rounds):
            lint_result = self.lint(current_code)
            
            rounds.append({
                "round": round_num + 1,
                "errors": lint_result["error_count"],
            })
            
            if lint_result["clean"]:
                return {
                    "success": True,
                    "final_code": current_code,
                    "total_rounds": round_num + 1,
                    "rounds": rounds,
                }
            
            current_code = self.fix(current_code, lint_result["errors"])
        
        return {
            "success": False,
            "final_code": current_code,
            "remaining_errors": lint_result["error_count"],
            "rounds": rounds,
        }
```

---

## 5. Feedback Loop — Learn from Results

Feedback Loop là **xương sống** của Loop Engineering — kết nối kết quả hành động quá khứ với hành động tương lai.

### 5.1 Metrics Feedback Loop

```python
from dataclasses import dataclass, field
from typing import Dict, List


@dataclass
class TaskMetrics:
    """Metrics cho 1 task"""
    task_type: str
    duration_seconds: float
    tokens_used: int
    success: bool
    error_type: str = ""
    retry_count: int = 0
    quality_score: float = 0.0


class MetricsFeedbackLoop:
    """
    Feedback Loop dựa trên metrics:
    Collect → Analyze → Optimize → Apply
    """
    
    def __init__(self):
        self.metrics_history: List[TaskMetrics] = []
        self.insights: List[str] = []
        self.optimizations: List[str] = []
    
    def collect(self, metrics: TaskMetrics):
        """Thu thập metrics mới"""
        self.metrics_history.append(metrics)
    
    def analyze(self) -> dict:
        """Phân tích patterns từ metrics"""
        if not self.metrics_history:
            return {"error": "No metrics yet"}
        
        total = len(self.metrics_history)
        successes = [m for m in self.metrics_history if m.success]
        failures = [m for m in self.metrics_history if not m.success]
        
        # Success rate
        success_rate = len(successes) / total if total else 0
        
        # Average metrics
        avg_tokens = sum(m.tokens_used for m in self.metrics_history) / total
        avg_duration = sum(m.duration_seconds for m in self.metrics_history) / total
        
        # Error patterns
        error_types: Dict[str, int] = {}
        for m in failures:
            if m.error_type:
                error_types[m.error_type] = error_types.get(m.error_type, 0) + 1
        
        # Retry patterns
        avg_retries = sum(m.retry_count for m in self.metrics_history) / total
        
        return {
            "total_tasks": total,
            "success_rate": success_rate,
            "avg_tokens": avg_tokens,
            "avg_duration": avg_duration,
            "error_patterns": error_types,
            "avg_retries": avg_retries,
        }
    
    def suggest_optimizations(self) -> List[str]:
        """Gợi ý tối ưu hóa dựa trên phân tích"""
        analysis = self.analyze()
        suggestions = []
        
        if analysis.get("success_rate", 1.0) < 0.7:
            suggestions.append(
                "Success rate < 70%. Consider adding more context to prompts."
            )
        
        error_patterns = analysis.get("error_patterns", {})
        for error_type, count in error_patterns.items():
            if count >= 3:
                suggestions.append(
                    f"Error '{error_type}' occurred {count} times. "
                    f"Add guardrail to prevent it."
                )
        
        avg_tokens = analysis.get("avg_tokens", 0)
        if avg_tokens > 50000:
            suggestions.append(
                "Average token usage is high. Consider context compression."
            )
        
        return suggestions
    
    def apply_optimization(self, optimization: str):
        """Áp dụng tối ưu hóa (ghi lại để harness sử dụng)"""
        self.optimizations.append(optimization)
    
    def run_cycle(self, metrics: TaskMetrics) -> dict:
        """Chạy 1 cycle hoàn chỉnh"""
        self.collect(metrics)
        analysis = self.analyze()
        suggestions = self.suggest_optimizations()
        
        for suggestion in suggestions:
            self.apply_optimization(suggestion)
        
        return {
            "analysis": analysis,
            "new_suggestions": suggestions,
            "total_optimizations": len(self.optimizations),
        }
```

### 5.2 Pattern Learning Loop

```python
class PatternLearningLoop:
    """
    Learning Loop: Học patterns từ successes và failures
    để cải thiện performance trong tương lai.
    """
    
    def __init__(self):
        self.success_patterns: Dict[str, List[str]] = {}
        self.failure_patterns: Dict[str, List[str]] = {}
    
    def record_success(self, task_type: str, approach: str):
        """Ghi lại pattern thành công"""
        if task_type not in self.success_patterns:
            self.success_patterns[task_type] = []
        if approach not in self.success_patterns[task_type]:
            self.success_patterns[task_type].append(approach)
    
    def record_failure(self, task_type: str, approach: str, reason: str):
        """Ghi lại pattern thất bại"""
        if task_type not in self.failure_patterns:
            self.failure_patterns[task_type] = []
        self.failure_patterns[task_type].append(f"{approach}: {reason}")
    
    def get_best_approach(self, task_type: str) -> str:
        """Đề xuất cách tiếp cận tốt nhất cho task type"""
        successes = self.success_patterns.get(task_type, [])
        if successes:
            return successes[-1]  # Most recent success
        
        return f"Use default approach for {task_type}"
    
    def get_patterns_to_avoid(self, task_type: str) -> List[str]:
        """Lấy danh sách patterns cần tránh"""
        return self.failure_patterns.get(task_type, [])
    
    def get_insights(self) -> dict:
        """Tổng hợp insights"""
        return {
            "success_task_types": list(self.success_patterns.keys()),
            "failure_task_types": list(self.failure_patterns.keys()),
            "total_success_patterns": sum(
                len(v) for v in self.success_patterns.values()
            ),
            "total_failure_patterns": sum(
                len(v) for v in self.failure_patterns.values()
            ),
        }
```

---

## 6. Outer Loop — Continuous Improvement

Outer Loop chạy trên **tần suất thấp** (ngày/tuần) và tập trung vào việc cải thiện **toàn bộ hệ thống**.

### 6.1 Prompt Evolution Loop

```python
class PromptEvolutionLoop:
    """
    Outer Loop: Evolution of prompts qua nhiều version.
    
    Theo dõi performance của từng prompt version,
    tự động chọn và cải thiện prompt tốt nhất.
    """
    
    def __init__(self):
        self.versions: List[Dict] = []
        self.ab_test_results: Dict[str, Dict] = {}
    
    def register_version(self, version_id: str, prompt: str):
        """Đăng ký prompt version mới"""
        self.versions.append({
            "id": version_id,
            "prompt": prompt,
            "metrics": {"success_rate": 0, "avg_quality": 0, "sample_size": 0},
            "created_at": time.strftime("%Y-%m-%d %H:%M:%S"),
        })
    
    def record_result(self, version_id: str, success: bool, quality_score: float):
        """Ghi kết quả sử dụng prompt version"""
        for v in self.versions:
            if v["id"] == version_id:
                m = v["metrics"]
                n = m["sample_size"]
                m["avg_quality"] = (m["avg_quality"] * n + quality_score) / (n + 1)
                m["success_rate"] = (
                    (m["success_rate"] * n + (1.0 if success else 0.0)) / (n + 1)
                )
                m["sample_size"] = n + 1
                break
    
    def get_best_version(self) -> dict:
        """Lấy prompt version tốt nhất"""
        if not self.versions:
            return None
        
        return max(
            self.versions,
            key=lambda v: v["metrics"]["avg_quality"] * v["metrics"]["success_rate"],
        )
    
    def evolve(self, base_version_id: str, new_prompt: str) -> str:
        """Tạo version mới từ base"""
        base = next((v for v in self.versions if v["id"] == base_version_id), None)
        if not base:
            return None
        
        new_id = f"{base_version_id}_v{len(self.versions) + 1}"
        self.register_version(new_id, new_prompt)
        return new_id
```

### 6.2 A/B Testing Loop

```python
import random


class ABTestingLoop:
    """
    A/B Testing Loop: So sánh 2 phiên bản prompt/strategy
    trên cùng một tập tasks để tìm ra phiên bản tốt hơn.
    """
    
    def __init__(self, confidence_level: float = 0.95):
        self.confidence_level = confidence_level
        self.experiments: List[Dict] = []
    
    def create_experiment(self, name: str, variant_a: str, variant_b: str):
        """Tạo experiment mới"""
        experiment = {
            "name": name,
            "variant_a": {"id": variant_a, "results": [], "conversions": 0, "total": 0},
            "variant_b": {"id": variant_b, "results": [], "conversions": 0, "total": 0},
            "status": "running",
            "winner": None,
        }
        self.experiments.append(experiment)
        return experiment
    
    def assign_variant(self, experiment_name: str) -> str:
        """Randomly assign variant"""
        exp = next((e for e in self.experiments if e["name"] == experiment_name), None)
        if not exp:
            raise ValueError(f"Experiment '{experiment_name}' not found")
        
        return random.choice([exp["variant_a"]["id"], exp["variant_b"]["id"]])
    
    def record_result(self, experiment_name: str, variant_id: str, 
                      success: bool, score: float):
        """Ghi kết quả cho variant"""
        exp = next((e for e in self.experiments if e["name"] == experiment_name), None)
        if not exp:
            return
        
        for key in ["variant_a", "variant_b"]:
            if exp[key]["id"] == variant_id:
                exp[key]["results"].append(score)
                exp[key]["total"] += 1
                if success:
                    exp[key]["conversions"] += 1
                break
    
    def analyze(self, experiment_name: str) -> dict:
        """Phân tích kết quả experiment"""
        exp = next((e for e in self.experiments if e["name"] == experiment_name), None)
        if not exp:
            return {"error": "Experiment not found"}
        
        a = exp["variant_a"]
        b = exp["variant_b"]
        
        a_rate = a["conversions"] / a["total"] if a["total"] else 0
        b_rate = b["conversions"] / b["total"] if b["total"] else 0
        
        a_avg = sum(a["results"]) / len(a["results"]) if a["results"] else 0
        b_avg = sum(b["results"]) / len(b["results"]) if b["results"] else 0
        
        # Determine winner
        winner = None
        if a["total"] >= 30 and b["total"] >= 30:  # Min sample size
            if abs(a_rate - b_rate) > 0.05:  # 5% minimum difference
                winner = a["id"] if a_rate > b_rate else b["id"]
        
        return {
            "variant_a": {"id": a["id"], "conversion_rate": a_rate, "avg_score": a_avg, "samples": a["total"]},
            "variant_b": {"id": b["id"], "conversion_rate": b_rate, "avg_score": b_avg, "samples": b["total"]},
            "winner": winner,
            "min_sample_reached": a["total"] >= 30 and b["total"] >= 30,
        }
```

### 6.3 Feedback-Driven Prompt Optimization

```python
class FeedbackDrivenPromptOptimizer:
    """
    Outer Loop: Tự động tối ưu prompt dựa trên feedback
    từ hàng trăm/thousands tasks.
    """
    
    def __init__(self):
        self.feedback_log: List[Dict] = []
        self.prompt_versions: Dict[str, str] = {}
        self.optimization_history: List[Dict] = []
    
    def log_feedback(self, task_type: str, prompt_version: str,
                     quality_score: float, user_feedback: str = ""):
        """Ghi feedback"""
        self.feedback_log.append({
            "task_type": task_type,
            "prompt_version": prompt_version,
            "quality_score": quality_score,
            "user_feedback": user_feedback,
            "timestamp": time.strftime("%Y-%m-%d %H:%M:%S"),
        })
    
    def analyze_prompt_performance(self) -> Dict[str, Dict]:
        """Phân tích performance từng prompt version"""
        perf: Dict[str, Dict] = {}
        
        for entry in self.feedback_log:
            version = entry["prompt_version"]
            if version not in perf:
                perf[version] = {"scores": [], "count": 0}
            
            perf[version]["scores"].append(entry["quality_score"])
            perf[version]["count"] += 1
        
        for version, data in perf.items():
            scores = data["scores"]
            data["avg_score"] = sum(scores) / len(scores) if scores else 0
            data["min_score"] = min(scores) if scores else 0
            data["max_score"] = max(scores) if scores else 0
        
        return perf
    
    def identify_improvement_areas(self) -> List[str]:
        """Tìm các area cần cải thiện"""
        perf = self.analyze_prompt_performance()
        areas = []
        
        for version, data in perf.items():
            if data["avg_score"] < 0.7 and data["count"] >= 5:
                areas.append(
                    f"Prompt '{version}' has avg score {data['avg_score']:.2f} "
                    f"with {data['count']} samples — needs improvement"
                )
        
        return areas
```

---

## 7. Self-Improvement Patterns

### 7.1 Self-Consistency Checking

Agent tạo **nhiều solutions** và so sánh để chọn solution tốt nhất:

```python
class SelfConsistencyLoop:
    """
    Self-Consistency: Generate N solutions → Compare → Select best.
    
    Nếu majority agrees → high confidence.
    Nếu disagreed → need more thinking.
    """
    
    def __init__(self, n_samples: int = 3):
        self.n_samples = n_samples
    
    def generate_solutions(self, task: str) -> List[str]:
        """Generate multiple solutions"""
        # In real implementation: calls LLM multiple times
        return [f"Solution {i} for {task}" for i in range(self.n_samples)]
    
    def compare(self, solutions: List[str]) -> dict:
        """Compare solutions and find consensus"""
        # Simple comparison — in real use, could use embedding similarity
        unique_solutions = list(set(solutions))
        
        if len(unique_solutions) == 1:
            return {
                "consensus": True,
                "best": unique_solutions[0],
                "confidence": 1.0,
                "agreement_ratio": 1.0,
            }
        
        # Count occurrences
        counts = {}
        for s in solutions:
            counts[s] = counts.get(s, 0) + 1
        
        best = max(counts, key=counts.get)
        agreement = counts[best] / len(solutions)
        
        return {
            "consensus": agreement >= 0.67,
            "best": best,
            "confidence": agreement,
            "agreement_ratio": agreement,
            "unique_solutions": len(unique_solutions),
        }
    
    def run(self, task: str) -> dict:
        """Run self-consistency check"""
        solutions = self.generate_solutions(task)
        result = self.compare(solutions)
        
        return {
            "solutions": solutions,
            "analysis": result,
        }
```

### 7.2 Reflection Loop (Sau khi hoàn thành task)

```python
class ReflectionLoop:
    """
    Reflection Loop: Sau khi hoàn thành task, agent nhìn lại
    và tự đánh giá những gì đã làm tốt/cần cải thiện.
    """
    
    def __init__(self):
        self.reflections: List[Dict] = []
        self.lessons_learned: List[str] = []
    
    def reflect(self, task: dict, result: dict) -> dict:
        """Phản hồi sau khi hoàn thành task"""
        reflection = {
            "task_type": task.get("type", "unknown"),
            "success": result.get("success", False),
            "what_went_well": [],
            "what_could_improve": [],
            "lessons": [],
            "timestamp": time.strftime("%Y-%m-%d %H:%M:%S"),
        }
        
        if result.get("success"):
            reflection["what_went_well"].append("Task completed successfully")
            if result.get("total_rounds", 0) == 1:
                reflection["what_went_well"].append("Completed in first attempt")
        else:
            reflection["what_could_improve"].append(
                f"Failed after {result.get('total_rounds', '?')} rounds"
            )
            reflection["lessons"].append(
                f"Need better initial approach for {task.get('type', 'unknown')}"
            )
        
        self.reflections.append(reflection)
        self.lessons_learned.extend(reflection["lessons"])
        
        return reflection
    
    def get_learning_summary(self) -> dict:
        """Tổng hợp learnings"""
        return {
            "total_reflections": len(self.reflections),
            "success_count": sum(1 for r in self.reflections if r["success"]),
            "failure_count": sum(1 for r in self.reflections if not r["success"]),
            "lessons_learned": self.lessons_learned,
            "improvement_areas": list(set(
                area
                for r in self.reflections
                for area in r["what_could_improve"]
            )),
        }
```

---

## 8. Loop Orchestration Engine

Tổng hợp **tất cả loops** thành một hệ thống hoàn chỉnh:

```python
class LoopOrchestrationEngine:
    """
    Loop Orchestration Engine: Tổng hợp tất cả loops
    thành một hệ thống hoàn chỉnh.
    
    ┌─────────────────────────────────────────────────┐
    │  Inner Loop → Execution Loop → Validation Loop  │
    │       │                              │          │
    │       ▼                              ▼          │
    │  Reflection    ←──  Feedback Loop  ←──┘         │
    │       │                                         │
    │       ▼                                         │
    │  Outer Loop (Optimization)                      │
    └─────────────────────────────────────────────────┘
    """
    
    def __init__(self, name: str = "loop-orchestrator"):
        self.name = name
        self.inner_loop = InnerReasoningLoop()
        self.retry_engine = AdaptiveRetryEngine()
        self.validation_loop = TestDrivenLoop()
        self.feedback_loop = MetricsFeedbackLoop()
        self.pattern_learning = PatternLearningLoop()
        self.reflection_loop = ReflectionLoop()
        self.prompt_optimizer = FeedbackDrivenPromptOptimizer()
    
    def execute_task(self, task: Dict) -> Dict:
        """
        Execute 1 task qua tất cả loops:
        
        1. Inner Loop: Think → Act → Observe
        2. Execution Loop: Retry if fail
        3. Validation Loop: Test → Fix → Re-test
        4. Feedback Loop: Collect metrics
        5. Reflection Loop: Learn from result
        """
        task_type = task.get("type", "general")
        task_content = task.get("content", "")
        
        # Step 1: Inner Loop — Reasoning
        inner_result = self.inner_loop.run(
            task=task_content,
            context=task,
            executor=lambda x: f"Executed: {x}",
        )
        
        # Step 2: Execution Loop — Retry
        execution_result = None
        def execute():
            return self.validation_loop.run(
                task=task_content,
                context=task,
                test_suite=["test_1", "test_2"],
            )
        
        try:
            execution_result = self.retry_engine.execute_with_retry(execute)
        except Exception as e:
            execution_result = {"success": False, "error": str(e)}
        
        # Step 3: Collect metrics (Feedback Loop)
        metrics = TaskMetrics(
            task_type=task_type,
            duration_seconds=0.1,  # placeholder
            tokens_used=1000,
            success=execution_result.get("success", False),
            retry_count=len(self.retry_engine.retry_history),
        )
        feedback_result = self.feedback_loop.run_cycle(metrics)
        
        # Step 4: Reflection
        reflection = self.reflection_loop.reflect(task, execution_result)
        
        # Step 5: Pattern learning
        if execution_result.get("success"):
            self.pattern_learning.record_success(task_type, "default")
        else:
            self.pattern_learning.record_failure(
                task_type, "default", 
                execution_result.get("error", "unknown"),
            )
        
        return {
            "task": task,
            "inner_loop": inner_result,
            "execution": execution_result,
            "feedback": feedback_result,
            "reflection": reflection,
            "patterns": self.pattern_learning.get_insights(),
        }
```

---

## 9. Case Studies

### 9.1 Claude Code — Multi-Layer Loop System

Claude Code sử dụng **4 lớp loop** xếp chồng lên nhau:

```python
class ClaudeCodeLoops:
    """
    Claude Code Loop Architecture:
    
    Layer 1 (Inner): Think → Act → Observe (trong mỗi LLM call)
    Layer 2 (Tool): Write code → Run test → Fix → Re-test
    Layer 3 (Session): Task → Validate → User feedback → Improve
    Layer 4 (Cross-session): Patterns → Optimizations → Evolve
    """
    
    def __init__(self):
        # Layer 1: Inner — reasoning optimization
        self.reasoning_loop = InnerReasoningLoop(max_reflections=2)
        
        # Layer 2: Tool — execution with retry
        self.tool_loop = AdaptiveRetryEngine(max_retries=3)
        self.lint_fix_loop = LintFixLoop(max_rounds=3)
        
        # Layer 3: Session — validation + feedback
        self.validation = TestDrivenLoop(max_fix_rounds=5)
        self.feedback = MetricsFeedbackLoop()
        
        # Layer 4: Cross-session — learning
        self.patterns = PatternLearningLoop()
        self.reflection = ReflectionLoop()
    
    def process_request(self, request: str) -> Dict:
        """Process user request through all layers"""
        # Layer 1: Reason
        reasoning = self.reasoning_loop.run(
            task=request, context={}, executor=lambda x: x
        )
        
        # Layer 2: Execute with retry
        code = f"# Solution for: {request}"
        lint_result = self.lint_fix_loop.run(code)
        
        # Layer 3: Validate
        test_result = self.validation.run(
            task=request, context={}, test_suite=["functional", "lint"]
        )
        
        # Layer 3: Collect feedback
        metrics = TaskMetrics(
            task_type="coding",
            duration_seconds=1.0,
            tokens_used=2000,
            success=test_result["success"],
        )
        self.feedback.collect(metrics)
        
        # Layer 4: Reflect
        reflection = self.reflection.reflect(
            {"type": "coding", "content": request}, test_result
        )
        
        return {
            "code": lint_result["final_code"],
            "success": test_result["success"],
            "rounds": test_result["total_rounds"],
            "reflection": reflection,
        }
```

**Kết quả thực tế:**
- Claude Code resolve **44% GitHub issues** trên SWE-bench
- Self-refine loop cải thiện quality **+38%** sau mỗi round
- Retry engine giảm **60% transient failures**

### 9.2 Cursor IDE — Fast Inner Loop

Cursor tập trung vào **Inner Loop cực nhanh**:

```
User types code
    → Cursor predicts next lines (Inner Loop: Think)
    → User accepts/rejects (Observe)
    → Cursor adapts (Reflect)
    → All in <100ms
```

Key insight: **Inner Loop phải nhanh hơn phản xạ của con người** (<100ms) để không gây interruption.

### 9.3 Devin AI — Full Loop Stack

Devin sử dụng **đầy đủ tất cả loops**:

```
Devin's Loop Stack:
┌─────────────────────────────────────────┐
│ Outer Loop: Learn from each completed    │
│             task to improve future tasks  │
│                                          │
│  ┌────────────────────────────────────┐  │
│  │ Session Loop: Plan → Execute →     │  │
│  │ Test → Deploy → User Review        │  │
│  │                                    │  │
│  │  ┌──────────────────────────────┐  │  │
│  │  │ Execution Loop: Write →      │  │  │
│  │  │ Lint → Test → Fix (retry 3x)│  │  │
│  │  │                              │  │  │
│  │  │  ┌────────────────────────┐  │  │  │
│  │  │  │ Inner Loop: Think →    │  │  │  │
│  │  │  │ Act → Observe (~50ms)  │  │  │  │
│  │  │  └────────────────────────┘  │  │  │
│  │  └──────────────────────────────┘  │  │
│  └────────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

---

## 10. Loop Anti-Patterns

### 10.1 Các Lỗi Thường Gặp

| Anti-Pattern | Mô tả | Giải pháp |
|-------------|-------|-----------|
| **Infinite Loop** | Loop chạy mãi không dừng | Set max iterations + timeout |
| **Spin Loop** | Loop chạy nhanh nhưng không progress | Detect stagnation (no change in output) |
| **Rubber Duck Loop** | Agent tự hỏi tự trả lời mà không có external signal | Kết nối với real tests / user feedback |
| **Echo Chamber** | Agent reinforcement own bad patterns | A/B testing + external evaluation |
| **Premature Convergence** | Dừng quá sớm, chưa thực sự improve | Set minimum rounds + quality threshold |
| **Over-optimization** | Optimize cho metric mà mất general quality | Multi-objective optimization |

### 10.2 Guardrails Cho Loops

```python
class LoopGuardrails:
    """Bảo vệ loops khỏi các anti-patterns"""
    
    @staticmethod
    def infinite_loop_protection(max_iterations: int = 100, timeout: float = 300.0):
        """Prevent infinite loops"""
        def decorator(func):
            def wrapper(*args, **kwargs):
                start = time.time()
                for i in range(max_iterations):
                    if time.time() - start > timeout:
                        raise LoopTimeoutError(
                            f"Loop exceeded timeout ({timeout}s) after {i} iterations"
                        )
                    result = func(*args, i, **kwargs)
                    if result is not None and result.get("done"):
                        return result
                raise MaxIterationsError(
                    f"Loop exceeded max iterations ({max_iterations})"
                )
            return wrapper
        return decorator
    
    @staticmethod
    def stagnation_detection(history: list, window: int = 5, threshold: float = 0.01):
        """Detect if loop is making no progress"""
        if len(history) < window:
            return False
        
        recent = history[-window:]
        if all(isinstance(h, (int, float)) for h in recent):
            variance = max(recent) - min(recent)
            return variance < threshold
        
        return False


class LoopTimeoutError(Exception):
    pass


class MaxIterationsError(Exception):
    pass
```

---

## 11. Best Practices

### DO ✅

| # | Nguyên tắc | Giải thích |
|---|-----------|------------|
| 1 | **Always set max iterations** | Mọi loop đều phải có termination condition |
| 2 | **Track metrics in every loop** | Không đo lường = không cải thiện được |
| 3 | **Use different strategies for different error types** | Permanent error → don't retry. Transient → retry. |
| 4 | **Log loop history** | Để debug và phân tích patterns sau này |
| 5 | **Combine inner + outer loops** | Inner loop cho speed, outer loop cho learning |
| 6 | **Add stagnation detection** | Phát hiện khi loop không progress |
| 7 | **Use circuit breaker for external calls** | Tránh gọi service đang down liên tục |
| 8 | **Make loops observable** | Mỗi loop step nên log state, duration, result |
| 9 | **Separate retry logic from business logic** | Retry là infrastructure, không phải domain |
| 10 | **Test loops independently** | Unit test từng loop type riêng |

### DON'T ❌

| # | Anti-pattern | Tại sao |
|---|-------------|---------|
| 1 | **No max iterations** | Infinite loops消耗 resources |
| 2 | **Retry on permanent errors** | SyntaxError sẽ không fix được bằng retry |
| 3 | **Ignore metrics** | Nếu không track, bạn không biết loop có improve không |
| 4 | **One-size-fits-all retry** | Mỗi error type cần strategy riêng |
| 5 | **Nested loops without depth limit** | Loop trong loop có thể explode |
| 6 | **No timeout** | Even retry cần deadline |
| 7 | **Retry immediately without delay** | Thundering herd problem |
| 8 | **Self-loop without external validation** | Agent cần external signal (tests, user) |
| 9 | **Track everything, analyze nothing** | Metrics không dùng = noise |
| 10 | **Assume loops always improve** |有时候 loop worsening quality |

### 11.1 Loop Design Checklist

```
□ termination_condition: Loop dừng khi nào?
□ max_iterations: Số lần lặp tối đa?
□ timeout: Thời gian chạy tối đa?
□ error_handling: Xử lý lỗi ra sao?
□ retry_strategy: Retry hay bail out?
□ metrics: Theo dõi những gì?
□ logging: Ghi log ở đâu?
□ stagnation_detection: Phát hiện không progress?
□ external_validation: Kiểm tra kết quả bằng gì?
□ observability: Debug loop bằng cách nào?
```

---

## 12. Tương Lai

### 12.1 Xu Hướng 2026-2028

```
┌─────────────────────────────────────────────────────────────┐
│                    LOOP ENGINEERING EVOLUTION                 │
│                                                              │
│  2025: Basic loops (retry, validation)                      │
│        → Manual configuration                                │
│                                                              │
│  2026: Adaptive loops (ML-powered)                          │
│        → Self-tuning parameters                              │
│        → Auto strategy selection                             │
│                                                              │
│  2027: Self-evolving loops                                  │
│        → Loops that rewrite themselves                       │
│        → Cross-task learning at scale                        │
│        → Multi-agent loop coordination                       │
│                                                              │
│  2028: Autonomous improvement                               │
│        → Loops that design new loops                         │
│        → Continuous self-evolution                           │
│        → Human-in-the-loop for strategic decisions only      │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 12.2 Research Directions

1. **Meta-Learning Loops** — Agent học cách học: chọn chiến lược loop phù hợp cho từng loại task
2. **Multi-Agent Loop Coordination** — Nhiều agents share loop insights, collectively improve
3. **Causal Loop Analysis** — Hiểu causal relationships trong loops, không chỉ correlations
4. **Loop Compression** — Giảm số iterations cần thiết bằng predicted outcomes
5. **Ethical Loop Constraints** — Đảm bảo loops không tạo ra harmful feedback cycles

---

## Tài Liệu Tham Khảo

### Papers & Research
- **Self-Refine** (Madaan et al., 2023) — Iterative Refinement with Self-Feedback
- **Chain-of-Thought Reasoning** (Wei et al., 2022) — Thought reasoning for LLMs
- **Constitutional AI** (Anthropic, 2023) — Self-critique and improvement loops
- **Reflexion** (Shinn et al., 2023) — Language agents with verbal reinforcement learning
- **SWE-bench** (Princeton, 2025) — Benchmarking coding agents with iterative execution

### Frameworks & Tools
- **LangGraph** — State machine framework cho agent loops
- **AutoGen** (Microsoft) — Multi-agent loop orchestration
- **CrewAI** — Agent workflow loops
- **Anthropic Tool Use** — Built-in retry and validation loops

### Blogs & Resources
- [Anthropic: Building Effective Agents](https://docs.anthropic.com)
- [OpenAI: Retry and Backoff Best Practices](https://platform.openai.com)
- [Google: Circuit Breaker Pattern](https://cloud.google.com)
- [Martin Fowler: Circuit Breaker](https://martinfowler.com)

---

> **"Hệ thống tốt nhất không phải là hệ thống không bao giờ sai — mà là hệ thống sửa sai nhanh nhất."**

---

*Bài viết thuộc [AI Coding Skills Framework](../) — Module XII: Loop Engineering*