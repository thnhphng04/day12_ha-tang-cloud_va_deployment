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
- URL: https://day12ha-tang-cloudvadeployment-production-da89.up.railway.app/
- Screenshot: ./Screenshot.png

## Part 4: API Security

### Exercise 4.1-4.3: Test results
- **Auth (JWT)**: Successfully obtained token for user `student`.
- **Rate Limiting**: Verified 429 error after 10 requests/min.
- **Output**:
```json
{"detail":{"error":"Rate limit exceeded","limit":10,"window_seconds":60,"retry_after_seconds":12}}
```

### Exercise 4.4: Cost guard implementation
**Approach**:
1. **Usage Tracking**: Track `input_tokens` and `output_tokens` per user and globally in-memory (Production would use Redis).
2. **Pricing**: Applied mock pricing ($0.15/1M input, $0.60/1M output).
3. **Thresholds**: 
   - User budget: $1.0/day.
   - Global budget: $10.0/day (Emergency kill-switch).
4. **Validation**: Check budget before LLM call (return 402 if exceeded) and update usage after completion.

## Part 5: Scaling & Reliability

### Exercise 5.1-5.5: Implementation notes
- **Health Checks**: Đã triển khai `/health` (Liveness) và `/ready` (Readiness). Giúp platform tự động hồi phục khi app treo và điều phối traffic chính xác.
- **Graceful Shutdown**: Sử dụng `lifespan` và bắt tín hiệu SIGTERM để đảm bảo hoàn thành các request đang xử lý (in-flight requests) trước khi tắt (timeout 30s).
- **Stateless Design**: Chuyển session storage từ memory sang Redis. Điều này cho phép scale lên nhiều instance mà không mất lịch sử trò chuyện (session continuity).
- **Load Balancing**: Sử dụng Nginx làm Load Balancer để phân tán traffic đều cho các instance Agent, đảm bảo tính sẵn sàng cao.

## Part 6: Final Project

### Production Readiness Check
- **Kết quả script `check_production_ready.py`**: Passed 20/20 checks (100%).
- Hệ thống đã tích hợp đầy đủ:
  - Multi-stage Dockerfile tối ưu.
  - API Key Authentication & Rate Limiting.
  - Daily Cost Guard.
  - Health & Readiness Checks.
  - JSON Structured Logging.
  - Graceful Shutdown.
- **Sẵn sàng triển khai**: Code trong thư mục `06-lab-complete` đã hoàn toàn sẵn sàng để push lên và deploy trên môi trường Cloud (Railway/Render).




