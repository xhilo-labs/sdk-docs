# Frontend-Backend Integration Example

This example shows how the React frontend hook and Node.js backend work together for Pi Network payments.

## Frontend (React) - User-to-App Payments

```typescript
// components/PaymentComponent.tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk/react';

export default function PaymentComponent({ userId }: { userId: string }) {
  const { createAndMakePayment, isInitialized } = useXhiloPiNetwork();

  const handlePayment = async () => {
    if (!isInitialized) return;

    const result = await createAndMakePayment(
      userId,
      1.0, // amount
      'Premium Feature', // item name
      {
        onReadyForServerApproval: async (paymentId) => {
          // Call your backend API to approve the payment
          const response = await fetch('/api/payments/approve', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ paymentId, userId })
          });
          const result = await response.json();
          if (!result.success) {
            throw new Error(result.message);
          }
        },
        onReadyForServerCompletion: async (paymentId, txid) => {
          // Call your backend API to complete the payment
          const response = await fetch('/api/payments/complete', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ paymentId, txid, userId })
          });
          const result = await response.json();
          if (!result.success) {
            throw new Error(result.message);
          }
        },
        onCancel: () => {
          console.log('Payment cancelled by user');
        },
        onError: (error) => {
          console.error('Payment error:', error);
        }
      }
    );

    if (result.success) {
      console.log('Payment initiated successfully');
    } else {
      console.error('Payment failed:', result.message);
    }
  };

  return (
    <button onClick={handlePayment} disabled={!isInitialized}>
      Pay 1 Pi for Premium Feature
    </button>
  );
}
```

## Backend (Node.js) - Server Actions

```typescript
// app/actions/payments.ts
import { approvePaymentAction, completePaymentAction } from '@xhilo/pi-sdk/backend';

export async function approvePayment(paymentId: string, userId: string) {
  return await approvePaymentAction({ paymentId, userId });
}

export async function completePayment(paymentId: string, txid: string, userId: string) {
  return await completePaymentAction({ paymentId, txid, userId });
}
```

## Backend (Node.js) - App-to-User Payments

```typescript
// app/actions/withdrawals.ts
import { processA2UWithdrawalAction } from '@xhilo/pi-sdk/backend';

export async function processWithdrawal(userId: string, amount: number) {
  return await processA2UWithdrawalAction({
    withdrawalAmount: amount,
    userId,
    memo: `Withdrawal from MyApp by user ${userId}`,
    metadata: {
      withdrawalId: `wd-${Date.now()}`,
      app: 'MyApp'
    }
  });
}
```

## Express.js API Routes

```typescript
// app/api/payments/approve/route.ts
import { approvePayment } from '@/app/actions/payments';

export async function POST(request: Request) {
  const { paymentId, userId } = await request.json();
  const result = await approvePayment(paymentId, userId);
  return Response.json(result);
}

// app/api/payments/complete/route.ts
import { completePayment } from '@/app/actions/payments';

export async function POST(request: Request) {
  const { paymentId, txid, userId } = await request.json();
  const result = await completePayment(paymentId, txid, userId);
  return Response.json(result);
}

// app/api/withdrawals/route.ts
import { processWithdrawal } from '@/app/actions/withdrawals';

export async function POST(request: Request) {
  const { userId, amount } = await request.json();
  const result = await processWithdrawal(userId, amount);
  return Response.json(result);
}
```

## Environment Variables

```bash
# .env
PI_API_KEY=your_pi_api_key
PI_WALLET_PRIVATE_SEED=your_wallet_private_seed
NODE_ENV=development
```

## Key Points

1. **Frontend Hook**: The `createAndMakePayment` function is in the React hook where it belongs
2. **Backend Actions**: Simple server-side functions that call the Pi Platform API
3. **Separation of Concerns**: Frontend handles UI and payment flow, backend handles server-side API calls
4. **Simple Integration**: Backend uses the same pattern as your original `pi-backend-sdk.ts`
5. **Type Safety**: Full TypeScript support across frontend and backend

This architecture follows the Pi Network documentation and keeps the frontend and backend properly separated while making them work together seamlessly.
