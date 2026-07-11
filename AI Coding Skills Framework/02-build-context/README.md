# 🔨 II. Build Context

## Tổng Quan

**Build Context** là quá trình **tổ chức và quản lý thông tin** để đưa vào prompt của LLM. Context tốt giúp model hiểu rõ hơn, trả lời chính xác hơn, và tránh hallucination.

```
┌──────────────────────────────────────────────────────────────────┐
│                      BUILD CONTEXT                                │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │                                                            │  │
│  │  Retrieved Info     Context Building       Optimized      │  │
│  │  ┌──────┐          ┌──────────────┐       ┌──────────┐   │  │
│  │  │ Doc1 │──────┐   │  Budget      │       │ System   │   │  │
│  │  ├──────┤      │   │  Allocation  │       │ Prompt   │   │  │
│  │  │ Doc2 │──────┼──►│  ──►         │──────►│ Context  │   │  │
│  │  ├──────┤      │   │  Compress    │       │ Query    │   │  │
│  │  │ Doc3 │──────┘   │  ──►         │       │ Response │   │  │
│  │  └──────┘          │  Structure   │       └──────────┘   │  │
│  │                    │  ──►         │                       │  │
│  │  Chat History      │  Hierarchical│       Token-efficient │  │
│  │  ┌──────┐          └──────────────┘       Context         │  │
│  │  │ Msg1 │                                             │  │
│  │  ├──────┤          ┌──────────────┐                    │  │
│  │  │ Msg2 │─────────►│  Prompt      │                    │  │
│  │  ├──────┤          │  Engineering │                    │  │
│  │  │ Msg3 │─────────►│  ──►         │                    │  │
│  │  └──────┘          └──────────────┘                    │  │
│  │                                                            │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## Nội Dung

| # | Chủ đề | Mô tả |
|---|--------|-------|
| 1 | [Context Window Management](#1-context-window-management) | Quản lý kích thước context window |
| 2 | [Context Construction Strategies](#2-context-construction-strategies) | Các chiến lược xây dựng context |
| 3 | [Context Compression](#3-context-compression--summarization) | Nén và tóm tắt context |
| 4 | [Prompt Engineering for Context](#4-prompt-engineering-for-context) | Prompt templates cho context |
| 5 | [Hierarchical Context](#5-hierarchical-context) | Cấu trúc context phân cấp |
| 6 | [Streaming Context](#6-streaming-context) | Xử lý context real-time |

---

## 1. Context Window Management

### 1.1 Context Window Là Gì?

Context window là **bộ nhớ tạm thời** của LLM — tất cả token mà model có thể "nhìn thấy" tại một thời điểm. Quản lý nó hiệu quả là kỹ năng quan trọng nhất.

```
┌──────────────────────────────────────────────────────────────────┐
│                   CONTEXT WINDOW ANATOMY                         │
│                                                                  │
│  ◄─────────────────── Total Context (e.g., 128K tokens) ──────► │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │░░░░░░░ System Prompt ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░│  │
│  │░░░░░░░ (Instructions, personality, rules)  ~500 tokens ░░░░│  │
│  ├────────────────────────────────────────────────────────────┤  │
│  │████████ Retrieved Context █████████████████████████████████│  │
│  │████████ (RAG documents, knowledge base)    2K-8K tokens ███│  │
│  ├────────────────────────────────────────────────────────────┤  │
│  │▓▓▓▓▓▓▓▓ Conversation History ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│  │
│  │▓▓▓▓▓▓▓▓ (Past messages)              Variable ▓▓▓▓▓▓▓▓▓▓▓▓│  │
│  ├────────────────────────────────────────────────────────────┤  │
│  │▒▒▒▒▒▒▒▒ Tool Results ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒│  │
│  │▒▒▒▒▒▒▒▒ (API responses, code output)  Variable ▒▒▒▒▒▒▒▒▒▒│  │
│  ├────────────────────────────────────────────────────────────┤  │
│  │                                                            │  │
│  │  User Query                                  100-500 tokens│  │
│  │                                                            │  │
│  ├────────────────────────────────────────────────────────────┤  │
│  │                                                            │  │
│  │  Reserved for Output                        1K-4K tokens   │  │
│  │                                                            │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
│  ⚠️  Khi context đầy:                                          │
│  - FIFO: Xóa message cũ nhất                                  │
│  - Truncation: Cắt bớt context (mất thông tin!)              │
│  - Error: Reject request                                       │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 1.2 Token Budget Allocation

```python
class ContextBudget:
    """
    Quản lý ngân sách token cho các thành phần context
    
    Mục tiêu: Phân bổ token một cách thông minh để maximize
    thông tin trong khi respects context window limit
    """
    
    def __init__(self, total_tokens=128000, reserve_output=4000):
        self.total = total_tokens
        self.reserve_output = reserve_output
        self.available = total_tokens - reserve_output
        
        # Default allocation (tunable)
        self.allocation = {
            "system_prompt": 0.05,      # 5% - Instructions
            "retrieved_context": 0.50,  # 50% - RAG documents
            "conversation": 0.30,       # 30% - Chat history
            "tool_results": 0.10,       # 10% - Tool/API outputs
            "user_query": 0.05,         # 5%  - Current query
        }
    
    def get_budget(self, component):
        """Get max tokens for a specific component"""
        return int(self.available * self.allocation.get(component, 0))
    
    def adjust_for_query_type(self, query_type):
        """
        Dynamic allocation based on query complexity
        
        query_type: "simple", "complex", "conversational", "retrieval"
        """
        if query_type == "simple":
            # Simple questions need less context
            self.allocation = {
                "system_prompt": 0.10,
                "retrieved_context": 0.30,
                "conversation": 0.40,
                "tool_results": 0.10,
                "user_query": 0.10,
            }
        elif query_type == "complex":
            # Complex questions need more context
            self.allocation = {
                "system_prompt": 0.05,
                "retrieved_context": 0.60,
                "conversation": 0.15,
                "tool_results": 0.15,
                "user_query": 0.05,
            }
        elif query_type == "conversational":
            # Conversations need more history
            self.allocation = {
                "system_prompt": 0.05,
                "retrieved_context": 0.20,
                "conversation": 0.55,
                "tool_results": 0.10,
                "user_query": 0.10,
            }
        elif query_type == "retrieval":
            # Heavy RAG needs most context space
            self.allocation = {
                "system_prompt": 0.03,
                "retrieved_context": 0.70,
                "conversation": 0.10,
                "tool_results": 0.12,
                "user_query": 0.05,
            }
    
    def fit_text_to_budget(self, text, component, tokenizer_func=None):
        """
        Truncate or compress text to fit within budget
        
        tokenizer_func: function(text) -> list of tokens
        """
        budget = self.get_budget(component)
        
        if tokenizer_func:
            tokens = tokenizer_func(text)
            if len(tokens) <= budget:
                return text
            # Truncate
            truncated_tokens = tokens[:budget]
            return "".join(truncated_tokens)  # simplified
        else:
            # Rough estimate: ~4 chars per token
            max_chars = budget * 4
            if len(text) <= max_chars:
                return text
            return text[:max_chars] + "\n[...truncated...]"
    
    def report(self):
        """Print budget allocation report"""
        print(f"\n{'='*55}")
        print(f"{'CONTEXT BUDGET REPORT':^55}")
        print(f"{'='*55}")
        print(f"{'Total Context:':<30} {self.total:>12,} tokens")
        print(f"{'Output Reserve:':<30} {self.reserve_output:>12,} tokens")
        print(f"{'Available:':<30} {self.available:>12,} tokens")
        print(f"{'-'*55}")
        
        for comp, pct in self.allocation.items():
            tokens = self.get_budget(comp)
            bar = '█' * int(pct * 40)
            print(f"  {comp:<25} {tokens:>8,}  {pct*100:>4.0f}% {bar}")
        
        print(f"{'='*55}")


# Usage
budget = ContextBudget(total_tokens=128000)
budget.report()

# Adjust for different scenarios
print("\n--- For Complex RAG Query ---")
budget.adjust_for_query_type("complex")
budget.report()
```

```
OUTPUT:
=======================================================
              CONTEXT BUDGET REPORT               
=======================================================
Total Context:                        128,000 tokens
Output Reserve:                         4,000 tokens
Available:                            124,000 tokens
-------------------------------------------------------
  system_prompt                    6,200    5% ██
  retrieved_context               62,000   50% ████████████████████████
  conversation                    37,200   30% ██████████████
  tool_results                    12,400   10% ████
  user_query                       6,200    5% ██
=======================================================
```

### 1.3 Context Window Sizes — So Sánh

```
┌──────────────────────────────────────────────────────────────────┐
│               CONTEXT WINDOW SIZES ACROSS MODELS                 │
│                                                                  │
│  Model                    Context    Price     Best For          │
│  ──────────────────────── ────────── ───────── ──────────────   │
│  GPT-3.5 Turbo            4K/16K    $         Simple tasks      │
│  GPT-4                     8K/32K    $$$       General           │
│  GPT-4 Turbo               128K      $$$       Large documents   │
│  GPT-4o                    128K      $$$       Multimodal        │
│  Claude 3 Haiku            200K      $         Fast & large      │
│  Claude 3 Opus             200K      $$$$      Complex reasoning │
│  Gemini 1.5 Pro            1M        $$$       Massive context   │
│  Gemini 2.5 Pro            1M+       $$$       Latest            │
│  Llama 3.1 (8B)            128K      Free*     Open source       │
│  gemma3:12b (your model)   128K      Free*     Local RAG         │
│                                                                  │
│  * Free locally with Ollama                                      │
│                                                                  │
│  BIGGER CONTEXT ≠ BETTER                                        │
│  ├── "Lost in the Middle" problem                              │
│  ├── Models focus on beginning and end of context              │
│  ├── More context = more noise potential                       │
│  └── Better to have SMALL + RELEVANT context                  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 1.4 "Lost in the Middle" Problem

```
┌──────────────────────────────────────────────────────────────────┐
│              THE "LOST IN THE MIDDLE" PROBLEM                    │
│                                                                  │
│  Research Finding: LLMs pay more attention to information       │
│  at the BEGINNING and END of the context,                      │
│  but less attention to information in the MIDDLE.              │
│                                                                  │
│  Attention Pattern:                                             │
│                                                                  │
│  High  ┃████                                              ████┃ │
│        ┃████                                              ████┃ │
│        ┃██████                                          ████┃  │
│  Med   ┃████████                                      ████┃    │
│        ┃██████████                                  ████┃      │
│        ┃████████████                              ████┃        │
│  Low   ┃████████████████████████████████████████████┃          │
│        ┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━│
│         Beginning              Middle              End          │
│                                                                  │
│  SOLUTIONS:                                                     │
│  1. Put most important info at BEGINNING and END               │
│  2. Use shorter context (fewer, better docs)                   │
│  3. Repeat key information at strategic positions              │
│  4. Use re-ranking to put best docs first                      │
│  5. Use structured prompts that guide attention                │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 2. Context Construction Strategies

### 2.1 5 Strategies Tổng Quan

```
┌──────────────────────────────────────────────────────────────────┐
│              CONTEXT CONSTRUCTION STRATEGIES                      │
│                                                                  │
│  Strategy 1: PASS-THROUGH                                       │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ Retrieved ──► All docs ──► Directly into prompt          │   │
│  │ ✅ Simple, ✅ Complete info                              │   │
│  │ ❌ Noisy, ❌ Token waste                                 │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  Strategy 2: SELECTIVE                                          │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ Retrieved ──► Filter by relevance ──► Top-K only         │   │
│  │ ✅ Focused, ✅ Token-efficient                           │   │
│  │ ❌ May miss relevant info                                │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  Strategy 3: COMPRESSED                                         │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ Retrieved ──► LLM summarizes ──► Compact version         │   │
│  │ ✅ Efficient, ✅ Key info preserved                      │   │
│  │ ❌ May lose details, ❌ Extra LLM call                   │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  Strategy 4: STRUCTURED                                         │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ Retrieved ──► Categorize ──► Organized by type/topic     │   │
│  │ ✅ Organized, ✅ Easy to scan                           │   │
│  │ ❌ Schema design overhead                                │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  Strategy 5: ADAPTIVE                                           │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ Query analysis ──► Choose strategy ──► Dynamic assembly  │   │
│  │ ✅ Optimal for each query                                │   │
│  │ ❌ Complex to implement                                  │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 2.2 Context Building Patterns — Code Examples

```python
import json
from datetime import datetime

# ============================================================
# PATTERN 1: Citation-based Context
# ============================================================
def build_citation_context(documents):
    """
    Gán số nguồn [1], [2], [3]... cho mỗi document
    
    Phù hợp khi cần trích dẫn nguồn trong câu trả lời
    """
    context_parts = []
    for i, doc in enumerate(documents, 1):
        source = doc.get("source", "unknown")
        date = doc.get("date", "")
        content = doc["content"]
        context_parts.append(
            f"[{i}] ({source}, {date})\n{content}"
        )
    
    return "TÀI LIỆU THAM KHẢO:\n" + "\n\n".join(context_parts)


# ============================================================
# PATTERN 2: Relevance-ranked Context
# ============================================================
def build_ranked_context(documents, scores):
    """
    Sắp xếp context theo mức độ liên quan
    
    Thông tin quan trọng nhất ở đầu và cuối (tránh "lost in middle")
    """
    ranked = sorted(
        zip(documents, scores), 
        key=lambda x: x[1], 
        reverse=True
    )
    
    context = []
    
    # Group by relevance level
    high = [(d, s) for d, s in ranked if s > 0.8]
    medium = [(d, s) for d, s in ranked if 0.5 < s <= 0.8]
    low = [(d, s) for d, s in ranked if s <= 0.5]
    
    if high:
        context.append("🔴 THÔNG TIN QUAN TRỌNG NHẤT:")
        for doc, score in high:
            context.append(f"  • {doc['content']}")
    
    if medium:
        context.append("\n🟡 THÔNG TIN BỔ SUNG:")
        for doc, score in medium:
            context.append(f"  • {doc['content']}")
    
    if low:
        context.append("\n🔵 THÔNG TIN LIÊN QUAN:")
        for doc, score in low:
            context.append(f"  • {doc['content']}")
    
    return "\n".join(context)


# ============================================================
# PATTERN 3: Categorized Context
# ============================================================
def build_categorized_context(documents):
    """
    Phân loại context theo chủ đề/loại
    
    Giúp LLM dễ dàng tìm thông tin theo category
    """
    categories = {}
    for doc in documents:
        cat = doc.get("category", "general")
        if cat not in categories:
            categories[cat] = []
        categories[cat].append(doc)
    
    parts = []
    for cat_name, docs in sorted(categories.items()):
        parts.append(f"## {cat_name.upper()}")
        for doc in docs:
            parts.append(f"- {doc['content']}")
        parts.append("")  # blank line
    
    return "\n".join(parts)


# ============================================================
# PATTERN 4: Conversation-aware Context
# ============================================================
def build_conversational_context(query, docs, chat_history, user_profile=None):
    """
    Xây context考虑到 cuộc trò chuyện hiện tại
    
    Kết hợp: user profile + chat history + retrieved docs
    """
    parts = []
    
    # User profile (if available)
    if user_profile:
        parts.append("👤 VỀ NGƯỜI DÙNG:")
        parts.append(f"  Tên: {user_profile.get('name', 'N/A')}")
        parts.append(f"  Sở thích: {', '.join(user_profile.get('interests', []))}")
        parts.append(f"  Ngôn ngữ: {user_profile.get('language', 'vi')}")
        parts.append("")
    
    # Recent conversation
    if chat_history:
        parts.append("💬 CUỘC TRÒ CHUYỆN GẦN ĐÂY:")
        for msg in chat_history[-5:]:  # Last 5 messages
            role = "👤" if msg["role"] == "user" else "🤖"
            parts.append(f"  {role} {msg['content'][:100]}")
        parts.append("")
    
    # Retrieved documents
    if docs:
        parts.append(f"📚 THÔNG TIN THAM KHẢO ({len(docs)} nguồn):")
        for i, doc in enumerate(docs, 1):
            relevance = doc.get("score", 0)
            parts.append(f"  [{i}] (liên quan: {relevance:.0%}) {doc['content']}")
        parts.append("")
    
    parts.append(f"❓ CÂU HỎI HIỆN TẠI: {query}")
    
    return "\n".join(parts)


# ============================================================
# PATTERN 5: Multi-source Context
# ============================================================
def build_multisource_context(sources):
    """
    Kết hợp nhiều nguồn thông tin khác nhau
    
    sources: dict of {source_name: [documents]}
    
    Ví dụ:
    - Official docs (ưu tiên cao nhất)
    - Expert analysis
    - User data
    - Web search results
    """
    priority_order = ["official", "expert", "database", "web", "general"]
    
    parts = []
    parts.append("Bạn có quyền truy cập các nguồn sau (ưu tiên giảm dần):")
    parts.append("")
    
    for source_name in priority_order:
        if source_name in sources:
            docs = sources[source_name]
            parts.append(f"=== Nguồn: {source_name.upper()} (ưu tiên: {priority_order.index(source_name)+1}) ===")
            for doc in docs:
                parts.append(f"  • {doc}")
            parts.append("")
    
    parts.append("HƯỚNG DẪN: Ưu tiên thông tin từ nguồn oficial, "
                 "sau đó đến expert, rồi đến các nguồn khác.")
    
    return "\n".join(parts)


# ============================================================
# USAGE EXAMPLE
# ============================================================
if __name__ == "__main__":
    # Sample documents
    docs = [
        {"content": "BHYT là bảo hiểm bắt buộc", "source": "Luật BHYT", 
         "category": "legal", "score": 0.95},
        {"content": "Mức đóng 4.5% lương cơ sở", "source": "QĐ 105", 
         "category": "finance", "score": 0.88},
        {"content": "Thẻ BHYT有效期 5 năm", "source": "Luật BHYT", 
         "category": "legal", "score": 0.72},
        {"content": "Khám tại tuyến huyện trở lên", "source": "Hướng dẫn", 
         "category": "medical", "score": 0.65},
    ]
    
    print("=== Citation Context ===")
    print(build_citation_context(docs))
    
    print("\n=== Categorized Context ===")
    print(build_categorized_context(docs))
```

---

## 3. Context Compression & Summarization

### 3.1 Tại Sao Cần Compression?

```
┌──────────────────────────────────────────────────────────────────┐
│                 CONTEXT COMPRESSION NEEDED                        │
│                                                                  │
│  Scenario: 10 documents, mỗi doc ~2000 tokens                  │
│  Total raw context: ~20,000 tokens                              │
│  Budget available: ~8,000 tokens                                │
│                                                                  │
│  Problem: 20,000 > 8,000 → PHẢI nén!                          │
│                                                                  │
│  Compression Ratio = compressed_size / original_size             │
│                                                                  │
│  Target: 20,000 → 8,000 tokens (40% ratio)                     │
│                                                                  │
│  Techniques:                                                     │
│  ├── Map-Reduce: Tóm tắt từng doc rồi gộp                     │
│  ├── Extractive: Chọn câu quan trọng nhất                     │
│  ├── Selective: Chỉ giữ key facts                              │
│  ├── LLMLingua: Bỏ token ít quan trọng                        │
│  └── LLM Compression: Dùng LLM để tóm tắt                    │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 3.2 Compression Techniques

```python
import numpy as np

class ContextCompressor:
    """
    Collection of context compression techniques
    """
    
    def __init__(self, llm_func=None):
        """
        llm_func: function(prompt) -> response
        Nếu không có LLM, dùng rule-based compression
        """
        self.llm = llm_func
    
    # --------------------------------------------------------
    # Technique 1: MAP-REDUCE SUMMARIZATION
    # --------------------------------------------------------
    def map_reduce_summarize(self, documents, target_ratio=0.4):
        """
        Tóm tắt từng document riêng biệt (map),
        sau đó gộp tất cả summaries lại (reduce).
        
        Phù hợp: Nhiều documents, cần tóm tắt toàn cục
        """
        # MAP: Summarize each document individually
        summaries = []
        for doc in documents:
            text = doc if isinstance(doc, str) else doc.get("content", "")
            summary = self._summarize_text(text, ratio=target_ratio * 2)
            summaries.append(summary)
        
        # REDUCE: Combine all summaries
        combined = "\n".join(f"- {s}" for s in summaries)
        
        if self.llm and len(combined) > 200:
            final = self.llm(
                f"Gộp các tóm tắt sau thành một đoạn ngắn, "
                f"giữ lại thông tin quan trọng nhất:\n\n{combined}\n\n"
                f"Tóm tắt gộp:"
            )
        else:
            final = combined
        
        return final
    
    # --------------------------------------------------------
    # Technique 2: EXTRACTIVE COMPRESSION
    # --------------------------------------------------------
    def extractive_compress(self, text, num_sentences=5):
        """
        Chọn các câu QUAN TRỌNG NHẤT từ văn bản
        
        Dùng TF-IDF scoring để xác định câu quan trọng
        
        Phù hợp: Cần giữ nguyên wording, không paraphrase
        """
        sentences = [s.strip() for s in text.split('. ') if s.strip()]
        
        if len(sentences) <= num_sentences:
            return text
        
        # Simple TF-IDF scoring
        word_freq = {}
        for sent in sentences:
            words = sent.lower().split()
            for word in words:
                word_freq[word] = word_freq.get(word, 0) + 1
        
        # Score each sentence
        scored = []
        for sent in sentences:
            words = sent.lower().split()
            score = sum(word_freq.get(w, 0) for w in words) / max(len(words), 1)
            scored.append((sent, score))
        
        # Select top sentences (maintain original order)
        scored.sort(key=lambda x: x[1], reverse=True)
        top_sentences = [s for s, _ in scored[:num_sentences]]
        
        # Re-order by original position
        ordered = sorted(top_sentences, key=lambda s: text.index(s))
        
        return '. '.join(ordered) + '.'
    
    # --------------------------------------------------------
    # Technique 3: SELECTIVE KEY-FACT EXTRACTION
    # --------------------------------------------------------
    def selective_compress(self, text, key_fields=None):
        """
        Trích xuất key facts theo structured format
        
        Phù hợp: Cần structured output, dễ parse
        """
        if self.llm:
            prompt = f"""Trích xuất các thông tin quan trọng nhất từ văn bản sau.
            
Văn bản:
{text}

Trích xuất theo format:
- Chủ đề: ...
- Số liệu chính: ...
- Kết luận: ...
- Điều kiện/Loại trừ: ...

Trích xuất:"""
            return self.llm(prompt)
        
        # Rule-based fallback
        facts = []
        lines = text.split('\n')
        for line in lines:
            line = line.strip()
            if not line:
                continue
            # Lines with numbers/dates are usually important
            if any(c.isdigit() for c in line):
                facts.append(f"• {line}")
            # Lines at the beginning/end are usually important
            elif line.startswith(('Điều', 'Khoản', 'Mục', '#')):
                facts.append(f"• {line}")
        
        return '\n'.join(facts[:10])  # Limit to 10 facts
    
    # --------------------------------------------------------
    # Technique 4: SLIDING WINDOW HIERARCHICAL SUMMARY
    # --------------------------------------------------------
    def hierarchical_summarize(self, text, window_size=3, max_depth=5):
        """
        Tóm tắt theo cấp bậc using sliding window
        
        Level 1: Summarize each window of paragraphs
        Level 2: Summarize each window of Level-1 summaries
        ...continue until within budget...
        
        Phù hợp: Văn bản rất dài, cần tóm tắt nhiều cấp
        """
        paragraphs = [p.strip() for p in text.split('\n\n') if p.strip()]
        
        return self._recursive_summarize(paragraphs, window_size, 0, max_depth)
    
    def _recursive_summarize(self, items, window_size, depth, max_depth):
        if depth >= max_depth or len(items) <= window_size:
            return '\n\n'.join(items)
        
        summaries = []
        for i in range(0, len(items), window_size):
            window = '\n\n'.join(items[i:i + window_size])
            if self.llm:
                summary = self.llm(
                    f"Tóm tắt đoạn văn sau trong 1-2 câu:\n\n{window}\n\nTóm tắt:"
                )
            else:
                # Simple: take first sentence of each paragraph
                summary = '. '.join(
                    p.split('.')[0] + '.' 
                    for p in window.split('\n\n')[:2]
                )
            summaries.append(summary)
        
        return self._recursive_summarize(summaries, window_size, depth + 1, max_depth)
    
    # --------------------------------------------------------
    # Technique 5: DEDUPLICATION
    # --------------------------------------------------------
    def deduplicate(self, documents, similarity_threshold=0.85):
        """
        Loại bỏ documents trùng lặp hoặc quá giống nhau
        
        Giảm context size bằng cách merge similar docs
        """
        if not documents:
            return documents
        
        # Simple dedup by text similarity
        unique = [documents[0]]
        
        for doc in documents[1:]:
            doc_text = doc if isinstance(doc, str) else doc.get("content", "")
            is_duplicate = False
            
            for existing in unique:
                existing_text = existing if isinstance(existing, str) else existing.get("content", "")
                similarity = self._text_similarity(doc_text, existing_text)
                
                if similarity > similarity_threshold:
                    is_duplicate = True
                    break
            
            if not is_duplicate:
                unique.append(doc)
        
        return unique
    
    def _text_similarity(self, text1, text2):
        """Simple word overlap similarity"""
        words1 = set(text1.lower().split())
        words2 = set(text2.lower().split())
        
        if not words1 or not words2:
            return 0.0
        
        intersection = words1 & words2
        union = words1 | words2
        
        return len(intersection) / len(union)
    
    def _summarize_text(self, text, ratio=0.3):
        """Summarize a single text"""
        if self.llm:
            return self.llm(
                f"Tóm tắt văn bản sau thành {ratio*100:.0f}% độ dài gốc:\n\n{text}\n\nTóm tắt:"
            )
        
        # Simple truncation
        words = text.split()
        n = max(int(len(words) * ratio), 1)
        return ' '.join(words[:n])


# Usage
compressor = ContextCompressor(llm_func=my_llm_function)

# Compress 10 documents into compact context
documents = [...]  # Your documents
compressed = compressor.map_reduce_summarize(documents, target_ratio=0.4)
print(f"Original: {sum(len(d) for d in documents)} chars")
print(f"Compressed: {len(compressed)} chars")
print(f"Ratio: {len(compressed)/sum(len(d) for d in documents):.1%}")
```

### 3.3 Compression Comparison

```
┌──────────────────────┬──────────┬──────────┬──────────┬──────────────────┐
│ Technique            │ Quality  │ Speed    │ Size     │ Best For         │
│                      │          │          │ Reduction│                  │
├──────────────────────┼──────────┼──────────┼──────────┼──────────────────┤
│ Map-Reduce           │ ⭐⭐⭐⭐  │ ⭐⭐      │ 60-70%   │ Multi-doc        │
│ Extractive           │ ⭐⭐⭐    │ ⭐⭐⭐⭐⭐  │ 50-60%   │ Keep exact words │
│ Selective            │ ⭐⭐⭐⭐  │ ⭐⭐⭐    │ 70-80%   │ Key facts        │
│ Hierarchical         │ ⭐⭐⭐⭐⭐│ ⭐       │ 80-90%   │ Very long text   │
│ Deduplication        │ ⭐⭐     │ ⭐⭐⭐⭐   │ 20-40%   │ Redundant docs   │
│ LLMLingua            │ ⭐⭐⭐⭐  │ ⭐⭐⭐    │ 60-80%   │ Token-level      │
└──────────────────────┴──────────┴──────────┴──────────┴──────────────────┘
```

---

## 4. Prompt Engineering for Context

### 4.1 Prompt Templates

```
┌──────────────────────────────────────────────────────────────────┐
│              PROMPT TEMPLATES FOR RAG CONTEXT                     │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ TEMPLATE 1: BASIC RAG                                    │   │
│  │                                                          │   │
│  │ System: Bạn là trợ lý. Trả lời DỰA TRÊN thông tin     │   │
│  │ được cung cấp. Nếu không đủ thông tin, nói rõ.        │   │
│  │                                                          │   │
│  │ Context:                                                  │   │
│  │ [1] {doc1}                                               │   │
│  │ [2] {doc2}                                               │   │
│  │                                                          │   │
│  │ User: {query}                                            │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ TEMPLATE 2: CHAIN-OF-THOUGHT RAG                        │   │
│  │                                                          │   │
│  │ System: Bạn là trợ lý phân tích.                       │   │
│  │                                                          │   │
│  │ Bước 1: Đọc kỹ thông tin được cung cấp.                │   │
│  │ Bước 2: Xác định thông tin liên quan.                   │   │
│  │ Bước 3: Phân tích và tổng hợp.                          │   │
│  │ Bước 4: Trả lời rõ ràng, dẫn chứng [1], [2]...       │   │
│  │                                                          │   │
│  │ Information:                                              │   │
│  │ [1] {doc1}                                               │   │
│  │ [2] {doc2}                                               │   │
│  │                                                          │   │
│  │ Question: {query}                                        │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ TEMPLATE 3: SELF-CONSTRAINED RAG                        │   │
│  │                                                          │   │
│  │ System: Bạn là trợ lý BHYT.                             │   │
│  │                                                          │   │
│  │ NGUYÊN TẮC:                                              │   │
│  │ 1. CHỈ dùng thông tin từ context bên dưới              │   │
│  │ 2. KHÔNG bịa đặt thông tin                              │   │
│  │ 3. Nếu không có thông tin → "Theo tài liệu được cung   │   │
│  │    cấp, tôi chưa tìm thấy thông tin về..."             │   │
│  │ 4. Luôn trích dẫn nguồn [1], [2]...                     │   │
│  │                                                          │   │
│  │ CONTEXT:                                                  │   │
│  │ {context}                                                │   │
│  │                                                          │   │
│  │ CÂU HỎI: {query}                                        │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ TEMPLATE 4: MULTI-SOURCE RAG                            │   │
│  │                                                          │   │
│  │ System: Bạn là trợ lý phân tích đa nguồn.             │   │
│  │                                                          │   │
│  │ NGUỒN 1 — Tài liệu chính thức (ưu tiên cao):          │   │
│  │ {official_docs}                                          │   │
│  │                                                          │   │
│  │ NGUỒN 2 — Phân tích chuyên gia:                         │   │
│  │ {expert_analysis}                                        │   │
│  │                                                          │   │
│  │ NGUỒN 3 — Dữ liệu thực tế:                              │   │
│  │ {real_data}                                              │   │
│  │                                                          │   │
│  │ HƯỚNG DẪN: Ưu tiên nguồn 1 > nguồn 2 > nguồn 3.      │   │
│  │ Nếu nguồn khác nhau, nêu rõ quan điểm từng nguồn.     │   │
│  │                                                          │   │
│  │ CÂU HỎI: {query}                                        │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ TEMPLATE 5: CITATION-HEAVY RAG                          │   │
│  │                                                          │   │
│  │ System: Bạn là trợ lý pháp lý.                         │   │
│  │                                                          │   │
│  │ QUY TẮC TRÍCH DẪN:                                      │   │
│  │ - Mọi thông tin PHẢI có nguồn [1], [2]...             │   │
│  │ - Nếu không có nguồn → "Chưa có thông tin từ nguồn     │   │
│  │   chính thức"                                            │   │
│  │ - KHÔNG suy luận thêm từ context                       │   │
│  │                                                          │   │
│  │ TÀI LIỆU:                                                │   │
│  │ [1] {doc1_source}: {doc1_content}                       │   │
│  │ [2] {doc2_source}: {doc2_content}                       │   │
│  │                                                          │   │
│  │ CÂU HỎI: {query}                                        │   │
│  │                                                          │   │
│  │ Trả lời (luôn trích dẫn nguồn):                        │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 4.2 Advanced Prompt Techniques

```python
class PromptBuilder:
    """Advanced prompt building techniques for RAG"""
    
    def __init__(self, system_prompt=None):
        self.system_prompt = system_prompt or "Bạn là trợ lý AI thông minh."
    
    def build_with_instructions(self, query, context_docs, 
                                  custom_instructions=None):
        """
        Prompt với instructions tùy chỉnh
        
        Techniques:
        - Explicit constraints
        - Step-by-step instructions
        - Output format specification
        """
        instructions = custom_instructions or """
Quy tắc:
1. Chỉ dùng thông tin từ context được cung cấp
2. Luôn trích dẫn nguồn [1], [2]...
3. Nếu không đủ thông tin, nói rõ
4. Trả lời ngắn gọn, rõ ràng
"""
        
        context_parts = []
        for i, doc in enumerate(context_docs, 1):
            score = doc.get("score", 0)
            text = doc["content"] if isinstance(doc, dict) else doc
            context_parts.append(f"[{i}] {text}")
        
        context = "\n".join(context_parts)
        
        return f"""{self.system_prompt}

{instructions}

CONTEXT:
{context}

CÂU HỎI: {query}

TRẢ LỜI:"""
    
    def build_with_reasoning(self, query, context_docs):
        """
        Prompt yêu cầu model "suy nghĩ" trước khi trả lời
        
        Giúp giảm hallucination và tăng accuracy
        """
        context_parts = []
        for i, doc in enumerate(context_docs, 1):
            text = doc["content"] if isinstance(doc, dict) else doc
            context_parts.append(f"[{i}] {text}")
        
        context = "\n".join(context_parts)
        
        return f"""Bạn là trợ lý phân tích.

THÔNG TIN:
{context}

CÂU HỎI: {query}

HÃY LÀM THEO CÁC BƯỚC SAU:
1. [PHÂN TÍCH] Xác định thông tin nào trong context liên quan
2. [ĐÁNH GIÁ] Đánh giá độ đầy đủ của thông tin
3. [TỔNG HỢP] Tổng hợp thông tin liên quan
4. [TRẢ LỜI] Trả lời câu hỏi một cách rõ ràng

BẮT ĐẦU:"""
    
    def build_with_format(self, query, context_docs, 
                           output_format="markdown"):
        """
        Prompt với output format cụ thể
        
        output_format: "markdown", "json", "table", "list"
        """
        context_parts = []
        for i, doc in enumerate(context_docs, 1):
            text = doc["content"] if isinstance(doc, dict) else doc
            context_parts.append(f"[{i}] {text}")
        
        context = "\n".join(context_parts)
        
        format_instructions = {
            "markdown": "Trả lời bằng Markdown format với headers và bullet points.",
            "json": 'Trả lời bằng JSON format: {"answer": "...", "sources": [...], "confidence": 0.0-1.0}',
            "table": "Trả lời dưới dạng bảng Markdown.",
            "list": "Trả lời dưới dạng danh sách có đánh số.",
        }
        
        return f"""{self.system_prompt}

CONTEXT:
{context}

CÂU HỎI: {query}

OUTPUT FORMAT: {format_instructions.get(output_format, "Text tự nhiên.")}

TRẢ LỜI:"""
    
    def build_with_guardrails(self, query, context_docs):
        """
        Prompt với guardrails để prevent hallucination
        
        Techniques:
        - Explicit "don't make up" instruction
        - Confidence scoring
        - Source verification
        """
        context_parts = []
        for i, doc in enumerate(context_docs, 1):
            text = doc["content"] if isinstance(doc, dict) else doc
            context_parts.append(f"[{i}] {text}")
        
        context = "\n".join(context_parts)
        
        return f"""Bạn là trợ lý chính xác. TUÂN THỦ NGHIÊM NGẶT:

⚠️ QUY TẮC AN TOÀN:
- KHÔNG BAO GIỜ bịa đặt thông tin
- KHÔNG suy luận thêm từ context
- Nếu context không đủ → Nói "Không có đủ thông tin"
- Mỗi luận điểm PHẢI trích dẫn được nguồn [n]

CONTEXT:
{context}

CÂU HỎI: {query}

ĐÁNH GIÁ TRƯỚC KHI TRẢ LỜI:
1. Context có chứa thông tin trả lời câu hỏi không?
2. Nếu CÓ → Trả lời + trích dẫn nguồn
3. Nếu KHÔNG → Nói rõ "Chưa có thông tin từ nguồn tham khảo"

TRẢ LỜI:"""


# Usage
builder = PromptBuilder()

prompt = builder.build_with_guardrails(
    query="BHYT đóng bao nhiêu?",
    context_docs=[
        {"content": "Mức đóng BHYT là 4.5% lương cơ sở", "score": 0.95},
        {"content": "Thẻ BHYT有效期 5 năm", "score": 0.7},
    ]
)
```

---

## 5. Hierarchical Context

### 5.1 Cấu Trúc Phân Cấp

```
┌──────────────────────────────────────────────────────────────────┐
│                HIERARCHICAL CONTEXT STRUCTURE                     │
│                                                                  │
│  LEVEL 0: GLOBAL CONTEXT                                        │
│  ├── Luôn có trong mọi request                                 │
│  ├── System instructions, personality                           │
│  ├── User profile (nếu có)                                     │
│  └── Size: ~500 tokens                                         │
│  │                                                              │
│  ▼                                                              │
│  LEVEL 1: SESSION CONTEXT                                       │
│  ├── Thông tin về session hiện tại                             │
│  ├── Conversation summary (từ các session trước)              │
│  ├── Active tasks/projects                                     │
│  └── Size: ~1000 tokens                                        │
│  │                                                              │
│  ▼                                                              │
│  LEVEL 2: RECENT CONTEXT                                        │
│  ├── Messages gần nhất (sliding window)                       │
│  ├── Tool outputs gần đây                                     │
│  └── Size: ~2000-4000 tokens                                  │
│  │                                                              │
│  ▼                                                              │
│  LEVEL 3: RETRIEVED CONTEXT                                     │
│  ├── Documents từ RAG                                         │
│  ├── Knowledge graph results                                  │
│  ├── Code snippets                                             │
│  └── Size: ~4000-8000 tokens                                  │
│  │                                                              │
│  ▼                                                              │
│  LEVEL 4: FOCUSED CONTEXT                                       │
│  ├── Specific section/detail được chỉ ra                      │
│  ├── Current file being discussed                              │
│  └── Size: ~500-1000 tokens                                   │
│                                                                  │
│  RULE: Khi context đầy, xóa từ dưới lên trên                  │
│  (Level 4 → 3 → 2 → 1 → 0 never)                              │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 5.2 Implementation

```python
class HierarchicalContext:
    """
    Manage multi-level context with priority-based eviction
    """
    
    def __init__(self, total_budget=128000):
        self.total_budget = total_budget
        self.levels = {
            "global":    {"content": [], "priority": 0, "budget": 500},
            "session":   {"content": [], "priority": 1, "budget": 1000},
            "recent":    {"content": [], "priority": 2, "budget": 4000},
            "retrieved": {"content": [], "priority": 3, "budget": 8000},
            "focused":   {"content": [], "priority": 4, "budget": 1000},
        }
    
    def set_global(self, content):
        """Set global context (always present)"""
        self.levels["global"]["content"] = [content]
    
    def set_session(self, content):
        """Set session context"""
        self.levels["session"]["content"].append(content)
    
    def add_recent(self, message):
        """Add to recent messages"""
        self.levels["recent"]["content"].append(message)
        # Keep only last 20 messages
        if len(self.levels["recent"]["content"]) > 20:
            self.levels["recent"]["content"] = \
                self.levels["recent"]["content"][-20:]
    
    def set_retrieved(self, documents):
        """Set retrieved context from RAG"""
        self.levels["retrieved"]["content"] = documents
    
    def set_focused(self, detail):
        """Set focused context (specific detail)"""
        self.levels["focused"]["content"] = [detail]
    
    def build_prompt(self, query, max_tokens=None):
        """
        Build prompt from all levels
        
        Priority: global > session > recent > retrieved > focused
        If exceeds budget, remove from lowest priority first
        """
        parts = []
        used_tokens = 0
        
        # Sort levels by priority (0 = highest)
        sorted_levels = sorted(
            self.levels.items(),
            key=lambda x: x[1]["priority"]
        )
        
        for level_name, level_data in sorted_levels:
            if not level_data["content"]:
                continue
            
            content_text = "\n".join(
                str(c) for c in level_data["content"]
            )
            
            # Estimate tokens (~4 chars per token)
            estimated_tokens = len(content_text) // 4
            
            # Check budget
            if max_tokens and used_tokens + estimated_tokens > max_tokens:
                # Try to fit partial content
                remaining_tokens = max_tokens - used_tokens
                if remaining_tokens > 200:
                    content_text = content_text[:remaining_tokens * 4]
                    parts.append(f"[{level_name.upper()}]\n{content_text}")
                continue
            
            parts.append(f"[{level_name.upper()}]\n{content_text}")
            used_tokens += estimated_tokens
        
        # Add query at the end
        parts.append(f"[QUERY]\n{query}")
        
        return "\n\n".join(parts)
    
    def report(self):
        """Print context usage report"""
        print(f"\n{'='*50}")
        print(f"{'HIERARCHICAL CONTEXT REPORT':^50}")
        print(f"{'='*50}")
        
        total_used = 0
        for level_name, level_data in sorted(
            self.levels.items(), 
            key=lambda x: x[1]["priority"]
        ):
            content = "\n".join(str(c) for c in level_data["content"])
            tokens = len(content) // 4
            total_used += tokens
            
            status = "✅" if tokens <= level_data["budget"] else "⚠️"
            bar_len = min(int(tokens / level_data["budget"] * 20), 20)
            bar = '█' * bar_len + '░' * (20 - bar_len)
            
            print(f"  {level_name:<12} {tokens:>6} tokens "
                  f"[{bar}] {status}")
        
        print(f"{'-'*50}")
        print(f"  {'TOTAL':<12} {total_used:>6} tokens")
        print(f"  {'BUDGET':<12} {self.total_budget:>6} tokens")
        print(f"  {'REMAINING':<12} {self.total_budget - total_used:>6} tokens")
        print(f"{'='*50}")


# Usage
ctx = HierarchicalContext(total_budget=128000)

ctx.set_global("Bạn là trợ lý BHYT. Trả lời bằng tiếng Việt.")
ctx.set_session("User đang tìm hiểu về BHYT cho gia đình")
ctx.add_recent({"role": "user", "content": "BHYT đóng bao nhiêu?"})
ctx.add_recent({"role": "assistant", "content": "Mức đóng BHYT là 4.5%..."})
ctx.set_retrieved([
    {"content": "Điều 12 Luật BHYT: Quyền lợi tham gia"},
    {"content": "QĐ 105: Mức đóng chi tiết"},
])
ctx.set_focused("Điều 12: Người tham gia BHYT được享受 quyền lợi...")

prompt = ctx.build_prompt("Thẻ BHYT có hạn không?", max_tokens=5000)
ctx.report()
```

---

## 6. Streaming Context

### 6.1 Khái Niệm

```
┌──────────────────────────────────────────────────────────────────┐
│                 STREAMING CONTEXT PIPELINE                        │
│                                                                  │
│  Traditional (Batch):                                           │
│  User sends full query → Wait → Full response                   │
│                                                                  │
│  Streaming:                                                      │
│  User types → Partial query → Incremental context → Stream resp │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │                                                            │  │
│  │  User Types ──► Debounce ──► Update Context ──► Stream    │  │
│  │                                                            │  │
│  │  Turn 1: "BHYT"                                           │  │
│  │  → Retrieve BHYT docs                                    │  │
│  │  → Start streaming: "BHYT là..."                         │  │
│  │                                                            │  │
│  │  Turn 2: "BHYT đóng bao nhiêu?" (append)                 │  │
│  │  → Add financial docs to context                         │  │
│  │  → Continue: "...mức đóng là 4.5%..."                    │  │
│  │                                                            │  │
│  │  Turn 3: "và thời hạn?" (append)                          │  │
│  │  → Add validity docs to context                          │  │
│  │  → Continue: "...thời hạn 5 năm..."                      │  │
│  │                                                            │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 6.2 Implementation

```python
import asyncio
from collections import deque
from datetime import datetime, timedelta

class StreamingContextManager:
    """
    Manage context for streaming/incremental conversations
    
    Handles:
    - Debouncing (wait for user to stop typing)
    - Incremental context updates
    - Token budget management during streaming
    """
    
    def __init__(self, debounce_ms=500, max_context_tokens=4000):
        self.debounce_ms = debounce_ms
        self.max_context_tokens = max_context_tokens
        self.partial_query = ""
        self.context_buffer = deque(maxlen=50)
        self.last_update = datetime.now()
        self.is_streaming = False
    
    def on_user_input(self, text_chunk, is_final=False):
        """
        Handle streaming user input
        
        text_chunk: partial text from user
        is_final: True when user finished typing
        """
        self.partial_query += text_chunk
        self.last_update = datetime.now()
        
        if is_final:
            return self._process_final_query()
        
        return None
    
    def _process_final_query(self):
        """Process complete query with updated context"""
        # Get relevant context based on partial query
        context = self._retrieve_context(self.partial_query)
        
        # Build streaming-ready prompt
        prompt = self._build_streaming_prompt(
            self.partial_query, context
        )
        
        # Reset partial query
        self.partial_query = ""
        
        return prompt
    
    def _retrieve_context(self, query):
        """Retrieve context for streaming query"""
        # This would call your vector store
        # Simplified: return from buffer
        return list(self.context_buffer)
    
    def _build_streaming_prompt(self, query, context):
        """Build prompt optimized for streaming response"""
        context_text = "\n".join(
            f"[{i+1}] {c}" for i, c in enumerate(context)
        )
        
        return f"""Context:
{context_text}

Câu hỏi: {query}

Trả lời ngắn gọn (phù hợp cho streaming):"""
    
    def add_to_context(self, information, source="conversation"):
        """Add information to streaming context buffer"""
        self.context_buffer.append({
            "content": information,
            "source": source,
            "timestamp": datetime.now().isoformat(),
        })
    
    def get_context_size(self):
        """Estimate current context size in tokens"""
        total_chars = sum(
            len(str(item["content"])) 
            for item in self.context_buffer
        )
        return total_chars // 4  # rough token estimate


# Usage with Ollama streaming
class StreamingRAG:
    """RAG with streaming response"""
    
    def __init__(self, model="gemma3:12b"):
        self.model = model
        self.ollama_url = "http://localhost:11434"
        self.context_manager = StreamingContextManager()
    
    def query_streaming(self, question):
        """Stream response token by token"""
        import requests
        
        # Build context
        context = self.context_manager._retrieve_context(question)
        prompt = self.context_manager._build_streaming_prompt(
            question, context
        )
        
        # Stream from Ollama
        response = requests.post(f"{self.ollama_url}/api/generate", json={
            "model": self.model,
            "prompt": prompt,
            "stream": True  # Enable streaming
        }, stream=True)
        
        for line in response.iter_lines():
            if line:
                import json
                chunk = json.loads(line)
                if "response" in chunk:
                    yield chunk["response"]  # Yield each token
```

---

## 7. Labs Thực Hành

### Lab 1: Context Budget Demo

```python
# python 02-build-context/lab_budget.py
from README import ContextBudget

# Different scenarios
scenarios = [
    ("simple_qa", 128000),
    ("complex_rag", 32000),
    ("long_conversation", 8000),
]

for name, total in scenarios:
    print(f"\n{'='*50}")
    print(f"Scenario: {name} ({total:,} tokens)")
    budget = ContextBudget(total_tokens=total)
    budget.adjust_for_query_type("complex" if "rag" in name else "conversational")
    budget.report()
```

### Lab 2: Compression Demo

```python
# python 02-build-context/lab_compression.py

long_text = """
Bảo hiểm y tế (BHYT) là hình thức bảo hiểm bắt buộc được thực hiện 
theo Luật BHYT. Theo đó, mọi công dân Việt Nam đều phải tham gia BHYT.

Mức đóng BHYT được quy định cụ thể:
- Người lao động: 4.5% mức lương cơ sở
- Người sử dụng lao động: 3%
- Ngân sách nhà nước: 1.5%

Quyền lợi khi tham gia BHYT:
- Được khám chữa bệnh tại các cơ sở y tế tuyến huyện trở lên
- Chi trả từ 80% đến 100% chi phí tùy tuyến
- Được cấp thuốc theo danh mục

Thẻ BHYT có hiệu lực trong 5 năm kể từ ngày cấp.
Người tham gia cần đóng đúng hạn để được hưởng quyền lợi liên tục.
""" * 5  # Simulate long document

from README import ContextCompressor

compressor = ContextCompressor()

# Test different compression techniques
print("=== Original ===")
print(f"Length: {len(long_text)} chars")

print("\n=== Extractive Compression ===")
extracted = compressor.extractive_compress(long_text, num_sentences=5)
print(f"Length: {len(extracted)} chars")
print(extracted[:200])

print("\n=== Selective Compression ===")
selective = compressor.selective_compress(long_text)
print(f"Length: {len(selective)} chars")
print(selective[:200])
```

---

*Tài liệu: II. Build Context*
*Ngày tạo: 2026-07-11*
*Môi trường: Ollama (gemma3:12b, nomic-embed-text)*