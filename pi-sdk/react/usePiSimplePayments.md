# usePiSimplePayments Hook

Simple hook for **basic payments** without backend integration. Perfect for prototyping, simple apps, or when you don't need server-side processing.

## 📋 Overview

`usePiSimplePayments` provides a simplified interface for creating payments without the complexity of backend integration. It's ideal for simple use cases where you just need to create a payment and handle basic callbacks.

## 🚀 Basic Usage

```tsx
import { usePiSimplePayments } from '@xhilo/pi-sdk';

function SimplePayment() {
  const { createSimplePayment, isInitialized } = usePiSimplePayments();

  const handlePayment = async () => {
    const result = await createSimplePayment(
      {
        userId: 'user123',
        amount: 1.0,
        itemName: 'Premium Feature'
      },
      {
        onSuccess: (paymentId) => console.log('Payment successful:', paymentId),
        onError: (error) => console.error('Payment failed:', error),
        onCancel: () => console.log('Payment canceled')
      }
    );
  };

  return (
    <button onClick={handlePayment} disabled={!isInitialized}>
      Buy Premium Feature
    </button>
  );
}
```

## 🔧 Parameters

### `SimplePaymentOptions`

```tsx
interface SimplePaymentOptions {
  userId: string;                    // Required: User ID
  amount: number;                    // Required: Payment amount
  itemName: string;                  // Required: Name of the item being purchased
  customMetadata?: Record<string, any>; // Optional: Additional metadata
}
```

### Callbacks

```tsx
interface Callbacks {
  onSuccess?: (paymentId: string) => void;  // Optional: Success callback
  onError?: (error: string) => void;        // Optional: Error callback
  onCancel?: () => void;                    // Optional: Cancel callback
}
```

## 📤 Return Values

- **`createSimplePayment`** - `(options, callbacks?) => Promise<PiOperationResult<string>>` - Create a simple payment
- **`isInitialized`** - `boolean` - Whether the Pi SDK is initialized

## 🎯 When to Use This Hook

### ✅ Use `usePiSimplePayments` when:
- You want **simple payments** without backend integration
- You're **prototyping** or building a **simple app**
- You don't need **real-time progress updates**
- You want **minimal setup** for basic payment functionality
- You're building a **client-side only** application
- You want to **test payment flows** quickly

### ❌ Don't use `usePiSimplePayments` when:
- You need **User-to-App payments with backend** (use `usePiPayments`)
- You need **server-side validation** or processing
- You want **real-time progress updates** (use `usePiPayments`)
- You're building **production e-commerce** (use `usePiPayments`)
- You need **complex payment flows** (use `useXhiloPiNetwork`)

## 📝 Detailed Examples

### 1. Basic Payment

```tsx
import { usePiSimplePayments } from '@xhilo/pi-sdk';

function BasicPayment() {
  const { createSimplePayment, isInitialized } = usePiSimplePayments();

  const handlePayment = async () => {
    const result = await createSimplePayment({
      userId: 'user123',
      amount: 2.5,
      itemName: 'Digital Download'
    });

    if (result.success) {
      console.log('Payment initiated successfully');
    } else {
      console.error('Payment failed:', result.message);
    }
  };

  return (
    <button onClick={handlePayment} disabled={!isInitialized}>
      Buy Digital Download
    </button>
  );
}
```

### 2. Payment with Callbacks

```tsx
function PaymentWithCallbacks() {
  const { createSimplePayment, isInitialized } = usePiSimplePayments();
  const [status, setStatus] = useState('idle');

  const handlePayment = async () => {
    setStatus('processing');
    
    const result = await createSimplePayment(
      {
        userId: 'user456',
        amount: 5.0,
        itemName: 'Premium Upgrade',
        customMetadata: {
          source: 'web-app',
          timestamp: new Date().toISOString()
        }
      },
      {
        onSuccess: (paymentId) => {
          console.log('Payment successful:', paymentId);
          setStatus('success');
          alert('Payment completed successfully!');
        },
        onError: (error) => {
          console.error('Payment failed:', error);
          setStatus('error');
          alert('Payment failed: ' + error);
        },
        onCancel: () => {
          console.log('Payment canceled');
          setStatus('canceled');
          alert('Payment was canceled');
        }
      }
    );

    if (!result.success) {
      setStatus('error');
    }
  };

  return (
    <div>
      <button onClick={handlePayment} disabled={!isInitialized || status === 'processing'}>
        {status === 'processing' ? 'Processing...' : 'Upgrade to Premium'}
      </button>
      <p>Status: {status}</p>
    </div>
  );
}
```

### 3. Multiple Payment Options

```tsx
function MultiplePayments() {
  const { createSimplePayment, isInitialized } = usePiSimplePayments();

  const products = [
    { id: 1, name: 'Basic Plan', price: 1.0 },
    { id: 2, name: 'Pro Plan', price: 5.0 },
    { id: 3, name: 'Enterprise Plan', price: 10.0 }
  ];

  const handleProductPurchase = async (product) => {
    const result = await createSimplePayment(
      {
        userId: 'user789',
        amount: product.price,
        itemName: product.name,
        customMetadata: {
          productId: product.id,
          purchaseDate: new Date().toISOString()
        }
      },
      {
        onSuccess: (paymentId) => {
          console.log(`${product.name} purchased successfully:`, paymentId);
          // Update UI to show purchase
        },
        onError: (error) => {
          console.error(`Failed to purchase ${product.name}:`, error);
        }
      }
    );
  };

  return (
    <div>
      <h3>Choose a Plan</h3>
      {products.map(product => (
        <div key={product.id} style={{ margin: '10px 0', padding: '10px', border: '1px solid #ccc' }}>
          <h4>{product.name}</h4>
          <p>Price: {product.price} Pi</p>
          <button 
            onClick={() => handleProductPurchase(product)}
            disabled={!isInitialized}
          >
            Buy {product.name}
          </button>
        </div>
      ))}
    </div>
  );
}
```

### 4. Form-Based Payment

```tsx
function FormPayment() {
  const { createSimplePayment, isInitialized } = usePiSimplePayments();
  const [formData, setFormData] = useState({
    amount: '',
    itemName: '',
    userId: ''
  });

  const handleSubmit = async (e) => {
    e.preventDefault();
    
    if (!formData.amount || !formData.itemName || !formData.userId) {
      alert('Please fill in all fields');
      return;
    }

    const result = await createSimplePayment(
      {
        userId: formData.userId,
        amount: parseFloat(formData.amount),
        itemName: formData.itemName,
        customMetadata: {
          formSubmitted: true,
          timestamp: new Date().toISOString()
        }
      },
      {
        onSuccess: (paymentId) => {
          console.log('Form payment successful:', paymentId);
          // Reset form
          setFormData({ amount: '', itemName: '', userId: '' });
        },
        onError: (error) => {
          console.error('Form payment failed:', error);
        }
      }
    );
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label>User ID:</label>
        <input
          type="text"
          value={formData.userId}
          onChange={(e) => setFormData({...formData, userId: e.target.value})}
          required
        />
      </div>
      
      <div>
        <label>Item Name:</label>
        <input
          type="text"
          value={formData.itemName}
          onChange={(e) => setFormData({...formData, itemName: e.target.value})}
          required
        />
      </div>
      
      <div>
        <label>Amount (Pi):</label>
        <input
          type="number"
          step="0.1"
          value={formData.amount}
          onChange={(e) => setFormData({...formData, amount: e.target.value})}
          required
        />
      </div>
      
      <button type="submit" disabled={!isInitialized}>
        Create Payment
      </button>
    </form>
  );
}
```

### 5. Error Handling and Validation

```tsx
function RobustSimplePayment() {
  const { createSimplePayment, isInitialized } = usePiSimplePayments();
  const [error, setError] = useState(null);

  const validatePayment = (options) => {
    if (!options.userId || options.userId.trim() === '') {
      return 'User ID is required';
    }
    if (!options.amount || options.amount <= 0) {
      return 'Amount must be greater than 0';
    }
    if (!options.itemName || options.itemName.trim() === '') {
      return 'Item name is required';
    }
    return null;
  };

  const handlePayment = async () => {
    setError(null);
    
    const paymentOptions = {
      userId: 'user123',
      amount: 3.0,
      itemName: 'Test Item'
    };

    // Validate before creating payment
    const validationError = validatePayment(paymentOptions);
    if (validationError) {
      setError(validationError);
      return;
    }

    try {
      const result = await createSimplePayment(
        paymentOptions,
        {
          onSuccess: (paymentId) => {
            console.log('Payment successful:', paymentId);
            setError(null);
          },
          onError: (error) => {
            console.error('Payment error:', error);
            setError(error);
          },
          onCancel: () => {
            console.log('Payment canceled');
            setError('Payment was canceled by user');
          }
        }
      );

      if (!result.success) {
        setError(result.message || 'Payment failed');
      }
    } catch (err) {
      console.error('Unexpected error:', err);
      setError('An unexpected error occurred');
    }
  };

  return (
    <div>
      <button onClick={handlePayment} disabled={!isInitialized}>
        Make Payment
      </button>
      
      {error && (
        <div style={{ color: 'red', marginTop: '10px' }}>
          Error: {error}
        </div>
      )}
    </div>
  );
}
```

## 🔧 Integration with Components

### Using with PaymentButton

```tsx
import { usePiSimplePayments, PaymentButton } from '@xhilo/pi-sdk';

function SimplePaymentButton() {
  const { createSimplePayment } = usePiSimplePayments();

  const handlePayment = async () => {
    return createSimplePayment({
      userId: 'user123',
      amount: 2.0,
      itemName: 'Quick Purchase'
    });
  };

  return (
    <PaymentButton
      onPayment={handlePayment}
      amount={2.0}
      itemName="Quick Purchase"
    >
      Buy Now
    </PaymentButton>
  );
}
```

## ⚠️ Important Notes

1. **No Backend Integration** - This hook doesn't call any backend APIs
2. **Client-Side Only** - All processing happens on the client side
3. **Simple Use Cases** - Best for simple, one-off payments
4. **No Progress Updates** - No real-time progress tracking
5. **Basic Error Handling** - Limited error handling compared to `usePiPayments`

## 🔗 Related Hooks

- [`usePiPayments`](./usePiPayments.md) - High-level payments with backend integration
- [`useXhiloPiNetwork`](./useXhiloPiNetwork.md) - Low-level Pi Network functionality
- [`PaymentButton`](./PaymentButton.md) - Pre-built payment button component
