# ⚙️ X. Automation

### Tại Sao Automation Quan Trọng?

> *"Automation không thay thế con người — nó thay thế những việc con người KHÔNG PHẢI làm."*

#### Bằng chứng nghiên cứu:

1. **GitHub (2025)**: Repositories với CI/CD automation giảm **60% deployment failures** và **44% time-to-merge**.
2. **DORA Report (2025)**: Top-performing teams automate **95%+ deployments** — họ deploy hàng trăm lần/ngày thay vì hàng tuần.
3. **Stripe Engineering (2024)**: Automated code review pipelines phát hiện **38% more bugs** trước khi reaches human reviewers.

#### Triết lý cốt lõi:

```
Automation = Repetition → Rule → Script → Self-Healing Pipeline
```

**Automation Maturity Model**:
- **Level 0**: Manual (mỗi lần làm tay)
- **Level 1**: Scripted (có script chạy lại được)
- **Level 2**: Triggered (tự chạy khi event xảy ra)
- **Level 3**: Self-healing (tự detect + fix issues)
- **Level 4**: Predictive (tự predict vấn đề trước khi xảy ra)

**Analogies**: Automation giống dây chuyền sản xuất — ban đầu từng bước làm tay, dần dần conveyor belt tự chạy, robot tự lắp ráp, và cuối cùng nhà máy tự hoạt động 24/7.

**Nếu bỏ qua**: Manual steps = bottlenecks, human errors mỗi lần lặp lại, team dành 40% thời gian cho repetitive tasks thay vì innovation.

## Tổng Quan

**Automation** trong AI coding là việc **tự động hóa các quy trình lặp lại** — từ code generation, testing, deployment, đến monitoring. Mục tiêu: giảm human intervention, tăng tốc độ, và đảm bảo consistency.

```
┌──────────────────────────────────────────────────────────────────┐
│                     AUTOMATION PIPELINE                           │
│                                                                  │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐ │
│  │  Trigger  │───►│  Process │───►│ Validate │───►│  Deploy  │ │
│  │           │    │  & Build │    │  & Test  │    │  & Ship  │ │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘ │
│       │               │               │               │         │
│  ┌────┴────┐    ┌────┴────┐    ┌────┴────┐    ┌────┴────┐    │
│  │ • Git   │    │ • AI    │    │ • Tests │    │ • CI/CD │    │
│  │ • Cron  │    │ • Build │    │ • Lint  │    │ • Docker│    │
│  │ • Event │    │ • Format│    │ • Type  │    │ • K8s   │    │
│  │ • Manual│    │ • Gen   │    │ • Audit │    │ • Notify│    │
│  └─────────┘    └─────────┘    └─────────┘    └─────────┘    │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## Nội Dung

| # | Chủ đề | Mô tả |
|---|--------|-------|
| 1 | [Automation Patterns](#1-automation-patterns) | Các pattern tự động hóa phổ biến |
| 2 | [CI/CD Pipelines](#2-cicd-pipelines) | Pipeline tích hợp liên tục |
| 3 | [Code Generation Automation](#3-code-generation-automation) | Tự động generate code |
| 4 | [Testing Automation](#4-testing-automation) | Tự động test |
| 5 | [Monitoring & Alerting](#5-monitoring--alerting) | Theo dõi và cảnh báo |
| 6 | [Self-Healing Systems](#6-self-healing-systems) | Hệ thống tự sửa chữa |
| 7 | [Scheduled Tasks](#7-scheduled-tasks) | Tác vụ định kỳ |
| 8 | [Workflow Templates](#8-workflow-templates) | Mẫu quy trình tự động |
| 9 | [Anti-Patterns & Solutions](#9-anti-patterns--solutions) | Các lỗi thường gặp |
| 10 | [Production Automation](#10-production-automation) | Tự động hóa sản phẩm |

---

## 1. Automation Patterns

### 1.1 Pattern Taxonomy

```
┌──────────────────────────────────────────────────────────────────┐
│                 AUTOMATION PATTERN TAXONOMY                       │
│                                                                  │
│  TRIGGER PATTERNS                                                │
│  ├── Event-driven    → Git push, webhook, API call              │
│  ├── Scheduled       → Cron, interval, time-based               │
│  ├── Conditional     → Threshold, anomaly, state change         │
│  └── Manual          → On-demand trigger                        │
│                                                                  │
│  PROCESS PATTERNS                                                │
│  ├── Pipeline        → Sequential steps with gates              │
│  ├── Fan-out/Fan-in  → Parallel processing + merge              │
│  ├── Saga            → Distributed transaction with rollback    │
│  └── Retry           → Automatic retry on failure               │
│                                                                  │
│  QUALITY PATTERNS                                                │
│  ├── Gate            → Must pass before proceeding               │
│  ├── Canary          → Deploy to small % first                  │
│  ├── Blue-Green      → Two environments, switch                 │
│  └── Feature Flag    → Toggle features without deploy           │
│                                                                  │
│  FEEDBACK PATTERNS                                               │
│  ├── Notification    → Alert humans when needed                 │
│  ├── Auto-rollback   → Revert on failure                        │
│  ├── Learning        → Improve from past results                │
│  └── Adaptive        → Adjust parameters dynamically            │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 1.2 Automation Framework

```python
from dataclasses import dataclass, field
from typing import Any, Callable, Dict, List, Optional
from enum import Enum
import time


class TriggerType(Enum):
    EVENT = "event"
    SCHEDULED = "scheduled"
    CONDITIONAL = "conditional"
    MANUAL = "manual"


class PipelineStatus(Enum):
    PENDING = "pending"
    RUNNING = "running"
    SUCCESS = "success"
    FAILED = "failed"
    ROLLED_BACK = "rolled_back"


@dataclass
class PipelineStep:
    """Một bước trong automation pipeline"""
    name: str
    handler: Callable
    retry_count: int = 2
    timeout_seconds: int = 300
    gate: bool = False              # True = must pass to continue
    rollback: Optional[Callable] = None
    on_failure: Optional[Callable] = None
    config: Dict[str, Any] = field(default_factory=dict)


@dataclass
class PipelineResult:
    """Kết quả chạy pipeline"""
    status: PipelineStatus
    steps_completed: List[str]
    failed_step: Optional[str] = None
    error_message: Optional[str] = None
    duration_seconds: float = 0.0
    artifacts: Dict[str, Any] = field(default_factory=dict)


class AutomationPipeline:
    """
    Framework cho automation pipelines với:
    - Sequential steps with gates
    - Automatic retry on failure
    - Rollback support
    - Duration tracking
    """
    
    def __init__(self, name: str):
        self.name = name
        self.steps: List[PipelineStep] = []
        self.pre_hooks: List[Callable] = []
        self.post_hooks: List[Callable] = []
        self.on_failure_hooks: List[Callable] = []
    
    def add_step(self, step: PipelineStep) -> "AutomationPipeline":
        """Thêm step vào pipeline (chainable)"""
        self.steps.append(step)
        return self
    
    def step(self, name: str, handler: Callable, 
             gate: bool = False, **kwargs) -> "AutomationPipeline":
        """Shorthand để thêm step"""
        self.steps.append(PipelineStep(
            name=name, handler=handler, gate=gate, **kwargs
        ))
        return self
    
    def before(self, hook: Callable) -> "AutomationPipeline":
        """Thêm pre-execution hook"""
        self.pre_hooks.append(hook)
        return self
    
    def after(self, hook: Callable) -> "AutomationPipeline":
        """Thêm post-execution hook"""
        self.post_hooks.append(hook)
        return self
    
    def on_failure(self, hook: Callable) -> "AutomationPipeline":
        """Thêm failure hook"""
        self.on_failure_hooks.append(hook)
        return self
    
    def execute(self, context: Dict[str, Any] = None) -> PipelineResult:
        """Execute toàn bộ pipeline"""
        context = context or {}
        start_time = time.time()
        completed_steps = []
        artifacts = {}
        
        # Run pre-hooks
        for hook in self.pre_hooks:
            hook(context)
        
        for step in self.steps:
            success = False
            last_error = None
            
            for attempt in range(step.retry_count + 1):
                try:
                    result = step.handler(context, artifacts)
                    artifacts[step.name] = result
                    completed_steps.append(step.name)
                    success = True
                    break
                except Exception as e:
                    last_error = str(e)
                    if attempt < step.retry_count:
                        time.sleep(1 * (attempt + 1))  # Backoff
            
            if not success:
                # Gate step failure = pipeline failure
                if step.gate:
                    for hook in self.on_failure_hooks:
                        hook(step.name, last_error, context)
                    
                    # Execute rollback for completed steps
                    for completed_name in reversed(completed_steps):
                        for s in self.steps:
                            if s.name == completed_name and s.rollback:
                                try:
                                    s.rollback(context, artifacts)
                                except Exception:
                                    pass
                    
                    return PipelineResult(
                        status=PipelineStatus.FAILED,
                        steps_completed=completed_steps,
                        failed_step=step.name,
                        error_message=last_error,
                        duration_seconds=time.time() - start_time,
                        artifacts=artifacts,
                    )
        
        # Run post-hooks
        for hook in self.post_hooks:
            hook(context, artifacts)
        
        return PipelineResult(
            status=PipelineStatus.SUCCESS,
            steps_completed=completed_steps,
            duration_seconds=time.time() - start_time,
            artifacts=artifacts,
        )
```

---

## 2. CI/CD Pipelines

### 2.1 CI/CD Pipeline Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                  CI/CD PIPELINE ARCHITECTURE                      │
│                                                                  │
│  CONTINUOUS INTEGRATION                                          │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                                                          │   │
│  │  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐       │   │
│  │  │  Lint  │─►│  Type  │─►│  Unit  │─►│ Build  │       │   │
│  │  │  Check │  │ Check  │  │ Tests  │  │        │       │   │
│  │  └────────┘  └────────┘  └────────┘  └────────┘       │   │
│  │      │           │           │            │              │   │
│  │    FAIL=stop    FAIL=stop   FAIL=stop    FAIL=stop      │   │
│  │                                                          │   │
│  └──────────────────────────────────────────────────────────┘   │
│                          │                                       │
│                          ▼                                       │
│  CONTINUOUS DELIVERY                                             │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                                                          │   │
│  │  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐       │   │
│  │  │Deploy  │─►│Integr. │─►│ E2E    │─►│Deploy  │       │   │
│  │  │Staging │  │ Tests  │  │ Tests  │  │Prod    │       │   │
│  │  └────────┘  └────────┘  └────────┘  └────────┘       │   │
│  │                                                          │   │
│  └──────────────────────────────────────────────────────────┘   │
│                          │                                       │
│                          ▼                                       │
│  CONTINUOUS MONITORING                                           │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                                                          │   │
│  │  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐       │   │
│  │  │Metrics │─►│Alerts  │─►│Log     │─►│Feedback│       │   │
│  │  │Collect │  │Check   │  │Analyze │  │Loop    │       │   │
│  │  └────────┘  └────────┘  └────────┘  └────────┘       │   │
│  │                                                          │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 2.2 GitHub Actions Templates

```yaml
# .github/workflows/ci.yml - Complete CI Pipeline
name: CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  PYTHON_VERSION: '3.11'
  NODE_VERSION: '20'

jobs:
  # ========== STAGE 1: VALIDATE ==========
  lint-and-type-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: ${{ env.PYTHON_VERSION }}
      
      - name: Install dependencies
        run: |
          pip install -e ".[dev]"
      
      - name: Lint (Ruff)
        run: ruff check . --output-format=github
      
      - name: Format check (Ruff)
        run: ruff format --check .
      
      - name: Type check (mypy)
        run: mypy src/ --ignore-missing-imports

  # ========== STAGE 2: TEST ==========
  unit-tests:
    needs: lint-and-type-check
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ['3.10', '3.11', '3.12']
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
      
      - name: Install dependencies
        run: pip install -e ".[dev]"
      
      - name: Run unit tests
        run: |
          pytest tests/unit/ \
            --cov=src/ \
            --cov-report=xml \
            --cov-report=html \
            -v
      
      - name: Upload coverage
        if: matrix.python-version == '3.11'
        uses: codecov/codecov-action@v3

  integration-tests:
    needs: unit-tests
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_DB: testdb
          POSTGRES_USER: test
          POSTGRES_PASSWORD: test
        ports:
          - 5432:5432
      redis:
        image: redis:7
        ports:
          - 6379:6379
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: ${{ env.PYTHON_VERSION }}
      
      - name: Install dependencies
        run: pip install -e ".[dev]"
      
      - name: Run integration tests
        env:
          DATABASE_URL: postgresql://test:test@localhost:5432/testdb
          REDIS_URL: redis://localhost:6379
        run: |
          pytest tests/integration/ -v --tb=short

  # ========== STAGE 3: BUILD ==========
  build:
    needs: integration-tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Build Docker image
        run: |
          docker build -t ${{ github.repository }}:${{ github.sha }} .
      
      - name: Push to registry
        if: github.ref == 'refs/heads/main'
        run: |
          echo "${{ secrets.REGISTRY_PASSWORD }}" | docker login -u "${{ secrets.REGISTRY_USER }}" --password-stdin
          docker push ${{ github.repository }}:${{ github.sha }}

  # ========== STAGE 4: SECURITY ==========
  security-scan:
    needs: lint-and-type-check
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run Snyk security scan
        uses: snyk/actions/python@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
      
      - name: Run dependency audit
        run: |
          pip install safety
          safety check --full-report

  # ========== STAGE 5: DEPLOY ==========
  deploy-staging:
    needs: [build, security-scan]
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - uses: actions/checkout@v4
      
      - name: Deploy to staging
        run: |
          echo "Deploying ${{ github.sha }} to staging..."
          # kubectl set image deployment/app app=${{ github.repository }}:${{ github.sha }}
      
      - name: Smoke test
        run: |
          sleep 30
          curl -f https://staging.example.com/health || exit 1
      
      - name: Run E2E tests
        run: |
          npm run test:e2e -- --baseUrl=https://staging.example.com

  deploy-production:
    needs: deploy-staging
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/checkout@v4
      
      - name: Deploy to production (canary)
        run: |
          echo "Canary deploy to 10% of traffic..."
          # kubectl set image deployment/app app=${{ github.sha }} --canary-weight=10
      
      - name: Monitor canary (5 min)
        run: sleep 300
      
      - name: Full production deploy
        run: |
          echo "Full rollout..."
          # kubectl set image deployment/app app=${{ github.sha }}
      
      - name: Notify team
        if: success()
        run: |
          curl -X POST "${{ secrets.SLACK_WEBHOOK }}" \
            -H 'Content-Type: application/json' \
            -d '{"text":"✅ Deployed ${{ github.sha }} to production"}'
```

### 2.3 Pipeline Configuration

```python
from dataclasses import dataclass, field
from typing import Dict, List, Optional


@dataclass
class CIPipelineConfig:
    """Configuration cho CI/CD pipeline"""
    
    # Language / Framework
    language: str = "python"
    python_version: str = "3.11"
    
    # Quality Gates
    lint_enabled: bool = True
    type_check_enabled: bool = True
    min_test_coverage: float = 80.0
    max_complexity: int = 10
    
    # Security
    security_scan: bool = True
    dependency_audit: bool = True
    
    # Build
    docker_enabled: bool = True
    docker_registry: str = "ghcr.io"
    
    # Deploy
    deploy_staging: bool = True
    deploy_production: bool = True
    canary_percentage: int = 10
    canary_duration_minutes: int = 5
    
    # Notifications
    slack_notify: bool = True
    email_on_failure: bool = True
    
    def to_github_actions(self) -> str:
        """Generate GitHub Actions workflow YAML"""
        lines = [
            "name: CI/CD Pipeline",
            "on:",
            "  push:",
            "    branches: [main]",
            "  pull_request:",
            "    branches: [main]",
            "",
            "jobs:",
            "  quality-gate:",
            "    runs-on: ubuntu-latest",
            "    steps:",
            "      - uses: actions/checkout@v4",
        ]
        
        if self.language == "python":
            lines.extend([
                f"      - uses: actions/setup-python@v5",
                f"        with:",
                f"          python-version: '{self.python_version}'",
                "      - run: pip install -e '.[dev]'",
            ])
            
            if self.lint_enabled:
                lines.append("      - run: ruff check .")
            if self.type_check_enabled:
                lines.append("      - run: mypy src/")
            
            lines.extend([
                f"      - run: pytest --cov=src/ --cov-fail-under={self.min_test_coverage}",
            ])
        
        return "\n".join(lines)
```

---

## 3. Code Generation Automation

### 3.1 Automated Code Generation

```python
from typing import Dict, List, Optional


class CodeGenerator:
    """
    Tự động generate code từ templates và specifications.
    
    Supports:
    - CRUD from database schema
    - API endpoints from OpenAPI spec
    - Tests from source code
    - Documentation from code comments
    """
    
    def __init__(self, template_dir: str = "./templates"):
        self.template_dir = template_dir
        self.templates: Dict[str, str] = {}
    
    def generate_crud(self, model_name: str, 
                      fields: Dict[str, str],
                      output_dir: str = "./generated") -> List[str]:
        """
        Generate CRUD operations từ model definition.
        
        Args:
            model_name: Name of the model (e.g., "User")
            fields: Dict of field_name -> field_type
            output_dir: Where to write generated files
            
        Returns:
            List of generated file paths
        """
        generated_files = []
        
        # Generate model
        model_code = self._generate_model(model_name, fields)
        model_path = f"{output_dir}/models/{model_name.lower()}.py"
        generated_files.append(model_path)
        
        # Generate schema
        schema_code = self._generate_schema(model_name, fields)
        schema_path = f"{output_dir}/schemas/{model_name.lower()}.py"
        generated_files.append(schema_path)
        
        # Generate service
        service_code = self._generate_service(model_name, fields)
        service_path = f"{output_dir}/services/{model_name.lower()}.py"
        generated_files.append(service_path)
        
        # Generate API routes
        route_code = self._generate_routes(model_name, fields)
        route_path = f"{output_dir}/routes/{model_name.lower()}.py"
        generated_files.append(route_path)
        
        # Generate tests
        test_code = self._generate_tests(model_name, fields)
        test_path = f"{output_dir}/tests/test_{model_name.lower()}.py"
        generated_files.append(test_path)
        
        return generated_files
    
    def _generate_model(self, name: str, 
                       fields: Dict[str, str]) -> str:
        """Generate SQLAlchemy model"""
        type_map = {
            "str": "String(255)",
            "int": "Integer",
            "float": "Float",
            "bool": "Boolean",
            "datetime": "DateTime",
            "text": "Text",
        }
        
        fields_code = []
        for fname, ftype in fields.items():
            sql_type = type_map.get(ftype, "String(255)")
            fields_code.append(
                f"    {fname} = Column({sql_type}, nullable=False)"
            )
        
        return f'''
from sqlalchemy import Column, Integer, DateTime
from sqlalchemy.sql import func
from app.database import Base


class {name}(Base):
    __tablename__ = "{name.lower()}s"
    
    id = Column(Integer, primary_key=True, index=True)
{chr(10).join(fields_code)}
    created_at = Column(DateTime, server_default=func.now())
    updated_at = Column(DateTime, onupdate=func.now())
'''
    
    def _generate_schema(self, name: str, 
                        fields: Dict[str, str]) -> str:
        """Generate Pydantic schema"""
        type_map = {
            "str": "str",
            "int": "int",
            "float": "float",
            "bool": "bool",
            "datetime": "datetime",
            "text": "str",
        }
        
        fields_code = []
        for fname, ftype in fields.items():
            py_type = type_map.get(ftype, "str")
            fields_code.append(f"    {fname}: {py_type}")
        
        return f'''
from pydantic import BaseModel
from datetime import datetime


class {name}Base(BaseModel):
{chr(10).join(fields_code)}


class {name}Create({name}Base):
    pass


class {name}Update(BaseModel):
{chr(10).join([f"    {f}: Optional[{type_map.get(t, 'str')}] = None" for f, t in fields.items()])}


class {name}Response({name}Base):
    id: int
    created_at: datetime
    
    class Config:
        from_attributes = True
'''
    
    def _generate_service(self, name: str, 
                         fields: Dict[str, str]) -> str:
        """Generate service layer"""
        return f'''
from typing import List, Optional
from app.models.{name.lower()} import {name}
from app.schemas.{name.lower()} import {name}Create, {name}Update
from sqlalchemy.orm import Session


class {name}Service:
    @staticmethod
    def get_all(db: Session, skip: int = 0, limit: int = 100) -> List[{name}]:
        return db.query({name}).offset(skip).limit(limit).all()
    
    @staticmethod
    def get_by_id(db: Session, id: int) -> Optional[{name}]:
        return db.query({name}).filter({name}.id == id).first()
    
    @staticmethod
    def create(db: Session, data: {name}Create) -> {name}:
        obj = {name}(**data.model_dump())
        db.add(obj)
        db.commit()
        db.refresh(obj)
        return obj
    
    @staticmethod
    def update(db: Session, id: int, data: {name}Update) -> Optional[{name}]:
        obj = db.query({name}).filter({name}.id == id).first()
        if not obj:
            return None
        for key, value in data.model_dump(exclude_unset=True).items():
            setattr(obj, key, value)
        db.commit()
        db.refresh(obj)
        return obj
    
    @staticmethod
    def delete(db: Session, id: int) -> bool:
        obj = db.query({name}).filter({name}.id == id).first()
        if not obj:
            return False
        db.delete(obj)
        db.commit()
        return True
'''
    
    def _generate_routes(self, name: str, 
                        fields: Dict[str, str]) -> str:
        """Generate FastAPI routes"""
        return f'''
from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.orm import Session
from app.database import get_db
from app.schemas.{name.lower()} import {name}Create, {name}Update, {name}Response
from app.services.{name.lower()} import {name}Service

router = APIRouter(prefix="/{name.lower()}s", tags=["{name.lower()}s"])


@router.get("/", response_model=List[{name}Response])
def list_{name.lower()}s(skip: int = 0, limit: int = 100, db: Session = Depends(get_db)):
    return {name}Service.get_all(db, skip, limit)


@router.get("/{{id}}", response_model={name}Response)
def get_{name.lower()}(id: int, db: Session = Depends(get_db)):
    obj = {name}Service.get_by_id(db, id)
    if not obj:
        raise HTTPException(status_code=404, detail="{name} not found")
    return obj


@router.post("/", response_model={name}Response, status_code=201)
def create_{name.lower()}(data: {name}Create, db: Session = Depends(get_db)):
    return {name}Service.create(db, data)


@router.patch("/{{id}}", response_model={name}Response)
def update_{name.lower()}(id: int, data: {name}Update, db: Session = Depends(get_db)):
    obj = {name}Service.update(db, id, data)
    if not obj:
        raise HTTPException(status_code=404, detail="{name} not found")
    return obj


@router.delete("/{{id}}", status_code=204)
def delete_{name.lower()}(id: int, db: Session = Depends(get_db)):
    if not {name}Service.delete(db, id):
        raise HTTPException(status_code=404, detail="{name} not found")
'''
    
    def _generate_tests(self, name: str, 
                       fields: Dict[str, str]) -> str:
        """Generate pytest tests"""
        sample_data = ", ".join([
            f'"{f}": "test_{f}"' if t == "str" else f'"{f}": 1'
            for f, t in fields.items()
        ])
        
        return f'''
import pytest
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)


class Test{name}CRUD:
    def test_create_{name.lower()}(self):
        response = client.post("/{name.lower()}s/", json={{{sample_data}}})
        assert response.status_code == 201
        data = response.json()
        assert "id" in data
    
    def test_get_{name.lower()}(self):
        # Create first
        create_resp = client.post("/{name.lower()}s/", json={{{sample_data}}})
        id = create_resp.json()["id"]
        
        response = client.get(f"/{name.lower()}s/{{id}}")
        assert response.status_code == 200
    
    def test_list_{name.lower()}s(self):
        response = client.get("/{name.lower()}s/")
        assert response.status_code == 200
        assert isinstance(response.json(), list)
    
    def test_delete_{name.lower()}(self):
        create_resp = client.post("/{name.lower()}s/", json={{{sample_data}}})
        id = create_resp.json()["id"]
        
        response = client.delete(f"/{name.lower()}s/{{id}}")
        assert response.status_code == 204
'''
```

---

## 4. Testing Automation

### 4.1 Test Automation Strategy

```
┌──────────────────────────────────────────────────────────────────┐
│               TEST AUTOMATION PYRAMID                             │
│                                                                  │
│                        /\                                        │
│                       /  \         E2E Tests                     │
│                      / E2E\       (Few, slow, expensive)        │
│                     /──────\                                     │
│                    /        \     Integration Tests              │
│                   / Integr.  \   (Some, moderate speed)          │
│                  /────────────\                                  │
│                 /              \  Unit Tests                     │
│                /   Unit Tests   \ (Many, fast, cheap)            │
│               /──────────────────\                               │
│                                                                  │
│  AUTOMATION TARGETS:                                             │
│  ├── Unit tests:     100% automated (fast feedback)             │
│  ├── Integration:    90% automated (API + DB)                    │
│  ├── E2E tests:      70% automated (critical paths only)        │
│  └── Performance:    50% automated (periodic runs)               │
│                                                                  │
│  CI GATES:                                                       │
│  ├── PR merge:    Unit + Integration pass, >80% coverage        │
│  ├── Staging:     All tests pass                                │
│  └── Production:  Smoke tests pass after deploy                 │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 4.2 Automated Test Runner

```python
from dataclasses import dataclass, field
from typing import Callable, Dict, List, Optional
from enum import Enum
import time


class TestCategory(Enum):
    UNIT = "unit"
    INTEGRATION = "integration"
    E2E = "e2e"
    PERFORMANCE = "performance"
    SECURITY = "security"


@dataclass
class TestCase:
    name: str
    category: TestCategory
    handler: Callable
    timeout_seconds: int = 60
    tags: List[str] = field(default_factory=list)
    depends_on: List[str] = field(default_factory=list)


@dataclass
class TestResult:
    test_name: str
    status: str            # passed, failed, skipped, error
    duration_seconds: float
    error_message: Optional[str] = None
    stdout: str = ""


class AutomatedTestRunner:
    """
    Automated test runner với parallel execution,
    categorization, và reporting.
    """
    
    def __init__(self):
        self.test_cases: List[TestCase] = []
        self.results: List[TestResult] = []
        self.coverage_threshold: float = 80.0
    
    def register(self, test: TestCase):
        self.test_cases.append(test)
    
    def run(self, categories: List[TestCategory] = None,
            tags: List[str] = None) -> Dict:
        """Run tests with optional filtering"""
        
        # Filter tests
        tests_to_run = self.test_cases
        if categories:
            tests_to_run = [t for t in tests_to_run if t.category in categories]
        if tags:
            tests_to_run = [t for t in tests_to_run 
                           if any(tag in t.tags for tag in tags)]
        
        self.results = []
        total_start = time.time()
        
        for test in tests_to_run:
            start = time.time()
            try:
                test.handler()
                self.results.append(TestResult(
                    test_name=test.name,
                    status="passed",
                    duration_seconds=time.time() - start,
                ))
            except AssertionError as e:
                self.results.append(TestResult(
                    test_name=test.name,
                    status="failed",
                    duration_seconds=time.time() - start,
                    error_message=str(e),
                ))
            except Exception as e:
                self.results.append(TestResult(
                    test_name=test.name,
                    status="error",
                    duration_seconds=time.time() - start,
                    error_message=str(e),
                ))
        
        total_duration = time.time() - total_start
        
        return self._generate_report(total_duration)
    
    def _generate_report(self, total_duration: float) -> Dict:
        """Generate test report"""
        passed = sum(1 for r in self.results if r.status == "passed")
        failed = sum(1 for r in self.results if r.status == "failed")
        errors = sum(1 for r in self.results if r.status == "error")
        total = len(self.results)
        
        return {
            "total": total,
            "passed": passed,
            "failed": failed,
            "errors": errors,
            "pass_rate": (passed / total * 100) if total > 0 else 0,
            "duration_seconds": total_duration,
            "results": [
                {
                    "name": r.test_name,
                    "status": r.status,
                    "duration": r.duration_seconds,
                    "error": r.error_message,
                }
                for r in self.results
            ],
            "gate_passed": failed == 0 and errors == 0,
        }
```

---

## 5. Monitoring & Alerting

### 5.1 Monitoring Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│              MONITORING & ALERTING ARCHITECTURE                    │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    DATA COLLECTION                        │   │
│  │                                                          │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌────────┐ │   │
│  │  │ Metrics  │  │   Logs   │  │  Traces  │  │ Events │ │   │
│  │  │(Promethe)│  │(ELK/Loki)│  │(Jaeger)  │  │(custom)│ │   │
│  │  └────┬─────┘  └────┬─────┘  └────┬─────┘  └───┬────┘ │   │
│  │       └──────────────┴──────────────┴────────────┘      │   │
│  └──────────────────────────┬───────────────────────────────┘   │
│                              ▼                                   │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    PROCESSING                             │   │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │   │
│  │  │   Aggregate  │  │  Analyze &   │  │    Detect     │  │   │
│  │  │   & Store    │  │  Dashboard   │  │   Anomalies   │  │   │
│  │  └──────────────┘  └──────────────┘  └──────────────┘  │   │
│  └──────────────────────────┬───────────────────────────────┘   │
│                              ▼                                   │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    ALERTING                               │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐              │   │
│  │  │  Slack   │  │  Email   │  │  PagerDuty│              │   │
│  │  │  Alert   │  │  Alert   │  │  Alert    │              │   │
│  │  └──────────┘  └──────────┘  └──────────┘              │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 5.2 Monitoring System

```python
from dataclasses import dataclass, field
from typing import Any, Callable, Dict, List, Optional
from datetime import datetime
from enum import Enum
import statistics


class AlertSeverity(Enum):
    INFO = "info"
    WARNING = "warning"
    ERROR = "error"
    CRITICAL = "critical"


class MetricType(Enum):
    COUNTER = "counter"
    GAUGE = "gauge"
    HISTOGRAM = "histogram"


@dataclass
class AlertRule:
    """Rule cho alerting"""
    name: str
    metric_name: str
    condition: str      # "gt", "lt", "eq"
    threshold: float
    severity: AlertSeverity
    message_template: str
    cooldown_seconds: int = 300
    last_triggered: Optional[str] = None


@dataclass
class MetricPoint:
    name: str
    value: float
    timestamp: str
    tags: Dict[str, str] = field(default_factory=dict)


class MonitoringSystem:
    """
    Lightweight monitoring system cho AI coding pipelines.
    Tracks metrics, detects anomalies, và fires alerts.
    """
    
    def __init__(self):
        self.metrics: List[MetricPoint] = []
        self.alert_rules: List[AlertRule] = []
        self.alert_handlers: List[Callable] = []
    
    def record_metric(self, name: str, value: float,
                      tags: Dict[str, str] = None):
        """Ghi metric point"""
        self.metrics.append(MetricPoint(
            name=name,
            value=value,
            timestamp=datetime.now().isoformat(),
            tags=tags or {},
        ))
        
        # Check alert rules
        self._check_alerts(name, value)
    
    def add_alert_rule(self, rule: AlertRule):
        """Thêm alert rule"""
        self.alert_rules.append(rule)
    
    def on_alert(self, handler: Callable):
        """Register alert handler"""
        self.alert_handlers.append(handler)
    
    def get_metric_stats(self, metric_name: str,
                         window_minutes: int = 60) -> Dict:
        """Tính statistics cho metric trong time window"""
        cutoff = datetime.now()
        relevant = [
            m for m in self.metrics
            if m.name == metric_name
        ]
        
        if not relevant:
            return {"error": "No data"}
        
        values = [m.value for m in relevant]
        
        return {
            "count": len(values),
            "min": min(values),
            "max": max(values),
            "mean": statistics.mean(values),
            "median": statistics.median(values),
            "stdev": statistics.stdev(values) if len(values) > 1 else 0,
            "p95": sorted(values)[int(len(values) * 0.95)] if len(values) >= 20 else max(values),
        }
    
    def _check_alerts(self, metric_name: str, value: float):
        """Kiểm tra alert rules"""
        for rule in self.alert_rules:
            if rule.metric_name != metric_name:
                continue
            
            triggered = False
            if rule.condition == "gt" and value > rule.threshold:
                triggered = True
            elif rule.condition == "lt" and value < rule.threshold:
                triggered = True
            elif rule.condition == "eq" and value == rule.threshold:
                triggered = True
            
            if triggered:
                alert = {
                    "rule": rule.name,
                    "severity": rule.severity.value,
                    "metric": metric_name,
                    "value": value,
                    "threshold": rule.threshold,
                    "message": rule.message_template.format(
                        value=value, threshold=rule.threshold
                    ),
                    "timestamp": datetime.now().isoformat(),
                }
                
                for handler in self.alert_handlers:
                    handler(alert)
    
    def generate_dashboard(self) -> str:
        """Tạo text dashboard"""
        metric_names = set(m.name for m in self.metrics)
        
        lines = [
            "=" * 60,
            "  MONITORING DASHBOARD",
            "=" * 60,
            "",
        ]
        
        for name in sorted(metric_names):
            stats = self.get_metric_stats(name)
            lines.append(f"  📊 {name}")
            lines.append(f"     Count: {stats.get('count', 0)}")
            lines.append(f"     Mean:  {stats.get('mean', 0):.2f}")
            lines.append(f"     Min:   {stats.get('min', 0):.2f}")
            lines.append(f"     Max:   {stats.get('max', 0):.2f}")
            lines.append("")
        
        return "\n".join(lines)
```

---

## 6. Self-Healing Systems

### 6.1 Self-Healing Patterns

```
┌──────────────────────────────────────────────────────────────────┐
│              SELF-HEALING PATTERNS                                │
│                                                                  │
│  PATTERN 1: AUTO-RESTART                                         │
│  ┌──────────┐  fail  ┌──────────┐  restart  ┌──────────┐      │
│  │  Service │───────►│  Detect  │──────────►│  Restart │      │
│  │  Running │        │  Failure │           │  Service │      │
│  └──────────┘        └──────────┘           └──────────┘      │
│  Trigger: Process exit, health check fail                       │
│  Action: Restart process/service                                │
│                                                                  │
│  PATTERN 2: AUTO-ROLLBACK                                        │
│  ┌──────────┐  error  ┌──────────┐  revert  ┌──────────┐     │
│  │  Deploy  │────────►│  Monitor │─────────►│ Rollback │     │
│  │  v2.0    │         │  Metrics │          │  to v1.9 │     │
│  └──────────┘         └──────────┘          └──────────┘     │
│  Trigger: Error rate > threshold, latency spike                │
│  Action: Revert to last known good version                     │
│                                                                  │
│  PATTERN 3: AUTO-SCALE                                          │
│  ┌──────────┐  load   ┌──────────┐  scale  ┌──────────┐      │
│  │  Traffic │───────►│  Monitor │────────►│ Add/Remove│      │
│  │  Spike   │        │  Load    │         │ Instances │      │
│  └──────────┘        └──────────┘         └──────────┘      │
│  Trigger: CPU > 80%, queue depth > threshold                   │
│  Action: Add/remove instances                                  │
│                                                                  │
│  PATTERN 4: AUTO-RETRY WITH BACKOFF                             │
│  ┌──────────┐  fail   ┌──────────┐  wait   ┌──────────┐      │
│  │  API     │───────►│  Failure │───────►│  Retry   │      │
│  │  Call    │         │  Detected│        │  (exp)   │      │
│  └──────────┘         └──────────┘        └──────────┘      │
│  Trigger: Transient error (network, timeout)                   │
│  Action: Retry with exponential backoff                        │
│                                                                  │
│  PATTERN 5: CIRCUIT BREAKER                                    │
│  ┌──────────┐  fail>5 ┌──────────┐  wait   ┌──────────┐      │
│  │  Service │───────►│  Circuit │───────►│ Half-Open│      │
│  │  Call    │         │  OPEN    │        │  Test    │      │
│  └──────────┘         └──────────┘        └──────────┘      │
│  Trigger: Consecutive failures > threshold                     │
│  Action: Stop calling, periodically test recovery              │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 6.2 Self-Healing Implementation

```python
from dataclasses import dataclass, field
from typing import Callable, Dict, List, Optional
from datetime import datetime
import time
import random


class CircuitState(Enum):
    CLOSED = "closed"       # Normal operation
    OPEN = "open"           # Failing, reject calls
    HALF_OPEN = "half_open" # Testing recovery


@dataclass
class HealthCheck:
    name: str
    check_fn: Callable
    interval_seconds: int = 30
    timeout_seconds: int = 10
    last_check: Optional[str] = None
    last_status: str = "unknown"
    failure_count: int = 0


class SelfHealingSystem:
    """
    Self-healing system với health checks,
    circuit breaker, và auto-recovery.
    """
    
    def __init__(self):
        self.health_checks: Dict[str, HealthCheck] = {}
        self.circuit_breakers: Dict[str, CircuitState] = {}
        self.failure_counts: Dict[str, int] = {}
        self.recovery_handlers: Dict[str, Callable] = {}
        self.max_failures = 5
        self.circuit_open_duration = 60  # seconds
    
    def register_health_check(self, name: str, check_fn: Callable,
                               interval: int = 30):
        """Register health check"""
        self.health_checks[name] = HealthCheck(
            name=name,
            check_fn=check_fn,
            interval_seconds=interval,
        )
    
    def register_recovery(self, name: str, recovery_fn: Callable):
        """Register recovery handler"""
        self.recovery_handlers[name] = recovery_fn
    
    def check_health(self, name: str) -> Dict:
        """Run health check"""
        check = self.health_checks.get(name)
        if not check:
            return {"status": "error", "message": "Check not found"}
        
        try:
            result = check.check_fn()
            check.last_status = "healthy"
            check.failure_count = 0
            check.last_check = datetime.now().isoformat()
            
            # Reset circuit breaker
            self.circuit_breakers[name] = CircuitState.CLOSED
            self.failure_counts[name] = 0
            
            return {"status": "healthy", "result": result}
            
        except Exception as e:
            check.failure_count += 1
            check.last_status = "unhealthy"
            check.last_check = datetime.now().isoformat()
            
            self.failure_counts[name] = self.failure_counts.get(name, 0) + 1
            
            # Check if should open circuit
            if self.failure_counts[name] >= self.max_failures:
                self.circuit_breakers[name] = CircuitState.OPEN
                self._attempt_recovery(name)
            
            return {
                "status": "unhealthy",
                "error": str(e),
                "failure_count": self.failure_counts[name],
            }
    
    def allow_request(self, name: str) -> bool:
        """Circuit breaker: check if request should be allowed"""
        state = self.circuit_breakers.get(name, CircuitState.CLOSED)
        
        if state == CircuitState.CLOSED:
            return True
        elif state == CircuitState.OPEN:
            # After cooldown, try half-open
            self.circuit_breakers[name] = CircuitState.HALF_OPEN
            return True  # Allow one test request
        elif state == CircuitState.HALF_OPEN:
            return True  # Allow test request
        
        return False
    
    def _attempt_recovery(self, name: str):
        """Attempt automatic recovery"""
        handler = self.recovery_handlers.get(name)
        if handler:
            try:
                handler()
            except Exception:
                pass  # Recovery failed, will retry later
    
    def get_system_health(self) -> str:
        """System health report"""
        lines = ["=== System Health ==="]
        
        for name, check in self.health_checks.items():
            circuit = self.circuit_breakers.get(name, CircuitState.CLOSED)
            icon = "✅" if check.last_status == "healthy" else "❌"
            lines.append(
                f"  {icon} {name}: {check.last_status} "
                f"[circuit={circuit.value}] "
                f"failures={check.failure_count}"
            )
        
        return "\n".join(lines)
```

---

## 7. Scheduled Tasks

### 7.1 Task Scheduler

```python
from dataclasses import dataclass, field
from typing import Callable, Dict, List, Optional
from datetime import datetime, timedelta
from enum import Enum


class ScheduleType(Enum):
    ONCE = "once"
    INTERVAL = "interval"
    DAILY = "daily"
    WEEKLY = "weekly"


@dataclass
class ScheduledTask:
    name: str
    handler: Callable
    schedule_type: ScheduleType
    interval_seconds: int = 3600
    enabled: bool = True
    last_run: Optional[str] = None
    next_run: Optional[str] = None
    run_count: int = 0
    failure_count: int = 0


class TaskScheduler:
    """
    Lightweight task scheduler cho automation.
    Supports interval, daily, weekly scheduling.
    """
    
    def __init__(self):
        self.tasks: Dict[str, ScheduledTask] = {}
        self.execution_log: List[Dict] = []
    
    def schedule(self, name: str, handler: Callable,
                 schedule_type: ScheduleType,
                 interval_seconds: int = 3600) -> ScheduledTask:
        """Schedule a task"""
        task = ScheduledTask(
            name=name,
            handler=handler,
            schedule_type=schedule_type,
            interval_seconds=interval_seconds,
            next_run=datetime.now().isoformat(),
        )
        self.tasks[name] = task
        return task
    
    def run_pending(self) -> List[Dict]:
        """Run all tasks that are due"""
        now = datetime.now()
        results = []
        
        for name, task in self.tasks.items():
            if not task.enabled:
                continue
            if not task.next_run:
                continue
            
            next_run = datetime.fromisoformat(task.next_run)
            if now < next_run:
                continue
            
            # Execute
            start = time.time()
            try:
                task.handler()
                duration = time.time() - start
                
                task.last_run = now.isoformat()
                task.next_run = self._calculate_next(task, now).isoformat()
                task.run_count += 1
                
                results.append({
                    "task": name,
                    "status": "success",
                    "duration": duration,
                })
                
            except Exception as e:
                task.failure_count += 1
                task.last_run = now.isoformat()
                task.next_run = self._calculate_next(task, now).isoformat()
                
                results.append({
                    "task": name,
                    "status": "failed",
                    "error": str(e),
                })
        
        self.execution_log.extend(results)
        return results
    
    def _calculate_next(self, task: ScheduledTask, 
                       now: datetime) -> datetime:
        """Calculate next run time"""
        if task.schedule_type == ScheduleType.INTERVAL:
            return now + timedelta(seconds=task.interval_seconds)
        elif task.schedule_type == ScheduleType.DAILY:
            return now + timedelta(days=1)
        elif task.schedule_type == ScheduleType.WEEKLY:
            return now + timedelta(weeks=1)
        else:
            return now + timedelta(days=365)  # Effectively never
    
    def get_schedule_report(self) -> str:
        """Báo cáo lịch trình"""
        lines = ["=== Scheduled Tasks ==="]
        for name, task in self.tasks.items():
            status = "✅" if task.enabled else "⏸️"
            lines.append(
                f"  {status} {name} [{task.schedule_type.value}] "
                f"runs={task.run_count} "
                f"failures={task.failure_count} "
                f"next={task.next_run}"
            )
        return "\n".join(lines)
```

---

## 8. Workflow Templates

### 8.1 Common Automation Workflows

```python
WORKFLOW_TEMPLATES = {
    "feature_pipeline": {
        "description": "Full feature development automation",
        "steps": [
            {"name": "lint", "command": "ruff check .", "gate": True},
            {"name": "typecheck", "command": "mypy src/", "gate": True},
            {"name": "unit_tests", "command": "pytest tests/unit/ -v", "gate": True},
            {"name": "integration_tests", "command": "pytest tests/integration/ -v", "gate": True},
            {"name": "build", "command": "docker build -t app .", "gate": True},
            {"name": "deploy_staging", "command": "kubectl apply -f k8s/staging/", "gate": False},
            {"name": "smoke_test", "command": "curl -f http://staging/health", "gate": True},
            {"name": "deploy_prod", "command": "kubectl apply -f k8s/prod/", "gate": True},
        ],
    },
    
    "hotfix_pipeline": {
        "description": "Emergency hotfix — fast track",
        "steps": [
            {"name": "lint", "command": "ruff check .", "gate": True},
            {"name": "quick_test", "command": "pytest tests/unit/ -x -q", "gate": True},
            {"name": "deploy_prod", "command": "kubectl apply -f k8s/prod/", "gate": True},
            {"name": "verify", "command": "curl -f http://prod/health", "gate": True},
            {"name": "notify", "command": "echo 'Hotfix deployed'", "gate": False},
        ],
    },
    
    "nightly_maintenance": {
        "description": "Nightly maintenance tasks",
        "steps": [
            {"name": "backup_db", "command": "pg_dump app > backup.sql", "gate": True},
            {"name": "cleanup_logs", "command": "find /var/log -mtime +30 -delete", "gate": False},
            {"name": "security_scan", "command": "safety check", "gate": False},
            {"name": "dependency_update", "command": "pip install --upgrade -r requirements.txt", "gate": False},
            {"name": "full_test_suite", "command": "pytest --cov=src/", "gate": False},
        ],
    },
    
    "pr_quality_check": {
        "description": "Automated PR quality gate",
        "steps": [
            {"name": "lint", "command": "ruff check --output-format=github", "gate": True},
            {"name": "format_check", "command": "ruff format --check", "gate": True},
            {"name": "type_check", "command": "mypy src/", "gate": True},
            {"name": "unit_tests", "command": "pytest tests/unit/ --cov=src/ --cov-fail-under=80", "gate": True},
            {"name": "security_scan", "command": "bandit -r src/", "gate": False},
        ],
    },
}
```

---

## 9. Anti-Patterns & Solutions

### 9.1 Common Anti-Patterns

```
┌──────────────────────────────────────────────────────────────────┐
│           AUTOMATION ANTI-PATTERNS                                │
│                                                                  │
│  ❌ ANTI-PATTERN 1: FRAGILE PIPELINES                            │
│     Pipeline fails on minor config changes                       │
│     → SOLUTION: Version control pipeline configs, test changes  │
│                                                                  │
│  ❌ ANTI-PATTERN 2: SILENT FAILURES                              │
│     Pipeline reports success but deploy is broken                │
│     → SOLUTION: Add post-deploy verification, smoke tests       │
│                                                                  │
│  ❌ ANTI-PATTERN 3: CREDENTIALS IN CODE                          │
│     Hardcoded secrets in pipeline configs                        │
│     → SOLUTION: Use secret managers (Vault, GitHub Secrets)     │
│                                                                  │
│  ❌ ANTI-PATTERN 4: NO ROLLBACK PLAN                             │
│     Deploy fails, no way to revert                               │
│     → SOLUTION: Blue-green deploys, auto-rollback on failure    │
│                                                                  │
│  ❌ ANTI-PATTERN 5: TOO LONG PIPELINES                           │
│     CI takes 30+ minutes → developers ignore results            │
│     → SOLUTION: Parallelize, split into stages, cache deps      │
│                                                                  │
│  ❌ ANTI-PATTERN 6: FLAKY TESTS                                  │
│     Tests randomly fail → developers skip them                   │
│     → SOLUTION: Fix flaky tests, quarantine unreliable tests    │
│                                                                  │
│  ❌ ANTI-PATTERN 7: NO MONITORING                                │
│     Deploy succeeds but service is degraded                      │
│     → SOLUTION: Health checks, metrics, alerting                │
│                                                                  │
│  ❌ ANTI-PATTERN 8: MANUAL DEPLOY STEPS                          │
│     "Just click this button" → human error                       │
│     → SOLUTION: Fully automated deploy pipeline                 │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 10. Production Automation

### 10.1 Production Checklist

```
┌──────────────────────────────────────────────────────────────────┐
│            PRODUCTION AUTOMATION CHECKLIST                        │
│                                                                  │
│  CI/CD PIPELINE:                                                 │
│  □ Lint + type check on every PR                                │
│  □ Unit tests with >80% coverage gate                           │
│  □ Integration tests on staging                                 │
│  □ Automated build + push to registry                           │
│  □ Canary deploy before full rollout                            │
│  □ Auto-rollback on health check failure                        │
│                                                                  │
│  MONITORING:                                                     │
│  □ Application metrics (latency, errors, throughput)            │
│  □ Infrastructure metrics (CPU, memory, disk)                   │
│  □ Log aggregation (structured logging)                         │
│  □ Distributed tracing                                          │
│  □ Alert rules for critical metrics                             │
│                                                                  │
│  SELF-HEALING:                                                   │
│  □ Health checks (liveness + readiness)                         │
│  □ Auto-restart on crash                                        │
│  □ Circuit breakers for external dependencies                   │
│  □ Rate limiting                                                │
│  □ Graceful degradation                                         │
│                                                                  │
│  SCHEDULED TASKS:                                                │
│  □ Database backups (daily)                                     │
│  □ Log cleanup (weekly)                                         │
│  □ Security scans (weekly)                                      │
│  □ Dependency updates (monthly)                                 │
│  □ Performance benchmarks (nightly)                             │
│                                                                  │
│  SECURITY:                                                       │
│  □ Secrets in secret manager (not in code)                      │
│  □ Dependency vulnerability scanning                            │
│  □ SAST/DAST scanning in pipeline                               │
│  □ Access controls for deploy                                   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## Best Practices

```
┌──────────────────────────────────────────────────────────────────┐
│              AUTOMATION BEST PRACTICES                            │
│                                                                  │
│  1. AUTOMATE THE REPEATABLE                                      │
│     If you do it more than twice → automate it                  │
│     Focus on high-value, high-frequency tasks                    │
│                                                                  │
│  2. FAIL FAST                                                    │
│     Run cheapest checks first (lint → test → build)             │
│     Gate critical steps (no deploy without tests passing)       │
│                                                                  │
│  3. MAKE IT REVERSIBLE                                           │
│     Every deploy must have a rollback plan                      │
│     Blue-green or canary deployments                            │
│                                                                  │
│  4. OBSERVE EVERYTHING                                           │
│     If it's not monitored, it's not production-ready            │
│     Log, measure, alert on everything important                 │
│                                                                  │
│  5. KEEP PIPELINES FAST                                          │
│     Target: <10 min for CI, <30 min for CD                      │
│     Parallelize, cache dependencies, skip unnecessary steps     │
│                                                                  │
│  6. VERSION CONTROL EVERYTHING                                   │
│     Pipeline configs in repo (IaC)                              │
│     Infrastructure as Code (Terraform, Pulumi)                  │
│                                                                  │
│  7. TEST THE AUTOMATION                                          │
│     Test pipeline configs before merging                         │
│     Chaos testing for self-healing                               │
│                                                                  │
│  8. NOTIFICATION WITHOUT NOISE                                   │
│     Alert on actionable items only                               │
│     Different channels for different severity                   │
│                                                                  │
│  9. DOCUMENT RUNBOOKS                                            │
│     Every alert should have a runbook                            │
│     Automate the runbook if possible                             │
│                                                                  │
│  10. ITERATE AND IMPROVE                                         │
│      Review pipeline metrics weekly                             │
│      Remove bottlenecks, add value                               │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

---

## 11. Case Studies Thực Tế

### 11.1 SWE-agent: Automated Software Engineering

```
┌──────────────────────────────────────────────────────────────────┐
│                    SWE-AGENT ARCHITECTURE                         │
│                                                                  │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐       │
│  │  GitHub Issue │───►│  SWE-agent   │───►│  Patch File  │       │
│  │  (Problem)    │    │  (LLM Agent) │    │  (Solution)  │       │
│  └──────────────┘    └──────┬───────┘    └──────────────┘       │
│                             │                                     │
│                    ┌────────┴────────┐                            │
│                    │                 │                             │
│              ┌─────▼─────┐   ┌─────▼─────┐                     │
│              │  Search    │   │  Edit      │                     │
│              │  Codebase  │   │  Files     │                     │
│              │  (Read)    │   │  (Write)   │                     │
│              └───────────┘   └───────────┘                     │
│                                                                  │
│  KEY INSIGHT: Agent cần ability để navigate codebase,           │
│ 不是 just generate code. Autonomy loop:                          │
│  Observe → Think → Act → Observe → ...                          │
│                                                                  │
│  PERFORMANCE (SWE-bench):                                        │
│  ├── Resolved: 12.47% (full) → 24.07% (with LSP)              │
│  ├── Median steps: 6-8 edits per issue                          │
│  └── Average tokens: 15K-25K per issue                          │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 11.2 Anthropic's Claude Code Automation

```
┌──────────────────────────────────────────────────────────────────┐
│                CLAUDE CODE AUTOMATION STRATEGY                    │
│                                                                  │
│  PRINCIPLE 1: "Model is 99% of the solution"                    │
│  → Focus on prompt engineering, not complex tooling             │
│  → Simple agent loop beats complex orchestrations               │
│                                                                  │
│  PRINCIPLE 2: "Let the model do the work"                       │
│  → Model decides what files to read/edit                         │
│  → Model writes its own sub-agents (Task tool)                  │
│  → Model uses grep/ripgrep to navigate codebase                 │
│                                                                  │
│  PRINCIPLE 3: "Graceful degradation"                            │
│  → Try without MCP → Add MCP if stuck                          │
│  → Try single agent → Escalate to sub-agents                    │
│  → Try local context → Fetch from remote if needed             │
│                                                                  │
│  AUTOMATION DECISIONS:                                           │
│  ├── Which files to read → Model decides (not rules)           │
│  ├── When to stop reading → Model decides (not timeout)        │
│  ├── When to ask user → Model decides (not always)             │
│  └── When to give up → Model decides (not hardcoded)           │
│                                                                  │
│  RESULT: 200K+ lines of source code navigated by single model  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 11.3 Cursor IDE: AI-Native Development

```
┌──────────────────────────────────────────────────────────────────┐
│                  CURSOR AUTOMATION PIPELINE                       │
│                                                                  │
│  TAB COMPLETION (Autocomplete):                                  │
│  ├── Trigger: Every keystroke                                   │
│  ├── Speed: <100ms response time                               │
│  ├── Context: Current file + imports + open files               │
│  └── Pattern: Predict next edit, not next token                │
│                                                                  │
│  INLINE EDIT (Cmd+K):                                           │
│  ├── Trigger: User selection + natural language instruction     │
│  ├── Context: Selected code + surrounding context               │
│  ├── Pattern: Edit-in-place with diff preview                   │
│  └── Quality: One-shot generation, minimal iteration           │
│                                                                  │
│  CHAT (Cmd+L):                                                  │
│  ├── Trigger: User question with codebase context               │
│  ├── Context: @-mentioned files + chat history                  │
│  ├── Pattern: Multi-turn conversation with file references      │
│  └── Scope: Can span multiple files and files                  │
│                                                                  │
│  AUTOMATION HIGHLIGHT:                                           │
│  → Predict edits (not just tokens) for autocomplete            │
│  → Small, focused context windows per feature                   │
│  → Local indexing + remote LLM hybrid approach                 │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 11.4 GitHub Copilot: Enterprise Automation

```
┌──────────────────────────────────────────────────────────────────┐
│               GITHUB COPILOT AUTOMATION LAYERS                    │
│                                                                  │
│  LAYER 1: INLINE COMPLETION                                      │
│  ├── Context: Current file + nearby files                       │
│  ├── Model: GPT-4o / Codex                                     │
│  ├── Latency: <500ms                                            │
│  └── Automation: Real-time suggestion on every line            │
│                                                                  │
│  LAYER 2: CHAT IN IDE                                           │
│  ├── Context: Entire workspace (via indexing)                   │
│  ├── Pattern: Natural language → Code generation               │
│  └── Automation: @workspace for full codebase access           │
│                                                                  │
│  LAYER 3: PULL REQUEST AUTOMATION                               │
│  ├── Copilot for PRs: Auto-generate PR descriptions            │
│  ├── Copilot Code Review: AI reviewer for PRs                  │
│  ├── Copilot Autofix: Auto-fix security vulnerabilities        │
│  └── Automation: CI/CD integration with GitHub Actions         │
│                                                                  │
│  LAYER 4: COPILOT WORKSPACES (Beta)                             │
│  ├── Task assignment to AI agent                                │
│  ├── Autonomous code changes with PR creation                   │
│  ├── Multi-file refactoring capabilities                        │
│  └── Human-in-the-loop review before merge                     │
│                                                                  │
│  SCALE: 77% of Fortune 100 uses Copilot                        │
│  IMPACT: 55% faster task completion, 46% more code completed   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 11.5 Vercel v0: Full-Stack Automation

```
┌──────────────────────────────────────────────────────────────────┐
│                    V0 AUTOMATION PATTERN                          │
│                                                                  │
│  INPUT: Natural language → FULL-STACK APP                        │
│                                                                  │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐ │
│  │  Prompt   │───►│ Generate │───►│  Preview │───►│ Iterate  │ │
│  │  (NL)     │    │  Code    │    │  (Live)  │    │ (Edit)   │ │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘ │
│       │               │               │               │         │
│  ┌────┴────┐    ┌────┴────┐    ┌────┴────┐    ┌────┴────┐    │
│  │ • Text  │    │ • React │    │ • URL   │    │ • Chat  │    │
│  │ • Image │    │ • SQL   │    │ • Code  │    │ • Ref   │    │
│  │ • Figma │    │ • API   │    │ • Diff  │    │ • Undo  │    │
│  └─────────┘    └─────────┘    └─────────┘    └─────────┘    │
│                                                                  │
│  AUTOMATION HIGHLIGHTS:                                          │
│  → Scaffolding to production-ready in <5 min                   │
│  → Automatic: Component gen → Styling → DB schema → API       │
│  → Real-time streaming preview during generation               │
│  → Iterative refinement via natural language                   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 12. TypeScript Interfaces cho Automation

### 12.1 Core Automation Types

```typescript
// ═══════════════════════════════════════════════════════════════
// AUTOMATION TYPES — Production-grade interfaces cho automation systems
// ═══════════════════════════════════════════════════════════════

/**
 * Pipeline configuration — How an automation pipeline is structured
 */
interface PipelineConfig {
  name: string;
  description: string;
  version: string;
  steps: PipelineStepConfig[];
  triggers: TriggerConfig[];
  gates: QualityGateConfig[];
  notifications: NotificationConfig;
  settings: PipelineSettings;
}

interface PipelineStepConfig {
  id: string;
  name: string;
  type: 'build' | 'test' | 'deploy' | 'security' | 'custom';
  handler: string;  // function reference or module path
  timeout: number;  // milliseconds
  retries: number;
  retryDelay: number;
  rollback?: string;
  dependencies: string[];  // step IDs this depends on
  condition?: StepCondition;
  artifacts?: ArtifactConfig[];
}

interface TriggerConfig {
  type: 'push' | 'pull_request' | 'schedule' | 'webhook' | 'manual';
  branches?: string[];
  paths?: string[];
  schedule?: string;  // cron expression
  webhookUrl?: string;
  events?: string[];
}

interface QualityGateConfig {
  name: string;
  type: 'coverage' | 'complexity' | 'security' | 'performance' | 'custom';
  threshold: number;
  operator: 'gt' | 'lt' | 'gte' | 'lte' | 'eq';
  failPipeline: boolean;
  description: string;
}

interface PipelineSettings {
  parallel: boolean;
  maxConcurrency: number;
  timeout: number;  // overall pipeline timeout
  allowFailure: boolean;
  cacheEnabled: boolean;
  cacheStrategy: 'none' | 'dependencies' | 'full';
  environment: Record<string, string>;
}

/**
 * Execution tracking — Monitor pipeline runs
 */
interface PipelineRun {
  id: string;
  pipelineId: string;
  status: 'pending' | 'running' | 'success' | 'failed' | 'cancelled' | 'timeout';
  trigger: TriggerConfig;
  steps: StepRun[];
  startedAt: string;
  finishedAt?: string;
  duration?: number;
  triggeredBy: string;
  metadata: Record<string, any>;
}

interface StepRun {
  stepId: string;
  status: 'pending' | 'running' | 'success' | 'failed' | 'skipped' | 'timeout';
  startedAt?: string;
  finishedAt?: string;
  duration?: number;
  output?: string;
  error?: string;
  artifacts: string[];
  retryCount: number;
}

/**
 * Monitoring types
 */
interface MetricDefinition {
  name: string;
  type: 'counter' | 'gauge' | 'histogram' | 'summary';
  description: string;
  labels: string[];
  buckets?: number[];  // for histogram
}

interface AlertConfig {
  name: string;
  metric: string;
  condition: 'gt' | 'lt' | 'eq' | 'ne';
  threshold: number;
  duration: string;  // e.g., "5m" for 5 minutes
  severity: 'info' | 'warning' | 'critical' | 'page';
  channels: string[];
  runbook?: string;
}

/**
 * Self-healing types
 */
interface SelfHealingConfig {
  healthCheck: HealthCheckConfig;
  autoRestart: AutoRestartConfig;
  circuitBreaker: CircuitBreakerConfig;
  rateLimit: RateLimitConfig;
}

interface HealthCheckConfig {
  endpoint: string;
  interval: number;
  timeout: number;
  unhealthyThreshold: number;
  healthyThreshold: number;
}

interface AutoRestartConfig {
  enabled: boolean;
  maxRestarts: number;
  restartWindow: number;  // ms — window for counting restarts
  backoff: 'linear' | 'exponential';
  maxBackoff: number;
}

interface CircuitBreakerConfig {
  enabled: boolean;
  failureThreshold: number;
  successThreshold: number;
  timeout: number;  // ms — time to wait before half-open
}

interface RateLimitConfig {
  enabled: boolean;
  maxRequests: number;
  windowMs: number;
  strategy: 'sliding' | 'fixed';
}

// ═══════════════════════════════════════════════════════════════
// GUARDRAILS — Safety patterns cho AI automation
// ═══════════════════════════════════════════════════════════════

interface GuardrailConfig {
  /** Maximum number of AI-generated files per task */
  maxFilesPerTask: number;
  /** Maximum lines of code per AI generation */
  maxLinesPerGeneration: number;
  /** Require human approval for production deploys */
  requireApprovalForProduction: boolean;
  /** Allowed file extensions for AI modification */
  allowedFileExtensions: string[];
  /** Blocked paths that AI cannot modify */
  blockedPaths: string[];
  /** Maximum token budget per automation run */
  maxTokenBudget: number;
  /** Rollback policy on failure */
  rollbackPolicy: 'auto' | 'manual' | 'never';
  /** Maximum retries before escalation */
  maxRetriesBeforeEscalation: number;
}

interface DeploymentGuardrails {
  /** Maximum change size in lines */
  maxChangeSize: number;
  /** Require test coverage for changed files */
  requireTestCoverage: boolean;
  /** Minimum test coverage threshold */
  minTestCoverage: number;
  /** Require security scan */
  requireSecurityScan: boolean;
  /** Blocked paths for auto-deploy */
  blockedDeployPaths: string[];
  /** Environment-specific restrictions */
  environmentRestrictions: Record<string, DeploymentRestriction>;
}

interface DeploymentRestriction {
  requireApproval: boolean;
  approvers: string[];
  allowedHours?: { start: number; end: number };
  allowedDays?: number[];  // 0=Sunday, 6=Saturday
  maxDeployFrequency?: number;  // per hour
}
```

---

## 13. Design Principles cho Automation

### 13.1 SOLID cho Automation Systems

```
┌──────────────────────────────────────────────────────────────────┐
│            SOLID PRINCIPLES IN AUTOMATION                         │
│                                                                  │
│  S — SINGLE RESPONSIBILITY                                       │
│  Each automation step does ONE thing well                        │
│  ✅ Lint step → only lint                                        │
│  ✅ Test step → only test                                        │
│  ❌ "Lint-Test-Deploy" step → too many responsibilities          │
│                                                                  │
│  O — OPEN/CLOSED                                                 │
│  Open for extension, closed for modification                    │
│  ✅ Plugin-based steps (add new steps without changing core)    │
│  ❌ Hardcoded step list (requires code change to add steps)     │
│                                                                  │
│  L — LISKOV SUBSTITUTION                                         │
│  Any step implementation should work in any pipeline             │
│  ✅ All steps implement StepHandler interface                    │
│  ❌ Different step types with incompatible APIs                  │
│                                                                  │
│  I — INTERFACE SEGREGATION                                       │
│  Small, focused interfaces for automation                       │
│  ✅ Separate: TriggerHandler, StepHandler, Notifier             │
│  ❌ One giant AutomationInterface with 20 methods               │
│                                                                  │
│  D — DEPENDENCY INVERSION                                        │
│  Depend on abstractions, not implementations                    │
│  ✅ Step depends on ILogger, not ConsoleLogger                   │
│  ❌ Step directly uses fs.writeFile()                           │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 13.2 Automation Design Principles

```
┌──────────────────────────────────────────────────────────────────┐
│         AUTOMATION DESIGN PRINCIPLES (10 Commandments)            │
│                                                                  │
│  1. THOU SHALL AUTOMATE THE REPEATABLE                          │
│     → If done >2 times → automate it                            │
│     → Focus on high-value, high-frequency tasks                 │
│                                                                  │
│  2. THOU SHALL FAIL FAST                                         │
│     → Run cheapest checks first (lint → test → build)           │
│     → Gate critical steps (no deploy without tests)             │
│                                                                  │
│  3. THOU SHALL MAKE IT REVERSIBLE                               │
│     → Every deploy must have rollback plan                      │
│     → Blue-green or canary deployments                          │
│                                                                  │
│  4. THOU SHALL OBSERVE EVERYTHING                               │
│     → If not monitored, not production-ready                    │
│     → Log, measure, alert on everything important               │
│                                                                  │
│  5. THOU SHALL KEEP PIPELINES FAST                              │
│     → Target: <10 min CI, <30 min CD                            │
│     → Parallelize, cache, skip unnecessary steps                │
│                                                                  │
│  6. THOU SHALL VERSION CONTROL EVERYTHING                       │
│     → Pipeline configs in repo (IaC)                            │
│     → Infrastructure as Code (Terraform, Pulumi)                │
│                                                                  │
│  7. THOU SHALL TEST THE AUTOMATION                              │
│     → Test pipeline configs before merging                      │
│     → Chaos testing for self-healing systems                    │
│                                                                  │
│  8. THOU SHALL NOT NOISE                                        │
│     → Alert on actionable items only                            │
│     → Different channels for different severity                 │
│                                                                  │
│  9. THOU SHALL DOCUMENT RUNBOOKS                                │
│     → Every alert should have a runbook                         │
│     → Automate the runbook if possible                          │
│                                                                  │
│  10. THOU SHALL ITERATE AND IMPROVE                             │
│      → Review pipeline metrics weekly                           │
│      → Remove bottlenecks, add value                            │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 14. Testing Automation Harness

### 14.1 Testing Automation Systems

```python
import time
import json
import hashlib
from dataclasses import dataclass, field
from typing import Any, Callable, Dict, List, Optional
from enum import Enum
from pathlib import Path


class AutomationTestType(Enum):
    """Các loại test cho automation systems"""
    PIPELINE_CONFIG = "pipeline_config"      # Validate pipeline configs
    STEP_EXECUTION = "step_execution"        # Test individual steps
    ROLLBACK = "rollback"                    # Test rollback mechanisms
    CIRCUIT_BREAKER = "circuit_breaker"      # Test circuit breaker
    INTEGRATION = "integration"              # End-to-end pipeline test
    CHAOS = "chaos"                          # Chaos testing
    PERFORMANCE = "performance"              # Throughput & latency


@dataclass
class AutomationTest:
    """Một test case cho automation system"""
    name: str
    test_type: AutomationTestType
    description: str
    handler: Callable
    setup: Optional[Callable] = None
    teardown: Optional[Callable] = None
    timeout: int = 60
    tags: List[str] = field(default_factory=list)
    expected_result: Optional[Any] = None


class AutomationTestHarness:
    """
    Harness để test các automation components.
    
    Features:
    - Pipeline config validation
    - Step execution testing
    - Rollback verification
    - Circuit breaker testing
    - Chaos engineering
    - Performance benchmarks
    """
    
    def __init__(self):
        self.tests: List[AutomationTest] = []
        self.results: List[Dict] = []
        self.suite_metrics: Dict[str, Any] = {}
    
    def register(self, test: AutomationTest):
        """Register một automation test"""
        self.tests.append(test)
    
    def run_all(self) -> Dict:
        """Chạy toàn bộ automation tests"""
        self.results = []
        start_time = time.time()
        
        for test in self.tests:
            result = self._run_single(test)
            self.results.append(result)
        
        total_time = time.time() - start_time
        
        return self._generate_report(total_time)
    
    def run_by_type(self, test_type: AutomationTestType) -> Dict:
        """Chạy tests theo type"""
        self.results = []
        start_time = time.time()
        
        for test in self.tests:
            if test.test_type == test_type:
                result = self._run_single(test)
                self.results.append(result)
        
        total_time = time.time() - start_time
        return self._generate_report(total_time)
    
    def _run_single(self, test: AutomationTest) -> Dict:
        """Chạy một test case"""
        # Setup
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
        
        # Execute
        start = time.time()
        try:
            result = test.handler()
            duration = time.time() - start
            
            # Verify expected result
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
            # Teardown
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

def test_pipeline_config_valid():
    """Test that pipeline config is valid YAML with required fields"""
    config_path = Path(".github/workflows/ci.yml")
    assert config_path.exists(), "CI workflow file must exist"
    content = config_path.read_text()
    assert "name:" in content, "Workflow must have a name"
    assert "on:" in content, "Workflow must have triggers"
    assert "jobs:" in content, "Workflow must have jobs"

def test_circuit_breaker_opens_on_failure():
    """Test circuit breaker opens after threshold failures"""
    cb = CircuitBreaker(failure_threshold=3)
    for _ in range(3):
        cb.record_failure()
    assert cb.state == "open"

def test_rollback_reverts_changes():
    """Test that rollback properly reverts deployment"""
    deployer = Deployer()
    deployer.deploy("v1.0")
    deployer.deploy("v2.0")
    deployer.rollback()
    assert deployer.current_version == "v1.0"


# Register tests
harness = AutomationTestHarness()

harness.register(AutomationTest(
    name="CI Config Valid",
    test_type=AutomationTestType.PIPELINE_CONFIG,
    description="Validates GitHub Actions CI workflow config",
    handler=test_pipeline_config_valid,
    tags=["ci", "config"],
))

harness.register(AutomationTest(
    name="Circuit Breaker Opens",
    test_type=AutomationTestType.CIRCUIT_BREAKER,
    description="Circuit breaker opens after 3 failures",
    handler=test_circuit_breaker_opens_on_failure,
    tags=["reliability"],
))

harness.register(AutomationTest(
    name="Rollback Works",
    test_type=AutomationTestType.ROLLBACK,
    description="Deployment rollback reverts to previous version",
    handler=test_rollback_reverts_changes,
    tags=["deploy", "rollback"],
))

# Run
# report = harness.run_all()
# print(json.dumps(report, indent=2))
```

---

## 15. Anti-Patterns & Solutions Chi Tiết

### 15.1 Common Anti-Patterns

```
┌──────────────────────────────────────────────────────────────────┐
│              AUTOMATION ANTI-PATTERNS                             │
│                                                                  │
│  ❌ ANTI-PATTERN: Flaky Tests                                   │
│     Problem: Tests that pass/fail randomly                      │
│     Solution:                                                 │
│     → Isolate external dependencies (mock, stub)               │
│     → Use deterministic test data                              │
│     → Implement retry with jitter for network tests            │
│                                                                  │
│  ❌ ANTI-PATTERN: Monolithic Pipeline                           │
│     Problem: One giant pipeline for everything                 │
│     Solution:                                                 │
│     → Split into focused, composable pipelines                 │
│     → Use pipeline templates for reuse                         │
│     → Separate CI (validate) from CD (deploy)                 │
│                                                                  │
│  ❌ ANTI-PATTERN: Silent Failures                               │
│     Problem: Pipeline succeeds but output is broken            │
│     Solution:                                                 │
│     → Add post-deploy smoke tests                              │
│     → Implement health check gates                             │
│     → Monitor error rates after deploy                        │
│                                                                  │
│  ❌ ANTI-PATTERN: No Rollback Plan                              │
│     Problem: Deploy broken code with no way to revert          │
│     Solution:                                                 │
│     → Blue-green deployments                                    │
│     → Database migration backward compatibility                │
│     → Feature flags for quick disable                          │
│                                                                  │
│  ❌ ANTI-PATTERN: Manual Steps in Automation                   │
│     Problem: Human clicks required in the middle               │
│     Solution:                                                 │
│     → Fully automate the pipeline (no manual gates)           │
│     → Use chatops for approval (Slack/Teams integration)       │
│     → Implement auto-rollback instead of manual intervention   │
│                                                                  │
│  ❌ ANTI-PATTERN: Alert Fatigue                                 │
│     Problem: Too many alerts, everyone ignores them            │
│     Solution:                                                 │
│     → Tune alert thresholds based on historical data           │
│     → Implement severity levels (info/warn/critical)           │
│     → Add context and runbook links to every alert            │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 15.2 DO vs DON'T Summary

```
┌──────────────────────────────────────────────────────────────────┐
│              AUTOMATION DO vs DON'T                               │
│                                                                  │
│  ✅ DO:                          ❌ DON'T:                      │
│  Automate repeatable tasks       Automate one-off tasks         │
│  Test automation itself          Trust automation blindly       │
│  Version control configs         Store configs in UI only       │
│  Monitor all pipelines           "Set and forget" pipelines    │
│  Implement rollback              Deploy without rollback plan  │
│  Use quality gates               Skip tests to go faster       │
│  Parallelize when possible       Run everything sequentially   │
│  Cache dependencies              Reinstall every time          │
│  Notify on failures only         Spam notifications            │
│  Document runbooks               Assume everyone knows         │
│  Start simple, iterate           Build complex from day 1      │
│  Use canary deployments          Big bang deployments           │
│  Implement circuit breakers      Let failures cascade          │
│  Log everything                  Debug without logs            │
│  Review pipeline metrics weekly  Ignore pipeline performance   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 16. Future Trends trong Automation

### 16.1 AI-Powered Automation (2024-2026)

```
┌──────────────────────────────────────────────────────────────────┐
│              FUTURE AUTOMATION TRENDS                             │
│                                                                  │
│  TREND 1: AI-GENERATED CI/CD                                     │
│  ├── AI generates CI/CD pipeline từ project structure           │
│  ├── Automatic test generation và coverage optimization        │
│  ├── Smart caching decisions dựa trên code analysis            │
│  └── Self-optimizing pipeline configurations                   │
│                                                                  │
│  TREND 2: AUTONOMOUS DEBUGGING                                   │
│  ├── AI agent tự động tìm root cause                          │
│  ├── Automatic fix suggestions cho test failures                │
│  ├── Self-healing CI/CD pipelines                              │
│  └── Smart retry với intelligent failure classification        │
│                                                                  │
│  TREND 3: PREDICTIVE DEPLOYMENT                                  │
│  ├── Predict deployment风险 trước khi happen                    │
│  ├── Suggest optimal deployment windows                        │
│  ├── Auto-adjust canary percentage dựa on risk                │
│  └── Intelligent rollback timing                               │
│                                                                  │
│  TREND 4: NATURAL LANGUAGE AUTOMATION                            │
│  ├── "Deploy main to staging" → Full pipeline execution        │
│  ├── "Add a test for UserService" → Auto-generate test         │
│  ├── "Fix the failing CI" → Auto-diagnose và fix              │
│  └── "Monitor API latency" → Auto-setup monitoring            │
│                                                                  │
│  TREND 5: PLATFORM ENGINEERING                                   │
│  ├── Internal developer platforms (IDP)                         │
│  ├── Self-service infrastructure                               │
│  ├── Golden paths cho common workflows                         │
│  └── Automated compliance và governance                        │
│                                                                  │
│  TREND 6: EDGE AUTOMATION                                        │
│  ├── Run CI/CD at edge locations                                │
│  ├── Distributed testing across regions                        │
│  ├── Local-first automation (no cloud dependency)              │
│  └── Offline-capable automation pipelines                      │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## Tài Liệu Tham Khảo

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [GitLab CI/CD](https://docs.gitlab.com/ee/ci/)
- [Kubernetes Health Checks](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
- [Circuit Breaker Pattern](https://microservices.io/patterns/reliability/circuit-breaker.html)
- [The Twelve-Factor App](https://12factor.net/)
- [Prometheus Monitoring](https://prometheus.io/docs/)
- [SWE-agent](https://github.com/princeton-nlp/SWE-agent)
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- [Cursor IDE](https://cursor.sh)
- [GitHub Copilot](https://docs.github.com/en/copilot)
- [Vercel v0](https://v0.dev)
- [Platform Engineering](https://internaldeveloperplatform.org/)
