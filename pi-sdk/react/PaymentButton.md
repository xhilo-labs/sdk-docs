# PaymentButton Component

A pre-built React component that provides a ready-to-use payment button with built-in payment processing.

## 📋 Overview

`PaymentButton` is a complete payment solution that combines the payment hook functionality with a styled button component. It's perfect for quick implementation of payment features without building custom UI.

## 🚀 Basic Usage

```tsx
import { PaymentButton } from '@xhilo/pi-sdk';

function MyComponent() {
  return (
    <PaymentButton
      userId="user123"
      amount={5.0}
      memo="Premium Feature"
      onSuccess={(paymentId) => console.log('Payment successful:', paymentId)}
      onError={(error) => console.error('Payment failed:', error)}
    >
      Buy Premium
    </PaymentButton>
  );
}
```

## 🔧 Props

### `PaymentButtonProps`

```tsx
interface PaymentButtonProps {
  userId: string;                    // Required: User ID
  amount: number;                    // Required: Payment amount
  memo: string;                      // Required: Payment memo
  metadata?: Record<string, any>;    // Optional: Additional metadata
  className?: string;                // Optional: CSS class
  children?: React.ReactNode;        // Optional: Button content
  onSuccess?: (paymentId: string) => void;  // Optional: Success callback
  onError?: (error: string) => void;        // Optional: Error callback
  onCancel?: () => void;                    // Optional: Cancel callback
}
```

## 🎯 When to Use This Component

### ✅ Use `PaymentButton` when:
- You want a **quick implementation** of payment functionality
- You need a **pre-styled button** with payment logic
- You're building a **simple payment interface**
- You want **consistent UI** across your app
- You're **prototyping** payment features

### ❌ Don't use `PaymentButton` when:
- You need **custom UI design** (build your own)
- You want **complex payment flows** (use hooks directly)
- You need **backend integration** (use `usePiPayments` with custom UI)
- You want **full control** over the payment process

## 📝 Detailed Examples

### 1. Basic Payment Button

```tsx
import { PaymentButton } from '@xhilo/pi-sdk';

function BasicPayment() {
  return (
    <PaymentButton
      userId="user123"
      amount={2.5}
      memo="Digital Download"
      onSuccess={(paymentId) => console.log('Download purchased:', paymentId)}
    >
      Buy Download
    </PaymentButton>
  );
}
```

### 2. Multiple Payment Options

```tsx
function MultiplePayments() {
  const plans = [
    { name: 'Basic', price: 3.0, memo: 'Basic Plan' },
    { name: 'Premium', price: 5.0, memo: 'Premium Plan' },
    { name: 'Trial', price: 1.0, memo: 'Trial Plan' }
  ];

  return (
    <div>
      {plans.map(plan => (
        <PaymentButton
          key={plan.name}
          userId="user123"
          amount={plan.price}
          memo={plan.memo}
          className="mr-2 mb-2"
          onSuccess={(paymentId) => console.log(`${plan.name} purchased:`, paymentId)}
        >
          Get {plan.name}
        </PaymentButton>
      ))}
    </div>
  );
}
```

### 3. Custom Styling

```tsx
function CustomStyledButton() {
  return (
    <PaymentButton
      userId="user123"
      amount={10.0}
      memo="Custom Product"
      className="my-custom-payment-button"
      style={{
        background: 'linear-gradient(45deg, #ff6b6b, #4ecdc4)',
        border: 'none',
        borderRadius: '25px',
        color: 'white',
        fontSize: '16px',
        fontWeight: 'bold',
        padding: '15px 30px',
        boxShadow: '0 4px 15px rgba(0,0,0,0.2)',
        transition: 'all 0.3s ease'
      }}
      onSuccess={(paymentId) => console.log('Custom product purchased:', paymentId)}
    >
      🚀 Buy Custom Product
    </PaymentButton>
  );
}
```

### 4. E-commerce Product Card

```tsx
function ProductCard({ product }) {
  return (
    <div className="product-card">
      <img src={product.image} alt={product.name} />
      <h3>{product.name}</h3>
      <p>{product.description}</p>
      <p className="price">{product.price} Pi</p>
      
      <PaymentButton
        userId="user123"
        amount={product.price}
        memo={product.name}
        metadata={{
          productId: product.id,
          category: product.category
        }}
        onSuccess={(paymentId) => console.log('Purchase successful:', paymentId)}
        onError={(error) => console.error('Purchase failed:', error)}
      >
        Buy Now
      </PaymentButton>
    </div>
  );
}
```

### 5. Subscription Plans

```tsx
function SubscriptionPlans() {
  const plans = [
    { name: 'Basic', price: 5.0, features: ['Feature 1', 'Feature 2'] },
    { name: 'Pro', price: 15.0, features: ['All Basic', 'Feature 3', 'Feature 4'] },
    { name: 'Enterprise', price: 50.0, features: ['All Pro', 'Feature 5', 'Support'] }
  ];

  return (
    <div className="subscription-plans">
      {plans.map(plan => (
        <div key={plan.name} className="plan-card">
          <h3>{plan.name}</h3>
          <p className="price">{plan.price} Pi/month</p>
          <ul>
            {plan.features.map(feature => (
              <li key={feature}>{feature}</li>
            ))}
          </ul>
          
          <PaymentButton
            userId="user123"
            amount={plan.price}
            memo={`${plan.name} Plan`}
            metadata={{
              planType: plan.name.toLowerCase(),
              features: plan.features
            }}
            onSuccess={(paymentId) => console.log(`${plan.name} plan selected:`, paymentId)}
          >
            Choose {plan.name}
          </PaymentButton>
        </div>
      ))}
    </div>
  );
}
```

### 6. Form Integration

```tsx
function PaymentForm() {
  const [formData, setFormData] = useState({
    amount: '',
    memo: '',
    userId: ''
  });

  const isFormValid = formData.amount && formData.memo && formData.userId;

  return (
    <form className="payment-form">
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
        <label>Memo:</label>
        <input
          type="text"
          value={formData.memo}
          onChange={(e) => setFormData({...formData, memo: e.target.value})}
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
      
      <PaymentButton
        userId={formData.userId}
        amount={parseFloat(formData.amount) || 0}
        memo={formData.memo || 'Custom Item'}
        disabled={!isFormValid}
        onSuccess={(paymentId) => {
          console.log('Payment created:', paymentId);
          setFormData({ amount: '', memo: '', userId: '' });
        }}
        onError={(error) => console.error('Payment failed:', error)}
      >
        Create Payment
      </PaymentButton>
    </form>
  );
}
```

### 7. Conditional Rendering

```tsx
function ConditionalPayment({ user, product }) {
  const [isEligible, setIsEligible] = useState(false);

  useEffect(() => {
    // Check if user is eligible for this product
    checkEligibility(user.id, product.id).then(setIsEligible);
  }, [user.id, product.id]);

  if (!isEligible) {
    return (
      <button disabled className="disabled-button">
        Not Available
      </button>
    );
  }

  return (
    <PaymentButton
      userId={user.id}
      amount={product.price}
      memo={product.name}
      onSuccess={(paymentId) => console.log('Product purchased:', paymentId)}
    >
      Buy Now
    </PaymentButton>
  );
}
```

## 🎨 Styling

### CSS Classes

The component uses these CSS classes for styling:

```css
.payment-button {
  /* Main button container */
}

.payment-button--primary {
  /* Primary variant */
}

.payment-button--secondary {
  /* Secondary variant */
}

.payment-button--success {
  /* Success variant */
}

.payment-button--danger {
  /* Danger variant */
}

.payment-button--small {
  /* Small size */
}

.payment-button--medium {
  /* Medium size */
}

.payment-button--large {
  /* Large size */
}

.payment-button--loading {
  /* Loading state */
}

.payment-button--disabled {
  /* Disabled state */
}
```

### Custom Styling

```css
.my-custom-payment-button {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border: none;
  border-radius: 25px;
  color: white;
  font-weight: bold;
  padding: 15px 30px;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(0,0,0,0.2);
}

.my-custom-payment-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(0,0,0,0.3);
}

.my-custom-payment-button:active {
  transform: translateY(0);
}
```

## ⚠️ Important Notes

1. **Payment Function Required** - You must provide an `onPayment` function
2. **Async Function** - The payment function must return a Promise
3. **Error Handling** - Handle errors in your payment function
4. **Loading States** - Use the `loading` prop for better UX
5. **Accessibility** - The component includes proper ARIA labels

## 🔗 Related Components

- [`usePiSimplePayments`](./usePiSimplePayments.md) - Hook for simple payments
- [`usePiPayments`](./usePiPayments.md) - Hook for advanced payments
- [`PaymentProcessDisplay`](./PaymentProcessDisplay.md) - Display payment progress
