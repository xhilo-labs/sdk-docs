# Pi SDK Documentation

A unified SDK for Pi Network integration with React frontend and Node.js backend support.

## 🏗️ Architecture

The Pi SDK uses an **action-based architecture** for backend operations:

- **User-to-App (U2A) Payments**: Uses `piPlatformClient` and `userToAppPayment` actions
- **App-to-User (A2U) Payments**: Uses `pi-backend-sdk` and `appToUserPayment` actions
- **Consistent Error Handling**: All actions return standardized `PiOperationResult` types
- **Type Safety**: Full TypeScript support with comprehensive type definitions

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
   - [Getting Started](./getting-started/)
   - [API Reference](./api-reference/)
   - [React SDK](./react/)
   - [Backend SDK](./backend/)
   - [Examples](./examples/)
   - [Templates](./templates/)
   - [Troubleshooting](./troubleshooting/)

## 📚 Documentation Sections

### [Getting Started](./getting-started/)
Learn how to set up the SDK in your project, configure environment variables, and make your first payment.

### [API Reference](./api-reference/)
Complete API documentation for all components, hooks, and backend functions.

### [React SDK](./react/)
React-specific documentation including hooks, components, and provider usage.

### [Backend SDK](./backend/)
Backend SDK documentation for server-side payment processing.

### [Examples](./examples/)
Real-world examples and use cases for different integration scenarios.

### [Templates](./templates/)
Guide to using the included Next.js template and customizing it for your needs.

### [Troubleshooting](./troubleshooting/)
Common issues, solutions, and debugging tips.

## 🔗 Links

- **npm Package**: [@xhilo/pi-sdk](https://www.npmjs.com/package/@xhilo/pi-sdk)
- **GitHub Repository**: [pi-sdk](https://github.com/xhilo-labs/pi-sdk)
- **Pi Network Docs**: [developers.minepi.com](https://developers.minepi.com/)

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
