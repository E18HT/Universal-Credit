# Universal Credit API Documentation

## Overview

The Universal Credit API provides comprehensive endpoints for managing UC token operations, price feeds, reserve management, and user interactions. Built with Flask and designed for high performance and reliability.

## Base URL

```
Production: https://api.universalcredit.dev
Development: http://localhost:5000
```

## Authentication

Currently, the API operates without authentication for demonstration purposes. In production, all endpoints will require API key authentication.

```http
Authorization: Bearer YOUR_API_KEY
```

## Rate Limiting

- **Development**: No rate limiting
- **Production**: 1000 requests per hour per IP
- **Premium**: 10000 requests per hour with API key

## Endpoints

### Price Information

#### Get Current Prices
Retrieve real-time prices for UC token, Bitcoin, and Gold.

```http
GET /api/uc/prices
```

**Response:**
```json
{
  "uc_price": 1.2547,
  "btc_price": 55234.67,
  "gold_price": 2198.45,
  "timestamp": 1642678901
}
```

#### Get Price History
Retrieve historical price data for charting and analysis.

```http
GET /api/uc/price-history
```

**Query Parameters:**
- `days` (optional): Number of days of history (default: 30, max: 365)
- `interval` (optional): Data interval - "1h", "1d", "1w" (default: "1d")

**Response:**
```json
{
  "history": [
    {
      "date": "2025-01-01",
      "uc_price": 1.2000,
      "btc_price": 52000.00,
      "gold_price": 2150.00
    },
    {
      "date": "2025-01-02",
      "uc_price": 1.2100,
      "btc_price": 52500.00,
      "gold_price": 2160.00
    }
  ]
}
```

### Currency Operations

#### Get Supported Currencies
Retrieve list of all supported currencies for UC conversion.

```http
GET /api/uc/currencies
```

**Response:**
```json
{
  "currencies": [
    {
      "code": "USD",
      "name": "US Dollar",
      "rate": 1.00
    },
    {
      "code": "NGN",
      "name": "Nigerian Naira",
      "rate": 1650
    }
  ]
}
```

#### Convert Currency to UC
Calculate UC token amount for given local currency input.

```http
POST /api/uc/convert
Content-Type: application/json
```

**Request Body:**
```json
{
  "amount": 200,
  "currency": "NGN"
}
```

**Response:**
```json
{
  "local_amount": 200,
  "local_currency": "NGN",
  "usd_amount": 0.12,
  "uc_amount": 0.095673,
  "uc_price": 1.2547,
  "exchange_rate": 1650
}
```

### Transaction Operations

#### Send UC Tokens
Initiate a UC token transfer transaction.

```http
POST /api/uc/send
Content-Type: application/json
```

**Request Body:**
```json
{
  "amount": 200,
  "currency": "USD",
  "recipient": "family@example.com",
  "message": "Monthly support"
}
```

**Response:**
```json
{
  "transaction_id": "UC1642678901234",
  "status": "success",
  "local_amount": 200,
  "local_currency": "USD",
  "uc_amount": 159.4236,
  "recipient": "family@example.com",
  "fee": 0.20,
  "estimated_arrival": "3-5 seconds",
  "timestamp": "2025-01-20T10:30:00Z"
}
```

### Reserve Management

#### Get Reserve Information
Retrieve current reserve composition and health metrics.

```http
GET /api/uc/reserves
```

**Response:**
```json
{
  "bitcoin": {
    "value_usd": 60000000,
    "percentage": 60.0,
    "amount_btc": 1086.96
  },
  "gold": {
    "value_usd": 40000000,
    "percentage": 40.0,
    "amount_ounces": 18181.82
  },
  "total_value": 100000000,
  "reserve_ratio": 105.2,
  "last_rebalance": "2 hours ago",
  "needs_rebalance": false
}
```

### Financial Tools

#### Calculate Collateral Value
Calculate borrowing power using UC tokens as collateral.

```http
POST /api/uc/collateral-calculator
Content-Type: application/json
```

**Request Body:**
```json
{
  "uc_amount": 100,
  "platform": "defi"
}
```

**Response:**
```json
{
  "uc_amount": 100,
  "collateral_value_usd": 125.47,
  "max_borrow_usd": 94.10,
  "ltv_ratio": 75,
  "interest_rate": 8.5,
  "platform": "defi",
  "liquidation_price": 1.0038
}
```

#### Calculate Remittance Impact
Compare wealth building impact of UC vs traditional remittances.

```http
POST /api/uc/remittance-impact
Content-Type: application/json
```

**Request Body:**
```json
{
  "monthly_amount": 200,
  "currency": "USD",
  "years": 3
}
```

**Response:**
```json
{
  "monthly_amount": 200,
  "currency": "USD",
  "years": 3,
  "traditional_scenario": {
    "total_sent": 7200.00,
    "final_value": 4406.40,
    "value_lost": 2793.60
  },
  "uc_scenario": {
    "total_sent": 7200.00,
    "final_value": 11808.00,
    "value_gained": 4608.00
  },
  "comparison": {
    "wealth_difference": 7401.60,
    "percentage_improvement": 168.0,
    "monthly_savings_equivalent": 205.60
  }
}
```

### Platform Statistics

#### Get Platform Stats
Retrieve overall platform performance and usage statistics.

```http
GET /api/uc/stats
```

**Response:**
```json
{
  "total_users": 12547,
  "total_transactions": 89234,
  "total_volume_usd": 45678901,
  "countries_served": 23,
  "average_transaction_size": 247,
  "growth_rate_monthly": 28.5,
  "reserve_health": 105.2,
  "uptime": 99.97
}
```

#### Health Check
Simple endpoint to verify API availability.

```http
GET /api/uc/health
```

**Response:**
```json
{
  "status": "healthy",
  "service": "Universal Credit API",
  "version": "1.0.0",
  "timestamp": "2025-01-20T10:30:00Z"
}
```

## Error Handling

The API uses conventional HTTP response codes to indicate success or failure.

### HTTP Status Codes

- `200` - OK: Request successful
- `400` - Bad Request: Invalid request parameters
- `401` - Unauthorized: Authentication required
- `403` - Forbidden: Insufficient permissions
- `404` - Not Found: Resource not found
- `429` - Too Many Requests: Rate limit exceeded
- `500` - Internal Server Error: Server error

### Error Response Format

```json
{
  "error": "Invalid currency code",
  "code": "INVALID_CURRENCY",
  "message": "The provided currency code 'XYZ' is not supported",
  "timestamp": "2025-01-20T10:30:00Z"
}
```

## WebSocket Endpoints

For real-time updates, the API provides WebSocket connections.

### Price Updates
```
ws://localhost:5000/ws/prices
```

**Message Format:**
```json
{
  "type": "price_update",
  "data": {
    "uc_price": 1.2547,
    "btc_price": 55234.67,
    "gold_price": 2198.45,
    "timestamp": 1642678901
  }
}
```

### Transaction Updates
```
ws://localhost:5000/ws/transactions
```

**Message Format:**
```json
{
  "type": "transaction_update",
  "data": {
    "transaction_id": "UC1642678901234",
    "status": "confirmed",
    "confirmations": 1
  }
}
```

## SDK Examples

### JavaScript/Node.js

```javascript
const UCClient = require('@universal-credit/sdk');

const client = new UCClient({
  baseURL: 'http://localhost:5000',
  apiKey: 'your-api-key'
});

// Get current prices
const prices = await client.prices.getCurrent();
console.log(`UC Price: $${prices.uc_price}`);

// Convert currency
const conversion = await client.convert({
  amount: 200,
  currency: 'NGN'
});
console.log(`UC Amount: ${conversion.uc_amount}`);

// Send UC tokens
const transaction = await client.send({
  amount: 200,
  currency: 'USD',
  recipient: 'family@example.com'
});
console.log(`Transaction ID: ${transaction.transaction_id}`);
```

### Python

```python
from universal_credit import UCClient

client = UCClient(
    base_url='http://localhost:5000',
    api_key='your-api-key'
)

# Get current prices
prices = client.prices.get_current()
print(f"UC Price: ${prices['uc_price']}")

# Convert currency
conversion = client.convert(amount=200, currency='NGN')
print(f"UC Amount: {conversion['uc_amount']}")

# Send UC tokens
transaction = client.send(
    amount=200,
    currency='USD',
    recipient='family@example.com'
)
print(f"Transaction ID: {transaction['transaction_id']}")
```

### cURL Examples

**Get Current Prices:**
```bash
curl -X GET "http://localhost:5000/api/uc/prices" \
  -H "Accept: application/json"
```

**Convert Currency:**
```bash
curl -X POST "http://localhost:5000/api/uc/convert" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 200,
    "currency": "NGN"
  }'
```

**Send UC Tokens:**
```bash
curl -X POST "http://localhost:5000/api/uc/send" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 200,
    "currency": "USD",
    "recipient": "family@example.com"
  }'
```

## Testing

### Test Environment
```
Base URL: http://localhost:5000
```

### Sample Test Data

**Test Currencies:**
- USD: 1.00 (base rate)
- NGN: 1650 (Nigerian Naira)
- PHP: 56 (Philippine Peso)
- KES: 129 (Kenyan Shilling)

**Test Accounts:**
- Sender: test-sender@example.com
- Recipient: test-recipient@example.com

### Integration Testing

```javascript
// Jest test example
describe('UC API Integration', () => {
  test('should convert currency to UC', async () => {
    const response = await fetch('/api/uc/convert', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        amount: 200,
        currency: 'USD'
      })
    });
    
    const data = await response.json();
    expect(data.uc_amount).toBeGreaterThan(0);
    expect(data.local_currency).toBe('USD');
  });
});
```

## Changelog

### v1.0.0 (2025-01-20)
- Initial API release
- Core price and conversion endpoints
- Reserve management functionality
- Collateral calculation tools
- Remittance impact analysis

### Upcoming Features
- Authentication and API keys
- Advanced analytics endpoints
- Batch transaction processing
- Enhanced WebSocket support
- Mobile SDK development

## Support

For API support and questions:
- **Documentation**: [https://docs.universalcredit.dev](https://docs.universalcredit.dev)
- **Email**: api-support@universalcredit.dev
- **Discord**: [#api-support](https://discord.gg/universalcredit)
- **GitHub Issues**: [https://github.com/universal-credit/api/issues](https://github.com/universal-credit/api/issues)

