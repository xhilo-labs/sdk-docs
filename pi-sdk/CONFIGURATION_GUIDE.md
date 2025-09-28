# Configuration Guide

This guide explains how to properly configure and use the Pi SDK with the new configuration-driven architecture.

## 🏗️ Architecture Overview

The Pi SDK uses a **configuration-driven architecture** where:

- **No Environment Dependencies**: SDK functions don't read from environment variables
- **Explicit Configuration**: All backend functions require configuration parameters
- **Type Safety**: Full TypeScript support with proper type definitions
- **Clear Separation**: Different configuration types for different payment flows

## 📋 Configuration Types

### PiBackendConfig

Used for **App-to-User (A2U) payments** - sending Pi from your app to users.

```typescript
interface PiBackendConfig {
  apiKey: string;           // Your Pi Network API key
  privateSeed: string;      // Your wallet private seed
  sandbox?: boolean;        // Optional: Use sandbox environment
}
```

### PiPlatformConfig

Used for **User-to-App (U2A) payments** - processing payments from users to your app.

```typescript
interface PiPlatformConfig {
  apiKey: string;           // Your Pi Network API key
  baseUrl?: string;         // Optional: Custom API base URL
}
```

## 🚀 Basic Setup

### 1. Environment Variables

Create a `.env` file in your project root:

```bash
# Required for your application
PI_API_KEY=your_pi_api_key_here
PI_WALLET_PRIVATE_SEED=your_wallet_private_seed_here

# Optional
NODE_ENV=development
```

### 2. Configuration Objects

Create configuration objects in your application code:

```typescript
import { PiBackendConfig, PiPlatformConfig } from '@xhilo/pi-sdk/backend';

// For A2U payments (App-to-User)
const piBackendConfig: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!,
  sandbox: process.env.NODE_ENV === 'development'
};

// For U2A payments (User-to-App)
const piPlatformConfig: PiPlatformConfig = {
  apiKey: process.env.PI_API_KEY!,
  baseUrl: 'https://api.minepi.com/v2' // Optional
};
```

## 💳 Payment Examples

### App-to-User (A2U) Payments

Send Pi from your app to users:

```typescript
import { 
  processA2UWithdrawalAction, 
  createAndMakeA2UPayment,
  PiBackendConfig 
} from '@xhilo/pi-sdk/backend';

const piBackendConfig: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!
};

// Method 1: Detailed withdrawal
const result = await processA2UWithdrawalAction({
  withdrawalAmount: 10.0,
  userId: 'user123',
  memo: 'Welcome bonus',
  metadata: {
    type: 'bonus',
    campaign: 'welcome'
  }
}, piBackendConfig);

// Method 2: High-level helper
const result = await createAndMakeA2UPayment(
  'user123',           // userId
  10.0,                // amount
  'Welcome Bonus',     // itemName
  piBackendConfig,     // config
  { campaign: 'welcome' } // customMetadata
);
```

### User-to-App (U2A) Payments

Process payments from users to your app:

```typescript
import { 
  approvePaymentAction,
  completePaymentAction,
  getPaymentAction,
  PiPlatformConfig 
} from '@xhilo/pi-sdk/backend';

const piPlatformConfig: PiPlatformConfig = {
  apiKey: process.env.PI_API_KEY!
};

// Approve payment (called from frontend callback)
const approvalResult = await approvePaymentAction({
  paymentId: 'payment123',
  userId: 'user123'
}, piPlatformConfig, 'user_access_token');

// Complete payment (called from frontend callback)
const completionResult = await completePaymentAction({
  paymentId: 'payment123',
  txid: 'transaction123',
  userId: 'user123'
}, piPlatformConfig, 'user_access_token');

// Get payment details
const payment = await getPaymentAction('payment123', piPlatformConfig);
```

### Ads Verification

Verify rewarded ads and process rewards:

```typescript
import { 
  verifyRewardedAdAction,
  getAdEligibilityAction,
  getUserAdStatsAction,
  PiBackendConfig 
} from '@xhilo/pi-sdk/backend';

const piBackendConfig: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!
};

// Verify rewarded ad
const adResult = await verifyRewardedAdAction({
  adId: 'ad123',
  rewardAmount: 0.1,
  userId: 'user123',
  metadata: { source: 'mobile' }
}, piBackendConfig);

// Check ad eligibility
const eligibility = await getAdEligibilityAction({
  userId: 'user123',
  adType: 'rewarded',
  maxDailyViews: 5
}, piBackendConfig);

// Get user ad stats
const stats = await getUserAdStatsAction('user123', piBackendConfig);
```

## 🛠️ Framework Integration

### Next.js API Routes

```typescript
// app/api/withdraw/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { processA2UWithdrawalAction, PiBackendConfig } from '@xhilo/pi-sdk/backend';

export async function POST(request: NextRequest) {
  const { amount, userId, memo, metadata } = await request.json();
  
  const piBackendConfig: PiBackendConfig = {
    apiKey: process.env.PI_API_KEY!,
    privateSeed: process.env.PI_WALLET_PRIVATE_SEED!
  };
  
  const result = await processA2UWithdrawalAction({
    withdrawalAmount: amount,
    userId,
    memo,
    metadata
  }, piBackendConfig);
  
  return NextResponse.json(result);
}
```

### Express.js Routes

```typescript
import express from 'express';
import { 
  processA2UWithdrawalAction,
  approvePaymentAction,
  PiBackendConfig,
  PiPlatformConfig 
} from '@xhilo/pi-sdk/backend';

const app = express();
app.use(express.json());

// Configuration objects
const piBackendConfig: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!
};

const piPlatformConfig: PiPlatformConfig = {
  apiKey: process.env.PI_API_KEY!
};

// A2U withdrawal endpoint
app.post('/api/withdraw', async (req, res) => {
  try {
    const result = await processA2UWithdrawalAction(req.body, piBackendConfig);
    res.json(result);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// U2A payment approval endpoint
app.post('/api/payments/:id/approve', async (req, res) => {
  try {
    const result = await approvePaymentAction({
      paymentId: req.params.id,
      userId: req.body.userId
    }, piPlatformConfig, req.headers.authorization?.replace('Bearer ', ''));
    res.json(result);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});
```

## 🔧 Advanced Configuration

### Custom Ads Functions

You can customize ads behavior by providing your own functions:

```typescript
import { verifyRewardedAdAction, AdsCustomFunctions } from '@xhilo/pi-sdk/backend';

const customFunctions: AdsCustomFunctions = {
  checkUserAdEligibility: async (userId: string, adType: 'interstitial' | 'rewarded') => {
    // Your custom eligibility logic
    const user = await getUserFromDatabase(userId);
    return {
      eligible: user.adsEnabled && user.dailyAdCount < 5,
      message: 'User is eligible for ads'
    };
  },
  
  getUserAdViewsToday: async (userId: string, adType: 'interstitial' | 'rewarded', date: string) => {
    // Your custom view counting logic
    return await getAdViewsFromDatabase(userId, date);
  },
  
  logAdCompletion: async (userId: string, adId: string, adType: 'interstitial' | 'rewarded', rewardAmount: number, metadata: Record<string, any>) => {
    // Your custom logging logic
    await logAdCompletionToDatabase(userId, adId, rewardAmount, metadata);
  }
};

const result = await verifyRewardedAdAction({
  adId: 'ad123',
  rewardAmount: 0.1,
  userId: 'user123'
}, piBackendConfig, customFunctions);
```

### Error Handling

All functions return consistent result formats:

```typescript
interface PiOperationResult<T = any> {
  success: boolean;
  data?: T;
  message?: string;
}

// Example usage
const result = await processA2UWithdrawalAction(args, piBackendConfig);

if (result.success) {
  console.log('Payment processed:', result.paymentId);
} else {
  console.error('Payment failed:', result.message);
}
```

## 🚨 Common Mistakes

### ❌ Don't Do This

```typescript
// WRONG: Calling functions without configuration
const result = await processA2UWithdrawalAction(args); // ❌ Missing config

// WRONG: Using wrong configuration type
const result = await approvePaymentAction(args, piBackendConfig); // ❌ Wrong config type
```

### ✅ Do This Instead

```typescript
// CORRECT: Always provide configuration
const result = await processA2UWithdrawalAction(args, piBackendConfig); // ✅

// CORRECT: Use correct configuration type
const result = await approvePaymentAction(args, piPlatformConfig); // ✅
```

## 🔍 TypeScript Support

The SDK provides full TypeScript support:

```typescript
import type { 
  PiBackendConfig,
  PiPlatformConfig,
  ProcessA2UWithdrawalArgs,
  ApprovePaymentArgs,
  PiOperationResult 
} from '@xhilo/pi-sdk/backend';

// Type-safe configuration
const config: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!
};

// Type-safe function calls
const result: PiOperationResult = await processA2UWithdrawalAction(args, config);
```

## 📚 Additional Resources

- [Getting Started Guide](./getting-started.md)
- [API Reference](./api-reference.md)
- [Backend Documentation](./backend/README.md)
- [React SDK Documentation](./react/README.md)
- [Templates](./templates/)

## 🆘 Need Help?

- Check the [Troubleshooting Guide](./troubleshooting.md)
- Review the [Examples](./examples/)
- Open an issue on [GitHub](https://github.com/xhilo-labs/pi-sdk/issues)

---

*This configuration-driven approach ensures your SDK usage is explicit, type-safe, and maintainable.*
