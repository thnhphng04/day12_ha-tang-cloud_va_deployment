# Deployment Information

## Public URL
https://agent-thnhphng04.up.railway.app

## Platform
Railway

## Test Commands

### Health Check
```bash
curl https://agent-thnhphng04.up.railway.app/health
# Expected: {"status": "ok"}
```

### API Test (with authentication)
```bash
curl -X POST https://agent-thnhphng04.up.railway.app/ask \
  -H "X-API-Key: test_api_key_123" \
  -H "Content-Type: application/json" \
  -d '{"user_id": "test", "question": "Hello"}'
```

## Environment Variables Set
- PORT (auto-injected by Railway)
- AGENT_API_KEY (configured in dashboard)

## Screenshots
- [Deployment dashboard](screenshots/railway_deployment.png)
- [Service running](screenshots/railway_deployment.png)
