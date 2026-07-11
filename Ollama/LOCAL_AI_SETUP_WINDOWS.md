# Hướng Dẫn Setup Local AI IDE Cho Windows

Tài liệu này tổng hợp toàn bộ kiến thức và hướng dẫn từng bước để biến VS Code thành một IDE tích hợp AI chạy hoàn toàn dưới máy cá nhân (Local) trên hệ điều hành Windows, đảm bảo 100% bảo mật dữ liệu và tận dụng tối đa phần cứng (GPU) của bạn.

---

## 1. Tổng Quan Kiến Trúc
Hệ thống Local AI IDE gồm 2 thành phần chính:
- **Backend (Inference Engine):** Sử dụng **Ollama** bản Native cho Windows để chạy các mô hình AI mã nguồn mở.
- **Frontend (IDE):** Sử dụng **VS Code** kết hợp với extension **Continue.dev** làm giao diện tương tác (chat, autocomplete).

### Tại sao nên cài Native Ollama thay vì chạy qua Docker trên Windows?
> [!IMPORTANT]
> **Khuyến nghị hiệu năng:** Mặc dù Docker trên Windows (qua WSL2) hỗ trợ GPU Passthrough khá tốt, nhưng việc thiết lập rất phức tạp (yêu cầu cấu hình WSL2, cài đặt NVIDIA Container Toolkit). 
> Cài đặt **Ollama Native cho Windows** giúp tự động nhận diện card đồ họa (NVIDIA CUDA hoặc AMD ROCm) và tối ưu hóa hiệu năng phần cứng ngay lập tức mà không cần bất kỳ cấu hình phức tạp nào.

---

## 2. Lựa Chọn Model Theo Cấu Hình Phần Cứng Windows

Khác với Mac sử dụng Unified Memory (RAM dùng chung cho cả CPU và GPU), PC Windows phân chia rõ ràng giữa **System RAM** và **VRAM (Video RAM - Bộ nhớ của card đồ họa)**. Tốc độ sinh code của AI phụ thuộc lớn vào việc Model có nằm trọn trong VRAM của GPU hay không.

Dưới đây là các cấu hình khuyến nghị:

### A. Cấu hình Phổ thông (GPU VRAM 6GB - 8GB | RAM 16GB)
*Thường gặp trên các laptop/PC gaming phân khúc phổ thông (ví dụ: RTX 3050, RTX 3060 6GB, RTX 4050).*
- **Model Chat chính:** `qwen2.5-coder:7b` (Dung lượng ~4.7GB, chạy cực mượt trên GPU).
- **Model Autocomplete:** `qwen2.5-coder:1.5b` hoặc `qwen2.5-coder:3b` (~1GB - 2GB, phản hồi tức thì).
- **Embeddings:** `nomic-embed-text:latest` (~280MB, siêu nhẹ).

### B. Cấu hình Tầm trung (GPU VRAM 12GB | RAM 32GB)
*Phổ biến với các card đồ họa như RTX 3060 12GB, RTX 4070 12GB, RTX 4070 Super.*
- **Model Chat chính:** `qwen2.5-coder:14b` (Dung lượng ~9GB) hoặc `deepseek-coder-v2:16b` (Dung lượng ~10GB). Chạy hoàn toàn trên GPU, tốc độ phản hồi rất nhanh và thông minh.
- **Model Autocomplete:** `qwen2.5-coder:7b` (~4.7GB).
- **Embeddings:** `nomic-embed-text:latest`.

### C. Cấu hình Cao cấp (GPU VRAM >= 16GB | RAM >= 32GB)
*Trang bị card đồ họa cao cấp như RTX 3090, RTX 4080, RTX 4090, hoặc cấu hình chạy nhiều GPU.*
- **Model Chat chính:** `qwen2.5-coder:32b` (Dung lượng ~20GB, siêu thông minh) hoặc chạy song song nhiều model lớn.
- **Model Autocomplete:** `qwen2.5-coder:7b`.
- **Embeddings:** `nomic-embed-text:latest`.

---

### Bảng so sánh nhanh các Model

| Model | Mục đích | VRAM đề xuất | Độ thông minh | Tốc độ phản hồi | Khuyên dùng |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Qwen 2.5 Coder 14B** | Chat / Viết Code | >= 10GB | ⭐⭐⭐⭐ | 🚀🚀🚀🚀 | **Tốt nhất cho cấu hình trung/mạnh** |
| **Qwen 2.5 Coder 7B** | Chat / Autocomplete | >= 6GB | ⭐⭐⭐ | 🚀🚀🚀🚀🚀 | **Mặc định cho máy phổ thông** |
| **DeepSeek Coder V2 16B** | Chat chuyên sâu | >= 12GB | ⭐⭐⭐⭐⭐ | 🚀🚀🚀 | **Nên trải nghiệm nếu VRAM lớn** |
| **Qwen 2.5 Coder 1.5B** | Autocomplete | Mọi cấu hình | ⭐⭐ | 🚀🚀🚀🚀🚀 | **Dành cho máy cấu hình yếu** |
| **Nomic Embed** | Embeddings (`@codebase`) | ~300MB | N/A | 🚀🚀🚀🚀🚀 | **Bắt buộc để phân tích codebase** |

---

## 3. Hướng Dẫn Cài Đặt Chi Tiết

### Bước 3.1: Cài đặt Ollama cho Windows
Bạn có thể chọn một trong hai cách sau:

#### Cách 1: Tải bộ cài đặt trực tiếp (Khuyên dùng)
1. Truy cập trang chủ Ollama: [ollama.com/download](https://ollama.com/download)
2. Tải file cài đặt dành cho **Windows** (`OllamaSetup.exe`).
3. Chạy file exe và làm theo hướng dẫn cài đặt. Sau khi cài xong, Ollama sẽ khởi động và xuất hiện dưới dạng một icon nhỏ hình con khủng long ở khay hệ thống (System Tray) góc dưới bên phải màn hình.

#### Cách 2: Cài đặt qua Winget (Windows Package Manager)
Mở **PowerShell** (hoặc Command Prompt) và chạy lệnh:
```powershell
winget install Ollama.Ollama
```

---

### Bước 3.2: Tải các Model AI
Mở **PowerShell** hoặc **Command Prompt** (CMD) và chạy các lệnh dưới đây để tải model về máy (tốc độ tải phụ thuộc vào đường truyền Internet của bạn):

```powershell
# Tải model Chat chính (Tùy chọn theo VRAM của bạn, ví dụ chọn bản 14B)
ollama pull qwen2.5-coder:14b

# Tải model bổ trợ cho Autocomplete (Ví dụ bản 7B hoặc 3B)
ollama pull qwen2.5-coder:7b

# Tải model tạo Vector Embeddings (cho tính năng tìm kiếm codebase)
ollama pull nomic-embed-text
```

> [!TIP]
> Bạn có thể kiểm tra danh sách các model đã tải thành công bằng lệnh:
> ```powershell
> ollama list
> ```

---

### Bước 3.3: Cài đặt và cấu hình extension Continue trên VS Code

1. Mở **VS Code** trên Windows.
2. Mở trình quản lý Extension (Phím tắt: `Ctrl + Shift + X`).
3. Tìm kiếm từ khóa `Continue` và nhấn **Install**.
4. Sau khi cài đặt, một biểu tượng **Continue** (hình mũi tên/vòng tròn) sẽ xuất hiện ở thanh sidebar bên trái.
5. Click vào biểu tượng đó, chọn biểu tượng bánh răng (⚙️ Settings) ở góc dưới để mở file cấu hình.

Tùy vào phiên bản Continue bạn đang sử dụng, hãy chọn một trong hai cách cấu hình sau:

#### Lựa chọn A: Cấu hình bằng YAML (`config.yaml` - Phiên bản mới)
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
```json
{
  "models": [
    {
      "title": "Qwen 2.5 Coder 14B (Chat/Generate)",
      "provider": "ollama",
      "model": "qwen2.5-coder:14b",
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

---

## 4. Hướng Dẫn Sử Dụng Trên Windows
- **Trò chuyện trực tiếp:** Bôi đen đoạn code cần hỏi và nhấn tổ hợp phím `Ctrl + L`. Cửa sổ Chat của Continue sẽ hiện ra ở bên trái.
- **Chỉnh sửa code tại chỗ (Inline Edit):** Nhấn `Ctrl + I`, nhập yêu cầu và AI sẽ chỉnh sửa trực tiếp vào file code của bạn.
- **Autocomplete:** Khi bạn đang gõ code, AI sẽ tự động gợi ý đoạn code tiếp theo bằng màu xám mờ. Nhấn nút `Tab` để đồng ý áp dụng gợi ý.
- **Thấu hiểu toàn bộ dự án:** Nhập `@codebase` vào ô chat để AI phân tích và tìm kiếm ngữ cảnh từ toàn bộ các file trong thư mục làm việc hiện tại.

---

## 5. Nâng Cấp: Sử Dụng AI Coding Agent Tự Động Viết Code (Roo Code / Cline)

Nếu bạn muốn AI có khả năng tự chủ cao hơn—không chỉ gợi ý code mà có thể tự tạo file mới, chỉnh sửa nhiều file cùng lúc, tự chạy lệnh terminal (PowerShell/CMD) để kiểm thử và tự sửa lỗi—bạn có thể cài đặt thêm các **AI Coding Agent** chạy local.

Bạn hoàn toàn có thể chạy song song Continue và Roo Code trên VS Code vì chúng bổ trợ nhau rất tốt (Continue lo autocomplete nhanh khi gõ, Roo Code lo giải quyết các task lớn tự động từ A-Z).

### Cài đặt Roo Code (hoặc Cline) trên VS Code:
1. Mở VS Code, vào mục **Extensions** (Phím tắt: `Ctrl + Shift + X`).
2. Tìm và cài đặt extension **Roo Code** (hoặc **Cline**).
3. Bấm vào biểu tượng Roo Code ở thanh sidebar bên trái.
4. Chọn **API Provider** là `Ollama`.
5. Điền **Base URL** là `http://localhost:11434`.
6. Chọn model mà bạn đã tải (Khuyên dùng `qwen2.5-coder:14b` hoặc `deepseek-coder-v2:16b` để AI có đủ độ thông minh xử lý logic phức tạp).

### Cách hoạt động:
- Bạn nhập yêu cầu công việc cụ thể vào ô chat của Roo Code (ví dụ: *"Viết bộ unit test cho file script.js này"* hoặc *"Tạo giao diện Landing Page có responsive"*).
- Agent sẽ tự động lập kế hoạch, đề xuất sửa đổi mã nguồn hoặc tạo file mới. Bạn chỉ cần bấm duyệt (Approve) cho mỗi bước chỉnh sửa hoặc chạy thử lệnh terminal của AI.

---

## 6. Sử Dụng Aider với Local Model (Ollama)

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

### 6.1. Cài đặt Aider

Aider yêu cầu Python 3.8+. Trên Windows, bạn có thể cài đặt qua pip:

```powershell
# Cài đặt Aider qua pip
pip install aider-chat
```

> **Lưu ý:** 
> - Nếu bạn chưa có Python, hãy tải từ [python.org](https://www.python.org/downloads/) và **nhớ tick chọn "Add Python to PATH"** khi cài đặt.
> - Khuyên dùng tạo virtual environment riêng cho Aider để tránh xung đột package:
>   ```powershell
>   python -m venv aider-env
>   .\aider-env\Scripts\activate
>   pip install aider-chat
>   ```

### 6.2. Cấu hình Aider để dùng Ollama

Aider sử dụng biến môi trường hoặc file `.env` trong thư mục home để cấu hình. Có ba cách để kết nối Aider với Ollama:

#### Cách 1: Dùng biến môi trường (Nhanh, dùng cho lần đầu)

```powershell
# Đặt model mặc định và API base của Ollama
$env:AIDER_MODEL="ollama_chat/qwen2.5-coder:14b"
$env:AIDER_OLLAMA_API_BASE="http://localhost:11434"

# Chạy Aider với model Ollama
aider
```

#### Cách 2: Tạo file `.env` (Lưu cấu hình vĩnh viễn)

Tạo file `%USERPROFILE%\.aider.conf.yml` với nội dung:

```yaml
# ~\.aider.conf.yml
model: ollama_chat/qwen2.5-coder:14b
ollama-api-base: http://localhost:11434
```

Hoặc thêm vào file `%USERPROFILE%\.env`:
```
AIDER_MODEL=ollama_chat/qwen2.5-coder:14b
AIDER_OLLAMA_API_BASE=http://localhost:11434
```

#### Cách 3: Dùng flag trực tiếp khi chạy

```powershell
aider --model ollama_chat/qwen2.5-coder:14b --ollama-api-base http://localhost:11434
```

### 6.3. Các model khuyên dùng cho Aider trên Windows

Aider hoạt động tốt nhất với các model code-dedicated. Dưới đây là các khuyến nghị theo cấu hình phần cứng:

| Cấu hình | Model | Chất lượng code | Tốc độ | VRAM tiêu thụ |
| :--- | :--- | :--- | :--- | :--- |
| **Phổ thông (6-8GB VRAM)** | `ollama_chat/qwen2.5-coder:7b` | ⭐⭐⭐ | 🚀🚀🚀🚀🚀 | ~4.7GB |
| **Tầm trung (12GB VRAM)** | `ollama_chat/qwen2.5-coder:14b` | ⭐⭐⭐⭐ | 🚀🚀🚀🚀 | ~9GB |
| **Tầm trung (12GB VRAM)** | `ollama_chat/deepseek-coder-v2:16b` | ⭐⭐⭐⭐⭐ | 🚀🚀🚀 | ~10GB |
| **Cao cấp (16GB+ VRAM)** | `ollama_chat/qwen2.5-coder:32b` | ⭐⭐⭐⭐⭐ | 🚀🚀🚀 | ~20GB |

> **Mẹo:** Nếu bạn muốn Aider tự động chọn model phù hợp dựa trên độ phức tạp của task, bạn có thể thiết lập nhiều model trong cùng một file cấu hình (tính năng có từ phiên bản mới).

### 6.4. Cách sử dụng Aider cơ bản

```powershell
# 1. Mở PowerShell, di chuyển đến thư mục dự án (phải có Git init)
cd C:\path\to\your\project
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

### 6.5. Các flags hữu ích khác

```powershell
# Chỉ định kiến trúc model (quan trọng với Ollama)
aider --model ollama_chat/qwen2.5-coder:14b --map-tokens 1024

# Tăng số token cho context window (hữu ích với model 14B+)
aider --max-chat-history-tokens 8192

# Chỉ định thư mục làm việc
aider --directory C:\path\to\project

# Chạy ở chế độ quiet (ít log hơn)
aider --no-pretty
```

### 6.6. Lưu ý khi dùng Aider với Local Model

1. **Tốc độ inference:** Không nên dùng model nhỏ hơn 7B cho Aider vì chất lượng code sẽ không đủ tốt để chỉnh sửa đa file.
2. **Git repository là bắt buộc:** Aider yêu cầu dự án phải được init Git để có thể theo dõi và hoàn tác thay đổi.
3. **Context Window:** Model local thường có context window nhỏ hơn API đám mây (ví dụ 8K-32K tokens so với 128K+). Hãy giữ số lượng file trong phiên làm việc ở mức vừa phải (5-10 file).
4. **Kiểm tra kết nối:** Nếu Aider không kết nối được với Ollama, hãy kiểm tra:
   ```powershell
   # Kiểm tra Ollama đang chạy
   curl http://localhost:11434/api/tags
   
   # Kiểm tra model đã được tải
   ollama list
   ```

> **Mẹo nâng cao:** Kết hợp Aider với Cline/Roo Code: dùng Aider cho các tác vụ yêu cầu Git commit tự động và chỉnh sửa code chính xác, dùng Roo Code khi cần agent tự động chạy terminal và cài đặt dependencies.

---

## 7. Hướng Dẫn Gỡ Cài Đặt (Uninstall)

Khi bạn muốn giải phóng không gian bộ nhớ hoặc gỡ sạch hệ thống:

### Bước 7.1: Xóa các Model AI để giải phóng bộ nhớ ổ cứng
Mở **PowerShell** và xóa các model:
```powershell
ollama rm qwen2.5-coder:14b
ollama rm qwen2.5-coder:7b
ollama rm nomic-embed-text
```
Hoặc bạn có thể xóa trực tiếp thư mục lưu trữ model mặc định của Ollama trên Windows tại:
`C:\Users\<Tên_Tài_Khoản>\.ollama` (hoặc truy cập nhanh qua đường dẫn `%USERPROFILE%\.ollama` trong File Explorer).

### Bước 7.2: Gỡ phần mềm Ollama
1. Chuột phải vào biểu tượng Ollama ở khay hệ thống và chọn **Quit**.
2. Mở **Settings** -> **Apps** -> **Installed Apps** (hoặc Add/Remove Programs trên các bản Windows cũ).
3. Tìm kiếm **Ollama** và chọn **Uninstall**.

### Bước 7.3: Gỡ Aider (nếu đã cài)
```powershell
pip uninstall aider-chat
```
Nếu bạn đã tạo virtual environment riêng, chỉ cần xóa thư mục `aider-env` là xong.