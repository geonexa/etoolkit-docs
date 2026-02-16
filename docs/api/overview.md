# API Reference Overview

EO-Toolkit provides a RESTful API for programmatic access to features and data.

## Base URL

```
http://your-domain.com/api/
```

## Authentication

Most API endpoints require authentication. See [Authentication](authentication.md) for details.

## Response Format

### Success Response

```json
{
  "result": "success",
  "data": {...}
}
```

### Error Response

```json
{
  "error": "Error message",
  "status": 400
}
```

## Common Status Codes

- `200`: Success
- `400`: Bad Request
- `401`: Unauthorized
- `404`: Not Found
- `500`: Internal Server Error

## Rate Limiting

- Rate limits may apply
- Check response headers for limits
- Implement retry logic with backoff

## Endpoints

See [Endpoints](endpoints.md) for detailed API documentation.

## Examples

### cURL

```bash
curl -X POST http://your-domain.com/api/endpoint \
  -H "Content-Type: application/json" \
  -d '{"key": "value"}'
```

### Python

```python
import requests

response = requests.post(
    'http://your-domain.com/api/endpoint',
    json={'key': 'value'},
    headers={'Content-Type': 'application/json'}
)
```

### JavaScript

```javascript
fetch('http://your-domain.com/api/endpoint', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({key: 'value'})
})
```

---

Next: [API Endpoints](endpoints.md)
