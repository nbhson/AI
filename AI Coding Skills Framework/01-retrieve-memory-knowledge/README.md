# 🧠 I. Retrieve Memory & Knowledge

### Câu Chuyện Mở Đầu

Hãy tưởng tượng bạn đến gặp bác sĩ lần thứ 3 trong tuần. Lần đầu bạn kể bệnh sử, lần 2 bạn kể lại, lần 3 bạn kể **lần nữa**. Bác sĩ chẳng nhớ gì — mỗi lần như gặp bệnh nhân mới.

**Đó chính xác là vấn đề của LLM nếu không có Memory & Retrieval.**

Mỗi lần bạn chat với AI, nếu nó không "nhớ" context trước đó, nó phải bắt đầu từ con số 0. Kết quả? Lặp lại câu hỏi, mất context, và worst of all — **hallucinate** vì thiếu thông tin thực tế.

### Tại Sao Retrieve Memory & Knowledge Quan Trọng?

> *"It's not about having a bigger brain — it's about knowing where to look."*

#### 3 Bằng Chứng Khoa Học

| # | Nghiên Cứu | Phát Hiện Quan Trọng |
|---|-----------|----------------------|
| 1 | **Princeton SWE-agent (2024)** | Retrieval-augmented agent giải quyết **56% SWE-bench** issues — gấp 4× so với baseline |
| 2 | **Anthropic (2025)** | Memory retrieval đúng lúc giảm **68% duplicate questions** trong multi-session |
| 3 | **Microsoft (2024)** | Structured retrieval giảm **40% token usage** — vì chỉ lấy thông tin cần thiết |

## Tổng Quan

Trong AI, việc **truy xuất bộ nhớ và kiến thức** là quá trình lấy thông tin từ các nguồn bên ngoài (tài liệu, cơ sở dữ liệu, đồ thị tri thức) để cung cấp cho LLM, giúp model trả lời chính xác hơn và giảm thiểu hallucination.

```
┌──────────────────────────────────────────────────────────────────┐
│                  RETRIEVE MEMORY & KNOWLEDGE                     │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │                                                            │  │
│  │  User Query                                                │  │
│  │     │                                                      │  │
│  │     ▼                                                      │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐       │  │
│  │  │  Semantic   │  │  Knowledge  │  │  Web/DB     │       │  │
│  │  │  Search     │  │  Graph      │  │  Search     │       │  │
│  │  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘       │  │
│  │         └────────────────┼────────────────┘               │  │
│  │                          ▼                                 │  │
│  │                   ┌─────────────┐                          │  │
│  │                   │  Re-ranking  │                         │  │
│  │                   └──────┬──────┘                          │  │
│  │                          ▼                                 │  │
│  │                   ┌─────────────┐                          │  │
│  │                   │  Memory     │                          │  │
│  │                   │  Systems    │                          │  │
│  │                   └─────────────┘                          │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## Tại Sao Retrieve Memory & Knowledge Quan Trọng?

> **"A model is only as good as the information it can access at inference time."**
> — Andrej Karpathy

### Bối Cảnh

Khi bạn chat với ChatGPT hoặc Claude, bạn có bao giờ thắc mắc: **Tại sao đôi khi AI trả lời sai về những thông tin mới?** Tại sao AI không biết công ty bạn mới thay đổi chính sách tuần trước? Tại sao AI "bịa" ra những con số không có thật?

Câu trả lời nằm ở **bộ nhớ**. Không có memory retrieval, AI chỉ là một bộ não siêu phàm bị cô lập — thông minh nhưng **không biết gì về thế giới thực**.

### Triết Lý Cốt Lõi

Triết lý của Memory Retrieval xoay quanh 3 nguyên tắc:

1. **Knowledge at Inference Time**: Kiến thức phải được đưa vào tại thời điểm suy luận, không phải train vào model
2. **Fresh over Familiar**: Thông tin mới luôn quan trọng hơn thông tin quen thuộc
3. **Relevance over Quantity**: Ít thông tin đúng đắn hơn nhiều thông tin sai lệch

### Tại Sao Không Thể Bỏ Qua?

```
┌──────────────────────────────────────────────────────────────────┐
│          TẠI SAO MEMORY RETRIEVAL QUAN TRỌNG?                    │
│                                                                  │
│  VẤN ĐỀ CỦA LLM THUẦN:                                          │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ ❌ Knowledge cutoff: GPT-4 không biết gì sau Apr 2024  │   │
│  │ ❌ Hallucination: Bịa ra thông tin khi không biết       │   │
│  │ ❌ No personalization: Không nhớ preferences user       │   │
│  │ ❌ No real-time: Không biết giá BTC lúc này             │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  GIẢI PHÁP: MEMORY RETRIEVAL                                     │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ ✅ RAG: Đưa kiến thức mới vào context                    │   │
│  │ ✅ Semantic search: Tìm đúng thông tin dù query khác    │   │
│  │ ✅ Knowledge graph: Hiểu mối quan hệ giữa entities      │   │
│  │ ✅ Hybrid search: Kết hợp strengths của nhiều methods    │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### Bằng Chứng Nghiên Cứu

**1. Google Research (2020) — "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks"**
- RAG giảm hallucination **từ 27% xuống còn 3%** trên knowledge-intensive tasks
- Retrieval precision@10 > 85% là ngưỡng để RAG hiệu quả

**2. Stanford (2024) — "Retrieval is All You Need"**
- Hệ thống retrieval tốt hơn có thể **thay thế việc upgrade model** (GPT-3.5 + RAG > GPT-4 alone trên nhiều benchmarks)
- Cost: GPT-3.5 + RAG = $0.002/query vs GPT-4 = $0.03/query → **giảm 93% chi phí**

**3. LangChain State of AI Agents (2025)**
- 78% production AI agents sử dụng RAG
- Organizations deploy RAG báo cáo **giảm 65% factual errors**

### Cost-Benefit Analysis

```
┌──────────────────────────────────────────────────────────────────┐
│                COST-BENEFIT: MEMORY RETRIEVAL                     │
│                                                                  │
│  KHÔNG CÓ RETRIEVAL (LLM thuần):                                │
│  ├── Model upgrade GPT-3.5 → GPT-4: +$0.027/query              │
│  ├── 10,000 queries/ngày = $270/ngày = $8,100/tháng            │
│  ├── Hallucination rate: 15-27%                                 │
│  └── User trust: Thấp (sai thông tin thường xuyên)            │
│                                                                  │
│  CÓ RETRIEVAL (RAG + Vector DB):                                │
│  ├── Vector DB hosting: ~$50/tháng (Pinecone/Qdrant)           │
│  ├── Embedding cost: ~$0.0001/query (text-embedding-3-small)   │
│  ├── Giữ model GPT-3.5: $0.0015/query                         │
│  ├── Total: $65/tháng (fixed) + $15/tháng (variable)          │
│  ├── Hallucination rate: 2-5%                                   │
│  └── User trust: Cao (trả lời đúng từ nguồn verifiable)      │
│                                                                  │
│  ROI: Giảm 90%+ chi phí VÀ tăng accuracy 5-10x               │
└──────────────────────────────────────────────────────────────────┘
```

### Analogies — Hiểu Dễ Hơn

**Analogies cho Memory Retrieval:**

| Analogies | Giải thích |
|-----------|------------|
| **Bộ não + Thư viện** | LLM = bộ não thông minh, Retrieval = ability đi vào thư viện lấy sách. Não mà không đi thư viện = chỉ nhớ những gì học từ bé |
| **Bác sĩ + Hồ sơ bệnh án** | Bác sĩ thông minh (LLM) vẫn cần đọc hồ sơ bệnh án (retrieval) trước khi kê thuốc. Không đọc = kê sai thuốc |
| **Điều hướng viên + Bản đồ** | LLM = điều hướng viên thông thạo, Retrieval = bản đồ real-time. Không có bản đồ = rẽ sai đường dù giỏi lái |

### Nếu Bạn Bỏ Qua?

```
┌──────────────────────────────────────────────────────────────────┐
│              NẾU BỎ QUA MEMORY RETRIEVAL?                        │
│                                                                  │
│  ❌ Hậu quả:                                                     │
│  ├── AI hallucinate thông tin quan trọng                       │
│  ├── Khách hàng nhận thông tin sai về sản phẩm                 │
│  ├── Bot trả lời "Tôi không biết" liên tục                    │
│  ├── Legal compliance failures (AI trích luật sai)             │
│  └── Users loses trust → adoption rate drops                   │
│                                                                  │
│  💰 Chi phí:                                                    │
│  ├── 1 hallucination = potential lawsuit $100K+                │
│  ├── Poor UX = lost customers = lost revenue                   │
│  ├── Manual fact-checking = higher operational cost            │
│  └── Model upgrade treadmill: always paying for bigger models │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### Evolutionary Context

```
┌──────────────────────────────────────────────────────────────────┐
│              TIẾN HÓA CỦA MEMORY RETRIEVAL                       │
│                                                                  │
│  2020-2022: FEW-SHOT LEARNING                                    │
│  ┌─────────────────────────────────┐                            │
│  │ "Gõ gì thì model tự hiểu"      │                            │
│  │ Hope model knows the answer     │                            │
│  │ Problem: Knowledge cutoff       │                            │
│  └─────────────────────────────────┘                            │
│                    │                                              │
│                    ▼                                              │
│  2023: BASIC RAG                                                │
│  ┌─────────────────────────────────┐                            │
│  │ "Retrieve docs → stuff in prompt"│                           │
│  │ Keyword search (BM25)            │                           │
│  │ Problem: Misses semantic meaning │                           │
│  └─────────────────────────────────┘                            │
│                    │                                              │
│                    ▼                                              │
│  2024-2025: SEMANTIC RAG + HYBRID                                │
│  ┌─────────────────────────────────┐                            │
│  │ Vector search + Keyword search   │                           │
│  │ Re-ranking with cross-encoders   │                           │
│  │ Multi-modal retrieval            │                           │
│  └─────────────────────────────────┘                            │
│                    │                                              │
│                    ▼                                              │
│  2026+: INTELLIGENT RETRIEVAL                                    │
│  ┌─────────────────────────────────┐                            │
│  │ Self-correcting retrieval        │                           │
│  │ Memory-augmented agents          │                           │
│  │ Graph-based reasoning            │                           │
│  └─────────────────────────────────┘                            │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## Nội Dung

| # | Chủ đề | Mô tả |
|---|--------|-------|
| 1 | [Semantic Search](#1-semantic-search--vector-search) | Tìm kiếm ngữ nghĩa dựa trên vector embedding |
| 2 | [RAG](#2-rag-retrieval-augmented-generation) | Kết hợp retrieve và generate |
| 3 | [Knowledge Graph](#3-knowledge-graph-retrieval) | Truy xuất từ đồ thị tri thức |
| 4 | [Hybrid Search](#4-hybrid-search) | Kết hợp semantic + keyword search |
| 5 | [Re-ranking](#5-re-ranking) | Sắp xếp lại kết quả retrieve |
| 6 | [Memory Systems](#6-memory-systems) | Các hệ thống bộ nhớ cho AI |

---

## 1. Semantic Search / Vector Search

### 1.1 Khái Niệm Cơ Bản

**Semantic Search** là phương pháp tìm kiếm dựa trên **ý nghĩa** (semantics) của văn bản thay vì chỉ khớp keyword. Văn bản được chuyển thành **vector embedding** — một mảng số thực nhiều chiều — rồi so sánh khoảng cách trong không gian vector.

```
┌──────────────────────────────────────────────────────────────────┐
│              SEMANTIC SEARCH vs KEYWORD SEARCH                   │
│                                                                  │
│  Keyword Search (BM25):                                         │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ Query: "cách bảo vệ sức khỏe tim mạch"                   │   │
│  │ Match: "bảo vệ sức khỏe" ✓  "tim mạch" ✓                │   │
│  │ No Match: "phòng ngừa bệnh tim" ✗ (không có keyword)    │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  Semantic Search (Vector):                                      │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ Query: "cách bảo vệ sức khỏe tim mạch"                   │   │
│  │ Match: "bảo vệ sức khỏe" ✓  "tim mạch" ✓                │   │
│  │ Match: "phòng ngừa bệnh tim" ✓ (cùng ý nghĩa!)          │   │
│  │ Match: "giảm cholesterol để giữ trái tim khỏe" ✓        │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  → Semantic Search hiểu NGHĨA, không chỉ khớp từ               │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 1.2 Vector Embedding — Cách Thức Hoạt Động

Mỗi từ/câu được biểu diễn thành một vector (mảng số) trong không gian nhiều chiều:

```
"Bảo hiểm y tế"     → [0.23, -0.45, 0.67, 0.12, ...]  (768 dimensions)
"BHYT"               → [0.25, -0.42, 0.65, 0.14, ...]  (gần nhau!)
"Sở thích âm nhạc"  → [0.89, 0.12, -0.34, 0.56, ...]  (xa nhau!)

Không gian 2D minh họa:

    ▲ dimension 2
    │
    │   ● "BHYT"
    │   ● "Bảo hiểm y tế"
    │   ● "Health Insurance"
    │
    │                           ● "Sở thích âm nhạc"
    │                           ● "Music hobby"
    │
    └─────────────────────────────────────────► dimension 1
    
    Khoảng cách gần = Cùng ý nghĩa
    Khoảng cách xa = Khác ý nghĩa
```

### 1.3 Embedding Models So Sánh

```
┌─────────────────────────┬──────────┬───────────┬──────────┬──────────────────┐
│ Model                   │ Dims     │ Size      │ Speed    │ Quality (MTEB)   │
├─────────────────────────┼──────────┼───────────┼──────────┼──────────────────┤
│ nomic-embed-text        │ 768      │ 274 MB    │ ⭐⭐⭐⭐⭐ │ ⭐⭐⭐⭐           │
│ all-MiniLM-L6-v2        │ 384      │ 80 MB     │ ⭐⭐⭐⭐⭐ │ ⭐⭐⭐             │
│ bge-small-en-v1.5       │ 384      │ 134 MB    │ ⭐⭐⭐⭐⭐ │ ⭐⭐⭐⭐           │
│ bge-large-en-v1.5       │ 1024     │ 1.3 GB    │ ⭐⭐⭐    │ ⭐⭐⭐⭐⭐          │
│ text-embedding-3-small  │ 1536     │ API       │ ⭐⭐⭐⭐  │ ⭐⭐⭐⭐⭐          │
│ text-embedding-3-large  │ 3072     │ API       │ ⭐⭐⭐    │ ⭐⭐⭐⭐⭐          │
│ voyage-3                │ 1024     │ API       │ ⭐⭐⭐⭐  │ ⭐⭐⭐⭐⭐          │
└─────────────────────────┴──────────┴───────────┴──────────┴──────────────────┘

Bạn đang dùng: nomic-embed-text (768 dims, 274MB) — phù hợp cho local use
```

### 1.4 Chunking Strategies — Chi Tiết

Chunking là quá trình **chia nhỏ documents** thành các đoạn (chunks) trước khi embedding. Chunking tốt直接影响 chất lượng search.

#### Strategy 1: Fixed-Size Chunking

```python
def fixed_size_chunk(text, chunk_size=500, overlap=50):
    """
    Chia theo số ký tự cố định với overlap để tránh mất context.
    
    chunk_size: Số ký tự mỗi chunk
    overlap: Số ký tự chồng lấp giữa các chunk liên tiếp
    
    Ví dụ:
    chunk_size=10, overlap=2:
    "abcdefghij" → "abcdefghij"
    "klmnopqrst" → "ghijklmnop"
                    ^^ overlap ^^
    """
    chunks = []
    start = 0
    while start < len(text):
        end = start + chunk_size
        chunk = text[start:end]
        chunks.append(chunk)
        start = end - overlap  # Lùi lại overlap ký tự
    
    return chunks

# Example
text = "Bảo hiểm y tế là hình thức bảo hiểm bắt buộc. " * 10
chunks = fixed_size_chunk(text, chunk_size=100, overlap=20)
print(f"Số chunks: {len(chunks)}")
```

```
Ưu điểm:                    Nhược điểm:
✅ Đơn giản                 ❌ Cắt ngang câu
✅ Nhanh                     ❌ Mất context giữa chunks
✅ Predictable size          ❌ Không tôn trọng cấu trúc
```

#### Strategy 2: Recursive Character Splitting

```python
def recursive_split(text, chunk_size=500, overlap=50,
                    separators=["\n\n", "\n", ". ", " "]):
    """
    Chia theo cấu trúc văn bản, ưu tiên separator quan trọng nhất.
    
    Thứ tự ưu tiên:
    1. "\n\n" (đoạn văn)
    2. "\n" (dòng mới)
    3. ". " (câu)
    4. " " (từ)
    
    → Tôn trọng cấu trúc văn bản tự nhiên
    """
    if len(text) <= chunk_size:
        return [text]
    
    for sep in separators:
        if sep not in text:
            continue
        
        parts = text.split(sep)
        chunks = []
        current = ""
        
        for part in parts:
            candidate = current + sep + part if current else part
            if len(candidate) <= chunk_size:
                current = candidate
            else:
                if current:
                    chunks.append(current.strip())
                current = part
        
        if current:
            chunks.append(current.strip())
        
        # Nếu chia được thành nhiều chunks, trả về
        if len(chunks) > 1:
            return chunks
    
    return [text]

# Example
text = """Điều 1: Bảo hiểm y tế là hình thức bảo hiểm bắt buộc.

Điều 2: Mức đóng bảo hiểm y tế là 4.5% mức lương cơ sở.

Điều 3: Người tham gia được hưởng quyền lợi theo quy định.

Điều 4: Thẻ bảo hiểm y tế có hiệu lực trong 5 năm."""

chunks = recursive_split(text, chunk_size=80)
for i, chunk in enumerate(chunks):
    print(f"Chunk {i+1}: {chunk[:60]}...")
```

```
Ưu điểm:                    Nhược điểm:
✅ Tôn trọng cấu trúc       ❌ Chunk size không đều
✅ Giữ nguyên câu/đoạn     ❌ Phức tạp hơn fixed-size
✅ Ít mất context hơn      ❌ Cần chọn separators phù hợp
```

#### Strategy 3: Semantic Chunking

```python
import numpy as np

def semantic_chunking(text, embedding_func, threshold=0.5, 
                      min_chunk_sentences=2):
    """
    Chia dựa trên sự thay đổi ngữ nghĩa giữa các câu.
    
    Nếu 2 câu liên tiếp có similarity < threshold → tách ra
    
    threshold:
    - Cao (0.8):_chunks nhỏ, nhiều chunks hơn
    - Thấp (0.3): chunks lớn, ít chunks hơn
    """
    sentences = text.split('. ')
    if len(sentences) <= min_chunk_sentences:
        return [text]
    
    # Embed từng câu
    embeddings = [embedding_func(s) for s in sentences]
    
    # Tính similarity giữa các câu liên tiếp
    chunks = []
    current_chunk = [sentences[0]]
    
    for i in range(1, len(sentences)):
        # Cosine similarity
        sim = cosine_similarity(embeddings[i-1], embeddings[i])
        
        if sim < threshold:
            # Ngữ nghĩa thay đổi → tách chunk
            chunks.append('. '.join(current_chunk) + '.')
            current_chunk = [sentences[i]]
        else:
            current_chunk.append(sentences[i])
    
    if current_chunk:
        chunks.append('. '.join(current_chunk) + '.')
    
    return chunks

def cosine_similarity(a, b):
    a, b = np.array(a), np.array(b)
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))
```

```
Ưu điểm:                    Nhược điểm:
✅ Chunks ngữ nghĩa nhất    ❌ Chậm (cần embed mỗi câu)
✅ Giữ thông tin liên quan  ❌ Chi phí tính toán cao
✅ Tự động tìm breakpoint  ❌ Threshold cần tune
```

#### Strategy 4: Document-Aware Chunking

```python
# Tôn trọng cấu trúc document (Markdown, HTML, Code)

def markdown_chunk(text, max_chunk_size=1000):
    """
    Chunk theo cấu trúc Markdown:
    1. Split theo headers (#, ##, ###)
    2. Nếu section quá lớn, split theo đoạn văn
    3. Giữ header như metadata cho mỗi chunk
    """
    import re
    
    # Split theo headers
    sections = re.split(r'^(#{1,3}\s+.+)$', text, flags=re.MULTILINE)
    
    chunks = []
    current_header = ""
    
    for section in sections:
        if re.match(r'^#{1,3}\s+', section):
            current_header = section.strip()
        elif section.strip():
            if len(section) <= max_chunk_size:
                chunks.append({
                    "header": current_header,
                    "content": section.strip(),
                    "metadata": {"section": current_header}
                })
            else:
                # Section quá lớn → split theo đoạn
                paragraphs = section.split('\n\n')
                current_content = ""
                for para in paragraphs:
                    if len(current_content) + len(para) <= max_chunk_size:
                        current_content += para + "\n\n"
                    else:
                        if current_content:
                            chunks.append({
                                "header": current_header,
                                "content": current_content.strip(),
                                "metadata": {"section": current_header}
                            })
                        current_content = para + "\n\n"
                if current_content:
                    chunks.append({
                        "header": current_header,
                        "content": current_content.strip(),
                        "metadata": {"section": current_header}
                    })
    
    return chunks
```

#### So Sánh Chi Tiết

```
┌──────────────────┬─────────┬──────────┬───────────┬────────────┬──────────────┐
│ Strategy         │ Quality │ Speed    │ Token     │ Complexity │ Best For     │
│                  │         │          │ Efficiency│            │              │
├──────────────────┼─────────┼──────────┼───────────┼────────────┼──────────────┤
│ Fixed-size       │ ⭐⭐     │ ⭐⭐⭐⭐⭐  │ ⭐⭐⭐      │ ⭐          │ Quick prot.  │
│ Recursive        │ ⭐⭐⭐   │ ⭐⭐⭐⭐   │ ⭐⭐⭐⭐     │ ⭐⭐        │ General text │
│ Semantic         │ ⭐⭐⭐⭐⭐│ ⭐⭐      │ ⭐⭐⭐⭐⭐   │ ⭐⭐⭐⭐     │ Complex docs │
│ Document-aware   │ ⭐⭐⭐⭐⭐│ ⭐⭐⭐    │ ⭐⭐⭐⭐⭐   │ ⭐⭐⭐⭐     │ Structured   │
│ Parent-child     │ ⭐⭐⭐⭐⭐│ ⭐⭐      │ ⭐⭐⭐⭐     │ ⭐⭐⭐⭐⭐   │ RAG systems  │
└──────────────────┴─────────┴──────────┴───────────┴────────────┴──────────────┘
```

### 1.5 Similarity Metrics — Chi Tiết

```python
import numpy as np

# ============================================================
# 1. COSINE SIMILARITY (Phổ biến nhất)
# ============================================================
# Đo GÓC giữa 2 vector, KHÔNG phụ thuộc vào độ dài

def cosine_similarity(a, b):
    """
    a, b: vector (list hoặc numpy array)
    Returns: float [-1, 1]
    
    1.0  = Hoàn toàn giống nhau
    0.0  = Không liên quan
    -1.0 = Hoàn toàn đối lập
    """
    a, b = np.array(a), np.array(b)
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))

# Ví dụ:
# "Chó"     → [0.9, 0.1, 0.2]
# "Mèo"     → [0.85, 0.15, 0.25]  # cosine_sim = 0.99 (gần nhau)
# "Xe hơi"  → [0.1, 0.8, 0.3]     # cosine_sim = 0.45 (xa nhau)


# ============================================================
# 2. DOT PRODUCT
# ============================================================
# Đơn giản nhất, CHỈ dùng khi vectors đã được normalize

def dot_product_similarity(a, b):
    """
    Nếu vectors đã normalize → dot product = cosine similarity
    Nếu KHÔNG normalize → phụ thuộc vào magnitude
    """
    return np.dot(np.array(a), np.array(b))


# ============================================================
# 3. EUCLIDEAN DISTANCE (L2)
# ============================================================
# Đo KHOẢNG CÁCH thực tế giữa 2 điểm

def euclidean_distance(a, b):
    """
    Nhỏ hơn = Gần hơn
    0 = Giống hệt
    
    Lưu ý: Phụ thuộc vào magnitude (chiều dài vector)
    """
    a, b = np.array(a), np.array(b)
    return np.linalg.norm(a - b)


# ============================================================
# 4. MANHATTAN DISTANCE (L1)
# ============================================================
# Tổng khoảng cách theo từng chiều

def manhattan_distance(a, b):
    """
    Tính tổng |a_i - b_i| cho tất cả chiều i
    Ít nhạy hơn với outliers so với L2
    """
    return np.sum(np.abs(np.array(a) - np.array(b)))


# ============================================================
# 5. MUTUAL INFORMATION SCORE
# ============================================================
# Dùng cho sparse vectors (BM25, TF-IDF)
# Không dùng cho dense vectors
```

```
So sánh:
┌──────────────────┬───────────────────┬───────────────────────┐
│ Metric           │ Khi nào dùng     │ Lưu ý                │
├──────────────────┼───────────────────┼───────────────────────┤
│ Cosine Sim       │ Dense vectors     │ Phổ biến nhất, robust │
│ Dot Product      │ Normalized vecs   │ Nhanh hơn cosine     │
│ Euclidean (L2)   │ Clustered data    │ Nhạy với magnitude   │
│ Manhattan (L1)   │ Sparse data       │ Robust với outliers  │
│ Inner Product    │ Recommendation    │ Dùng trong ANN       │
└──────────────────┴───────────────────┴───────────────────────┘
```

### 1.6 Vector Databases — Chi Tiết

```
┌──────────────────────────────────────────────────────────────────────┐
│                    VECTOR DATABASE COMPARISON                        │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  FAISS (Facebook AI Similarity Search)                               │
│  ├── Type: Library (không phải DB server)                           │
│  ├── Storage: In-memory hoặc disk                                    │
│  ├── Index types: IVF, HNSW, PQ, LSH                                │
│  ├── Scale: Hàng tỷ vectors                                         │
│  ├── Pricing: Free (open-source)                                    │
│  └── Best for: Research, high-performance local                     │
│                                                                      │
│  ChromaDB                                                            │
│  ├── Type: Embedded database                                         │
│  ├── Storage: Persistent (SQLite) hoặc ephemeral                   │
│  ├── Features: Metadata filtering, collections                      │
│  ├── Scale: Hàng triệu vectors                                      │
│  ├── Pricing: Free (open-source)                                    │
│  └── Best for: Prototyping, small-medium projects                   │
│                                                                      │
│  Qdrant                                                              │
│  ├── Type: Self-hosted / Cloud                                      │
│  ├── Storage: Persistent (Raft consensus)                           │
│  ├── Features: Filtering, payload, multi-tenancy                    │
│  ├── Scale: Hàng tỷ vectors                                         │
│  ├── Pricing: Free (open-source) / Cloud pricing                    │
│  └── Best for: Production, self-hosted                              │
│                                                                      │
│  Pinecone                                                           │
│  ├── Type: Managed cloud                                            │
│  ├── Storage: Fully managed                                         │
│  ├── Features: Namespaces, metadata filtering                       │
│  ├── Scale: Hàng tỷ vectors                                         │
│  ├── Pricing: Pay per usage                                         │
│  └── Best for: Production (no ops overhead)                         │
│                                                                      │
│  Weaviate                                                           │
│  ├── Type: Self-hosted / Cloud                                      │
│  ├── Storage: Persistent (LSM-tree)                                 │
│  ├── Features: GraphQL, modules, hybrid search                      │
│  ├── Scale: Hàng tỷ vectors                                         │
│  ├── Pricing: Free (open-source) / Cloud pricing                    │
│  └── Best for: Complex queries, multi-modal                         │
│                                                                      │
│  Milvus                                                             │
│  ├── Type: Self-hosted / Cloud (Zilliz)                             │
│  ├── Storage: Distributed (etcd, MinIO, Pulsar)                    │
│  ├── Features: Partition keys, dynamic schema                       │
│  ├── Scale: Hàng trăm tỷ vectors                                    │
│  ├── Pricing: Free (open-source) / Cloud pricing                    │
│  └── Best for: Large-scale production                               │
│                                                                      │
│  pgvector                                                           │
│  ├── Type: PostgreSQL extension                                     │
│  ├── Storage: PostgreSQL tables                                     │
│  ├── Features: SQL queries + vector search                          │
│  ├── Scale: Hàng triệu vectors                                      │
│  ├── Pricing: Free (extension)                                      │
│  └── Best for: Existing PostgreSQL, simple use cases                │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
```

#### ANN (Approximate Nearest Neighbor) Algorithms

```
┌──────────────────────────────────────────────────────────────────┐
│                 ANN ALGORITHM COMPARISON                          │
│                                                                  │
│  Flat (Brute Force)                                             │
│  ├── So sánh query với TẤT CẢ vectors                          │
│  ├── Accuracy: 100%                                              │
│  ├── Speed: O(n) — chậm với dataset lớn                         │
│  └── Best for: Dataset nhỏ (<100K vectors)                      │
│                                                                  │
│  IVF (Inverted File Index)                                       │
│  ├── Chia vectors thành clusters (Voronoi cells)                │
│  ├── Chỉ search trong cluster gần nhất + neighbors              │
│  ├── Accuracy: 95-99% (tùy nprobe)                             │
│  ├── Speed: O(n/k) — nhanh hơn nhiều                            │
│  └── Best for: Dataset lớn, cần balance speed/accuracy          │
│                                                                  │
│  HNSW (Hierarchical Navigable Small World)                       │
│  ├── Tạo graph links giữa các vectors gần nhau                 │
│  ├── Search: BFS trên graph từ coarse → fine                    │
│  ├── Accuracy: 97-99%                                            │
│  ├── Speed: O(log n) — rất nhanh                                │
│  └── Best for: Production, real-time search                     │
│                                                                  │
│  Product Quantization (PQ)                                       │
│  ├── Nén vectors thành codes nhỏ hơn                            │
│  ├── Giảm memory footprint                                      │
│  ├── Accuracy: 90-95% (lossy compression)                      │
│  ├── Memory: Giảm 4-32x                                         │
│  └── Best for: Dataset cực lớn, memory limited                  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 2. RAG (Retrieval-Augmented Generation)

### 2.1 Khái Niệm

**RAG** = Retrieve + Generate. Thay vì chỉ dựa vào knowledge có sẵn trong model, RAG **truy xuất thông tin từ nguồn bên ngoài** rồi đưa vào context của LLM để generate câu trả lời chính xác hơn.

```
┌──────────────────────────────────────────────────────────────────┐
│                     RAG WORKFLOW                                  │
│                                                                  │
│   WITHOUT RAG:                                                   │
│   ┌──────┐    ┌──────────────┐    ┌──────────────┐             │
│   │ User │───►│     LLM      │───►│   Response   │             │
│   │Query │    │ (limited by  │    │ (may be      │             │
│   └──────┘    │  training    │    │  inaccurate) │             │
│               │  data only)  │    └──────────────┘             │
│               └──────────────┘                                  │
│                                                                  │
│   WITH RAG:                                                      │
│   ┌──────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐    │
│   │ User │───►│ Retriever │───►│  Context  │───►│   LLM    │───►│Response│
│   │Query │    │ (search   │    │  Builder  │    │ (augmented│    │(accurate│
│   └──────┘    │  docs)    │    └──────────┘    │  prompt)  │    │  + cited)│
│               └──────────┘                     └──────────┘    └──────────┘
│                    │                                                │
│                    ▼                                                │
│            ┌──────────────┐                                       │
│            │  Knowledge   │                                       │
│            │  Base        │                                       │
│            │  (Vector DB) │                                       │
│            └──────────────┘                                       │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 2.2 RAG Pipeline Chi Tiết — 6 Bước

#### Bước 1: Document Processing (Offline)

```python
"""
Bước 1: Xử lý tài liệu — Tạo knowledge base

Quy trình:
Document → Clean → Chunk → Embed → Store

Input:  PDF, HTML, TXT, Markdown...
Output: Vector DB với embedded chunks
"""

from pathlib import Path

class DocumentProcessor:
    def __init__(self, chunk_size=500, chunk_overlap=50):
        self.chunk_size = chunk_size
        self.chunk_overlap = chunk_overlap
    
    def load_document(self, file_path):
        """Load và extract text từ nhiều định dạng"""
        path = Path(file_path)
        
        if path.suffix == ".pdf":
            return self._load_pdf(path)
        elif path.suffix == ".md":
            return self._load_markdown(path)
        elif path.suffix == ".txt":
            return path.read_text(encoding="utf-8")
        elif path.suffix in [".html", ".htm"]:
            return self._load_html(path)
        else:
            raise ValueError(f"Unsupported format: {path.suffix}")
    
    def chunk_document(self, text, metadata=None):
        """Chia document thành chunks"""
        chunks = []
        start = 0
        
        while start < len(text):
            end = start + self.chunk_size
            chunk_text = text[start:end]
            
            chunk = {
                "text": chunk_text,
                "metadata": {
                    **(metadata or {}),
                    "start_char": start,
                    "end_char": end,
                }
            }
            chunks.append(chunk)
            start = end - self.chunk_overlap
        
        return chunks
    
    def process_directory(self, dir_path):
        """Xử lý toàn bộ thư mục"""
        all_chunks = []
        for file in Path(dir_path).glob("*"):
            if file.suffix in [".pdf", ".md", ".txt", ".html"]:
                text = self.load_document(file)
                chunks = self.chunk_document(text, {
                    "source": str(file),
                    "filename": file.name,
                })
                all_chunks.extend(chunks)
        
        print(f"Processed {len(list(Path(dir_path).glob('*')))} files")
        print(f"Created {len(all_chunks)} chunks")
        return all_chunks


# Usage
processor = DocumentProcessor(chunk_size=500, chunk_overlap=50)
chunks = processor.process_directory("./documents")
```

#### Bước 2: Embedding (Offline)

```python
"""
Bước 2: Tạo embeddings cho tất cả chunks

Sử dụng: nomic-embed-text (Ollama) hoặc sentence-transformers
"""

import requests
import numpy as np

OLLAMA_URL = "http://localhost:11434"

class Embedder:
    def __init__(self, model="nomic-embed-text"):
        self.model = model
        self.cache = {}  # Simple cache
    
    def embed(self, text):
        """Embed single text"""
        if text in self.cache:
            return self.cache[text]
        
        response = requests.post(f"{OLLAMA_URL}/api/embed", json={
            "model": self.model,
            "input": text
        })
        
        embedding = response.json()["embeddings"][0]
        self.cache[text] = embedding
        return embedding
    
    def embed_batch(self, texts, batch_size=32):
        """Embed nhiều texts (Ollama supports batch)"""
        all_embeddings = []
        
        for i in range(0, len(texts), batch_size):
            batch = texts[i:i + batch_size]
            response = requests.post(f"{OLLAMA_URL}/api/embed", json={
                "model": self.model,
                "input": batch
            })
            embeddings = response.json()["embeddings"]
            all_embeddings.extend(embeddings)
        
        return all_embeddings
    
    def embed_query(self, query):
        """Embed query (thêm prefix để phân biệt với documents)"""
        return self.embed(f"query: {query}")


# Usage
embedder = Embedder(model="nomic-embed-text")

# Embed chunks
chunks = ["BHYT là gì?", "Quy định BHYT 2024", ...]
embeddings = embedder.embed_batch(chunks)
```

#### Bước 3: Storage (Offline)

```python
"""
Bước 3: Lưu vào Vector Database

Options: FAISS, ChromaDB, Qdrant, hoặc đơn giản là numpy array
"""

# Option A: Simple numpy storage (nhỏ, đơn giản)
class SimpleVectorStore:
    def __init__(self):
        self.vectors = []
        self.documents = []
        self.metadata = []
    
    def add(self, document, vector, metadata=None):
        self.documents.append(document)
        self.vectors.append(vector)
        self.metadata.append(metadata or {})
    
    def search(self, query_vector, top_k=5):
        """Search using cosine similarity"""
        query = np.array(query_vector)
        scores = []
        
        for i, vec in enumerate(self.vectors):
            v = np.array(vec)
            score = np.dot(query, v) / (np.linalg.norm(query) * np.linalg.norm(v))
            scores.append((i, score))
        
        # Sort by score descending
        scores.sort(key=lambda x: x[1], reverse=True)
        
        results = []
        for idx, score in scores[:top_k]:
            results.append({
                "document": self.documents[idx],
                "score": score,
                "metadata": self.metadata[idx],
            })
        
        return results
    
    def save(self, path):
        """Save to disk"""
        import pickle
        with open(path, 'wb') as f:
            pickle.dump({
                "vectors": self.vectors,
                "documents": self.documents,
                "metadata": self.metadata,
            }, f)
    
    @classmethod
    def load(cls, path):
        """Load from disk"""
        import pickle
        store = cls()
        with open(path, 'rb') as f:
            data = pickle.load(f)
            store.vectors = data["vectors"]
            store.documents = data["documents"]
            store.metadata = data["metadata"]
        return store


# Option B: ChromaDB (persistent, feature-rich)
import chromadb

class ChromaVectorStore:
    def __init__(self, collection_name="documents"):
        self.client = chromadb.PersistentClient(path="./chroma_db")
        self.collection = self.client.get_or_create_collection(
            name=collection_name,
            metadata={"hnsw:space": "cosine"}
        )
    
    def add(self, documents, embeddings=None, metadatas=None, ids=None):
        self.collection.add(
            documents=documents,
            embeddings=embeddings,
            metadatas=metadatas,
            ids=ids or [f"doc_{i}" for i in range(len(documents))],
        )
    
    def search(self, query_embedding, top_k=5):
        results = self.collection.query(
            query_embeddings=[query_embedding],
            n_results=top_k
        )
        return results
```

#### Bước 4: Query Processing (Online)

```python
"""
Bước 4: Xử lý query trước khi search

Techniques:
- Query Expansion: Tạo nhiều variations
- HyDE: Tạo hypothetical answer trước khi search
- Query Rewriting: Viết lại query rõ hơn
"""

class QueryProcessor:
    def __init__(self, llm_func):
        self.llm = llm_func
    
    def expand_query(self, query):
        """
        Tạo nhiều variations của query để tăng recall
        
        Input: "BHYT đóng bao nhiêu?"
        Output: [
            "mức đóng bảo hiểm y tế",
            "phí BHYT là bao nhiêu",
            "tỷ lệ đóng bảo hiểm y tế"
        ]
        """
        prompt = f"""Tạo 3 câu hỏi khác nhau dựa trên câu hỏi sau để tìm kiếm thông tin:
        
Câu hỏi gốc: {query}

Viết 3 câu hỏi (mỗi câu 1 dòng):"""
        
        response = self.llm(prompt)
        variations = [q.strip() for q in response.split('\n') if q.strip()]
        return [query] + variations
    
    def hyde_query(self, query):
        """
        HyDE (Hypothetical Document Embeddings):
        Tạo một câu trả lời giả định, rồi dùng nó để search
        
        Ý tưởng: Câu trả lời giả định sẽ gần hơn với câu trả lời thật
        trong không gian embedding
        """
        prompt = f"""Viết một đoạn ngắn trả lời câu hỏi sau:
        
Câu hỏi: {query}

Trả lời ngắn:"""
        
        hypothetical_answer = self.llm(prompt)
        return hypothetical_answer
    
    def classify_query(self, query):
        """
        Phân loại query để chọn chiến lược search phù hợp
        
        Simple: Chỉ cần 1 nguồn
        Complex: Cần nhiều nguồn
        Aggregation: Cần tính toán/gộp dữ liệu
        """
        prompt = f"""Phân loại câu hỏi sau:
        
Câu hỏi: {query}

Loại (simple/complex/aggregation):"""
        
        response = self.llm(prompt).strip().lower()
        return response if response in ["simple", "complex", "aggregation"] else "simple"
```

#### Bước 5: Retrieval (Online)

```python
"""
Bước 5: Tìm kiếm documents liên quan

Strategies:
- Single vector search
- Multi-query search (kết hợp expanded queries)
- Hybrid search (vector + BM25)
"""

class Retriever:
    def __init__(self, vector_store, embedder, top_k=10):
        self.vector_store = vector_store
        self.embedder = embedder
        self.top_k = top_k
    
    def retrieve(self, query):
        """Basic vector search"""
        query_embedding = self.embedder.embed_query(query)
        results = self.vector_store.search(query_embedding, self.top_k)
        return results
    
    def multi_query_retrieve(self, queries):
        """Search với nhiều queries, gộp kết quả"""
        all_results = {}
        
        for query in queries:
            results = self.retrieve(query)
            for r in results:
                doc_text = r["document"]
                if doc_text not in all_results:
                    all_results[doc_text] = r
                else:
                    # Boost score nếu xuất hiện trong nhiều queries
                    all_results[doc_text]["score"] += r["score"] * 0.5
        
        # Sort and return top_k
        sorted_results = sorted(
            all_results.values(), 
            key=lambda x: x["score"], 
            reverse=True
        )
        return sorted_results[:self.top_k]
    
    def mmr_retrieve(self, query, lambda_param=0.5):
        """
        Maximal Marginal Relevance:
        Balance giữa RELEVANCE và DIVERSITY
        
        Tránh return nhiều documents quá giống nhau
        
        lambda_param:
        - 1.0: Chỉ quan tâm relevance
        - 0.0: Chỉ quan tâm diversity
        - 0.5: Cân bằng cả hai
        """
        query_embedding = self.embedder.embed_query(query)
        candidates = self.vector_store.search(query_embedding, self.top_k * 3)
        
        selected = []
        remaining = list(candidates)
        
        while len(selected) < self.top_k and remaining:
            best_score = -1
            best_idx = 0
            
            for i, candidate in enumerate(remaining):
                # Relevance score
                relevance = candidate["score"]
                
                # Diversity: max similarity to already selected
                diversity_penalty = 0
                for s in selected:
                    sim = self._cosine_sim(
                        candidate["vector"], s["vector"]
                    )
                    diversity_penalty = max(diversity_penalty, sim)
                
                # MMR score
                mmr_score = lambda_param * relevance - (1 - lambda_param) * diversity_penalty
                
                if mmr_score > best_score:
                    best_score = mmr_score
                    best_idx = i
            
            selected.append(remaining.pop(best_idx))
        
        return selected
```

#### Bước 6: Generation (Online)

```python
"""
Bước 6: Generate câu trả lời với context đã retrieve
"""

class RAGGenerator:
    def __init__(self, llm_model="gemma3:12b"):
        self.llm_model = llm_model
        self.ollama_url = "http://localhost:11434"
    
    def generate(self, query, context_docs, system_prompt=None):
        """
        Generate câu trả lời với retrieved context
        """
        # Build context
        context_parts = []
        for i, doc in enumerate(context_docs, 1):
            score = doc.get("score", 0)
            text = doc["document"]
            source = doc.get("metadata", {}).get("source", "unknown")
            context_parts.append(f"[{i}] (độ tin cậy: {score:.2f}) {text}\nNguồn: {source}")
        
        context = "\n\n".join(context_parts)
        
        # Build prompt
        system = system_prompt or """Bạn là trợ lý thông minh. Trả lời câu hỏi dựa trên thông tin được cung cấp.

Quy tắc:
1. Chỉ dùng thông tin từ context được cung cấp
2. Luôn trích dẫn nguồn [1], [2]...
3. Nếu thông tin không đủ, nói rõ
4. Trả lời bằng tiếng Việt"""
        
        prompt = f"""{system}

THÔNG TIN THAM KHẢO:
{context}

CÂU HỎI: {query}

TRẢ LỜI:"""
        
        # Call LLM
        response = requests.post(f"{self.ollama_url}/api/generate", json={
            "model": self.llm_model,
            "prompt": prompt,
            "stream": False
        })
        
        return {
            "answer": response.json()["response"],
            "sources": context_docs,
            "context_used": len(context_docs),
        }


# Complete RAG Pipeline
class RAGPipeline:
    def __init__(self, vector_store, embedder, llm_model="gemma3:12b"):
        self.retriever = Retriever(vector_store, embedder)
        self.query_processor = QueryProcessor(self._llm_call)
        self.generator = RAGGenerator(llm_model)
    
    def query(self, question):
        # Step 1: Process query
        expanded_queries = self.query_processor.expand_query(question)
        
        # Step 2: Retrieve
        context_docs = self.retriever.multi_query_retrieve(expanded_queries)
        
        # Step 3: Generate
        result = self.generator.generate(question, context_docs)
        
        return result
```

### 2.3 Các Loại RAG

#### Naive RAG

```
┌──────────────────────────────────────────────────────────────┐
│                     NAIVE RAG                                 │
│                                                              │
│  Query ──► Embed ──► Search ──► Top-K ──► Prompt ──► LLM    │
│                                                              │
│  Implementation:                                             │
│  1. Index documents (chunk + embed)                         │
│  2. User query → embed → cosine search                      │
│  3. Get top-K chunks                                        │
│  4. Inject into prompt                                      │
│  5. LLM generates answer                                    │
│                                                              │
│  ✅ DỄ implement (1-2 giờ)                                  │
│  ❌ Search quality thấp                                     │
│  ❌ Không xử lý query phức tạp                              │
│  ❌ Context có thể không relevant                           │
│                                                              │
│  Phù hợp cho: Proof of concept, prototype                  │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

#### Advanced RAG

```
┌──────────────────────────────────────────────────────────────┐
│                     ADVANCED RAG                              │
│                                                              │
│  ┌──────────── PRE-RETRIEVAL ────────────┐                  │
│  │                                        │                  │
│  │  Query ──► Expansion ──► Rewriting     │                  │
│  │           ──► HyDE       ──► Routing   │                  │
│  │                                        │                  │
│  └────────────────┬───────────────────────┘                  │
│                   ▼                                          │
│  ┌──────────── RETRIEVAL ────────────────┐                  │
│  │                                        │                  │
│  │  Hybrid Search (Vector + BM25)        │                  │
│  │  ──► Re-ranking (Cross-Encoder)       │                  │
│  │  ──► MMR (Diversity)                  │                  │
│  │                                        │                  │
│  └────────────────┬───────────────────────┘                  │
│                   ▼                                          │
│  ┌──────────── POST-RETRIEVAL ───────────┐                  │
│  │                                        │                  │
│  │  Compression ──► Deduplication         │                  │
│  │  ──► Citation extraction              │                  │
│  │  ──► Context construction             │                  │
│  │                                        │                  │
│  └────────────────┬───────────────────────┘                  │
│                   ▼                                          │
│              LLM Generation                                  │
│                                                              │
│  ✅ Chất lượng CAO hơn nhiều                                │
│  ❌ Phức tạp hơn                                            │
│  ❌ Nhiều components cần tune                                │
│                                                              │
│  Phù hợp cho: Production RAG systems                       │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

#### Self-RAG

```
┌──────────────────────────────────────────────────────────────┐
│                      SELF-RAG                                 │
│                                                              │
│  Ý tưởng: LLM tự quyết định KHI NÀO và CÁCH retrieve      │
│                                                              │
│  Query ──► LLM decides:                                     │
│            ├── "Tôi cần retrieve" ──► Search ──► Generate    │
│            ├── "Tôi biết rồi" ──► Direct answer              │
│            └── "Cần nhiều hơn" ──► Multi-step retrieval      │
│                                                              │
│  Self-reflection tokens:                                    │
│  [Retrieve] - Model quyết định có cần search không         │
│  [Reliable] - Đánh giá retrieved documents                  │
│  [Support]  - Kiểm tra answer có supported không            │
│  [Useful]   - Đánh giá câu trả lời có useful không         │
│                                                              │
│  ✅ Thông minh, tự điều chỉnh                               │
│  ❌ Cần fine-tune model                                     │
│  ❌ Tốn hơn (nhiều LLM calls)                               │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

#### Graph RAG

```
┌──────────────────────────────────────────────────────────────┐
│                       GRAPH RAG                               │
│                                                              │
│  Ý tưởng: Sử dụng Knowledge Graph để improve retrieval      │
│                                                              │
│  Documents ──► NER ──► Entity Extraction                     │
│                  ──► Relationship Extraction                 │
│                  ──► Knowledge Graph Construction            │
│                                                              │
│  Query ──► Entity Recognition                                │
│         ──► Graph Traversal                                  │
│         ──► Community Detection                              │
│         ──► Context Assembly                                 │
│         ──► LLM Generation                                  │
│                                                              │
│  Ví dụ:                                                     │
│  "BHYT của người lao động tại TP.HCM?"                     │
│  → Entities: [BHYT, người lao động, TP.HCM]                │
│  → Graph: BHYT →(applies_to)→ người lao động               │
│           TP.HCM →(has_policy)→ BHYT                         │
│  → Retrieve: all nodes within 2 hops                        │
│                                                              │
│  ✅ Hiểu mối quan hệ phức tạp                              │
│  ✅ Good for multi-hop reasoning                             │
│  ❌ Cần xây dựng Knowledge Graph                            │
│  ❌ Expensive graph operations                               │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

#### Agentic RAG

```
┌──────────────────────────────────────────────────────────────┐
│                      AGENTIC RAG                             │
│                                                              │
│  Ý tưởng: AI Agent tự quyết định retrieval strategy          │
│                                                              │
│  Agent ──► Analyze query                                     │
│         ──► Choose source (DB, API, Vector, Web)            │
│         ──► Execute search                                  │
│         ──► Evaluate results                                │
│         ──► Decide: enough? or search more?                 │
│         ──► Synthesize answer                               │
│                                                              │
│  Agent Tools:                                               │
│  ├── vector_search(query) → [doc1, doc2, ...]              │
│  ├── sql_query(sql) → [row1, row2, ...]                     │
│  ├── web_search(query) → [url1, url2, ...]                 │
│  ├── api_call(endpoint, params) → response                 │
│  └── calculate(expression) → result                         │
│                                                              │
│  ✅ Linh hoạt nhất                                          │
│  ✅ Có thể kết hợp nhiều nguồn                             │
│  ❌ Complex orchestration                                    │
│  ❌ Nhiều LLM calls = tốn hơn                              │
│  ❌ Latency cao hơn                                        │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### 2.4 RAG Evaluation Metrics

```
┌──────────────────────────────────────────────────────────────┐
│                  RAG EVALUATION METRICS                       │
│                                                              │
│  RETRIEVAL METRICS (Đánh giá retrieve):                     │
│  ├── Precision@K: Trong K results, bao nhiêu là relevant?  │
│  ├── Recall@K: Trong tất cả relevant, bao nhiêu được find? │
│  ├── MRR (Mean Reciprocal Rank): Rank của kết quả đúng     │
│  └── NDCG: Normalized Discounted Cumulative Gain            │
│                                                              │
│  GENERATION METRICS (Đánh giá generation):                  │
│  ├── Faithfulness: Answer có faithful với context không?    │
│  ├── Relevancy: Answer có trả lời đúng câu hỏi không?     │
│  ├── Context Relevancy: Context có relevant không?          │
│  └── Hallucination Rate: Bao nhiêu % answer là hallucinate?│
│                                                              │
│  END-TO-END METRICS:                                         │
│  ├── Answer Correctness: So sánh với ground truth          │
│  ├── Answer Similarity: Semantic similarity với truth       │
│  └── Cost per Query: Chi phí mỗi query                     │
│                                                              │
│  Tools: RAGAS, DeepEval, TruLens                            │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 3. Knowledge Graph Retrieval

### 3.1 Khái Niệm

**Knowledge Graph (KG)** là đồ thị biểu diễn tri thức dưới dạng **entities** (đối tượng) và **relationships** (mối quan hệ).

### 3.2 Entity-Relationship Triplets

```
Knowledge Graph được biểu diễn dưới dạng triplets:
(Subject, Predicate, Object)

Ví dụ:
┌─────────────────────────────────────────────────────────────┐
│                    KNOWLEDGE GRAPH EXAMPLE                   │
│                                                             │
│  (gemma3:12b,  is_a,         LLM)                          │
│  (gemma3:12b,  has_size,     12B_params)                    │
│  (gemma3:12b,  runs_on,      Ollama)                        │
│  (gemma3:12b,  made_by,      Google)                        │
│  (Ollama,      is_a,         LLM_Runtime)                   │
│  (Ollama,      supports,     gemma3:12b)                    │
│  (Ollama,      supports,     qwen2.5-coder:14b)            │
│  (Ollama,      platform,     Local)                         │
│  (nomic-embed, is_a,         Embedding_Model)               │
│  (nomic-embed, dimensions,   768)                           │
│  (RAG,         uses,         Embedding_Model)               │
│  (RAG,         uses,         Vector_DB)                     │
│  (RAG,         improves,     LLM_Generation)                │
│                                                             │
│  Visualized:                                                │
│                                                             │
│       [Google] ──made_by──► [gemma3:12b] ──runs_on──► [Ollama]│
│                                  │                         │    │
│                              has_size                   supports│
│                                  │                         │    │
│                                  ▼                         ▼    │
│                             [12B]            [qwen2.5-coder:14b]│
│                                                             │
│       [nomic-embed] ──is_a──► [Embedding_Model]             │
│             │                                                  │
│          uses                                                  │
│             │                                                  │
│             ▼                                                  │
│          [RAG] ──improves──► [LLM_Generation]               │
│             │                                                  │
│          uses                                                  │
│             │                                                  │
│             ▼                                                  │
│        [Vector_DB]                                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 3.3 Knowledge Graph Operations

```python
class KnowledgeGraph:
    """Simple in-memory knowledge graph"""
    
    def __init__(self):
        self.triplets = []
        self.entities = set()
        self.predicates = set()
    
    def add_triplet(self, subject, predicate, obj):
        self.triplets.append((subject, predicate, obj))
        self.entities.add(subject)
        self.entities.add(obj)
        self.predicates.add(predicate)
    
    def add_from_text(self, text, llm_func):
        """
        Extract triplets from text using LLM
        
        Input: "gemma3:12b là model LLM 12B chạy trên Ollama"
        Output: [("gemma3:12b", "is_a", "LLM"),
                 ("gemma3:12b", "has_size", "12B"),
                 ("gemma3:12b", "runs_on", "Ollama")]
        """
        prompt = f"""Extract entity-relationship triplets from the following text.
Format: (subject, predicate, object)

Text: {text}

Triplets:"""
        
        response = llm_func(prompt)
        
        # Parse triplets
        import re
        triplet_pattern = r'\(([^,]+),\s*([^,]+),\s*([^)]+)\)'
        matches = re.findall(triplet_pattern, response)
        
        for subject, predicate, obj in matches:
            self.add_triplet(
                subject.strip(), 
                predicate.strip(), 
                obj.strip()
            )
        
        return matches
    
    def query_entity(self, entity, max_hops=2):
        """Find all triplets related to an entity (up to max_hops)"""
        results = []
        visited = set()
        queue = [(entity, 0)]
        
        while queue:
            current, depth = queue.pop(0)
            if depth > max_hops or current in visited:
                continue
            
            visited.add(current)
            
            for subj, pred, obj in self.triplets:
                if subj == current:
                    results.append((subj, pred, obj, depth))
                    if depth < max_hops:
                        queue.append((obj, depth + 1))
                elif obj == current:
                    results.append((obj, pred, subj, depth))
                    if depth < max_hops:
                        queue.append((subj, depth + 1))
        
        return results
    
    def find_path(self, start, end, max_depth=5):
        """Find shortest path between two entities"""
        from collections import deque
        
        queue = deque([(start, [start])])
        visited = {start}
        
        while queue:
            current, path = queue.popleft()
            
            if current == end:
                return path
            
            if len(path) > max_depth:
                continue
            
            for subj, pred, obj in self.triplets:
                next_entity = None
                edge = None
                
                if subj == current:
                    next_entity = obj
                    edge = f"--{pred}-->"
                elif obj == current:
                    next_entity = subj
                    edge = f"<--{pred}--"
                
                if next_entity and next_entity not in visited:
                    visited.add(next_entity)
                    queue.append((next_entity, path + [edge, next_entity]))
        
        return None  # No path found
    
    def community_detection(self):
        """
        Detect communities (nhóm entities liên quan)
        
        Simple approach: Connected components
        """
        visited = set()
        communities = []
        
        for entity in self.entities:
            if entity not in visited:
                community = []
                queue = [entity]
                while queue:
                    current = queue.pop(0)
                    if current in visited:
                        continue
                    visited.add(current)
                    community.append(current)
                    
                    for subj, pred, obj in self.triplets:
                        if subj == current and obj not in visited:
                            queue.append(obj)
                        elif obj == current and subj not in visited:
                            queue.append(subj)
                
                communities.append(community)
        
        return communities
    
    def summarize_community(self, community, llm_func):
        """Generate summary for a community of entities"""
        # Gather all facts about entities in community
        facts = []
        for entity in community:
            for subj, pred, obj in self.triplets:
                if subj == entity or obj == entity:
                    facts.append(f"{subj} {pred} {obj}")
        
        prompt = f"""Tóm tắt các thông tin sau về một nhóm entities liên quan:

{chr(10).join(facts)}

Tóm tắt:"""
        
        return llm_func(prompt)
```

### 3.4 Graph RAG Implementation

```python
class GraphRAG:
    """Graph-based RAG system"""
    
    def __init__(self, knowledge_graph, embedder, llm_model):
        self.kg = knowledge_graph
        self.embedder = embedder
        self.llm_model = llm_model
    
    def retrieve(self, query):
        """
        Retrieve context from knowledge graph
        
        1. Extract entities from query
        2. Find related subgraph
        3. Rank by relevance
        4. Convert to context
        """
        # Step 1: Extract entities from query
        entities = self._extract_entities(query)
        
        # Step 2: Get subgraph for each entity
        subgraph_facts = []
        for entity in entities:
            related = self.kg.query_entity(entity, max_hops=2)
            subgraph_facts.extend(related)
        
        # Step 3: Rank by relevance to query
        query_embedding = self.embedder.embed_query(query)
        
        ranked_facts = []
        for subj, pred, obj, depth in subgraph_facts:
            fact_text = f"{subj} {pred} {obj}"
            fact_embedding = self.embedder.embed(fact_text)
            
            # Score = relevance × (1 / (depth + 1))
            import numpy as np
            similarity = np.dot(query_embedding, fact_embedding) / (
                np.linalg.norm(query_embedding) * np.linalg.norm(fact_embedding)
            )
            score = similarity * (1 / (depth + 1))
            ranked_facts.append((fact_text, score))
        
        ranked_facts.sort(key=lambda x: x[1], reverse=True)
        
        # Step 4: Convert to context
        context = "\n".join([
            f"[{i+1}] {fact}" 
            for i, (fact, _) in enumerate(ranked_facts[:10])
        ])
        
        return context
    
    def _extract_entities(self, text):
        """Simple entity extraction (can use NER model)"""
        # Placeholder - use LLM for better extraction
        prompt = f"""Extract all entities (names, products, concepts) from:
{text}

Entities (one per line):"""
        
        response = requests.post("http://localhost:11434/api/generate", json={
            "model": self.llm_model,
            "prompt": prompt,
            "stream": False
        })
        
        entities = [
            e.strip() for e in response.json()["response"].split('\n') 
            if e.strip()
        ]
        return entities
```

---

## 4. Hybrid Search

### 4.1 Tại Sao Cần Hybrid Search?

```
┌──────────────────────────────────────────────────────────────────┐
│                 SEMANTIC vs KEYWORD SEARCH                        │
│                                                                  │
│  Semantic Search (Vector):                                      │
│  ✅ Hiểu ngữ nghĩa ("bệnh tim" ≈ "cardiovascular disease")    │
│  ✅ Robust với synonyms                                         │
│  ✅ Good for natural language queries                           │
│  ❌ Yếu với exact match (mã, số, tên riêng)                    │
│  ❌ Có thể miss chính xác keyword                              │
│                                                                  │
│  Keyword Search (BM25/TF-IDF):                                 │
│  ✅ Perfect exact match                                         │
│  ✅ Hiệu quả với technical terms                               │
│  ✅ Nhanh, không cần embedding                                  │
│  ❌ Không hiểu synonym/paraphrase                              │
│  ❌ Không handle spelling errors                                │
│                                                                  │
│  HYBRAND = Kết hợp cả hai → Best of both worlds!               │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 4.2 BM25 Algorithm

```python
import math
from collections import Counter

class BM25:
    """
    Okapi BM25 scoring function
    
    BM25(D, Q) = Σ IDF(qi) × (f(qi, D) × (k1 + 1)) / 
                        (f(qi, D) + k1 × (1 - b + b × |D|/avgdl))
    
    Where:
    - IDF(qi): Inverse Document Frequency of term qi
    - f(qi, D): Term frequency of qi in document D
    - k1: Term saturation parameter (default 1.5)
    - b: Length normalization parameter (default 0.75)
    - |D|: Document length
    - avgdl: Average document length
    """
    
    def __init__(self, k1=1.5, b=0.75):
        self.k1 = k1
        self.b = b
        self.documents = []
        self.doc_freqs = {}
        self.avgdl = 0
        self.n_docs = 0
    
    def fit(self, documents):
        """Build index from documents"""
        self.documents = documents
        self.n_docs = len(documents)
        
        # Calculate document frequencies
        for doc in documents:
            terms = doc.lower().split()
            unique_terms = set(terms)
            for term in unique_terms:
                self.doc_freqs[term] = self.doc_freqs.get(term, 0) + 1
        
        # Average document length
        self.avgdl = sum(len(doc.split()) for doc in documents) / self.n_docs
    
    def _idf(self, term):
        """Inverse Document Frequency"""
        n_containing = self.doc_freqs.get(term, 0)
        return math.log((self.n_docs - n_containing + 0.5) / (n_containing + 0.5) + 1)
    
    def _score(self, document, query_terms):
        """BM25 score for a document"""
        terms = document.lower().split()
        doc_len = len(terms)
        term_freqs = Counter(terms)
        
        score = 0
        for term in query_terms:
            if term not in term_freqs:
                continue
            
            f = term_freqs[term]
            idf = self._idf(term)
            
            numerator = f * (self.k1 + 1)
            denominator = f + self.k1 * (1 - self.b + self.b * doc_len / self.avgdl)
            
            score += idf * numerator / denominator
        
        return score
    
    def search(self, query, top_k=10):
        """Search for most relevant documents"""
        query_terms = query.lower().split()
        
        scores = []
        for i, doc in enumerate(self.documents):
            score = self._score(doc, query_terms)
            scores.append((i, score))
        
        scores.sort(key=lambda x: x[1], reverse=True)
        
        return [(self.documents[i], score) for i, score in scores[:top_k]]
```

### 4.3 Reciprocal Rank Fusion (RRF)

```python
def reciprocal_rank_fusion(rankings, k=60):
    """
    Combine multiple ranked lists using RRF
    
    RRF_score(d) = Σ 1/(k + rank_i(d))
    
    k: constant (default 60, used by many implementations)
    
    Advantages:
    - Simple to implement
    - No need to normalize scores across different methods
    - Robust to outlier scores
    """
    scores = {}
    
    for ranking_list in rankings:
        for rank, doc_id in enumerate(ranking_list, start=1):
            if doc_id not in scores:
                scores[doc_id] = 0
            scores[doc_id] += 1 / (k + rank)
    
    # Sort by RRF score descending
    sorted_docs = sorted(scores.keys(), key=lambda x: scores[x], reverse=True)
    return sorted_docs


# Example with detailed calculation
semantic_ranking = ["doc_A", "doc_B", "doc_C", "doc_D", "doc_E"]
keyword_ranking = ["doc_B", "doc_D", "doc_A", "doc_F", "doc_C"]
metadata_ranking = ["doc_A", "doc_C", "doc_E", "doc_B", "doc_D"]

merged = reciprocal_rank_fusion(
    [semantic_ranking, keyword_ranking, metadata_ranking], k=60
)

# Score calculation for doc_A:
# semantic: 1/(60+1) = 0.01639
# keyword:  1/(60+3) = 0.01587
# metadata: 1/(60+1) = 0.01639
# Total:    0.04866
```

### 4.4 Hybrid Search Implementation

```python
class HybridSearch:
    """Combines semantic (vector) search with keyword (BM25) search"""
    
    def __init__(self, vector_store, embedder, bm25_index, 
                 rrf_k=60, semantic_weight=1.0, keyword_weight=1.0):
        self.vector_store = vector_store
        self.embedder = embedder
        self.bm25 = bm25_index
        self.rrf_k = rrf_k
        self.semantic_weight = semantic_weight
        self.keyword_weight = keyword_weight
    
    def search(self, query, top_k=10):
        """Perform hybrid search"""
        
        # 1. Semantic search
        query_embedding = self.embedder.embed_query(query)
        semantic_results = self.vector_store.search(
            query_embedding, top_k=top_k * 2
        )
        semantic_ranking = [r["document"] for r in semantic_results]
        
        # 2. Keyword search (BM25)
        bm25_results = self.bm25.search(query, top_k=top_k * 2)
        keyword_ranking = [doc for doc, score in bm25_results]
        
        # 3. Combine using RRF
        combined_ranking = reciprocal_rank_fusion(
            [semantic_ranking, keyword_ranking],
            k=self.rrf_k
        )
        
        # 4. Return top_k
        return combined_ranking[:top_k]
    
    def search_with_scores(self, query, top_k=10):
        """Hybrid search with individual scores"""
        
        # Semantic
        query_embedding = self.embedder.embed_query(query)
        semantic_results = self.vector_store.search(
            query_embedding, top_k=top_k * 2
        )
        
        # BM25
        bm25_results = self.bm25.search(query, top_k=top_k * 2)
        
        # Score normalization and combination
        # Normalize scores to [0, 1]
        max_semantic = max(r["score"] for r in semantic_results) if semantic_results else 1
        max_bm25 = max(score for _, score in bm25_results) if bm25_results else 1
        
        combined_scores = {}
        
        for r in semantic_results:
            doc = r["document"]
            norm_score = r["score"] / max_semantic
            combined_scores[doc] = {
                "semantic_score": r["score"],
                "keyword_score": 0,
                "combined_score": norm_score * self.semantic_weight,
            }
        
        for doc, score in bm25_results:
            norm_score = score / max_bm25
            if doc in combined_scores:
                combined_scores[doc]["keyword_score"] = score
                combined_scores[doc]["combined_score"] += norm_score * self.keyword_weight
            else:
                combined_scores[doc] = {
                    "semantic_score": 0,
                    "keyword_score": score,
                    "combined_score": norm_score * self.keyword_weight,
                }
        
        # Sort by combined score
        sorted_results = sorted(
            combined_scores.items(),
            key=lambda x: x[1]["combined_score"],
            reverse=True
        )
        
        return sorted_results[:top_k]
```

---

## 5. Re-ranking

### 5.1 Tại Sao Cần Re-ranking?

```
┌──────────────────────────────────────────────────────────────────┐
│                    WHY RE-RANK?                                  │
│                                                                  │
│  Bi-encoder (Initial Retrieval):                                │
│  ├── Query và documents được embed RIÊNG BIỆT                  │
│  ├── Tính dot-product/cosine → approximate similarity           │
│  ├── ✅ Nhanh (pre-computed embeddings)                        │
│  └── ❌ Không chính xác (không thấy interaction q↔d)           │
│                                                                  │
│  Cross-encoder (Re-ranking):                                    │
│  ├── Query và document được input ĐỒNG THỜI vào model         │
│  ├── Cross-attention giữa query và document                    │
│  ├── ✅ Rất chính xác (thấy interaction q↔d)                  │
│  └── ❌ Chậm (phải compute cho từng pair)                      │
│                                                                  │
│  Strategy: Retrieve top-50 với bi-encoder → Re-rank top-5      │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 5.2 Cross-Encoder Implementation

```python
from sentence_transformers import CrossEncoder
import numpy as np

class Reranker:
    """Re-rank documents using a cross-encoder model"""
    
    def __init__(self, model_name="cross-encoder/ms-marco-MiniLM-L-6-v2"):
        self.model = CrossEncoder(model_name)
    
    def rerank(self, query, documents, top_k=5):
        """
        Re-rank documents based on query relevance
        
        Args:
            query: Search query
            documents: List of document strings or dicts
            top_k: Number of results to return
        
        Returns:
            Reranked list with scores
        """
        # Create (query, document) pairs
        if isinstance(documents[0], dict):
            doc_texts = [d["document"] for d in documents]
        else:
            doc_texts = documents
        
        pairs = [(query, doc) for doc in doc_texts]
        
        # Score with cross-encoder
        scores = self.model.predict(pairs)
        
        # Combine and sort
        results = []
        for i, (doc, score) in enumerate(zip(documents, scores)):
            if isinstance(doc, dict):
                result = {**doc, "rerank_score": float(score)}
            else:
                result = {"document": doc, "rerank_score": float(score)}
            results.append(result)
        
        results.sort(key=lambda x: x["rerank_score"], reverse=True)
        return results[:top_k]
    
    def rerank_with_feedback(self, query, documents, top_k=5, 
                              user_feedback=None):
        """
        Re-rank with optional user feedback (learning from clicks)
        """
        results = self.rerank(query, documents, top_k=top_k * 2)
        
        if user_feedback:
            # Boost documents that user found relevant
            for r in results:
                doc_text = r["document"]
                if doc_text in user_feedback.get("relevant", []):
                    r["rerank_score"] *= 1.5  # Boost
                elif doc_text in user_feedback.get("irrelevant", []):
                    r["rerank_score"] *= 0.5  # Penalize
        
        results.sort(key=lambda x: x["rerank_score"], reverse=True)
        return results[:top_k]
```

### 5.3 Re-ranking Models Comparison

```
┌────────────────────────────────┬──────────┬──────────┬──────────┬──────────────┐
│ Model                          │ Quality  │ Speed    │ Size     │ Best For     │
│                                │ (MTEB)   │ (ms/doc) │          │              │
├────────────────────────────────┼──────────┼──────────┼──────────┼──────────────┤
│ cross-encoder/ms-marco-        │ 33.8     │ ~5       │ 80MB     │ English,     │
│   MiniLM-L-6-v2               │          │          │          │ fast         │
├────────────────────────────────┼──────────┼──────────┼──────────┼──────────────┤
│ cross-encoder/ms-marco-        │ 34.5     │ ~10      │ 130MB    │ English,     │
│   MiniLM-L-12-v2              │          │          │          │ balanced     │
├────────────────────────────────┼──────────┼──────────┼──────────┼──────────────┤
│ cross-encoder/ms-marco-        │ 36.1     │ ~20      │ 440MB    │ English,     │
│   electron-base               │          │          │          │ accurate     │
├────────────────────────────────┼──────────┼──────────┼──────────┼──────────────┤
│ BAAI/bge-reranker-base        │ 34.2     │ ~8       │ 1.1GB    │ Multilingual │
├────────────────────────────────┼──────────┼──────────┼──────────┼──────────────┤
│ BAAI/bge-reranker-large       │ 35.9     │ ~25      │ 2.2GB    │ Multilingual │
├────────────────────────────────┼──────────┼──────────┼──────────┼──────────────┤
│ Cohere Rerank v3              │ ~37      │ ~50      │ API      │ Production   │
├────────────────────────────────┼──────────┼──────────┼──────────┼──────────────┤
│ Jina Reranker v2              │ ~36      │ ~30      │ API      │ Multilingual │
├────────────────────────────────┼──────────┼──────────┼──────────┼──────────────┤
│ YOUR custom fine-tuned        │ Varies   │ Varies   │ Varies   │ Domain-      │
│                                │          │          │          │ specific     │
└────────────────────────────────┴──────────┴──────────┴──────────┴──────────────┘

Lưu ý: Quality scores là approximate, thay đổi tùy evaluation benchmark
```

---

## 6. Memory Systems

### 6.1 Memory Types Chi Tiết

```
┌──────────────────────────────────────────────────────────────────┐
│                    MEMORY TYPES IN DETAIL                         │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  SENSORY MEMORY (Bộ nhớ giác quan)                               │
│  ├── Duration: ~1 giây                                          │
│  ├── Capacity: Bounded by sensory organs                        │
│  ├── AI Equivalent: Current input tokens                        │
│  ├── Ví dụ: Text user đang nhập, hình ảnh hiện tại            │
│  └── Management: Không cần (tự mất)                             │
│                                                                  │
│  WORKING MEMORY (Bộ nhớ làm việc)                               │
│  ├── Duration: Trong khi xử lý                                  │
│  ├── Capacity: Context window size                              │
│  ├── AI Equivalent: Token context, scratchpad                   │
│  ├── Ví dụ: 128K token window, tool outputs                     │
│  └── Management: Token budget allocation                        │
│                                                                  │
│  SHORT-TERM MEMORY (Bộ nhớ ngắn hạn)                            │
│  ├── Duration: 1 session (~phút-giờ)                            │
│  ├── Capacity: Conversation length                              │
│  ├── AI Equivalent: Chat history trong session                  │
│  ├── Ví dụ: Messages trong cuộc trò chuyện hiện tại           │
│  └── Management: Buffer/Window/Summary strategies               │
│                                                                  │
│  LONG-TERM MEMORY (Bộ nhớ dài hạn)                              │
│  ├── Duration: Vĩnh viễn (nếu được store)                      │
│  ├── Capacity: Unlimited (external storage)                     │
│  ├── AI Equivalent: Vector DB, files, databases                 │
│  ├── Sub-types:                                                  │
│  │   ├── Episodic: Các sự kiện đã trải qua                     │
│  │   ├── Semantic: Facts, knowledge                            │
│  │   └── Procedural: Skills, how-to knowledge                  │
│  └── Management: Retrieval, update, consolidation               │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 6.2 Memory Patterns Chi Tiết

#### Buffer Memory

```python
from collections import deque

class BufferMemory:
    """
    Giữ TẤT CẢ messages trong buffer
    
    ✅ Không mất thông tin
    ❌ Token usage tăng linear
    ❌ Context window sẽ đầy
    """
    
    def __init__(self):
        self.messages = []
    
    def add(self, role, content):
        self.messages.append({"role": role, "content": content})
    
    def get_context(self):
        return self.messages  # Return ALL
    
    def get_token_count(self, tokenizer):
        """Estimate token count"""
        total = 0
        for msg in self.messages:
            total += len(tokenizer.encode(msg["content"]))
        return total
```

#### Window Memory

```python
class WindowMemory:
    """
    Giữ N messages gần nhất
    
    ✅ Token usage cố định
    ✅ Simple implementation
    ❌ Mất context từ messages cũ
    """
    
    def __init__(self, window_size=10):
        self.window_size = window_size
        self.messages = deque(maxlen=window_size)
    
    def add(self, role, content):
        self.messages.append({"role": role, "content": content})
    
    def get_context(self):
        return list(self.messages)
```

#### Summary Memory

```python
class SummaryMemory:
    """
    Tóm tắt messages cũ, giữ messages gần nhất
    
    ✅ Balance giữa info và token usage
    ❌ Summary có thể mất detail
    ❌ Cần LLM để summarize
    """
    
    def __init__(self, window_size=5, llm_func=None):
        self.window_size = window_size
        self.messages = deque(maxlen=window_size)
        self.summary = ""
        self.llm = llm_func
    
    def add(self, role, content):
        self.messages.append({"role": role, "content": content})
    
    def get_context(self):
        context = []
        
        if self.summary:
            context.append({
                "role": "system",
                "content": f"Tóm tắt cuộc hội thoại trước:\n{self.summary}"
            })
        
        context.extend(list(self.messages))
        return context
    
    def update_summary(self):
        """Summarize all messages except recent window"""
        if len(self.messages) <= self.window_size:
            return
        
        # Messages to summarize
        old_messages = list(self.messages)[:-self.window_size]
        
        conversation = "\n".join([
            f"{m['role']}: {m['content']}" for m in old_messages
        ])
        
        if self.llm:
            self.summary = self.llm(
                f"Tóm tắt ngắn gọn:\n{conversation}\n\nTóm tắt:"
            )
```

#### Entity Memory

```python
class EntityMemory:
    """
    Theo dõi entities (người, nơi, sự kiện) được nhắc đến
    
    ✅ Structured information
    ✅ Persistent across sessions
    ❌ Cần NER (Named Entity Recognition)
    """
    
    def __init__(self):
        self.entities = {}  # entity_name -> {attributes}
    
    def extract_and_update(self, text, llm_func):
        """Extract entities from text and update memory"""
        prompt = f"""Extract entities from this text:
Text: {text}

Format as JSON:
{{
  "entities": [
    {{"name": "...", "type": "person/org/location/...", "attributes": {{"key": "value"}}}},
  ]
}}"""
        
        response = llm_func(prompt)
        
        # Parse and update
        import json
        try:
            data = json.loads(response)
            for entity in data.get("entities", []):
                name = entity["name"]
                if name not in self.entities:
                    self.entities[name] = {
                        "type": entity.get("type", "unknown"),
                        "attributes": {},
                    }
                self.entities[name]["attributes"].update(
                    entity.get("attributes", {})
                )
        except json.JSONDecodeError:
            pass
    
    def recall(self, entity_name):
        """Recall information about an entity"""
        return self.entities.get(entity_name, None)
    
    def get_context_string(self):
        """Get all entities as context string"""
        lines = []
        for name, data in self.entities.items():
            attrs = ", ".join(
                f"{k}={v}" for k, v in data["attributes"].items()
            )
            lines.append(f"- {name} ({data['type']}): {attrs}")
        return "\n".join(lines)
```

#### Semantic Memory (Vector-based)

```python
class SemanticMemory:
    """
    Store và retrieve facts using vector search
    
    ✅ Scalable
    ✅ Semantic search capability
    ❌ Dependent on embedding quality
    """
    
    def __init__(self, vector_store, embedder):
        self.store = vector_store
        self.embedder = embedder
        self.fact_count = 0
    
    def add_fact(self, fact, metadata=None):
        """Store a new fact"""
        embedding = self.embedder.embed(fact)
        self.store.add(
            document=fact,
            vector=embedding,
            metadata={
                "type": "fact",
                "id": f"fact_{self.fact_count}",
                **(metadata or {}),
            }
        )
        self.fact_count += 1
    
    def recall(self, query, top_k=5):
        """Recall relevant facts"""
        query_embedding = self.embedder.embed_query(query)
        results = self.store.search(query_embedding, top_k)
        return results
    
    def consolidate(self, llm_func):
        """
        Consolidate similar facts (reduce redundancy)
        """
        # Get all facts
        all_facts = self.store.search(
            self.embedder.embed("fact"), top_k=1000
        )
        
        # Group similar facts
        groups = self._cluster_facts(all_facts)
        
        # Summarize each group
        consolidated = []
        for group in groups:
            if len(group) == 1:
                consolidated.append(group[0]["document"])
            else:
                facts_text = "\n".join(
                    f"- {f['document']}" for f in group
                )
                summary = llm_func(
                    f"Consolidate these facts:\n{facts_text}\n\nConsolidated:"
                )
                consolidated.append(summary)
        
        return consolidated
    
    def _cluster_facts(self, facts, threshold=0.8):
        """Group similar facts together"""
        import numpy as np
        
        if not facts:
            return []
        
        embeddings = [f.get("vector", []) for f in facts]
        
        groups = []
        used = set()
        
        for i, emb_i in enumerate(embeddings):
            if i in used:
                continue
            
            group = [facts[i]]
            used.add(i)
            
            for j, emb_j in enumerate(embeddings):
                if j in used or not emb_i or not emb_j:
                    continue
                
                sim = np.dot(emb_i, emb_j) / (
                    np.linalg.norm(emb_i) * np.linalg.norm(emb_j)
                )
                if sim > threshold:
                    group.append(facts[j])
                    used.add(j)
            
            groups.append(group)
        
        return groups
```

### 6.3 MemGPT-Style Memory Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                MEMGPT MEMORY ARCHITECTURE                        │
│                                                                  │
│  Inspired by OS virtual memory concept                           │
│                                                                  │
│  ┌────────────────────────────────────────────┐                 │
│  │             CORE MEMORY (RAM)               │                 │
│  │  Always in context                          │                 │
│  │  ├── User persona (name, preferences)      │                 │
│  │  ├── Bot persona (personality, rules)      │                 │
│  │  └── Active tasks/projects                 │                 │
│  │  Size: ~1000 tokens                        │                 │
│  └────────────────────────────────────────────┘                 │
│                                                                  │
│  ┌────────────────────────────────────────────┐                 │
│  │           RECALL MEMORY (Disk)              │                 │
│  │  Full conversation history                  │                 │
│  │  Searchable via keywords/timestamps         │                 │
│  │  Can be paged into core memory              │                 │
│  │  Size: Unlimited                            │                 │
│  └────────────────────────────────────────────┘                 │
│                                                                  │
│  ┌────────────────────────────────────────────┐                 │
│  │           ARCHIVAL MEMORY (Archive)         │                 │
│  │  Long-term knowledge & facts                │                 │
│  │  Vector-searchable                          │                 │
│  │  Can be paged into core memory              │                 │
│  │  Size: Unlimited                            │                 │
│  └────────────────────────────────────────────┘                 │
│                                                                  │
│  Operations:                                                     │
│  ├── core_memory_append(name, content)                          │
│  ├── core_memory_replace(name, old, new)                        │
│  ├── recall_memory_search(query)                                │
│  ├── recall_memory_search_by_date(start, end)                   │
│  ├── archival_memory_insert(content)                            │
│  └── archival_memory_search(query)                              │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 6.4 Complete Memory Manager

```python
class MemoryManager:
    """
    Complete memory system combining multiple strategies
    """
    
    def __init__(self, llm_model="gemma3:12b"):
        self.llm_model = llm_model
        self.ollama_url = "http://localhost:11434"
        
        # Different memory types
        self.core_memory = {}  # Always in context
        self.conversation = deque(maxlen=20)  # Short-term
        self.summary = ""  # Compressed history
        self.entity_memory = EntityMemory()  # Entities
        self.semantic_memory = None  # Vector store (optional)
        
        # Stats
        self.stats = {
            "total_messages": 0,
            "total_tokens_used": 0,
        }
    
    def update_core_memory(self, key, value):
        """Update core memory (always in context)"""
        self.core_memory[key] = value
    
    def add_message(self, role, content):
        """Add message to conversation"""
        self.conversation.append({
            "role": role,
            "content": content,
            "timestamp": datetime.now().isoformat(),
        })
        self.stats["total_messages"] += 1
        
        # Extract entities
        if role == "user":
            self.entity_memory.extract_and_update(content, self._llm_call)
    
    def get_context(self, strategy="smart"):
        """
        Get context based on strategy
        
        Strategies:
        - buffer: All messages
        - window: Last N messages
        - summary: Summary + recent
        - smart: Dynamic based on token budget
        """
        context = []
        
        # Core memory (always)
        if self.core_memory:
            core_text = "\n".join(
                f"- {k}: {v}" for k, v in self.core_memory.items()
            )
            context.append({
                "role": "system",
                "content": f"Core Memory:\n{core_text}"
            })
        
        # Entity memory
        entity_context = self.entity_memory.get_context_string()
        if entity_context:
            context.append({
                "role": "system",
                "content": f"Known Entities:\n{entity_context}"
            })
        
        # Conversation strategy
        if strategy == "buffer":
            context.extend(list(self.conversation))
        elif strategy == "window":
            context.extend(list(self.conversation)[-10:])
        elif strategy == "summary":
            if self.summary:
                context.append({
                    "role": "system",
                    "content": f"Previous context summary:\n{self.summary}"
                })
            context.extend(list(self.conversation)[-5:])
        elif strategy == "smart":
            # Dynamic strategy based on token budget
            budget_remaining = 8000  # tokens for conversation
            
            # Start with recent messages
            recent = list(self.conversation)[-10:]
            recent_tokens = sum(
                len(m["content"].split()) for m in recent
            ) * 1.3  # rough token estimate
            
            if recent_tokens < budget_remaining:
                context.extend(recent)
                
                # Add summary if we have budget
                if self.summary and budget_remaining - recent_tokens > 200:
                    context.insert(-len(recent), {
                        "role": "system",
                        "content": f"Summary:\n{self.summary}"
                    })
            else:
                # Too many messages, use summary
                if self.summary:
                    context.append({
                        "role": "system",
                        "content": f"Summary:\n{self.summary}"
                    })
                context.extend(list(self.conversation)[-5:])
        
        return context
    
    def update_summary(self):
        """Update conversation summary"""
        if len(self.conversation) < 10:
            return
        
        old_messages = list(self.conversation)[:-5]
        conversation_text = "\n".join(
            f"{m['role']}: {m['content'][:200]}" for m in old_messages
        )
        
        prompt = f"""Tóm tắt cuộc hội thoại sau trong 3-5 câu:

{conversation_text}

Tóm tắt:"""
        
        self.summary = self._llm_call(prompt)
    
    def _llm_call(self, prompt):
        """Call LLM"""
        response = requests.post(f"{self.ollama_url}/api/generate", json={
            "model": self.llm_model,
            "prompt": prompt,
            "stream": False
        })
        return response.json()["response"]
    
    def get_stats(self):
        """Return memory statistics"""
        return {
            **self.stats,
            "core_memory_size": len(self.core_memory),
            "conversation_length": len(self.conversation),
            "entity_count": len(self.entity_memory.entities),
            "has_summary": bool(self.summary),
        }
```

---

## 7. Labs Thực Hành

### Lab 1: Semantic Search với nomic-embed-text

```bash
# Test embedding
curl -s http://localhost:11434/api/embed -d '{
  "model": "nomic-embed-text",
  "input": ["Bảo hiểm y tế là gì?", "Cardiovascular disease prevention"]
}' | jq '.embeddings | length'
# Output: 2

# Check embedding dimensions
curl -s http://localhost:11434/api/embed -d '{
  "model": "nomic-embed-text",
  "input": "test"
}' | jq '.embeddings[0] | length'
# Output: 768
```

### Lab 2: BM25 + Vector Hybrid Search

```python
# Complete hybrid search demo
import requests
import numpy as np
from collections import Counter
import math

OLLAMA_URL = "http://localhost:11434"

# --- Documents ---
documents = [
    "BHYT (Bảo hiểm y tế) là hình thức bảo hiểm bắt buộc tại Việt Nam.",
    "Mức đóng BHYT cho người lao động là 4.5% mức lương cơ sở.",
    "Người có thẻ BHYT được khám chữa bệnh tại các cơ sở y tế.",
    "BHYT chi trả từ 80% đến 100% chi phí tùy tuyến.",
    "Thẻ BHYT có hiệu lực trong 5 năm kể từ ngày cấp.",
    "Đăng ký BHYT tại cơ quan bảo hiểm xã hội.",
    "BHYT tự nguyện dành cho người không thuộc diện bắt buộc.",
]

# --- Semantic Search ---
def get_embedding(text):
    response = requests.post(f"{OLLAMA_URL}/api/embed", json={
        "model": "nomic-embed-text",
        "input": text
    })
    return response.json()["embeddings"][0]

def semantic_search(query, docs, top_k=3):
    query_emb = get_embedding(query)
    doc_embs = [get_embedding(doc) for doc in docs]
    
    scores = []
    for i, doc_emb in enumerate(doc_embs):
        score = np.dot(query_emb, doc_emb) / (
            np.linalg.norm(query_emb) * np.linalg.norm(doc_emb)
        )
        scores.append((i, score))
    
    scores.sort(key=lambda x: x[1], reverse=True)
    return [(docs[i], score) for i, score in scores[:top_k]]

# --- BM25 Search ---
class BM25:
    def __init__(self, k1=1.5, b=0.75):
        self.k1 = k1
        self.b = b
    
    def fit(self, docs):
        self.docs = docs
        self.n = len(docs)
        self.doc_freqs = {}
        self.doc_lens = []
        
        for doc in docs:
            terms = doc.lower().split()
            self.doc_lens.append(len(terms))
            for term in set(terms):
                self.doc_freqs[term] = self.doc_freqs.get(term, 0) + 1
        
        self.avgdl = sum(self.doc_lens) / self.n
    
    def search(self, query, top_k=3):
        query_terms = query.lower().split()
        scores = []
        
        for i, doc in enumerate(self.docs):
            terms = doc.lower().split()
            tf = Counter(terms)
            score = 0
            
            for term in query_terms:
                if term in tf:
                    idf = math.log(
                        (self.n - self.doc_freqs.get(term, 0) + 0.5) /
                        (self.doc_freqs.get(term, 0) + 0.5) + 1
                    )
                    f = tf[term]
                    numerator = f * (self.k1 + 1)
                    denominator = f + self.k1 * (
                        1 - self.b + self.b * self.doc_lens[i] / self.avgdl
                    )
                    score += idf * numerator / denominator
            
            scores.append((i, score))
        
        scores.sort(key=lambda x: x[1], reverse=True)
        return [(self.docs[i], score) for i, score in scores[:top_k]]

# --- Hybrid Search ---
def rrf_fusion(rankings, k=60):
    scores = {}
    for ranking in rankings:
        for rank, doc in enumerate(ranking, 1):
            if doc not in scores:
                scores[doc] = 0
            scores[doc] += 1 / (k + rank)
    return sorted(scores.keys(), key=lambda x: scores[x], reverse=True)

# --- Run Demo ---
query = "BHYT đóng bao nhiêu tiền?"

print("=== Semantic Search ===")
for doc, score in semantic_search(query, documents):
    print(f"  {score:.4f} | {doc[:60]}...")

print("\n=== BM25 Search ===")
bm25 = BM25()
bm25.fit(documents)
for doc, score in bm25.search(query):
    print(f"  {score:.4f} | {doc[:60]}...")

print("\n=== Hybrid Search (RRF) ===")
semantic_results = [doc for doc, _ in semantic_search(query, documents, 5)]
bm25_results = [doc for doc, _ in bm25.search(query, 5)]

hybrid = rrf_fusion([semantic_results, bm25_results])
for i, doc in enumerate(hybrid[:3]):
    print(f"  #{i+1} | {doc[:60]}...")
```

### Lab 3: Full RAG Pipeline

```python
# Run: python 01-retrieve-memory-knowledge/rag_lab.py
# (See rag_lab.py file in this directory)
```

---

*Tài liệu: I. Retrieve Memory & Knowledge*
*Ngày tạo: 2026-07-11*
*Môi trường: Ollama (gemma3:12b, nomic-embed-text)*