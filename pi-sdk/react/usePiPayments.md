# usePiPayments Hook

High-level hook for **User-to-App (U2A) payments** with backend integration, real-time progress updates, and complete payment flow management.

## 📋 Overview

`usePiPayments` is designed for applications that need **full payment processing** with backend integration. It handles the entire payment flow from initiation to completion, including server-side approval and completion steps.

## 🚀 Basic Usage

```tsx
import { usePiPayments, PaymentProcessDisplay } from '@xhilo/pi-sdk';

function PaymentComponent() {
  const { 
    createAndCompletePayment, 
    isProcessing, 
    error, 
    success 
  } = usePiPayments();

  const [processData, setProcessData] = useState(null);

  const handlePayment = async () => {
    const result = await createAndCompletePayment({
      userId: 'user123',
      amount: 5.0,
      memo: 'Premium Subscription',
      apiConfig: {
        baseUrl: 'https://myapi.com',
        approveEndpoint: '/api/payments/approve',
        completeEndpoint: '/api/payments/complete'
      },
      onProcessUpdate: setProcessData,
      onSuccess: (paymentId) => console.log('Payment completed:', paymentId)
    });
  };

  return (
    <div>
      <button onClick={handlePayment} disabled={isProcessing}>
        {isProcessing ? 'Processing...' : 'Buy Premium'}
      </button>
      {processData && <PaymentProcessDisplay processData={processData} />}
    </div>
  );
}
```

## 🔧 Parameters

### `CreateAndCompletePaymentOptions`

```tsx
interface CreateAndCompletePaymentOptions {
  userId: string;                    // Required: User ID
  amount: number;                    // Required: Payment amount
  memo: string;                      // Required: Payment description
  metadata?: Record<string, any>;    // Optional: Additional metadata
  onSuccess?: (paymentId: string) => void;           // Optional: Success callback
  onError?: (error: string) => void;                 // Optional: Error callback
  onCancel?: () => void;                             // Optional: Cancel callback
  onProcessUpdate?: (processData: PaymentProcessData) => void; // Optional: Progress callback
  apiConfig?: {                     // Optional: API configuration
    baseUrl?: string;               // Default: '' (relative URLs)
    approveEndpoint?: string;       // Default: '/api/payments/approve'
    completeEndpoint?: string;      // Default: '/api/payments/complete'
  };
}
```

### `PaymentProcessData`

```tsx
interface PaymentProcessData {
  stage: 'initiating' | 'approving' | 'completing' | 'success' | 'error' | 'cancelled';
  message: string;                  // Human-readable status message
  paymentId?: string;               // Payment ID (when available)
  txid?: string;                    // Transaction ID (when available)
  progress?: number;                // Progress percentage (0-100)
  timestamp: string;                // ISO timestamp
}
```

## 📤 Return Values

- **`createAndCompletePayment`** - `(options) => Promise<PiOperationResult<string>>` - Main payment function
- **`isProcessing`** - `boolean` - Whether a payment is currently being processed
- **`error`** - `string | null` - Current error message (if any)
- **`success`** - `boolean` - Whether the last payment was successful

## 🎯 When to Use This Hook

### ✅ Use `usePiPayments` when:
- You need **User-to-App (U2A) payments** with backend processing
- You want **real-time progress updates** for users
- You need to **integrate with your backend API** for approval/completion
- You want a **complete payment flow** with server-side validation
- You need **detailed payment tracking** and status updates
- You're building **e-commerce or subscription** features

### ❌ Don't use `usePiPayments` when:
- You want **simple payments** without backend (use `usePiSimplePayments`)
- You need **App-to-User payments** (use backend hooks)
- You want **basic payment creation** only (use `useXhiloPiNetwork`)

## 📝 Detailed Examples

### 1. Basic Payment with Progress Display

```tsx
import { usePiPayments, PaymentProcessDisplay } from '@xhilo/pi-sdk';

function BasicPayment() {
  const { createAndCompletePayment, isProcessing, error } = usePiPayments();
  const [processData, setProcessData] = useState(null);

  const handlePayment = async () => {
    const result = await createAndCompletePayment({
      userId: 'user123',
      amount: 10.0,
      memo: 'Premium Plan - Monthly',
      metadata: {
        planType: 'premium',
        billingCycle: 'monthly'
      },
      onProcessUpdate: setProcessData,
      onSuccess: (paymentId) => {
        console.log('Payment successful:', paymentId);
        alert('Payment completed successfully!');
      },
      onError: (error) => {
        console.error('Payment failed:', error);
        alert('Payment failed: ' + error);
      },
      onCancel: () => {
        console.log('Payment canceled');
        alert('Payment was canceled');
      }
    });

    if (!result.success) {
      console.error('Payment initiation failed:', result.message);
    }
  };

  return (
    <div>
      <button onClick={handlePayment} disabled={isProcessing}>
        {isProcessing ? 'Processing Payment...' : 'Subscribe to Premium'}
      </button>
      
      {error && (
        <div style={{ color: 'red', marginTop: '10px' }}>
          Error: {error}
        </div>
      )}
      
      {processData && (
        <PaymentProcessDisplay 
          processData={processData} 
          style={{ marginTop: '20px' }}
        />
      )}
    </div>
  );
}
```

### 2. Custom API Configuration

```tsx
function CustomAPIPayment() {
  const { createAndCompletePayment } = usePiPayments();

  const handlePayment = async () => {
    const result = await createAndCompletePayment({
      userId: 'user456',
      amount: 25.0,
      memo: 'Custom Product Purchase',
      apiConfig: {
        baseUrl: 'https://api.mycompany.com/v1',
        approveEndpoint: '/payments/approve',
        completeEndpoint: '/payments/complete'
      },
      onProcessUpdate: (data) => {
        console.log('Payment progress:', data);
        // Custom progress handling
        if (data.stage === 'approving') {
          showCustomLoader('Waiting for server approval...');
        }
      }
    });
  };

  return <button onClick={handlePayment}>Buy Custom Product</button>;
}
```

### 3. E-commerce Integration

```tsx
function EcommercePayment({ product, user }) {
  const { createAndCompletePayment, isProcessing } = usePiPayments();
  const [orderStatus, setOrderStatus] = useState('pending');

  const handlePurchase = async () => {
    const result = await createAndCompletePayment({
      userId: user.id,
      amount: product.price,
      memo: `Purchase: ${product.name}`,
      metadata: {
        productId: product.id,
        productName: product.name,
        category: product.category,
        orderDate: new Date().toISOString()
      },
      apiConfig: {
        baseUrl: process.env.REACT_APP_API_URL,
        approveEndpoint: '/orders/approve-payment',
        completeEndpoint: '/orders/complete-payment'
      },
      onProcessUpdate: (data) => {
        // Update order status based on payment progress
        switch (data.stage) {
          case 'initiating':
            setOrderStatus('processing');
            break;
          case 'approving':
            setOrderStatus('verifying');
            break;
          case 'completing':
            setOrderStatus('finalizing');
            break;
          case 'success':
            setOrderStatus('completed');
            break;
          case 'error':
            setOrderStatus('failed');
            break;
        }
      },
      onSuccess: (paymentId) => {
        // Redirect to success page or show confirmation
        window.location.href = `/order-success?paymentId=${paymentId}`;
      }
    });
  };

  return (
    <div>
      <h3>{product.name}</h3>
      <p>Price: {product.price} Pi</p>
      <p>Status: {orderStatus}</p>
      
      <button 
        onClick={handlePurchase} 
        disabled={isProcessing || orderStatus === 'completed'}
      >
        {isProcessing ? 'Processing...' : 'Buy Now'}
      </button>
    </div>
  );
}
```

### 4. Subscription Management

```tsx
function SubscriptionPayment({ subscription, onSubscriptionUpdate }) {
  const { createAndCompletePayment, isProcessing } = usePiPayments();

  const handleRenewal = async () => {
    const result = await createAndCompletePayment({
      userId: subscription.userId,
      amount: subscription.amount,
      memo: `Renewal: ${subscription.planName}`,
      metadata: {
        subscriptionId: subscription.id,
        planName: subscription.planName,
        renewalDate: new Date().toISOString(),
        billingCycle: subscription.billingCycle
      },
      onProcessUpdate: (data) => {
        // Update subscription status
        onSubscriptionUpdate({
          ...subscription,
          status: data.stage === 'success' ? 'active' : 'processing'
        });
      },
      onSuccess: (paymentId) => {
        // Update subscription in database
        updateSubscription(subscription.id, {
          status: 'active',
          lastPaymentId: paymentId,
          nextBillingDate: calculateNextBillingDate(subscription.billingCycle)
        });
      }
    });
  };

  return (
    <div>
      <h3>{subscription.planName}</h3>
      <p>Amount: {subscription.amount} Pi</p>
      <p>Status: {subscription.status}</p>
      
      <button 
        onClick={handleRenewal} 
        disabled={isProcessing || subscription.status === 'active'}
      >
        {isProcessing ? 'Processing Renewal...' : 'Renew Subscription'}
      </button>
    </div>
  );
}
```

### 5. Error Handling and Retry Logic

```tsx
function RobustPayment() {
  const { createAndCompletePayment, isProcessing, error } = usePiPayments();
  const [retryCount, setRetryCount] = useState(0);
  const [processData, setProcessData] = useState(null);

  const handlePayment = async () => {
    try {
      const result = await createAndCompletePayment({
        userId: 'user789',
        amount: 15.0,
        memo: 'Robust Payment Test',
        onProcessUpdate: setProcessData,
        onError: (error) => {
          console.error('Payment error:', error);
          // Implement retry logic for certain errors
          if (error.includes('network') && retryCount < 3) {
            setTimeout(() => {
              setRetryCount(prev => prev + 1);
              handlePayment(); // Retry
            }, 2000);
          }
        }
      });

      if (result.success) {
        setRetryCount(0); // Reset retry count on success
      }
    } catch (error) {
      console.error('Unexpected error:', error);
    }
  };

  return (
    <div>
      <button onClick={handlePayment} disabled={isProcessing}>
        {isProcessing ? 'Processing...' : 'Make Payment'}
      </button>
      
      {error && (
        <div style={{ color: 'red' }}>
          Error: {error}
          {retryCount > 0 && <p>Retry attempt: {retryCount}/3</p>}
        </div>
      )}
      
      {processData && (
        <PaymentProcessDisplay processData={processData} />
      )}
    </div>
  );
}
```

## 🔧 Backend API Requirements

Your backend needs to implement these endpoints:

### POST `/api/payments/approve`
```json
{
  "paymentId": "string",
  "userId": "string"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Payment approved successfully"
}
```

### POST `/api/payments/complete`
```json
{
  "paymentId": "string",
  "txid": "string",
  "userId": "string"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Payment completed successfully"
}
```

## ⚠️ Important Notes

1. **Backend Integration Required** - This hook requires backend API endpoints
2. **Error Handling** - Always implement proper error handling and user feedback
3. **Loading States** - Use `isProcessing` to show loading indicators
4. **Progress Updates** - Implement `onProcessUpdate` for better UX
5. **API Configuration** - Configure your API endpoints correctly
6. **Testing** - Test with sandbox mode before production

## 🔗 Related Hooks

- [`useXhiloPiNetwork`](./useXhiloPiNetwork.md) - Low-level Pi Network functionality
- [`usePiSimplePayments`](./usePiSimplePayments.md) - Simple payments without backend
- [`PaymentProcessDisplay`](./PaymentProcessDisplay.md) - Display payment progress
