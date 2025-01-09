# Enterprise Authentication API Documentation
*Author: Ronak Tanna*

## Table of Contents
- [Overview](#overview)
- [Base URLs](#base-urls)
- [Authentication](#authentication)
- [Endpoints](#endpoints)
  - [1. Generate Access Token](#1-generate-access-token)
  - [2. Validate Token](#2-validate-token)
  - [3. Refresh Token](#3-refresh-token)
- [Code Examples](#code-examples)
  - [cURL Examples](#curl-examples)
  - [Python](#python)
  - [Node.js](#nodejs)
- [Error Handling](#error-handling)
- [Rate Limiting](#rate-limiting)
- [Security Considerations](#security-considerations)
- [SDK Support](#sdk-support)
- [Additional Resources](#additional-resources)

## Overview
The Enterprise Authentication API provides secure user authentication and authorization services for enterprise applications. This RESTful API supports OAuth 2.0 and JWT tokens, enabling seamless integration with existing security infrastructure while maintaining enterprise-grade security standards and best practices.

## Base URLs

| Environment | URL |
|------------|-----|
| Production | `https://api.auth.enterprise.com/v2` |
| Staging | `https://api.staging.auth.enterprise.com/v2` |

## Authentication

All API requests require authentication using a valid API key included in the Authorization header:

```http
Authorization: Bearer your-api-key
```

## Endpoints

### 1. Generate Access Token

Creates a new access token for authenticated users.

#### HTTP Request
`POST /auth/token`

#### Headers
```http
Content-Type: application/json
```

#### Request Body Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| username | string | Yes | User's email OR username |
| password | string | Yes | User's password |
| client_id | string | Yes | Your application's client ID |
| grant_type | string | Yes | Must be "password" for this flow |

#### Example Request
```json
{
  "username": "user@enterprise.com",
  "password": "securePassword123",
  "client_id": "your-client-id",
  "grant_type": "password"
}
```

#### cURL Example
```bash
curl -X POST https://api.auth.enterprise.com/v2/auth/token \
  -H "Content-Type: application/json" \
  -d '{
    "username": "user@enterprise.com",
    "password": "securePassword123",
    "client_id": "your-client-id",
    "grant_type": "password"
  }'
```

#### Success Response (200 OK)
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "8xLOxBtZp8"
}
```

### 2. Validate Token

Validates an existing access token.

#### HTTP Request
`GET /auth/validate`

#### Headers
```http
Authorization: Bearer your-access-token
```

#### cURL Example
```bash
curl -X GET https://api.auth.enterprise.com/v2/auth/validate \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

#### Success Response (200 OK)
```json
{
  "valid": true,
  "expires_in": 2400,
  "user_id": "usr_123456789"
}
```

### 3. Refresh Token

Obtains a new access token using a refresh token.

#### HTTP Request
`POST /auth/refresh`

#### Headers
```http
Content-Type: application/json
```

#### Request Body Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| refresh_token | string | Yes | Valid refresh token |
| client_id | string | Yes | Your application's client ID |

#### Example Request
```json
{
  "refresh_token": "8xLOxBtZp8",
  "client_id": "your-client-id"
}
```

#### cURL Example
```bash
curl -X POST https://api.auth.enterprise.com/v2/auth/refresh \
  -H "Content-Type: application/json" \
  -d '{
    "refresh_token": "8xLOxBtZp8",
    "client_id": "your-client-id"
  }'
```

#### Success Response (200 OK)
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "9yMPxCuZq9"
}
```

## Code Examples

### cURL Examples

#### Using Environment Variables
For better security and reusability, you can use environment variables with your cURL commands:

```bash
# Set your environment variables
export AUTH_API_KEY="your-api-key"
export CLIENT_ID="your-client-id"
export AUTH_API_URL="https://api.auth.enterprise.com/v2"

# Generate token using environment variables
curl -X POST "${AUTH_API_URL}/auth/token" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${AUTH_API_KEY}" \
  -d '{
    "username": "user@enterprise.com",
    "password": "securePassword123",
    "client_id": "'"${CLIENT_ID}"'",
    "grant_type": "password"
  }'
```

#### Testing Error Responses

##### Invalid Credentials
```bash
curl -X POST https://api.auth.enterprise.com/v2/auth/token \
  -H "Content-Type: application/json" \
  -d '{
    "username": "invalid@enterprise.com",
    "password": "wrongpassword",
    "client_id": "your-client-id",
    "grant_type": "password"
  }'
```

##### Invalid Token
```bash
curl -X GET https://api.auth.enterprise.com/v2/auth/validate \
  -H "Authorization: Bearer invalid_token"
```

### Python

```python
import requests

def get_access_token(username, password, client_id):
    url = "https://api.auth.enterprise.com/v2/auth/token"
    payload = {
        "username": username,
        "password": password,
        "client_id": client_id,
        "grant_type": "password"
    }
    response = requests.post(url, json=payload)
    return response.json()
```

### Node.js

```javascript
const axios = require('axios');

async function getAccessToken(username, password, clientId) {
  const url = 'https://api.auth.enterprise.com/v2/auth/token';
  const payload = {
    username,
    password,
    client_id: clientId,
    grant_type: 'password'
  };
  
  try {
    const response = await axios.post(url, payload);
    return response.data;
  } catch (error) {
    throw error;
  }
}
```

## Error Handling

### Error Response Format

```json
{
  "error": "error_code",
  "message": "Human-readable error message",
  "request_id": "req_abc123"
}
```

### Common Error Codes

| Error Code | HTTP Status | Description |
|------------|-------------|-------------|
| invalid_credentials | 401 | Provided credentials are incorrect |
| invalid_token | 401 | Token is invalid or expired |
| invalid_client | 401 | Client ID is invalid |
| rate_limit_exceeded | 429 | Too many requests |
| server_error | 500 | Internal server error |

## Rate Limiting

The API implements rate limiting to ensure service stability:

* 100 requests per minute per IP address
* 1000 requests per hour per client ID

Rate limit headers are included in all responses:

```http
X-RateLimit-Minutes-Limit: 1000
X-RateLimit-Minutes-Remaining: 995
X-RateLimit-Hour-Limit: 100
X-RateLimit-Hour-Remaining: 95
X-RateLimit-Reset: 1640995200
```

| Header | Description |
|--------|-------------|
| X-RateLimit-Minutes-Limit | Number of requests allowed per minute for the requesting IP address |
| X-RateLimit-Minutes-Remaining | Number of requests remaining per minute for the IP address |
| X-RateLimit-Hour-Limit | Number of requests allowed per hour for your client ID |
| X-RateLimit-Hour-Remaining | Number of requests remaining per hour for the client ID |
| X-RateLimit-Reset | UNIX timestamp when the rate limit resets |

## Security Considerations

### 1. SSL/TLS Required
* All API requests must be made over HTTPS
* Requests over plain HTTP will fail

### 2. Token Security
* Store tokens securely
* Never expose tokens in client-side code
* Refresh tokens automatically before expiration

### 3. Best Practices
* Implement exponential backoff for failed requests
* Rotate client secrets regularly
* Monitor token usage for suspicious activity

## SDK Support

Official SDKs are available for:

* Python
* Node.js
* Java
* Go
* .NET

## Additional Resources

* [API Changelog](https://docs.enterprise.com/api/changelog)
* [Security Best Practices](https://docs.enterprise.com/security)
* [SDK Documentation](https://docs.enterprise.com/sdks)

---