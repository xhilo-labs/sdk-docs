# Backend SDK Documentation

This section contains all backend-specific documentation for the Pi SDK.

## 📖 Documentation

- [Backend SDK Guide](./README.md) - Complete backend SDK documentation

## 🔧 Backend Features

- **User-to-App (U2A) Payments**: Server-side payment approval and completion
- **App-to-User (A2U) Payments**: Payment creation and submission
- **Pi Platform Integration**: Direct integration with Pi Network APIs
- **Type Safety**: Full TypeScript support
- **Error Handling**: Comprehensive error management

## 🚀 Quick Start

1. **Install the backend SDK:**
   ```bash
   npm install @xhilo/pi-sdk/backend
   ```

2. **Configure environment variables:**
   ```bash
   PI_API_KEY=your_api_key
   PI_WALLET_PRIVATE_SEED=your_private_seed
   ```

3. **Import and use:**
   ```typescript
   import { approvePaymentAction, completePaymentAction } from '@xhilo/pi-sdk/backend';
   ```

## 📚 Related Documentation

- [Getting Started](../getting-started/) - Setup and configuration
- [API Reference](../api-reference/) - Complete API documentation
- [Examples](../examples/) - Usage examples
- [Troubleshooting](../troubleshooting/) - Common issues and solutions