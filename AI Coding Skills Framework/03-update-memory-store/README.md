# 💾 III. Update Memory & Knowledge Store

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

## 6. Labs Thực Hành

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

from knowledge_graph import KnowledgeGraph  # From Part I

kg = KnowledgeGraph()
kg.add_triplet("BHYT", "mức đóng", "4.5%")
kg.add_triplet("BHYT", "mức đóng", "4.5% lương cơ sở")  # Duplicate-ish
kg.add_triplet("BHYT", "mức đóng", "5%")  # Updated

consolidator = MemoryConsolidator(kg)
report = consolidator.generate_memory_report()
print(report)

conflicts = consolidator.conflict_resolution()
print(f"Conflicts: {len(conflicts)}")
for c in conflicts:
    print(f"  {c['subject']} → {c['conflicting_values']}")
```

---

*Tài liệu: III. Update Memory & Knowledge Store*
*Ngày tạo: 2026-07-11*