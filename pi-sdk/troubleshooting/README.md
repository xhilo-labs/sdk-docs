# Troubleshooting

This section contains common issues, solutions, and debugging tips for the Pi SDK.

## 📖 Documentation

- [Troubleshooting Guide](./troubleshooting.md) - Complete troubleshooting documentation

## 🔧 Common Issues

### Installation Issues
- Package installation problems
- Dependency conflicts
- TypeScript configuration

### Runtime Issues
- SDK initialization failures
- Payment processing errors
- Authentication problems

### Development Issues
- Environment configuration
- API key setup
- Sandbox vs production

## 🚀 Quick Solutions

### SDK Not Initializing
```typescript
// Check if running in Pi Browser
if (typeof window.Pi === 'undefined') {
  console.error('Pi SDK not available. Please run in Pi Browser.');
}
```

### Payment Errors
```typescript
// Check payment status
const result = await createPayment(paymentData, {
  onError: (error) => {
    console.error('Payment error:', error);
    // Handle error appropriately
  }
});
```

## 📚 Related Documentation

- [Getting Started](../getting-started/) - Setup and configuration
- [API Reference](../api-reference/) - Complete API documentation
- [Examples](../examples/) - Usage examples
- [React SDK](../react/) - React-specific documentation
