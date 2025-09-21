# useXhiloPi Hook

A context-based hook that provides access to the Pi Network context from the `XhiloPiProvider`. This is useful when you need to access Pi functionality from deeply nested components.

## 📋 Overview

`useXhiloPi` allows you to access the Pi Network context without passing props through multiple component levels. It's particularly useful in complex component hierarchies where you need Pi functionality in child components.

## 🚀 Basic Usage

```tsx
import { useXhiloPi } from '@xhilo/pi-sdk';

function ChildComponent() {
  const { user, isInitialized, initialize } = useXhiloPi();

  if (!isInitialized) {
    return <div>Initializing Pi SDK...</div>;
  }

  if (!user) {
    return <div>Please log in with Pi</div>;
  }

  return <div>Welcome, {user.username}!</div>;
}
```

## 🔧 Prerequisites

This hook requires your app to be wrapped with `XhiloPiProvider`:

```tsx
import { XhiloPiProvider } from '@xhilo/pi-sdk';

function App() {
  return (
    <XhiloPiProvider>
      <YourApp />
    </XhiloPiProvider>
  );
}
```

## 📤 Return Values

The hook returns the same values as `useXhiloPiNetwork`:

- **`initialize`** - `() => Promise<PiOperationResult>` - Initialize the Pi SDK
- **`authenticate`** - `(scopes, onIncompletePaymentFound) => Promise<PiOperationResult<PiUser>>` - Authenticate user
- **`createPayment`** - `(paymentData, callbacks) => Promise<PiOperationResult<string>>` - Create a payment
- **`createAndMakePayment`** - `(userId, amount, itemName, callbacks) => Promise<PiOperationResult<string>>` - Create payment with helper
- **`validateWithdrawal`** - `(amount, minWithdrawal, userBalance) => Promise<PiOperationResult>` - Validate withdrawal
- **`recordTaskCompletion`** - `(taskId, reward, metadata) => Promise<PiOperationResult<TaskCompletionData>>` - Record task completion
- **`validateWalletAddress`** - `() => Promise<PiOperationResult<string>>` - Validate wallet address
- **`checkAdRewardEligibility`** - `(adType, maxDailyViews) => Promise<PiOperationResult>` - Check ad eligibility
- **`prepareMetadata`** - `(title, description, image, url) => Promise<PiOperationResult<PiNetMetadataDTO>>` - Prepare ad metadata
- **`showAd`** - `(adType) => Promise<PiOperationResult<ShowAdResponse>>` - Show an ad
- **`isAdReady`** - `(adType) => Promise<PiOperationResult<IsAdReadyResponse>>` - Check if ad is ready
- **`requestAd`** - `(adType) => Promise<PiOperationResult<RequestAdResponse>>` - Request an ad
- **`openShareDialog`** - `(title, message) => Promise<PiOperationResult>` - Open share dialog
- **`logout`** - `() => Promise<PiOperationResult>` - Logout current user
- **`isInitialized`** - `boolean` - Whether the Pi SDK is initialized
- **`user`** - `PiUser | null` - Current authenticated user
- **`getConsoleLogs`** - `() => ConsoleLog[]` - Get console logs for debugging

## 🎯 When to Use This Hook

### ✅ Use `useXhiloPi` when:
- You need Pi functionality in **deeply nested components**
- You want to **avoid prop drilling** for Pi-related data
- You're building a **complex component hierarchy**
- You need **context-based state management**
- You want **consistent Pi state** across components

### ❌ Don't use `useXhiloPi` when:
- You're in a **simple component** (use `useXhiloPiNetwork` directly)
- You don't need **context sharing** (use `useXhiloPiNetwork`)
- You want **component-specific Pi state** (use `useXhiloPiNetwork`)

## 📝 Detailed Examples

### 1. Basic Context Usage

```tsx
import { XhiloPiProvider, useXhiloPi } from '@xhilo/pi-sdk';

function App() {
  return (
    <XhiloPiProvider>
      <Header />
      <MainContent />
      <Footer />
    </XhiloPiProvider>
  );
}

function Header() {
  const { user, isInitialized, logout } = useXhiloPi();

  return (
    <header>
      <h1>My Pi App</h1>
      {user ? (
        <div>
          <span>Welcome, {user.username}!</span>
          <button onClick={logout}>Logout</button>
        </div>
      ) : (
        <span>Please log in</span>
      )}
    </header>
  );
}

function MainContent() {
  const { user, isInitialized } = useXhiloPi();

  if (!isInitialized) {
    return <div>Loading...</div>;
  }

  if (!user) {
    return <LoginForm />;
  }

  return <Dashboard />;
}

function Footer() {
  const { isInitialized } = useXhiloPi();
  
  return (
    <footer>
      <p>Pi SDK Status: {isInitialized ? 'Ready' : 'Not Ready'}</p>
    </footer>
  );
}
```

### 2. Nested Component with Payment

```tsx
function ProductPage() {
  return (
    <div>
      <ProductHeader />
      <ProductDetails />
      <ProductActions />
    </div>
  );
}

function ProductHeader() {
  const { user } = useXhiloPi();
  
  return (
    <div>
      <h1>Premium Product</h1>
      {user && <p>Special price for {user.username}!</p>}
    </div>
  );
}

function ProductDetails() {
  const { isInitialized } = useXhiloPi();
  
  return (
    <div>
      <p>Price: 10 Pi</p>
      <p>Features: Feature 1, Feature 2, Feature 3</p>
      {!isInitialized && <p>Please initialize Pi SDK to purchase</p>}
    </div>
  );
}

function ProductActions() {
  const { createPayment, user, isInitialized } = useXhiloPi();

  const handlePurchase = async () => {
    if (!user || !isInitialized) {
      alert('Please log in first');
      return;
    }

    const result = await createPayment(
      {
        amount: 10,
        memo: 'Premium Product Purchase',
        metadata: { productId: 'premium-001' }
      },
      {
        onReadyForServerApproval: async (paymentId) => {
          console.log('Payment ready for approval:', paymentId);
        },
        onReadyForServerCompletion: async (paymentId, txid) => {
          console.log('Payment completed:', paymentId, txid);
        },
        onCancel: () => console.log('Payment canceled'),
        onError: (error) => console.error('Payment error:', error)
      }
    );

    if (result.success) {
      console.log('Payment initiated successfully');
    }
  };

  return (
    <button 
      onClick={handlePurchase} 
      disabled={!user || !isInitialized}
    >
      Buy Premium Product
    </button>
  );
}
```

### 3. User Profile Component

```tsx
function UserProfile() {
  const { user, logout, isInitialized } = useXhiloPi();

  if (!isInitialized) {
    return <div>Loading user profile...</div>;
  }

  if (!user) {
    return <div>Please log in to view your profile</div>;
  }

  return (
    <div className="user-profile">
      <h2>User Profile</h2>
      <div className="profile-info">
        <p><strong>Username:</strong> {user.username}</p>
        <p><strong>User ID:</strong> {user.uid}</p>
        <p><strong>Wallet Address:</strong> {user.walletAddress}</p>
      </div>
      
      <div className="profile-actions">
        <button onClick={logout}>Logout</button>
      </div>
    </div>
  );
}
```

### 4. Ad Component

```tsx
function AdComponent() {
  const { 
    checkAdRewardEligibility, 
    showAd, 
    isAdReady, 
    user 
  } = useXhiloPi();

  const [adStatus, setAdStatus] = useState('idle');

  const handleShowAd = async () => {
    if (!user) {
      alert('Please log in to watch ads');
      return;
    }

    setAdStatus('checking');
    
    // Check eligibility
    const eligibility = await checkAdRewardEligibility('rewarded', 5);
    if (!eligibility.success) {
      setAdStatus('not-eligible');
      return;
    }

    // Check if ad is ready
    const adReady = await isAdReady('rewarded');
    if (!adReady.success || !adReady.data?.isReady) {
      setAdStatus('not-ready');
      return;
    }

    setAdStatus('showing');
    
    // Show the ad
    const result = await showAd('rewarded');
    if (result.success) {
      setAdStatus('completed');
    } else {
      setAdStatus('error');
    }
  };

  return (
    <div className="ad-component">
      <h3>Watch Ad to Earn Pi</h3>
      <p>Status: {adStatus}</p>
      
      <button 
        onClick={handleShowAd}
        disabled={!user || adStatus === 'showing'}
      >
        {adStatus === 'showing' ? 'Showing Ad...' : 'Watch Ad'}
      </button>
    </div>
  );
}
```

### 5. Debug Component

```tsx
function DebugPanel() {
  const { getConsoleLogs, isInitialized, user } = useXhiloPi();
  const [logs, setLogs] = useState([]);

  const refreshLogs = () => {
    setLogs(getConsoleLogs());
  };

  useEffect(() => {
    refreshLogs();
    const interval = setInterval(refreshLogs, 1000);
    return () => clearInterval(interval);
  }, []);

  return (
    <div className="debug-panel">
      <h3>Debug Information</h3>
      
      <div className="debug-info">
        <p><strong>SDK Initialized:</strong> {isInitialized ? 'Yes' : 'No'}</p>
        <p><strong>User Authenticated:</strong> {user ? 'Yes' : 'No'}</p>
        {user && (
          <>
            <p><strong>Username:</strong> {user.username}</p>
            <p><strong>User ID:</strong> {user.uid}</p>
          </>
        )}
      </div>

      <div className="debug-logs">
        <h4>Console Logs</h4>
        <button onClick={refreshLogs}>Refresh Logs</button>
        <div className="logs-container">
          {logs.map((log, index) => (
            <div key={index} className={`log-entry log-${log.type}`}>
              <span className="log-timestamp">{log.timestamp}</span>
              <span className="log-type">{log.type}</span>
              <span className="log-message">{log.message}</span>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}
```

### 6. Conditional Rendering Based on Pi State

```tsx
function ConditionalContent() {
  const { user, isInitialized } = useXhiloPi();

  // Show loading state
  if (!isInitialized) {
    return <LoadingSpinner />;
  }

  // Show login prompt
  if (!user) {
    return <LoginPrompt />;
  }

  // Show authenticated content
  return (
    <div>
      <AuthenticatedHeader />
      <MainDashboard />
      <UserActions />
    </div>
  );
}

function LoadingSpinner() {
  return <div>Loading Pi SDK...</div>;
}

function LoginPrompt() {
  const { authenticate } = useXhiloPi();

  const handleLogin = async () => {
    const result = await authenticate(['payments'], (payment) => {
      console.log('Incomplete payment found:', payment);
    });
    
    if (result.success) {
      console.log('Login successful');
    }
  };

  return (
    <div>
      <h2>Welcome to Pi App</h2>
      <p>Please log in with your Pi account to continue</p>
      <button onClick={handleLogin}>Login with Pi</button>
    </div>
  );
}
```

## ⚠️ Important Notes

1. **Provider Required** - Must be used within `XhiloPiProvider`
2. **Context Sharing** - All components share the same Pi state
3. **Performance** - Re-renders when Pi state changes
4. **Error Handling** - Handle cases where context is not available
5. **Type Safety** - Provides full TypeScript support

## 🔗 Related Hooks

- [`useXhiloPiNetwork`](./useXhiloPiNetwork.md) - Direct Pi Network functionality
- [`useXhiloPiUser`](./useXhiloPiUser.md) - Get current user
- [`useXhiloPiReady`](./useXhiloPiReady.md) - Check if Pi is ready
- [`useXhiloPiLogs`](./useXhiloPiLogs.md) - Access console logs
