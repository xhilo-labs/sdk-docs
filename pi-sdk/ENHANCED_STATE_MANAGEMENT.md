# Enhanced State Management Guide

## 🚀 Overview

The Pi SDK now includes comprehensive state management for authentication, scopes, and tokens. This prevents common issues like "zombie authentication" where users appear logged in but actually have no valid permissions.

## 🔧 Available State & Methods

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

## 💡 Usage Examples

### **1. Basic Authentication Check**
```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk/react';

function MyComponent() {
  const { 
    isAuthenticated, 
    user, 
    isInitialized,
    login 
  } = useXhiloPiNetwork();

  if (!isInitialized) {
    return <div>Initializing Pi SDK...</div>;
  }

  if (!isAuthenticated) {
    return (
      <button onClick={() => login(['username', 'payments'])}>
        Login with Pi
      </button>
    );
  }

  return <div>Welcome, {user?.username}!</div>;
}
```

### **2. Scope-Based Feature Access**
```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk/react';

function PaymentComponent() {
  const { 
    hasScope, 
    hasAllScopes, 
    missingScopes,
    refreshAuth,
    user 
  } = useXhiloPiNetwork();

  // Check for specific scope
  const canMakePayments = hasScope('payments');
  
  // Check for multiple scopes
  const hasFullAccess = hasAllScopes(['username', 'payments', 'wallet_address']);
  
  // Get missing scopes
  const requiredScopes = ['payments', 'wallet_address'];
  const missing = missingScopes(requiredScopes);

  const handleRequestAdditionalScopes = async () => {
    if (missing.length > 0) {
      const result = await refreshAuth(missing);
      if (result.success) {
        console.log('Additional scopes granted!');
      }
    }
  };

  return (
    <div>
      {canMakePayments ? (
        <PaymentButton />
      ) : (
        <button onClick={handleRequestAdditionalScopes}>
          Grant Payment Permission
        </button>
      )}
      
      {missing.length > 0 && (
        <p>Missing scopes: {missing.join(', ')}</p>
      )}
    </div>
  );
}
```

### **3. Token Expiry Handling**
```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk/react';
import { useEffect } from 'react';

function TokenAwareComponent() {
  const { 
    isTokenExpired, 
    tokenExpiry,
    refreshAuth,
    user 
  } = useXhiloPiNetwork();

  useEffect(() => {
    if (isTokenExpired && user) {
      console.warn('Token expired, refreshing authentication...');
      refreshAuth();
    }
  }, [isTokenExpired, user, refreshAuth]);

  const timeUntilExpiry = tokenExpiry ? tokenExpiry - Date.now() : null;
  const hoursUntilExpiry = timeUntilExpiry ? Math.floor(timeUntilExpiry / (1000 * 60 * 60)) : null;

  return (
    <div>
      {hoursUntilExpiry !== null && (
        <p>Token expires in {hoursUntilExpiry} hours</p>
      )}
      {isTokenExpired && (
        <p className="text-red-500">Token expired! Please re-authenticate.</p>
      )}
    </div>
  );
}
```

### **4. Complete State Reset**
```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk/react';

function SettingsComponent() {
  const { reset, logout } = useXhiloPiNetwork();

  const handleLogout = async () => {
    await logout();
    // User is now logged out, state is cleared
  };

  const handleReset = () => {
    reset();
    // All state is reset, localStorage is cleared
    // User will need to re-authenticate
  };

  return (
    <div>
      <button onClick={handleLogout}>
        Logout
      </button>
      <button onClick={handleReset} className="text-red-500">
        Reset All Data
      </button>
    </div>
  );
}
```

### **5. Progressive Scope Requests**
```tsx
import { useXhiloPiNetwork } from '@xhilo/pi-sdk/react';
import { useState } from 'react';

function ProgressiveAuthComponent() {
  const { 
    hasScope, 
    missingScopes,
    refreshAuth,
    user 
  } = useXhiloPiNetwork();
  const [isRequesting, setIsRequesting] = useState(false);

  const requestScope = async (scope: Scope) => {
    if (hasScope(scope)) return;
    
    setIsRequesting(true);
    try {
      const result = await refreshAuth([scope]);
      if (result.success) {
        console.log(`Scope ${scope} granted!`);
      }
    } catch (error) {
      console.error('Failed to request scope:', error);
    } finally {
      setIsRequesting(false);
    }
  };

  return (
    <div>
      <h3>Available Features:</h3>
      
      <div className="space-y-2">
        <div className="flex items-center gap-2">
          <span>Username Access</span>
          {hasScope('username') ? (
            <span className="text-green-500">✓ Granted</span>
          ) : (
            <button 
              onClick={() => requestScope('username')}
              disabled={isRequesting}
            >
              Request
            </button>
          )}
        </div>
        
        <div className="flex items-center gap-2">
          <span>Payment Access</span>
          {hasScope('payments') ? (
            <span className="text-green-500">✓ Granted</span>
          ) : (
            <button 
              onClick={() => requestScope('payments')}
              disabled={isRequesting}
            >
              Request
            </button>
          )}
        </div>
        
        <div className="flex items-center gap-2">
          <span>Wallet Address Access</span>
          {hasScope('wallet_address') ? (
            <span className="text-green-500">✓ Granted</span>
          ) : (
            <button 
              onClick={() => requestScope('wallet_address')}
              disabled={isRequesting}
            >
              Request
            </button>
          )}
        </div>
      </div>
    </div>
  );
}
```

## 🔧 Best Practices

### **1. Always Check Authentication State**
```tsx
const { isAuthenticated, isInitialized } = useXhiloPiNetwork();

if (!isInitialized) return <LoadingSpinner />;
if (!isAuthenticated) return <LoginPrompt />;
```

### **2. Validate Scopes Before Actions**
```tsx
const { hasScope, refreshAuth } = useXhiloPiNetwork();

const handlePayment = async () => {
  if (!hasScope('payments')) {
    const result = await refreshAuth(['payments']);
    if (!result.success) return;
  }
  
  // Proceed with payment
};
```

### **3. Handle Token Expiry Gracefully**
```tsx
const { isTokenExpired, refreshAuth } = useXhiloPiNetwork();

useEffect(() => {
  if (isTokenExpired) {
    refreshAuth();
  }
}, [isTokenExpired, refreshAuth]);
```

### **4. Use Reset for Clean State**
```tsx
const { reset } = useXhiloPiNetwork();

const handleAppReset = () => {
  reset(); // Clears everything and starts fresh
};
```

## 🎯 Benefits

1. **Granular Control**: Check specific scopes instead of just authentication status
2. **Progressive Enhancement**: Request additional scopes as needed
3. **Token Management**: Automatic expiry detection and handling
4. **Clean State**: Easy reset functionality for testing and error recovery
5. **Better UX**: Show users exactly what permissions they have and what they're missing
6. **Debugging**: Clear state management makes debugging easier

## 🚨 Preventing Zombie Authentication

The enhanced state management prevents the common "zombie authentication" issue where users appear logged in but have no valid scopes or expired tokens:

```tsx
// ❌ WRONG: Only checking if user exists
if (user && user.uid) {
  setIsAuthenticated(true); // But user might have no scopes!
}

// ✅ CORRECT: Comprehensive validation
if (user && user.uid && isAuthenticated && !isTokenExpired) {
  const requiredScopes = ['username', 'payments'];
  const hasRequiredScopes = requiredScopes.every(scope => hasScope(scope));
  
  if (hasRequiredScopes) {
    // User is truly authenticated with valid scopes
    setIsAuthenticated(true);
  } else {
    // User missing scopes - reset and re-authenticate
    reset();
  }
}
```

This enhanced state management makes the Pi Network integration much more robust and user-friendly! 🚀
