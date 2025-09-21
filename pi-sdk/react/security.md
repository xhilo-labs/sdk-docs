# Security Guide

This guide covers security best practices and access token validation for the Pi SDK.

## 🔐 Access Token Validation

### **Overview**
The Pi SDK provides **optional access token validation** for enhanced security in User-to-App (U2A) payments. This ensures that only authenticated Pi Network users can initiate payments and prevents malicious users from tampering with payment requests.

### **How It Works**

1. **Frontend Authentication**: User authenticates in Pi Browser and receives access token
2. **Automatic Token Sending**: Frontend automatically includes access token in API requests
3. **Backend Validation**: Backend optionally validates token with Pi Platform API
4. **User Verification**: Backend verifies user identity matches payment request

## 🚀 Implementation

### **Frontend (Automatic)**
The `usePiPayments` hook automatically handles access token validation:

```tsx
import { usePiPayments } from '@xhilo/pi-sdk';

function PaymentComponent() {
  const { createAndCompletePayment } = usePiPayments();
  
  const handlePayment = async () => {
    // Access token is automatically sent in Authorization header
    const result = await createAndCompletePayment({
      userId: 'user123',
      amount: 10.0,
      memo: 'Test payment'
    });
  };
}
```

### **Backend (Optional)**
Your backend can optionally validate the access token:

```typescript
import { NextRequest, NextResponse } from 'next/server';
import { approvePaymentAction } from '@xhilo/pi-sdk/backend';

export async function POST(request: NextRequest) {
  const body = await request.json();
  const { paymentId, userId } = body;
  
  // Extract access token from Authorization header
  const authHeader = request.headers.get('authorization');
  const accessToken = authHeader?.replace('Bearer ', '');
  
  // Optional: Validate access token
  if (accessToken) {
    try {
      const userInfo = await getMeAction(accessToken);
      
      // Verify user identity matches payment request
      if (userInfo.data.uid !== userId) {
        return NextResponse.json(
          { success: false, message: 'User ID mismatch with authenticated user' },
          { status: 401 }
        );
      }
    } catch (error) {
      return NextResponse.json(
        { success: false, message: 'Invalid access token. Please re-authenticate' },
        { status: 401 }
      );
    }
  }
  
  // Process payment with or without token validation
  const result = await approvePaymentAction({ paymentId, userId }, accessToken);
  
  return NextResponse.json(result);
}
```

## 🛡️ Security Benefits

### **1. User Identity Verification**
```typescript
// Ensures the user making the payment is who they claim to be
const userInfo = await getMeAction(accessToken);
if (userInfo.data.uid !== userId) {
  // Reject payment - user ID mismatch
}
```

### **2. Token Tampering Prevention**
```typescript
// Pi Platform API validates token authenticity
// Invalid or tampered tokens will fail validation
try {
  const userInfo = await getMeAction(accessToken);
  // Token is valid
} catch (error) {
  // Token is invalid or tampered
}
```

### **3. Malicious User Prevention**
```typescript
// Only real Pi Network users can get valid access tokens
// Malicious users cannot fake payment requests
```

## 🔧 Configuration

### **Environment Variables**
Your backend needs the appropriate environment variables:

```bash
# For U2A payments (User-to-App)
PI_APP_SECRET=your_pi_app_secret_key

# For A2U payments (App-to-User)
PI_API_KEY=your_pi_api_key
PI_WALLET_PRIVATE_SEED=your_wallet_private_seed
```

### **Optional vs Required Validation**
- **Optional**: Backend can work with or without access token validation
- **Backward Compatible**: Existing implementations continue to work
- **Enhanced Security**: New implementations can enable validation

## 📊 Payment Flow Security

### **U2A Payments (User-to-App)**
```
User → Frontend → Backend → Pi Platform API
 ├── User authenticates → Gets access token
 ├── Frontend sends token → Backend validates (optional)
 ├── Backend verifies user → Pi Platform API
 └── Payment processed → Secure transaction
```

### **A2U Payments (App-to-User)**
```
App → Backend → pi-backend SDK → Pi Network
 ├── App initiates payment → No token needed
 ├── Backend creates payment → pi-backend SDK
 └── Payment processed → Direct to Pi Network
```

## ⚠️ Security Considerations

### **1. Token Storage**
- **Frontend**: Tokens are stored in memory during session
- **Backend**: Never store access tokens permanently
- **Transmission**: Always use HTTPS for token transmission

### **2. Error Handling**
```typescript
// Handle token validation errors gracefully
try {
  const userInfo = await getMeAction(accessToken);
} catch (error) {
  // Log error for debugging
  console.error('Token validation failed:', error);
  
  // Return user-friendly error
  return NextResponse.json(
    { success: false, message: 'Authentication failed. Please try again.' },
    { status: 401 }
  );
}
```

### **3. Rate Limiting**
Consider implementing rate limiting for payment endpoints to prevent abuse:

```typescript
// Example rate limiting
const rateLimit = new Map();

export async function POST(request: NextRequest) {
  const clientIP = request.ip;
  const now = Date.now();
  const windowMs = 60000; // 1 minute
  const maxRequests = 10;
  
  if (rateLimit.has(clientIP)) {
    const requests = rateLimit.get(clientIP);
    if (requests.length >= maxRequests && now - requests[0] < windowMs) {
      return NextResponse.json(
        { success: false, message: 'Too many requests' },
        { status: 429 }
      );
    }
  }
  
  // Process payment...
}
```

## 🧪 Testing Security

### **1. Test Valid Tokens**
```typescript
// Test with valid access token
const validToken = 'valid_access_token_from_pi_browser';
const result = await getMeAction(validToken);
// Should succeed
```

### **2. Test Invalid Tokens**
```typescript
// Test with invalid access token
const invalidToken = 'fake_token_123';
try {
  await getMeAction(invalidToken);
} catch (error) {
  // Should fail with 401 error
}
```

### **3. Test Token Tampering**
```typescript
// Test with tampered token
const tamperedToken = 'valid_token_but_modified';
try {
  await getMeAction(tamperedToken);
} catch (error) {
  // Should fail with 401 error
}
```

## 📚 Related Documentation

- [usePiPayments Hook](./usePiPayments.md) - Frontend payment implementation
- [Backend Actions](../backend/actions.md) - Backend payment processing
- [Environment Setup](./getting-started.md) - Configuration guide
- [Troubleshooting](./troubleshooting.md) - Common issues and solutions

## 🆘 Support

If you encounter security issues or have questions:

1. **Check the logs** for detailed error messages
2. **Verify environment variables** are correctly set
3. **Test with sandbox mode** before production
4. **Contact support** if issues persist

---

*Security is a shared responsibility. Always follow best practices and keep your implementation updated.*
