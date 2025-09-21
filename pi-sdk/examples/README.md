# Examples

This section contains real-world examples and use cases for the Pi SDK.

## 📖 Documentation

- [Examples Guide](./examples.md) - Comprehensive examples and use cases

## 🎯 Example Categories

### Basic Integration
- Simple payment processing
- User authentication
- Basic component usage

### Advanced Features
- Custom payment flows
- Backend integration
- Error handling patterns

### Real-world Scenarios
- E-commerce integration
- Subscription payments
- Reward systems

## 🚀 Quick Examples

### React Integration
```tsx
import { XhiloPiProvider, usePiPayments } from '@xhilo/pi-sdk/react';

function App() {
  return (
    <XhiloPiProvider sandbox={false}>
      <PaymentComponent />
    </XhiloPiProvider>
  );
}
```

### Backend Integration
```typescript
import { approvePaymentAction } from '@xhilo/pi-sdk/backend';

// Approve a payment
const result = await approvePaymentAction({
  paymentId: 'payment_123',
  userId: 'user_456'
});
```

## 📚 Related Documentation

- [Getting Started](../getting-started/) - Setup and configuration
- [API Reference](../api-reference/) - Complete API documentation
- [React SDK](../react/) - React-specific documentation
- [Backend SDK](../backend/) - Backend integration guide
