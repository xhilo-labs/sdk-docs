# PaymentProcessDisplay Component

Display real-time payment progress with visual feedback.

## 🚀 Basic Usage

```tsx
import { PaymentProcessDisplay } from '@xhilo/pi-sdk/react';

function PaymentComponent() {
  const [processData, setProcessData] = useState(null);
  
  return (
    <div>
      {/* Your payment button here */}
      {processData && (
        <PaymentProcessDisplay 
          processData={processData}
          onProcessUpdate={setProcessData}
        />
      )}
    </div>
  );
}
```

## 🔧 Props

### `PaymentProcessDisplayProps`

```tsx
interface PaymentProcessDisplayProps {
  processData: PaymentProcessData;     // Required: Payment process data
  className?: string;                  // Optional: CSS class
  showProgress?: boolean;              // Optional: Show progress bar (default: true)
  showTimestamp?: boolean;             // Optional: Show timestamp (default: true)
}
```

### `PaymentProcessData`

```tsx
interface PaymentProcessData {
  stage: 'initiating' | 'approving' | 'completing' | 'success' | 'error' | 'cancelled';
  message: string;
  paymentId?: string;
  txid?: string;
  progress?: number; // 0-100
  timestamp: string;
}
```

## 📝 Detailed Examples

### 1. Basic Payment Progress

```tsx
function BasicPaymentProgress() {
  const [processData, setProcessData] = useState(null);
  
  const handlePayment = async () => {
    // Simulate payment process
    setProcessData({
      stage: 'initiating',
      message: 'Starting payment...',
      progress: 0,
      timestamp: new Date().toISOString()
    });
    
    // Simulate progress updates
    setTimeout(() => {
      setProcessData({
        stage: 'approving',
        message: 'Approving payment...',
        progress: 50,
        timestamp: new Date().toISOString()
      });
    }, 1000);
    
    setTimeout(() => {
      setProcessData({
        stage: 'completing',
        message: 'Completing payment...',
        progress: 80,
        timestamp: new Date().toISOString()
      });
    }, 2000);
    
    setTimeout(() => {
      setProcessData({
        stage: 'success',
        message: 'Payment completed successfully!',
        progress: 100,
        timestamp: new Date().toISOString()
      });
    }, 3000);
  };
  
  return (
    <div>
      <button onClick={handlePayment}>
        Start Payment
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
function CustomStyledProgress() {
  const [processData, setProcessData] = useState(null);
  
  return (
    <div>
      <button onClick={handlePayment}>
        Start Payment
      </button>
      {processData && (
        <PaymentProcessDisplay 
          processData={processData}
          className="my-custom-progress"
          showProgress={true}
          showTimestamp={false}
        />
      )}
    </div>
  );
}
```

### 3. Error Handling

```tsx
function PaymentWithErrorHandling() {
  const [processData, setProcessData] = useState(null);
  
  const handlePayment = async () => {
    try {
      setProcessData({
        stage: 'initiating',
        message: 'Starting payment...',
        progress: 0,
        timestamp: new Date().toISOString()
      });
      
      // Simulate payment failure
      setTimeout(() => {
        setProcessData({
          stage: 'error',
          message: 'Payment failed: Insufficient funds',
          progress: 0,
          timestamp: new Date().toISOString()
        });
      }, 2000);
      
    } catch (error) {
      setProcessData({
        stage: 'error',
        message: `Payment error: ${error.message}`,
        progress: 0,
        timestamp: new Date().toISOString()
      });
    }
  };
  
  return (
    <div>
      <button onClick={handlePayment}>
        Start Payment
      </button>
      {processData && (
        <PaymentProcessDisplay processData={processData} />
      )}
    </div>
  );
}
```

### 4. Integration with Payment Hooks

```tsx
import { usePiPayments, PaymentProcessDisplay } from '@xhilo/pi-sdk/react';

function IntegratedPaymentComponent() {
  const { createAndCompletePayment } = usePiPayments();
  const [processData, setProcessData] = useState(null);
  
  const handlePayment = async () => {
    const result = await createAndCompletePayment({
      userId: 'user123',
      amount: 5.0,
      memo: 'Test Payment',
      onProcessUpdate: setProcessData
    });
    
    if (result.success) {
      console.log('Payment successful:', result.data);
    }
  };
  
  return (
    <div>
      <button onClick={handlePayment}>
        Make Payment
      </button>
      {processData && (
        <PaymentProcessDisplay processData={processData} />
      )}
    </div>
  );
}
```

## 🎨 Styling

### CSS Classes

The component uses the following CSS classes for styling:

```css
.payment-process-display {
  /* Main container */
}

.payment-process-stage {
  /* Stage indicator */
}

.payment-process-message {
  /* Status message */
}

.payment-process-progress {
  /* Progress bar container */
}

.payment-process-progress-bar {
  /* Progress bar fill */
}

.payment-process-timestamp {
  /* Timestamp display */
}

.payment-process-success {
  /* Success state styling */
}

.payment-process-error {
  /* Error state styling */
}
```

### Custom Styling

```css
.my-custom-progress {
  background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
  border-radius: 10px;
  padding: 20px;
  color: white;
}

.my-custom-progress .payment-process-progress-bar {
  background: rgba(255, 255, 255, 0.3);
  border-radius: 5px;
  height: 8px;
}
```

## 🎯 When to Use This Component

### ✅ Use `PaymentProcessDisplay` when:
- You want to show **real-time payment progress**
- You need **visual feedback** during payment processing
- You're building a **user-friendly payment experience**
- You want to **display payment status** and **error messages**

### ❌ Don't use `PaymentProcessDisplay` when:
- You don't need **visual progress feedback**
- You're building a **minimal payment interface**
- You prefer **simple text-based status** updates

## 🔧 Configuration

### Progress Stages

The component handles the following payment stages:

- **`initiating`** - Payment is being initiated
- **`approving`** - Payment is being approved
- **`completing`** - Payment is being completed
- **`success`** - Payment completed successfully
- **`error`** - Payment failed with error
- **`cancelled`** - Payment was cancelled

### Progress Data

```tsx
const processData = {
  stage: 'approving',           // Current stage
  message: 'Approving payment...', // User-friendly message
  paymentId: 'pay_123',         // Payment ID (optional)
  txid: 'tx_456',              // Transaction ID (optional)
  progress: 50,                 // Progress percentage (0-100)
  timestamp: '2024-01-01T12:00:00Z' // ISO timestamp
};
```

## 🆕 What's New in v0.2.14

- ✅ **Real-time Progress**: Visual progress bar and status updates
- ✅ **Error Handling**: Clear error message display
- ✅ **Customizable**: Configurable progress and timestamp display
- ✅ **TypeScript Support**: Complete type safety

## 🔗 Related Documentation

- [PaymentButton](./PaymentButton.md) - Payment button component
- [usePiPayments](./usePiPayments.md) - Payment hook with progress
- [Components Overview](./components.md) - All components
