# Pi SDK Documentation

This is the documentation site for the Pi SDK - a unified SDK for Pi Network integration with React frontend and Node.js backend support.

## 🏗️ Architecture

The Pi SDK uses a **configuration-driven architecture** for all operations:

- **Configuration-Driven**: All functions require explicit configuration parameters (no environment variable dependencies)
- **User-to-App (U2A) Payments**: Uses `PiPlatformConfig` with `apiKey` only
- **App-to-User (A2U) Payments**: Uses `PiBackendConfig` with `apiKey` and `privateSeed`
- **Consistent Error Handling**: All actions return standardized `PiOperationResult` types
- **Type Safety**: Full TypeScript support with comprehensive type definitions
- **Security**: Access token validation and user identity verification

## 🎯 Features

### **React Hooks**
- **`useXhiloPiNetwork`** - Core Pi Network integration with enhanced state management
- **`usePiPayments`** - Complete payment lifecycle management
- **`usePiSimplePayments`** - Simplified payment processing
- **`usePiAds`** - Full ads functionality with backend verification
- **`usePiAdsSimple`** - Frontend-only ads display

### **Enhanced State Management**
- **Scope Management** - Track and validate user permissions (`hasScope`, `hasAllScopes`, `missingScopes`)
- **Token Management** - Real-time expiry detection and handling (`isTokenExpired`, `tokenExpiry`)
- **Authentication State** - Comprehensive auth state tracking (`isAuthenticated`, `authenticatedScopes`)
- **Automatic Recovery** - Auto-reset invalid states (`reset`, `refreshAuth`)
- **Zombie Auth Prevention** - Prevents broken authentication states

### **React Components**
- **`PaymentButton`** - Pre-built payment button with backend integration
- **`PaymentProcessDisplay`** - Display real-time payment progress
- **`SimplePaymentButton`** - Simple payment button for basic use cases

### **Backend Actions**
- **`processA2UWithdrawalAction`** - Server-side A2U payment processing
- **`createAndMakeA2UPayment`** - High-level A2U payment helper
- **`approvePaymentAction`** - Server-side U2A payment approval
- **`completePaymentAction`** - Server-side U2A payment completion
- **`verifyRewardedAdAction`** - Server-side ads verification
- **`getAdEligibilityAction`** - Check user ad eligibility
- **`getUserAdStatsAction`** - Get user ad statistics

## 🔐 Security Features

### **Access Token Validation (U2A Payments)**
- **Automatic Token Handling**: Frontend automatically sends user access tokens
- **Backend Validation**: Optional server-side token verification with Pi Platform API
- **User Identity Verification**: Ensures payments are from authenticated users
- **Tamper Prevention**: Prevents malicious users from faking payment requests

### **Configuration Management**
```typescript
// PiPlatformConfig for U2A payments (User-to-App)
const piPlatformConfig: PiPlatformConfig = {
  apiKey: process.env.PI_API_KEY!
};

// PiBackendConfig for A2U payments (App-to-User)
const piBackendConfig: PiBackendConfig = {
  apiKey: process.env.PI_API_KEY!,
  privateSeed: process.env.PI_WALLET_PRIVATE_SEED!
};
```

## 🚀 Quick Start

1. **Install the SDK:**
   ```bash
   npm install @xhilo/pi-sdk
   ```

2. **Create a new Next.js app with template:**
   ```bash
   npx create-next-app@latest my-pi-app --template @xhilo/pi-sdk/nextjs
   ```

3. **Read the documentation:**
   - [Configuration Guide](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/CONFIGURATION_GUIDE) - Start here for proper setup.
   - [Real Examples](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/REAL_EXAMPLES) - Working examples based on actual implementation
   - [Getting Started](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/getting-started)
   - [API Reference](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/api-reference)
   - [React SDK](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/react)
   - [Backend SDK](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/backend)
   - [Templates](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/templates)
   - [Troubleshooting](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/troubleshooting)

## 📚 Documentation Section

### [Configuration Guide](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/CONFIGURATION_GUIDE)
Complete guide to the configuration-driven architecture, including PiBackendConfig vs PiPlatformConfig, framework integration, and best practices.

### [Real Examples](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/REAL_EXAMPLES)
Working examples based on the actual implementation, including React hooks, backend actions, and complete integration patterns.

### [Getting Started](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/getting-started)
Learn how to set up the SDK in your project, configure environment variables, and make your first payment.

### [API Reference](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/api-reference)
Complete API documentation for all components, hooks, and backend functions.

### [React SDK](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/react)
React-specific documentation including hooks, components, and provider usage.

### [Backend SDK](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/backend)
Backend SDK documentation for server-side payment processing.

### [Templates](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/templates)
Guide to using the included Next.js template and customizing it for your needs.

### [Examples](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/examples)
Real-world examples and use cases for different integration scenarios.

### [Troubleshooting](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/troubleshooting)
Common issues, solutions, and debugging tips.

### [Security Guide](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/react/security)
Access token validation, security best practices, and implementation guide.

## 🔗 Links

- **npm Package**: [@xhilo/pi-sdk](https://www.npmjs.com/package/@xhilo/pi-sdk)
- **GitHub Repository**: [pi-sdk](https://github.com/xhilo-labs/pi-sdk)
- **Pi Network Docs**: [developers.minepi.com](https://developers.minepi.com/)

## 🛠️ Development

This documentation site is built with Jekyll and hosted on GitHub Pages.

### Local Development

1. Install Jekyll:
   ```bash
   gem install jekyll bundler
   ```

2. Install dependencies:
   ```bash
   bundle install
   ```

3. Start local server:
   ```bash
   bundle exec jekyll serve
   ```

4. Open [http://localhost:4000](http://localhost:4000)

### Adding Content

- Add new pages as `.md` files in the root directory
- Update `_config.yml` to add navigation items
- Use Jekyll front matter for page metadata

## 📄 License

MIT License - see LICENSE file for details.

## 🤝 Contributing

Contributions are welcome! Please read our contributing guidelines and submit pull requests.

## 📚 Additional Guides

- **[Enhanced State Management Guide](./ENHANCED_STATE_MANAGEMENT.md)** - Comprehensive guide to the new state management features
- **[Configuration Guide](./CONFIGURATION_GUIDE.md)** - How to configure the SDK properly
- **[Real Examples](./REAL_EXAMPLES.md)** - Working code examples for common use cases

## 📞 Support

- **Documentation**: This site
- **Issues**: [GitHub Issues](https://github.com/xhilo-labs/pi-sdk/issues)
- **Discussions**: [GitHub Discussions](https://github.com/xhilo-labs/pi-sdk/discussions)

---

*Built with ❤️ for the Pi Network community*