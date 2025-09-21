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
import { XhiloPiProvider } from '@xhilo/pi-sdk/react'
import { useXhiloPiUser } from '@xhilo/pi-sdk/react'

function App() {
  return (
    <XhiloPiProvider>
      <MyComponent />
    </XhiloPiProvider>
  )
}

function MyComponent() {
  const user = useXhiloPiUser()
  
  if (user) {
    return <div>Welcome, {user.username}!</div>
  }
  
  return <div>Please authenticate with Pi Network</div>
}
```

### Backend Integration

```javascript
import { PiBackendClient } from '@xhilo/pi-sdk/backend'

const piClient = new PiBackendClient({
  apiKey: process.env.PI_API_KEY,
  walletPrivateSeed: process.env.PI_WALLET_PRIVATE_SEED,
  sandbox: process.env.NODE_ENV !== 'production'
})

// Process payment
const payment = await piClient.createPayment({
  amount: 1.0,
  memo: 'Test payment',
  metadata: { userId: '123' }
})
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

### 2. Create Client Provider

```tsx
// components/ClientProvider.tsx
'use client'

import { XhiloPiProvider } from '@xhilo/pi-sdk/react'

export default function ClientProvider({ children }) {
  return <XhiloPiProvider>{children}</XhiloPiProvider>
}
```

### 3. Wrap Your App

```tsx
// app/layout.tsx
import ClientProvider from './components/ClientProvider'

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <ClientProvider>
          {children}
        </ClientProvider>
      </body>
    </html>
  )
}
```

## 💳 Making Your First Payment

### Simple Payment

```tsx
import { usePiSimplePayments } from '@xhilo/pi-sdk/react'

function PaymentComponent() {
  const { createPayment } = usePiSimplePayments()

  const handlePayment = async () => {
    try {
      const result = await createPayment({
        amount: 1.0,
        memo: 'Test payment',
        metadata: { item: 'test' }
      })
      console.log('Payment created:', result)
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
  const { createPayment, approvePayment, completePayment } = usePiPayments()

  const handlePayment = async () => {
    try {
      // 1. Create payment
      const payment = await createPayment({
        amount: 5.0,
        memo: 'Advanced payment',
        metadata: { orderId: '123' }
      })

      // 2. Approve payment (user action)
      const approved = await approvePayment(payment.identifier)

      if (approved) {
        // 3. Complete payment (backend call)
        const completed = await completePayment(payment.identifier)
        console.log('Payment completed:', completed)
      }
    } catch (error) {
      console.error('Payment failed:', error)
    }
  }

  return (
    <button onClick={handlePayment}>
      Pay 5 Pi
    </button>
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
