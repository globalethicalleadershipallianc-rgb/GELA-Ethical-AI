# GELA API Reference v1.0

---

## Base URL

```
https://api.gela.org/v1
```

**Testnet:** `https://testnet.api.gela.org/v1`

---

## Authentication

Most endpoints require a JWT token obtained after membership verification.

```http
Authorization: Bearer <your_jwt_token>
```

---

## Public Endpoints

### GET /status

Health check.

**Response:**

```json
{
  "status": "healthy",
  "version": "1.0.0",
  "network": "polygon-mainnet"
}
```

---

### GET /conflicts

Get all active conflicts.

**Response:**

```json
[
  {
    "id": "c123",
    "name": "Border Tension - Region A",
    "location": { "lat": 34.5, "lng": 69.2 },
    "type": "military_movement",
    "severity": "high",
    "timestamp": "2026-01-15T10:00:00Z",
    "source": "satellite"
  }
]
```

---

### GET /signatories

Get all charter signatories.

**Response:**

```json
{
  "total": 1250,
  "signatories": [
    {
      "address": "0x...",
      "name": "Individual Member",
      "type": "individual",
      "timestamp": "2026-01-01T00:00:00Z"
    }
  ]
}
```

---

## Authenticated Endpoints

### POST /members/apply

Apply for membership.

**Request:**

```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "type": "individual",
  "country": "Bangladesh"
}
```

**Response:**

```json
{
  "application_id": "app_123",
  "status": "pending",
  "next_steps": "Pay membership fee to activate"
}
```

---

### GET /members/profile

Get your membership profile.

**Response:**

```json
{
  "id": "member_123",
  "type": "individual",
  "status": "active",
  "joined": "2026-01-01",
  "voting_power": 1,
  "pv_tokens": 100
}
```

---

### POST /veto/create

Create a new veto proposal (only sovereign and individual members).

**Request:**

```json
{
  "conflict_id": "c123",
  "duration_days": 7
}
```

**Response:**

```json
{
  "proposal_id": "prop_456",
  "conflict_name": "Border Tension - Region A",
  "end_time": "2026-01-22T10:00:00Z",
  "threshold": 51
}
```

---

### POST /veto/cast

Cast a veto vote.

**Request:**

```json
{
  "proposal_id": "prop_456",
  "vote": "veto"
}
```

**Response:**

```json
{
  "success": true,
  "veto_percentage": 52.3,
  "war_stopped": true,
  "message": "VETO PASSED! War stopped by 52.3% of voters."
}
```

---

### GET /veto/status/{proposal_id}

Get proposal status.

**Response:**

```json
{
  "proposal_id": "prop_456",
  "veto_count": 523,
  "total_votes": 1000,
  "veto_percentage": 52.3,
  "threshold": 51,
  "passed": true,
  "active": false,
  "time_remaining_minutes": 0
}
```

---

## WebSocket

**URL:** `wss://api.gela.org/ws`

### Events

| Event | Payload | Description |
|-------|---------|-------------|
| conflict-update | Conflict object | New or updated conflict |
| new-alert | Alert object | Critical alert requiring attention |
| veto-status | Proposal status | Real-time veto count updates |

### Example Subscription

```javascript
const ws = new WebSocket('wss://api.gela.org/ws');

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log(data.type, data.payload);
};
```

---

## Error Codes

| Code | Meaning |
|------|---------|
| 400 | Bad request (missing fields) |
| 401 | Unauthorized (invalid token) |
| 403 | Forbidden (insufficient permissions) |
| 404 | Not found |
| 429 | Too many requests |
| 500 | Internal server error |

---

## Rate Limits

| Endpoint Type | Limit |
|---------------|-------|
| Public | 100 requests per minute |
| Authenticated | 1000 requests per minute |
| Veto casting | 1 per proposal per voter |

---

## Smart Contract Addresses

### Polygon Mainnet

**People's Veto Token (PVT):**
```
0x1234567890AbCdEf1234567890AbCdEf123456
```

**GELA Governance Contract:**
```
0xAbCdEf1234567890AbCdEf1234567890AbCdEf
```

### Polygon Testnet (Mumbai)

**People's Veto Token (PVT - Testnet):**
```
0xTestnet1234567890AbCdEf1234567890AbCd
```

---

## SDK Documentation

### JavaScript/Node.js

```bash
npm install @gela/sdk
```

```javascript
const GELA = require('@gela/sdk');

const client = new GELA.Client({
  apiUrl: 'https://api.gela.org/v1',
  jwtToken: 'your_token_here'
});

// Get conflicts
const conflicts = await client.getConflicts();

// Cast veto
const result = await client.castVeto('prop_456');
```

### Python

```bash
pip install gela-sdk
```

```python
from gela import Client

client = Client(
    api_url='https://api.gela.org/v1',
    jwt_token='your_token_here'
)

# Get conflicts
conflicts = client.get_conflicts()

# Cast veto
result = client.cast_veto('prop_456')
```

---

## Support & Resources

- **Documentation:** https://docs.gela.org
- **GitHub:** https://github.com/globalethicalleadershipallianc-rgb/GELA-Ethical-AI
- **Email:** api-support@gela.org
- **Discord:** [Link to community]

---

**Version:** 1.0  
**Last Updated:** 2026  
**License:** MIT
