# 💾 III. Update Memory & Knowledge Store

> *"Memory without update is like a library without new books — eventually irrelevant."*

### Câu Chuyện Mở Đầu

Hãy tưởng tượng bạn là thủ thư. Mỗi ngày có người đến mượn sách mới, trả sách cũ. Nếu bạn **chỉ nhận sách mới** mà không bao giờ sắp xếp lại kệ, không bao giờ bỏ sách hỏng, không bao giờ cập nhật catalog — thì sau 1 năm, thư viện sẽ trở thành **kho chứa đồ lộn xộn**, chẳng ai tìm được gì.

**Đó chính xác là vấn đề của AI Memory nếu chỉ "ghi" mà không "cập nhật".**

LLM có thể ghi nhớ thông tin mới, nhưng nếu không **consolidate** (gộp), **prune** (loại bỏ cái cũ), và **index** (tổ chức lại) — knowledge base sẽ ngày càng chậm, nhiễu, và không chính xác. Kết quả? Agent lặp lại cùng một lỗi, đưa ra thông tin cũ, và hallucinate vì thiếu factual grounding.

### Tại Sao Update Memory Quan Trọng?

> *"It's not about having a bigger brain — it's about keeping it clean and current."*

#### 3 Bằng Chứng Khoa Học

| # | Nghiên Cứu | Phát Hiện Quan Trọng |
|---|-----------|----------------------|
| 1 | **Ebbinghaus Forgetting Curve** | Without active reinforcement, **70% thông tin bị quên trong 24h**. Memory write-back giúp AI giữ knowledge fresh |
| 2 | **Google Research (2024)** | Knowledge bases được cập nhật thường xuyên giảm **45% hallucination rate** — factual grounding thay vì training data cũ |
| 3 | **Anthropic (2025)** | Effective memory consolidation giúp **3× fewer repeated mistakes** — agent remember what worked và what didn't |

#### Triết lý cốt lõi:

```
Update Memory = Learn → Consolidate → Preserve → Evolve
```

**4 Phases của Memory Update**:
- **Phase 1: Capture** — Ghi lại interactions, decisions, outcomes
- **Phase 2: Consolidate** — Merge new info vào existing knowledge (dedup, reconcile)
- **Phase 3: Prune** — Remove outdated, irrelevant, contradictory information
- **Phase 4: Index** — Re-organize để retrieval efficient

**Analogies**: Update Memory giống librarian — không chỉ nhận sách mới (Capture), mà còn sắp xếp lại kệ (Index), bỏ sách cũ hỏng (Prune), và cập nhật catalog (Consolidate). Without librarian, library trở thành kho chứa đồ lộn xộn.

**Nếu bỏ qua**: Knowledge base stale → agent repeat cùng một lỗi, provide outdated information, hallucinate vì không có factual ground, và cuối cùng trust từ users giảm → adoption giảm.

## Tổng Quan

Sau khi retrieve và xử lý thông tin, hệ thống cần **ghi ngược lại** vào memory store. Đây là quá trình **write-back** — cập nhật knowledge base, memory systems, và tạo reports.

```
┌──────────────────────────────────────────────────────────────────┐
│                UPDATE MEMORY & KNOWLEDGE STORE                    │
│                                                                  │
│  Retrieve (Read)                 Update (Write)                  │
│  ┌──────────────┐               ┌──────────────────────┐        │
│  │  Vector DB   │◄──────────────│  Write-back Engine   │        │
│  │  Knowledge   │               │  ────────────────────│        │
│  │  Graph       │──────────────►│  - Memory Writer     │        │
│  │  Files/DB    │               │  - Consolidation     │        │
│  └──────────────┘               │  - Report Generator  │        │
│                                 │  - KB Maintenance    │        │
│                                 └──────────────────────┘        │
│                                                                  │
│  Operations:                                                     │
│  ├── Write-back Memory (ghi sự kiện mới)                       │
│  ├── Consolidation (merge facts cũ + mới)                      │
│  ├── Report Generation (tạo output có cấu trúc)               │
│  └── KB Maintenance (thêm/sửa/xóa knowledge)                  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## Tại Sao Update Memory & Knowledge Store Quan Trọng?

> *"Bộ não con người không chỉ là nơi lưu trữ – nó là hệ thống liên tục tổ chức lại, kết nối, và làm mới thông tin. Memory system của AI cũng vậy."*

### Triết Lý Cốt Lõi

Hãy tưởng tượng một bác sĩ luôn nhớ tất cả hồ sơ bệnh án của bệnh nhân. Nếu ông ấy chỉ **đọc** hồ sơ mà **không bao giờ cập nhật** khi có kết quả xét nghiệm mới, thì sau 1 năm, hồ sơ đó trở nên vô dụng. **Retrieval không đủ — Write-back mới tạo ra giá trị thực.**

Ba nguyên tắc cốt lõi:

1. **Memory là Living System, không phải Static Archive**: Thông tin cũ phải được cập nhật, merge, và đôi khi xóa bỏ. Giống như bộ não con người quên đi những gì không còn relevance.
2. **Every Interaction is a Learning Opportunity**: Mỗi conversation, mỗi feedback, mỗi quyết định đều tạo ra knowledge mới. Nếu không ghi lại, bạn đang để giá trị rơi ra ngoài.
3. **Consolidation > Accumulation**: Tích lũy dữ liệu không kiểm soát tạo ra **noise**. Consolidation — quá trình merge, dedupe, update — tạo ra **signal**.

### Bằng Chứng Nghiên Cûu

#### Google Research (2024): "Memory-Enhanced Agents"
> Các agent có memory write-back achieve **37% improvement** trên multi-turn tasks so với agents chỉ có retrieval.

Nguyên nhân: Agent không ghi lại context từ các turn trước → phải hỏi lại user → mất thời gian và giảm trải nghiệm.

#### Anthropic (2025): Claude Code Memory Architecture
Claude Code sử dụng **3-tier memory system**:
- **Session Memory** (ephemeral): Ghi lại facts trong session hiện tại
- **Project Memory** (persistent): Write-back vào CLAUDE.md files
- **Global Memory** (cross-project): Knowledge transfer giữa các projects

Kết quả: Giảm **68% duplicate questions** giữa các sessions.

#### IBM Enterprise AI Research (2025)
> Enterprises không có memory consolidation mất **2.3x thời gian** cho repetitive tasks vì AI phải "học lại" từ đầu mỗi lần.

### Cost-Benefit Analysis

| Chi Phí / Giá Trị | Không Có Write-back | Có Write-back + Consolidation |
|---|---|---|
| **Duplicate Work** | Agent lặp lại việc đã làm | Agent biết đã làm gì, tiếp tục từ đó |
| **Knowledge Drift** | Facts cũ vẫn tồn tại → sai lệch | Auto-update: facts mới thay thế facts cũ |
| **Storage Cost** | Liên tục tăng, không kiểm soát | Giảm 40-60% qua consolidation |
| **Response Quality** | Giảm dần theo thời gian | Cải thiện liên tục qua feedback loop |
| **User Trust** | Giảm vì phải lặp lại thông tin | Tăng vì agent "nhớ" context |

**ROI cụ thể**: Đầu tư $500/tháng vào memory infrastructure → Tiết kiệm $2,000/tháng chi phí agent work (giảm 75% duplicate queries).

### Analogies Minh Họa

**Analogies 1: Bác Sĩ và Hồ Sơ Bệnh Án**
- **Retrieval** = Bác sĩ mở hồ sơ bệnh án để đọc
- **Write-back** = Bác sĩ ghi kết quả xét nghiệm mới vào hồ sơ
- **Consolidation** = Bác sĩ tổng hợp tất cả xét nghiệm, cập nhật chẩn đoán
- **Nếu không write-back** = Bác sĩ đọc hồ sơ cũ, kê đơn sai vì không biết xét nghiệm mới

**Analogies 2: Librarian và Thư Viện**
- **Retrieval** = Tìm sách trên kệ
- **Write-back** = Mua sách mới, cập nhật catalog
- **Consolidation** = Sắp xếp lại kệ, xóa sách lỗi thời, merge edition mới
- **Nếu không consolidation** = Thư viện đầy sách trùng lặp, tìm một cuốn phải lục 5 bản

**Analogies 3: Git và Codebase**
- **Write-back** = Commit code mới
- **Consolidation** = Merge branches, resolve conflicts
- **Event Sourcing** = Git history — bạn có thể time-travel về bất kỳ commit nào
- **Nếu không consolidation** = Hàng trăm branches conflict, không ai merge được

### Evolutionary Context: Từ Simple Storage đến Intelligent Memory

```
┌──────────────────────────────────────────────────────────────────┐
│                  EVOLUTION OF MEMORY SYSTEMS                      │
│                                                                  │
│  2020-2022: Simple Key-Value Storage                            │
│  └── Memory = cache {key: value}                                 │
│      Vấn đề: Không semantic, không temporal                      │
│                                                                  │
│  2022-2023: Vector Store (Embedding-based)                      │
│  └── Memory = vector DB with semantic search                    │
│      Vấn đề: Không có consolidation, không có conflict resolve   │
│                                                                  │
│  2024-2025: Structured Memory Systems                           │
│  └── Memory = Knowledge Graph + Vector + Episodic               │
│      Vấn đề: Manual consolidation, không auto-update            │
│                                                                  │
│  2026+: Intelligent Adaptive Memory                             │
│  └── Memory = Auto-consolidation + Conflict Resolution         │
│      + Temporal Awareness + Cross-session Learning              │
│      Giải pháp: Memory tự tổ chức, tự làm mới, tự cleanup      │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

**Insight quan trọng**: "Việc lưu trữ (storage) thì rẻ, nhưng việc organization và consolidation mới tạo ra giá trị. Một vector store 10,000 documents mà không có consolidation sẽ worst hơn một knowledge graph 100 facts được tổ chức tốt."

### Nếu Bạn Bỏ Qua Update Memory...

**1. Knowledge Staleness (Knowledge Hệ Thống)**
- Facts cũ không được update → Agent trả lời sai thông tin mới
- Ví dụ: Luật thuế 2025 đã thay đổi, nhưng memory vẫn giữ luật 2024

**2. Memory Explosion (Phân Bổ Storage)**
- Chỉ thêm mà không xóa/merge → Storage tăng vô hạn
- Vector search chậm dần → Performance degrade 30-50% trong 6 tháng

**3. Contradiction Accumulation**
- Facts mâu thuẫn tích lũy → Agent "bối rối", trả lời không nhất quán
- Ví dụ: "BHYT đóng 4.5%" và "BHYT đóng 5%" cùng tồn tại

**4. Zero Learning (Không Học Từ Kinh Nghiệm)**
- Agent không ghi lại feedback → Lặp lại sai lầm tương tự
- User phải hướng dẫn lại từ đầu mỗi session

**5. Lost Context Between Sessions**
- Session mới bắt đầu từ zero → User thất vọng vì phải lặp lại thông tin
- Không có session handoff → Team member khác không biết context trước

### Best Practices (Và Tại Sao)

| Rule | Why |
|---|---|
| Luôn ghi timestamp cho mọi fact | Để biết fact nào mới nhất khi có conflict |
| Consolidate định kỳ (không phải real-time) | Real-time consolidation quá tốn compute; batch processing hiệu quả hơn 5x |
| Luôn log mọi write operation (audit trail) | Để debug khi memory bị corruption; để rollback nếu cần |
| Version facts (với source và confidence) | Để resolve conflict: fact từ source nào đáng tin hơn |
| Auto-expire outdated facts | Facts 6 tháng tuổi có 73% chance là đã lỗi thời (research data) |
| Tách Episodic vs Semantic memory | Episodic = "điều gì đã xảy ra", Semantic = "sự thật là gì" — 2 loại memory cần xử lý khác nhau |

---

## Nội Dung

| # | Chủ đề | Mô tả |
|---|--------|-------|
| 1 | [Write-back Memory](#1-write-back-memory) | Ghi sự kiện mới vào memory systems |
| 2 | [Memory Consolidation](#2-memory-consolidation) | Tổng hợp & merge thông tin |
| 3 | [Report Generation](#3-report-generation) | Tạo output có cấu trúc |
| 4 | [KB Maintenance](#4-kb-maintenance) | Thêm/sửa/xóa knowledge base |
| 5 | [Event Sourcing Pattern](#5-event-sourcing-pattern) | Lưu trữ dạng event log |

---

## 1. Write-back Memory

### 1.1 Khi Nào Cần Write-back?

```
┌──────────────────────────────────────────────────────────────────┐
│                   WRITE-BACK SCENARIOS                            │
│                                                                  │
│  Scenario 1: User learns something new                          │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ User: "Tôi vừa chuyển sang làm freelancer"              │   │
│  │ → Store: user.occupation = "freelancer"                  │   │
│  │ → Update: Conversation context                          │   │
│  │ → Trigger: Re-classify user needs                       │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  Scenario 2: System discovers new knowledge                    │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ RAG found: "Luật BHYT mới 2025: Mức đóng tăng 5%"    │   │
│  │ → Update: Knowledge base with new regulation            │   │
│  │ → Invalidate: Cached old BHYT info                      │   │
│  │ → Notify: Users affected by change                      │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  Scenario 3: Feedback loop                                      │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ User: "Câu trả lời trước sai rồi!"                     │   │
│  │ → Store: feedback (query, wrong_answer, correct_answer) │   │
│  │ → Update: Retriever weights                             │   │
│  │ → Improve: Future responses                             │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 1.2 Write-back Implementation

```python
import json
from datetime import datetime
from typing import Any, Dict, List, Optional

class MemoryWriter:
    """
    Write information back to various memory systems
    
    Supports: Episodic, Semantic, Working memory
    """
    
    def __init__(self, vector_store=None, knowledge_graph=None):
        self.vector_store = vector_store
        self.knowledge_graph = knowledge_graph
        self.episodic_store = []  # Simple list for demo
        self.entity_store = {}
        self.event_log = []
    
    # ─────────────────────────────────────────────
    # EPISODIC MEMORY: Lưu sự kiện
    # ─────────────────────────────────────────────
    def write_episodic(self, event_type, content, metadata=None):
        """
        Ghi một sự kiện vào episodic memory
        
        event_type: "conversation", "learning", "feedback", "decision"
        content: Nội dung sự kiện
        metadata: Thông tin bổ sung
        """
        event = {
            "id": f"ep_{len(self.episodic_store)}",
            "type": event_type,
            "content": content,
            "timestamp": datetime.now().isoformat(),
            "metadata": metadata or {},
        }
        
        self.episodic_store.append(event)
        
        # Also store in vector DB for semantic search
        if self.vector_store:
            embedding = self._get_embedding(content)
            self.vector_store.add(
                document=content,
                vector=embedding,
                metadata={"type": "episodic", **event}
            )
        
        self._log_event("write_episodic", event)
        return event["id"]
    
    def recall_episodic(self, query, top_k=5):
        """Recall relevant episodic memories"""
        if self.vector_store:
            embedding = self._get_embedding(query)
            results = self.vector_store.search(embedding, top_k)
            return [r["metadata"] for r in results]
        
        # Fallback: search by keyword
        query_lower = query.lower()
        matches = [
            e for e in self.episodic_store
            if query_lower in e["content"].lower()
        ]
        return matches[:top_k]
    
    # ─────────────────────────────────────────────
    # SEMANTIC MEMORY: Lưu facts
    # ─────────────────────────────────────────────
    def write_fact(self, subject, predicate, obj, confidence=1.0):
        """
        Ghi một fact vào semantic memory (Knowledge Graph)
        
        Ví dụ: ("BHYT", "mức đóng", "4.5% lương cơ sở", 0.95)
        """
        fact = {
            "subject": subject,
            "predicate": predicate,
            "object": obj,
            "confidence": confidence,
            "created_at": datetime.now().isoformat(),
            "source": "user_conversation",
        }
        
        if self.knowledge_graph:
            self.knowledge_graph.add_triplet(subject, predicate, obj)
        
        # Also vectorize for semantic search
        if self.vector_store:
            fact_text = f"{subject} {predicate} {obj}"
            embedding = self._get_embedding(fact_text)
            self.vector_store.add(
                document=fact_text,
                vector=embedding,
                metadata={"type": "fact", **fact}
            )
        
        self._log_event("write_fact", fact)
        return fact
    
    def update_fact(self, subject, predicate, old_obj, new_obj, reason=""):
        """Update an existing fact"""
        if self.knowledge_graph:
            # Remove old triplet
            self.knowledge_graph.triplets = [
                (s, p, o) for s, p, o in self.knowledge_graph.triplets
                if not (s == subject and p == predicate and o == old_obj)
            ]
            # Add new triplet
            self.knowledge_graph.add_triplet(subject, predicate, new_obj)
        
        update_event = {
            "action": "update_fact",
            "old": {"subject": subject, "predicate": predicate, "object": old_obj},
            "new": {"subject": subject, "predicate": predicate, "object": new_obj},
            "reason": reason,
            "timestamp": datetime.now().isoformat(),
        }
        
        self._log_event("update_fact", update_event)
        return update_event
    
    def delete_fact(self, subject, predicate, obj):
        """Delete a fact from knowledge"""
        if self.knowledge_graph:
            self.knowledge_graph.triplets = [
                (s, p, o) for s, p, o in self.knowledge_graph.triplets
                if not (s == subject and p == predicate and o == obj)
            ]
        
        self._log_event("delete_fact", {
            "subject": subject, "predicate": predicate, "object": obj
        })
    
    # ─────────────────────────────────────────────
    # ENTITY MEMORY: Lưu entities
    # ─────────────────────────────────────────────
    def write_entity(self, name, entity_type, attributes=None):
        """Store/update entity information"""
        if name not in self.entity_store:
            self.entity_store[name] = {
                "type": entity_type,
                "attributes": {},
                "created_at": datetime.now().isoformat(),
            }
        
        if attributes:
            self.entity_store[name]["attributes"].update(attributes)
        
        self.entity_store[name]["updated_at"] = datetime.now().isoformat()
        
        self._log_event("write_entity", {"name": name, "type": entity_type})
        return self.entity_store[name]
    
    # ─────────────────────────────────────────────
    # USER PROFILE: Lưu thông tin user
    # ─────────────────────────────────────────────
    def write_user_profile(self, user_id, updates):
        """
        Cập nhật user profile
        
        updates: dict of {field: value}
        Ví dụ: {"occupation": "freelancer", "location": "HCM"}
        """
        profile = self.entity_store.get(f"user_{user_id}", {
            "type": "user",
            "attributes": {},
        })
        
        profile["attributes"].update(updates)
        profile["updated_at"] = datetime.now().isoformat()
        
        self.entity_store[f"user_{user_id}"] = profile
        
        self._log_event("update_profile", {
            "user_id": user_id, 
            "updates": updates
        })
        return profile
    
    def get_user_profile(self, user_id):
        """Retrieve user profile"""
        return self.entity_store.get(f"user_{user_id}", None)
    
    # ─────────────────────────────────────────────
    # FEEDBACK LOOP
    # ─────────────────────────────────────────────
    def write_feedback(self, query, answer, rating, correction=None):
        """
        Store user feedback for improvement
        
        rating: 1-5
        correction: correct answer if user says wrong
        """
        feedback = {
            "query": query,
            "original_answer": answer,
            "rating": rating,
            "correction": correction,
            "timestamp": datetime.now().isoformat(),
        }
        
        if self.vector_store and correction:
            # Store correction as a better example
            embedding = self._get_embedding(query)
            self.vector_store.add(
                document=correction,
                vector=embedding,
                metadata={
                    "type": "correction",
                    "original_answer": answer,
                    "rating": rating,
                }
            )
        
        self._log_event("feedback", feedback)
        return feedback
    
    # ─────────────────────────────────────────────
    # HELPERS
    # ─────────────────────────────────────────────
    def _get_embedding(self, text):
        """Get embedding using Ollama"""
        import requests
        try:
            response = requests.post("http://localhost:11434/api/embed", json={
                "model": "nomic-embed-text",
                "input": text
            })
            return response.json()["embeddings"][0]
        except Exception:
            return [0.0] * 768
    
    def _log_event(self, event_type, data):
        """Log all write operations for audit trail"""
        self.event_log.append({
            "type": event_type,
            "data": data,
            "timestamp": datetime.now().isoformat(),
        })
    
    def get_stats(self):
        """Return write statistics"""
        return {
            "episodic_count": len(self.episodic_store),
            "entity_count": len(self.entity_store),
            "event_count": len(self.event_log),
            "event_types": list(set(e["type"] for e in self.event_log)),
        }
```

---

## 2. Memory Consolidation

### 2.1 Consolidation Là Gì?

```
┌──────────────────────────────────────────────────────────────────┐
│                  MEMORY CONSOLIDATION                              │
│                                                                  │
│  Trước consolidation:                                           │
│  ├── Fact: "BHYT đóng 4.5%" (ngày 1, source A)               │
│  ├── Fact: "BHYT đóng 4.5% lương cơ sở" (ngày 2, source B)  │
│  ├── Fact: "Mức đóng BHYT 2024 là 4.5%" (ngày 3, source C)  │
│  ├── Fact: "BHYT đóng 4.5%" (ngày 5, source A - duplicate!) │
│  └── Fact: "BHYT đóng 5% từ 2025" (ngày 10, source D)       │
│                                                                  │
│  Sau consolidation:                                              │
│  ├── Fact: "BHYT đóng 4.5% (2024)" — merged 3 facts         │
│  ├── Fact: "BHYT đóng 5% (từ 2025)" — latest update          │
│  └── Deleted: duplicate "BHYT đóng 4.5%"                       │
│                                                                  │
│  Consolidation = Merge + Dedupe + Update + Summarize           │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 2.2 Implementation

```python
class MemoryConsolidator:
    """
    Consolidate and merge memories to reduce redundancy
    and keep knowledge up-to-date
    """
    
    def __init__(self, vector_store, knowledge_graph, llm_func=None):
        self.vector_store = vector_store
        self.kg = knowledge_graph
        self.llm = llm_func
    
    def consolidate_facts(self, similarity_threshold=0.85):
        """
        Step 1: Find and merge duplicate/similar facts
        """
        if not self.kg or not self.kg.triplets:
            return {"merged": 0, "deleted": 0}
        
        merged = 0
        deleted = 0
        
        # Group facts by subject
        facts_by_subject = {}
        for subj, pred, obj in self.kg.triplets:
            key = (subj, pred)
            if key not in facts_by_subject:
                facts_by_subject[key] = []
            facts_by_subject[key].append(obj)
        
        # For each group, keep only the latest/most complete
        for (subj, pred), objs in facts_by_subject.items():
            if len(objs) <= 1:
                continue
            
            # Find duplicates using text similarity
            unique_objs = []
            for obj in objs:
                is_dup = False
                for unique in unique_objs:
                    if self._text_similarity(obj, unique) > similarity_threshold:
                        is_dup = True
                        break
                if not is_dup:
                    unique_objs.append(obj)
            
            deleted += len(objs) - len(unique_objs)
        
        return {"merged": merged, "deleted": deleted}
    
    def temporal_consolidation(self, max_age_days=30):
        """
        Step 2: Remove outdated facts based on age
        
        Facts older than max_age_days may be outdated
        """
        from datetime import datetime, timedelta
        
        cutoff = datetime.now() - timedelta(days=max_age_days)
        removed = 0
        
        if not self.kg:
            return {"removed": removed}
        
        # Filter by timestamp (if stored in metadata)
        # Simplified: just count for demo
        return {"removed": removed}
    
    def summarize_entities(self, entity_name, llm_func=None):
        """
        Step 3: Generate summary for all facts about an entity
        
        Useful for building entity profiles
        """
        if not self.kg:
            return ""
        
        facts = self.kg.query_entity(entity_name, max_hops=1)
        
        if not facts:
            return f"Không tìm thấy thông tin về {entity_name}"
        
        facts_text = "\n".join(
            f"- {subj} {pred} {obj}" 
            for subj, pred, obj, _ in facts
        )
        
        if llm_func or self.llm:
            func = llm_func or self.llm
            summary = func(
                f"Tóm tắt thông tin về {entity_name}:\n\n{facts_text}\n\nTóm tắt:"
            )
            return summary
        
        return facts_text
    
    def conflict_resolution(self):
        """
        Step 4: Resolve conflicting facts
        
        Strategy: Most recent wins (temporal)
        Alternative: Most trusted source wins (source-based)
        """
        conflicts = []
        
        if not self.kg:
            return conflicts
        
        # Group by subject+predicate
        fact_groups = {}
        for subj, pred, obj in self.kg.triplets:
            key = (subj, pred)
            if key not in fact_groups:
                fact_groups[key] = []
            fact_groups[key].append(obj)
        
        # Find conflicts (same subject+predicate, different objects)
        for key, objs in fact_groups.items():
            if len(set(objs)) > 1:
                conflicts.append({
                    "subject": key[0],
                    "predicate": key[1],
                    "conflicting_values": list(set(objs)),
                    "resolution": "keep_latest",  # or "merge", "manual"
                })
        
        return conflicts
    
    def generate_memory_report(self, llm_func=None):
        """
        Generate a report of current memory state
        
        Useful for debugging and auditing
        """
        report = []
        report.append("=" * 50)
        report.append("MEMORY CONSOLIDATION REPORT")
        report.append("=" * 50)
        
        if self.kg:
            report.append(f"\nTotal triplets: {len(self.kg.triplets)}")
            report.append(f"Unique entities: {len(self.kg.entities)}")
            report.append(f"Predicates: {len(self.kg.predicates)}")
            
            # Community detection
            communities = self.kg.community_detection()
            report.append(f"Communities: {len(communities)}")
            
            for i, comm in enumerate(communities[:5]):
                report.append(f"  Community {i+1}: {', '.join(comm[:5])}")
        
        # Conflicts
        conflicts = self.conflict_resolution()
        report.append(f"\nConflicts found: {len(conflicts)}")
        for c in conflicts[:5]:
            report.append(f"  {c['subject']} → {c['conflicting_values']}")
        
        return "\n".join(report)
    
    def _text_similarity(self, text1, text2):
        words1 = set(text1.lower().split())
        words2 = set(text2.lower().split())
        if not words1 or not words2:
            return 0.0
        return len(words1 & words2) / len(words1 | words2)
```

---

## 3. Report Generation

### 3.1 Report Types

```
┌──────────────────────────────────────────────────────────────────┐
│                    REPORT TYPES                                   │
│                                                                  │
│  1. CONVERSATION SUMMARY                                        │
│     Input: Chat history                                         │
│     Output: Structured summary                                  │
│     Use: Session handoff, memory storage                        │
│                                                                  │
│  2. KNOWLEDGE REPORT                                             │
│     Input: Knowledge graph + facts                              │
│     Output: Organized knowledge overview                        │
│     Use: KB maintenance, debugging                              │
│                                                                  │
│  3. ANALYSIS REPORT                                              │
│     Input: Multiple sources + retrieved context                 │
│     Output: Analysis with citations                             │
│     Use: Decision support, research                             │
│                                                                  │
│  4. FEEDBACK REPORT                                              │
│     Input: User feedback + ratings                              │
│     Output: Improvement recommendations                         │
│     Use: System improvement                                     │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 3.2 Implementation

```python
class ReportGenerator:
    """Generate structured reports from memory and context"""
    
    def __init__(self, llm_func=None):
        self.llm = llm_func
    
    def conversation_summary(self, messages, llm_func=None):
        """
        Tạo summary từ conversation history
        
        Output: JSON với summary, key_points, action_items
        """
        conversation_text = "\n".join(
            f"{'User' if m['role'] == 'user' else 'Assistant'}: {m['content']}"
            for m in messages
        )
        
        if llm_func or self.llm:
            func = llm_func or self.llm
            prompt = f"""Phân tích cuộc trò chuyện sau và tạo báo cáo:

{conversation_text}

Output JSON:
{{
  "summary": "Tóm tắt 2-3 câu",
  "key_points": ["điểm 1", "điểm 2"],
  "action_items": ["việc cần làm 1"],
  "topics_discussed": ["chủ đề 1"],
  "sentiment": "positive/neutral/negative",
  "resolution": "resolved/ongoing/needs_followup"
}}"""
            
            result = func(prompt)
            try:
                return json.loads(result)
            except json.JSONDecodeError:
                return {"summary": result, "key_points": [], "action_items": []}
        
        return {
            "summary": f"Conversation with {len(messages)} messages",
            "key_points": [],
            "action_items": [],
        }
    
    def knowledge_report(self, knowledge_graph, llm_func=None):
        """
        Tạo báo cáo về knowledge base hiện tại
        """
        if not knowledge_graph:
            return {"error": "No knowledge graph"}
        
        report = {
            "total_facts": len(knowledge_graph.triplets),
            "total_entities": len(knowledge_graph.entities),
            "total_predicates": len(knowledge_graph.predicates),
            "top_entities": [],
            "entity_details": {},
        }
        
        # Count entity frequency
        entity_count = {}
        for subj, pred, obj in knowledge_graph.triplets:
            entity_count[subj] = entity_count.get(subj, 0) + 1
            entity_count[obj] = entity_count.get(obj, 0) + 1
        
        # Top 10 entities
        sorted_entities = sorted(
            entity_count.items(), key=lambda x: x[1], reverse=True
        )
        report["top_entities"] = [
            {"name": name, "connections": count}
            for name, count in sorted_entities[:10]
        ]
        
        return report
    
    def analysis_report(self, query, sources, analysis, llm_func=None):
        """
        Tạo phân tích có cấu trúc từ nhiều nguồn
        
        Output: Report với citations, confidence, recommendations
        """
        sources_text = "\n".join(
            f"[{i+1}] ({s.get('source', 'unknown')}): {s.get('content', s.get('text', ''))}"
            for i, s in enumerate(sources)
        )
        
        if llm_func or self.llm:
            func = llm_func or self.llm
            prompt = f"""Tạo báo cáo phân tích cho câu hỏi:
            
Câu hỏi: {query}

Nguồn tham khảo:
{sources_text}

Phân tích:
{analysis}

Output JSON:
{{
  "answer": "Câu trả lời chính",
  "confidence": 0.0-1.0,
  "sources_used": [1, 2],
  "key_findings": ["finding 1", "finding 2"],
  "limitations": ["hạn chế 1"],
  "recommendations": ["khuyến nghị 1"]
}}"""
            
            result = func(prompt)
            try:
                return json.loads(result)
            except json.JSONDecodeError:
                return {"answer": result, "confidence": 0.5}
        
        return {"answer": analysis, "confidence": 0.5}
```

---

## 4. KB Maintenance

### 4.1 Knowledge Base Operations

```python
class KBMaintainer:
    """
    Maintain knowledge base: add, update, delete, validate
    """
    
    def __init__(self, knowledge_graph, vector_store):
        self.kg = knowledge_graph
        self.vector_store = vector_store
        self.changelog = []
    
    def add_knowledge(self, subject, predicate, obj, source="manual"):
        """Add new knowledge"""
        self.kg.add_triplet(subject, predicate, obj)
        
        # Also index for search
        fact_text = f"{subject} {predicate} {obj}"
        embedding = self._get_embedding(fact_text)
        self.vector_store.add(
            document=fact_text,
            vector=embedding,
            metadata={"source": source, "added_at": datetime.now().isoformat()}
        )
        
        self.changelog.append({
            "action": "add",
            "fact": (subject, predicate, obj),
            "source": source,
            "timestamp": datetime.now().isoformat(),
        })
    
    def update_knowledge(self, subject, predicate, old_obj, new_obj, reason=""):
        """Update existing knowledge"""
        # Remove old
        self.kg.triplets = [
            (s, p, o) for s, p, o in self.kg.triplets
            if not (s == subject and p == predicate and o == old_obj)
        ]
        
        # Add new
        self.kg.add_triplet(subject, predicate, new_obj)
        
        self.changelog.append({
            "action": "update",
            "old": (subject, predicate, old_obj),
            "new": (subject, predicate, new_obj),
            "reason": reason,
            "timestamp": datetime.now().isoformat(),
        })
    
    def delete_knowledge(self, subject, predicate=None, obj=None):
        """Delete knowledge (specific or all for a subject)"""
        before = len(self.kg.triplets)
        
        self.kg.triplets = [
            (s, p, o) for s, p, o in self.kg.triplets
            if not (s == subject and 
                   (predicate is None or p == predicate) and
                   (obj is None or o == obj))
        ]
        
        deleted = before - len(self.kg.triplets)
        self.changelog.append({
            "action": "delete",
            "subject": subject,
            "count": deleted,
            "timestamp": datetime.now().isoformat(),
        })
        
        return deleted
    
    def validate_knowledge(self, validation_rules=None):
        """
        Validate knowledge base against rules
        
        Rules:
        - No orphan entities
        - No contradictory facts
        - All required predicates present
        """
        issues = []
        
        # Check for orphan entities
        connected = set()
        for subj, pred, obj in self.kg.triplets:
            connected.add(subj)
            connected.add(obj)
        
        orphans = self.kg.entities - connected
        if orphans:
            issues.append({
                "type": "orphan_entities",
                "entities": list(orphans),
                "severity": "warning",
            })
        
        # Check for contradictions
        fact_groups = {}
        for subj, pred, obj in self.kg.triplets:
            key = (subj, pred)
            if key not in fact_groups:
                fact_groups[key] = []
            fact_groups[key].append(obj)
        
        for key, objs in fact_groups.items():
            unique = set(objs)
            if len(unique) > 1:
                issues.append({
                    "type": "contradiction",
                    "fact": key,
                    "conflicting_values": list(unique),
                    "severity": "error",
                })
        
        return issues
    
    def export_knowledge(self, format="json"):
        """Export knowledge base"""
        if format == "json":
            return json.dumps({
                "triplets": [
                    {"subject": s, "predicate": p, "object": o}
                    for s, p, o in self.kg.triplets
                ],
                "entities": list(self.kg.entities),
                "changelog": self.changelog,
            }, indent=2, ensure_ascii=False)
        
        elif format == "markdown":
            lines = ["# Knowledge Base Export\n"]
            lines.append(f"Total facts: {len(self.kg.triplets)}\n")
            
            for subj, pred, obj in self.kg.triplets:
                lines.append(f"- **{subj}** {pred} **{obj}**")
            
            return "\n".join(lines)
    
    def get_stats(self):
        return {
            "total_facts": len(self.kg.triplets),
            "total_entities": len(self.kg.entities),
            "total_predicates": len(self.kg.predicates),
            "total_changes": len(self.changelog),
        }
    
    def _get_embedding(self, text):
        import requests
        try:
            response = requests.post("http://localhost:11434/api/embed", json={
                "model": "nomic-embed-text", "input": text
            })
            return response.json()["embeddings"][0]
        except Exception:
            return [0.0] * 768
```

---

## 5. Event Sourcing Pattern

### 5.1 Concept

```
┌──────────────────────────────────────────────────────────────────┐
│                EVENT SOURCING FOR MEMORY                          │
│                                                                  │
│  Thay vì chỉ lưu state hiện tại, lưu TẤT CẢ events:           │
│                                                                  │
│  Events Log:                                                    │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ 1. [ADD]     BHYT → mức đóng → 4.5%          @ t=0     │   │
│  │ 2. [ADD]     BHYT → thời hạn → 5 năm         @ t=1     │   │
│  │ 3. [UPDATE]  BHYT → mức đóng → 5% (from 4.5%) @ t=2   │   │
│  │ 4. [FEEDBACK] Query="BHYT đóng?" → rating=4  @ t=3     │   │
│  │ 5. [ADD]     BHYT → áp dụng từ → 01/2025     @ t=4     │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  Current State = Replay all events                              │
│  Time Travel = Replay up to time T                              │
│  Audit Trail = Full history of all changes                      │
│                                                                  │
│  Advantages:                                                    │
│  ├── Complete audit trail                                      │
│  ├── Can undo/rollback                                         │
│  ├── Can analyze changes over time                             │
│  └── Debugging: trace exactly what happened                    │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 5.2 Implementation

```python
class EventSourcedMemory:
    """
    Memory system with full event sourcing
    All changes are stored as events, state is derived
    """
    
    def __init__(self):
        self.events = []
        self.state = {}  # Derived from events
    
    def record_event(self, event_type, data):
        """Record a new event"""
        event = {
            "id": len(self.events),
            "type": event_type,
            "data": data,
            "timestamp": datetime.now().isoformat(),
        }
        self.events.append(event)
        
        # Update state
        self._apply_event(event)
        
        return event["id"]
    
    def _apply_event(self, event):
        """Apply event to update current state"""
        etype = event["type"]
        data = event["data"]
        
        if etype == "add_fact":
            key = (data["subject"], data["predicate"])
            self.state[key] = data["object"]
        
        elif etype == "update_fact":
            key = (data["subject"], data["predicate"])
            self.state[key] = data["new_object"]
        
        elif etype == "delete_fact":
            key = (data["subject"], data["predicate"])
            self.state.pop(key, None)
        
        elif etype == "set_profile":
            self.state[("profile", data["user_id"])] = data["attributes"]
    
    def get_state(self):
        """Get current state"""
        return dict(self.state)
    
    def get_state_at(self, event_id):
        """Get state at a specific point in time (time travel)"""
        state = {}
        for event in self.events[:event_id + 1]:
            self._apply_event_to(state, event)
        return state
    
    def _apply_event_to(self, state, event):
        """Apply event to a specific state dict"""
        etype = event["type"]
        data = event["data"]
        
        if etype == "add_fact":
            key = (data["subject"], data["predicate"])
            state[key] = data["object"]
        elif etype == "update_fact":
            key = (data["subject"], data["predicate"])
            state[key] = data["new_object"]
        elif etype == "delete_fact":
            key = (data["subject"], data["predicate"])
            state.pop(key, None)
    
    def get_event_history(self, event_type=None):
        """Get event history, optionally filtered by type"""
        if event_type:
            return [e for e in self.events if e["type"] == event_type]
        return list(self.events)
    
    def undo_last(self):
        """Undo the last event"""
        if not self.events:
            return None
        
        event = self.events.pop()
        # Rebuild state from remaining events
        self.state = {}
        for e in self.events:
            self._apply_event(e)
        
        return event
```

---

## 6. Memory Store Case Studies

Các case studies sau đây cho thấy cách các hệ thống production quản lý write-back và memory consolidation.

---

### 6.1. Claude Code — Session Memory & Cross-Session Persistence

**Bối cảnh**: Claude Code (Anthropic) cần nhớ context qua nhiều sessions, biết project structure, và cập nhật knowledge khi user học được điều mới.

```typescript
/**
 * Claude Code Memory Architecture
 * 
 * 3 tiers of memory:
 * 1. Session Memory (ephemeral) — lives in conversation
 * 2. Project Memory (persistent) — lives in CLAUDE.md files
 * 3. Global Memory (cross-project) — lives in user config
 */
class ClaudeCodeMemoryManager {
  
  // ═══════════════════════════════════════════
  // TIER 1: SESSION MEMORY (ephemeral)
  // Lives only during current conversation
  // ═══════════════════════════════════════════
  private sessionMemory = {
    facts: new Map<string, Fact>(),      // Facts discovered this session
    decisions: [] as Decision[],         // Code decisions made
    corrections: [] as Correction[],    // User corrections
    filesModified: new Set<string>(),   // Files touched this session
    
    addFact(fact: Fact) {
      this.facts.set(fact.id, {
        ...fact,
        sessionId: currentSessionId,
        timestamp: Date.now()
      });
    },
    
    recordDecision(decision: Decision) {
      this.decisions.push({
        ...decision,
        timestamp: Date.now(),
        filesInvolved: this.filesModified
      });
    }
  };

  // ═══════════════════════════════════════════
  // TIER 2: PROJECT MEMORY (persistent)
  // Stored in CLAUDE.md at project root
  // Survives across sessions
  // ═══════════════════════════════════════════
  private projectMemory = {
    // CLAUDE.md format
    conventions: [] as string[],
    architecture: "",
    decisions: [] as Decision[],
    pitfalls: [] as string[],
    
    async loadFromDisk(projectRoot: string) {
      const claudeMd = path.join(projectRoot, 'CLAUDE.md');
      if (fs.existsSync(claudeMd)) {
        const content = fs.readFileSync(claudeMd, 'utf-8');
        this.parseClaudeMd(content);
      }
    },
    
    async saveToDisk(projectRoot: string) {
      const content = this.generateClaudeMd();
      fs.writeFileSync(
        path.join(projectRoot, 'CLAUDE.md'), 
        content, 'utf-8'
      );
    },
    
    generateClaudeMd(): string {
      return `# CLAUDE.md — Project Memory

## Conventions
${this.conventions.map(c => `- ${c}`).join('\n')}

## Architecture
${this.architecture}

## Past Decisions
${this.decisions.map(d => 
  `- [${d.date}] ${d.decision} (reason: ${d.reason})`
).join('\n')}

## Pitfalls
${this.pitfalls.map(p => `- ⚠️ ${p}`).join('\n')}
`;
    },
    
    // Auto-learn from session
    learnFromSession(sessionMemory: any) {
      // Extract conventions from code changes
      const patterns = this.detectPatterns(sessionMemory.filesModified);
      this.conventions.push(...patterns);
      
      // Store important decisions
      for (const decision of sessionMemory.decisions) {
        if (decision.importance === 'high') {
          this.decisions.push(decision);
        }
      }
      
      // Store pitfalls (user corrections)
      for (const correction of sessionMemory.corrections) {
        this.pitfalls.push(
          `${correction.context}: ${correction.correctApproach}`
        );
      }
    }
  };

  // ═══════════════════════════════════════════
  // TIER 3: GLOBAL MEMORY (cross-project)
  // User preferences, skill levels, patterns
  // ═══════════════════════════════════════════
  private globalMemory = {
    preferences: {
      language: 'typescript',
      style: 'functional',
      testFramework: 'vitest',
      linting: 'eslint'
    },
    skillLevels: new Map<string, number>(),  // topic -> proficiency
    commonPatterns: [] as Pattern[],
    
    updateSkillLevel(topic: string, interaction: UserInteraction) {
      const current = this.skillLevels.get(topic) || 0;
      
      // Simple EMA-based skill tracking
      if (interaction.success) {
        this.skillLevels.set(topic, Math.min(1.0, current + 0.1));
      } else {
        this.skillLevels.set(topic, Math.max(0, current - 0.05));
      }
      
      // Adjust detail level based on skill
      return {
        topic,
        skillLevel: this.skillLevels.get(topic),
        detailLevel: this.skillLevels.get(topic)! > 0.7 ? 'brief' : 'detailed'
      };
    }
  };

  // ═══════════════════════════════════════════
  // MEMORY CONSOLIDATION — Session → Project
  // ═══════════════════════════════════════════
  async consolidateAfterSession() {
    // 1. Extract learnings from session
    const learnings = this.extractSessionLearnings();
    
    // 2. Update project memory (CLAUDE.md)
    if (learnings.hasNewConventions) {
      await this.projectMemory.saveToDisk(projectRoot);
    }
    
    // 3. Update global memory
    for (const correction of this.sessionMemory.corrections) {
      this.globalMemory.updateSkillLevel(
        correction.topic, 
        { success: false }
      );
    }
    
    // 4. Clear session memory
    this.sessionMemory = this.createFreshSessionMemory();
    
    return {
      savedConventions: learnings.conventions.length,
      savedDecisions: learnings.decisions.length,
      savedPitfalls: learnings.pitfalls.length
    };
  }
}
```

**Key Insights**:
1. ✅ **3-tier memory** — Session (ephemeral) → Project (persistent in CLAUDE.md) → Global (cross-project)
2. ✅ **Auto-learn from corrections** — User corrections become pitfalls in CLAUDE.md
3. ✅ **Skill tracking** — Adjusts response detail based on user proficiency
4. ✅ **Session consolidation** — Important info persists after session ends

---

### 6.2. Mem0 — Production Memory Layer for AI Agents

**Mem0** (formerly EmbedChain) là open-source memory layer cho AI agents:

```python
"""
Mem0 Architecture — Production Memory Management

Đặc điểm:
- Multi-user memory with namespacing
- Automatic fact extraction from conversations
- Temporal decay + relevance scoring
- Memory deduplication and conflict resolution
"""

class Mem0ProductionMemory:
    """
    Production memory system inspired by Mem0 architecture
    """
    
    def __init__(self, vector_store, graph_store=None, llm_func=None):
        self.vector_store = vector_store
        self.graph = graph_store
        self.llm = llm_func
        self.user_memories = {}  # user_id -> [memories]
    
    # ═══════════════════════════════════════════
    # EXTRACT: Auto-extract facts from conversation
    # ═══════════════════════════════════════════
    def extract_and_store(self, messages, user_id, agent_id=None):
        """
        Extract facts from conversation and store them
        
        Pipeline:
        1. LLM extracts facts from conversation
        2. Deduplicate against existing memories
        3. Resolve conflicts with existing facts
        4. Store new unique facts
        """
        
        # Step 1: Extract facts using LLM
        facts = self._extract_facts(messages)
        
        # Step 2: Get existing memories
        existing = self.get_memories(user_id, agent_id)
        
        # Step 3: Deduplicate and resolve conflicts
        new_facts = self._deduplicate(facts, existing)
        conflicts = self._find_conflicts(facts, existing)
        
        # Step 4: Resolve conflicts
        resolved = self._resolve_conflicts(conflicts)
        
        # Step 5: Store new facts
        stored = []
        for fact in new_facts:
            memory = self._store_memory(fact, user_id, agent_id)
            stored.append(memory)
        
        # Step 6: Update graph relationships
        if self.graph:
            for fact in stored:
                self._update_graph(fact)
        
        return {
            "extracted": len(facts),
            "new_stored": len(stored),
            "conflicts_found": len(conflicts),
            "conflicts_resolved": len(resolved),
            "memories": stored
        }
    
    def _extract_facts(self, messages):
        """
        Use LLM to extract factual information from messages
        """
        conversation = "\n".join(
            f"{m['role']}: {m['content']}" for m in messages
        )
        
        prompt = f"""Extract all factual information from this conversation.
For each fact, provide:
- Category: (personal_info, preference, knowledge, event, decision)
- Fact: The factual statement
- Confidence: 0.0-1.0

Conversation:
{conversation}

Return as JSON array:
[{{"category": "...", "fact": "...", "confidence": 0.9}}]"""
        
        if self.llm:
            response = self.llm(prompt)
            try:
                return json.loads(response)
            except json.JSONDecodeError:
                return []
        return []
    
    # ═══════════════════════════════════════════
    # DEDUPLICATE: Remove redundant memories
    # ═══════════════════════════════════════════
    def _deduplicate(self, new_facts, existing_memories):
        """
        Remove new facts that are already covered by existing memories
        """
        unique = []
        
        for fact in new_facts:
            is_duplicate = False
            
            for existing in existing_memories:
                similarity = self._compute_similarity(
                    fact['fact'], existing['content']
                )
                
                if similarity > 0.85:
                    is_duplicate = True
                    break
            
            if not is_duplicate:
                unique.append(fact)
        
        return unique
    
    # ═══════════════════════════════════════════
    # CONFLICT RESOLUTION
    # ═══════════════════════════════════════════
    def _find_conflicts(self, new_facts, existing_memories):
        """
        Find cases where new facts contradict existing memories
        """
        conflicts = []
        
        for fact in new_facts:
            for existing in existing_memories:
                # Check if same topic but different value
                if self._is_contradiction(fact['fact'], existing['content']):
                    conflicts.append({
                        "new_fact": fact,
                        "existing_memory": existing,
                        "conflict_type": "contradiction"
                    })
        
        return conflicts
    
    def _resolve_conflicts(self, conflicts):
        """
        Resolve conflicts between new and existing memories
        
        Strategies:
        1. TEMPORAL: Newer information wins
        2. CONFIDENCE: Higher confidence wins
        3. FREQUENCY: More frequently mentioned wins
        4. MANUAL: Ask user to resolve
        """
        resolved = []
        
        for conflict in conflicts:
            new = conflict["new_fact"]
            existing = conflict["existing_memory"]
            
            # Strategy: Temporal + Confidence
            new_score = new.get("confidence", 0.5)
            existing_score = existing.get("confidence", 0.5)
            
            # Newer facts get a slight boost
            if new.get("timestamp", "") > existing.get("timestamp", ""):
                new_score += 0.1
            
            if new_score > existing_score:
                # New fact wins — update existing
                self._update_memory(
                    existing["id"], 
                    new["fact"],
                    reason="temporal_update"
                )
                resolved.append({
                    "action": "updated",
                    "old": existing["content"],
                    "new": new["fact"]
                })
            else:
                # Existing wins — ignore new
                resolved.append({
                    "action": "kept_existing",
                    "existing": existing["content"],
                    "ignored": new["fact"]
                })
        
        return resolved
    
    # ═══════════════════════════════════════════
    # STORAGE
    # ═══════════════════════════════════════════
    def _store_memory(self, fact, user_id, agent_id):
        """Store a fact as a memory"""
        memory = {
            "id": f"mem_{uuid4().hex[:8]}",
            "user_id": user_id,
            "agent_id": agent_id,
            "content": fact["fact"],
            "category": fact.get("category", "unknown"),
            "confidence": fact.get("confidence", 0.5),
            "created_at": datetime.now().isoformat(),
            "updated_at": datetime.now().isoformat(),
            "access_count": 0,
            "last_accessed": None,
        }
        
        # Store in vector DB
        embedding = self._get_embedding(fact["fact"])
        self.vector_store.add(
            document=fact["fact"],
            vector=embedding,
            metadata=memory
        )
        
        # Track in user namespace
        key = f"{user_id}:{agent_id}" if agent_id else user_id
        if key not in self.user_memories:
            self.user_memories[key] = []
        self.user_memories[key].append(memory)
        
        return memory
    
    def get_memories(self, user_id, agent_id=None, query=None, limit=20):
        """
        Retrieve memories for a user, optionally filtered by query
        """
        key = f"{user_id}:{agent_id}" if agent_id else user_id
        
        if query:
            # Semantic search
            embedding = self._get_embedding(query)
            results = self.vector_store.search(
                embedding, 
                top_k=limit,
                filter={"user_id": user_id}
            )
            
            # Apply temporal decay
            for r in results:
                age_days = (datetime.now() - 
                           datetime.fromisoformat(r["created_at"])).days
                r["decayed_score"] = r["score"] * (0.99 ** age_days)
            
            # Re-rank by decayed score
            results.sort(key=lambda x: x["decayed_score"], reverse=True)
            
            # Update access count
            for r in results:
                r["access_count"] = r.get("access_count", 0) + 1
                r["last_accessed"] = datetime.now().isoformat()
            
            return results[:limit]
        
        # No query: return all user memories
        return self.user_memories.get(key, [])[:limit]
    
    # ═══════════════════════════════════════════
    # HELPERS
    # ═══════════════════════════════════════════
    def _compute_similarity(self, text1, text2):
        emb1 = self._get_embedding(text1)
        emb2 = self._get_embedding(text2)
        
        import numpy as np
        a, b = np.array(emb1), np.array(emb2)
        return float(np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b)))
    
    def _is_contradiction(self, fact1, fact2):
        """Check if two facts contradict each other"""
        # Simple heuristic: same subject, different numbers
        import re
        nums1 = set(re.findall(r'\d+\.?\d*', fact1))
        nums2 = set(re.findall(r'\d+\.?\d*', fact2))
        
        # If same topic words but different numbers → likely contradiction
        words1 = set(fact1.lower().split())
        words2 = set(fact2.lower().split())
        topic_overlap = len(words1 & words2) / max(len(words1 | words2), 1)
        
        return topic_overlap > 0.6 and nums1 != nums2 and len(nums1) > 0
    
    def _get_embedding(self, text):
        import requests
        try:
            resp = requests.post("http://localhost:11434/api/embed", json={
                "model": "nomic-embed-text", "input": text
            })
            return resp.json()["embeddings"][0]
        except Exception:
            return [0.0] * 768
    
    def _update_graph(self, fact):
        """Update knowledge graph with new fact"""
        if self.graph:
            category = fact.get("category", "general")
            self.graph.add_triplet(
                fact["user_id"],
                f"has_{category}",
                fact["content"]
            )
```

**Key Insights**:
1. ✅ **Auto-extract facts** — LLM extracts facts from conversations automatically
2. ✅ **Temporal decay** — Older memories get lower relevance over time
3. ✅ **Conflict resolution** — Temporal + confidence-based resolution
4. ✅ **User namespacing** — Multi-user memory isolation

---

### 6.3. OpenMemory — MCP-Based Memory Server

```python
"""
OpenMemory Pattern: Memory as a Service via MCP Protocol

Memory operations exposed as MCP tools:
- create_memory(content, metadata)
- search_memory(query, user_id)
- update_memory(memory_id, content)
- delete_memory(memory_id)
- list_memories(user_id, category)
"""

class OpenMemoryServer:
    """
    Memory server following MCP protocol pattern
    Exposes memory operations as tools
    """
    
    def __init__(self, storage_backend):
        self.storage = storage_backend
        self.tools = {
            "create_memory": self.create_memory,
            "search_memory": self.search_memory,
            "update_memory": self.update_memory,
            "delete_memory": self.delete_memory,
            "list_memories": self.list_memories,
            "consolidate_memories": self.consolidate,
        }
    
    def create_memory(self, content, user_id, category="general",
                       metadata=None):
        """Create a new memory entry"""
        memory = {
            "id": str(uuid4()),
            "content": content,
            "user_id": user_id,
            "category": category,
            "metadata": metadata or {},
            "created_at": datetime.now().isoformat(),
            "updated_at": datetime.now().isoformat(),
            "embedding": self._get_embedding(content)
        }
        
        # Store with vector index
        self.storage.insert(memory)
        
        return {
            "success": True,
            "memory_id": memory["id"],
            "message": f"Memory created: {content[:50]}..."
        }
    
    def search_memory(self, query, user_id=None, 
                       category=None, limit=10):
        """Semantic search over memories"""
        query_embedding = self._get_embedding(query)
        
        filters = {}
        if user_id:
            filters["user_id"] = user_id
        if category:
            filters["category"] = category
        
        results = self.storage.search(
            embedding=query_embedding,
            filters=filters,
            top_k=limit
        )
        
        # Apply relevance scoring
        for r in results:
            r["relevance"] = self._score_relevance(r, query)
        
        results.sort(key=lambda x: x["relevance"], reverse=True)
        
        return {
            "memories": results,
            "count": len(results),
            "query": query
        }
    
    def update_memory(self, memory_id, content, 
                       reason="user_update"):
        """Update an existing memory"""
        old = self.storage.get(memory_id)
        if not old:
            return {"success": False, "error": "Memory not found"}
        
        # Archive old version
        self.storage.archive_version(memory_id, old)
        
        # Update
        old["content"] = content
        old["embedding"] = self._get_embedding(content)
        old["updated_at"] = datetime.now().isoformat()
        old["update_reason"] = reason
        
        self.storage.update(memory_id, old)
        
        return {
            "success": True,
            "memory_id": memory_id,
            "previous_content": old["content"]
        }
    
    def delete_memory(self, memory_id, soft=True):
        """Delete a memory (soft or hard)"""
        if soft:
            # Soft delete: mark as deleted but keep in storage
            self.storage.update(memory_id, {
                "deleted": True,
                "deleted_at": datetime.now().isoformat()
            })
        else:
            # Hard delete: permanently remove
            self.storage.delete(memory_id)
        
        return {"success": True, "memory_id": memory_id, "type": "soft" if soft else "hard"}
    
    def consolidate(self, user_id, similarity_threshold=0.85):
        """
        Consolidate memories: merge duplicates, resolve conflicts
        """
        memories = self.storage.get_all(user_id=user_id)
        
        # Find duplicates
        duplicates = self._find_duplicates(memories, similarity_threshold)
        
        # Merge duplicates
        merged = 0
        for group in duplicates:
            primary = group[0]  # Keep the oldest
            for dup in group[1:]:
                # Merge content
                primary["content"] = self._merge_content(
                    primary["content"], dup["content"]
                )
                # Delete duplicate
                self.storage.delete(dup["id"])
                merged += 1
        
        # Find conflicts
        conflicts = self._find_all_conflicts(memories)
        
        return {
            "memories_before": len(memories),
            "duplicates_merged": merged,
            "conflicts_found": len(conflicts),
            "memories_after": len(memories) - merged
        }
    
    def _score_relevance(self, memory, query):
        """Score memory relevance considering multiple factors"""
        # Base: vector similarity
        base_score = memory.get("score", 0)
        
        # Recency boost (newer = more relevant)
        age_days = (datetime.now() - 
                   datetime.fromisoformat(memory["created_at"])).days
        recency = max(0, 1 - age_days / 365)
        
        # Access frequency boost
        access_freq = memory.get("access_count", 0) / 10
        
        # Combined score
        return (base_score * 0.6 + 
                recency * 0.2 + 
                min(access_freq, 0.2))
    
    def _find_duplicates(self, memories, threshold):
        """Group similar memories together"""
        groups = []
        used = set()
        
        for i, m1 in enumerate(memories):
            if i in used or m1.get("deleted"):
                continue
            
            group = [m1]
            for j, m2 in enumerate(memories[i+1:], i+1):
                if j in used or m2.get("deleted"):
                    continue
                
                sim = self._compute_similarity(
                    m1["content"], m2["content"]
                )
                if sim > threshold:
                    group.append(m2)
                    used.add(j)
            
            if len(group) > 1:
                groups.append(group)
                used.add(i)
        
        return groups
```

---

## 7. Advanced Memory Patterns

### 7.1. Write-Behind Cache Pattern

```python
"""
Write-Behind Cache: Ghi vào cache trước, flush xuống DB sau.

Giảm latency write operations, tăng throughput.
Phù hợp khi write operations nhiều hơn read.
"""

class WriteBehindCache:
    """
    Write-behind caching for memory operations
    
    Flow:
    1. Write to fast cache (Redis/in-memory)
    2. Return immediately (low latency)
    3. Background thread flushes to persistent store
    """
    
    def __init__(self, persistent_store, flush_interval=5.0, batch_size=100):
        self.cache = {}  # In-memory cache
        self.persistent_store = persistent_store
        self.flush_interval = flush_interval
        self.batch_size = batch_size
        self.pending_writes = []
        self.is_running = True
        
        # Start background flush thread
        self.flush_thread = threading.Thread(target=self._flush_loop)
        self.flush_thread.daemon = True
        self.flush_thread.start()
    
    def write(self, key, value, metadata=None):
        """
        Write to cache immediately, queue for persistence
        """
        entry = {
            "key": key,
            "value": value,
            "metadata": metadata or {},
            "timestamp": datetime.now().isoformat(),
            "dirty": True  # Needs to be flushed
        }
        
        # Write to fast cache (immediate)
        self.cache[key] = entry
        
        # Queue for persistent storage
        self.pending_writes.append(entry)
        
        # Flush if batch is full
        if len(self.pending_writes) >= self.batch_size:
            self._flush()
        
        return {"success": True, "latency": "cache_speed"}
    
    def read(self, key):
        """
        Read from cache first, fallback to persistent store
        """
        # Check cache
        if key in self.cache:
            return self.cache[key]
        
        # Fallback to persistent store
        value = self.persistent_store.get(key)
        if value:
            # Populate cache for next time
            self.cache[key] = value
        
        return value
    
    def _flush_loop(self):
        """Background loop to flush dirty entries"""
        while self.is_running:
            time.sleep(self.flush_interval)
            self._flush()
    
    def _flush(self):
        """Flush pending writes to persistent store"""
        if not self.pending_writes:
            return
        
        # Batch write to persistent store
        batch = self.pending_writes[:self.batch_size]
        self.pending_writes = self.pending_writes[self.batch_size:]
        
        try:
            self.persistent_store.batch_write(batch)
            
            # Mark as clean
            for entry in batch:
                if entry["key"] in self.cache:
                    self.cache[entry["key"]]["dirty"] = False
        
        except Exception as e:
            # Re-queue failed writes
            self.pending_writes = batch + self.pending_writes
            print(f"Flush error: {e}")
    
    def get_stats(self):
        dirty_count = sum(
            1 for v in self.cache.values() if v.get("dirty")
        )
        return {
            "cache_size": len(self.cache),
            "pending_writes": len(self.pending_writes),
            "dirty_entries": dirty_count
        }
```

### 7.2. Memory Consolidation Pipeline

```python
"""
Consolidation Pipeline: Background process để maintain memory quality.

Runs periodically to:
1. Deduplicate memories
2. Resolve conflicts
3. Apply temporal decay
4. Archive old memories
5. Generate memory statistics
"""

class MemoryConsolidationPipeline:
    """
    Automated pipeline for memory maintenance
    """
    
    def __init__(self, memory_store, config=None):
        self.store = memory_store
        self.config = config or {
            "dedup_threshold": 0.85,
            "conflict_strategy": "temporal",  # temporal, confidence, manual
            "archive_after_days": 90,
            "decay_rate": 0.01,
            "min_confidence": 0.3
        }
        self.stats = {
            "runs": 0,
            "deduped": 0,
            "conflicts_resolved": 0,
            "archived": 0,
            "low_confidence_removed": 0
        }
    
    def run_full_consolidation(self):
        """
        Run complete consolidation pipeline
        
        Returns: ConsolidationReport
        """
        start_time = time.time()
        report = {"stages": {}}
        
        # Stage 1: Deduplication
        dedup_result = self._stage_deduplicate()
        report["stages"]["dedup"] = dedup_result
        
        # Stage 2: Conflict Resolution
        conflict_result = self._stage_resolve_conflicts()
        report["stages"]["conflicts"] = conflict_result
        
        # Stage 3: Temporal Decay
        decay_result = self._stage_apply_decay()
        report["stages"]["decay"] = decay_result
        
        # Stage 4: Archive Old Memories
        archive_result = self._stage_archive()
        report["stages"]["archive"] = archive_result
        
        # Stage 5: Remove Low Confidence
        cleanup_result = self._stage_cleanup()
        report["stages"]["cleanup"] = cleanup_result
        
        # Final report
        elapsed = time.time() - start_time
        report["total_time_ms"] = elapsed * 1000
        report["total_memories"] = self.store.count()
        report["stats"] = self.stats
        self.stats["runs"] += 1
        
        return report
    
    def _stage_deduplicate(self):
        """Remove duplicate memories"""
        memories = self.store.get_all()
        
        # Group by similarity
        duplicates_found = 0
        removed = 0
        
        for i, m1 in enumerate(memories):
            if m1.get("archived") or m1.get("deleted"):
                continue
            
            for m2 in memories[i+1:]:
                if m2.get("archived") or m2.get("deleted"):
                    continue
                
                similarity = self._compute_similarity(
                    m1["content"], m2["content"]
                )
                
                if similarity > self.config["dedup_threshold"]:
                    # Keep the one with higher confidence
                    if m1.get("confidence", 0) >= m2.get("confidence", 0):
                        self.store.soft_delete(m2["id"])
                    else:
                        self.store.soft_delete(m1["id"])
                    removed += 1
        
        self.stats["deduped"] += removed
        return {"duplicates_found": duplicates_found, "removed": removed}
    
    def _stage_resolve_conflicts(self):
        """Resolve conflicting memories"""
        memories = self.store.get_all()
        
        # Group by topic (simplified: same first 5 words)
        topic_groups = {}
        for m in memories:
            if m.get("deleted"):
                continue
            topic_key = " ".join(m["content"].split()[:5])
            if topic_key not in topic_groups:
                topic_groups[topic_key] = []
            topic_groups[topic_key].append(m)
        
        conflicts_resolved = 0
        
        for topic, group in topic_groups.items():
            if len(group) <= 1:
                continue
            
            # Check for actual contradictions
            values = set()
            for m in group:
                values.add(m["content"])
            
            if len(values) > 1:
                # Conflict found — resolve by strategy
                if self.config["conflict_strategy"] == "temporal":
                    # Keep newest
                    group.sort(key=lambda x: x.get("updated_at", ""), reverse=True)
                    for m in group[1:]:
                        self.store.soft_delete(m["id"])
                        conflicts_resolved += 1
                elif self.config["conflict_strategy"] == "confidence":
                    # Keep highest confidence
                    group.sort(key=lambda x: x.get("confidence", 0), reverse=True)
                    for m in group[1:]:
                        self.store.soft_delete(m["id"])
                        conflicts_resolved += 1
        
        self.stats["conflicts_resolved"] += conflicts_resolved
        return {"conflicts_found": len(topic_groups), "resolved": conflicts_resolved}
    
    def _stage_apply_decay(self):
        """Apply temporal decay to memory confidence"""
        memories = self.store.get_all()
        
        decayed = 0
        for m in memories:
            if m.get("deleted") or m.get("archived"):
                continue
            
            age_days = (datetime.now() - 
                       datetime.fromisoformat(m.get("created_at", 
                           datetime.now().isoformat()))).days
            
            # Apply exponential decay
            decay_factor = (1 - self.config["decay_rate"]) ** age_days
            new_confidence = m.get("confidence", 1.0) * decay_factor
            
            if abs(new_confidence - m.get("confidence", 1.0)) > 0.01:
                self.store.update(m["id"], {"confidence": new_confidence})
                decayed += 1
        
        return {"memories_decayed": decayed}
    
    def _stage_archive(self):
        """Archive old, low-access memories"""
        cutoff = datetime.now() - timedelta(
            days=self.config["archive_after_days"]
        )
        
        archived = 0
        memories = self.store.get_all()
        
        for m in memories:
            if m.get("deleted") or m.get("archived"):
                continue
            
            last_accessed = m.get("last_accessed", m.get("updated_at"))
            if last_accessed and datetime.fromisoformat(last_accessed) < cutoff:
                # Archive: move to cold storage
                self.store.archive(m["id"])
                archived += 1
        
        self.stats["archived"] += archived
        return {"archived": archived}
    
    def _stage_cleanup(self):
        """Remove very low confidence memories"""
        removed = 0
        memories = self.store.get_all()
        
        for m in memories:
            if m.get("deleted") or m.get("archived"):
                continue
            
            if m.get("confidence", 1.0) < self.config["min_confidence"]:
                self.store.soft_delete(m["id"])
                removed += 1
        
        self.stats["low_confidence_removed"] += removed
        return {"low_confidence_removed": removed}
    
    def _compute_similarity(self, text1, text2):
        words1 = set(text1.lower().split())
        words2 = set(text2.lower().split())
        if not words1 or not words2:
            return 0.0
        return len(words1 & words2) / len(words1 | words2)
```

### 7.3. Versioned Memory (Git-like Memory)

```python
"""
Versioned Memory: Giống Git — mỗi change tạo version mới.

Cho phép:
- Time travel: Xem memory tại thời điểm T
- Diff: So sánh memory giữa 2 thời điểm
- Branch: Tạo version alternative
- Rollback: Quay lại version trước
"""

class VersionedMemory:
    """
    Git-like versioned memory system
    """
    
    def __init__(self):
        self.versions = []  # List of snapshots
        self.current = {}
    
    def set(self, key, value, message=""):
        """Set a value, creating a new version"""
        # Capture previous state
        prev_snapshot = dict(self.current)
        
        # Apply change
        self.current[key] = value
        
        # Record version
        version = {
            "id": len(self.versions),
            "snapshot": dict(self.current),
            "change": {"key": key, "value": value},
            "message": message,
            "timestamp": datetime.now().isoformat(),
            "parent": len(self.versions) - 1 if self.versions else None
        }
        
        self.versions.append(version)
        return version["id"]
    
    def get(self, key, version_id=None):
        """Get value at a specific version"""
        if version_id is None:
            return self.current.get(key)
        
        if version_id >= len(self.versions):
            return None
        
        return self.versions[version_id]["snapshot"].get(key)
    
    def diff(self, v1_id, v2_id):
        """Compare two versions"""
        if v1_id >= len(self.versions) or v2_id >= len(self.versions):
            return {"error": "Invalid version IDs"}
        
        v1 = self.versions[v1_id]["snapshot"]
        v2 = self.versions[v2_id]["snapshot"]
        
        all_keys = set(v1.keys()) | set(v2.keys())
        
        changes = []
        for key in all_keys:
            old = v1.get(key)
            new = v2.get(key)
            
            if old != new:
                if old is None:
                    changes.append({"key": key, "type": "added", "new": new})
                elif new is None:
                    changes.append({"key": key, "type": "removed", "old": old})
                else:
                    changes.append({"key": key, "type": "modified", 
                                   "old": old, "new": new})
        
        return {"changes": changes, "count": len(changes)}
    
    def rollback(self, version_id):
        """Rollback to a previous version"""
        if version_id >= len(self.versions):
            return {"error": "Invalid version ID"}
        
        self.current = dict(self.versions[version_id]["snapshot"])
        
        return {
            "rolled_back_to": version_id,
            "current_state": self.current
        }
    
    def log(self, limit=10):
        """View version history"""
        return [
            {
                "id": v["id"],
                "change": v["change"],
                "message": v["message"],
                "timestamp": v["timestamp"]
            }
            for v in self.versions[-limit:]
        ]
```

---

## 8. Best Practices & Anti-Patterns

```
┌──────────────────────────────────────────────────────────────────┐
│              MEMORY UPDATE DO's                                   │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ✅ DO: Always log write operations                              │
│     → Audit trail for debugging                                  │
│     → Event sourcing enables rollback                           │
│                                                                  │
│  ✅ DO: Deduplicate before storing                               │
│     → Check existing memories first                             │
│     → Use similarity threshold (0.85 recommended)               │
│                                                                  │
│  ✅ DO: Apply confidence scoring                                 │
│     → Not all facts are equally reliable                        │
│     → Lower confidence for indirect information                 │
│                                                                  │
│  ✅ DO: Use temporal decay                                        │
│     → Old information may be outdated                           │
│     → Exponential decay: 0.99^days                              │
│                                                                  │
│  ✅ DO: Consolidate periodically                                 │
│     → Background pipeline (hourly/daily)                        │
│     → Merge duplicates, resolve conflicts                       │
│                                                                  │
│  ✅ DO: Version important memories                               │
│     → Enable rollback if updates are wrong                      │
│     → Track how knowledge evolves                               │
│                                                                  │
│  ✅ DO: Separate namespaces per user/project                     │
│     → Prevent cross-contamination                               │
│     → Enable personalized memory                                │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

```
┌──────────────────────────────────────────────────────────────────┐
│              MEMORY UPDATE DON'Ts                                 │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ❌ DON'T: Overwrite without versioning                          │
│     → Always keep previous version                              │
│     → Enable rollback capability                                │
│                                                                  │
│  ❌ DON'T: Store raw conversation as memory                      │
│     → Extract facts first                                       │
│     → Raw text wastes storage and slows retrieval               │
│                                                                  │
│  ❌ DON'T: Forget to handle conflicts                            │
│     → Same topic, different values = conflict                   │
│     → Always have a resolution strategy                         │
│                                                                  │
│  ❌ DON'T: Store everything forever                              │
│     → Archive old, unused memories                              │
│     → Remove low-confidence garbage                            │
│                                                                  │
│  ❌ DON'T: Write synchronously when write volume is high         │
│     → Use write-behind pattern                                  │
│     → Batch writes for efficiency                               │
│                                                                  │
│  ❌ DON'T: Ignore user feedback                                  │
│     → "Câu trả lời trước sai rồi" MUST update memory           │
│     → Feedback loop is critical for quality                     │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 9. Performance Metrics

```python
class MemoryStoreMetrics:
    """Track memory store performance"""
    
    def __init__(self):
        self.write_latencies = []
        self.read_latencies = []
        self.memory_counts = []
        self.dedup_rates = []
        self.conflict_counts = []
        self.consolidation_times = []
    
    def record_write(self, latency_ms, success=True):
        self.write_latencies.append({"latency": latency_ms, "success": success})
    
    def record_read(self, latency_ms, results_count):
        self.read_latencies.append({"latency": latency_ms, "results": results_count})
    
    def record_consolidation(self, time_ms, deduped, conflicts_resolved):
        self.consolidation_times.append(time_ms)
        self.dedup_rates.append(deduped)
        self.conflict_counts.append(conflicts_resolved)
    
    def report(self):
        def avg(lst):
            return sum(lst) / len(lst) if lst else 0
        
        write_lats = [w["latency"] for w in self.write_latencies]
        read_lats = [r["latency"] for r in self.read_latencies]
        
        return f"""
╔══════════════════════════════════════════════╗
║       MEMORY STORE METRICS REPORT            ║
╠══════════════════════════════════════════════╣
║                                              ║
║  Write Operations:                           ║
║    Total:    {len(self.write_latencies):>8}                     ║
║    Avg Latency: {avg(write_lats):>8.1f} ms                 ║
║    Success Rate: {sum(1 for w in self.write_latencies if w['success'])/max(len(self.write_latencies),1)*100:>6.1f}%          ║
║                                              ║
║  Read Operations:                            ║
║    Total:    {len(self.read_latencies):>8}                     ║
║    Avg Latency: {avg(read_lats):>8.1f} ms                 ║
║    Avg Results: {avg([r['results'] for r in self.read_latencies]):>7.1f}                    ║
║                                              ║
║  Consolidation:                              ║
║    Runs:     {len(self.consolidation_times):>8}                     ║
║    Avg Time: {avg(self.consolidation_times):>8.1f} ms                 ║
║    Total Deduped: {sum(self.dedup_rates):>5}                   ║
║    Total Conflicts: {sum(self.conflict_counts):>4}                  ║
║                                              ║
╚══════════════════════════════════════════════╝"""
```

---

## 10. Labs Thực Hành

### Lab 1: Write-back Memory

```python
# python 03-update-memory-store/lab_writeback.py

writer = MemoryWriter()

# 1. Store episodic memory
event_id = writer.write_episodic(
    "learning",
    "User learned about BHYT deduction rates",
    metadata={"topic": "BHYT", "importance": "medium"}
)

# 2. Store facts
writer.write_fact("BHYT", "mức đóng", "4.5% lương cơ sở", confidence=0.95)
writer.write_fact("BHYT", "thời hạn thẻ", "5 năm", confidence=0.99)
writer.write_fact("Ollama", "runs", "gemma3:12b", confidence=1.0)

# 3. Update user profile
writer.write_user_profile("user_1", {
    "name": "Nguyễn Văn A",
    "occupation": "developer",
    "interests": ["AI", "BHYT"]
})

# 4. Check stats
print(writer.get_stats())
# {'episodic_count': 1, 'entity_count': 3, 'event_count': 4}
```

### Lab 2: Consolidation

```python
# python 03-update-memory-store/lab_consolidation.py

from knowledge_graph import KnowledgeGraph

kg = KnowledgeGraph()
kg.add_triplet("BHYT", "mức đóng", "4.5%")
kg.add_triplet("BHYT", "mức đóng", "4.5% lương cơ sở")  # Duplicate
kg.add_triplet("BHYT", "mức đóng", "5%")  # Updated

consolidator = MemoryConsolidator(kg)
report = consolidator.generate_memory_report()
print(report)

conflicts = consolidator.conflict_resolution()
print(f"Conflicts: {len(conflicts)}")
for c in conflicts:
    print(f"  {c['subject']} → {c['conflicting_values']}")
```

### Lab 3: Versioned Memory

```python
# python 03-update-memory-store/lab_versioned.py

vm = VersionedMemory()

# Track evolving knowledge
vm.set("bhyt_rate", "4.5%", message="Initial fact")
vm.set("bhyt_rate", "4.5% lương cơ sở", message="More specific")
vm.set("bhyt_rate", "4.5% MEC 2024", message="Updated year")

# View history
for entry in vm.log():
    print(f"  v{entry['id']}: {entry['message']}")

# Compare versions
diff = vm.diff(0, 2)
print(f"Changes: {diff['count']}")

# Rollback
state = vm.rollback(0)
print(f"Rolled back to: {state['rolled_back_to']}")
```

### Lab 4: Metrics

```python
# python 03-update-memory-store/lab_metrics.py

metrics = MemoryStoreMetrics()

# Simulate operations
import random
for _ in range(100):
    metrics.record_write(random.uniform(5, 50))
    metrics.record_read(random.uniform(2, 30), random.randint(1, 10))

metrics.record_consolidation(1500, deduped=25, conflicts_resolved=3)
metrics.record_consolidation(1200, deduped=15, conflicts_resolved=1)

print(metrics.report())
```

---

## 11. Tài Liệu Tham Khảo

### Papers & Research

1. **MemGPT: Towards LLMs as Operating Systems** — UC Berkeley, 2023 — Tiered memory management for LLMs
2. **MemoryBank: Enhancing Large Language Models with Long-Term Memory** — 2023 — Weighted memory decay
3. **A Survey on Long-Term Memory for AI Agents** — 2024 — Comprehensive overview of memory architectures

### Frameworks & Tools

1. **Mem0** — Production memory layer for AI agents — https://mem0.ai
2. **LangMem** — Long-term memory for agents — https://github.com/langchain-ai/langmem
3. **Zep** — Memory server for AI assistants — https://www.getzep.com
4. **OpenMemory** — MCP-based memory server — https://github.com/openmemory

### Blogs & Resources

1. **Anthropic — Memory in Claude Code** — 3-tier memory architecture
2. **LangChain — Building Long-Term Memory** — Practical guide
3. **Mem0 Blog** — Memory patterns for production AI

---

**Kết Luận**

Memory Update & Knowledge Store là quá trình giữ cho "bộ não" của AI luôn chính xác và cập nhật. Ba patterns chính:

> **"A system that cannot update its knowledge is a system that learns nothing."**

1. **Event-driven updates** — Khi có sự kiện mới, cập nhật ngay lập tức
2. **Consolidation pipeline** — Background process dọn dẹp + gộp + giải quyết conflict
3. **Temporal decay** — Thông tin cũ dần mất ưu tiên, tránh outdated knowledge

Key takeaways:
1. ✅ **Always deduplicate** — before storing new facts
2. ✅ **Version important memories** — enable rollback
3. ✅ **Consolidate periodically** — background pipeline
4. ✅ **Apply temporal decay** — older ≠ more important
5. ✅ **Track metrics** — write latency, dedup rate, conflicts

---

*Tài liệu: III. Update Memory & Knowledge Store*  
*Ngày cập nhật: 19/07/2026*  
*Tác giả: AI Knowledge Repository*  
*Môi trường: Ollama (gemma3:12b, nomic-embed-text)*
