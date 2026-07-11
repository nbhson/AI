# 🤖 X. Automation

## Tổng Quan

**Automation** trong AI coding là quá trình **tự động hóa các tác vụ coding lặp đi lặp lại** — từ lint, test, build, deploy, đến code generation và maintenance. Automation giúp tiết kiệm thời gian, giảm lỗi human, và tăng consistent quality.

```
┌──────────────────────────────────────────────────────────────────┐
│                       AUTOMATION                                  │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │                                                            │  │
│  │  ┌──────────┐    ┌──────────┐    ┌──────────┐            │  │
│  │  │ Trigger  │    │ Pipeline │    │ Output   │            │  │
│  │  │ (Event,  │───►│ (Steps,  │───►│ (Files,  │            │  │
│  │  │  Cron,   │    │  Tools,  │    │  Deploy, │            │  │
│  │  │  Webhook)│    │  Agents) │    │  Report) │            │  │
│  │  └──────────┘    └──────────┘    └──────────┘            │  │
│  │                                                            │  │
│  │  ┌──────────────────────────────────────────────────┐     │  │
│  │  │          Scheduler & Orchestration Layer           │     │  │
│  │  └──────────────────────────────────────────────────┘     │  │
│  │                                                            │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## Nội Dung

| # | Chủ đề | Mô tả |
|---|--------|-------|
| 1 | [Automation Patterns](#1-automation-patterns) | Các mẫu tự động hóa phổ biến |
| 2 | [CI/CD Pipelines](#2-cicd-pipelines) | Tích hợp liên tục / triển khai liên tục |
| 3 | [Code Generation](#3-code-generation-automation) | Tự动生成 code |
| 4 | [Scheduled Tasks](#4-scheduled-tasks) | Tự động chạy theo lịch |
| 5 | [Git Automation](#5-git-automation) | Tự động hóa Git workflow |
| 6 | [Monitoring & Alerts](#6-monitoring--alerts) | Theo dõi và cảnh báo |

---

## 1. Automation Patterns

### 1.1 Các Mẫu Tự Động Hóa

```
┌──────────────────────────────────────────────────────────────────┐
│                  AUTOMATION PATTERNS                              │
│                                                                  │
│  1. EVENT-DRIVEN (Kích hoạt bởi sự kiện)                       │
│     Git push → Run tests                                        │
│     PR created → Auto review                                    │
│     File changed → Rebuild                                      │
│                                                                  │
│  2. SCHEDULED (Kích hoạt theo lịch)                             │
│     Daily → Run full test suite                                 │
│     Weekly → Security scan                                      │
│     Monthly → Dependency update check                           │
│                                                                  │
│  3. REACTIVE (Phản hồi thay đổi)                                │
│     Test fail → Auto-fix attempt                                │
│     Lint error → Auto-format                                    │
│     Performance drop → Alert + rollback                         │
│                                                                  │
│  4. PROACTIVE (Chủ động phòng ngừa)                             │
│     Check unused deps → Suggest removal                         │
│     Detect code smell → Suggest refactor                        │
│     Monitor coverage → Suggest new tests                        │
│                                                                  │
│  5. SELF-HEALING (Tự sửa chữa)                                  │
│     Build fail → Auto-fix & retry                               │
│     Test flaky → Quarantine & retry                             │
│     Deploy fail → Auto-rollback                                 │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 1.2 Automation Engine

```python
from dataclasses import dataclass, field
from typing import Any, Callable, Dict, List, Optional
from enum import Enum
from datetime import datetime
import time

class TriggerType(Enum):
    EVENT = "event"
    SCHEDULED = "scheduled"
    MANUAL = "manual"
    CONDITIONAL = "conditional"


@dataclass
class AutomationRule:
    """Một quy tắc tự động hóa"""
    id: str
    name: str
    trigger_type: TriggerType
    trigger_config: Dict[str, Any] = field(default_factory=dict)
    conditions: List[Callable] = field(default_factory=list)
    actions: List[Callable] = field(default_factory=list)
    enabled: bool = True
    last_run: Optional[str] = None
    run_count: int = 0


class AutomationEngine:
    """
    Engine chạy automation rules.
    
    Supports:
    - Event-driven triggers
    - Scheduled triggers
    - Conditional execution
    - Retry on failure
    - Execution logging
    """
    
    def __init__(self):
        self.rules: Dict[str, AutomationRule] = {}
        self.event_handlers: Dict[str, List[str]] = {}
        self.execution_log: List[Dict] = []
    
    def register_rule(self, rule: AutomationRule):
        """Đăng ký automation rule"""
        self.rules[rule.id] = rule
        
        if rule.trigger_type == TriggerType.EVENT:
            event = rule.trigger_config.get("event", "")
            if event not in self.event_handlers:
                self.event_handlers[event] = []
            self.event_handlers[event].append(rule.id)
    
    def trigger_event(self, event_name: str, 
                      event_data: Dict = None):
        """Kích hoạt event — chạy tất cả rules liên quan"""
        handler_ids = self.event_handlers.get(event_name, [])
        
        for rule_id in handler_ids:
            rule = self.rules.get(rule_id)
            if rule and rule.enabled:
                self._execute_rule(rule, event_data or {})
    
    def _execute_rule(self, rule: AutomationRule, 
                      context: Dict):
        """Thực thi một rule"""
        start_time = time.time()
        
        # Check conditions
        for condition in rule.conditions:
            if not condition(context):
                self._log(rule.id, "skipped", 
                         "condition not met", context)
                return
        
        # Execute actions
        try:
            for action in rule.actions:
                action(context)
            
            rule.last_run = datetime.now().isoformat()
            rule.run_count += 1
            elapsed = time.time() - start_time
            
            self._log(rule.id, "success", 
                     f"completed in {elapsed:.2f}s", context)
            
        except Exception as e:
            elapsed = time.time() - start_time
            self._log(rule.id, "failed", str(e), context)
            
            # Retry logic
            max_retries = rule.trigger_config.get("max_retries", 0)
            if max_retries > 0:
                self._retry_rule(rule, context, max_retries)
    
    def _retry_rule(self, rule: AutomationRule, 
                    context: Dict, max_retries: int):
        """Retry rule trên failure"""
        for attempt in range(max_retries):
            time.sleep(2 ** attempt)  # Exponential backoff
            try:
                for action in rule.actions:
                    action(context)
                self._log(rule.id, "success", 
                         f"retry {attempt + 1} succeeded", context)
                return
            except Exception:
                continue
        
        self._log(rule.id, "failed", 
                 f"all {max_retries} retries exhausted", context)
    
    def _log(self, rule_id: str, status: str, 
             message: str, context: Dict):
        self.execution_log.append({
            "rule_id": rule_id,
            "status": status,
            "message": message,
            "timestamp": datetime.now().isoformat(),
            "context_keys": list(context.keys()),
        })
```

---

## 2. CI/CD Pipelines

### 2.1 GitHub Actions Templates

```yaml
# .github/workflows/ci.yml
# CI Pipeline — chạy khi push hoặc PR

name: CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  lint:
    name: Lint & Format
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      - name: Install dependencies
        run: pip install -r requirements.txt
      - name: Run linter
        run: |
          flake8 src/
          black --check src/
          isort --check src/
      - name: Type check
        run: mypy src/

  test:
    name: Test
    runs-on: ubuntu-latest
    needs: lint
    strategy:
      matrix:
        python-version: [3.10, 3.11, 3.12]
    steps:
      - uses: actions/checkout@v4
      - name: Setup Python ${{ matrix.python-version }}
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
      - name: Install dependencies
        run: pip install -r requirements.txt
      - name: Run tests
        run: |
          pytest tests/ \
            --cov=src/ \
            --cov-report=xml \
            --junitxml=report.xml
      - name: Upload coverage
        uses: codecov/codecov-action@v4
        with:
          file: ./coverage.xml

  security:
    name: Security Scan
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run security scan
        run: |
          pip install safety bandit
          safety check -r requirements.txt
          bandit -r src/ -f json -o security-report.json
      - name: Upload security report
        uses: actions/upload-artifact@v4
        with:
          name: security-report
          path: security-report.json

  build:
    name: Build
    runs-on: ubuntu-latest
    needs: [test, security]
    steps:
      - uses: actions/checkout@v4
      - name: Build package
        run: |
          python -m build
      - name: Upload artifacts
        uses: actions/upload-artifact@v4
        with:
          name: dist
          path: dist/
```

### 2.2 Deployment Automation

```yaml
# .github/workflows/deploy.yml
# Deploy Pipeline — chạy khi release

name: Deploy

on:
  release:
    types: [published]
  workflow_dispatch:
    inputs:
      environment:
        description: 'Deploy target'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production

jobs:
  deploy-staging:
    name: Deploy to Staging
    runs-on: ubuntu-latest
    if: |
      github.event_name == 'workflow_dispatch' && 
      github.event.inputs.environment == 'staging'
    environment: staging
    steps:
      - uses: actions/checkout@v4
      - name: Deploy
        run: |
          echo "Deploying to staging..."
          # Add your deploy commands here
      - name: Run smoke tests
        run: |
          echo "Running smoke tests..."
          # curl https://staging.example.com/health
      - name: Notify
        if: always()
        run: |
          echo "Deployment status: ${{ job.status }}"

  deploy-production:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: deploy-staging
    if: github.event_name == 'release'
    environment: production
    steps:
      - uses: actions/checkout@v4
      - name: Deploy
        run: |
          echo "Deploying to production..."
      - name: Verify deployment
        run: |
          echo "Verifying production health..."
      - name: Create rollback plan
        run: |
          echo "Rollback plan saved"
```

---

## 3. Code Generation Automation

### 3.1 Scaffolding Templates

```python
from pathlib import Path
from typing import Dict, List
import os

class CodeGenerator:
    """
    Tự động generate code từ templates.
    
    Supports:
    - Project scaffolding
    - Component generation
    - API endpoint generation
    - Test file generation
    """
    
    def __init__(self, template_dir: str = "./templates"):
        self.template_dir = Path(template_dir)
        self.variables: Dict[str, str] = {}
    
    def set_variables(self, **kwargs):
        """Set template variables"""
        self.variables.update(kwargs)
    
    def generate_project(self, project_name: str, 
                         structure: str = "python"):
        """Generate project structure"""
        structures = {
            "python": self._python_structure(),
            "fastapi": self._fastapi_structure(),
            "react": self._react_structure(),
        }
        
        files = structures.get(structure, 
                               self._python_structure())
        
        for filepath, content in files.items():
            full_path = Path(project_name) / filepath
            full_path.parent.mkdir(parents=True, exist_ok=True)
            full_path.write_text(
                self._render(content), encoding="utf-8"
            )
            print(f"  ✅ Created {full_path}")
    
    def generate_endpoint(self, name: str, 
                          methods: List[str] = None):
        """Generate API endpoint files"""
        methods = methods or ["GET", "POST"]
        
        template = f'''
from fastapi import APIRouter, HTTPException
from pydantic import BaseModel

router = APIRouter(prefix="/{name.lower()}", tags=["{name}"])


class {name}Create(BaseModel):
    """Request model for creating {name}"""
    pass


class {name}Response(BaseModel):
    """Response model for {name}"""
    id: str
    created_at: str


@router.get("/", response_model=List[{name}Response])
async def list_{name.lower()}s():
    """List all {name.lower()}s"""
    return []


@router.get("/{{item_id}}", response_model={name}Response)
async def get_{name.lower()}(item_id: str):
    """Get a specific {name.lower()}"""
    raise HTTPException(status_code=404, detail="Not found")


@router.post("/", response_model={name}Response, status_code=201)
async def create_{name.lower()}(data: {name}Create):
    """Create a new {name.lower()}"""
    return {name}Response(id="new", created_at="now")
'''
        return template
    
    def generate_test(self, module_name: str, 
                      functions: List[str] = None):
        """Generate test file"""
        functions = functions or ["main"]
        
        test_cases = ""
        for func in functions:
            test_cases += f'''
    def test_{func}(self):
        """Test {func} function"""
        result = {module_name}.{func}()
        assert result is not None
    
    def test_{func}_edge_cases(self):
        """Test {func} with edge cases"""
        # TODO: Add edge case tests
        pass
'''
        
        template = f'''
import pytest
from {module_name} import *


class Test{module_name.title()}:
    """Test suite for {module_name}"""
{test_cases}
'''
        return template
    
    def _render(self, template: str) -> str:
        """Render template với variables"""
        result = template
        for key, value in self.variables.items():
            result = result.replace(f"{{{{{key}}}}}", value)
        return result
    
    def _python_structure(self) -> Dict[str, str]:
        return {
            "README.md": "# {project_name}\n\nProject description.",
            "pyproject.toml": """[build-system]
requires = ["setuptools>=68.0"]
build-backend = "setuptools.backends._legacy:_Backend"

[project]
name = "{project_name}"
version = "0.1.0"
dependencies = []
""",
            "src/__init__.py": "",
            "src/main.py": 'def main():\n    print("Hello, World!")\n',
            "tests/__init__.py": "",
            "tests/test_main.py": "from src.main import main\n",
            ".gitignore": "__pycache__/\n*.pyc\n.env\n",
        }
```

---

## 4. Scheduled Tasks

### 4.1 Task Scheduler

```python
import time
from datetime import datetime, timedelta
from typing import Callable, Dict, List, Optional
from dataclasses import dataclass, field
from enum import Enum
import threading

class ScheduleType(Enum):
    ONCE = "once"
    INTERVAL = "interval"
    DAILY = "daily"
    WEEKLY = "weekly"
    CRON = "cron"


@dataclass
class ScheduledTask:
    """Task chạy theo lịch"""
    id: str
    name: str
    func: Callable
    schedule_type: ScheduleType
    interval_seconds: int = 0
    run_time: str = ""             # "HH:MM" cho daily
    enabled: bool = True
    last_run: Optional[str] = None
    next_run: Optional[str] = None
    error_count: int = 0


class TaskScheduler:
    """
    Scheduler tự động chạy tasks theo lịch.
    
    Usage:
        scheduler = TaskScheduler()
        scheduler.add_task(ScheduledTask(
            id="daily-scan",
            name="Daily Security Scan",
            func=run_security_scan,
            schedule_type=ScheduleType.DAILY,
            run_time="02:00",
        ))
        scheduler.start()
    """
    
    def __init__(self):
        self.tasks: Dict[str, ScheduledTask] = {}
        self.running = False
        self._thread: Optional[threading.Thread] = None
    
    def add_task(self, task: ScheduledTask):
        """Thêm task vào scheduler"""
        self.tasks[task.id] = task
        self._update_next_run(task)
    
    def remove_task(self, task_id: str):
        """Xóa task"""
        if task_id in self.tasks:
            del self.tasks[task_id]
    
    def start(self):
        """Bắt đầu scheduler (background thread)"""
        self.running = True
        self._thread = threading.Thread(
            target=self._run_loop, daemon=True
        )
        self._thread.start()
        print("⏰ Scheduler started")
    
    def stop(self):
        """Dừng scheduler"""
        self.running = False
        if self._thread:
            self._thread.join(timeout=5)
        print("⏰ Scheduler stopped")
    
    def _run_loop(self):
        """Main loop — check và run tasks"""
        while self.running:
            now = datetime.now()
            
            for task in self.tasks.values():
                if not task.enabled:
                    continue
                
                if task.next_run:
                    next_time = datetime.fromisoformat(task.next_run)
                    if now >= next_time:
                        self._execute_task(task)
                        self._update_next_run(task)
            
            time.sleep(30)  # Check every 30 seconds
    
    def _execute_task(self, task: ScheduledTask):
        """Thực thi scheduled task"""
        try:
            print(f"⏰ Running: {task.name}")
            task.func()
            task.last_run = datetime.now().isoformat()
            task.error_count = 0
        except Exception as e:
            task.error_count += 1
            print(f"❌ Task '{task.name}' failed: {e}")
    
    def _update_next_run(self, task: ScheduledTask):
        """Cập nhật thời gian chạy tiếp theo"""
        now = datetime.now()
        
        if task.schedule_type == ScheduleType.ONCE:
            task.next_run = None
        elif task.schedule_type == ScheduleType.INTERVAL:
            next_time = now + timedelta(seconds=task.interval_seconds)
            task.next_run = next_time.isoformat()
        elif task.schedule_type == ScheduleType.DAILY:
            hour, minute = map(int, task.run_time.split(":"))
            next_time = now.replace(hour=hour, minute=minute, second=0)
            if next_time <= now:
                next_time += timedelta(days=1)
            task.next_run = next_time.isoformat()


# Usage
scheduler = TaskScheduler()

scheduler.add_task(ScheduledTask(
    id="daily-test",
    name="Daily Test Suite",
    func=lambda: print("Running full test suite..."),
    schedule_type=ScheduleType.DAILY,
    run_time="06:00",
))

scheduler.add_task(ScheduledTask(
    id="weekly-scan",
    name="Weekly Security Scan",
    func=lambda: print("Running security scan..."),
    schedule_type=ScheduleType.WEEKLY,
    run_time="02:00",
))

scheduler.start()
```

---

## 5. Git Automation

### 5.1 Git Hooks Automation

```python
from pathlib import Path
from typing import List

class GitAutomation:
    """
    Tự động hóa Git workflow — pre-commit hooks,
    commit message validation, branch naming.
    """
    
    @staticmethod
    def generate_pre_commit_hook(actions: List[str] = None) -> str:
        """Tạo pre-commit hook script"""
        actions = actions or ["lint", "format", "test"]
        
        hook = "#!/bin/bash\n"
        hook += "# Auto-generated pre-commit hook\n"
        hook += "set -e\n\n"
        
        if "lint" in actions:
            hook += 'echo "🔍 Running linter..."\n'
            hook += "flake8 . --count --max-line-length=88\n\n"
        
        if "format" in actions:
            hook += 'echo "🎨 Checking formatting..."\n'
            hook += "black --check .\n"
            hook += "isort --check .\n\n"
        
        if "test" in actions:
            hook += 'echo "🧪 Running tests..."\n'
            hook += "pytest tests/ -x -q\n\n"
        
        hook += 'echo "✅ All checks passed!"\n'
        return hook
    
    @staticmethod
    def generate_commit_message(
        type_: str, scope: str, description: str,
        body: str = "", breaking: str = ""
    ) -> str:
        """
        Tạo commit message theo Conventional Commits.
        
        Types: feat, fix, docs, style, refactor, test, chore
        """
        msg = f"{type_}"
        if scope:
            msg += f"({scope})"
        msg += f": {description}"
        
        if body:
            msg += f"\n\n{body}"
        
        if breaking:
            msg += f"\n\nBREAKING CHANGE: {breaking}"
        
        return msg
    
    @staticmethod
    def validate_branch_name(name: str) -> tuple:
        """Validate branch name theo convention"""
        import re
        
        valid_patterns = [
            r"^(feature|fix|hotfix|release|docs)/.+$",
            r"^main$",
            r"^develop$",
            r"^v\d+\.\d+\.\d+$",
        ]
        
        for pattern in valid_patterns:
            if re.match(pattern, name):
                return True, "Valid branch name"
        
        return False, (
            f"Invalid branch name: '{name}'. "
            f"Use format: type/description "
            f"(e.g., feature/add-auth)"
        )
```

---

## 6. Monitoring & Alerts

### 6.1 Monitoring Dashboard

```python
from dataclasses import dataclass, field
from typing import Any, Dict, List, Optional
from datetime import datetime

@dataclass
class Metric:
    """Một metric được theo dõi"""
    name: str
    value: float
    unit: str = ""
    timestamp: str = field(
        default_factory=lambda: datetime.now().isoformat()
    )
    tags: Dict[str, str] = field(default_factory=dict)


class MonitoringDashboard:
    """
    Dashboard monitor cho automation system.
    
    Tracks:
    - Pipeline execution times
    - Success/failure rates
    - Resource usage
    - Alert thresholds
    """
    
    def __init__(self):
        self.metrics: List[Metric] = []
        self.alerts: List[Dict] = []
        self.thresholds: Dict[str, Dict] = {}
    
    def record_metric(self, name: str, value: float,
                      unit: str = "", tags: Dict = None):
        """Ghi metric"""
        metric = Metric(name=name, value=value, 
                       unit=unit, tags=tags or {})
        self.metrics.append(metric)
        
        # Check thresholds
        self._check_threshold(name, value)
    
    def set_threshold(self, metric_name: str, 
                      warning: float = None,
                      critical: float = None,
                      operator: str = "gt"):
        """Đặt ngưỡng cảnh báo"""
        self.thresholds[metric_name] = {
            "warning": warning,
            "critical": critical,
            "operator": operator,
        }
    
    def _check_threshold(self, name: str, value: float):
        """Kiểm tra ngưỡng"""
        threshold = self.thresholds.get(name)
        if not threshold:
            return
        
        op = threshold.get("operator", "gt")
        comparisons = {
            "gt": lambda v, t: v > t,
            "lt": lambda v, t: v < t,
            "gte": lambda v, t: v >= t,
            "lte": lambda v, t: v <= t,
        }
        
        compare = comparisons.get(op, comparisons["gt"])
        
        if threshold.get("critical") and compare(value, threshold["critical"]):
            self.alerts.append({
                "level": "critical",
                "metric": name,
                "value": value,
                "threshold": threshold["critical"],
                "timestamp": datetime.now().isoformat(),
            })
        elif threshold.get("warning") and compare(value, threshold["warning"]):
            self.alerts.append({
                "level": "warning",
                "metric": name,
                "value": value,
                "threshold": threshold["warning"],
                "timestamp": datetime.now().isoformat(),
            })
    
    def get_summary(self) -> Dict:
        """Tổng hợp metrics"""
        from collections import defaultdict
        
        by_name = defaultdict(list)
        for m in self.metrics:
            by_name[m.name].append(m.value)
        
        summary = {}
        for name, values in by_name.items():
            summary[name] = {
                "count": len(values),
                "avg": sum(values) / len(values),
                "min": min(values),
                "max": max(values),
                "latest": values[-1],
            }
        
        return {
            "metrics": summary,
            "alerts": self.alerts[-10:],  # Last 10 alerts
            "total_alerts": len(self.alerts),
        }


# Usage
dashboard = MonitoringDashboard()

# Set thresholds
dashboard.set_threshold("pipeline_duration_s", warning=300, critical=600)
dashboard.set_threshold("test_failure_rate", warning=5, critical=20)
dashboard.set_threshold("deploy_time_s", warning=120, critical=300)

# Record metrics
dashboard.record_metric("pipeline_duration_s", 45.2, "seconds")
dashboard.record_metric("test_failure_rate", 2.1, "percent")
dashboard.record_metric("deploy_time_s", 89.5, "seconds")

# Get summary
summary = dashboard.get_summary()
```

---

## Best Practices

```
┌──────────────────────────────────────────────────────────────────┐
│                AUTOMATION BEST PRACTICES                         │
│                                                                  │
│  1. START WITH REPETITIVE TASKS                                  │
│     Tự động hóa việc làm nhiều nhất trước                      │
│     → Max ROI, min effort                                       │
│                                                                  │
│  2. MAKE IT RELIABLE                                             │
│     Automation phải đáng tin cậy hơn manual                     │
│     → Retry + fallback + logging                                │
│                                                                  │
│  3. KEEP IT SIMPLE                                               │
│     Automation phức tạp = maintenance nightmare                  │
│     → Simple > Clever                                            │
│                                                                  │
│  4. MAKE IT VISIBLE                                              │
│     Mọi automation phải log + report                             │
│     → Không chạy ngầm không ai biết                             │
│                                                                  │
│  5. VERSION CONTROL EVERYTHING                                   │
│     CI/CD configs, hooks, scripts — tất cả vào Git             │
│     → Audit trail, rollback capability                          │
│                                                                  │
│  6. TEST YOUR AUTOMATION                                         │
│     Test automation pipelines trước khi deploy                  │
│     → Chicken-and-egg, nhưng cần thiết                         │
│                                                                  │
│  7. GRADUAL ADOPTION                                             │
│     Bắt đầu với lint + format, dần thêm test, deploy          │
│     → Incremental automation                                    │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## Tài Liệu Tham Khảo

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Pre-commit Hooks](https://pre-commit.com/)
- [APScheduler](https://apscheduler.readthedocs.io/)