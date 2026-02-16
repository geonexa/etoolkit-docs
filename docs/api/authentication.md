# API Authentication

EO-Toolkit uses Django's session-based authentication for API access.

## Authentication Methods

### Session Authentication

The primary authentication method uses Django sessions:

1. Log in through web interface (`/accounts/login/`)
2. Session cookie is set
3. Subsequent API requests include session cookie
4. Session is validated server-side

### CSRF Protection

POST requests require CSRF token:

1. Get CSRF token from cookie or response header
2. Include in request header: `X-CSRFToken: <token>`
3. Or include in form data: `csrfmiddlewaretoken: <token>`

## Making Authenticated Requests

### Browser (JavaScript)

```javascript
// Session cookie automatically included
fetch('/api/endpoint', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-CSRFToken': getCookie('csrftoken')
  },
  credentials: 'include',
  body: JSON.stringify({...})
})
```

### Python (Requests)

```python
import requests

session = requests.Session()

# Login first
login_response = session.post(
    'http://your-domain.com/accounts/login/',
    data={
        'username': 'your_username',
        'password': 'your_password',
        'csrfmiddlewaretoken': session.cookies.get('csrftoken')
    }
)

# Use session for API calls
response = session.post(
    'http://your-domain.com/api/endpoint',
    json={...},
    headers={'X-CSRFToken': session.cookies.get('csrftoken')}
)
```

### cURL

```bash
# Login and save cookies
curl -c cookies.txt -b cookies.txt \
  -X POST http://your-domain.com/accounts/login/ \
  -d "username=your_username&password=your_password"

# Use cookies for API calls
curl -b cookies.txt \
  -X POST http://your-domain.com/api/endpoint \
  -H "Content-Type: application/json" \
  -H "X-CSRFToken: <token>" \
  -d '{"key": "value"}'
```

## Authentication Required

Most API endpoints require authentication. Unauthenticated requests return:

```json
{
  "error": "Authentication required",
  "status": 401
}
```

## Session Management

### Session Expiration

- Sessions expire after inactivity
- Default timeout: Configurable in Django settings
- Re-authenticate when session expires

### Logout

To logout:

```bash
POST /accounts/logout/
```

This invalidates the session.

## Security Considerations

1. **HTTPS**: Use HTTPS in production
2. **Secure Cookies**: Enable secure cookie flags
3. **CSRF Protection**: Always include CSRF tokens
4. **Session Timeout**: Configure appropriate timeouts
5. **Password Security**: Use strong passwords

## Troubleshooting

### Authentication Fails

- Verify credentials
- Check session cookie is set
- Ensure CSRF token included
- Check session hasn't expired

### CSRF Token Errors

- Include CSRF token in requests
- Get token from cookie or header
- Ensure token matches session

### Session Expired

- Re-authenticate
- Check session timeout settings
- Implement session refresh logic

---

Return to: [API Overview](overview.md)
