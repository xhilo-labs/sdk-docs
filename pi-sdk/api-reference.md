---
layout: default
title: API Reference
---

# API Reference

Complete API documentation for the Pi SDK.

## React Hooks

### useXhiloPiNetwork

Main hook for Pi Network integration with robust state management.

```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk/react'

function MyComponent() {
  const {
    initialize,
    authenticate,
    login,
    createPayment,
    createAndMakePayment,
    user,
    isInitialized,
    getConsoleLogs
  } = useXhiloPiNetwork(sandbox, allowedOrigins)
  
  // Use the hook methods
}
```

**Parameters:**
- `sandbox?: boolean` - Use sandbox environment (default: false)
- `allowedOrigins?: string[]` - Allowed origins for security

**Returns:**
- `UseXhiloPiNetworkReturn` - Object with all Pi Network methods and state

### usePiPayments

Advanced payment hook with backend integration.

```tsx
import { usePiPayments } from '@xhilo/pi-sdk/react'

function PaymentComponent() {
  const { createAndCompletePayment, isProcessing, error, success } = usePiPayments()
  
  const handlePayment = async () => {
    const result = await createAndCompletePayment({
      userId: 'user123',
      amount: 1.0,
      memo: 'Test payment',
      metadata: { orderId: '123' },
      onSuccess: (paymentId) => console.log('Success:', paymentId),
      onError: (error) => console.error('Error:', error),
      onCancel: () => console.log('Cancelled'),
      onProcessUpdate: (processData) => console.log('Process:', processData)
    })
  }
}
```

**Returns:**
- `createAndCompletePayment: (options: CreateAndCompletePaymentOptions) => Promise<PiOperationResult<string>>`
- `isProcessing: boolean` - Whether payment is currently processing
- `error: string | null` - Current error message
- `success: boolean` - Whether last payment was successful

### usePiSimplePayments

Simplified payment hook for easy payments.

```tsx
import { usePiSimplePayments } from '@xhilo/pi-sdk/react'

function SimplePaymentComponent() {
  const { createSimplePayment } = usePiSimplePayments()
  
  const handlePayment = async () => {
    const result = await createSimplePayment({
      userId: 'user123',
      amount: 1.0,
      itemName: 'Simple Item',
      customMetadata: { orderId: '123' }
    }, {
      onSuccess: (paymentId) => console.log('Success:', paymentId),
      onError: (error) => console.error('Error:', error),
      onCancel: () => console.log('Cancelled')
    })
  }
}
```

**Returns:**
- `createSimplePayment: (options: SimplePaymentOptions, callbacks?: PaymentCallbacks) => Promise<PiOperationResult<string>>`

### PaymentButton

Ready-to-use payment button component.

```tsx
import { PaymentButton } from '@xhilo/pi-sdk/react'

<PaymentButton
  userId="user123"
  amount={1.0}
  memo="Test payment"
  metadata={{ orderId: '123' }}
  onSuccess={(paymentId) => console.log('Success:', paymentId)}
  onError={(error) => console.error('Error:', error)}
  onCancel={() => console.log('Cancelled')}
  className="my-payment-button"
>
  Pay 1 Pi
</PaymentButton>
```

**Props:**
- `userId: string` - User ID for the payment
- `amount: number` - Payment amount in Pi
- `memo: string` - Payment description
- `metadata?: Record<string, any>` - Additional payment data
- `onSuccess?: (paymentId: string) => void` - Success callback
- `onError?: (error: string) => void` - Error callback
- `onCancel?: () => void` - Cancel callback
- `className?: string` - CSS class name
- `children?: React.ReactNode` - Button content

### SimplePaymentButton

Simplified payment button component.

```tsx
import { SimplePaymentButton } from '@xhilo/pi-sdk/react'

<SimplePaymentButton
  userId="user123"
  amount={1.0}
  itemName="Simple Item"
  customMetadata={{ orderId: '123' }}
  onSuccess={(paymentId) => console.log('Success:', paymentId)}
  onError={(error) => console.error('Error:', error)}
  onCancel={() => console.log('Cancelled')}
  className="my-simple-button"
>
  Buy Item
</SimplePaymentButton>
```

**Props:**
- `userId: string` - User ID for the payment
- `amount: number` - Payment amount in Pi
- `itemName: string` - Name of the item being purchased
- `customMetadata?: Record<string, any>` - Additional payment data
- `onSuccess?: (paymentId: string) => void` - Success callback
- `onError?: (error: string) => void` - Error callback
- `onCancel?: () => void` - Cancel callback
- `className?: string` - CSS class name
- `children?: React.ReactNode` - Button content

### PaymentProcessDisplay

Component to display payment processing status.

```tsx
import { PaymentProcessDisplay } from '@xhilo/pi-sdk/react'

<PaymentProcessDisplay
  payment={payment}
  showProgress={true}
  className="my-payment-display"
/>
```

**Props:**
- `payment: PaymentDTO` - Payment object to display
- `showProgress?: boolean` - Whether to show progress indicator
- `className?: string` - CSS class name

## Backend Integration

### Configuration-Based Architecture

The backend SDK uses a configuration-driven approach where all functions require explicit configuration parameters.

```typescript
import { 
  processA2UWithdrawalAction,
  approvePaymentAction,
  PiBackendConfig,
  PiPlatformConfig 
} from '@xhilo/pi-sdk/backend'

// Backend configuration for A2U payments
const piBackendConfig: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!
}

// Platform configuration for U2A payments
const piPlatformConfig: PiPlatformConfig = {
  apiKey: process.env.PI_API_KEY!
}
```

**Configuration Types:**
- `PiBackendConfig` - For App-to-User payments (requires apiKey + privateSeed)
- `PiPlatformConfig` - For User-to-App payments (requires apiKey only)

### Backend Actions

#### processA2UWithdrawalAction

Process an App-to-User withdrawal.

```typescript
const result = await processA2UWithdrawalAction({
  withdrawalAmount: 1.0,
  userId: 'user123',
  memo: 'Test payment',
  metadata: { userId: '123' }
}, piBackendConfig)
```

**Parameters:**
- `args.withdrawalAmount: number` - Amount in Pi to send
- `args.userId: string` - Pi Network user UID
- `args.memo: string` - Payment description
- `args.metadata: object` - Additional payment data
- `config: PiBackendConfig` - Backend configuration

**Returns:**
- `Promise<ProcessA2UWithdrawalResult>` - Withdrawal result

#### approvePaymentAction

Approve a User-to-App payment.

```typescript
const result = await approvePaymentAction({
  paymentId: 'payment123',
  userId: 'user123'
}, piPlatformConfig, 'access_token')
```

**Parameters:**
- `args.paymentId: string` - Payment ID from frontend
- `args.userId: string` - User ID
- `config: PiPlatformConfig` - Platform configuration
- `accessToken?: string` - Optional access token for validation

**Returns:**
- `Promise<PaymentActionResult>` - Approval result

#### completePaymentAction

Complete a User-to-App payment.

```typescript
const result = await completePaymentAction({
  paymentId: 'payment123',
  txid: 'transaction123',
  userId: 'user123'
}, piPlatformConfig, 'access_token')
```

**Parameters:**
- `args.paymentId: string` - Payment ID
- `args.txid: string` - Transaction ID from Pi Network
- `args.userId: string` - User ID
- `config: PiPlatformConfig` - Platform configuration
- `accessToken?: string` - Optional access token for validation

**Returns:**
- `Promise<PaymentActionResult>` - Completion result

#### verifyRewardedAdAction

Verify a rewarded ad and process rewards.

```typescript
const result = await verifyRewardedAdAction({
  adId: 'ad123',
  rewardAmount: 0.1,
  userId: 'user123'
}, piBackendConfig)
```

**Parameters:**
- `args.adId: string` - Ad ID from frontend
- `args.rewardAmount: number` - Reward amount in Pi
- `args.userId: string` - User ID
- `config: PiBackendConfig` - Backend configuration

**Returns:**
- `Promise<VerifyRewardedAdResult>` - Verification result

## Types

### PiUser

```typescript
interface PiUser {
  uid: string
  username: string
  walletAddress: string
  // ... other user properties
}
```

### Payment

```typescript
interface Payment {
  identifier: string
  amount: number
  memo: string
  metadata: object
  status: 'pending' | 'approved' | 'completed' | 'cancelled'
  // ... other payment properties
}
```

### CreatePaymentParams

```typescript
interface CreatePaymentParams {
  amount: number
  memo: string
  metadata?: object
}
```

## Error Handling

All SDK methods can throw errors. Always wrap in try-catch:

```tsx
try {
  const payment = await createPayment({
    amount: 1.0,
    memo: 'Test payment'
  })
} catch (error) {
  console.error('Payment failed:', error.message)
}
```

**Common Error Types:**
- `PiNetworkError` - General Pi Network errors
- `AuthenticationError` - Authentication failures
- `PaymentError` - Payment processing errors
- `NetworkError` - Network connectivity issues
