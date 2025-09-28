---
layout: default
title: Getting Started
---

# Getting Started

This guide will help you get up and running with the Pi SDK in just a few minutes.

## 📋 Prerequisites

- Node.js 16 or higher
- npm or yarn
- Pi Network developer account
- Pi Browser (for testing)

## 🚀 Installation

### Option 1: Install SDK Only

```bash
npm install @xhilo/pi-sdk
```

### Option 2: Create New App with Template

```bash
npm install @xhilo/pi-sdk --template -nextjs
```

This creates a complete Next.js application with Pi SDK integration.

## ⚙️ Configuration

### 1. Environment Variables

Create a `.env.local` file in your project root:

```env
# Required
PI_API_KEY=your_pi_api_key_here
PI_WALLET_PRIVATE_SEED=your_wallet_private_seed_here
NEXT_PUBLIC_APP_URL=http://localhost:3000

# Optional
NEXT_PUBLIC_PI_SANDBOX=true
```

### 2. Get Your Pi Network Credentials

1. **API Key**: Get from [Pi Network Developer Portal](https://developers.minepi.com/)
2. **Wallet Private Seed**: Generate from your Pi wallet
3. **App URL**: Your application's URL for payment callbacks

## 🎯 Basic Usage

### React Integration

```tsx
import { useXhiloPiNetwork, usePiPayments, usePiSimplePayments } from '@xhilo/pi-sdk/react'

function App() {
  return <MyComponent />
}

function MyComponent() {
  const { user, isInitialized, authenticate } = useXhiloPiNetwork()
  const { createAndCompletePayment } = usePiPayments()
  const { createSimplePayment } = usePiSimplePayments()
  
  if (!isInitialized) {
    return <div>Initializing Pi SDK...</div>
  }
  
  if (!user) {
    return (
      <button onClick={() => authenticate(['payments'])}>
        Login with Pi Network
      </button>
    )
  }
  
  return <div>Welcome, {user.username}!</div>
}
```

### Backend Integration

```typescript
import { 
  processA2UWithdrawalAction, 
  approvePaymentAction,
  PiBackendConfig,
  PiPlatformConfig 
} from '@xhilo/pi-sdk/backend'

// Configuration objects (required for all backend functions)
const piBackendConfig: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!
}

const piPlatformConfig: PiPlatformConfig = {
  apiKey: process.env.PI_API_KEY!
}

// A2U Payment (App-to-User)
const a2uResult = await processA2UWithdrawalAction({
  withdrawalAmount: 1.0,
  userId: 'user123',
  memo: 'Test payment',
  metadata: { userId: '123' }
}, piBackendConfig)

// U2A Payment (User-to-App) 
const u2aResult = await approvePaymentAction({
  paymentId: 'payment123',
  userId: 'user123'
}, piPlatformConfig)
```

## 🔧 Next.js Setup

### 1. Add Pi SDK Script

The template includes this automatically, but if you're setting up manually:

```tsx
// app/layout.tsx
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <head>
        <script src="https://sdk.minepi.com/pi-sdk.js" async />
      </head>
      <body>
        {children}
      </body>
    </html>
  )
}
```

### 2. Create Main App Component

```tsx
// app/page.tsx
'use client'

import { useXhiloPiNetwork } from '@xhilo/pi-sdk/react'

export default function HomePage() {
  const { initialize, authenticate, user, isInitialized } = useXhiloPiNetwork()

  useEffect(() => {
    if (!isInitialized) {
      initialize()
    }
  }, [isInitialized, initialize])

  if (!user) {
    return (
      <div>
        <h1>Welcome to My Pi App</h1>
        <button onClick={() => authenticate(['username', 'payments'])}>
          Sign In with Pi
        </button>
      </div>
    )
  }

  return (
    <div>
      <h1>Welcome, {user.username}!</h1>
      <p>You are signed in with Pi Network</p>
    </div>
  )
}
```

## 💳 Making Your First Payment

### Simple Payment

```tsx
import { usePiSimplePayments } from '@xhilo/pi-sdk/react'

function PaymentComponent() {
  const { createSimplePayment } = usePiSimplePayments()

  const handlePayment = async () => {
    try {
      const result = await createSimplePayment({
        userId: 'user123',
        amount: 1.0,
        itemName: 'Test Item',
        customMetadata: { orderId: '123' }
      }, {
        onSuccess: (paymentId) => console.log('Payment successful:', paymentId),
        onError: (error) => console.error('Payment failed:', error),
        onCancel: () => console.log('Payment cancelled')
      })
      console.log('Payment result:', result)
    } catch (error) {
      console.error('Payment failed:', error)
    }
  }

  return (
    <button onClick={handlePayment}>
      Pay 1 Pi
    </button>
  )
}
```

### Advanced Payment with Backend

```tsx
import { usePiPayments } from '@xhilo/pi-sdk/react'

function AdvancedPayment() {
  const { createAndCompletePayment, isProcessing, error, success } = usePiPayments()

  const handlePayment = async () => {
    try {
      const result = await createAndCompletePayment({
        userId: 'user123',
        amount: 5.0,
        memo: 'Advanced payment',
        metadata: { orderId: '123' },
        onSuccess: (paymentId) => console.log('Payment successful:', paymentId),
        onError: (error) => console.error('Payment failed:', error),
        onCancel: () => console.log('Payment cancelled'),
        onProcessUpdate: (processData) => {
          console.log('Payment process:', processData.stage, processData.message)
        }
      })
      console.log('Payment result:', result)
    } catch (error) {
      console.error('Payment failed:', error)
    }
  }

  return (
    <div>
      <button onClick={handlePayment} disabled={isProcessing}>
        {isProcessing ? 'Processing...' : 'Pay 5 Pi'}
      </button>
      {error && <div className="error">Error: {error}</div>}
      {success && <div className="success">Payment successful!</div>}
    </div>
  )
}
```

## 🧪 Testing

### 1. Start Development Server

```bash
npm run dev
```

### 2. Open in Pi Browser

- Install [Pi Browser](https://minepi.com/download)
- Navigate to your app URL
- Test authentication and payments

### 3. Use Debug Panel

The template includes a debug panel to monitor:
- SDK initialization status
- User authentication state
- Payment processing logs
- Error messages

## 🎉 Next Steps

- Explore the [API Reference](/api-reference) for detailed documentation
- Check out [Examples](/examples) for common use cases
- Use the [Template](/templates) for a complete Next.js setup
- Read [Troubleshooting](/troubleshooting) if you encounter issues

## 🆘 Need Help?

- Check the [Troubleshooting](/troubleshooting) section
- Review the [Examples](/examples) for common patterns
- Open an issue on [GitHub](https://github.com/xhilo-labs/pi-sdk/issues)
