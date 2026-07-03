# Hướng Dẫn Setup Local AI IDE (Cho Mac M4 24GB RAM)

Tài liệu này tổng hợp toàn bộ kiến thức và hướng dẫn từng bước để biến VS Code thành một IDE tích hợp AI chạy hoàn toàn dưới máy cá nhân (Local), đảm bảo 100% bảo mật dữ liệu và không phụ thuộc vào các dịch vụ đám mây.

## 1. Tổng Quan Kiến Trúc
Hệ thống Local AI IDE gồm 2 thành phần chính:
- **Backend (Inference Engine):** Sử dụng **Ollama** để chạy các mô hình AI mã nguồn mở.
- **Frontend (IDE):** Sử dụng **VS Code** kết hợp với extension **Continue.dev** làm giao diện giao tiếp (chat, autocomplete).

### Tại sao không nên dùng Docker cho Ollama trên Mac?
<details>
<summary><b>Bấm để xem chi tiết cảnh báo hiệu năng</b></summary>

> **Cảnh báo về hiệu năng:** Docker trên Mac (Apple Silicon) chạy qua máy ảo Linux và hiện tại chưa hỗ trợ truyền trực tiếp GPU (Metal). Nếu chạy Ollama qua Docker, quá trình sinh code sẽ **chỉ dùng CPU**, dẫn đến tốc độ chậm hơn từ 3-5 lần.
> 
> **Best Practice:** Cài đặt Ollama trực tiếp (Native) qua Homebrew để tận dụng tối đa sức mạnh GPU của chip M4. Các thành phần ứng dụng khác (Database, Web Server) vẫn có thể chạy trên Docker bình thường (và kết nối qua `http://host.docker.internal:11434`).
</details>

## 2. Các Model Khuyên Dùng (Dành cho 24GB Unified RAM)
Với 24GB RAM, macOS cho phép cấp phát tối đa khoảng 16-18GB cho VRAM đồ họa.
- **Qwen 2.5 Coder 14B (`qwen2.5-coder:14b`):** 
  - Dung lượng: ~9-10GB RAM (định dạng Q4/Q5).
  - Mục đích: Trò chuyện (Chat), giải thích code, phân tích và viết hàm phức tạp.
  - Ưu điểm: Đủ nhỏ để chạy siêu mượt trên M4, dư RAM để mở trình duyệt, Docker và cấp bộ nhớ ngữ cảnh (Context Window) lớn.
- **DeepSeek Coder V2 Lite 16B (`deepseek-coder-v2:16b`):**
  - Dung lượng: ~10GB RAM (định dạng Q4).
  - Mục đích: Lập trình nâng cao, thuật toán phức tạp (chạy song song hoặc thay thế Qwen).
  - Ưu điểm: Kiến trúc MoE cực mạnh, hỗ trợ nhiều ngôn ngữ lập trình.
- **Qwen 2.5 Coder 7B (`qwen2.5-coder:7b`):**
  - Dung lượng: ~4GB RAM.
  - Mục đích: Tự động hoàn thành code (Tab Autocomplete).
  - Ưu điểm: Phản hồi tức thì ngay khi bạn đang gõ code.
- **Nomic Embed (`nomic-embed-text:latest`):**
  - Dung lượng: ~280MB.
  - Mục đích: Tạo Vector Embeddings hỗ trợ tính năng thấu hiểu toàn bộ codebase (`@codebase`).
  - Ưu điểm: Siêu nhẹ, chạy cực nhanh trên GPU Metal của Mac.

### Bảng so sánh nhanh và khuyến nghị

| Model | Mục đích | Độ thông minh (Code) | Tốc độ trên M4 24GB | Khuyên dùng |
| :--- | :--- | :--- | :--- | :--- |
| **Qwen 2.5 Coder 14B** | Chuyên Code | ⭐⭐⭐⭐ | 🚀🚀🚀🚀 | **Tốt nhất hiện tại** |
| **DeepSeek Coder V2 16B** | Chuyên Code | ⭐⭐⭐⭐⭐ | 🚀🚀🚀 | **Nên trải nghiệm thử** |
| **Qwen 2.5 Coder 7B** | Autocomplete | ⭐⭐⭐ | 🚀🚀🚀🚀🚀 | **Mặc định cho Autocomplete** |
| **Nomic Embed** | Embeddings | N/A | 🚀🚀🚀🚀🚀 | **Bắt buộc nếu dùng `@codebase`** |
| **Llama 3.1 8B** | Chat Đa Năng | ⭐⭐⭐ | 🚀🚀🚀🚀🚀 | Thay thế nếu cần chat đa nhiệm |
| **Gemma 2 9B** | Logic / Chat | ⭐⭐⭐⭐ | 🚀🚀🚀🚀 | Thay thế nếu cần suy luận logic |


## 3. Hướng Dẫn Cài Đặt (Native)

### Bước 3.1: Cài đặt Ollama
Mở Terminal và chạy lệnh sau để cài đặt qua Homebrew:
```bash
brew install ollama
```
Bật service Ollama chạy ngầm:
```bash
brew services start ollama
```
Tắt service Ollama:
```bash
brew services stop ollama
```

### Bước 3.2: Tải các Model AI
Chạy các lệnh sau trong Terminal để kéo model về máy (sẽ mất một lúc tùy tốc độ mạng):
```bash
# Tải model Chat chính (Qwen 14B)
ollama pull qwen2.5-coder:14b

# Tải model Chat phụ / lập trình chuyên sâu (DeepSeek V2 16B)
ollama pull deepseek-coder-v2:16b

# Tải model siêu tốc dành cho Autocomplete
ollama pull qwen2.5-coder:7b

# Tải model tạo Vector Embeddings (cho tính năng tìm kiếm codebase)
ollama pull nomic-embed-text
```

### Bước 3.3: Cài đặt và cấu hình VS Code
1. Mở VS Code, vào mục **Extensions** (Phím tắt: `Cmd + Shift + X`).
2. Tìm và cài đặt extension **Continue** (từ nhà phát triển *Continue*).
3. Bấm vào biểu tượng Continue ở thanh công cụ bên trái của VS Code.
4. Bấm vào nút bánh răng (⚙️ Settings) ở góc dưới cùng để mở file cấu hình của Continue. 

Tùy vào phiên bản của extension Continue, bạn có thể lựa chọn cấu hình bằng định dạng **JSON** hoặc định dạng **YAML** mới:

#### Lựa chọn A: Cấu hình bằng YAML (`config.yaml` - Khuyên dùng cho phiên bản mới)
Phiên bản mới của Continue sử dụng định dạng YAML kết hợp với phân vai (`roles`) rõ ràng cho từng model:

```yaml
name: Main Config
version: 1.0.0
schema: v1
models:
  - name: Qwen 2.5 Coder 14B
    provider: ollama
    model: qwen2.5-coder:14b
    roles:
      - chat
      - edit
      - apply
  - name: DeepSeek Coder V2 16B
    provider: ollama
    model: deepseek-coder-v2:16b
    roles:
      - chat
      - edit
      - apply
  - name: Qwen 2.5 Coder 7B
    provider: ollama
    model: qwen2.5-coder:7b
    roles:
      - autocomplete
  - name: Nomic Embed
    provider: ollama
    model: nomic-embed-text:latest
    roles:
      - embed
```

#### Lựa chọn B: Cấu hình bằng JSON (`config.json` - Phiên bản cũ)
Nếu extension của bạn vẫn đang sử dụng file cấu hình JSON truyền thống:

```json
{
  "models": [
    {
      "title": "Qwen 2.5 Coder 14B (Chat/Generate)",
      "provider": "ollama",
      "model": "qwen2.5-coder:14b",
      "apiBase": "http://localhost:11434"
    },
    {
      "title": "DeepSeek Coder V2 16B (Chat/Generate)",
      "provider": "ollama",
      "model": "deepseek-coder-v2:16b",
      "apiBase": "http://localhost:11434"
    }
  ],
  "tabAutocompleteModel": {
    "title": "Qwen 2.5 Coder 7B (Autocomplete)",
    "provider": "ollama",
    "model": "qwen2.5-coder:7b",
    "apiBase": "http://localhost:11434"
  },
  "embeddingsProvider": {
    "provider": "ollama",
    "model": "nomic-embed-text"
  },
  "allowAnonymousTelemetry": false
}
```

Sau khi cấu hình xong, bạn bôi đen code rồi ấn `Cmd + L` để chat với AI, sử dụng nút `Tab` để tự động điền code, hoặc gõ `@codebase` trong khung chat để AI tự quét toàn bộ thư mục dự án của bạn (sử dụng model Nomic Embed).

## 4. Nâng Cấp: Sử Dụng AI Coding Agent Tự Động Viết Code (Roo Code / Cline)

Nếu bạn muốn AI có khả năng tự chủ cao hơn—không chỉ gợi ý code mà có thể tự tạo file mới, chỉnh sửa nhiều file cùng lúc, tự chạy lệnh terminal để kiểm thử và tự sửa lỗi—bạn có thể cài đặt thêm các **AI Coding Agent** chạy local.

Bạn hoàn toàn có thể chạy song song Continue và Roo Code trên VS Code vì chúng bổ trợ nhau rất tốt (Continue lo autocomplete nhanh khi gõ, Roo Code lo giải quyết các task lớn tự động từ A-Z).

### Cài đặt Roo Code (hoặc Cline) trên VS Code:
1. Mở VS Code, vào mục **Extensions** (`Cmd + Shift + X`).
2. Tìm và cài đặt extension **Roo Code** (hoặc **Cline**).
3. Bấm vào biểu tượng Roo Code ở thanh sidebar bên trái.
4. Chọn **API Provider** là `Ollama`.
5. Điền **Base URL** là `http://localhost:11434`.
6. Chọn model mà bạn đã tải (Khuyên dùng `qwen2.5-coder:14b` hoặc `deepseek-coder-v2:16b` để AI có đủ độ thông minh xử lý logic phức tạp).

### Cách hoạt động:
- Bạn nhập yêu cầu công việc cụ thể vào ô chat của Roo Code (ví dụ: *"Viết bộ unit test cho file script.js này"* hoặc *"Tạo giao diện Landing Page có responsive"*).
- Agent sẽ tự động lập kế hoạch, đề xuất sửa đổi mã nguồn hoặc tạo file mới. Bạn chỉ cần bấm duyệt (Approve) cho mỗi bước chỉnh sửa hoặc chạy thử lệnh terminal của AI.

## 5. Hướng Dẫn Gỡ Cài Đặt (Uninstall)
<details>
<summary><b>Bấm để xem hướng dẫn dọn dẹp sạch sẽ hệ thống (khi cần)</b></summary>

### Xóa các model AI (Giải phóng dung lượng ổ cứng)
Xóa từng model thông qua lệnh của Ollama:
```bash
ollama rm qwen2.5-coder:14b
ollama rm deepseek-coder-v2:16b
ollama rm qwen2.5-coder:7b
ollama rm nomic-embed-text
```
**Hoặc** xóa sạch toàn bộ thư mục dữ liệu chứa model của Ollama:
```bash
rm -rf ~/.ollama
```

### Gỡ phần mềm Ollama
Dừng service đang chạy ngầm và gỡ cài đặt qua Homebrew:
```bash
brew services stop ollama
brew uninstall ollama
```
</details>

## 6. Phụ Lục: Các kiến thức liên quan
<details>
<summary><b>Bấm để xem thông tin phụ trợ</b></summary>

- **Dự án Thunderbolt (từ Thunderbird/Mozilla):** Là một nền tảng mã nguồn mở độc lập đi theo triết lý "AI You Control". Nền tảng này hỗ trợ Model Context Protocol (MCP) và cho phép gắn API ngoại hoặc model local. Nếu bạn có định hướng tự code hẳn một IDE cho riêng mình bằng Electron/Tauri trong tương lai, kho lưu trữ Github của Thunderbolt là một nguồn tham khảo tuyệt vời về kiến trúc phần mềm.
</details>


