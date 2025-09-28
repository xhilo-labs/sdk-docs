# Components

Pre-built React components for Pi Network integration. These components provide ready-to-use UI elements for common Pi Network operations.

## 📋 Available Components

- **[`PaymentButton`](./PaymentButton.md)** - Pre-built payment button with built-in payment logic
- **[`PaymentProcessDisplay`](./PaymentProcessDisplay.md)** - Display real-time payment progress
- **[`SimplePaymentButton`](./SimplePaymentButton.md)** - Simple payment button for basic use cases

## 🚀 Quick Start

```tsx
import { 
  PaymentButton, 
  PaymentProcessDisplay,
  SimplePaymentButton 
} from '@xhilo/pi-sdk';

function MyComponent() {
  return (
    <div>
      <PaymentButton
        onPayment={async () => {
          // Your payment logic
          return { success: true, data: 'payment123' };
        }}
        amount={5.0}
        itemName="Premium Feature"
      >
        Buy Premium
      </PaymentButton>
    </div>
  );
}
```

## 🎯 When to Use Components

### ✅ Use Components when:
- You want **quick implementation** of payment features
- You need **consistent UI** across your app
- You're **prototyping** or building **simple apps**
- You want **pre-styled** payment interfaces
- You need **ready-to-use** payment buttons

### ❌ Don't use Components when:
- You need **custom UI design** (build your own)
- You want **complex payment flows** (use hooks directly)
- You need **backend integration** (use `usePiPayments` with custom UI)
- You want **full control** over the payment process

## 📝 Component Examples

### 1. Basic Payment Interface

```tsx
import { 
  PaymentButton, 
  usePiPayments 
} from '@xhilo/pi-sdk';

function PaymentInterface() {
  const { createAndCompletePayment } = usePiPayments();
  const [processData, setProcessData] = useState(null);

  const handlePayment = async () => {
    return createAndCompletePayment({
      userId: 'user123',
      amount: 10.0,
      memo: 'Premium Subscription',
      onProcessUpdate: setProcessData
    });
  };

  return (
    <div className="payment-interface">
      <h2>Premium Subscription</h2>
      <p>Get access to all premium features for just 10 Pi</p>
      
      <PaymentButton
        onPayment={handlePayment}
        amount={10.0}
        itemName="Premium Subscription"
        variant="primary"
        size="large"
      >
        Subscribe Now
      </PaymentButton>
      
      {processData && (
        <div>Payment in progress...</div>
      )}
    </div>
  );
}
```

### 2. Product Catalog

```tsx
function ProductCatalog({ products }) {
  const { createSimplePayment } = usePiSimplePayments();

  return (
    <div className="product-catalog">
      <h2>Our Products</h2>
      <div className="products-grid">
        {products.map(product => (
          <div key={product.id} className="product-card">
            <img src={product.image} alt={product.name} />
            <h3>{product.name}</h3>
            <p>{product.description}</p>
            <p className="price">{product.price} Pi</p>
            
            <SimplePaymentButton
              onPayment={() => createSimplePayment({
                userId: 'user123',
                amount: product.price,
                itemName: product.name
              })}
              amount={product.price}
              itemName={product.name}
              variant="primary"
            >
              Buy Now
            </SimplePaymentButton>
          </div>
        ))}
      </div>
    </div>
  );
}
```

### 3. Subscription Plans

```tsx
function SubscriptionPlans() {
  const { createAndCompletePayment } = usePiPayments();
  const [processData, setProcessData] = useState(null);

  const plans = [
    { name: 'Basic', price: 5.0, features: ['Feature 1', 'Feature 2'] },
    { name: 'Pro', price: 15.0, features: ['All Basic', 'Feature 3', 'Feature 4'] },
    { name: 'Enterprise', price: 50.0, features: ['All Pro', 'Feature 5', 'Support'] }
  ];

  const handlePlanSelection = async (plan) => {
    return createAndCompletePayment({
      userId: 'user123',
      amount: plan.price,
      memo: `${plan.name} Plan`,
      onProcessUpdate: setProcessData
    });
  };

  return (
    <div className="subscription-plans">
      <h2>Choose Your Plan</h2>
      
      {processData && (
        <div>Payment in progress...</div>
      )}
      
      <div className="plans-grid">
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
            >
              Choose {plan.name}
            </PaymentButton>
          </div>
        ))}
      </div>
    </div>
  );
}
```

### 4. Custom Styled Components

```tsx
function CustomStyledPayment() {
  const { createSimplePayment } = usePiSimplePayments();

  return (
    <div className="custom-payment-section">
      <h2>Custom Styled Payment</h2>
      
      <PaymentButton
        onPayment={() => createSimplePayment({
          userId: 'user123',
          amount: 25.0,
          itemName: 'Custom Product'
        })}
        amount={25.0}
        itemName="Custom Product"
        className="custom-payment-button"
        style={{
          background: 'linear-gradient(45deg, #ff6b6b, #4ecdc4)',
          border: 'none',
          borderRadius: '25px',
          color: 'white',
          fontSize: '18px',
          fontWeight: 'bold',
          padding: '20px 40px',
          boxShadow: '0 4px 15px rgba(0,0,0,0.2)',
          transition: 'all 0.3s ease'
        }}
        showAmount={true}
        showItemName={true}
      >
        🚀 Buy Custom Product
      </PaymentButton>
    </div>
  );
}
```

### 5. Form Integration

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
      return { success: false, message: 'Please fill in all fields' };
    }

    return createSimplePayment({
      userId: formData.userId,
      amount: parseFloat(formData.amount),
      itemName: formData.itemName
    });
  };

  return (
    <form className="payment-form">
      <h2>Create Payment</h2>
      
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

### 6. Conditional Rendering

```tsx
function ConditionalPayment({ user, product }) {
  const { createSimplePayment } = usePiSimplePayments();
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
      onPayment={() => createSimplePayment({
        userId: user.id,
        amount: product.price,
        itemName: product.name
      })}
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

All components use consistent CSS classes for styling:

```css
/* Payment Button */
.payment-button {
  /* Base button styles */
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

/* Payment Process Display */
.payment-process-display {
  /* Process display container */
}

.payment-process-display--minimal {
  /* Minimal variant */
}

.payment-process-display--detailed {
  /* Detailed variant */
}

/* Simple Payment Button */
.simple-payment-button {
  /* Simple button styles */
}
```

### Custom Styling

```css
/* Custom payment button */
.custom-payment-button {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border: none;
  border-radius: 25px;
  color: white;
  font-weight: bold;
  padding: 15px 30px;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(0,0,0,0.2);
}

.custom-payment-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(0,0,0,0.3);
}

.custom-payment-button:active {
  transform: translateY(0);
}

/* Custom process display */
.custom-process-display {
  background: #f8f9fa;
  border: 1px solid #dee2e6;
  border-radius: 8px;
  padding: 20px;
  margin: 20px 0;
}

/* Product grid */
.products-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
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

.product-card img {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: 4px;
  margin-bottom: 15px;
}

.product-card .price {
  font-size: 24px;
  font-weight: bold;
  color: #007bff;
  margin: 15px 0;
}
```

## ⚠️ Important Notes

1. **Payment Function Required** - All payment components require an `onPayment` function
2. **Async Functions** - Payment functions must return a Promise
3. **Error Handling** - Handle errors in your payment functions
4. **Loading States** - Components handle loading states automatically
5. **Accessibility** - All components include proper ARIA labels

## 🔗 Related Documentation

- [PaymentButton](./PaymentButton.md) - Detailed payment button documentation
- [SimplePaymentButton](./SimplePaymentButton.md) - Simple button documentation
- [usePiPayments](./usePiPayments.md) - Advanced payment hook
- [usePiSimplePayments](./usePiSimplePayments.md) - Simple payment hook
