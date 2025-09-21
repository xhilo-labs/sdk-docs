# Pi React SDK Example

This is an example React application demonstrating how to use the `@xhilo/pi-sdk` package.

## Features

- **Authentication**: Sign in with Pi Network
- **User Management**: View user profile and wallet information
- **Payment Testing**: Create and test Pi Network payments
- **Ad Integration**: Test interstitial and rewarded ads
- **Debug Tools**: View SDK logs and debug information

## Getting Started

1. Install dependencies:
   ```bash
   npm install
   ```

2. Start the development server:
   ```bash
   npm run dev
   ```

3. Open your browser and navigate to `http://localhost:3000`

## Usage

1. **Sign In**: Click "Sign In with Pi" to authenticate with your Pi Network account
2. **Navigate**: Use the bottom navigation to explore different features
3. **Test Payments**: Go to the Payment tab to test payment creation
4. **View Logs**: Click "Show Debug Logs" to see SDK activity

## Important Notes

- This app must be run in the Pi Browser for full functionality
- The Pi SDK script is loaded from `https://sdk.minepi.com/pi-sdk.js`
- All Pi Network features require proper authentication and permissions

## Available Scripts

- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run preview` - Preview production build
- `npm run lint` - Run ESLint
