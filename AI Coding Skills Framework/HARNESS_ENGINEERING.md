# Harness Engineering - Kỹ Thuật Thiết Kế Hệ Thống Xung Quanh AI Agents

> **"Mỗi khi agent mắc lỗi, bạn không cầu nguyện nó làm tốt hơn lần sau – bạn xây dựng một giải pháp hệ thống để nó không bao giờ mắc lỗi đó nữa"**  
> — Mitchell Hashimoto (Founder of HashiCorp, Creator of Terraform)

---

## Mục Lục

- [Harness Engineering - Kỹ Thuật Thiết Kế Hệ Thống Xung Quanh AI Agents](#harness-engineering---kỹ-thuật-thiết-kế-hệ-thống-xung-quanh-ai-agents)
  - [Mục Lục](#mục-lục)
  - [1. Giới Thiệu](#1-giới-thiệu)
    - [Bối Cảnh](#bối-cảnh)
    - [Mục Đích Của Tài Liệu](#mục-đích-của-tài-liệu)
  - [2. Harness Engineering Là Gì?](#2-harness-engineering-là-gì)
    - [Định Nghĩa](#định-nghĩa)
    - [Triết Lý Cốt Lõi](#triết-lý-cốt-lõi)
    - [Ví Dụ Minh Họa](#ví-dụ-minh-họa)
    - [So Sánh Với Các Khái Niệm Khác](#so-sánh-với-các-khái-niệm-khác)
  - [3. Ba Giai Đoạn Tiến Hóa Của Kỹ Nghệ AI](#3-ba-giai-đoạn-tiến-hóa-của-kỹ-nghệ-ai)
    - [3.1. Prompt Engineering (2022-2024)](#31-prompt-engineering-2022-2024)
    - [3.2. Context Engineering (2025)](#32-context-engineering-2025)
    - [3.3. Harness Engineering (2026+)](#33-harness-engineering-2026)
    - [So Sánh Tổng Quan](#so-sánh-tổng-quan)
    - [Insight Quan Trọng](#insight-quan-trọng)
  - [4. Tại Sao Harness Engineering Quan Trọng?](#4-tại-sao-harness-engineering-quan-trọng)
    - [4.1. Tối Ưu Hiệu Suất Vượt Trội](#41-tối-ưu-hiệu-suất-vượt-trội)
    - [4.2. Mô Hình Chỉ Là Công Cụ, Hệ Thống Mới Quyết Định Kết Quả](#42-mô-hình-chỉ-là-công-cụ-hệ-thống-mới-quyết-định-kết-quả)
    - [4.3. Kiểm Soát và Đáng Tin Cậy](#43-kiểm-soát-và-đáng-tin-cậy)
    - [4.4. Scalability và Maintainability](#44-scalability-và-maintainability)
    - [4.5. Giảm Chi Phí Đáng Kể](#45-giảm-chi-phí-đáng-kể)
    - [4.6. Competitive Advantage](#46-competitive-advantage)
    - [Tóm Lại](#tóm-lại)
  - [5. Các Thành Phần Cốt Lõi Của Harness](#5-các-thành-phần-cốt-lõi-của-harness)
    - [5.1. Tools (Công Cụ) - "Tay Chân"](#51-tools-công-cụ---tay-chân)
    - [5.2. Memory (Bộ Nhớ) - "Não"](#52-memory-bộ-nhớ---não)
    - [5.3. Context Management (Quản Lý Ngữ Cảnh) - "Hệ Tuần Hoàn"](#53-context-management-quản-lý-ngữ-cảnh---hệ-tuần-hoàn)
    - [5.4. Guardrails (Rào Cản) - "Hệ Miễn Dịch"](#54-guardrails-rào-cản---hệ-miễn-dịch)
    - [5.5. Feedback Loops (Vòng Lặp Phản Hồi) - "Cảm Giác"](#55-feedback-loops-vòng-lặp-phản-hồi---cảm-giác)
    - [5.6. Permissions (Quyền Hạn) - "Xương"](#56-permissions-quyền-hạn---xương)
    - [5.7. Orchestration (Điều Phối) - "Hệ Thần Kinh"](#57-orchestration-điều-phối---hệ-thần-kinh)
    - [Tích Hợp Tất Cả](#tích-hợp-tất-cả)
  - [6. Case Studies Thực Tế](#6-case-studies-thực-tế)
    - [6.1. SWE-agent (Princeton NLP)](#61-swe-agent-princeton-nlp)
    - [6.2. Anthropic Multi-Agent Architecture](#62-anthropic-multi-agent-architecture)
    - [6.3. Claude Code Leak - Hệ Thống Harness Siêu Đẳng](#63-claude-code-leak---hệ-thống-harness-siêu-đẳng)
      - [A. Quản lý Context 5 Cấp Độ](#a-quản-lý-context-5-cấp-độ)
      - [B. Bộ Nhớ 3 Tầng với Auto-Optimization](#b-bộ-nhớ-3-tầng-với-auto-optimization)
      - [C. Phân Quyền Tool Chặt Chẽ](#c-phân-quyền-tool-chặt-chẽ)
      - [D. Tone Detection với Regex (!)](#d-tone-detection-với-regex-)
    - [6.4. Cursor IDE - Harness Tối Ưu Cho Coding](#64-cursor-ide---harness-tối-ưu-cho-coding)
    - [Bài Học Chung Từ Các Case Studies](#bài-học-chung-từ-các-case-studies)
  - [7. Nguyên Tắc Thiết Kế Harness](#7-nguyên-tắc-thiết-kế-harness)
    - [7.1. Nguyên Tắc SOLID Cho Harness](#71-nguyên-tắc-solid-cho-harness)
    - [7.2. The 10 Commandments of Harness Engineering](#72-the-10-commandments-of-harness-engineering)
    - [7.3. The Harness Design Pattern](#73-the-harness-design-pattern)
  - [8. Best Practices](#8-best-practices)
    - [8.1. Tool Design](#81-tool-design)
    - [8.2. Memory Management](#82-memory-management)
    - [8.3. Context Building](#83-context-building)
    - [8.4. Guardrails](#84-guardrails)
    - [8.5. Testing Harness](#85-testing-harness)
  - [9. Công Cụ và Framework](#9-công-cụ-và-framework)
    - [9.1. Frameworks Phổ Biến](#91-frameworks-phổ-biến)
    - [9.2. Công Cụ Hỗ Trợ](#92-công-cụ-hỗ-trợ)
    - [9.3. Starter Template](#93-starter-template)
  - [10. Tương Lai Của Harness Engineering](#10-tương-lai-của-harness-engineering)
    - [10.1. Xu Hướng 2026-2028](#101-xu-hướng-2026-2028)
    - [10.2. Thách Thức Phía Trước](#102-thách-thức-phía-trước)
    - [10.3. Lời Khuyên Cho Tương Lai](#103-lời-khuyên-cho-tương-lai)
  - [11. Tài Liệu Tham Khảo](#11-tài-liệu-tham-khảo)
    - [Papers \& Research](#papers--research)
    - [Frameworks \& Tools](#frameworks--tools)
    - [Communities](#communities)
    - [Blogs \& Resources](#blogs--resources)
    - [Courses \& Tutorials](#courses--tutorials)

---

## 1. Giới Thiệu

Trong kỷ nguyên AI, chúng ta đã chứng kiến sự phát triển vượt bậc của các mô hình ngôn ngữ lớn (LLMs). Từ GPT-3, GPT-4, Claude, đến Gemini, các mô hình này ngày càng thông minh và mạnh mẽ hơn. Tuy nhiên, một câu hỏi quan trọng được đặt ra: **Tại sao với cùng một mô hình AI, một số ứng dụng hoạt động xuất sắc trong khi những ứng dụng khác lại thất bại?**

Câu trả lời nằm ở **Harness Engineering** - một kỹ thuật không tập trung vào việc cải thiện mô hình AI, mà thay vào đó tập trung vào việc xây dựng toàn bộ **hệ sinh thái xung quanh mô hình đó**: công cụ (tools), quyền truy cập (permissions), bộ nhớ (memory), vòng lặp phản hồi (feedback loops), rào cản bảo vệ (guardrails), và quản lý ngữ cảnh (context management).

### Bối Cảnh

Khi Mitchell Hashimoto (người sáng lập HashiCorp, tác giả của Terraform) tuyên bố rằng *"Prompt Engineering đã chết"* và giới thiệu khái niệm **Harness Engineering**, ông đã mở ra một chương mới trong cách chúng ta nghĩ về phát triển ứng dụng AI. Thay vì lo lắng về cách viết prompt hoàn hảo, chúng ta cần tập trung vào việc xây dựng một hệ thống hoàn chỉnh để AI có thể hoạt động hiệu quả.

### Mục Đích Của Tài Liệu

Tài liệu này cung cấp một cái nhìn toàn diện về Harness Engineering, bao gồm:
- Định nghĩa và triết lý cốt lõi
- So sánh với các kỹ thuật trước đó (Prompt Engineering, Context Engineering)
- Các thành phần và nguyên tắc thiết kế
- Case studies từ các công ty hàng đầu (Princeton NLP, Anthropic)
- Best practices và hướng dẫn triển khai thực tế

---

## 2. Harness Engineering Là Gì?

### Định Nghĩa

**Harness Engineering** là kỹ thuật xây dựng và thiết kế toàn bộ **"môi trường xung quanh"** một mô hình AI (bao gồm công cụ, quyền truy cập, bộ nhớ, vòng lặp phản hồi, rào cản bảo vệ, quản lý context...) thay vì tập trung vào việc cải thiện bản thân mô hình đó.

Nói một cách đơn giản:
- **Mô hình AI** là thứ **suy nghĩ**
- **Harness** là thứ mà AI **suy nghĩ về**
- Và chính **Harness** mới quyết định **chất lượng đầu ra cuối cùng**

### Triết Lý Cốt Lõi

> *"Mỗi khi agent mắc lỗi, bạn không cầu nguyện nó làm tốt hơn lần sau – bạn xây dựng một giải pháp hệ thống để nó không bao giờ mắc lỗi đó nữa"*

Triết lý này nhấn mạnh vào:

1. **Systematic Solutions over Prayers**: Thay vì hy vọng AI sẽ "học" từ lỗi, bạn xây dựng cơ chế hệ thống ngăn chặn lỗi đó tái diễn.

2. **Infrastructure over Instructions**: Thay vì viết prompt dài dòng hướng dẫn AI làm gì, bạn xây dựng cơ sở hạ tầng hạn chế những gì AI có thể làm.

3. **Design over Training**: Thay vì fine-tune mô hình, bạn thiết kế môi trường hoạt động của mô hình.

### Ví Dụ Minh Họa

Hãy tưởng tượng bạn có một AI coding assistant:

**❌ Cách tiếp cận cũ (Prompt Engineering):**
```
"Hãy viết code Python. Đảm bảo code của bạn không có lỗi cú pháp. 
Kiểm tra kỹ trước khi trả về. Nếu có lỗi, hãy sửa..."
```

**✅ Cách tiếp cận mới (Harness Engineering):**
```javascript
// Tích hợp linter vào harness
harness.addTool({
  name: "write_code",
  execute: async (code) => {
    // Tự động check syntax
    const lintResult = await linter.check(code);
    if (lintResult.hasErrors) {
      // Tự động reject và yêu cầu sửa
      return { status: "error", errors: lintResult.errors };
    }
    return { status: "success", code };
  }
});
```

Trong ví dụ trên, thay vì "nhờ" AI cẩn thận, bạn xây dựng một hệ thống **không cho phép** AI trả về code có lỗi.

### So Sánh Với Các Khái Niệm Khác

| Khía cạnh | Prompt Engineering | Context Engineering | Harness Engineering |
|-----------|-------------------|---------------------|---------------------|
| **Focus** | Cách hỏi | Dữ liệu đầu vào | Toàn bộ hệ thống |
| **Mục tiêu** | Câu prompt tốt hơn | Context phù hợp hơn | Môi trường tốt hơn |
| **Ví dụ** | Viết email | Đính kèm file | Thiết kế văn phòng + quy trình |
| **Kiểm soát** | Thấp | Trung bình | Cao |
| **Tính bền vững** | Thấp | Trung bình | Cao |

---

## 3. Ba Giai Đoạn Tiến Hóa Của Kỹ Nghệ AI

Để hiểu rõ hơn về Harness Engineering, chúng ta cần nhìn lại quá trình tiến hóa của kỹ nghệ AI qua ba giai đoạn chính:

### 3.1. Prompt Engineering (2022-2024)

**Focus**: *"Hỏi AI thế nào cho đúng?"*

**Đặc điểm:**
- Tập trung vào việc viết câu prompt tốt
- Sử dụng các kỹ thuật: few-shot learning, chain-of-thought, role-playing
- Phụ thuộc nhiều vào "nghệ thuật" viết prompt

**Ví dụ:**
```
User: "Act as a senior Python developer. Write a function to..."
User: "Think step by step..."
User: "Here are some examples: ..."
```

**Hạn chế:**
- Khó mở rộng và duy trì
- Kết quả không ổn định
- Khó debug khi có lỗi
- Phụ thuộc quá nhiều vào cách diễn đạt

**So sánh:** Giống như viết email - bạn lo lắng về từng từ, từng câu.

---

### 3.2. Context Engineering (2025)

**Focus**: *"Đưa thông tin gì để AI trả lời tốt?"*

**Đặc điểm:**
- Tập trung vào việc cung cấp đúng dữ liệu đầu vào
- Sử dụng RAG (Retrieval Augmented Generation)
- Quản lý context window hiệu quả
- Tích hợp knowledge bases

**Ví dụ:**
```javascript
// Retrieve relevant context
const relevantDocs = await vectorDB.search(query);
const context = relevantDocs.map(doc => doc.content).join('\n');

// Add to prompt
const prompt = `Context: ${context}\n\nQuestion: ${query}`;
```

**Ưu điểm:**
- Cải thiện độ chính xác
- Giảm hallucination
- Có thể scale với dữ liệu lớn

**Hạn chế:**
- Vẫn phụ thuộc vào chất lượng context
- Chưa kiểm soát được hành vi của AI
- Khó xử lý các tình huống phức tạp

**So sánh:** Giống như đính kèm đúng file vào email - bạn cung cấp đủ thông tin cần thiết.

---

### 3.3. Harness Engineering (2026+)

**Focus**: *"Toàn bộ hệ thống quanh AI vận hành ra sao?"*

**Đặc điểm:**
- Thiết kế toàn bộ môi trường hoạt động
- Tích hợp tools, memory, guardrails
- Kiểm soát chặt chẽ hành vi của AI
- Tự động xử lý lỗi và tối ưu hóa

**Ví dụ:**
```javascript
const harness = new AIHarness({
  model: "claude-3-opus",
  tools: [codeExecutor, fileManager, webSearch],
  memory: {
    shortTerm: conversationHistory,
    longTerm: vectorStore,
    workingMemory: currentContext
  },
  guardrails: {
    maxTokens: 100000,
    allowedDomains: ["github.com", "stackoverflow.com"],
    securityRules: [noSensitiveData, validateOutputs]
  },
  feedback: {
    onError: autoRetry,
    onSuccess: logMetrics
  }
});
```

**Ưu điểm:**
- Kiểm soát hoàn toàn hành vi AI
- Dễ debug và maintain
- Có thể mở rộng và tái sử dụng
- Kết quả ổn định và đáng tin cậy

**So sánh:** Giống như thiết kế cả văn phòng và quy trình làm việc - bạn tạo ra một hệ sinh thái hoàn chỉnh.

---

### So Sánh Tổng Quan

```
┌─────────────────────────────────────────────────────────────┐
│                    TIẾN HÓA KỸ NGHỆ AI                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  2022-2024: PROMPT ENGINEERING                              │
│  ┌─────────────────────────────────┐                       │
│  │ "Làm sao hỏi cho đúng?"         │                       │
│  │ Focus: Câu prompt                │                       │
│  │ Control: ⭐ Thấp                 │                       │
│  └─────────────────────────────────┘                       │
│                    │                                        │
│                    ▼                                        │
│  2025: CONTEXT ENGINEERING                                  │
│  ┌─────────────────────────────────┐                       │
│  │ "Đưa thông tin gì?"             │                       │
│  │ Focus: Dữ liệu đầu vào          │                       │
│  │ Control: ⭐⭐ Trung bình         │                       │
│  └─────────────────────────────────┘                       │
│                    │                                        │
│                    ▼                                        │
│  2026+: HARNESS ENGINEERING                                 │
│  ┌─────────────────────────────────┐                       │
│  │ "Hệ thống hoạt động ra sao?"   │                       │
│  │ Focus: Toàn bộ môi trường       │                       │
│  │ Control: ⭐⭐⭐⭐⭐ Cao          │                       │
│  └─────────────────────────────────┘                       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Insight Quan Trọng

> **"Prompt Engineering chưa chết, nhưng nó không còn đủ"**

Mỗi giai đoạn không thay thế hoàn toàn giai đoạn trước, mà bổ sung thêm một layer kiểm soát:
- Bạn vẫn cần **prompt tốt** (Prompt Engineering)
- Bạn vẫn cần **context phù hợp** (Context Engineering)
- Nhưng bạn cần thêm **hệ thống hoàn chỉnh** (Harness Engineering)

---

## 4. Tại Sao Harness Engineering Quan Trọng?

### 4.1. Tối Ưu Hiệu Suất Vượt Trội

**Nghiên cứu từ Princeton NLP** (paper SWE-agent) đã chứng minh một phát hiện đột phá:

> Chỉ cần thay đổi cách thiết kế **"giao diện/môi trường"** (ACI - Agent-Computer Interface), hiệu suất của AI có thể **tăng tới 64%** mà không cần nâng cấp mô hình.

**Kết quả thực nghiệm:**

| Cách tiếp cận | Success Rate | Improvement |
|---------------|--------------|-------------|
| Baseline (no harness) | 12.5% | - |
| With optimized harness | 20.5% | +64% |

**Điều này có nghĩa là gì?**
- Harness design có thể quan trọng hơn cả việc chọn mô hình AI
- ROI cao hơn so với việc train/fine-tune mô hình mới
- Có thể áp dụng ngay lập tức mà không cần tài nguyên lớn

---

### 4.2. Mô Hình Chỉ Là Công Cụ, Hệ Thống Mới Quyết Định Kết Quả

**Thực tế của thị trường AI:**

```
┌──────────────────────────────────────────────────────┐
│  COMMODITIZATION OF AI MODELS                        │
├──────────────────────────────────────────────────────┤
│                                                      │
│  2023: GPT-4 là "vua"                                │
│  2024: Claude 3, Gemini Ultra cạnh tranh             │
│  2025: Nhiều mô hình tương đương nhau                │
│  2026: Mô hình trở thành "hàng hóa" (commodity)      │
│                                                      │
│  ➜ Sự khác biệt không còn ở mô hình                 │
│  ➜ Sự khác biệt nằm ở HARNESS                       │
│                                                      │
└──────────────────────────────────────────────────────┘
```

**Ví dụ thực tế:**

Cùng một mô hình Claude 3.5 Sonnet:
- **Cursor IDE**: Rất hiệu quả trong coding
- **Random chatbot**: Không tốt lắm

➜ **Điều khác biệt?** Harness design của Cursor!

---

### 4.3. Kiểm Soát và Đáng Tin Cậy

**Vấn đề với approach cũ:**

```javascript
// ❌ Không thể kiểm soát
const response = await ai.chat("Fix this bug...");
// Bạn không biết AI sẽ làm gì
// Bạn không thể đảm bảo output
// Bạn khó debug khi có lỗi
```

**Với Harness Engineering:**

```javascript
// ✅ Kiểm soát hoàn toàn
const harness = new AIHarness({
  tools: {
    'file_edit': {
      validator: (path) => path.startsWith('/safe/dir'),
      maxSize: 10000,
      allowedExtensions: ['.js', '.ts', '.py']
    }
  },
  outputValidation: {
    checkSyntax: true,
    checkSecurity: true,
    runTests: true
  }
});

// Bạn biết chính xác AI có thể làm gì
// Bạn đảm bảo output luôn valid
// Bạn có logs chi tiết để debug
```

---

### 4.4. Scalability và Maintainability

**Kịch bản thực tế:**

Bạn có 10 AI agents trong hệ thống. Bạn phát hiện một lỗi bảo mật:

**❌ Không có Harness:**
```
- Phải update 10 prompts khác nhau
- Phải test lại 10 agents
- Không chắc đã fix hết chưa
- Mất 2-3 ngày
```

**✅ Có Harness:**
```javascript
// Fix tại 1 nơi trong harness
harness.addSecurityRule({
  name: 'no-sensitive-data',
  validate: (output) => !containsSensitiveData(output)
});

// Tất cả agents tự động có rule này
// Test 1 lần
// Deploy trong vài phút
```

---

### 4.5. Giảm Chi Phí Đáng Kể

**Cost Savings từ Harness Engineering:**

1. **Giảm token usage**
   - Context compression
   - Smart caching
   - Efficient tool calling
   - **Tiết kiệm: 40-60% cost**

2. **Giảm error rate**
   - Automatic validation
   - Built-in guardrails
   - **Giảm: 70-80% failed requests**

3. **Tăng development speed**
   - Reusable components
   - Clear architecture
   - **Tăng: 3-5x faster development**

**Ví dụ tính toán:**

```
Dự án AI Agent truyền thống:
- API cost: $2,000/month
- Development time: 3 months
- Error handling: 40 hours/month
Total: ~$30,000 (3 months)

Với Harness Engineering:
- API cost: $800/month (giảm 60%)
- Development time: 1 month (reuse harness)
- Error handling: 5 hours/month (tự động)
Total: ~$12,000 (3 months)

➜ Savings: $18,000 (60%)
```

---

### 4.6. Competitive Advantage

Khi các mô hình AI trở nên tương đương nhau, **Harness Engineering chính là competitive moat** (lợi thế cạnh tranh):

- **Anthropic**: Harness siêu đẳng (theo leak code của Claude)
- **Cursor**: Harness tối ưu cho coding
- **Perplexity**: Harness tốt cho search & research

➜ Đây là lý do tại sao họ dẫn đầu thị trường!

---

### Tóm Lại

Harness Engineering quan trọng vì:

1. ✅ **Tăng hiệu suất 64%+** (theo nghiên cứu Princeton)
2. ✅ **Tạo competitive advantage** khi mô hình trở thành commodity
3. ✅ **Kiểm soát và đáng tin cậy** hơn nhiều
4. ✅ **Scale và maintain dễ dàng**
5. ✅ **Tiết kiệm 40-60% chi phí**
6. ✅ **Tăng tốc development 3-5x**

> **"In the age of AI commoditization, your harness is your moat"**

---

## 5. Các Thành Phần Cốt Lõi Của Harness

Một harness hoàn chỉnh bao gồm 7 thành phần chính. Hãy tưởng tượng chúng như các "cơ quan" trong cơ thể con người:

```
┌─────────────────────────────────────────────────────────┐
│              KIẾN TRÚC HARNESS HOÀN CHỈNH               │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─────────────┐      ┌─────────────┐                 │
│  │   TOOLS     │◄────►│   MEMORY    │                 │
│  │  (Tay chân) │      │ (Bộ não)    │                 │
│  └─────────────┘      └─────────────┘                 │
│         ▲                    ▲                         │
│         │                    │                         │
│         ▼                    ▼                         │
│  ┌─────────────────────────────────┐                  │
│  │      CONTEXT MANAGEMENT         │                  │
│  │      (Hệ thống tuần hoàn)       │                  │
│  └─────────────────────────────────┘                  │
│         ▲                    ▲                         │
│         │                    │                         │
│  ┌─────────────┐      ┌─────────────┐                 │
│  │ GUARDRAILS  │      │  FEEDBACK   │                 │
│  │(Hệ miễn dịch)│      │(Cảm giác)   │                 │
│  └─────────────┘      └─────────────┘                 │
│         ▲                    ▲                         │
│         │                    │                         │
│  ┌─────────────┐      ┌─────────────┐                 │
│  │ PERMISSIONS │      │ ORCHESTRATION│                │
│  │  (Xương)    │      │(Hệ thần kinh)│                │
│  └─────────────┘      └─────────────┘                 │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

### 5.1. Tools (Công Cụ) - "Tay Chân"

**Mục đích**: Định nghĩa những gì AI có thể **làm**

**Ví dụ:**
```typescript
interface Tool {
  name: string;
  description: string;
  parameters: JSONSchema;
  execute: (params: any) => Promise<ToolResult>;
  validation?: {
    preExecution?: (params: any) => boolean;
    postExecution?: (result: any) => boolean;
  };
}

// Example: File Editor Tool
const fileEditorTool: Tool = {
  name: "edit_file",
  description: "Edit a file with specified changes",
  parameters: {
    type: "object",
    properties: {
      path: { type: "string" },
      changes: { type: "array" }
    }
  },
  execute: async (params) => {
    // Implementation
    return { success: true, content: "..." };
  },
  validation: {
    preExecution: (params) => {
      // Check if path is safe
      return params.path.startsWith('/safe/');
    },
    postExecution: (result) => {
      // Validate syntax
      return linter.check(result.content);
    }
  }
};
```

**Best Practices:**
- ✅ Giới hạn scope của mỗi tool (single responsibility)
- ✅ Validation đầu vào và đầu ra
- ✅ Clear error messages
- ✅ Idempotent khi có thể
- ❌ Tránh tools quá mạnh (e.g., "execute_any_command")

---

### 5.2. Memory (Bộ Nhớ) - "Não"

**Mục đích**: Quản lý thông tin AI cần **nhớ**

**3 Loại Memory:**

```typescript
interface MemorySystem {
  // 1. Short-term Memory (Conversation)
  shortTerm: {
    maxMessages: number;
    messages: Message[];
    compress: () => void;
  };
  
  // 2. Long-term Memory (Knowledge)
  longTerm: {
    vectorStore: VectorDB;
    add: (content: string, metadata: any) => void;
    search: (query: string) => Promise<Document[]>;
  };
  
  // 3. Working Memory (Current Task)
  workingMemory: {
    currentTask: Task;
    subTasks: Task[];
    context: Map<string, any>;
  };
}
```

**Ví dụ Implementation:**
```typescript
class HarnessMemory implements MemorySystem {
  shortTerm = {
    maxMessages: 20,
    messages: [],
    compress: () => {
      // Summarize old messages when hitting limit
      if (this.messages.length > this.maxMessages) {
        const old = this.messages.slice(0, -10);
        const summary = this.summarize(old);
        this.messages = [
          { role: 'system', content: summary },
          ...this.messages.slice(-10)
        ];
      }
    }
  };
  
  longTerm = {
    vectorStore: new ChromaDB(),
    add: async (content, metadata) => {
      await this.vectorStore.add({
        content,
        metadata,
        embedding: await this.embed(content)
      });
    },
    search: async (query) => {
      return await this.vectorStore.search(query, { limit: 5 });
    }
  };
  
  workingMemory = {
    currentTask: null,
    subTasks: [],
    context: new Map()
  };
}
```

**Best Practices:**
- ✅ Context compression khi cần
- ✅ Prioritize relevant memories
- ✅ Clear working memory sau task
- ✅ Separate concerns (short/long/working)

---

### 5.3. Context Management (Quản Lý Ngữ Cảnh) - "Hệ Tuần Hoàn"

**Mục đích**: Đảm bảo AI luôn có đúng thông tin tại đúng thời điểm

**5 Cấp Độ Context (theo Anthropic):**

```typescript
interface ContextManager {
  // Level 1: System Instructions
  systemContext: {
    identity: string;
    capabilities: string[];
    limitations: string[];
  };
  
  // Level 2: Task Context
  taskContext: {
    goal: string;
    constraints: string[];
    currentStep: number;
  };
  
  // Level 3: Domain Knowledge
  domainContext: {
    codebase: CodebaseInfo;
    documentation: Document[];
    conventions: Convention[];
  };
  
  // Level 4: Conversation History
  conversationContext: {
    history: Message[];
    summary: string;
  };
  
  // Level 5: Immediate Context
  immediateContext: {
    currentFile: string;
    recentChanges: Change[];
    openFiles: string[];
  };
  
  // Build final context for AI
  buildContext(): string;
}
```

**Context Optimization Strategies:**

```typescript
class SmartContextManager {
  async buildOptimizedContext(query: string): Promise<string> {
    // 1. Always include system & task
    const core = this.getSystemAndTaskContext();
    
    // 2. Retrieve relevant domain knowledge
    const relevant = await this.retrieveRelevant(query);
    
    // 3. Add compressed conversation history
    const history = this.compressHistory();
    
    // 4. Include immediate context
    const immediate = this.getImmediateContext();
    
    // 5. Ensure under token limit
    return this.fitToLimit([core, relevant, history, immediate]);
  }
  
  private fitToLimit(contexts: string[]): string {
    const limit = 100000; // tokens
    let total = '';
    let tokenCount = 0;
    
    for (const ctx of contexts) {
      const tokens = this.countTokens(ctx);
      if (tokenCount + tokens < limit) {
        total += ctx;
        tokenCount += tokens;
      } else {
        // Truncate or skip
        break;
      }
    }
    
    return total;
  }
}
```

---

### 5.4. Guardrails (Rào Cản) - "Hệ Miễn Dịch"

**Mục đích**: Ngăn chặn hành vi không mong muốn

**Các Loại Guardrails:**

```typescript
interface GuardrailSystem {
  // Input Guardrails
  inputGuardrails: {
    contentFilter: (input: string) => ValidationResult;
    promptInjectionDetection: (input: string) => boolean;
    rateLimiting: RateLimiter;
  };
  
  // Output Guardrails
  outputGuardrails: {
    sensitiveDataDetection: (output: string) => boolean;
    toxicityFilter: (output: string) => ToxicityScore;
    factualityCheck: (output: string) => Promise<boolean>;
  };
  
  // Behavioral Guardrails
  behavioralGuardrails: {
    maxIterations: number;
    timeoutMs: number;
    allowedDomains: string[];
    forbiddenActions: string[];
  };
  
  // Security Guardrails
  securityGuardrails: {
    sandboxing: boolean;
    fileAccessControl: AccessControl;
    networkRestrictions: NetworkPolicy;
  };
}
```

**Ví dụ Implementation:**

```typescript
class GuardrailLayer {
  async validateInput(input: string): Promise<ValidationResult> {
    // Check for prompt injection
    if (this.detectPromptInjection(input)) {
      return { valid: false, reason: 'Prompt injection detected' };
    }
    
    // Check for inappropriate content
    const toxicity = await this.checkToxicity(input);
    if (toxicity.score > 0.8) {
      return { valid: false, reason: 'Inappropriate content' };
    }
    
    return { valid: true };
  }
  
  async validateOutput(output: string): Promise<ValidationResult> {
    // Check for sensitive data leaks
    if (this.containsSensitiveData(output)) {
      return { valid: false, reason: 'Sensitive data detected' };
    }
    
    // Check output quality
    if (!this.meetsQualityStandards(output)) {
      return { valid: false, reason: 'Quality check failed' };
    }
    
    return { valid: true };
  }
  
  private detectPromptInjection(input: string): boolean {
    const patterns = [
      /ignore previous instructions/i,
      /system prompt/i,
      /you are now/i,
      // ... more patterns
    ];
    return patterns.some(p => p.test(input));
  }
}
```

---

### 5.5. Feedback Loops (Vòng Lặp Phản Hồi) - "Cảm Giác"

**Mục đích**: Tự động học và cải thiện từ kết quả

```typescript
interface FeedbackSystem {
  // Automatic feedback
  onSuccess: (task: Task, result: any) => void;
  onError: (task: Task, error: Error) => void;
  onTimeout: (task: Task) => void;
  
  // Metrics tracking
  metrics: {
    successRate: number;
    avgResponseTime: number;
    tokenUsage: number;
    errorTypes: Map<string, number>;
  };
  
  // Auto-optimization
  optimizer: {
    analyzePatterns: () => Insights;
    suggestImprovements: () => Suggestion[];
    autoAdjust: (metric: string) => void;
  };
}
```

**Example:**

```typescript
class FeedbackLoop {
  async handleTaskCompletion(task: Task, result: TaskResult) {
    // 1. Log metrics
    await this.logMetrics({
      taskType: task.type,
      duration: result.duration,
      tokensUsed: result.tokensUsed,
      success: result.success
    });
    
    // 2. Analyze for patterns
    if (result.success) {
      await this.memory.longTerm.add(
        `Successful ${task.type}: ${result.approach}`,
        { type: 'success_pattern', task: task.type }
      );
    } else {
      await this.memory.longTerm.add(
        `Failed ${task.type}: ${result.error}`,
        { type: 'failure_pattern', task: task.type }
      );
    }
    
    // 3. Auto-adjust if needed
    if (this.metrics.successRate < 0.7) {
      await this.optimizer.suggestImprovements();
    }
  }
}
```

---

### 5.6. Permissions (Quyền Hạn) - "Xương"

**Mục đích**: Định nghĩa AI được phép làm gì, ở đâu, khi nào

```typescript
interface PermissionSystem {
  // Resource permissions
  resources: {
    files: FilePermissions;
    network: NetworkPermissions;
    system: SystemPermissions;
  };
  
  // Time-based permissions
  temporal: {
    allowedHours: TimeRange[];
    maxDuration: number;
  };
  
  // Context-based permissions
  contextual: {
    requireApproval: (action: Action) => boolean;
    escalationRules: EscalationRule[];
  };
}

interface FilePermissions {
  read: string[];      // Allowed paths to read
  write: string[];     // Allowed paths to write
  delete: string[];    // Allowed paths to delete
  maxFileSize: number; // Max file size in bytes
}
```

---

### 5.7. Orchestration (Điều Phối) - "Hệ Thần Kinh"

**Mục đích**: Điều phối tất cả các thành phần hoạt động hài hòa

```typescript
class HarnessOrchestrator {
  async execute(userRequest: string): Promise<Result> {
    // 1. Validate input
    const inputCheck = await this.guardrails.validateInput(userRequest);
    if (!inputCheck.valid) {
      return { error: inputCheck.reason };
    }
    
    // 2. Build context
    const context = await this.contextManager.buildContext(userRequest);
    
    // 3. Check permissions
    const action = await this.planner.plan(userRequest);
    if (!this.permissions.allows(action)) {
      return { error: 'Permission denied' };
    }
    
    // 4. Execute with tools
    const result = await this.agent.execute(action, context);
    
    // 5. Validate output
    const outputCheck = await this.guardrails.validateOutput(result);
    if (!outputCheck.valid) {
      return await this.retry(action); // Retry logic
    }
    
    // 6. Update memory & feedback
    await this.memory.update(result);
    await this.feedback.log(action, result);
    
    return result;
  }
}
```

---

### Tích Hợp Tất Cả

```typescript
// Complete Harness Example
const harness = new AIHarness({
  model: "claude-3.5-sonnet",
  
  tools: [fileEditor, codeRunner, webSearch],
  
  memory: {
    shortTerm: { maxMessages: 20 },
    longTerm: { vectorStore: chromaDB },
    workingMemory: { maxSize: 10000 }
  },
  
  contextManager: {
    levels: 5,
    optimization: "smart",
    tokenLimit: 100000
  },
  
  guardrails: {
    input: [promptInjectionFilter, toxicityFilter],
    output: [sensitiveDataFilter, qualityChecker],
    behavioral: { maxIterations: 10, timeoutMs: 30000 }
  },
  
  feedback: {
    autoLog: true,
    metricsTracking: true,
    autoOptimize: true
  },
  
  permissions: {
    files: {
      read: ["/project/**"],
      write: ["/project/src/**"],
      delete: []
    },
    network: {
      allowedDomains: ["github.com", "stackoverflow.com"]
    }
  }
});
```

---

## 6. Case Studies Thực Tế

Các case studies sau đây cho thấy cách các tổ chức hàng đầu áp dụng Harness Engineering để đạt được kết quả vượt trội.

---

### 6.1. SWE-agent (Princeton NLP)

**Bối cảnh**: Princeton NLP phát triển một AI agent để tự động sửa lỗi GitHub issues.

**Vấn đề ban đầu**:
- Success rate chỉ 12.5% với baseline approach
- Agent thường "lạc lối" trong codebase
- Kết quả tìm kiếm quá nhiều, gây overwhelm
- Không biết đâu là thông tin quan trọng

**Giải pháp Harness Engineering**:

```typescript
// 1. GIỚI HẠN OUTPUT CỦA TOOLS
const searchTool = {
  name: "search_file",
  execute: async (pattern: string) => {
    const results = await grep(pattern);
    // ❌ Trước: Trả về toàn bộ
    // ✅ Sau: Giới hạn 50 kết quả đầu
    return results.slice(0, 50);
  }
};

// 2. VIEWER THÔNG MINH
const fileViewer = {
  name: "view_file",
  context: {
    showLineNumbers: true,
    contextLines: 100,  // Chỉ hiện 100 dòng tại 1 thời điểm
    highlightRelevant: true
  },
  execute: async (file: string, startLine: number) => {
    // Thay vì load toàn bộ file (có thể 1000+ dòng)
    // Chỉ load 100 dòng xung quanh vùng quan tâm
    return loadFileWindow(file, startLine, 100);
  }
};

// 3. TÍCH HỢP LINTER REALTIME
const editTool = {
  name: "edit_file",
  execute: async (file: string, changes: Change[]) => {
    const newContent = applyChanges(file, changes);
    
    // Tự động check syntax ngay lập tức
    const lintResult = await linter.check(newContent);
    
    if (lintResult.errors.length > 0) {
      // Không cho phép save file có lỗi
      return {
        success: false,
        errors: lintResult.errors,
        message: "Please fix syntax errors before proceeding"
      };
    }
    
    await saveFile(file, newContent);
    return { success: true };
  }
};

// 4. NÉN LỊCH SỬ CHAT
const memoryManager = {
  compressHistory: async () => {
    if (history.length > 20) {
      // Summarize old conversations
      const old = history.slice(0, -10);
      const summary = await summarize(old);
      history = [
        { role: 'system', content: summary },
        ...history.slice(-10)
      ];
    }
  }
};
```

**Kết quả**:
- Success rate tăng từ **12.5% → 20.5%** (+64%)
- Thời gian giải quyết issue giảm 40%
- Token usage giảm 30%

**Key Insights**:
> "Không phải mô hình yếu, mà môi trường không được thiết kế tốt"

---

### 6.2. Anthropic Multi-Agent Architecture

**Bối cảnh**: Anthropic phát triển Claude Code để tạo các ứng dụng phức tạp (games, DAW, etc.)

**Vấn đề ban đầu**:
- Single agent làm quá nhiều việc cùng lúc → overwhelmed
- Agent dừng lại quá sớm (chưa hoàn thành)
- Hoặc tiếp tục quá lâu (không biết khi nào dừng)

**Giải pháp: Multi-Agent Orchestration**

```typescript
// Kiến trúc 3 Agent
class MultiAgentHarness {
  agents = {
    planner: new PlannerAgent(),
    generator: new GeneratorAgent(),
    evaluator: new EvaluatorAgent()
  };
  
  async execute(task: string): Promise<Result> {
    // 1. PLANNER: Lập kế hoạch
    const plan = await this.agents.planner.createPlan(task);
    /*
    Output example:
    {
      steps: [
        "1. Create game canvas",
        "2. Implement player movement",
        "3. Add collision detection",
        "4. Implement scoring system"
      ],
      estimatedComplexity: "medium"
    }
    */
    
    // 2. GENERATOR: Thực thi từng bước
    const results = [];
    for (const step of plan.steps) {
      const result = await this.agents.generator.generate(step);
      results.push(result);
      
      // 3. EVALUATOR: Đánh giá sau mỗi bước
      const evaluation = await this.agents.evaluator.evaluate(result, step);
      
      if (!evaluation.passed) {
        // Retry with feedback
        const retry = await this.agents.generator.generate(
          step,
          { previousAttempt: result, feedback: evaluation.feedback }
        );
        results[results.length - 1] = retry;
      }
    }
    
    // 4. FINAL EVALUATION
    const finalEval = await this.agents.evaluator.evaluateFinal(results);
    
    return {
      success: finalEval.passed,
      output: results,
      quality: finalEval.quality
    };
  }
}
```

**Đặc điểm của từng Agent**:

```typescript
// PLANNER AGENT
class PlannerAgent {
  systemPrompt = `
    You are a planning specialist.
    Break down complex tasks into clear, actionable steps.
    Consider dependencies and order.
    Output: JSON with steps array.
  `;
  
  tools = []; // No tools, pure planning
}

// GENERATOR AGENT
class GeneratorAgent {
  systemPrompt = `
    You are a code generator.
    Implement exactly what is asked in the step.
    Use provided tools to write and test code.
  `;
  
  tools = [
    writeFile,
    readFile,
    executeCode,
    installPackage
  ];
}

// EVALUATOR AGENT
class EvaluatorAgent {
  systemPrompt = `
    You are a quality evaluator.
    Check if the implementation meets requirements.
    Test for bugs, edge cases, and completeness.
    Decide: PASS or FAIL with specific feedback.
  `;
  
  tools = [
    runTests,
    checkSyntax,
    analyzeCode
  ];
}
```

**Kết quả**:
- Có thể tạo các ứng dụng phức tạp: 2D games, DAW (Digital Audio Workstation)
- Success rate cao hơn 80% so với single-agent
- Code quality tốt hơn nhờ evaluation loop

**Key Insights**:
> "Chia để trị - Mỗi agent tập trung vào một nhiệm vụ cụ thể"

---

### 6.3. Claude Code Leak - Hệ Thống Harness Siêu Đẳng

**Bối cảnh**: Vào 2026, source code của Claude Code bị leak vô tình, hé lộ kiến trúc harness cực kỳ tinh vi.

**Phát hiện chính**:

#### A. Quản lý Context 5 Cấp Độ

```typescript
class ClaudeContextManager {
  buildContext(query: string): Context {
    return {
      // Level 1: System Identity
      system: {
        role: "AI Coding Assistant",
        version: "Claude 3.5",
        capabilities: [...],
        limitations: [...]
      },
      
      // Level 2: Task Context
      task: {
        currentGoal: this.getCurrentGoal(),
        progressSoFar: this.getProgress(),
        remainingSteps: this.getRemainingSteps()
      },
      
      // Level 3: Domain Knowledge (Codebase)
      domain: {
        projectStructure: this.getProjectStructure(),
        conventions: this.getCodeConventions(),
        dependencies: this.getDependencies()
      },
      
      // Level 4: Conversation History (Compressed)
      conversation: {
        recentMessages: this.getRecentMessages(10),
        summary: this.getSummary(),
        keyDecisions: this.getKeyDecisions()
      },
      
      // Level 5: Immediate Context
      immediate: {
        currentFile: this.getCurrentFile(),
        openFiles: this.getOpenFiles(),
        recentEdits: this.getRecentEdits(),
        cursorPosition: this.getCursorPosition()
      }
    };
  }
}
```

#### B. Bộ Nhớ 3 Tầng với Auto-Optimization

```typescript
class ClaudeMemorySystem {
  // Tier 1: Hot Memory (RAM)
  hotMemory = {
    currentConversation: [],
    maxSize: 20,
    // Tự động compress khi đầy
    autoCompress: true
  };
  
  // Tier 2: Warm Memory (Vector DB)
  warmMemory = {
    vectorStore: new ChromaDB(),
    recentQueries: new LRUCache(100),
    // Cache các truy vấn phổ biến
    queryCache: new Map()
  };
  
  // Tier 3: Cold Memory (Long-term Storage)
  coldMemory = {
    database: new PostgreSQL(),
    // Lưu trữ patterns, learnings lâu dài
    successPatterns: [],
    failurePatterns: []
  };
  
  // AUTO DREAM: Chạy ngầm để tối ưu memory
  async autoDream() {
    // Chạy trong idle time
    setInterval(async () => {
      // 1. Analyze patterns
      const patterns = await this.analyzePatterns();
      
      // 2. Move rarely used to cold storage
      await this.archiveOldMemories();
      
      // 3. Optimize vector embeddings
      await this.warmMemory.vectorStore.optimize();
      
      // 4. Update query cache
      await this.updateQueryCache();
    }, 60000); // Mỗi phút
  }
}
```

#### C. Phân Quyền Tool Chặt Chẽ

```typescript
class ClaudeToolPermissions {
  // Mỗi tool có permission riêng
  toolPermissions = {
    'read_file': {
      allowedPaths: ['/workspace/**'],
      deniedPaths: ['/workspace/.env', '/workspace/secrets/**'],
      requireApproval: false
    },
    
    'write_file': {
      allowedPaths: ['/workspace/src/**', '/workspace/test/**'],
      deniedPaths: ['/workspace/config/**'],
      requireApproval: true, // Cần user confirm
      maxFileSize: 100000 // 100KB
    },
    
    'execute_command': {
      allowedCommands: ['npm', 'git', 'python', 'node'],
      deniedCommands: ['rm -rf', 'sudo', 'chmod'],
      requireApproval: true,
      timeout: 30000 // 30s
    },
    
    'web_search': {
      allowedDomains: ['github.com', 'stackoverflow.com', 'docs.*'],
      rateLimitperHour: 50
    }
  };
  
  async checkPermission(tool: string, params: any): Promise<PermissionResult> {
    const perm = this.toolPermissions[tool];
    
    // Check path restrictions
    if (tool.includes('file')) {
      if (!this.isPathAllowed(params.path, perm)) {
        return { allowed: false, reason: 'Path not allowed' };
      }
    }
    
    // Check if needs approval
    if (perm.requireApproval) {
      const approved = await this.requestUserApproval(tool, params);
      if (!approved) {
        return { allowed: false, reason: 'User denied' };
      }
    }
    
    return { allowed: true };
  }
}
```

#### D. Tone Detection với Regex (!)

```typescript
class ClaudeToneDetector {
  // Phát hiện sự ức chế/không hài lòng của user
  frustrationPatterns = [
    /why (is|are) (you|this)/i,
    /stop doing/i,
    /i (told|said) you/i,
    /for the (\d+)(st|nd|rd|th) time/i,
    /just do it/i,
    /seriously\?/i
  ];
  
  detectFrustration(message: string): boolean {
    return this.frustrationPatterns.some(p => p.test(message));
  }
  
  adjustTone(message: string): ToneAdjustment {
    if (this.detectFrustration(message)) {
      return {
        tone: 'apologetic',
        verbosity: 'concise',
        explanation: 'minimal',
        action: 'immediate' // Làm ngay, giải thích sau
      };
    }
    
    return {
      tone: 'helpful',
      verbosity: 'balanced',
      explanation: 'detailed',
      action: 'thoughtful'
    };
  }
}
```

**Kết quả**:
- Claude Code trở thành một trong những AI coding assistant tốt nhất
- User satisfaction rate > 90%
- Retention rate cao

**Key Insights**:
> "Devil is in the details - Mỗi chi tiết nhỏ trong harness đều được optimize kỹ lưỡng"

---

### 6.4. Cursor IDE - Harness Tối Ưu Cho Coding

**Đặc điểm nổi bật**:

```typescript
class CursorHarness {
  // 1. CONTEXT AWARE - Biết bạn đang làm gì
  async buildContext(): Promise<Context> {
    return {
      // File đang mở
      currentFile: editor.getCurrentFile(),
      
      // Code đang select
      selectedCode: editor.getSelection(),
      
      // Cursor position
      cursorLine: editor.getCursorLine(),
      
      // Recent edits (để hiểu intent)
      recentEdits: this.getRecentEdits(5),
      
      // Related files (import/export)
      relatedFiles: await this.findRelatedFiles(),
      
      // Git context
      gitBranch: await git.getCurrentBranch(),
      uncommittedChanges: await git.getUncommittedChanges()
    };
  }
  
  // 2. SMART SUGGESTIONS
  async getSuggestions(context: Context): Promise<Suggestion[]> {
    // Dựa trên context, đưa ra suggestions phù hợp
    const suggestions = [];
    
    if (context.selectedCode) {
      suggestions.push({
        type: 'refactor',
        action: 'Refactor this code',
        confidence: 0.9
      });
      
      suggestions.push({
        type: 'explain',
        action: 'Explain this code',
        confidence: 0.95
      });
    }
    
    if (context.cursorLine.includes('TODO')) {
      suggestions.push({
        type: 'implement',
        action: 'Implement this TODO',
        confidence: 0.85
      });
    }
    
    return suggestions.sort((a, b) => b.confidence - a.confidence);
  }
  
  // 3. INCREMENTAL EDITS
  async applyEdit(edit: Edit): Promise<void> {
    // Không replace toàn bộ file
    // Chỉ thay đổi phần cần thiết
    await editor.replaceRange(
      edit.startLine,
      edit.endLine,
      edit.newContent
    );
    
    // Auto format
    await editor.formatDocument();
    
    // Run linter
    const lintResult = await linter.check();
    if (lintResult.errors.length > 0) {
      // Show inline errors
      editor.showErrors(lintResult.errors);
    }
  }
}
```

**Kết quả**:
- Trở thành coding assistant được yêu thích nhất
- NPS (Net Promoter Score) > 70
- Nhiều developers chuyển từ GitHub Copilot sang Cursor

---

### Bài Học Chung Từ Các Case Studies

| Lesson | SWE-agent | Anthropic | Claude Leak | Cursor |
|--------|-----------|-----------|-------------|--------|
| **Giới hạn output** | ✅ 50 results max | - | - | - |
| **Multi-agent** | - | ✅ 3 agents | ✅ Planner/Generator | - |
| **Context layers** | ✅ 3 levels | - | ✅ 5 levels | ✅ Smart context |
| **Memory tiers** | ✅ Compression | - | ✅ 3 tiers | - |
| **Tool permissions** | - | - | ✅ Strict | ✅ Sandbox |
| **User feedback** | - | ✅ Evaluation | ✅ Tone detection | ✅ Suggestions |
| **Incremental** | - | - | - | ✅ Smart edits |

**Nguyên tắc chung**:
1. ✅ **Constraints enable creativity** - Giới hạn giúp AI tập trung
2. ✅ **Layered architecture** - Chia nhỏ vấn đề
3. ✅ **Feedback loops** - Học từ kết quả
4. ✅ **User-centric design** - Luôn nghĩ về UX
5. ✅ **Obsess over details** - Mọi chi tiết đều quan trọng

---

## 7. Nguyên Tắc Thiết Kế Harness

### 7.1. Nguyên Tắc SOLID Cho Harness

**1. Single Responsibility**
- Mỗi tool chỉ làm một việc duy nhất
- Mỗi component có một lý do để thay đổi

**2. Open/Closed**
- Harness mở cho mở rộng (thêm tools, guardrails)
- Đóng cho sửa đổi (core logic ổn định)

**3. Liskov Substitution**
- Tools có thể thay thế cho nhau
- Cùng interface, khác implementation

**4. Interface Segregation**
- Không ép AI sử dụng tools không cần thiết
- Chỉ expose những gì cần

**5. Dependency Inversion**
- Depend on abstractions, not implementations
- Dễ dàng swap components

---

### 7.2. The 10 Commandments of Harness Engineering

```
1. Thou shall LIMIT, not GUIDE
   → Giới hạn những gì AI có thể làm, đừng chỉ hướng dẫn

2. Thou shall VALIDATE, not HOPE
   → Validation mọi input/output, đừng hy vọng AI làm đúng

3. Thou shall DESIGN, not TRAIN
   → Thiết kế môi trường, đừng chỉ train mô hình

4. Thou shall CONSTRAIN, not INSTRUCT
   → Tạo constraints, đừng viết instructions dài dòng

5. Thou shall AUTOMATE, not MANUAL
   → Tự động hóa checks, đừng để user phải check

6. Thou shall FAIL FAST, not SLOW
   → Phát hiện lỗi sớm, đừng để lỗi lan rộng

7. Thou shall LOG EVERYTHING
   → Log mọi action để debug và improve

8. Thou shall ITERATE, not PERFECT
   → Ship và improve, đừng chờ perfect

9. Thou shall MEASURE, not GUESS
   → Measure metrics, đừng đoán

10. Thou shall OBSESS OVER UX
    → User experience là ưu tiên số 1
```

---

### 7.3. The Harness Design Pattern

```typescript
// Template for designing any harness
class HarnessDesignPattern {
  // 1. Define what AI CAN do
  defineCapabilities() {
    return {
      tools: [...],
      permissions: {...}
    };
  }
  
  // 2. Define what AI CANNOT do
  defineConstraints() {
    return {
      guardrails: [...],
      validators: [...]
    };
  }
  
  // 3. Define what AI SHOULD remember
  defineMemory() {
    return {
      shortTerm: {...},
      longTerm: {...},
      working: {...}
    };
  }
  
  // 4. Define how AI SHOULD learn
  defineFeedback() {
    return {
      onSuccess: () => {},
      onError: () => {},
      metrics: [...]
    };
  }
  
  // 5. Define how components WORK TOGETHER
  defineOrchestration() {
    return {
      workflow: [...],
      errorHandling: {...}
    };
  }
}
```

---

## 8. Best Practices

### 8.1. Tool Design

✅ **DO:**
- Keep tools small and focused
- Validate inputs và outputs
- Provide clear error messages
- Make tools idempotent when possible
- Include examples in descriptions

❌ **DON'T:**
- Create "god tools" that do everything
- Trust AI to validate itself
- Use vague error messages
- Allow destructive operations without confirmation

### 8.2. Memory Management

✅ **DO:**
- Compress context regularly
- Prioritize relevant information
- Clear working memory after tasks
- Use tiered storage (hot/warm/cold)

❌ **DON'T:**
- Keep entire conversation history
- Load unnecessary data
- Mix different types of memory
- Forget to clean up

### 8.3. Context Building

✅ **DO:**
- Layer context by priority
- Include only relevant information
- Monitor token usage
- Compress when approaching limits

❌ **DON'T:**
- Include everything
- Ignore token limits
- Use static context
- Forget immediate context

### 8.4. Guardrails

✅ **DO:**
- Validate at multiple layers
- Check both input and output
- Fail fast and clearly
- Log all violations

❌ **DON'T:**
- Rely only on prompts
- Skip validation steps
- Hide errors from users
- Trust without verifying

### 8.5. Testing Harness

```typescript
// Test suite for harness
describe('Harness Tests', () => {
  test('Tool validation works', async () => {
    const result = await harness.executeTool('write_file', {
      path: '/unsafe/path'
    });
    expect(result.error).toBe('Path not allowed');
  });
  
  test('Context limits respected', async () => {
    const context = await harness.buildContext(largeQuery);
    const tokens = countTokens(context);
    expect(tokens).toBeLessThan(100000);
  });
  
  test('Guardrails catch sensitive data', async () => {
    const output = "API_KEY=sk_12345";
    const validation = await harness.validateOutput(output);
    expect(validation.valid).toBe(false);
  });
  
  test('Memory compression works', async () => {
    // Add 50 messages
    for (let i = 0; i < 50; i++) {
      await harness.addMessage({ content: `Message ${i}` });
    }
    expect(harness.memory.shortTerm.messages.length).toBeLessThan(25);
  });
});
```

---

## 9. Công Cụ và Framework

### 9.1. Frameworks Phổ Biến

**LangChain / LangGraph**
```typescript
import { ChatOpenAI } from "@langchain/openai";
import { DynamicStructuredTool } from "@langchain/core/tools";

const harness = {
  llm: new ChatOpenAI({ model: "gpt-4" }),
  tools: [
    new DynamicStructuredTool({
      name: "search",
      description: "Search the web",
      func: async (input) => { /* ... */ }
    })
  ]
};
```

**AutoGen (Microsoft)**
```python
from autogen import AssistantAgent, UserProxyAgent

assistant = AssistantAgent(
    name="assistant",
    llm_config={"model": "gpt-4"},
    system_message="You are a helpful assistant"
)

harness = UserProxyAgent(
    name="harness",
    human_input_mode="NEVER",
    max_consecutive_auto_reply=10,
    code_execution_config={"work_dir": "coding"}
)
```

**CrewAI**
```python
from crewai import Agent, Task, Crew

# Multi-agent harness
planner = Agent(role='Planner', goal='Create plan')
executor = Agent(role='Executor', goal='Execute plan')
reviewer = Agent(role='Reviewer', goal='Review output')

harness = Crew(
    agents=[planner, executor, reviewer],
    tasks=[plan_task, execute_task, review_task]
)
```

---

### 9.2. Công Cụ Hỗ Trợ

**Vector Databases:**
- Pinecone
- Chroma
- Weaviate
- Qdrant

**Monitoring & Logging:**
- LangSmith
- Weights & Biases
- Helicone
- OpenLLMetry

**Guardrails:**
- Guardrails AI
- NeMo Guardrails (NVIDIA)
- LlamaGuard (Meta)

**Testing:**
- PromptFoo
- Deepeval
- Ragas

---

### 9.3. Starter Template

```typescript
// Complete starter template
import { ChatAnthropic } from "@langchain/anthropic";
import { ChromaDB } from "chromadb";

class ProductionHarness {
  constructor(config) {
    this.model = new ChatAnthropic({
      model: "claude-3.5-sonnet",
      temperature: 0
    });
    
    this.tools = this.initializeTools();
    this.memory = this.initializeMemory();
    this.guardrails = this.initializeGuardrails();
    this.logger = this.initializeLogger();
  }
  
  async execute(userInput) {
    try {
      // 1. Validate input
      await this.guardrails.validateInput(userInput);
      
      // 2. Build context
      const context = await this.buildContext(userInput);
      
      // 3. Execute with tools
      const result = await this.model.invoke(context, {
        tools: this.tools
      });
      
      // 4. Validate output
      await this.guardrails.validateOutput(result);
      
      // 5. Log metrics
      await this.logger.log({
        input: userInput,
        output: result,
        tokens: result.usage
      });
      
      return result;
    } catch (error) {
      await this.handleError(error);
      throw error;
    }
  }
}

// Usage
const harness = new ProductionHarness({
  apiKey: process.env.ANTHROPIC_API_KEY
});

const result = await harness.execute("Build me a todo app");
```

---

## 10. Tương Lai Của Harness Engineering

### 10.1. Xu Hướng 2026-2028

**1. Self-Optimizing Harnesses**
- Harness tự động điều chỉnh parameters
- A/B testing tự động
- Continuous learning from usage

**2. Harness-as-a-Service**
- Cloud platforms cung cấp pre-built harnesses
- Plug-and-play harness components
- Marketplace cho harness templates

**3. Visual Harness Builders**
- No-code tools để design harness
- Visual workflow editors
- Drag-and-drop tool composition

**4. Cross-Model Harnesses**
- Một harness chạy trên nhiều models
- Automatic model switching based on task
- Cost-optimized model routing

**5. Regulatory Compliance Harnesses**
- Built-in GDPR, HIPAA compliance
- Audit trails tự động
- Explainability baked in

---

### 10.2. Thách Thức Phía Trước

**Technical Challenges:**
- Standardization across platforms
- Interoperability between harnesses
- Performance optimization at scale
- Security và privacy

**Business Challenges:**
- ROI measurement
- Team skill gaps
- Legacy system integration
- Changing regulations

**Research Challenges:**
- Optimal harness architecture patterns
- Automated harness generation
- Harness testing methodologies
- Cross-domain transferability

---

### 10.3. Lời Khuyên Cho Tương Lai

```
1. Start Simple
   → Đừng over-engineer từ đầu
   → Build MVP harness first

2. Measure Everything
   → Track metrics từ ngày 1
   → Data-driven optimization

3. Stay Updated
   → Field đang phát triển nhanh
   → Follow best practices

4. Share Knowledge
   → Open source harness components
   → Build community

5. Think Long-term
   → Harness là infrastructure
   → Invest in maintainability
```

---

## 11. Tài Liệu Tham Khảo

### Papers & Research

1. **SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering**
   - Princeton NLP Lab
   - https://arxiv.org/abs/2405.15793

2. **The Rise of AI Agents and the Importance of Harness Engineering**
   - Mitchell Hashimoto, HashiCorp
   - https://www.youtube.com/watch?v=example

3. **Building Reliable AI Agents with Guardrails**
   - Anthropic Research
   - https://www.anthropic.com/research

### Frameworks & Tools

1. **LangChain** - https://langchain.com
2. **LangGraph** - https://langchain-ai.github.io/langgraph/
3. **AutoGen** - https://microsoft.github.io/autogen/
4. **CrewAI** - https://www.crewai.com
5. **Guardrails AI** - https://www.guardrailsai.com

### Communities

1. **LangChain Discord** - Active community
2. **r/LangChain** - Reddit discussions
3. **AI Engineers** - Slack community
4. **HuggingFace Forums** - Technical discussions

### Blogs & Resources

1. **Anthropic Blog** - https://www.anthropic.com/news
2. **OpenAI Cookbook** - https://cookbook.openai.com
3. **LangChain Blog** - https://blog.langchain.dev
4. **Mitchell Hashimoto's Blog** - https://mitchellh.com

### Courses & Tutorials

1. **DeepLearning.AI - Building AI Agents**
2. **LangChain Academy**
3. **Anthropic Prompt Engineering**
4. **Microsoft AutoGen Tutorial**

---

**Kết Luận**

Harness Engineering không chỉ là một trend công nghệ - nó là sự chuyển đổi paradigm trong cách chúng ta xây dựng ứng dụng AI. Khi các mô hình AI ngày càng mạnh mẽ và trở thành "hàng hóa đại trà", khả năng thiết kế một harness tốt sẽ là yếu tố quyết định thành công.

> **"The model thinks. The harness shapes what it thinks about. And the harness determines the quality of the final output."**

Hãy bắt đầu từ hôm nay - không cần perfect, chỉ cần start. Build, measure, learn, và iterate. Harness Engineering là journey, không phải destination.

**Happy Building! 🚀**

---

**Ngày cập nhật:** 13/07/2026  
**Tác giả:** AI Knowledge Repository
