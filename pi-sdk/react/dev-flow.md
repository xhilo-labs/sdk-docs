# 🚀 Developer Flow Guide

This guide walks you through implementing Pi Network payments and ads in your application, from basic setup to advanced features.

## 📋 Table of Contents

- [Quick Start](#quick-start)
- [Payment Implementation](#payment-implementation)
- [Ads Implementation](#ads-implementation)
- [Advanced Features](#advanced-features)
- [Production Deployment](#production-deployment)
- [Troubleshooting](#troubleshooting)

## 🚀 Quick Start

### 1. Install the SDK

```bash
npm install @xhilo/pi-sdk
```

### 2. Basic Setup

```typescript
import { useXhiloPiNetwork, usePiPayments, usePiAds } from '@xhilo/pi-sdk/react';

function App() {
  const { initialize, authenticate, user, isInitialized } = useXhiloPiNetwork();
  
  // Handle authentication and initialization
  useEffect(() => {
    if (!isInitialized) {
      initialize();
    }
  }, [isInitialized, initialize]);

  if (!user) {
    return <LoginScreen onLogin={authenticate} />;
  }

  return <YourApp user={user} />;
}
```

### 3. Environment Variables

```bash
# Frontend (.env.local)
REACT_APP_PI_APP_ID=your_app_id

# Backend (.env)
PI_API_KEY=your_api_key
PI_WALLET_PRIVATE_SEED=your_wallet_seed
```

## 💰 Payment Implementation

### User-to-App (U2A) Payments

U2A payments allow users to pay your app for services, products, or features.

#### Frontend Implementation

```typescript
import { usePiPayments } from '@xhilo/pi-sdk/react';

function PaymentButton() {
  const { createAndCompletePayment, isProcessing, error, success } = usePiPayments();

  const handlePayment = async () => {
    try {
      const result = await createAndCompletePayment({
        userId: 'user123',
        amount: 1.0,
        memo: 'Premium feature access',
        metadata: { feature: 'premium' },
        onSuccess: (paymentId) => {
          console.log('Payment completed successfully:', paymentId);
        },
        onError: (error) => {
          console.error('Payment failed:', error);
        },
        onCancel: () => {
          console.log('Payment cancelled by user');
        }
      });

      if (result.success) {
        console.log('Payment initiated:', result.data);
      }
    } catch (error) {
      console.error('Payment failed:', error);
    }
  };

  return (
    <button onClick={handlePayment} disabled={isProcessing}>
      {isProcessing ? 'Processing...' : 'Pay 1 Pi'}
    </button>
  );
}
```

#### Backend Implementation

```typescript
// pages/api/payments/approve.ts
import { approvePaymentAction, PiPlatformConfig } from '@xhilo/pi-sdk/backend';

const piPlatformConfig: PiPlatformConfig = {
  apiKey: process.env.PI_API_KEY!
};

export default async function handler(req, res) {
  if (req.method !== 'POST') {
    return res.status(405).json({ message: 'Method not allowed' });
  }

  const { paymentId, userId } = req.body;

  try {
    const result = await approvePaymentAction(
      { paymentId, userId },
      piPlatformConfig
    );

    if (result.success) {
      res.status(200).json(result);
    } else {
      res.status(400).json(result);
    }
  } catch (error) {
    res.status(500).json({ 
      success: false, 
      message: error.message 
    });
  }
}
```

### App-to-User (A2U) Payments

A2U payments allow your app to pay users (rewards, refunds, etc.).

#### Backend Implementation

```typescript
// pages/api/payments/create-reward.ts
import { createAndMakeA2UPayment, PiBackendConfig } from '@xhilo/pi-sdk/backend';

const piBackendConfig: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!
};

export default async function handler(req, res) {
  if (req.method !== 'POST') {
    return res.status(405).json({ message: 'Method not allowed' });
  }

  const { userId, amount, memo } = req.body;

  try {
    const result = await createAndMakeA2UPayment(
      userId,
      amount,
      memo,
      piBackendConfig,
      { type: 'reward' }
    );

    if (result.success) {
      res.status(200).json({
        success: true,
        message: 'Reward payment created and submitted',
        data: result.data
      });
    } else {
      res.status(400).json(result);
    }
  } catch (error) {
    res.status(500).json({ 
      success: false, 
      message: error.message 
    });
  }
}
```

## 📺 Ads Implementation

### Frontend-Only Ads (Simple)

Perfect for basic ad display without rewards.

```typescript
import { usePiAdsSimple } from '@xhilo/pi-sdk/react';

function SimpleAdButton() {
  const { showAd, isAdReady, isProcessing, error } = usePiAdsSimple();

  const handleShowAd = async () => {
    try {
      // Check if ad is ready
      const ready = await isAdReady('rewarded');
      
      if (ready.success && ready.data.isReady) {
        // Show the ad
        const result = await showAd('rewarded');
        
        if (result.success) {
          console.log('Ad shown successfully:', result.data);
        }
      } else {
        console.log('Ad not ready yet');
      }
    } catch (error) {
      console.error('Ad error:', error);
    }
  };

  return (
    <div>
      <button onClick={handleShowAd} disabled={isProcessing}>
        {isProcessing ? 'Loading...' : 'Show Ad'}
      </button>
      {error && <p style={{ color: 'red' }}>Error: {error}</p>}
    </div>
  );
}
```

### Full Ads with Backend (Advanced)

Includes reward processing and ad verification.

```typescript
import { usePiAds } from '@xhilo/pi-sdk/react';

function FullAdButton() {
  const { 
    showAd, 
    isAdReady, 
    checkEligibility, 
    getAdStats,
    isProcessing, 
    error 
  } = usePiAds();

  const handleShowAd = async () => {
    try {
      // Check eligibility
      const eligibility = await checkEligibility('rewarded');
      
      if (!eligibility.success || !eligibility.data.eligible) {
        console.log('User not eligible for ads');
        return;
      }

      // Check if ad is ready
      const ready = await isAdReady('rewarded');
      
      if (ready.success && ready.data.isReady) {
        // Show the ad (automatically verifies with backend)
        const result = await showAd('rewarded');
        
        if (result.success) {
          console.log('Ad shown and verified:', result.data);
          
          // Get updated stats
          const stats = await getAdStats();
          console.log('Ad stats:', stats.data);
        }
      }
    } catch (error) {
      console.error('Ad error:', error);
    }
  };

  return (
    <div>
      <button onClick={handleShowAd} disabled={isProcessing}>
        {isProcessing ? 'Loading...' : 'Show Ad & Get Reward'}
      </button>
      {error && <p style={{ color: 'red' }}>Error: {error}</p>}
    </div>
  );
}
```

#### Backend Ads API

```typescript
// pages/api/ads/verify-rewarded.ts
import { verifyRewardedAdAction, PiBackendConfig } from '@xhilo/pi-sdk/backend';

const piBackendConfig: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!
};

export default async function handler(req, res) {
  if (req.method !== 'POST') {
    return res.status(405).json({ message: 'Method not allowed' });
  }

  const { adId, userId, rewardAmount, metadata } = req.body;

  try {
    const result = await verifyRewardedAdAction(
      { adId, userId, rewardAmount, metadata },
      piBackendConfig
    );

    if (result.success) {
      res.status(200).json(result);
    } else {
      res.status(400).json(result);
    }
  } catch (error) {
    res.status(500).json({ 
      success: false, 
      message: error.message 
    });
  }
}
```

## 🔧 Advanced Features

### Custom Payment Callbacks

```typescript
import { usePiPayments } from '@xhilo/pi-sdk/react';

function AdvancedPaymentButton() {
  const { createAndCompletePayment } = usePiPayments();

  const handlePayment = async () => {
    const result = await createAndCompletePayment({
      userId: 'user123',
      amount: 5.0,
      memo: 'Premium subscription',
      metadata: { plan: 'premium', duration: 'monthly' },
      onSuccess: (paymentId) => {
        console.log('Payment completed successfully:', paymentId);
      },
      onError: (error) => {
        console.error('Payment error:', error);
      },
      onCancel: () => {
        console.log('Payment cancelled by user');
      },
      onProcessUpdate: (processData) => {
        console.log('Payment process update:', processData);
      }
    });
  };

  return <button onClick={handlePayment}>Subscribe Premium</button>;
}
```

### Custom Ads Functions

```typescript
// Backend: Custom ads implementation
import { verifyRewardedAdAction, PiBackendConfig } from '@xhilo/pi-sdk/backend';

const piBackendConfig: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!
};

// Use the action with proper configuration
const result = await verifyRewardedAdAction(
  { 
    adId: 'ad123',
    userId: 'user123', 
    rewardAmount: 0.1, 
    metadata: { source: 'mobile' } 
  },
  piBackendConfig
);
```

### Error Handling

```typescript
import { usePiPayments, usePiAds } from '@xhilo/pi-sdk/react';

function ErrorHandlingExample() {
  const { createAndCompletePayment } = usePiPayments();
  const { showAd } = usePiAds();

  const handlePayment = async () => {
    try {
      const result = await createAndCompletePayment({
        userId: 'user123',
        amount: 1.0,
        memo: 'Test payment',
        onError: (error) => {
          // Handle specific error types
          switch (error) {
            case 'User not authenticated':
              // Redirect to login
              break;
            case 'Insufficient balance':
              // Show balance error
              break;
            case 'Payment creation failed':
              // Retry or show error
              break;
            default:
              // Generic error handling
              console.error('Payment failed:', error);
          }
        }
      });

      if (!result.success) {
        console.error('Payment failed:', result.message);
      }
    } catch (error) {
      console.error('Unexpected error:', error);
    }
  };

  return <button onClick={handlePayment}>Test Payment</button>;
}
```

## 🚀 Production Deployment

### 1. Environment Setup

```bash
# Production environment variables
PI_API_KEY=your_production_api_key
PI_WALLET_PRIVATE_SEED=your_production_wallet_seed
REACT_APP_PI_APP_ID=your_production_app_id
```

### 2. Security Considerations

- ✅ **Validate Access Tokens** - Always verify user identity on backend
- ✅ **Rate Limiting** - Implement rate limits for API endpoints
- ✅ **Error Logging** - Log errors for monitoring
- ✅ **Input Validation** - Validate all user inputs
- ✅ **HTTPS Only** - Use HTTPS in production

### 3. Monitoring

```typescript
// Add monitoring to your API routes
export default async function handler(req, res) {
  const startTime = Date.now();
  
  try {
    // Your API logic
    const result = await yourApiLogic();
    
    // Log success
    console.log('API success:', {
      endpoint: req.url,
      duration: Date.now() - startTime,
      userId: req.body.userId
    });
    
    res.status(200).json(result);
  } catch (error) {
    // Log error
    console.error('API error:', {
      endpoint: req.url,
      duration: Date.now() - startTime,
      error: error.message,
      stack: error.stack
    });
    
    res.status(500).json({ 
      success: false, 
      message: 'Internal server error' 
    });
  }
}
```

## 🔍 Troubleshooting

### Common Issues

#### 1. "User not authenticated" Error
```typescript
// Check if user is properly authenticated
const { user, isAuthenticated, hasScope } = useXhiloPiNetwork();

if (!isAuthenticated) {
  console.log('User not authenticated, redirecting to login');
  // Redirect to Pi Browser login
}

// Check if user has required scopes
if (!hasScope('payments')) {
  console.log('User missing payments scope');
  // Request additional scopes
}
```

#### 2. "Payment creation failed" Error
```typescript
// Check if user is authenticated and has required scopes
const { user, isAuthenticated, hasScope } = useXhiloPiNetwork();

if (!isAuthenticated) {
  console.error('User not authenticated');
}

if (!hasScope('payments')) {
  console.error('User missing payments scope');
}

// Check user data
console.log('User data:', user);
```

#### 3. "Ad not ready" Error
```typescript
// Check ad readiness
const { isAdReady } = usePiAds();

const checkAd = async () => {
  const ready = await isAdReady('rewarded');
  if (ready.success) {
    console.log('Ad ready:', ready.data.isReady);
    console.log('Ad reason:', ready.data.reason);
  } else {
    console.error('Ad check failed:', ready.message);
  }
};
```

#### 4. Backend API Errors
```typescript
// Check backend configuration
if (!process.env.PI_API_KEY) {
  console.error('PI_API_KEY not set in backend');
}

if (!process.env.PI_WALLET_PRIVATE_SEED) {
  console.error('PI_WALLET_PRIVATE_SEED not set in backend');
}

// Ensure proper configuration is passed to actions
const piBackendConfig: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!
};
```

### Debug Mode

```typescript
// Enable debug logging
const { createAndCompletePayment } = usePiPayments();

const handlePayment = async () => {
  // Enable debug mode
  console.log('Debug mode enabled');
  
  const result = await createAndCompletePayment({
    userId: 'user123',
    amount: 1.0,
    memo: 'Debug payment',
    metadata: { debug: true },
    onSuccess: (paymentId) => console.log('Payment success:', paymentId),
    onError: (error) => console.error('Payment error:', error),
    onCancel: () => console.log('Payment cancelled')
  });
  
  console.log('Payment result:', result);
};
```

## 📚 Additional Resources

- [API Reference](./api-reference.md) - Complete API documentation
- [Templates Guide](./templates.md) - Using the included templates
- [Examples](./examples.md) - Real-world examples
- [Troubleshooting](./troubleshooting.md) - Common issues and solutions

## 🤝 Support

If you encounter issues not covered in this guide:

1. Check the [Troubleshooting Guide](./troubleshooting.md)
2. Review the [API Reference](./api-reference.md)
3. Check the [Examples](./examples.md)
4. Open an issue on [GitHub](https://github.com/xhilo-labs/pi-sdk)

---

**Happy coding! 🚀**
