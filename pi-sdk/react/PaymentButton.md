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
      onPayment={async () => {
        // Your payment logic here
        return { success: true, data: 'payment123' };
      }}
      amount={5.0}
      itemName="Premium Feature"
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
import { PaymentButton, usePiSimplePayments } from '@xhilo/pi-sdk';

function BasicPayment() {
  const { createSimplePayment } = usePiSimplePayments();

  const handlePayment = async () => {
    return createSimplePayment({
      userId: 'user123',
      amount: 2.5,
      itemName: 'Digital Download'
    });
  };

  return (
    <PaymentButton
      onPayment={handlePayment}
      amount={2.5}
      itemName="Digital Download"
    >
      Buy Download
    </PaymentButton>
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
      amount: 5.0,
      itemName: 'Premium Plan'
    });
  };

  return (
    <div>
      {/* Primary variant */}
      <PaymentButton
        onPayment={handlePayment}
        amount={5.0}
        itemName="Premium Plan"
        variant="primary"
        size="large"
      >
        Get Premium
      </PaymentButton>

      {/* Secondary variant */}
      <PaymentButton
        onPayment={handlePayment}
        amount={3.0}
        itemName="Basic Plan"
        variant="secondary"
        size="medium"
      >
        Get Basic
      </PaymentButton>

      {/* Success variant */}
      <PaymentButton
        onPayment={handlePayment}
        amount={1.0}
        itemName="Trial"
        variant="success"
        size="small"
      >
        Start Trial
      </PaymentButton>
    </div>
  );
}
```

### 3. Custom Styling

```tsx
function CustomStyledButton() {
  const { createSimplePayment } = usePiSimplePayments();

  const handlePayment = async () => {
    return createSimplePayment({
      userId: 'user123',
      amount: 10.0,
      itemName: 'Custom Product'
    });
  };

  return (
    <PaymentButton
      onPayment={handlePayment}
      amount={10.0}
      itemName="Custom Product"
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
      showAmount={true}
      showItemName={true}
    >
      🚀 Buy Custom Product
    </PaymentButton>
  );
}
```

### 4. E-commerce Product Card

```tsx
function ProductCard({ product }) {
  const { createSimplePayment } = usePiSimplePayments();
  const [isProcessing, setIsProcessing] = useState(false);

  const handlePurchase = async () => {
    setIsProcessing(true);
    try {
      const result = await createSimplePayment({
        userId: 'user123',
        amount: product.price,
        itemName: product.name,
        customMetadata: {
          productId: product.id,
          category: product.category
        }
      });
      
      if (result.success) {
        // Handle success
        console.log('Purchase successful:', result.data);
      }
    } catch (error) {
      console.error('Purchase failed:', error);
    } finally {
      setIsProcessing(false);
    }
  };

  return (
    <div className="product-card">
      <img src={product.image} alt={product.name} />
      <h3>{product.name}</h3>
      <p>{product.description}</p>
      <p className="price">{product.price} Pi</p>
      
      <PaymentButton
        onPayment={handlePurchase}
        amount={product.price}
        itemName={product.name}
        loading={isProcessing}
        variant="primary"
        size="large"
        showAmount={true}
      >
        {isProcessing ? 'Processing...' : 'Add to Cart'}
      </PaymentButton>
    </div>
  );
}
```

### 5. Subscription Plans

```tsx
function SubscriptionPlans() {
  const { createSimplePayment } = usePiSimplePayments();

  const plans = [
    { name: 'Basic', price: 5.0, features: ['Feature 1', 'Feature 2'] },
    { name: 'Pro', price: 15.0, features: ['All Basic', 'Feature 3', 'Feature 4'] },
    { name: 'Enterprise', price: 50.0, features: ['All Pro', 'Feature 5', 'Support'] }
  ];

  const handlePlanSelection = async (plan) => {
    return createSimplePayment({
      userId: 'user123',
      amount: plan.price,
      itemName: `${plan.name} Plan`,
      customMetadata: {
        planType: plan.name.toLowerCase(),
        features: plan.features
      }
    });
  };

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
            onPayment={() => handlePlanSelection(plan)}
            amount={plan.price}
            itemName={`${plan.name} Plan`}
            variant={plan.name === 'Pro' ? 'primary' : 'secondary'}
            size="large"
            showAmount={true}
            showItemName={true}
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
  const { createSimplePayment } = usePiSimplePayments();
  const [formData, setFormData] = useState({
    amount: '',
    itemName: '',
    userId: ''
  });

  const handleFormPayment = async () => {
    if (!formData.amount || !formData.itemName || !formData.userId) {
      alert('Please fill in all fields');
      return { success: false, message: 'Missing required fields' };
    }

    return createSimplePayment({
      userId: formData.userId,
      amount: parseFloat(formData.amount),
      itemName: formData.itemName
    });
  };

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
      
      <PaymentButton
        onPayment={handleFormPayment}
        amount={parseFloat(formData.amount) || 0}
        itemName={formData.itemName || 'Custom Item'}
        variant="primary"
        size="large"
        showAmount={true}
        showItemName={true}
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
    <PaymentButton
      onPayment={handlePayment}
      amount={product.price}
      itemName={product.name}
      variant="primary"
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
