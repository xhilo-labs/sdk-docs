# API Reference

Complete API documentation for the Pi SDK.

## React Hooks

### useXhiloPiNetwork

Main hook for Pi Network integration with robust state management.

```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk/react'

function MyComponent() {
  const {
    createPayment,
    user,
    isInitialized,
    isAuthenticated,
    userId,
    getConsoleLogs
  } = useXhiloPiNetwork()
  
  // Use the hook methods
}
```

**Parameters:**
- None (uses global Pi Network state)

**Returns:**
- `createPayment: (paymentData: PaymentData) => Promise<PiOperationResult<string>>` - Create a payment
- `user: PiUser | null` - Current user information
- `isInitialized: boolean` - Whether Pi Network is initialized
- `isAuthenticated: boolean` - Whether user is authenticated
- `userId: string | null` - Current user ID
- `getConsoleLogs: () => ConsoleLog[]` - Get console logs for debugging

### usePiPayments

Advanced payment hook with backend integration.

```tsx
import { usePiPayments } from '@xhilo/pi-sdk/react'

function PaymentComponent() {
  const { createPayment, isProcessing, error, success } = usePiPayments({
    baseUrl: 'https://your-api.com'
  })
  
  const handlePayment = async () => {
    const result = await createPayment({
      amount: 1.0,
      memo: 'Test payment'
    })
  }
}
```

**Parameters:**
- `options?: { baseUrl?: string; onSuccess?: (paymentId: string) => void; onError?: (error: string) => void; onCancel?: () => void; onProcessUpdate?: (processData: PaymentProcessData) => void }`

**Returns:**
- `createPayment: (options: CreateAndCompletePaymentOptions) => Promise<PiOperationResult<string>>`
- `isProcessing: boolean` - Whether payment is currently processing
- `error: string | null` - Current error message
- `success: boolean` - Whether last payment was successful

### usePiSimplePayments

Simplified payment hook for easy payments.

```tsx
import { usePiSimplePayments } from '@xhilo/pi-sdk/react'

function SimplePaymentComponent() {
  const { createPayment } = usePiSimplePayments()
  
  const handlePayment = async () => {
    const result = await createPayment({
      amount: 1.0,
      memo: 'Simple payment'
    })
  }
}
```

**Parameters:**
- None

**Returns:**
- `createPayment: (options: SimplePaymentOptions) => Promise<PiOperationResult<string>>`

### usePiAds

Ads functionality with backend verification (temporary stub implementation).

```tsx
import { usePiAds } from '@xhilo/pi-sdk/react'

function AdsComponent() {
  const { 
    showAd, 
    verifyRewardedAd, 
    checkEligibility, 
    getAdStats,
    isProcessing,
    error 
  } = usePiAds()
  
  const handleShowAd = async () => {
    const result = await showAd('rewarded')
  }
}
```

**Parameters:**
- `sandbox?: boolean` - Use sandbox environment (default: false)
- `allowedOrigins?: string[]` - Allowed origins for security

**Returns:**
- `showAd: (adType: 'interstitial' | 'rewarded') => Promise<{ success: boolean; message?: string }>`
- `verifyRewardedAd: (adId: string, rewardAmount?: number, metadata?: Record<string, any>) => Promise<{ success: boolean; message?: string }>`
- `checkEligibility: (adType: 'interstitial' | 'rewarded', maxDailyViews?: number) => Promise<{ success: boolean; message?: string }>`
- `getAdStats: () => Promise<{ success: boolean; message?: string }>`
- `isProcessing: boolean` - Whether ad operation is processing
- `error: string | null` - Current error message

### usePiAdsSimple

Frontend-only ads display (temporary stub implementation).

```tsx
import { usePiAdsSimple } from '@xhilo/pi-sdk/react'

function SimpleAdsComponent() {
  const { showAd, isProcessing, error } = usePiAdsSimple()
  
  const handleShowAd = async () => {
    const result = await showAd('rewarded')
  }
}
```

**Parameters:**
- `sandbox?: boolean` - Use sandbox environment (default: false)
- `allowedOrigins?: string[]` - Allowed origins for security

**Returns:**
- `showAd: (adType: 'interstitial' | 'rewarded') => Promise<{ success: boolean; message?: string }>`
- `isProcessing: boolean` - Whether ad operation is processing
- `error: string | null` - Current error message

## React Components

### PaymentButton

Pre-built payment button with backend integration.

```tsx
import { PaymentButton } from '@xhilo/pi-sdk/react'

function MyComponent() {
  return (
    <PaymentButton
      userId="user123"
      amount={5.0}
      memo="Premium Feature"
      onSuccess={(paymentId) => console.log('Success:', paymentId)}
    >
      Buy Premium
    </PaymentButton>
  )
}
```

**Props:**
- `userId: string` - User ID
- `amount: number` - Payment amount
- `memo: string` - Payment memo
- `metadata?: Record<string, any>` - Additional metadata
- `className?: string` - CSS class
- `children?: React.ReactNode` - Button content
- `onSuccess?: (paymentId: string) => void` - Success callback
- `onError?: (error: string) => void` - Error callback
- `onCancel?: () => void` - Cancel callback

### PaymentProcessDisplay

Display real-time payment progress.

```tsx
import { PaymentProcessDisplay } from '@xhilo/pi-sdk/react'

function PaymentComponent() {
  const [processData, setProcessData] = useState(null)
  
  return (
    <div>
      {processData && (
        <PaymentProcessDisplay 
          processData={processData}
          showProgress={true}
          showTimestamp={true}
        />
      )}
    </div>
  )
}
```

**Props:**
- `processData: PaymentProcessData` - Payment process data
- `className?: string` - CSS class
- `showProgress?: boolean` - Show progress bar (default: true)
- `showTimestamp?: boolean` - Show timestamp (default: true)

### SimplePaymentButton

Simple payment button for basic use cases.

```tsx
import { SimplePaymentButton } from '@xhilo/pi-sdk/react'

function MyComponent() {
  return (
    <SimplePaymentButton
      userId="user123"
      amount={2.5}
      itemName="Digital Download"
      onSuccess={(paymentId) => console.log('Success:', paymentId)}
    >
      Buy Download
    </SimplePaymentButton>
  )
}
```

**Props:**
- `userId: string` - User ID
- `amount: number` - Payment amount
- `itemName: string` - Item name
- `customMetadata?: Record<string, any>` - Custom metadata
- `className?: string` - CSS class
- `children?: React.ReactNode` - Button content
- `onSuccess?: (paymentId: string) => void` - Success callback
- `onError?: (error: string) => void` - Error callback
- `onCancel?: () => void` - Cancel callback

## Backend Actions

All backend actions are **async** and require **explicit configuration parameters**.

### App-to-User (A2U) Payments

#### processA2UWithdrawalAction

Process a withdrawal from your app to a user.

```typescript
import { processA2UWithdrawalAction, PiBackendConfig } from '@xhilo/pi-sdk/backend'

const piBackendConfig: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!
}

const result = await processA2UWithdrawalAction({
  withdrawalAmount: 10.0,
  userId: 'user-uid',
  memo: 'Withdrawal from MyApp',
  metadata: { withdrawalId: 'wd-123' }
}, piBackendConfig)
```

**Parameters:**
- `args: ProcessA2UWithdrawalArgs` - Withdrawal arguments
- `config: PiBackendConfig` - Backend configuration

**Returns:**
- `Promise<ProcessA2UWithdrawalResult>` - Withdrawal result

#### createAndMakeA2UPayment

High-level helper for A2U payments.

```typescript
import { createAndMakeA2UPayment, PiBackendConfig } from '@xhilo/pi-sdk/backend'

const result = await createAndMakeA2UPayment(
  'user-uid',
  10.0,
  'Premium Feature',
  piBackendConfig,
  { customData: 'value' }
)
```

**Parameters:**
- `userId: string` - User ID
- `amount: number` - Payment amount
- `memo: string` - Payment memo
- `config: PiBackendConfig` - Backend configuration
- `metadata?: Record<string, any>` - Additional metadata

**Returns:**
- `Promise<ProcessA2UWithdrawalResult>` - Payment result

### User-to-App (U2A) Payments

#### approvePaymentAction

Approve a U2A payment.

```typescript
import { approvePaymentAction, PiPlatformConfig } from '@xhilo/pi-sdk/backend'

const piPlatformConfig: PiPlatformConfig = {
  apiKey: process.env.PI_API_KEY!
}

const result = await approvePaymentAction({
  paymentId: 'payment-123',
  userId: 'user-uid'
}, piPlatformConfig)
```

**Parameters:**
- `args: ApprovePaymentArgs` - Approval arguments
- `config: PiPlatformConfig` - Platform configuration

**Returns:**
- `Promise<PiOperationResult<PaymentDTO>>` - Approval result

#### completePaymentAction

Complete a U2A payment.

```typescript
import { completePaymentAction, PiPlatformConfig } from '@xhilo/pi-sdk/backend'

const result = await completePaymentAction({
  paymentId: 'payment-123',
  txid: 'transaction-id',
  userId: 'user-uid'
}, piPlatformConfig)
```

**Parameters:**
- `args: CompletePaymentArgs` - Completion arguments
- `config: PiPlatformConfig` - Platform configuration

**Returns:**
- `Promise<PiOperationResult<PaymentDTO>>` - Completion result

### Ads Verification

#### verifyRewardedAdAction

Verify a rewarded ad and process rewards.

```typescript
import { verifyRewardedAdAction, PiBackendConfig } from '@xhilo/pi-sdk/backend'

const result = await verifyRewardedAdAction({
  adId: 'ad123',
  rewardAmount: 0.1,
  userId: 'user123',
  metadata: { source: 'mobile' }
}, piBackendConfig)
```

**Parameters:**
- `args: VerifyRewardedAdArgs` - Verification arguments
- `config: PiBackendConfig` - Backend configuration

**Returns:**
- `Promise<PiOperationResult<VerifyRewardedAdResult>>` - Verification result

#### getAdEligibilityAction

Check user ad eligibility.

```typescript
import { getAdEligibilityAction, PiBackendConfig } from '@xhilo/pi-sdk/backend'

const eligibility = await getAdEligibilityAction({
  userId: 'user123',
  adType: 'rewarded',
  maxDailyViews: 5
}, piBackendConfig)
```

**Parameters:**
- `args: GetAdEligibilityArgs` - Eligibility arguments
- `config: PiBackendConfig` - Backend configuration

**Returns:**
- `Promise<PiOperationResult<AdEligibilityResult>>` - Eligibility result

#### getUserAdStatsAction

Get user ad statistics.

```typescript
import { getUserAdStatsAction, PiBackendConfig } from '@xhilo/pi-sdk/backend'

const stats = await getUserAdStatsAction('user123', piBackendConfig)
```

**Parameters:**
- `userId: string` - User ID
- `config: PiBackendConfig` - Backend configuration

**Returns:**
- `Promise<PiOperationResult<UserAdStatsResult>>` - User stats result

## Configuration Types

### PiBackendConfig

Configuration for A2U payments and ads verification.

```typescript
interface PiBackendConfig {
  apiKey: string
  privateSeed: string
  baseUrl?: string
}
```

### PiPlatformConfig

Configuration for U2A payments.

```typescript
interface PiPlatformConfig {
  apiKey: string
  baseUrl?: string
}
```

## Utility Functions

### getConsoleLogs

Get console logs for debugging.

```typescript
import { getConsoleLogs } from '@xhilo/pi-sdk/react'

const logs = getConsoleLogs()
```

**Returns:**
- `ConsoleLog[]` - Array of console logs

## Error Handling

All functions return a consistent result format:

```typescript
interface PiOperationResult<T = any> {
  success: boolean
  data?: T
  message?: string
}
```

**Example:**
```typescript
const result = await processA2UWithdrawalAction(args, config)

if (result.success) {
  console.log('Payment processed:', result.data)
} else {
  console.error('Payment failed:', result.message)
}
```

## TypeScript Support

The package includes complete TypeScript definitions:

```typescript
import type { 
  PiOperationResult,
  PaymentData,
  PiBackendConfig,
  PiPlatformConfig,
  ProcessA2UWithdrawalArgs,
  ApprovePaymentArgs,
  CompletePaymentArgs,
  VerifyRewardedAdArgs,
  GetAdEligibilityArgs,
  UserAdStatsResult,
  AdEligibilityResult,
  VerifyRewardedAdResult,
  ProcessA2UWithdrawalResult
} from '@xhilo/pi-sdk/backend'
```

---

*For more detailed examples, see the [Real Examples](./REAL_EXAMPLES.md) and [Configuration Guide](./CONFIGURATION_GUIDE.md).*