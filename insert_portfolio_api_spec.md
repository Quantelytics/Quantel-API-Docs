# Portfolio Upload API

## Overview
This API allows clients to **create** and **update** portfolio information and holdings data in the system.

## Base URL
```
https://optimizer.dev.tenjin-api.com
```

## Authentication
All requests require an API key in the header:
```
Authorization: x-api-key <YOUR_API_KEY>
Content-Type: application/json
```

## Endpoints

| Method | Path                       | Description                    |
|--------|----------------------------|--------------------------------|
| POST   | `/upload_portfolio`              | Create a new portfolio         |

---

## 📥 Request

### Headers

| Name          | Type   | Required | Description                   |
|---------------|--------|----------|-------------------------------|
| Authorization | string | ✅       | `x-api-key <token>`           |
| Content-Type  | string | ✅       | `application/json`            |

### Body Parameters

| Field          | Type   | Create Required | Update Required | Description                            |
|----------------|--------|-----------------|-----------------|----------------------------------------|
| user_name      | string | ✅              | ✅              | Unique user identifier (email)         |
| account_id     | string | ✅              | ✅              | Account ID                             |
| portfolio_name | string | ❌              | ❌              | Name of the portfolio                  |
| first_name     | string | ✅              | ❌              | User’s first name                      |
| last_name      | string | ✅              | ❌              | User’s last name                       |
| csv_string     | string | ✅              | ✅              | Holdings CSV string (see format below) |

#### CSV Format

- **Header row** (column order): `date,ticker,weights,units_held,units_cost,currency`
- **Date format**: `YYYY-MM-DD`
- **Example**:
  ```csv
  date,ticker,weights,units_held,units_cost,currency
  2024-01-01,NVDA,0.10,100,150.00,USD
  2024-01-01,TSLA,0.90,200,250.00,USD
  2024-02-01,QQQ,0.95,300,5.00,USD
  2024-02-01,SPY,0.05,10,200.00,USD
  ```

### Example Request (Create)

```bash
curl -X POST https://optimizer.dev.tenjin-api.com/upload_portfolio \
  -H "Authorization: x-api-key abc123" \
  -H "Content-Type: application/json" \
  -d '{
    "user_name": "sample_user",
    "portfolio_name": "Growth_Portfolio",
    "account_id": "JD-GROWTH"
    "first_name": "John",
    "last_name": "Doe",
    "csv_string": "date,ticker,weights,units_held,units_cost,currency\n2024-01-01,NVDA,0.10,100,150.00,USD\n..."
  }'
```

---

## 📤 Response

### 201 Created (Create)
```json
{
  "status": "success",
  "message": "Successfully created portfolio",
  "account_id": "abcd-1234",
  "portfolio_name": "Growth_Portfolio",
  "portfolio_id": "uuid-3979",
  "user_id": "uuid-5678",
  "username": "sample_user"
}
```

### 200 OK (Update)
```json
{
  "status": "success",
  "message": "Successfully updated portfolio",
  "account_id": "abcd-1234",
  "portfolio_name": "Growth_Portfolio",
  "portfolio_id": "uuid-3979",
  "user_id": "uuid-5678",
  "username": "sample_user"
}
```

### Error Responses

| HTTP Code | Description                            |
|-----------|----------------------------------------|
| 400       | Bad request (invalid input)            |
| 401       | Unauthorized (missing or invalid API key) |
| 404       | Not found (account_id does not exist)  |
| 500       | Internal server error                  |

---

## 📝 Notes

- The `units_held` and `units_cost` fields are **required** for the **latest** holding, and **optional** for historical holdings.
- For each rebalance date (same `date`), all `weights` must sum to 1.
- Use ticker `$TJCASH` to represent cash holdings.
- Dates must follow ISO format `YYYY-MM-DD`.
- After adding, please visit https://web-preview.tenjin-ai.com/ and sign in with the same email used in API call. 
- Please allow up to 10 minutes for the portfolio to appear in the web interface. 