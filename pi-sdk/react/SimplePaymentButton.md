# SimplePaymentButton Component

A simplified payment button component for basic payment operations without complex backend integration.

## 📋 Overview

`SimplePaymentButton` is a lightweight payment button designed for simple use cases where you don't need backend integration or complex payment flows. It's perfect for prototyping, simple apps, or basic payment functionality.

## 🚀 Basic Usage

```tsx
import { SimplePaymentButton, usePiSimplePayments } from '@xhilo/pi-sdk';

function SimplePayment() {
  const { createSimplePayment } = usePiSimplePayments();

  return (
    <SimplePaymentButton
      onPayment={() => createSimplePayment({
        userId: 'user123',
        amount: 2.0,
        itemName: 'Simple Purchase'
      })}
      amount={2.0}
      itemName="Simple Purchase"
    >
      Buy Now
    </SimplePaymentButton>
  );
}
```

## 🔧 Props

### `SimplePaymentButtonProps`

```tsx
interface SimplePaymentButtonProps {
  onPayment: () => Promise<PiOperationResult<string>>; // Required: Payment function
  amount: number;                                      // Required: Payment amount
  itemName: string;                                    // Required: Item name
  children: React.ReactNode;                           // Required: Button content
  disabled?: boolean;                                  // Optional: Disable button
  loading?: boolean;                                   // Optional: Show loading state
  variant?: 'primary' | 'secondary' | 'success' | 'danger'; // Optional: Button variant
  size?: 'small' | 'medium' | 'large';                // Optional: Button size
  className?: string;                                  // Optional: CSS class
  style?: React.CSSProperties;                        // Optional: Inline styles
  showAmount?: boolean;                                // Optional: Show amount in button (default: true)
  showItemName?: boolean;                              // Optional: Show item name in button (default: false)
}
```

## 🎯 When to Use This Component

### ✅ Use `SimplePaymentButton` when:
- You want **simple payments** without backend integration
- You're **prototyping** or building a **simple app**
- You don't need **real-time progress updates**
- You want **minimal setup** for basic payment functionality
- You're building a **client-side only** application

### ❌ Don't use `SimplePaymentButton` when:
- You need **User-to-App payments with backend** (use `PaymentButton` with `usePiPayments`)
- You need **server-side validation** or processing
- You want **real-time progress updates** (use `PaymentButton` with `usePiPayments`)
- You're building **production e-commerce** (use `PaymentButton` with `usePiPayments`)

## 📝 Detailed Examples

### 1. Basic Simple Payment

```tsx
import { SimplePaymentButton, usePiSimplePayments } from '@xhilo/pi-sdk';

function BasicSimplePayment() {
  const { createSimplePayment } = usePiSimplePayments();

  const handlePayment = async () => {
    return createSimplePayment({
      userId: 'user123',
      amount: 1.5,
      itemName: 'Digital Download'
    });
  };

  return (
    <SimplePaymentButton
      onPayment={handlePayment}
      amount={1.5}
      itemName="Digital Download"
    >
      Download Now
    </SimplePaymentButton>
  );
}
```

### 2. Different Variants and Sizes

```tsx
function VariantExamples() {
  const { createSimplePayment } = usePiSimplePayments();

  const handlePayment = async () => {
    return createSimplePayment({
      userId: 'user123',
      amount: 3.0,
      itemName: 'Test Product'
    });
  };

  return (
    <div>
      {/* Primary variant */}
      <SimplePaymentButton
        onPayment={handlePayment}
        amount={3.0}
        itemName="Test Product"
        variant="primary"
        size="large"
      >
        Buy Primary
      </SimplePaymentButton>

      {/* Secondary variant */}
      <SimplePaymentButton
        onPayment={handlePayment}
        amount={2.0}
        itemName="Test Product"
        variant="secondary"
        size="medium"
      >
        Buy Secondary
      </SimplePaymentButton>

      {/* Success variant */}
      <SimplePaymentButton
        onPayment={handlePayment}
        amount={1.0}
        itemName="Test Product"
        variant="success"
        size="small"
      >
        Buy Success
      </SimplePaymentButton>
    </div>
  );
}
```

### 3. Custom Styling

```tsx
function CustomStyledSimpleButton() {
  const { createSimplePayment } = usePiSimplePayments();

  const handlePayment = async () => {
    return createSimplePayment({
      userId: 'user123',
      amount: 5.0,
      itemName: 'Custom Product'
    });
  };

  return (
    <SimplePaymentButton
      onPayment={handlePayment}
      amount={5.0}
      itemName="Custom Product"
      className="custom-simple-button"
      style={{
        background: 'linear-gradient(45deg, #ff6b6b, #4ecdc4)',
        border: 'none',
        borderRadius: '20px',
        color: 'white',
        fontSize: '16px',
        fontWeight: 'bold',
        padding: '12px 24px',
        boxShadow: '0 3px 10px rgba(0,0,0,0.2)',
        transition: 'all 0.3s ease'
      }}
      showAmount={true}
      showItemName={true}
    >
      🎯 Buy Custom Product
    </SimplePaymentButton>
  );
}
```

### 4. Multiple Product Options

```tsx
function MultipleProducts() {
  const { createSimplePayment } = usePiSimplePayments();

  const products = [
    { id: 1, name: 'Basic Plan', price: 1.0, description: 'Basic features' },
    { id: 2, name: 'Pro Plan', price: 3.0, description: 'Pro features' },
    { id: 3, name: 'Premium Plan', price: 5.0, description: 'Premium features' }
  ];

  const handleProductPurchase = async (product) => {
    return createSimplePayment({
      userId: 'user123',
      amount: product.price,
      itemName: product.name,
      customMetadata: {
        productId: product.id,
        description: product.description
      }
    });
  };

  return (
    <div className="products-grid">
      {products.map(product => (
        <div key={product.id} className="product-card">
          <h3>{product.name}</h3>
          <p>{product.description}</p>
          <p className="price">{product.price} Pi</p>
          
          <SimplePaymentButton
            onPayment={() => handleProductPurchase(product)}
            amount={product.price}
            itemName={product.name}
            variant="primary"
            size="medium"
            showAmount={true}
          >
            Buy {product.name}
          </SimplePaymentButton>
        </div>
      ))}
    </div>
  );
}
```

### 5. Form Integration

```tsx
function SimplePaymentForm() {
  const { createSimplePayment } = usePiSimplePayments();
  const [formData, setFormData] = useState({
    amount: '',
    itemName: '',
    userId: ''
  });

  const handleFormPayment = async () => {
    if (!formData.amount || !formData.itemName || !formData.userId) {
      return { success: false, message: 'Please fill in all fields' };
    }

    return createSimplePayment({
      userId: formData.userId,
      amount: parseFloat(formData.amount),
      itemName: formData.itemName
    });
  };

  return (
    <form className="simple-payment-form">
      <h2>Create Simple Payment</h2>
      
      <div className="form-group">
        <label>User ID:</label>
        <input
          type="text"
          value={formData.userId}
          onChange={(e) => setFormData({...formData, userId: e.target.value})}
          required
        />
      </div>
      
      <div className="form-group">
        <label>Item Name:</label>
        <input
          type="text"
          value={formData.itemName}
          onChange={(e) => setFormData({...formData, itemName: e.target.value})}
          required
        />
      </div>
      
      <div className="form-group">
        <label>Amount (Pi):</label>
        <input
          type="number"
          step="0.1"
          value={formData.amount}
          onChange={(e) => setFormData({...formData, amount: e.target.value})}
          required
        />
      </div>
      
      <SimplePaymentButton
        onPayment={handleFormPayment}
        amount={parseFloat(formData.amount) || 0}
        itemName={formData.itemName || 'Custom Item'}
        variant="primary"
        size="large"
        showAmount={true}
        showItemName={true}
      >
        Create Simple Payment
      </SimplePaymentButton>
    </form>
  );
}
```

### 6. Conditional Rendering

```tsx
function ConditionalSimplePayment({ user, product }) {
  const { createSimplePayment } = usePiSimplePayments();
  const [isEligible, setIsEligible] = useState(false);

  useEffect(() => {
    // Check if user is eligible for this product
    checkEligibility(user.id, product.id).then(setIsEligible);
  }, [user.id, product.id]);

  const handlePayment = async () => {
    return createSimplePayment({
      userId: user.id,
      amount: product.price,
      itemName: product.name
    });
  };

  if (!isEligible) {
    return (
      <button disabled className="disabled-button">
        Not Available
      </button>
    );
  }

  return (
    <SimplePaymentButton
      onPayment={handlePayment}
      amount={product.price}
      itemName={product.name}
      variant="primary"
      showAmount={true}
    >
      Buy Now
    </SimplePaymentButton>
  );
}
```

### 7. Error Handling

```tsx
function SimplePaymentWithErrorHandling() {
  const { createSimplePayment } = usePiSimplePayments();
  const [error, setError] = useState(null);

  const handlePayment = async () => {
    setError(null);
    
    try {
      const result = await createSimplePayment({
        userId: 'user123',
        amount: 2.0,
        itemName: 'Test Product'
      });
      
      if (!result.success) {
        setError(result.message);
        return result;
      }
      
      return result;
    } catch (err) {
      setError(err.message);
      return { success: false, message: err.message };
    }
  };

  return (
    <div>
      <SimplePaymentButton
        onPayment={handlePayment}
        amount={2.0}
        itemName="Test Product"
        variant="primary"
      >
        Buy Test Product
      </SimplePaymentButton>
      
      {error && (
        <div className="error-message">
          Error: {error}
        </div>
      )}
    </div>
  );
}
```

## 🎨 Styling

### CSS Classes

The component uses these CSS classes for styling:

```css
.simple-payment-button {
  /* Base button styles */
}

.simple-payment-button--primary {
  /* Primary variant */
}

.simple-payment-button--secondary {
  /* Secondary variant */
}

.simple-payment-button--success {
  /* Success variant */
}

.simple-payment-button--danger {
  /* Danger variant */
}

.simple-payment-button--small {
  /* Small size */
}

.simple-payment-button--medium {
  /* Medium size */
}

.simple-payment-button--large {
  /* Large size */
}

.simple-payment-button--loading {
  /* Loading state */
}

.simple-payment-button--disabled {
  /* Disabled state */
}
```

### Custom Styling

```css
.custom-simple-button {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border: none;
  border-radius: 20px;
  color: white;
  font-weight: bold;
  padding: 12px 24px;
  transition: all 0.3s ease;
  box-shadow: 0 3px 10px rgba(0,0,0,0.2);
}

.custom-simple-button:hover {
  transform: translateY(-1px);
  box-shadow: 0 5px 15px rgba(0,0,0,0.3);
}

.custom-simple-button:active {
  transform: translateY(0);
}

.products-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
  margin: 20px 0;
}

.product-card {
  border: 1px solid #dee2e6;
  border-radius: 8px;
  padding: 20px;
  text-align: center;
  background: white;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.product-card .price {
  font-size: 20px;
  font-weight: bold;
  color: #007bff;
  margin: 15px 0;
}

.error-message {
  color: #dc3545;
  background: #f8d7da;
  border: 1px solid #f5c6cb;
  border-radius: 4px;
  padding: 10px;
  margin-top: 10px;
}
```

## ⚠️ Important Notes

1. **Payment Function Required** - You must provide an `onPayment` function
2. **Async Function** - The payment function must return a Promise
3. **Error Handling** - Handle errors in your payment function
4. **Loading States** - Use the `loading` prop for better UX
5. **Accessibility** - The component includes proper ARIA labels
6. **Simple Use Cases** - Best for basic, one-off payments

## 🔗 Related Components

- [`PaymentButton`](./PaymentButton.md) - Advanced payment button with backend integration
- [`PaymentProcessDisplay`](./PaymentProcessDisplay.md) - Display payment progress
- [`usePiSimplePayments`](./usePiSimplePayments.md) - Hook for simple payments
