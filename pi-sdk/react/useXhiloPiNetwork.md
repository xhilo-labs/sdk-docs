# useXhiloPiNetwork Hook

The main hook for Pi Network integration. This is the core hook that provides access to all Pi Network functionality including authentication, payments, ads, and more.

## 📋 Overview

`useXhiloPiNetwork` is the foundational hook that wraps the Pi Network SDK and provides a React-friendly interface for all Pi Network operations.

## 🚀 Basic Usage

```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk';

function MyComponent() {
  const {
    initialize,
    authenticate,
    createPayment,
    user,
    isInitialized,
    // ... other methods
  } = useXhiloPiNetwork();

  // Your component logic
}
```

## 🔧 Parameters

### `sandbox` (optional)
- **Type:** `boolean`
- **Default:** `false`
- **Description:** Enable sandbox mode for testing

### `allowedOrigins` (optional)
- **Type:** `string[]`
- **Default:** `undefined`
- **Description:** Allowed origins for security

```tsx
// Production mode (default)
const pi = useXhiloPiNetwork();

// Sandbox mode for testing
const pi = useXhiloPiNetwork(true);

// With allowed origins
const pi = useXhiloPiNetwork(false, ['https://myapp.com']);
```

## 🔧 Enhanced State Management

The hook now includes comprehensive state management for authentication, scopes, and tokens:

### **Authentication State**
```tsx
const {
  isAuthenticated,        // Whether user is currently authenticated
  authenticatedScopes,    // Array of granted scopes
  user,                   // Current user data
  isInitialized,          // Whether SDK is initialized
} = useXhiloPiNetwork();
```

### **Scope Management**
```tsx
const {
  hasScope,               // Check if user has specific scope
  hasAllScopes,           // Check if user has all required scopes
  missingScopes,          // Get scopes user is missing
  refreshAuth,            // Re-authenticate with additional scopes
} = useXhiloPiNetwork();

// Check specific scope
const canPay = hasScope('payments');

// Check multiple scopes
const hasFullAccess = hasAllScopes(['username', 'payments', 'wallet_address']);

// Get missing scopes
const missing = missingScopes(['payments', 'wallet_address']);

// Request additional scopes
await refreshAuth(['wallet_address']);
```

### **Token Management**
```tsx
const {
  isTokenExpired,         // Whether current token has expired
  tokenExpiry,            // Timestamp when token expires
  reset,                  // Reset all state and clear localStorage
} = useXhiloPiNetwork();

// Check if token is expired
if (isTokenExpired) {
  // Handle expired token
}

// Reset all state
reset(); // Clears everything and starts fresh
```

## 📤 Return Values

### Core State
- **`isInitialized`** - `boolean` - Whether the Pi SDK is initialized
- **`user`** - `PiUser | null` - Current authenticated user
- **`getConsoleLogs`** - `() => ConsoleLog[]` - Get console logs for debugging

### Enhanced State Management
- **`isAuthenticated`** - `boolean` - Whether user is currently authenticated
- **`authenticatedScopes`** - `Scope[]` - Array of granted scopes
- **`hasScope(scope)`** - `(scope: Scope) => boolean` - Check if user has specific scope
- **`hasAllScopes(scopes)`** - `(scopes: Scope[]) => boolean` - Check if user has all required scopes
- **`missingScopes(requiredScopes)`** - `(requiredScopes: Scope[]) => Scope[]` - Get scopes user is missing
- **`isTokenExpired`** - `boolean` - Whether current token has expired
- **`tokenExpiry`** - `number | null` - Timestamp when token expires
- **`reset()`** - `() => void` - Reset all state and clear localStorage
- **`refreshAuth(additionalScopes?)`** - `(additionalScopes?: Scope[]) => Promise<PiOperationResult<PiUser>>` - Re-authenticate with additional scopes

### Authentication Methods
- **`initialize()`** - Initialize the Pi SDK
- **`authenticate(scopes, onIncompletePaymentFound)`** - Authenticate user
- **`login()`** - Login user (alias for authenticate)
- **`logout()`** - Logout current user

### Payment Methods
- **`createPayment(paymentData, callbacks)`** - Create a payment
- **`createAndMakePayment(userId, amount, itemName, callbacks)`** - Create payment with helper

### Validation Methods
- **`validateWithdrawal(amount, minWithdrawal, userBalance)`** - Validate withdrawal
- **`recordTaskCompletion(taskId, reward, metadata)`** - Record task completion
- **`validateWalletAddress()`** - Validate wallet address

### Ad Methods
- **`checkAdRewardEligibility(adType, maxDailyViews)`** - Check ad eligibility
- **`prepareMetadata(title, description, image, url)`** - Prepare ad metadata
- **`showAd(adType)`** - Show an ad
- **`isAdReady(adType)`** - Check if ad is ready
- **`requestAd(adType)`** - Request an ad

### Utility Methods
- **`openShareDialog(title, message)`** - Open share dialog

## 🎯 When to Use This Hook

### ✅ Use `useXhiloPiNetwork` when:
- You need **full control** over Pi Network functionality
- You want to **build custom payment flows**
- You need access to **all Pi Network features** (ads, validation, etc.)
- You're building a **complex application** with custom UI
- You want to **handle authentication manually**
- You need **low-level access** to Pi Network methods

### ❌ Don't use `useXhiloPiNetwork` when:
- You want **simple payments** without backend integration (use `usePiSimplePayments`)
- You need **User-to-App payments with backend** (use `usePiPayments`)
- You want **pre-built components** (use component hooks)

## 📝 Detailed Examples

### 1. Enhanced State Management Example

```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk';
import { useEffect } from 'react';

function EnhancedAuthExample() {
  const {
    // Core functionality
    initialize,
    authenticate,
    user,
    isInitialized,
    
    // Enhanced state management
    isAuthenticated,
    authenticatedScopes,
    hasScope,
    hasAllScopes,
    missingScopes,
    isTokenExpired,
    tokenExpiry,
    reset,
    refreshAuth,
  } = useXhiloPiNetwork();

  // Auto-handle token expiry
  useEffect(() => {
    if (isAuthenticated && isTokenExpired) {
      console.log('Token expired, resetting authentication...');
      reset();
    }
  }, [isAuthenticated, isTokenExpired, reset]);

  // Check user permissions
  const canMakePayments = hasScope('payments');
  const canAccessWallet = hasScope('wallet_address');
  const hasFullAccess = hasAllScopes(['username', 'payments', 'wallet_address']);

  // Get missing scopes
  const requiredScopes = ['username', 'payments'];
  const missing = missingScopes(requiredScopes);

  const handleLogin = async () => {
    const result = await authenticate(['username', 'payments', 'wallet_address'], () => {});
    if (result.success) {
      console.log('User authenticated with scopes:', authenticatedScopes);
    }
  };

  const handleRequestAdditionalScopes = async () => {
    if (missing.length > 0) {
      const result = await refreshAuth(missing);
      if (result.success) {
        console.log('Additional scopes granted!');
      }
    }
  };

  const handleReset = () => {
    reset(); // Clears everything and starts fresh
  };

  return (
    <div>
      <h3>Authentication Status</h3>
      <p>Authenticated: {isAuthenticated ? 'Yes' : 'No'}</p>
      <p>Scopes: {authenticatedScopes.join(', ')}</p>
      <p>Token Expired: {isTokenExpired ? 'Yes' : 'No'}</p>
      
      <h3>Permissions</h3>
      <p>Can Make Payments: {canMakePayments ? 'Yes' : 'No'}</p>
      <p>Can Access Wallet: {canAccessWallet ? 'Yes' : 'No'}</p>
      <p>Has Full Access: {hasFullAccess ? 'Yes' : 'No'}</p>
      
      {missing.length > 0 && (
        <div>
          <p>Missing Scopes: {missing.join(', ')}</p>
          <button onClick={handleRequestAdditionalScopes}>
            Request Additional Scopes
          </button>
        </div>
      )}
      
      <div>
        <button onClick={handleLogin}>Login</button>
        <button onClick={handleReset}>Reset All</button>
      </div>
    </div>
  );
}
```

### 2. Basic Authentication Flow

```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk';

function AuthExample() {
  const { 
    initialize, 
    authenticate, 
    user, 
    isInitialized, 
    logout 
  } = useXhiloPiNetwork();

  const handleInitialize = async () => {
    const result = await initialize();
    if (result.success) {
      console.log('Pi SDK initialized');
    } else {
      console.error('Initialization failed:', result.message);
    }
  };

  const handleLogin = async () => {
    const result = await authenticate(
      ['payments', 'username'], // Required scopes
      (incompletePayment) => {
        // Handle incomplete payments
        console.log('Incomplete payment found:', incompletePayment);
      }
    );
    
    if (result.success) {
      console.log('User authenticated:', result.data);
    }
  };

  const handleLogout = async () => {
    const result = await logout();
    if (result.success) {
      console.log('User logged out');
    }
  };

  if (user) {
    return (
      <div>
        <h2>Welcome, {user.username}!</h2>
        <p>Wallet: {user.walletAddress}</p>
        <button onClick={handleLogout}>Logout</button>
      </div>
    );
  }

  return (
    <div>
      <button onClick={handleInitialize} disabled={isInitialized}>
        {isInitialized ? 'Initialized' : 'Initialize Pi SDK'}
      </button>
      <button onClick={handleLogin} disabled={!isInitialized}>
        Login with Pi
      </button>
    </div>
  );
}
```

### 2. Custom Payment Flow

```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk';

function CustomPayment() {
  const { createPayment, isInitialized, user } = useXhiloPiNetwork();

  const handleCustomPayment = async () => {
    if (!user) {
      console.error('User not authenticated');
      return;
    }

    const paymentData = {
      amount: 2.5,
      memo: 'Custom payment for premium features',
      metadata: {
        orderId: 'CUST-' + Date.now(),
        item: 'Premium Features',
        userId: user.uid
      }
    };

    const callbacks = {
      onReadyForServerApproval: async (paymentId) => {
        console.log('Payment ready for approval:', paymentId);
        // Call your backend API here
        const response = await fetch('/api/payments/approve', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ paymentId, userId: user.uid })
        });
        const result = await response.json();
        if (!result.success) {
          throw new Error('Server approval failed');
        }
      },
      onReadyForServerCompletion: async (paymentId, txid) => {
        console.log('Payment ready for completion:', paymentId, txid);
        // Call your backend API here
        const response = await fetch('/api/payments/complete', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ paymentId, txid, userId: user.uid })
        });
        const result = await response.json();
        if (!result.success) {
          throw new Error('Server completion failed');
        }
      },
      onCancel: () => {
        console.log('Payment canceled');
      },
      onError: (error) => {
        console.error('Payment error:', error);
      }
    };

    const result = await createPayment(paymentData, callbacks);
    if (result.success) {
      console.log('Payment initiated successfully');
    } else {
      console.error('Payment failed:', result.message);
    }
  };

  return (
    <button 
      onClick={handleCustomPayment} 
      disabled={!isInitialized || !user}
    >
      Make Custom Payment
    </button>
  );
}
```

### 3. Ad Integration

```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk';

function AdExample() {
  const { 
    checkAdRewardEligibility, 
    showAd, 
    isAdReady, 
    prepareMetadata 
  } = useXhiloPiNetwork();

  const handleShowRewardedAd = async () => {
    // Check if user is eligible for ad rewards
    const eligibility = await checkAdRewardEligibility('rewarded', 5);
    if (!eligibility.success) {
      console.log('User not eligible for ad rewards');
      return;
    }

    // Check if ad is ready
    const adReady = await isAdReady('rewarded');
    if (!adReady.success || !adReady.data?.isReady) {
      console.log('Ad not ready');
      return;
    }

    // Prepare metadata for the ad
    const metadata = await prepareMetadata(
      'Earn Pi Coins!',
      'Watch this ad to earn Pi coins',
      'https://example.com/ad-image.jpg',
      'https://example.com'
    );

    if (!metadata.success) {
      console.error('Failed to prepare metadata');
      return;
    }

    // Show the ad
    const adResult = await showAd('rewarded');
    if (adResult.success) {
      console.log('Ad shown successfully:', adResult.data);
    } else {
      console.error('Failed to show ad:', adResult.message);
    }
  };

  return (
    <button onClick={handleShowRewardedAd}>
      Watch Ad to Earn Pi
    </button>
  );
}
```

### 4. Task Completion

```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk';

function TaskExample() {
  const { recordTaskCompletion } = useXhiloPiNetwork();

  const handleTaskCompletion = async () => {
    const result = await recordTaskCompletion(
      'daily-login', // Task ID
      1.0, // Reward amount
      { 
        completedAt: new Date().toISOString(),
        source: 'web-app'
      } // Metadata
    );

    if (result.success) {
      console.log('Task completed successfully:', result.data);
    } else {
      console.error('Task completion failed:', result.message);
    }
  };

  return (
    <button onClick={handleTaskCompletion}>
      Complete Daily Login Task
    </button>
  );
}
```

## 🔍 Debugging

### Console Logs

```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk';

function DebugExample() {
  const { getConsoleLogs } = useXhiloPiNetwork();

  const viewLogs = () => {
    const logs = getConsoleLogs();
    console.table(logs);
  };

  return (
    <button onClick={viewLogs}>
      View Console Logs
    </button>
  );
}
```

### Error Handling

```tsx
function ErrorHandlingExample() {
  const { initialize, authenticate } = useXhiloPiNetwork();

  const handleInitialize = async () => {
    try {
      const result = await initialize();
      if (!result.success) {
        // Handle initialization error
        console.error('Initialization failed:', result.message);
        // Show user-friendly error message
        alert('Failed to initialize Pi SDK. Please try again.');
      }
    } catch (error) {
      // Handle unexpected errors
      console.error('Unexpected error:', error);
      alert('An unexpected error occurred. Please refresh the page.');
    }
  };

  return <button onClick={handleInitialize}>Initialize</button>;
}
```

## ⚠️ Important Notes

1. **Always check `isInitialized`** before calling Pi Network methods
2. **Handle errors gracefully** - Pi Network operations can fail
3. **Use appropriate scopes** when authenticating
4. **Implement proper loading states** for better UX
5. **Test in sandbox mode** before going to production

## 🔗 Related Hooks

- [`usePiPayments`](./usePiPayments.md) - High-level payment processing
- [`usePiSimplePayments`](./usePiSimplePayments.md) - Simple payment creation
- [`useXhiloPi`](./useXhiloPi.md) - Provider context access
