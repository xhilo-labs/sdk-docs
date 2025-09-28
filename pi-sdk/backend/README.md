# @xhilo/pi-sdk Backend

Backend SDK for Pi Network integration with server-side payment processing capabilities.

## Installation

```bash
npm install @xhilo/pi-sdk
```

## Features

- **App-to-User (A2U) Payments**: Send Pi from your app to users
- **User-to-App (U2A) Payments**: Process payments from users to your app
- **Ads Verification**: Verify rewarded ads and process rewards
- **Pi Platform API Integration**: Full integration with Pi Network's official API
- **Configuration-Driven**: All functions require explicit configuration parameters (no environment variable dependencies)
- **Async Functions**: All backend actions are async for better serverless compatibility
- **TypeScript Support**: Complete type definitions
- **Express.js Ready**: Built for Node.js server environments
- **Serverless Compatible**: Works in Vercel, AWS Lambda, and other serverless environments

## Quick Start

### Environment Setup

```bash
# .env (for your application)
PI_API_KEY=your_pi_api_key
PI_WALLET_PRIVATE_SEED=your_wallet_private_seed
NODE_ENV=development # or production
```

### Basic Usage

```typescript
import { 
  processA2UWithdrawalAction,
  approvePaymentAction,
  completePaymentAction,
  PiBackendConfig,
  PiPlatformConfig 
} from '@xhilo/pi-sdk/backend';

// Configuration objects (required for all functions)
const piBackendConfig: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!
};

const piPlatformConfig: PiPlatformConfig = {
  apiKey: process.env.PI_API_KEY!
};

// All backend functions require explicit configuration and are async
const a2uResult = await processA2UWithdrawalAction({
  withdrawalAmount: 10.0,
  userId: 'user-uid',
  memo: 'Withdrawal from MyApp',
  metadata: { withdrawalId: 'wd-123' }
}, piBackendConfig);

const u2aResult = await approvePaymentAction({
  paymentId: 'payment-123',
  userId: 'user-uid'
}, piPlatformConfig);
```

## API Reference

### App-to-User (A2U) Payments

Send Pi from your app to users.

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

// Process withdrawal
const result = await processA2UWithdrawalAction({
  withdrawalAmount: 10.0,
  userId: 'user-uid',
  memo: 'Withdrawal from MyApp',
  metadata: {
    withdrawalId: 'wd-123',
    reason: 'earnings'
  }
}, piBackendConfig);

// High-level helper
const result = await createAndMakeA2UPayment(
  'user-uid',
  10.0,
  'Premium Feature',
  piBackendConfig,
  { customData: 'value' }
);
```

### User-to-App (U2A) Payments

Process payments from users to your app.

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

// Approve a payment (called from frontend callback)
const approveResult = await approvePaymentAction({
  paymentId: 'payment-id',
  userId: 'user-uid'
}, piPlatformConfig);

// Complete a payment (called from frontend callback)
const completeResult = await completePaymentAction({
  paymentId: 'payment-id',
  txid: 'transaction-id',
  userId: 'user-uid'
}, piPlatformConfig);

// Get payment details
const payment = await getPaymentAction('payment-id', piPlatformConfig);
```

### Ads Verification

Verify rewarded ads and process rewards.

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

### Pi Platform API Client

Direct access to Pi Network's official API.

```typescript
import { createPiPlatformClient } from '@xhilo/pi-sdk/backend';

const client = createPiPlatformClient({
  apiKey: process.env.PI_API_KEY!,
  sandbox: true
});

// Get payment details
const payment = await client.getPayment('payment-id');

// Get user information
const user = await client.getMe();

// Approve payment
await client.approvePayment('payment-id');

// Complete payment
await client.completePayment('payment-id', 'txid');

// Cancel payment
await client.cancelPayment('payment-id');
```

### Backend Client

Low-level access to Pi backend SDK.

```typescript
import { createPiBackendClient } from '@xhilo/pi-sdk/backend';

const client = createPiBackendClient({
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!,
  sandbox: true
});

// Create payment
const paymentId = await client.createPayment({
  amount: 10.0,
  memo: 'Payment memo',
  metadata: { orderId: '123' },
  uid: 'user-uid'
});

// Submit payment
const txid = await client.submitPayment({ paymentId });

// Complete payment
const result = await client.completePayment({ paymentId, txid });
```

## Express.js Integration

```typescript
import express from 'express';
import { 
  processA2UWithdrawalAction,
  approvePaymentAction,
  completePaymentAction,
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
    }, piPlatformConfig);
    res.json(result);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// U2A payment completion endpoint
app.post('/api/payments/:id/complete', async (req, res) => {
  try {
    const result = await completePaymentAction({
      paymentId: req.params.id,
      txid: req.body.txid,
      userId: req.body.userId
    }, piPlatformConfig);
    res.json(result);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});
```

## Error Handling

All functions return a consistent result format:

```typescript
interface PiOperationResult<T = any> {
  success: boolean;
  data?: T;
  message?: string;
}
```

```typescript
const result = await processA2UWithdrawalAction(args);

if (result.success) {
  console.log('Payment processed:', result.paymentId);
} else {
  console.error('Payment failed:', result.message);
}
```

## TypeScript Support

The package includes complete TypeScript definitions:

```typescript
import type { 
  PiOperationResult,
  PaymentData,
  PiBackendConfig,
  ProcessA2UWithdrawalArgs 
} from '@xhilo/pi-sdk/backend';
```

## License

MIT
