# Pi React SDK Documentation

Comprehensive documentation for the `@xhilo/pi-react-sdk` package, covering all hooks, components, and usage patterns.

## 📚 Documentation Index

### Getting Started
- **[Developer Flow](./dev-flow.md)** - Complete implementation guide for payments and ads

### Core Hooks
- **[useXhiloPiNetwork](./useXhiloPiNetwork.md)** - Main hook for Pi Network functionality
- **[usePiPayments](./usePiPayments.md)** - High-level payment processing with backend integration
- **[usePiSimplePayments](./usePiSimplePayments.md)** - Simplified payment creation

### Provider Hooks
- **[useXhiloPi](./useXhiloPi.md)** - Full Pi Network context access
- **[useXhiloPiUser](./useXhiloPiUser.md)** - User information and logout
- **[useXhiloPiReady](./useXhiloPiReady.md)** - SDK initialization status
- **[useXhiloPiLogs](./useXhiloPiLogs.md)** - Console logs for debugging

### Components
- **[PaymentButton](./PaymentButton.md)** - Pre-built payment button with backend integration
- **[PaymentProcessDisplay](./PaymentProcessDisplay.md)** - Display real-time payment progress
- **[SimplePaymentButton](./SimplePaymentButton.md)** - Simple payment button for basic use cases
- **[Components Overview](./components.md)** - All components and usage patterns

### Provider Hooks
- **[Provider Hooks Overview](./provider-hooks.md)** - Context-based hooks for complex component hierarchies

### Security
- **[Security Guide](./security.md)** - Access token validation and security best practices

## 🚀 Quick Start Guide

### 1. Installation

```bash
npm install @xhilo/pi-react-sdk
```

### 2. Basic Setup

```tsx
import { XhiloPiProvider } from '@xhilo/pi-react-sdk';

function App() {
  return (
    <XhiloPiProvider>
      <YourApp />
    </XhiloPiProvider>
  );
}
```

### 3. Choose Your Hook

#### For Simple Payments (No Backend)
```tsx
import { usePiSimplePayments } from '@xhilo/pi-react-sdk';

function SimplePayment() {
  const { createSimplePayment } = usePiSimplePayments();
  
  const handlePayment = async () => {
    return createSimplePayment({
      userId: 'user123',
      amount: 2.0,
      itemName: 'Premium Feature'
    });
  };

  return <button onClick={handlePayment}>Buy Premium</button>;
}
```

#### For Advanced Payments (With Backend)
```tsx
import { usePiPayments, PaymentProcessDisplay } from '@xhilo/pi-react-sdk';

function AdvancedPayment() {
  const { createAndCompletePayment } = usePiPayments();
  const [processData, setProcessData] = useState(null);

  const handlePayment = async () => {
    return createAndCompletePayment({
      userId: 'user123',
      amount: 5.0,
      memo: 'Premium Subscription',
      apiConfig: {
        baseUrl: 'https://myapi.com',
        approveEndpoint: '/api/payments/approve',
        completeEndpoint: '/api/payments/complete'
      },
      onProcessUpdate: setProcessData
    });
  };

  return (
    <div>
      <button onClick={handlePayment}>Subscribe</button>
      {processData && <PaymentProcessDisplay processData={processData} />}
    </div>
  );
}
```

#### For Full Control
```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-react-sdk';

function FullControl() {
  const { 
    initialize, 
    authenticate, 
    createPayment, 
    user, 
    isInitialized 
  } = useXhiloPiNetwork();

  // Your custom implementation
}
```

## 🎯 Hook Selection Guide

### When to Use Which Hook

| Hook | Use Case | Backend Required | Progress Updates | Complexity |
|------|----------|------------------|------------------|------------|
| `useXhiloPiNetwork` | Full control, custom flows | Optional | No | High |
| `usePiPayments` | U2A payments with backend | Yes | Yes | Medium |
| `usePiSimplePayments` | Simple payments, prototyping | No | No | Low |
| `useXhiloPi` | Context access in nested components | Optional | No | Medium |
| `useXhiloPiUser` | User management only | No | No | Low |
| `useXhiloPiReady` | SDK initialization status | No | No | Low |
| `useXhiloPiLogs` | Debugging and monitoring | No | No | Low |

### Component Selection

| Component | Use Case | Backend Required | Progress Display | Complexity |
|-----------|----------|------------------|------------------|------------|
| `PaymentButton` | Quick implementation with backend | Yes | No | Medium |
| `PaymentProcessDisplay` | Show payment progress | Yes | Yes | Low |
| `SimplePaymentButton` | Simple payments, prototyping | No | No | Low |

## 📖 Detailed Examples

### E-commerce Integration

```tsx
import { 
  usePiPayments, 
  PaymentProcessDisplay, 
  useXhiloPiUser 
} from '@xhilo/pi-react-sdk';

function EcommerceApp() {
  const { user } = useXhiloPiUser();
  const { createAndCompletePayment } = usePiPayments();
  const [processData, setProcessData] = useState(null);

  const handlePurchase = async (product) => {
    return createAndCompletePayment({
      userId: user.uid,
      amount: product.price,
      memo: `Purchase: ${product.name}`,
      apiConfig: {
        baseUrl: process.env.REACT_APP_API_URL,
        approveEndpoint: '/orders/approve-payment',
        completeEndpoint: '/orders/complete-payment'
      },
      onProcessUpdate: setProcessData,
      onSuccess: (paymentId) => {
        // Redirect to success page
        window.location.href = `/order-success?paymentId=${paymentId}`;
      }
    });
  };

  return (
    <div>
      <ProductCatalog onPurchase={handlePurchase} />
      {processData && <PaymentProcessDisplay processData={processData} />}
    </div>
  );
}
```

### Subscription Management

```tsx
import { usePiPayments } from '@xhilo/pi-react-sdk';

function SubscriptionManager() {
  const { createAndCompletePayment } = usePiPayments();

  const handleRenewal = async (subscription) => {
    return createAndCompletePayment({
      userId: subscription.userId,
      amount: subscription.amount,
      memo: `Renewal: ${subscription.planName}`,
      onProcessUpdate: (data) => {
        // Update subscription status based on progress
        updateSubscriptionStatus(subscription.id, data.stage);
      }
    });
  };

  return (
    <div>
      <SubscriptionPlans onRenewal={handleRenewal} />
    </div>
  );
}
```

### Debug and Monitoring

```tsx
import { useXhiloPiLogs, useXhiloPiReady } from '@xhilo/pi-react-sdk';

function DebugPanel() {
  const { logs, clearLogs } = useXhiloPiLogs();
  const { isReady, isInitialized } = useXhiloPiReady();

  return (
    <div className="debug-panel">
      <h3>Pi SDK Debug Information</h3>
      
      <div className="status">
        <p>Initialized: {isInitialized ? 'Yes' : 'No'}</p>
        <p>Ready: {isReady ? 'Yes' : 'No'}</p>
        <p>Logs: {logs.length}</p>
      </div>
      
      <button onClick={clearLogs}>Clear Logs</button>
      
      <div className="logs">
        {logs.map((log, index) => (
          <div key={index} className={`log log-${log.type}`}>
            <span>{log.timestamp}</span>
            <span>{log.type}</span>
            <span>{log.message}</span>
          </div>
        ))}
      </div>
    </div>
  );
}
```

## 🔧 Configuration

### Environment Variables

```bash
# For U2A payments (User-to-App) - Frontend automatically sends access tokens
PI_APP_SECRET=your_pi_app_secret_key

# For A2U payments (App-to-User) - Backend only
PI_API_KEY=your_pi_api_key
PI_WALLET_PRIVATE_SEED=your_wallet_private_seed
```

### API Configuration

```tsx
// For usePiPayments hook
const apiConfig = {
  baseUrl: 'https://myapi.com',
  approveEndpoint: '/api/payments/approve',
  completeEndpoint: '/api/payments/complete'
};
```

### Sandbox Mode

```tsx
// Enable sandbox mode for testing
const { initialize } = useXhiloPiNetwork(true);
```

## 🎨 Styling

### CSS Classes

All components use consistent CSS classes:

```css
/* Payment Buttons */
.payment-button { /* Base styles */ }
.payment-button--primary { /* Primary variant */ }
.payment-button--secondary { /* Secondary variant */ }

/* Process Display */
.payment-process-display { /* Base styles */ }
.payment-process-display--minimal { /* Minimal variant */ }
.payment-process-display--detailed { /* Detailed variant */ }

/* Simple Payment Button */
.simple-payment-button { /* Base styles */ }
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
```

## ⚠️ Important Notes

1. **Provider Required** - Wrap your app with `XhiloPiProvider`
2. **Error Handling** - Always implement proper error handling
3. **Loading States** - Use loading states for better UX
4. **Testing** - Test with sandbox mode before production
5. **Security** - Never expose API keys in client-side code

## 🔗 Related Resources

- [Main README](../README.md) - Package overview and installation
- [GitHub Repository](https://github.com/xhilo-labs/pi-sdk) - Source code and issues
- [Pi Network Documentation](https://developers.minepi.com/) - Official Pi Network docs
- [Support](mailto:support@xhilo.com) - Get help and support

## 📝 Contributing

Contributions are welcome! Please read our contributing guidelines and submit pull requests.

## 📄 License

MIT License - see LICENSE file for details.
