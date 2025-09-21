# Pi React SDK Documentation

Comprehensive documentation for the `@xhilo/pi-react-sdk` package, covering all hooks, components, and usage patterns.

## 📚 Documentation Index

### Getting Started
- **[Developer Flow](./dev-flow.md)** - Complete implementation guide for payments and ads

### Core Hooks
- **[useXhiloPiNetwork](./useXhiloPiNetwork.md)** - Main hook for Pi Network functionality
- **[usePiPayments](./usePiPayments.md)** - High-level payment processing with backend integration
- **[usePiSimplePayments](./usePiSimplePayments.md)** - Simplified payment creation

### Ads Hooks
- **[usePiAds](./usePiAds.md)** - Full ads functionality with backend verification
- **[usePiAdsSimple](./usePiAdsSimple.md)** - Frontend-only ads display

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

## 🚀 Quick Start

### Installation

```bash
npm install @xhilo/pi-sdk@0.1.0
```

### Basic Setup

```tsx
import { XhiloPiProvider, useXhiloPiNetwork } from '@xhilo/pi-sdk';

function App() {
  return (
    <XhiloPiProvider>
      <MyComponent />
    </XhiloPiProvider>
  );
}

function MyComponent() {
  const { createPayment, isInitialized, user } = useXhiloPiNetwork();
  
  // Your component logic here
}
```

## 🎯 Key Features

### **React 19 Compatible**
- ✅ Supports React 16, 17, 18, and 19
- ✅ Backward compatible with existing projects
- ✅ Future-ready for React 19 features

### **Comprehensive Payment Support**
- **User-to-App (U2A) Payments**: Complete payment lifecycle with backend integration
- **App-to-User (A2U) Payments**: Server-side payment processing
- **Access Token Validation**: Enhanced security for U2A payments

### **Advanced Ads Integration**
- **Full Backend Integration**: Complete ads verification and reward processing
- **Frontend-Only Option**: Simple ads display without backend requirements
- **Customizable Logic**: Developer-configurable eligibility and reward functions

### **Developer Experience**
- **TypeScript Support**: Full type safety and IntelliSense
- **Comprehensive Logging**: Debug-friendly console output
- **Error Handling**: Standardized error responses across all hooks
- **Flexible Architecture**: Mix and match hooks based on your needs

## 📖 Usage Patterns

### **Simple Payment Flow**
```tsx
import { usePiSimplePayments } from '@xhilo/pi-sdk';

function PaymentComponent() {
  const { createPayment } = usePiSimplePayments();
  
  const handlePayment = async () => {
    const result = await createPayment({
      amount: 1.0,
      memo: 'Test payment'
    });
    
    if (result.success) {
      console.log('Payment created:', result.data);
    }
  };
  
  return <button onClick={handlePayment}>Pay 1 Pi</button>;
}
```

### **Advanced Payment with Backend**
```tsx
import { usePiPayments } from '@xhilo/pi-sdk';

function AdvancedPaymentComponent() {
  const { createPayment } = usePiPayments({
    baseUrl: 'https://your-api.com',
    onSuccess: (payment) => console.log('Payment successful:', payment),
    onError: (error) => console.error('Payment failed:', error)
  });
  
  // Payment logic here
}
```

### **Ads Integration**
```tsx
import { usePiAds } from '@xhilo/pi-sdk';

function AdsComponent() {
  const { showAd, verifyRewardedAd } = usePiAds({
    baseUrl: 'https://your-api.com'
  });
  
  const handleShowAd = async () => {
    const result = await showAd('rewarded');
    if (result.success) {
      console.log('Ad shown successfully');
    }
  };
  
  return <button onClick={handleShowAd}>Watch Ad</button>;
}
```

## 🔧 Configuration

### **Environment Variables**
```bash
# For U2A payments
PI_APP_SECRET=your_pi_app_secret_key

# For A2U payments  
PI_API_KEY=your_pi_api_key
PI_WALLET_PRIVATE_SEED=your_wallet_private_seed
```

### **Provider Configuration**
```tsx
<XhiloPiProvider
  sandbox={false} // Set to true for testing
  onReady={() => console.log('SDK ready')}
  onError={(error) => console.error('SDK error:', error)}
>
  <App />
</XhiloPiProvider>
```

## 🆕 What's New in v0.1.0

- ✅ **React 19 Support**: Full compatibility with React 19
- ✅ **Enhanced Ads System**: Complete backend integration for ads
- ✅ **Access Token Validation**: Improved security for U2A payments
- ✅ **New Hooks**: `usePiAds`, `usePiAdsSimple` for ads functionality
- ✅ **Better Error Handling**: Standardized error responses
- ✅ **TypeScript Improvements**: Enhanced type definitions

## 🔗 Related Documentation

- **[Backend SDK](../backend/)** - Server-side payment processing
- **[Templates](../templates/)** - Next.js starter templates
- **[Examples](../examples/)** - Real-world usage examples
- **[API Reference](../api-reference/)** - Complete API documentation

---

*For more detailed information, explore the individual hook and component documentation above.*