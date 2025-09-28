# Real Examples

This document provides real, working examples based on the actual implementation of the Pi SDK.

## 🚀 React Examples

### Basic Authentication

```tsx
import React from 'react';
import { useXhiloPiNetwork } from '@xhilo/pi-sdk/react';

function App() {
  const { user, isInitialized, authenticate, logout } = useXhiloPiNetwork();

  if (!isInitialized) {
    return <div>Initializing Pi SDK...</div>;
  }

  if (!user) {
    return (
      <div>
        <h1>Welcome to My Pi App</h1>
        <button 
          onClick={() => authenticate(['payments'])}
          className="bg-green-600 text-white px-6 py-3 rounded-lg hover:bg-green-700"
        >
          Login with Pi Network
        </button>
      </div>
    );
  }

  return (
    <div>
      <h1>Welcome, {user.username}!</h1>
      <p>Wallet: {user.wallet_address}</p>
      <button 
        onClick={logout}
        className="bg-red-600 text-white px-4 py-2 rounded-lg hover:bg-red-700"
      >
        Logout
      </button>
    </div>
  );
}

export default App;
```

### Simple Payment

```tsx
import React from 'react';
import { usePiSimplePayments } from '@xhilo/pi-sdk/react';

function SimplePaymentComponent() {
  const { createSimplePayment } = usePiSimplePayments();

  const handlePayment = async () => {
    const result = await createSimplePayment({
      userId: 'user123',
      amount: 1.0,
      itemName: 'Premium Feature',
      customMetadata: { 
        orderId: 'ORD-123',
        feature: 'premium_access'
      }
    }, {
      onSuccess: (paymentId) => {
        console.log('Payment successful:', paymentId);
        alert('Payment completed successfully!');
      },
      onError: (error) => {
        console.error('Payment failed:', error);
        alert('Payment failed: ' + error);
      },
      onCancel: () => {
        console.log('Payment cancelled');
        alert('Payment was cancelled');
      }
    });

    console.log('Payment result:', result);
  };

  return (
    <div>
      <h2>Buy Premium Feature</h2>
      <p>Get access to premium features for 1 Pi</p>
      <button 
        onClick={handlePayment}
        className="bg-blue-600 text-white px-6 py-3 rounded-lg hover:bg-blue-700"
      >
        Pay 1 Pi
      </button>
    </div>
  );
}

export default SimplePaymentComponent;
```

### Advanced Payment with Progress Tracking

```tsx
import React, { useState } from 'react';
import { usePiPayments } from '@xhilo/pi-sdk/react';

function AdvancedPaymentComponent() {
  const { createAndCompletePayment, isProcessing, error, success } = usePiPayments();
  const [processData, setProcessData] = useState(null);

  const handlePayment = async () => {
    const result = await createAndCompletePayment({
      userId: 'user123',
      amount: 5.0,
      memo: 'Advanced Payment',
      metadata: { 
        orderId: 'ADV-456',
        type: 'subscription'
      },
      onSuccess: (paymentId) => {
        console.log('Payment successful:', paymentId);
        setProcessData({ stage: 'success', message: 'Payment completed!' });
      },
      onError: (error) => {
        console.error('Payment failed:', error);
        setProcessData({ stage: 'error', message: error });
      },
      onCancel: () => {
        console.log('Payment cancelled');
        setProcessData({ stage: 'cancelled', message: 'Payment was cancelled' });
      },
      onProcessUpdate: (data) => {
        console.log('Payment process update:', data);
        setProcessData(data);
      }
    });

    console.log('Payment result:', result);
  };

  return (
    <div>
      <h2>Advanced Payment</h2>
      <p>Complete payment with progress tracking for 5 Pi</p>
      
      <button 
        onClick={handlePayment}
        disabled={isProcessing}
        className="bg-purple-600 text-white px-6 py-3 rounded-lg hover:bg-purple-700 disabled:opacity-50"
      >
        {isProcessing ? 'Processing...' : 'Pay 5 Pi'}
      </button>

      {processData && (
        <div className="mt-4 p-4 bg-gray-100 rounded-lg">
          <h3>Payment Status: {processData.stage}</h3>
          <p>{processData.message}</p>
          {processData.progress && (
            <div className="w-full bg-gray-200 rounded-full h-2.5 mt-2">
              <div 
                className="bg-blue-600 h-2.5 rounded-full" 
                style={{ width: `${processData.progress}%` }}
              ></div>
            </div>
          )}
        </div>
      )}

      {error && (
        <div className="mt-4 p-4 bg-red-100 text-red-700 rounded-lg">
          Error: {error}
        </div>
      )}

      {success && (
        <div className="mt-4 p-4 bg-green-100 text-green-700 rounded-lg">
          Payment successful!
        </div>
      )}
    </div>
  );
}

export default AdvancedPaymentComponent;
```

### Using Payment Components

```tsx
import React from 'react';
import { PaymentButton, SimplePaymentButton } from '@xhilo/pi-sdk/react';

function PaymentComponentsExample() {
  return (
    <div className="space-y-6">
      <div>
        <h2>Payment Button</h2>
        <PaymentButton
          userId="user123"
          amount={2.0}
          memo="Custom Payment"
          metadata={{ orderId: 'CUST-789' }}
          onSuccess={(paymentId) => console.log('Success:', paymentId)}
          onError={(error) => console.error('Error:', error)}
          onCancel={() => console.log('Cancelled')}
          className="bg-green-600 text-white px-6 py-3 rounded-lg hover:bg-green-700"
        >
          Pay 2 Pi
        </PaymentButton>
      </div>

      <div>
        <h2>Simple Payment Button</h2>
        <SimplePaymentButton
          userId="user123"
          amount={1.5}
          itemName="Digital Item"
          customMetadata={{ itemId: 'DIG-001' }}
          onSuccess={(paymentId) => console.log('Success:', paymentId)}
          onError={(error) => console.error('Error:', error)}
          onCancel={() => console.log('Cancelled')}
          className="bg-blue-600 text-white px-6 py-3 rounded-lg hover:bg-blue-700"
        >
          Buy Digital Item
        </SimplePaymentButton>
      </div>
    </div>
  );
}

export default PaymentComponentsExample;
```

## 🛠️ Backend Examples

### Next.js API Routes

#### A2U Payment (App-to-User)

```typescript
// app/api/withdraw/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { processA2UWithdrawalAction, PiBackendConfig } from '@xhilo/pi-sdk/backend';

export async function POST(request: NextRequest) {
  try {
    const { amount, userId, memo, metadata } = await request.json();

    // Validate required fields
    if (!amount || !userId || !memo) {
      return NextResponse.json(
        { success: false, message: 'Missing required fields' },
        { status: 400 }
      );
    }

    // Validate environment variables
    if (!process.env.PI_API_KEY || !process.env.PI_WALLET_PRIVATE_SEED) {
      return NextResponse.json(
        { success: false, message: 'Pi Network credentials not configured' },
        { status: 500 }
      );
    }

    const piBackendConfig: PiBackendConfig = {
      apiKey: process.env.PI_API_KEY,
      privateSeed: process.env.PI_WALLET_PRIVATE_SEED
    };

    const result = await processA2UWithdrawalAction({
      withdrawalAmount: amount,
      userId,
      memo,
      metadata: metadata || {}
    }, piBackendConfig);

    if (result.success) {
      return NextResponse.json({
        success: true,
        message: 'Withdrawal processed successfully',
        data: {
          paymentId: result.paymentId,
          txid: result.txid,
          amount,
          userId,
          completedAt: new Date().toISOString()
        }
      });
    } else {
      return NextResponse.json(
        { success: false, message: result.message },
        { status: 400 }
      );
    }
  } catch (error) {
    console.error('Withdrawal error:', error);
    return NextResponse.json(
      { success: false, message: 'Internal server error' },
      { status: 500 }
    );
  }
}
```

#### U2A Payment Approval

```typescript
// app/api/payments/[id]/approve/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { approvePaymentAction, PiPlatformConfig } from '@xhilo/pi-sdk/backend';

export async function POST(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const { userId } = await request.json();
    const paymentId = params.id;

    // Validate environment variables
    if (!process.env.PI_API_KEY) {
      return NextResponse.json(
        { success: false, message: 'Pi Network credentials not configured' },
        { status: 500 }
      );
    }

    const piPlatformConfig: PiPlatformConfig = {
      apiKey: process.env.PI_API_KEY
    };

    // Get access token from Authorization header
    const authHeader = request.headers.get('authorization');
    const accessToken = authHeader?.replace('Bearer ', '');

    const result = await approvePaymentAction({
      paymentId,
      userId
    }, piPlatformConfig, accessToken);

    return NextResponse.json(result);
  } catch (error) {
    console.error('Payment approval error:', error);
    return NextResponse.json(
      { success: false, message: 'Internal server error' },
      { status: 500 }
    );
  }
}
```

#### Ads Verification

```typescript
// app/api/ads/verify/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { verifyRewardedAdAction, PiBackendConfig } from '@xhilo/pi-sdk/backend';

export async function POST(request: NextRequest) {
  try {
    const { adId, userId, rewardAmount, metadata } = await request.json();

    // Validate required fields
    if (!adId || !userId) {
      return NextResponse.json(
        { success: false, message: 'Missing required fields: adId and userId' },
        { status: 400 }
      );
    }

    // Validate environment variables
    if (!process.env.PI_API_KEY || !process.env.PI_WALLET_PRIVATE_SEED) {
      return NextResponse.json(
        { success: false, message: 'Pi Network credentials not configured' },
        { status: 500 }
      );
    }

    const piBackendConfig: PiBackendConfig = {
      apiKey: process.env.PI_API_KEY,
      privateSeed: process.env.PI_WALLET_PRIVATE_SEED
    };

    const result = await verifyRewardedAdAction({
      adId,
      userId,
      rewardAmount: rewardAmount || 0.1,
      metadata: metadata || {}
    }, piBackendConfig);

    return NextResponse.json(result);
  } catch (error) {
    console.error('Ad verification error:', error);
    return NextResponse.json(
      { success: false, message: 'Internal server error' },
      { status: 500 }
    );
  }
}
```

### Express.js Server

```typescript
import express from 'express';
import { 
  processA2UWithdrawalAction,
  approvePaymentAction,
  completePaymentAction,
  verifyRewardedAdAction,
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
    const { amount, userId, memo, metadata } = req.body;
    
    const result = await processA2UWithdrawalAction({
      withdrawalAmount: amount,
      userId,
      memo,
      metadata: metadata || {}
    }, piBackendConfig);
    
    res.json(result);
  } catch (error) {
    console.error('Withdrawal error:', error);
    res.status(500).json({ success: false, message: 'Internal server error' });
  }
});

// U2A payment approval endpoint
app.post('/api/payments/:id/approve', async (req, res) => {
  try {
    const { userId } = req.body;
    const { id: paymentId } = req.params;
    const accessToken = req.headers.authorization?.replace('Bearer ', '');
    
    const result = await approvePaymentAction({
      paymentId,
      userId
    }, piPlatformConfig, accessToken);
    
    res.json(result);
  } catch (error) {
    console.error('Payment approval error:', error);
    res.status(500).json({ success: false, message: 'Internal server error' });
  }
});

// U2A payment completion endpoint
app.post('/api/payments/:id/complete', async (req, res) => {
  try {
    const { txid, userId } = req.body;
    const { id: paymentId } = req.params;
    const accessToken = req.headers.authorization?.replace('Bearer ', '');
    
    const result = await completePaymentAction({
      paymentId,
      txid,
      userId
    }, piPlatformConfig, accessToken);
    
    res.json(result);
  } catch (error) {
    console.error('Payment completion error:', error);
    res.status(500).json({ success: false, message: 'Internal server error' });
  }
});

// Ads verification endpoint
app.post('/api/ads/verify', async (req, res) => {
  try {
    const { adId, userId, rewardAmount, metadata } = req.body;
    
    const result = await verifyRewardedAdAction({
      adId,
      userId,
      rewardAmount: rewardAmount || 0.1,
      metadata: metadata || {}
    }, piBackendConfig);
    
    res.json(result);
  } catch (error) {
    console.error('Ad verification error:', error);
    res.status(500).json({ success: false, message: 'Internal server error' });
  }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

## 🔧 TypeScript Examples

### Type-Safe Configuration

```typescript
import { 
  PiBackendConfig, 
  PiPlatformConfig,
  ProcessA2UWithdrawalArgs,
  ApprovePaymentArgs 
} from '@xhilo/pi-sdk/backend';

// Type-safe configuration
const piBackendConfig: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!,
  sandbox: process.env.NODE_ENV === 'development'
};

const piPlatformConfig: PiPlatformConfig = {
  apiKey: process.env.PI_API_KEY!,
  baseUrl: 'https://api.minepi.com/v2'
};

// Type-safe function calls
const withdrawalArgs: ProcessA2UWithdrawalArgs = {
  withdrawalAmount: 10.0,
  userId: 'user123',
  memo: 'Type-safe withdrawal',
  metadata: { orderId: 'TS-001' }
};

const approvalArgs: ApprovePaymentArgs = {
  paymentId: 'payment123',
  userId: 'user123'
};
```

### Custom Error Handling

```typescript
import { PiOperationResult } from '@xhilo/pi-sdk/backend';

async function handlePaymentWithErrorHandling() {
  try {
    const result: PiOperationResult = await processA2UWithdrawalAction(args, config);
    
    if (result.success) {
      console.log('Payment successful:', result.paymentId);
      return { success: true, data: result };
    } else {
      console.error('Payment failed:', result.message);
      return { success: false, error: result.message };
    }
  } catch (error) {
    console.error('Unexpected error:', error);
    return { success: false, error: 'Unexpected error occurred' };
  }
}
```

## 🎯 Complete Integration Example

### Frontend + Backend Integration

```tsx
// Frontend: React component
import React, { useState } from 'react';
import { usePiPayments } from '@xhilo/pi-sdk/react';

function CompletePaymentFlow() {
  const { createAndCompletePayment, isProcessing } = usePiPayments();
  const [result, setResult] = useState(null);

  const handlePayment = async () => {
    const result = await createAndCompletePayment({
      userId: 'user123',
      amount: 3.0,
      memo: 'Complete Integration Test',
      metadata: { 
        orderId: 'INT-001',
        source: 'react_app'
      },
      apiConfig: {
        baseUrl: 'https://myapi.com',
        approveEndpoint: '/api/payments/approve',
        completeEndpoint: '/api/payments/complete'
      },
      onSuccess: (paymentId) => {
        console.log('Payment completed:', paymentId);
        setResult({ success: true, paymentId });
      },
      onError: (error) => {
        console.error('Payment failed:', error);
        setResult({ success: false, error });
      }
    });
  };

  return (
    <div>
      <button 
        onClick={handlePayment}
        disabled={isProcessing}
        className="bg-green-600 text-white px-6 py-3 rounded-lg hover:bg-green-700 disabled:opacity-50"
      >
        {isProcessing ? 'Processing...' : 'Pay 3 Pi'}
      </button>
      
      {result && (
        <div className={`mt-4 p-4 rounded-lg ${
          result.success ? 'bg-green-100 text-green-700' : 'bg-red-100 text-red-700'
        }`}>
          {result.success ? `Payment successful: ${result.paymentId}` : `Error: ${result.error}`}
        </div>
      )}
    </div>
  );
}

export default CompletePaymentFlow;
```

```typescript
// Backend: Next.js API route
import { NextRequest, NextResponse } from 'next/server';
import { approvePaymentAction, completePaymentAction, PiPlatformConfig } from '@xhilo/pi-sdk/backend';

const piPlatformConfig: PiPlatformConfig = {
  apiKey: process.env.PI_API_KEY!
};

export async function POST(request: NextRequest) {
  const { paymentId, userId, txid } = await request.json();
  const accessToken = request.headers.get('authorization')?.replace('Bearer ', '');
  
  try {
    // Approve payment
    const approvalResult = await approvePaymentAction({
      paymentId,
      userId
    }, piPlatformConfig, accessToken);
    
    if (!approvalResult.success) {
      return NextResponse.json(approvalResult, { status: 400 });
    }
    
    // Complete payment
    const completionResult = await completePaymentAction({
      paymentId,
      txid,
      userId
    }, piPlatformConfig, accessToken);
    
    return NextResponse.json(completionResult);
  } catch (error) {
    console.error('Payment processing error:', error);
    return NextResponse.json(
      { success: false, message: 'Internal server error' },
      { status: 500 }
    );
  }
}
```

These examples are based on the actual implementation and should work with the current version of the Pi SDK.
