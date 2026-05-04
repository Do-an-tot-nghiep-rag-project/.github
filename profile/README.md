# 🏢 CRM Chatbot — Hệ thống CRM Thông minh tích hợp AI (RAG)
> **Đồ án Tốt nghiệp** — Xây dựng hệ thống CRM tích hợp AI Chatbot sử dụng kỹ thuật **Retrieval-Augmented Generation (RAG)** để trả lời câu hỏi dựa trên tài liệu nội bộ doanh nghiệp.
---
## 🎯 Giới thiệu
Dự án xây dựng một nền tảng chatbot thông minh tích hợp vào hệ thống CRM, cho phép nhân viên hoặc khách hàng đặt câu hỏi bằng ngôn ngữ tự nhiên và nhận câu trả lời chính xác dựa trên kho tài liệu nội bộ (quy trình, chính sách, thông tin sản phẩm…).
Hệ thống sử dụng kỹ thuật **RAG** — kết hợp tìm kiếm ngữ nghĩa (vector search) với mô hình ngôn ngữ lớn (LLM) — để đảm bảo câu trả lời luôn có cơ sở từ dữ liệu thực tế, tránh hiện tượng "hallucination" của AI.
---
## 📦 Cấu trúc tổ chức (3 Repositories)
| Repository | Công nghệ | Vai trò |
|---|---|---|
| 🖥️ **[crm-chatbot](https://github.com/your-org/crm-chatbot)** | Next.js 16, TypeScript, Firebase | Giao diện web người dùng & quản trị |
| 🛡️ **[sh.crm.api](https://github.com/your-org/sh.crm.api)** | ASP.NET Core 8, PostgreSQL, Redis | Backend xác thực & quản lý Agent |
| 🧠 **[sh.vectorindexing](https://github.com/your-org/sh.vectorindexing)** | ASP.NET Core 8, Qdrant, Gemini AI | RAG pipeline & Chat AI backend |
---
## 🔄 Luồng xử lý Chat (RAG Flow)
```
Người dùng gõ câu hỏi
    → crm-chatbot gửi request + Firebase JWT
    → sh.crm.api xác thực token, forward sang sh.vectorindexing
    → IntentDetector phân tích ý định câu hỏi
    → EmbeddingService chuyển câu hỏi thành vector
    → QdrantService tìm top-K chunks liên quan nhất
    → ConfidenceCalculator đánh giá chất lượng kết quả
    → ChatContextBuilder xây dựng prompt với context
    → AnswerGenerator sinh câu trả lời bằng LLM (Gemini Flash)
    → Streaming trả về frontend theo thời gian thực (SSE)
    → ConversationService lưu lịch sử hội thoại
```
## 🔄 Luồng Ingestion Tài liệu
```
Admin Upload File
    → FileTextExtractor trích xuất nội dung
    → LlamaChunkingService chia nhỏ thành chunks
    → EmbeddingService chuyển chunks thành vectors
    → QdrantService lưu vectors vào collection
    → PostgreSQL lưu metadata tài liệu
    → MinIO lưu file gốc
```
---
## 🛠️ Tech Stack tổng hợp
### Frontend
| | |
|---|---|
| **Framework** | Next.js 16 (App Router) + React 19 |
| **Language** | TypeScript 5.9 |
| **UI** | Tailwind CSS v4 + Shadcn/UI + Radix UI |
| **Auth** | Firebase Authentication (Google OAuth) |
| **Real-time** | Microsoft SignalR / Server-Sent Events |
### Backend
| | |
|---|---|
| **Framework** | ASP.NET Core 8 |
| **Architecture** | Clean Architecture (Domain / Application / Infrastructure) |
| **ORM** | Entity Framework Core 8 + Dapper |
| **Validation** | FluentValidation 12 |
| **Logging** | Serilog + OpenTelemetry |
### AI / ML
| | |
|---|---|
| **Embedding** | Google Gemini `gemini-embedding-001` (mặc định) |
| **Embedding Alt** | OpenAI `text-embedding-3-small`, BGE-M3 (đa ngôn ngữ) |
| **LLM** | Google Gemini Flash, Ollama (local) |
| **Vector DB** | Qdrant Cloud |
### Infrastructure
| | |
|---|---|
| **Database** | PostgreSQL 15 |
| **Cache** | Redis 7 |
| **Object Storage** | MinIO (tương thích S3) |
| **Container** | Docker + Docker Compose |
| **Auth Provider** | Firebase (Google Cloud) |
---
## 🚀 Hướng dẫn chạy toàn bộ hệ thống
### Bước 1 — Khởi động Infrastructure
```bash
docker-compose -f docker-compose.local.yml up -d
# Redis (6379) + MinIO (9000, 9001)
```
### Bước 2 — Chạy sh.crm.api
```bash
cd sh.crm.api
dotnet run --project sh.chat.api
# http://localhost:5000
```
### Bước 3 — Chạy sh.vectorindexing
```bash
cd sh.vectorindexing
dotnet run --project sh.vectorindexing
# http://localhost:5261
```
### Bước 4 — Chạy crm-chatbot
```bash
cd crm-chatbot
cp .env.sample .env
yarn install && yarn dev
# http://localhost:4000
```
---
## 📂 Services & Ports
| Service | Port | Ghi chú |
|---|---|---|
| **crm-chatbot** (Frontend) | 4000 | Next.js dev server |
| **sh.crm.api** (Auth API) | 5000 | ASP.NET Core |
| **sh.vectorindexing** (RAG API) | 5261 | ASP.NET Core |
| **PostgreSQL** | 5432 | Database chính |
| **Redis** | 6379 | Cache |
| **MinIO API** | 9000 | Object storage |
| **MinIO Console** | 9001 | MinIO web UI |
| **Qdrant** | 6333 | Vector database (cloud) |
---
## 👨‍💻 Thành viên
| Tên | Vai trò |
|---|---|
| **Phạm Chí Thành** | Full-stack Developer — Đồ án Tốt nghiệp |
---
## 📄 Tài liệu
- 📋 Báo cáo đồ án: `CNTT_2022605231_Phạm Chí Thành_BaoCao.docx`
- 🗂️ Đăng ký đề tài: `dang_ky_de_tai.md`
