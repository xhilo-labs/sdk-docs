# Pi SDK Documentation

This is the documentation site for the Pi SDK - a unified SDK for Pi Network integration with React frontend and Node.js backend support.

## 🏗️ Architecture

The Pi SDK uses an **action-based architecture** for backend operations:

- **User-to-App (U2A) Payments**: Uses `piPlatformClient` and `userToAppPayment` actions
- **App-to-User (A2U) Payments**: Uses `pi-backend-sdk` and `appToUserPayment` actions
- **Consistent Error Handling**: All actions return standardized `PiOperationResult` types
- **Type Safety**: Full TypeScript support with comprehensive type definitions
- **Security**: Optional access token validation for enhanced security

## 🎯 Features

### **React Hooks**
- **`useXhiloPiNetwork`** - Core Pi Network integration with authentication
- **`usePiPayments`** - Complete payment lifecycle management
- **`usePiSimplePayments`** - Simplified payment processing
- **`usePiAds`** - Ads functionality with backend verification
- **`XhiloPiProvider`** - Context provider for Pi Network state

### **Backend Actions**
- **`userToAppPayment`** - Server-side U2A payment processing
- **`appToUserPayment`** - Server-side A2U payment processing
- **`ads`** - Ads verification and reward processing
- **`piPlatformFetchClient`** - Pi Platform API integration
- **`pi-backend-sdk`** - Pi Backend SDK integration

## 🔐 Security Features

### **Access Token Validation (U2A Payments)**
- **Automatic Token Handling**: Frontend automatically sends user access tokens
- **Backend Validation**: Optional server-side token verification with Pi Platform API
- **User Identity Verification**: Ensures payments are from authenticated users
- **Tamper Prevention**: Prevents malicious users from faking payment requests

### **Environment Variables**
```bash
# For U2A payments (User-to-App)
PI_APP_SECRET=your_pi_app_secret_key

# For A2U payments (App-to-User)
PI_API_KEY=your_pi_api_key
PI_WALLET_PRIVATE_SEED=your_wallet_private_seed
```

## 🚀 Quick Start

1. **Install the SDK:**
   ```bash
   npm install @xhilo/pi-sdk
   ```

2. **Create a new Next.js app with template:**
   ```bash
   npm install @xhilo/pi-sdk --template -nextjs
   ```

3. **Read the documentation:**
   - [Getting Started](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/getting-started)
   - [Developer Flow](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/react/dev-flow)
   - [API Reference](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/api-reference)
   - [React SDK](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/react)
   - [Backend SDK](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/backend)
   - [Examples](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/examples)
   - [Templates](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/templates)
   - [Troubleshooting](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/troubleshooting)

## 📚 Documentation Sections

### [Getting Started](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/getting-started)
Learn how to set up the SDK in your project, configure environment variables, and make your first payment.

### [Developer Flow](https://github.com/xhilo-labs/sdk-docs/tree/main/pi-sdk/react/dev-flow)
Complete implementation guide for payments and ads with step-by-step examples and best practices.

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

## 📞 Support

- **Documentation**: This site
- **Issues**: [GitHub Issues](https://github.com/xhilo-labs/pi-sdk/issues)
- **Discussions**: [GitHub Discussions](https://github.com/xhilo-labs/pi-sdk/discussions)

---

*Built with ❤️ for the Pi Network community*