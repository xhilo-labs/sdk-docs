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
import { XhiloPiProvider, usePiPayments, usePiAds } from '@xhilo/pi-sdk';

function App() {
  return (
    <XhiloPiProvider>
      <YourApp />
    </XhiloPiProvider>
  );
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
import { usePiPayments } from '@xhilo/pi-sdk';

function PaymentButton() {
  const { createPayment, approvePayment, completePayment, isProcessing } = usePiPayments();

  const handlePayment = async () => {
    try {
      // 1. Create payment
      const payment = await createPayment({
        amount: 1.0,
        memo: 'Premium feature access',
        metadata: { feature: 'premium' }
      });

      if (payment.success) {
        console.log('Payment created:', payment.data);
        
        // 2. Approve payment (user confirms in Pi Browser)
        const approval = await approvePayment(payment.data.identifier);
        
        if (approval.success) {
          // 3. Complete payment (backend processes)
          const completion = await completePayment(payment.data.identifier);
          
          if (completion.success) {
            console.log('Payment completed successfully!');
          }
        }
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
import { approvePaymentAction } from '@xhilo/pi-sdk/backend';

export default async function handler(req, res) {
  if (req.method !== 'POST') {
    return res.status(405).json({ message: 'Method not allowed' });
  }

  const { paymentId } = req.body;
  const accessToken = req.headers.authorization?.replace('Bearer ', '');

  try {
    const result = await approvePaymentAction(
      { paymentId },
      undefined, // config
      accessToken
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
import { createPaymentAction, submitPaymentAction } from '@xhilo/pi-sdk/backend';

export default async function handler(req, res) {
  if (req.method !== 'POST') {
    return res.status(405).json({ message: 'Method not allowed' });
  }

  const { userId, amount, memo } = req.body;

  try {
    // 1. Create payment
    const createResult = await createPaymentAction({
      amount,
      memo,
      metadata: { type: 'reward' },
      uid: userId
    });

    if (!createResult.success) {
      return res.status(400).json(createResult);
    }

    // 2. Submit payment
    const submitResult = await submitPaymentAction({
      paymentId: createResult.data.identifier
    });

    if (submitResult.success) {
      res.status(200).json({
        success: true,
        message: 'Reward payment created and submitted',
        data: {
          paymentId: createResult.data.identifier,
          amount,
          status: 'submitted'
        }
      });
    } else {
      res.status(400).json(submitResult);
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
import { usePiAdsSimple } from '@xhilo/pi-sdk';

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
import { usePiAds } from '@xhilo/pi-sdk';

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
import { verifyRewardedAdAction } from '@xhilo/pi-sdk/backend';

export default async function handler(req, res) {
  if (req.method !== 'POST') {
    return res.status(405).json({ message: 'Method not allowed' });
  }

  const { adId, userId, rewardAmount, metadata } = req.body;
  const accessToken = req.headers.authorization?.replace('Bearer ', '');

  try {
    const result = await verifyRewardedAdAction(
      { adId, userId, rewardAmount, metadata },
      undefined, // config
      accessToken
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
import { usePiPayments } from '@xhilo/pi-sdk';

function AdvancedPaymentButton() {
  const { createPayment } = usePiPayments();

  const handlePayment = async () => {
    const payment = await createPayment({
      amount: 5.0,
      memo: 'Premium subscription',
      metadata: { plan: 'premium', duration: 'monthly' },
      callbacks: {
        onReadyForServerApproval: (paymentId) => {
          console.log('Payment ready for approval:', paymentId);
          // Custom logic before approval
        },
        onCancel: (paymentId) => {
          console.log('Payment cancelled:', paymentId);
          // Handle cancellation
        },
        onError: (error, payment) => {
          console.error('Payment error:', error, payment);
          // Handle errors
        }
      }
    });
  };

  return <button onClick={handlePayment}>Subscribe Premium</button>;
}
```

### Custom Ads Functions

```typescript
// Backend: Custom ads implementation
import { verifyRewardedAdAction } from '@xhilo/pi-sdk/backend';

const customAdsFunctions = {
  checkUserAdEligibility: async (userId, adType) => {
    // Your custom eligibility logic
    const user = await getUserFromDatabase(userId);
    const todayViews = await getTodayAdViews(userId, adType);
    
    return {
      eligible: todayViews < user.dailyAdLimit,
      message: todayViews < user.dailyAdLimit 
        ? 'User is eligible' 
        : 'Daily ad limit reached'
    };
  },
  
  logAdCompletion: async (userId, adId, adType, rewardAmount, metadata, adStatus) => {
    // Your custom logging logic
    await logToAnalytics({
      userId,
      adId,
      adType,
      rewardAmount,
      timestamp: new Date(),
      adStatus
    });
  }
};

// Use custom functions
const result = await verifyRewardedAdAction(
  { adId, userId, rewardAmount, metadata },
  undefined,
  customAdsFunctions
);
```

### Error Handling

```typescript
import { usePiPayments, usePiAds } from '@xhilo/pi-sdk';

function ErrorHandlingExample() {
  const { createPayment } = usePiPayments();
  const { showAd } = usePiAds();

  const handlePayment = async () => {
    try {
      const result = await createPayment({
        amount: 1.0,
        memo: 'Test payment'
      });

      if (!result.success) {
        // Handle specific error types
        switch (result.message) {
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
            console.error('Payment failed:', result.message);
        }
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
const { user, isAuthenticated } = useXhiloPiNetwork();

if (!isAuthenticated) {
  console.log('User not authenticated, redirecting to login');
  // Redirect to Pi Browser login
}
```

#### 2. "Payment creation failed" Error
```typescript
// Check environment variables
if (!process.env.REACT_APP_PI_APP_ID) {
  console.error('REACT_APP_PI_APP_ID not set');
}

// Check user balance
const { user } = useXhiloPiNetwork();
console.log('User balance:', user.balance);
```

#### 3. "Ad not ready" Error
```typescript
// Check ad readiness
const { isAdReady } = usePiAds();

const checkAd = async () => {
  const ready = await isAdReady('rewarded');
  console.log('Ad ready:', ready.data.isReady);
  console.log('Ad reason:', ready.data.reason);
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
```

### Debug Mode

```typescript
// Enable debug logging
const { createPayment } = usePiPayments();

const handlePayment = async () => {
  // Enable debug mode
  console.log('Debug mode enabled');
  
  const result = await createPayment({
    amount: 1.0,
    memo: 'Debug payment',
    metadata: { debug: true }
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
