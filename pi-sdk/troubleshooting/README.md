---
layout: default
title: Troubleshooting
---

# Troubleshooting

Common issues and solutions for the Pi SDK.

## 🚨 Common Issues

### "use client" Errors

**Error:** `You're importing a component that needs createContext/useState/useEffect. It only works in a Client Component but none of its parents are marked with "use client"`

**Solution:** Add `'use client';` directive to the top of your component file:

```tsx
'use client'

import { useXhiloPiNetwork } from '@xhilo/pi-sdk/react'

function MyComponent() {
  const user = useXhiloPiUser()
  // ... rest of component
}
```

### Pi SDK Not Loading

**Error:** Pi SDK functions are undefined or not working

**Solutions:**
1. **Check Pi SDK script**: Ensure the script is included in your layout:

```tsx
// app/layout.tsx
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <head>
        <script src="https://sdk.minepi.com/pi-sdk.js" async />
      </head>
      <body>
        {children}
      </body>
    </html>
  )
}
```

2. **Use Pi Browser**: The SDK works best in the Pi Browser
3. **Check environment**: Ensure you're in the correct environment (sandbox vs production)

### Authentication Issues

**Error:** User authentication fails or user is always null

**Solutions:**
1. **Check environment variables**:
```env
PI_API_KEY=your_actual_api_key
PI_WALLET_PRIVATE_SEED=your_actual_seed
NEXT_PUBLIC_PI_SANDBOX=true
```

2. **Initialize SDK properly**:
```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk/react'

function App() {
  const { initialize, authenticate, user, isInitialized } = useXhiloPiNetwork()
  
  useEffect(() => {
    if (!isInitialized) {
      initialize()
    }
  }, [isInitialized, initialize])
  
  return <YourComponents />
}
```

3. **Check user state**:
```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk/react'

function UserComponent() {
  const { user, isInitialized, isAuthenticated } = useXhiloPiNetwork()
  
  if (!isInitialized) {
    return <div>Loading SDK...</div>
  }
  
  if (!isAuthenticated || !user) {
    return <div>Please log in</div>
  }
  
  return <div>Welcome, {user.username}!</div>
}
```

### Payment Processing Errors

**Error:** Payments fail to create, approve, or complete

**Solutions:**
1. **Check payment parameters**:
```tsx
const payment = await createPayment({
  amount: 1.0, // Must be a positive number
  memo: 'Test payment', // Required string
  metadata: { userId: '123' } // Optional object
})
```

2. **Handle errors properly**:
```tsx
try {
  const payment = await createPayment({
    amount: 1.0,
    memo: 'Test payment'
  })
} catch (error) {
  console.error('Payment error:', error)
  // Handle specific error types
  if (error.name === 'PaymentError') {
    alert('Payment failed: ' + error.message)
  }
}
```

3. **Check network connectivity**: Ensure stable internet connection

### Build Errors

**Error:** Next.js build fails with TypeScript or import errors

**Solutions:**
1. **Check imports**: Ensure correct import paths:
```tsx
// Correct
import { useXhiloPiNetwork } from '@xhilo/pi-sdk/react'

// Incorrect
import { useXhiloPiUser } from '@xhilo/pi-sdk'
```

2. **Update TypeScript**: Ensure you're using compatible TypeScript version
3. **Check Next.js version**: Ensure you're using Next.js 14+

### Environment Variable Issues

**Error:** Environment variables not loading

**Solutions:**
1. **Check file name**: Must be `.env.local` (not `.env`)
2. **Check location**: File must be in project root
3. **Restart dev server**: After adding new environment variables
4. **Check variable names**: Must start with `NEXT_PUBLIC_` for client-side access

```env
# .env.local
PI_API_KEY=your_api_key
PI_WALLET_PRIVATE_SEED=your_seed
NEXT_PUBLIC_PI_SANDBOX=true
NEXT_PUBLIC_APP_URL=http://localhost:3000
```

## 🔍 Debugging Tips

### Use the Debug Panel

The template includes a debug panel to monitor:
- SDK initialization status
- User authentication state
- Real-time console logs
- Error messages

### Check Browser Console

Open browser dev tools and check for:
- JavaScript errors
- Network request failures
- Console warnings

### Verify SDK Status

```tsx
import { useXhiloPiReady, useXhiloPiUser } from '@xhilo/pi-sdk/react'

function DebugInfo() {
  const isReady = useXhiloPiReady()
  const user = useXhiloPiUser()
  
  return (
    <div>
      <p>SDK Ready: {isReady ? 'Yes' : 'No'}</p>
      <p>User: {user ? user.username : 'Not logged in'}</p>
    </div>
  )
}
```

### Check Network Requests

Monitor network tab in dev tools for:
- Failed API calls
- CORS errors
- Authentication failures

## 🛠️ Advanced Troubleshooting

### CORS Issues

**Error:** CORS policy blocks requests

**Solution:** Configure your backend to allow CORS:

```javascript
// Express.js example
app.use(cors({
  origin: ['http://localhost:3000', 'https://yourdomain.com'],
  credentials: true
}))
```

### Memory Leaks

**Issue:** App becomes slow or crashes over time

**Solutions:**
1. **Clean up event listeners**:
```tsx
useEffect(() => {
  const handleEvent = () => {
    // Handle event
  }
  
  window.addEventListener('pi:ready', handleEvent)
  
  return () => {
    window.removeEventListener('pi:ready', handleEvent)
  }
}, [])
```

2. **Avoid unnecessary re-renders**:
```tsx
const memoizedValue = useMemo(() => {
  return expensiveCalculation()
}, [dependencies])
```

### Performance Issues

**Issue:** App is slow or unresponsive

**Solutions:**
1. **Use React.memo** for expensive components:
```tsx
const ExpensiveComponent = React.memo(({ data }) => {
  return <div>{data}</div>
})
```

2. **Optimize re-renders**:
```tsx
const handleClick = useCallback(() => {
  // Handle click
}, [dependencies])
```

3. **Lazy load components**:
```tsx
const LazyComponent = lazy(() => import('./LazyComponent'))
```

## 📞 Getting Help

### Check Documentation

1. **API Reference**: [api-reference](/api-reference)
2. **Examples**: [examples](/examples)
3. **Templates**: [templates](/templates)

### Common Solutions

1. **Restart dev server**: `npm run dev`
2. **Clear cache**: Delete `.next` folder and restart
3. **Reinstall dependencies**: `rm -rf node_modules && npm install`
4. **Check Pi Browser**: Ensure you're testing in Pi Browser

### Still Having Issues?

1. **Check GitHub Issues**: [pi-react-sdk/issues](https://github.com/xhilo-labs/pi-sdk/issues)
2. **Create New Issue**: Provide detailed error information
3. **Include Debug Info**: Use the debug panel to gather information

### Error Reporting

When reporting issues, include:
- Error message and stack trace
- Steps to reproduce
- Environment details (OS, Node.js version, browser)
- SDK version
- Code snippet that causes the issue

## 🔧 Development Tips

### Use TypeScript

Enable TypeScript for better error catching:

```tsx
interface PaymentProps {
  amount: number
  memo: string
  onSuccess: (payment: Payment) => void
}

function PaymentButton({ amount, memo, onSuccess }: PaymentProps) {
  // Component implementation
}
```

### Test in Pi Browser

Always test your app in the Pi Browser for full functionality.

### Use Environment Variables

Never hardcode sensitive information:

```tsx
// Good
const apiKey = process.env.NEXT_PUBLIC_PI_API_KEY

// Bad
const apiKey = 'your-actual-api-key'
```

### Handle Loading States

Always show loading states for better UX:

```tsx
const [isLoading, setIsLoading] = useState(false)

const handlePayment = async () => {
  setIsLoading(true)
  try {
    await createPayment({ amount: 1.0, memo: 'Test' })
  } finally {
    setIsLoading(false)
  }
}
```
