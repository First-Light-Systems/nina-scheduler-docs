# API Reference

**Document Version**: 1.0 | **Last Updated**: December 2025

The Science Scheduler Server provides a REST API for programmatic access to all system functionality.

## Interactive API Documentation

**[View Full API Reference](api/index.html)**

The interactive API reference includes:

- Complete endpoint documentation
- Request/response schemas
- Authentication details
- Try-it-out functionality

## Quick Reference

### Base URL

```
https://your-server.com/api/v1
```

### Authentication

Most endpoints require JWT authentication:

```bash
# Login to get tokens
curl -X POST https://your-server.com/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "user@example.com", "password": "your-password"}'

# Use the access token for authenticated requests
curl https://your-server.com/api/v1/observations \
  -H "Authorization: Bearer <your-access-token>"
```

### Key Endpoints

| Endpoint | Description |
|----------|-------------|
| `POST /auth/login` | Authenticate and get JWT tokens |
| `GET /users` | List users (admin only) |
| `POST /users` | Create new user |
| `GET /users/profile` | Get current user's profile |
| `PUT /users/profile` | Update current user's profile |
| `GET /observations` | List observation requests |
| `POST /observations` | Create new observation |
| `GET /observations/{id}` | Get observation details |
| `GET /observatories` | List registered observatories |
| `GET /queue` | View observation queue |
| `GET /coordinates/lookup?name=M31` | Look up target coordinates |
| `GET /targets` | List saved targets |

### Response Format

All responses follow a consistent JSON format:

```json
{
  "success": true,
  "data": {
    // Response data here
  },
  "timestamp": "2025-12-23T00:00:00.000Z",
  "request_id": "uuid"
}
```

Error responses:

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable message"
  },
  "timestamp": "2025-12-23T00:00:00.000Z",
  "request_id": "uuid"
}
```

### Rate Limiting

- **General API**: 100 requests per 15 minutes
- **Authentication endpoints**: 10 requests per 15 minutes

Rate limit headers are included in responses:
- `X-RateLimit-Limit`: Maximum requests allowed
- `X-RateLimit-Remaining`: Requests remaining in window
- `X-RateLimit-Reset`: Time when limit resets

## Example: Creating an Observation

```bash
curl -X POST https://your-server.com/api/v1/observations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <your-access-token>" \
  -d '{
    "name": "M31 LRGB Imaging",
    "observation_type": "flexible",
    "priority": 5,
    "target": {
      "name": "M31",
      "ra": 10.6847,
      "dec": 41.2689,
      "exposure_configs": [
        {"filter": "L", "exposure_time": 300, "exposure_count": 10},
        {"filter": "R", "exposure_time": 180, "exposure_count": 5},
        {"filter": "G", "exposure_time": 180, "exposure_count": 5},
        {"filter": "B", "exposure_time": 180, "exposure_count": 5}
      ]
    },
    "constraints": {
      "minimum_altitude": 30,
      "maximum_airmass": 2.0,
      "moon_separation": 30
    }
  }'
```

## Example: Looking Up Coordinates

```bash
curl "https://your-server.com/api/v1/coordinates/lookup?name=NGC%207000" \
  -H "Authorization: Bearer <your-access-token>"
```

Response:
```json
{
  "success": true,
  "data": {
    "name": "NGC 7000",
    "resolved_name": "North America Nebula",
    "ra": 314.6,
    "dec": 44.3,
    "ra_sexagesimal": "20h 58m 24s",
    "dec_sexagesimal": "+44° 18' 00\"",
    "source": "simbad",
    "object_type": "HII Region"
  }
}
```

---

For complete API documentation with all endpoints, schemas, and examples, see the **[Interactive API Reference](api/index.html)**.
