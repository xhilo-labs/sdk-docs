# PaymentProcessDisplay Component

A React component for displaying real-time payment progress and status updates to users.

## 📋 Overview

`PaymentProcessDisplay` provides a visual representation of payment progress, showing users the current stage, progress percentage, and status messages during payment processing.

## 🚀 Basic Usage

```tsx
import { PaymentProcessDisplay } from '@xhilo/pi-sdk';

function PaymentComponent() {
  const [processData, setProcessData] = useState(null);

  return (
    <div>
      {/* Your payment button */}
      <PaymentProcessDisplay processData={processData} />
    </div>
  );
}
```

## 🔧 Props

### `PaymentProcessDisplayProps`

```tsx
interface PaymentProcessDisplayProps {
  processData: PaymentProcessData | null;  // Required: Process data from payment hook
  className?: string;                      // Optional: CSS class name
  style?: React.CSSProperties;            // Optional: Inline styles
  showProgress?: boolean;                 // Optional: Show progress bar (default: true)
  showTimestamp?: boolean;                // Optional: Show timestamp (default: false)
  variant?: 'default' | 'minimal' | 'detailed'; // Optional: Display variant
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

## 🎯 When to Use This Component

### ✅ Use `PaymentProcessDisplay` when:
- You're using **`usePiPayments`** hook for payment processing
- You want to show **real-time progress** to users
- You need **visual feedback** during payment processing
- You want to **improve user experience** with status updates
- You're building **e-commerce or subscription** features

### ❌ Don't use `PaymentProcessDisplay` when:
- You're using **`usePiSimplePayments`** (no progress data)
- You want **custom progress UI** (build your own)
- You don't need **visual progress indicators**

## 📝 Detailed Examples

### 1. Basic Usage

```tsx
import { usePiPayments, PaymentProcessDisplay } from '@xhilo/pi-sdk';

function BasicPayment() {
  const { createAndCompletePayment, isProcessing } = usePiPayments();
  const [processData, setProcessData] = useState(null);

  const handlePayment = async () => {
    const result = await createAndCompletePayment({
      userId: 'user123',
      amount: 5.0,
      memo: 'Test Payment',
      onProcessUpdate: setProcessData
    });
  };

  return (
    <div>
      <button onClick={handlePayment} disabled={isProcessing}>
        Make Payment
      </button>
      
      {processData && (
        <PaymentProcessDisplay processData={processData} />
      )}
    </div>
  );
}
```

### 2. Custom Styling

```tsx
function StyledPayment() {
  const { createAndCompletePayment } = usePiPayments();
  const [processData, setProcessData] = useState(null);

  return (
    <div>
      <button onClick={handlePayment}>Make Payment</button>
      
      {processData && (
        <PaymentProcessDisplay
          processData={processData}
          className="my-custom-payment-display"
          style={{
            marginTop: '20px',
            padding: '15px',
            border: '1px solid #ddd',
            borderRadius: '8px'
          }}
        />
      )}
    </div>
  );
}
```

### 3. Different Variants

```tsx
function VariantExamples() {
  const [processData, setProcessData] = useState(null);

  return (
    <div>
      {/* Default variant */}
      <PaymentProcessDisplay 
        processData={processData} 
        variant="default" 
      />
      
      {/* Minimal variant */}
      <PaymentProcessDisplay 
        processData={processData} 
        variant="minimal" 
      />
      
      {/* Detailed variant */}
      <PaymentProcessDisplay 
        processData={processData} 
        variant="detailed" 
        showTimestamp={true}
      />
    </div>
  );
}
```

### 4. E-commerce Integration

```tsx
function EcommercePayment({ product, user }) {
  const { createAndCompletePayment, isProcessing } = usePiPayments();
  const [processData, setProcessData] = useState(null);
  const [showProcess, setShowProcess] = useState(false);

  const handlePurchase = async () => {
    setShowProcess(true);
    
    const result = await createAndCompletePayment({
      userId: user.id,
      amount: product.price,
      memo: `Purchase: ${product.name}`,
      onProcessUpdate: setProcessData,
      onSuccess: () => {
        setShowProcess(false);
        // Redirect to success page
      },
      onError: () => {
        setShowProcess(false);
        // Show error message
      }
    });
  };

  return (
    <div className="product-card">
      <h3>{product.name}</h3>
      <p>Price: {product.price} Pi</p>
      
      <button 
        onClick={handlePurchase} 
        disabled={isProcessing}
      >
        {isProcessing ? 'Processing...' : 'Buy Now'}
      </button>
      
      {showProcess && processData && (
        <div className="payment-process">
          <PaymentProcessDisplay 
            processData={processData}
            variant="detailed"
            showTimestamp={true}
          />
        </div>
      )}
    </div>
  );
}
```

### 5. Subscription Flow

```tsx
function SubscriptionPayment({ subscription }) {
  const { createAndCompletePayment } = usePiPayments();
  const [processData, setProcessData] = useState(null);
  const [subscriptionStatus, setSubscriptionStatus] = useState('idle');

  const handleRenewal = async () => {
    setSubscriptionStatus('processing');
    
    const result = await createAndCompletePayment({
      userId: subscription.userId,
      amount: subscription.amount,
      memo: `Renewal: ${subscription.planName}`,
      onProcessUpdate: (data) => {
        setProcessData(data);
        
        // Update subscription status based on progress
        switch (data.stage) {
          case 'initiating':
            setSubscriptionStatus('processing');
            break;
          case 'approving':
            setSubscriptionStatus('verifying');
            break;
          case 'completing':
            setSubscriptionStatus('finalizing');
            break;
          case 'success':
            setSubscriptionStatus('active');
            break;
          case 'error':
            setSubscriptionStatus('failed');
            break;
        }
      }
    });
  };

  return (
    <div className="subscription-card">
      <h3>{subscription.planName}</h3>
      <p>Amount: {subscription.amount} Pi</p>
      <p>Status: {subscriptionStatus}</p>
      
      <button 
        onClick={handleRenewal} 
        disabled={subscriptionStatus === 'processing'}
      >
        {subscriptionStatus === 'processing' ? 'Processing...' : 'Renew'}
      </button>
      
      {processData && (
        <PaymentProcessDisplay 
          processData={processData}
          variant="minimal"
        />
      )}
    </div>
  );
}
```

### 6. Custom Progress Component

```tsx
function CustomProgressDisplay({ processData }) {
  if (!processData) return null;

  const getStageIcon = (stage) => {
    switch (stage) {
      case 'initiating': return '🚀';
      case 'approving': return '⏳';
      case 'completing': return '⚡';
      case 'success': return '✅';
      case 'error': return '❌';
      case 'cancelled': return '🚫';
      default: return '📋';
    }
  };

  const getStageColor = (stage) => {
    switch (stage) {
      case 'initiating': return '#3498db';
      case 'approving': return '#f39c12';
      case 'completing': return '#9b59b6';
      case 'success': return '#27ae60';
      case 'error': return '#e74c3c';
      case 'cancelled': return '#95a5a6';
      default: return '#34495e';
    }
  };

  return (
    <div 
      className="custom-progress"
      style={{
        padding: '20px',
        border: `2px solid ${getStageColor(processData.stage)}`,
        borderRadius: '10px',
        backgroundColor: '#f8f9fa'
      }}
    >
      <div style={{ display: 'flex', alignItems: 'center', marginBottom: '10px' }}>
        <span style={{ fontSize: '24px', marginRight: '10px' }}>
          {getStageIcon(processData.stage)}
        </span>
        <h4 style={{ margin: 0, color: getStageColor(processData.stage) }}>
          {processData.message}
        </h4>
      </div>
      
      {processData.progress !== undefined && (
        <div style={{ marginBottom: '10px' }}>
          <div 
            style={{
              width: '100%',
              height: '8px',
              backgroundColor: '#e0e0e0',
              borderRadius: '4px',
              overflow: 'hidden'
            }}
          >
            <div
              style={{
                width: `${processData.progress}%`,
                height: '100%',
                backgroundColor: getStageColor(processData.stage),
                transition: 'width 0.3s ease'
              }}
            />
          </div>
          <p style={{ margin: '5px 0 0 0', fontSize: '14px', color: '#666' }}>
            {processData.progress}% complete
          </p>
        </div>
      )}
      
      {processData.paymentId && (
        <p style={{ margin: '5px 0', fontSize: '12px', color: '#666' }}>
          Payment ID: {processData.paymentId}
        </p>
      )}
      
      {processData.txid && (
        <p style={{ margin: '5px 0', fontSize: '12px', color: '#666' }}>
          Transaction ID: {processData.txid}
        </p>
      )}
    </div>
  );
}

// Usage
function CustomPayment() {
  const { createAndCompletePayment } = usePiPayments();
  const [processData, setProcessData] = useState(null);

  return (
    <div>
      <button onClick={handlePayment}>Make Payment</button>
      <CustomProgressDisplay processData={processData} />
    </div>
  );
}
```

## 🎨 Styling

### CSS Classes

The component uses these CSS classes for styling:

```css
.payment-process-display {
  /* Main container */
}

.payment-process-display--minimal {
  /* Minimal variant */
}

.payment-process-display--detailed {
  /* Detailed variant */
}

.payment-process-display__stage {
  /* Stage indicator */
}

.payment-process-display__message {
  /* Status message */
}

.payment-process-display__progress {
  /* Progress bar container */
}

.payment-process-display__progress-bar {
  /* Progress bar */
}

.payment-process-display__details {
  /* Additional details */
}
```

### Custom Styling

```css
.my-custom-payment-display {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border-radius: 15px;
  padding: 20px;
  box-shadow: 0 4px 15px rgba(0,0,0,0.1);
}

.my-custom-payment-display .payment-process-display__progress-bar {
  background: linear-gradient(90deg, #ff6b6b, #4ecdc4);
  height: 10px;
  border-radius: 5px;
}
```

## ⚠️ Important Notes

1. **Requires Process Data** - Only works with `usePiPayments` hook
2. **Real-time Updates** - Updates automatically as payment progresses
3. **Responsive Design** - Adapts to different screen sizes
4. **Accessibility** - Includes proper ARIA labels and semantic HTML
5. **Customizable** - Supports custom styling and variants

## 🔗 Related Components

- [`usePiPayments`](./usePiPayments.md) - Hook that provides process data
- [`PaymentButton`](./PaymentButton.md) - Pre-built payment button
- [`SimplePaymentButton`](./SimplePaymentButton.md) - Simple payment button
