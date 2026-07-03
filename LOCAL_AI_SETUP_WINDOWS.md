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

## 6. Hướng Dẫn Gỡ Cài Đặt (Uninstall)

Khi bạn muốn giải phóng không gian bộ nhớ hoặc gỡ sạch hệ thống:

### Bước 6.1: Xóa các Model AI để giải phóng bộ nhớ ổ cứng
Mở **PowerShell** và xóa các model:
```powershell
ollama rm qwen2.5-coder:14b
ollama rm qwen2.5-coder:7b
ollama rm nomic-embed-text
```
Hoặc bạn có thể xóa trực tiếp thư mục lưu trữ model mặc định của Ollama trên Windows tại:
`C:\Users\<Tên_Tài_Khoản>\.ollama` (hoặc truy cập nhanh qua đường dẫn `%USERPROFILE%\.ollama` trong File Explorer).

### Bước 6.2: Gỡ phần mềm Ollama
1. Chuột phải vào biểu tượng Ollama ở khay hệ thống và chọn **Quit**.
2. Mở **Settings** -> **Apps** -> **Installed Apps** (hoặc Add/Remove Programs trên các bản Windows cũ).
3. Tìm kiếm **Ollama** và chọn **Uninstall**.
