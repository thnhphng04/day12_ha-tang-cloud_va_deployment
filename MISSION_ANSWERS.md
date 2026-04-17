# Day 12 Lab - Mission Answers

## Part 1: Localhost vs Production

### Exercise 1.1: Anti-patterns found
1. **Hardcoded Secrets**: API keys (`OPENAI_API_KEY`) and database URLs are directly in the code, which is a security risk if committed to version control.
2. **No Config Management**: Settings like `DEBUG` and `MAX_TOKENS` are hardcoded rather than being externalized.
3. **Insecure Logging**: Using `print()` instead of a logging library, and printing secrets to the console.
4. **Missing Health Checks**: No endpoints for liveness/readiness probes, making it hard for orchestration tools to monitor the app.
5. **Fixed Binding**: Hardcoded `host="localhost"` and `port=8000` prevents the app from running in environments like Docker or Cloud platforms without code changes.

### Exercise 1.3: Comparison table
| Feature | Develop | Production | Why Important? |
| :--- | :--- | :--- | :--- |
| **Config** | Hardcoded | Env Vars / Pydantic Settings | 12-Factor compliance; allows same bundle to run in multiple envs. |
| **Secrets** | Exposed in code | `.env` / Secret Manager | Security; protects sensitive credentials. |
| **Port** | Fixed (8000) | Dynamic (Env: `PORT`) | Required by Cloud providers like Railway/Render. |
| **Health Check**| None | `/health` & `/ready` | Reliability; enables automated health monitoring and zero-downtime deploys. |
| **Logging** | `print()` | Structured JSON Logging | Scalability; easy to search and filter logs in production. |
| **Shutdown** | Immediate | Graceful (SIGTERM handled) | Data Integrity; prevents dropping active connections. |

## Part 2: Docker

### Exercise 2.1: Dockerfile questions
1. **Base image (Develop)**: `python:3.11` (Kích thước lớn, chứa đầy đủ công cụ build).
2. **Working directory**: `/app`.
3. **Tại sao COPY requirements.txt trước?**: Để tận dụng Layer Caching của Docker. Nếu chỉ thay đổi code mà không đổi thư viện, Docker sẽ bỏ qua bước `pip install` giúp build nhanh hơn.
4. **Sự khác biệt của multi-stage build**: Tách biệt môi trường build (có tools) và môi trường chạy (chỉ có runtime) giúp giảm kích thước và tăng bảo mật.

### Exercise 2.3: Image size comparison
- **Develop (Single-stage)**: 1.66 GB
- **Production (Multi-stage)**: 236 MB
- **Difference**: ~85.8% reduction

## Part 3: Cloud Deployment

### Exercise 3.1: Railway deployment
- URL: [Đang chờ triển khai] (Vui lòng chạy `railway login` và `railway up` để lấy URL)
- Screenshot: [Link to screenshot in repo]

### Exercise 3.2: Render vs Railway Comparison
| Feature | Railway (`railway.toml`) | Render (`render.yaml`) |
| :--- | :--- | :--- |
| **Approach** | Platform-as-a-Service (PaaS) | Infrastructure as Code (IaC) |
| **Config Format** | TOML | YAML (Blueprint) |
| **Simplicity** | Rất cao, tự động detect tốt | Cao, nhưng cần khai báo Blueprint |
| **Infrastructure**| Tập trung vào 1 service | Có thể khai báo cả stack (Web, Redis, DB) |
| **Auto-scaling** | Support tốt qua dashboard | Support qua Blueprint spec |

**Khác biệt chính**: Railway tập trung vào sự đơn giản ("it just works"), trong khi Render Blueprint (`render.yaml`) cho phép định nghĩa toàn bộ hạ tầng (như Redis, Database) trong một file duy nhất, giúp quản lý hạ tầng như code (IaC) tốt hơn.