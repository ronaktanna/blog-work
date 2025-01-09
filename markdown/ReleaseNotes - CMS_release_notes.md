# ContentHub API Platform - Release Notes
*Author: Ronak Tanna*<br>

## Version 3.2.0 (December 2024)
*Release Date: December 15, 2024*

### 🚀 New Features

#### Asset Management Pipeline
- Introduced new image transformation endpoints for real-time image processing
- Added support for WebP format in asset optimization API
- Implemented bulk asset tagging with AI-powered auto-tagging capability

```javascript
// New image transformation example
const response = await contenthub.assets.transform({
  assetId: "asset_123",
  operations: [
    { type: "resize", width: 800, height: 600 },
    { type: "format", output: "webp", quality: 85 }
  ]
});
```

#### Content Delivery Network
- Added edge caching support for dynamic content
- Introduced regional content routing for improved latency
- Implemented automatic SSL certificate management

### 🔄 Improvements

#### Performance Optimization
- Reduced API response time by 40% through query optimization
- Implemented connection pooling for database operations
- Added request batching support for bulk operations

```python
# New batch operation example
batch_response = contenthub.batch([
    {"method": "GET", "path": "/v3/assets/asset_123"},
    {"method": "GET", "path": "/v3/assets/asset_456"},
    {"method": "GET", "path": "/v3/assets/asset_789"}
])
```

#### Security Enhancements
- Added support for short-lived access tokens
- Implemented IP-based rate limiting
- Enhanced audit logging with detailed request tracking

### 🐛 Bug Fixes
- Fixed race condition in concurrent asset uploads
- Resolved memory leak in long-running webhook connections
- Fixed incorrect content-type headers in binary file downloads

### ⚠️ Breaking Changes

#### Authentication Updates
The authentication mechanism has been updated to enhance security. Starting from this version:
- API key format has changed
- Token expiration is now enforced
- Refresh token rotation is mandatory

**Migration Guide:**
1. Update your authentication headers:
   ```diff
   - Authorization: ApiKey your-api-key
   + Authorization: Bearer your-jwt-token
   ```

2. Implement token refresh logic:
   ```javascript
   // New token refresh implementation
   const refreshToken = async () => {
     const response = await contenthub.auth.refresh({
       refresh_token: currentRefreshToken
     });
     return response.access_token;
   };
   ```

#### Deprecated Endpoints
The following endpoints will be removed in version 4.0.0:
- `/v2/assets/upload` - Use `/v3/assets/upload` instead
- `/v2/media/transform` - Use `/v3/assets/transform` instead

### 📚 Documentation Updates
- Added new tutorial for image transformation pipeline
- Updated authentication documentation with security best practices
- Added performance optimization guide
- Updated SDK documentation for all supported languages

### 🛠️ Developer Tools
- Released new version of Content Management CLI (v2.1.0)
- Updated Postman collection with new endpoints
- Added new code snippets in SDK documentation

## Upcoming in 4.0.0
- GraphQL API (Beta)
- Real-time collaboration features
- Enhanced search capabilities with AI
- Asset versioning system

## Getting Help
- Documentation: [docs.contenthub.developer.com](https://docs.contenthub.developer.com)
- Support: [docs.contenthub.developer.com](https://support.contenthub.developer.com)
- Community: [docs.contenthub.developer.com](https://community.contenthub.developer.com)

---

## Version 3.1.2 (November 2024)
*Release Date: November 28, 2024*

### 🔒 Security Updates
- Patched XSS vulnerability in content preview endpoint
- Updated dependencies to address security vulnerabilities
- Enhanced input validation for file upload endpoints

### 🐛 Bug Fixes
- Fixed incorrect content ordering in collection endpoints
- Resolved asset metadata synchronization issues
- Fixed rate limiting calculation for burst requests

---

## Version 3.1.1 (November 2024)
*Release Date: November 15, 2024*

### 🔄 Improvements
- Optimized database queries for content retrieval
- Enhanced caching mechanism for frequently accessed content
- Improved error messages for validation failures

### 🐛 Bug Fixes
- Fixed pagination issues in search results
- Resolved timezone handling in scheduling endpoints
- Fixed memory leak in webhook connections