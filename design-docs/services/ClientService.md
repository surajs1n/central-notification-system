# Client Service Design

## 1. Responsibilities
- Client onboarding and offboarding
- API key management and rotation
- Rate limit configuration per client
- Client metadata management

## 2. APIs
### 2.1 Internal APIs
| Endpoint | Method | Parameters | Description |
|----------|--------|------------|-------------|
| `/internal/clients` | POST | `{ name, contact }` | Register new client |
| `/internal/clients/{id}` | GET | - | Get client details |

### 2.2 Admin APIs
| Endpoint | Method | Access Level | Description |
|----------|--------|--------------|-------------|
| `/admin/clients` | GET | Admin | List all clients |
| `/admin/clients/{id}/status` | PATCH | Admin | Update client status |

## 3. Data Model
```mermaid
erDiagram
    CLIENTS {
        id UUID PK
        name VARCHAR
        contact_email VARCHAR
        status ENUM
        created_at TIMESTAMP
        updated_at TIMESTAMP
    }
    
    API_KEYS {
        client_id UUID FK
        key VARCHAR
        status ENUM
    }
    
    CLIENTS ||--o{ API_KEYS : has
```

## 4. Key Algorithms
- **API Key Generation**:
  ```python
  def generate_api_key():
      return secrets.token_urlsafe(32)
  ```

## 5. Error Handling
| Code | Message | Retryable | HTTP Status |
|------|---------|-----------|-------------|
| CLIENT_4001 | Client not found | No | 404 |
| CLIENT_4002 | Invalid rate limit value | No | 400 |
| CLIENT_4003 | API key quota exceeded | Yes | 429 |

## 6. Dependencies
- PostgreSQL: Primary data store

## 7. Deployment Architecture
```mermaid
graph TD
    ClientService --> PostgreSQL[(PostgreSQL)]
```

## 8. Size Estimations
- **Storage**: 
  - Clients: 1000 × 1KB = 1MB
  - API Keys: 3000 × 0.5KB = 1.5MB
  - **Total**: 2.5MB
