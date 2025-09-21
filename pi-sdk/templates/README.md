# Pi SDK Next.js Template

A comprehensive Next.js template for testing the Pi SDK with multiple payment flows, ads, authentication, and backend integration.

> ✅ **Next.js App Router Compatible** - All components are properly configured with "use client" directives for seamless integration with Next.js 13+ App Router.

## 🚀 Features

### Payment Flows
- **Simple Payment Flow** - Basic payments without backend integration
- **Advanced Payment Flow** - Payments with backend integration and progress tracking
- **Custom Payment Flow** - Full control with custom implementation

### Authentication
- Pi Network user authentication
- User profile display
- Session management

### Ads & Rewards
- Ad eligibility checking
- Ad readiness verification
- Ad request and display
- Rewarded ads testing

### Debug Panel
- Real-time SDK status monitoring
- Console logs viewer with filtering
- User information display
- Export functionality

## 📦 Installation

1. **Clone and setup:**
```bash
cd template
npm install
```

2. **Environment setup:**
   ```bash
   cp .env.example .env.local
   ```

3. **Configure environment variables:**
```env
# Pi Network Configuration
PI_API_KEY=your_pi_api_key_here
PI_WALLET_PRIVATE_SEED=your_wallet_private_seed_here

# Next.js Configuration
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_PI_SANDBOX=true
```

4. **Start development server:**
```bash
npm run dev
```

5. **Open in browser:**
```
http://localhost:3000
```

## 🎯 Usage

### Authentication Tab
- Initialize Pi SDK
- Authenticate with Pi Network
- View user information
- Logout functionality

### Payment Flows Tab
- **Simple Payment**: Test basic payments without backend
- **Advanced Payment**: Test payments with backend integration and progress tracking
- **Custom Payment**: Test full control implementation with custom backend calls

### Ads & Rewards Tab
- Check ad eligibility
- Verify ad readiness
- Request and show ads
- Test rewarded ad functionality

### Debug Panel Tab
- Monitor SDK status in real-time
- View and filter console logs
- Export logs for analysis
- Check user information and environment

## 🔧 Backend API

The template includes real Pi SDK backend integration using action-based architecture:

### POST `/api/payments/approve`
Approves a U2A payment request using `approvePaymentAction`.

**Request:**
```json
{
  "paymentId": "string",
  "userId": "string",
  "amount": "number"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Payment approved successfully",
  "data": {
    "paymentId": "string",
    "userId": "string",
    "approvedAt": "string",
    "amount": "number",
    "status": "approved"
  }
}
```

### POST `/api/payments/complete`
Completes a U2A payment request using `completePaymentAction`.

**Request:**
```json
{
  "paymentId": "string",
  "txid": "string",
  "userId": "string",
  "amount": "number"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Payment completed successfully",
  "data": {
    "paymentId": "string",
    "txid": "string",
    "userId": "string",
    "completedAt": "string",
    "amount": "number",
    "status": "completed"
  }
}
```

### POST `/api/withdraw`
Processes an A2U withdrawal using `processA2UWithdrawalAction`.

**Request:**
```json
{
  "userId": "string",
  "amount": "number",
  "itemName": "string",
  "memo": "string",
  "metadata": "object"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Withdrawal successfully processed",
  "data": {
    "paymentId": "string",
    "txid": "string",
    "userId": "string",
    "amount": "number",
    "completedAt": "string",
    "status": "completed"
  }
}
```

## 🎨 Styling

The template uses Tailwind CSS with custom Pi Network themed colors:

- **Primary**: Pi blue (`#0ea5e9`)
- **Success**: Green (`#10b981`)
- **Error**: Red (`#ef4444`)
- **Warning**: Yellow (`#f59e0b`)

## 📱 Components

### Core Components
- `Header` - Navigation and user status
- `AuthSection` - Authentication management
- `PaymentFlows` - Payment flow testing
- `AdSection` - Ad functionality testing
- `DebugPanel` - Debug and monitoring

### Payment Components
- `SimplePaymentFlow` - Basic payment testing
- `AdvancedPaymentFlow` - Backend-integrated payments
- `CustomPaymentFlow` - Full control implementation

## 🔍 Testing Different Scenarios

### 1. Simple Payment Testing
- Test basic payment creation
- Verify success/error handling
- Test different product configurations

### 2. Advanced Payment Testing
- Test backend integration
- Verify progress tracking
- Test error scenarios
- Monitor payment completion

### 3. Custom Payment Testing
- Test full control implementation
- Verify custom backend calls
- Test manual error handling
- Customize payment metadata

### 4. Ad Testing
- Test ad eligibility
- Verify ad readiness
- Test ad display
- Monitor ad rewards

### 5. Debug Testing
- Monitor SDK status
- Track console logs
- Export debug information
- Verify user authentication

## 🛠️ Development

### Project Structure
```
template/
├── app/
│   ├── api/payments/     # Backend API routes
│   ├── globals.css       # Global styles
│   ├── layout.tsx        # Root layout
│   └── page.tsx          # Main page
├── components/
│   ├── payment/          # Payment flow components
│   ├── Header.tsx        # Header component
│   ├── AuthSection.tsx   # Authentication
│   ├── PaymentFlows.tsx  # Payment testing
│   ├── AdSection.tsx     # Ad testing
│   └── DebugPanel.tsx    # Debug panel
├── package.json
├── next.config.js
├── tailwind.config.js
└── README.md
```

### Available Scripts
- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run start` - Start production server
- `npm run lint` - Run ESLint

## ⚠️ Important Notes

1. **Environment Variables**: Make sure to set up your Pi Network API credentials
2. **Sandbox Mode**: Use sandbox mode for testing (`NEXT_PUBLIC_PI_SANDBOX=true`)
3. **Backend Integration**: The included API routes are for testing only
4. **Production**: Replace mock API routes with real backend implementation
5. **Security**: Never expose API keys in client-side code

## 🔗 Related Documentation

- [Pi SDK Documentation](../react/docs/README.md)
- [Pi Network Developer Docs](https://developers.minepi.com/)
- [Next.js Documentation](https://nextjs.org/docs)

## 📄 License

MIT License - see LICENSE file for details.

