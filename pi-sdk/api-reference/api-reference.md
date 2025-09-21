---
layout: default
title: API Reference
---

# API Reference

Complete API documentation for the Pi SDK.

## React Components

### XhiloPiProvider

Context provider that wraps your React application to provide Pi Network functionality.

```tsx
import { XhiloPiProvider } from '@xhilo/pi-sdk/react'

<XhiloPiProvider>
  <App />
</XhiloPiProvider>
```

**Props:**
- `children: React.ReactNode` - Your app components
- `config?: PiConfig` - Optional configuration

### PaymentButton

Ready-to-use payment button component.

```tsx
import { PaymentButton } from '@xhilo/pi-sdk/react'

<PaymentButton
  amount={1.0}
  memo="Test payment"
  onSuccess={(payment) => console.log('Success:', payment)}
  onError={(error) => console.error('Error:', error)}
/>
```

**Props:**
- `amount: number` - Payment amount in Pi
- `memo: string` - Payment description
- `onSuccess: (payment: Payment) => void` - Success callback
- `onError: (error: Error) => void` - Error callback
- `onCancel: () => void` - Cancel callback
- `metadata?: object` - Additional payment data

### SimplePaymentButton

Simplified payment button for basic use cases.

```tsx
import { SimplePaymentButton } from '@xhilo/pi-sdk/react'

<SimplePaymentButton
  amount={1.0}
  memo="Simple payment"
  onSuccess={(payment) => console.log('Success:', payment)}
/>
```

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

## React Hooks

### useXhiloPi

Main hook for accessing the Pi SDK instance.

```tsx
import { useXhiloPi } from '@xhilo/pi-sdk/react'

function MyComponent() {
  const pi = useXhiloPi()
  
  // Access SDK methods
  const initialize = pi.initialize
  const authenticate = pi.authenticate
  // ... other methods
}
```

**Returns:**
- `PiNetworkClient` instance with all SDK methods

### useXhiloPiUser

Hook to access user authentication state.

```tsx
import { useXhiloPiUser } from '@xhilo/pi-sdk/react'

function UserInfo() {
  const user = useXhiloPiUser()
  
  if (user) {
    return <div>Welcome, {user.username}!</div>
  }
  
  return <div>Please log in</div>
}
```

**Returns:**
- `PiUser | null` - Current user or null if not authenticated

### useXhiloPiReady

Hook to check if the SDK is initialized and ready.

```tsx
import { useXhiloPiReady } from '@xhilo/pi-sdk/react'

function StatusIndicator() {
  const isReady = useXhiloPiReady()
  
  return (
    <div>
      SDK Status: {isReady ? 'Ready' : 'Loading...'}
    </div>
  )
}
```

**Returns:**
- `boolean` - True if SDK is ready

### usePiPayments

Hook for advanced payment processing.

```tsx
import { usePiPayments } from '@xhilo/pi-sdk/react'

function PaymentComponent() {
  const { createPayment, approvePayment, completePayment } = usePiPayments()
  
  const handlePayment = async () => {
    const payment = await createPayment({
      amount: 1.0,
      memo: 'Test payment'
    })
    
    const approved = await approvePayment(payment.identifier)
    if (approved) {
      await completePayment(payment.identifier)
    }
  }
}
```

**Returns:**
- `createPayment: (params: CreatePaymentParams) => Promise<Payment>`
- `approvePayment: (identifier: string) => Promise<boolean>`
- `completePayment: (identifier: string) => Promise<Payment>`

### usePiSimplePayments

Hook for simplified payment processing.

```tsx
import { usePiSimplePayments } from '@xhilo/pi-sdk/react'

function SimplePayment() {
  const { createPayment } = usePiSimplePayments()
  
  const handlePayment = async () => {
    const payment = await createPayment({
      amount: 1.0,
      memo: 'Simple payment'
    })
  }
}
```

### useXhiloPiLogs

Hook to access SDK console logs.

```tsx
import { useXhiloPiLogs } from '@xhilo/pi-sdk/react'

function DebugPanel() {
  const logs = useXhiloPiLogs()
  
  return (
    <div>
      {logs.map((log, index) => (
        <div key={index}>{log.message}</div>
      ))}
    </div>
  )
}
```

## Backend Integration

### PiBackendClient

Server-side client for Pi Network integration.

```javascript
import { PiBackendClient } from '@xhilo/pi-sdk/backend'

const piClient = new PiBackendClient({
  apiKey: process.env.PI_API_KEY,
  walletPrivateSeed: process.env.PI_WALLET_PRIVATE_SEED,
  sandbox: process.env.NODE_ENV !== 'production'
})
```

**Constructor Options:**
- `apiKey: string` - Your Pi Network API key
- `walletPrivateSeed: string` - Your wallet private seed
- `sandbox?: boolean` - Use sandbox environment (default: false)

### Methods

#### createPayment

Create a new payment.

```javascript
const payment = await piClient.createPayment({
  amount: 1.0,
  memo: 'Test payment',
  metadata: { userId: '123' }
})
```

**Parameters:**
- `amount: number` - Payment amount in Pi
- `memo: string` - Payment description
- `metadata?: object` - Additional payment data

**Returns:**
- `Promise<Payment>` - Created payment object

#### approvePayment

Approve a payment.

```javascript
const approved = await piClient.approvePayment(paymentIdentifier)
```

**Parameters:**
- `paymentIdentifier: string` - Payment identifier

**Returns:**
- `Promise<boolean>` - True if approved

#### completePayment

Complete a payment.

```javascript
const completed = await piClient.completePayment(paymentIdentifier)
```

**Parameters:**
- `paymentIdentifier: string` - Payment identifier

**Returns:**
- `Promise<Payment>` - Completed payment object

#### getPayment

Get payment details.

```javascript
const payment = await piClient.getPayment(paymentIdentifier)
```

#### cancelPayment

Cancel a payment.

```javascript
const cancelled = await piClient.cancelPayment(paymentIdentifier)
```

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
