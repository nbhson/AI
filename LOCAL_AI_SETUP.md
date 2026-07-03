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

## 5. Sử Dụng Aider với Local Model (Ollama)

[Aider](https://aider.chat/) là một AI pair programming tool chạy trực tiếp trong terminal, có khả năng làm việc trực tiếp với Git repository của bạn. Aider có thể chỉnh sửa code trong nhiều file cùng lúc, tự commit lên Git, và hỗ trợ nhiều ngôn ngữ lập trình.

### Tại sao nên dùng Aider thay vì (hoặc kết hợp với) các công cụ khác?

| Tính năng | Aider | Continue | Roo Code / Cline |
| :--- | :--- | :--- | :--- |
| **Giao diện** | Terminal (CLI) | VS Code Extension | VS Code Extension |
| **Tự động Git commit** | ✅ Có sẵn | ❌ | ❌ |
| **Chỉnh sửa đa file** | ✅ Có | ✅ Có | ✅ Có |
| **Chạy lệnh terminal** | ❌ (do người dùng chạy) | ❌ | ✅ |
| **Autocomplete khi gõ** | ❌ | ✅ | ❌ |
| **Yêu cầu VS Code** | ❌ (chạy độc lập) | ✅ | ✅ |

### 5.1. Cài đặt Aider

Bạn có thể cài đặt Aider qua pip (Python) hoặc Homebrew:

```bash
# Cách 1: Cài đặt qua pip (khuyên dùng - luôn có phiên bản mới nhất)
pip install aider-chat

# Cách 2: Cài đặt qua Homebrew
brew install aider
```

> **Lưu ý:** Aider yêu cầu Python 3.8+. Nếu dùng pip, hãy đảm bảo bạn đang dùng đúng Python environment (khuyên dùng virtual environment).

### 5.2. Cấu hình Aider để dùng Ollama

Aider sử dụng biến môi trường hoặc file `.env` trong thư mục home để cấu hình. Có hai cách để kết nối Aider với Ollama:

#### Cách 1: Dùng biến môi trường (Nhanh, dùng cho lần đầu)

```bash
# Đặt model mặc định và API base của Ollama
export AIDER_MODEL=ollama_chat/qwen2.5-coder:14b
export AIDER_OLLAMA_API_BASE=http://localhost:11434

# Chạy Aider với model Ollama
aider
```

#### Cách 2: Tạo file `.env` (Lưu cấu hình vĩnh viễn)

Tạo file `~/.aider.conf.yml` hoặc thêm vào `~/.env`:

```yaml
# ~/.aider.conf.yml
model: ollama_chat/qwen2.5-coder:14b
ollama-api-base: http://localhost:11434
```

Hoặc thêm vào file `.env` trong thư mục home:
```bash
# ~/.env
AIDER_MODEL=ollama_chat/qwen2.5-coder:14b
AIDER_OLLAMA_API_BASE=http://localhost:11434
```

#### Cách 3: Dùng flag trực tiếp khi chạy

```bash
aider --model ollama_chat/qwen2.5-coder:14b --ollama-api-base http://localhost:11434
```

### 5.3. Các model khuyên dùng cho Aider

Aider hoạt động tốt nhất với các model code-dedicated. Dưới đây là các khuyến nghị cho Mac M4 24GB:

| Model | Chất lượng code | Tốc độ | RAM tiêu thụ | Phù hợp cho |
| :--- | :--- | :--- | :--- | :--- |
| `ollama_chat/qwen2.5-coder:14b` | ⭐⭐⭐⭐ | 🚀🚀🚀🚀 | ~9-10GB | Dự án vừa và nhỏ, phản hồi nhanh |
| `ollama_chat/deepseek-coder-v2:16b` | ⭐⭐⭐⭐⭐ | 🚀🚀🚀 | ~10GB | Dự án lớn, logic phức tạp |
| `ollama_chat/qwen2.5-coder:7b` | ⭐⭐⭐ | 🚀🚀🚀🚀🚀 | ~4GB | Code đơn giản, muốn tốc độ tối đa |

> **Mẹo:** Nếu bạn muốn Aider tự động chọn model phù hợp dựa trên độ phức tạp của task, bạn có thể thiết lập nhiều model trong cùng một file cấu hình (tính năng có từ phiên bản mới).

### 5.4. Cách sử dụng Aider cơ bản

```bash
# 1. Chạy Aider trong thư mục dự án (phải có Git init)
cd /path/to/your/project
git init  # Nếu chưa có Git repo
aider

# 2. Chat với Aider (gõ yêu cầu trực tiếp)
# Ví dụ: "Add error handling to the fetchData function in api.js"

# 3. Chuyển sang chế độ Architect (lên kế hoạch trước, code sau)
# /architect
# Trong chế độ này, Aider sẽ phân tích thiết kế trước, sau đó mới viết code.
# Phù hợp cho các task phức tạp cần lên architecture trước.

# 4. Chuyển sang chế độ Code (chế độ mặc định - tập trung viết code)
# /code

# 5. Thêm file vào phiên làm việc
# /add src/main.py
# /add src/utils/helpers.py

# 6. Thêm nhiều file cùng lúc
# /add src/*.py src/utils/*.py

# 7. Thêm toàn bộ thư mục
# /add src/

# 8. Xoá file khỏi context
# /drop src/old-file.py

# 9. Xoá toàn bộ file khỏi context (xóa sạch)
# /clear

# 10. Xem danh sách các file đang được quản lý
# /files

# 11. Xem repo map (sơ đồ cấu trúc dự án)
# /map

# 12. Xoá lịch sử chat
# /history

# 13. Xoá hoàn toàn (clear + reset toàn bộ session)
# /reset

# 14. Diff các thay đổi trước khi commit
# /diff

# 15. Commit thay đổi
# /commit

# 16. Yêu cầu Aider giải thích code
# /explain src/main.py

# 17. Chạy Aider với chế độ tự động commit (không cần hỏi)
aider --auto-commits

# 18. Chạy Aider ở chế độ "tự động" (không cần phê duyệt từng thay đổi)
aider --yes
```

### 5.5. Các flags hữu ích khác

```bash
# Chỉ định kiến trúc model (quan trọng với Ollama)
aider --model ollama_chat/qwen2.5-coder:14b --map-tokens 1024

# Tăng số token cho context window (hữu ích với model 14B+)
aider --max-chat-history-tokens 8192

# Chỉ định thư mục làm việc
aider --directory /path/to/project

# Chạy ở chế độ quiet (ít log hơn)
aider --no-pretty
```

### 5.6. Lưu ý khi dùng Aider với Local Model

1. **Tốc độ inference:** Không nên dùng model nhỏ hơn 7B cho Aider vì chất lượng code sẽ không đủ tốt để chỉnh sửa đa file.
2. **Git repository là bắt buộc:** Aider yêu cầu dự án phải được init Git để có thể theo dõi và hoàn tác thay đổi.
3. **Context Window:** Model local thường có context window nhỏ hơn API đám mây (ví dụ 8K-32K tokens so với 128K+). Hãy giữ số lượng file trong phiên làm việc ở mức vừa phải (5-10 file).
4. **Kiểm tra kết nối:** Nếu Aider không kết nối được với Ollama, hãy kiểm tra:
   ```bash
   # Kiểm tra Ollama đang chạy
   curl http://localhost:11434/api/tags
   
   # Kiểm tra model đã được tải
   ollama list
   ```

> **Mẹo nâng cao:** Kết hợp Aider với Cline/Roo Code: dùng Aider cho các tác vụ yêu cầu Git commit tự động và chỉnh sửa code chính xác, dùng Roo Code khi cần agent tự động chạy terminal và cài đặt dependencies.

## 6. Hướng Dẫn Gỡ Cài Đặt (Uninstall)
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

### Gỡ Aider (nếu đã cài)
```bash
pip uninstall aider-chat
# Hoặc nếu cài qua Homebrew:
brew uninstall aider
```
</details>

## 7. Phụ Lục: Các kiến thức liên quan
<details>
<summary><b>Bấm để xem thông tin phụ trợ</b></summary>

- **Dự án Thunderbolt (từ Thunderbird/Mozilla):** Là một nền tảng mã nguồn mở độc lập đi theo triết lý "AI You Control". Nền tảng này hỗ trợ Model Context Protocol (MCP) và cho phép gắn API ngoại hoặc model local. Nếu bạn có định hướng tự code hẳn một IDE cho riêng mình bằng Electron/Tauri trong tương lai, kho lưu trữ Github của Thunderbolt là một nguồn tham khảo tuyệt vời về kiến trúc phần mềm.
</details>