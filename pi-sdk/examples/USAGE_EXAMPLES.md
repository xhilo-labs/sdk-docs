# Pi SDK Usage Examples

This document shows how to use the modularized Pi SDK with high-level hooks and components.

## High-Level Payment Hook

The `usePiPayments` hook provides a complete payment flow that handles both frontend and backend calls:

```typescript
import { usePiPayments } from '@xhilo/pi-sdk/react';

function PaymentComponent({ userId }: { userId: string }) {
  const { createAndCompletePayment, isProcessing, error, success } = usePiPayments();

  const handlePayment = async () => {
    const result = await createAndCompletePayment({
      userId,
      amount: 1.0,
      memo: 'Test payment for MiniTasks app',
      metadata: {
        orderId: 'ORD-123',
        item: 'Premium Feature Unlock'
      },
      onSuccess: (paymentId) => {
        console.log('Payment completed:', paymentId);
        // Update your app state here
      },
      onError: (error) => {
        console.error('Payment failed:', error);
        // Handle error in your UI
      },
      onCancel: () => {
        console.log('Payment canceled');
        // Handle cancellation
      }
    });

    if (result.success) {
      console.log('Payment initiated successfully');
    }
  };

  return (
    <div>
      <button onClick={handlePayment} disabled={isProcessing}>
        {isProcessing ? 'Processing...' : 'Pay 1 Pi'}
      </button>
      {error && <p className="text-red-600">Error: {error}</p>}
      {success && <p className="text-green-600">Payment completed!</p>}
    </div>
  );
}
```

## Simple Payment Hook

For basic payments, use the `usePiSimplePayments` hook:

```typescript
import { usePiSimplePayments } from '@xhilo/pi-sdk/react';

function SimplePaymentComponent({ userId }: { userId: string }) {
  const { createSimplePayment, isInitialized } = usePiSimplePayments();

  const handleSimplePayment = async () => {
    const result = await createSimplePayment(
      {
        userId,
        amount: 5.0,
        itemName: 'Premium Feature',
        customMetadata: { feature: 'unlock' }
      },
      {
        onSuccess: (paymentId) => console.log('Success:', paymentId),
        onError: (error) => console.error('Error:', error),
        onCancel: () => console.log('Canceled')
      }
    );

    console.log('Payment result:', result);
  };

  return (
    <button onClick={handleSimplePayment} disabled={!isInitialized}>
      Buy Premium Feature
    </button>
  );
}
```

## Payment Components

### PaymentButton Component

```typescript
import { PaymentButton } from '@xhilo/pi-sdk/react';

function MyApp() {
  return (
    <PaymentButton
      userId="user123"
      amount={2.5}
      memo="Purchase premium subscription"
      metadata={{ plan: 'premium', duration: 'monthly' }}
      onSuccess={(paymentId) => {
        console.log('Payment completed:', paymentId);
        // Unlock premium features
      }}
      onError={(error) => {
        console.error('Payment failed:', error);
        // Show error message
      }}
      className="bg-blue-600 text-white px-4 py-2 rounded"
    >
      Subscribe to Premium
    </PaymentButton>
  );
}
```

### SimplePaymentButton Component

```typescript
import { SimplePaymentButton } from '@xhilo/pi-sdk/react';

function ShopPage() {
  return (
    <div>
      <h2>Buy Items</h2>
      
      <SimplePaymentButton
        userId="user123"
        amount={1.0}
        itemName="Extra Lives"
        customMetadata={{ type: 'powerup' }}
        onSuccess={(paymentId) => console.log('Lives purchased:', paymentId)}
      >
        Buy Extra Lives (1 Pi)
      </SimplePaymentButton>

      <SimplePaymentButton
        userId="user123"
        amount={5.0}
        itemName="Premium Pack"
        customMetadata={{ type: 'bundle' }}
        onSuccess={(paymentId) => console.log('Pack purchased:', paymentId)}
      >
        Buy Premium Pack (5 Pi)
      </SimplePaymentButton>
    </div>
  );
}
```

## Utility Functions

```typescript
import { 
  createPaymentMetadata, 
  validatePaymentAmount, 
  formatPaymentAmount,
  createPaymentMemo 
} from '@xhilo/pi-sdk/react';

// Create metadata
const metadata = createPaymentMetadata('user123', 'Premium Feature', {
  plan: 'monthly',
  discount: '10%'
});

// Validate amount
const validation = validatePaymentAmount(1.5);
if (!validation.isValid) {
  console.error(validation.error);
}

// Format for display
const displayAmount = formatPaymentAmount(1.5); // "1.50 Pi"

// Create memo
const memo = createPaymentMemo('Premium Feature', 'user123');
// "Payment for Premium Feature by user user123"
```

## Backend Integration

The hooks automatically call your backend APIs. Make sure you have these endpoints:

```typescript
// app/api/payments/approve/route.ts
import { approvePaymentAction } from '@xhilo/pi-sdk/backend';

export async function POST(request: Request) {
  const { paymentId, userId } = await request.json();
  const result = await approvePaymentAction({ paymentId, userId });
  return Response.json(result);
}

// app/api/payments/complete/route.ts
import { completePaymentAction } from '@xhilo/pi-sdk/backend';

export async function POST(request: Request) {
  const { paymentId, txid, userId } = await request.json();
  const result = await completePaymentAction({ paymentId, txid, userId });
  return Response.json(result);
}
```

## Complete Example

Here's a complete example that matches your usage pattern:

```typescript
import React, { useState } from 'react';
import { usePiPayments, PaymentButton } from '@xhilo/pi-sdk/react';

interface PaymentTestComponentProps {
  userId: string;
  isInitialized: boolean;
}

export default function PaymentTestComponent({ userId, isInitialized }: PaymentTestComponentProps) {
  const { createAndCompletePayment, isProcessing, error, success } = usePiPayments();
  const [paymentStatus, setPaymentStatus] = useState<string | null>(null);

  const handleCreatePayment = async () => {
    if (!isInitialized) {
      console.error('Pi SDK not initialized');
      return;
    }

    setPaymentStatus('Creating payment...');

    const result = await createAndCompletePayment({
      userId,
      amount: 1.0,
      memo: 'Test payment for MiniTasks app',
      metadata: {
        orderId: 'ORD-' + Math.random().toString(36).substring(7),
        item: 'Premium Feature Unlock',
        userId
      },
      onSuccess: (paymentId) => {
        setPaymentStatus('Payment completed successfully!');
        console.log('Payment completed:', paymentId);
      },
      onError: (error) => {
        setPaymentStatus(`Payment failed: ${error}`);
        console.error('Payment error:', error);
      },
      onCancel: () => {
        setPaymentStatus('Payment canceled');
        console.log('Payment canceled');
      }
    });

    if (!result.success) {
      setPaymentStatus(`Failed to initiate payment: ${result.message}`);
    }
  };

  return (
    <div className="p-4 border rounded-lg shadow-md max-w-sm mx-auto my-8">
      <h2 className="text-xl font-semibold mb-4">Test Pi Payments (U2A)</h2>
      <p className="mb-2">Requesting 1.0 Pi from user: {userId}</p>

      <button
        onClick={handleCreatePayment}
        disabled={isProcessing || !isInitialized}
        className="bg-green-600 text-white px-6 py-3 rounded-lg hover:bg-green-700 transition disabled:opacity-50 disabled:cursor-not-allowed"
      >
        {isProcessing ? 'Processing...' : 'Pay with Pi'}
      </button>

      {paymentStatus && (
        <p className="mt-4 text-center text-sm text-blue-700">{paymentStatus}</p>
      )}
      {error && (
        <p className="mt-4 text-center text-sm text-red-600">Error: {error}</p>
      )}
      {success && (
        <p className="mt-4 text-center text-sm text-green-600">Payment completed successfully!</p>
      )}
    </div>
  );
}
```

This modular approach provides:

1. **High-level hooks** for complete payment flows
2. **Simple hooks** for basic payments
3. **Ready-to-use components** for common UI patterns
4. **Utility functions** for payment-related operations
5. **Type safety** throughout the entire flow
6. **Automatic backend integration** with your existing API endpoints
