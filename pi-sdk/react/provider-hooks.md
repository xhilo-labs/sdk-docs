# Provider Hooks

Context-based hooks that provide access to Pi Network functionality from the `XhiloPiProvider`. These hooks are useful when you need to access Pi functionality from deeply nested components without prop drilling.

## 📋 Available Provider Hooks

- **[`useXhiloPi`](./useXhiloPi.md)** - Full Pi Network context access
- **[`useXhiloPiUser`](./useXhiloPiUser.md)** - User information and logout
- **[`useXhiloPiReady`](./useXhiloPiReady.md)** - SDK initialization status
- **[`useXhiloPiLogs`](./useXhiloPiLogs.md)** - Console logs for debugging

## 🚀 Prerequisites

All provider hooks require your app to be wrapped with `XhiloPiProvider`:

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

## 🎯 When to Use Provider Hooks

### ✅ Use Provider Hooks when:
- You need Pi functionality in **deeply nested components**
- You want to **avoid prop drilling** for Pi-related data
- You're building a **complex component hierarchy**
- You need **context-based state management**
- You want **consistent Pi state** across components

### ❌ Don't use Provider Hooks when:
- You're in a **simple component** (use `useXhiloPiNetwork` directly)
- You don't need **context sharing** (use `useXhiloPiNetwork`)
- You want **component-specific Pi state** (use `useXhiloPiNetwork`)

## 📝 Usage Examples

### 1. Basic Provider Setup

```tsx
import { XhiloPiProvider } from '@xhilo/pi-sdk';

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
  const { user, logout } = useXhiloPiUser();
  
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
  const { isReady } = useXhiloPiReady();
  
  if (!isReady) {
    return <div>Loading Pi SDK...</div>;
  }
  
  return <Dashboard />;
}

function Footer() {
  const { isInitialized } = useXhiloPiReady();
  
  return (
    <footer>
      <p>Pi SDK Status: {isInitialized ? 'Ready' : 'Not Ready'}</p>
    </footer>
  );
}
```

### 2. User Management

```tsx
function UserManagement() {
  const { user, isAuthenticated, logout } = useXhiloPiUser();
  const { isReady } = useXhiloPiReady();

  if (!isReady) {
    return <div>Initializing Pi SDK...</div>;
  }

  if (!isAuthenticated) {
    return <LoginPrompt />;
  }

  return (
    <div className="user-management">
      <h2>User Management</h2>
      
      <div className="user-info">
        <h3>User Information</h3>
        <p><strong>Username:</strong> {user.username}</p>
        <p><strong>User ID:</strong> {user.uid}</p>
        <p><strong>Wallet Address:</strong> {user.walletAddress}</p>
      </div>
      
      <div className="user-actions">
        <button onClick={logout} className="logout-btn">
          Logout
        </button>
      </div>
    </div>
  );
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
    <div className="login-prompt">
      <h2>Welcome to Pi App</h2>
      <p>Please log in with your Pi account to continue</p>
      <button onClick={handleLogin}>Login with Pi</button>
    </div>
  );
}
```

### 3. Debug Panel

```tsx
function DebugPanel() {
  const { logs, clearLogs } = useXhiloPiLogs();
  const { isReady, isInitialized } = useXhiloPiReady();
  const { user } = useXhiloPiUser();

  return (
    <div className="debug-panel">
      <h3>Debug Information</h3>
      
      <div className="debug-info">
        <p><strong>SDK Initialized:</strong> {isInitialized ? 'Yes' : 'No'}</p>
        <p><strong>SDK Ready:</strong> {isReady ? 'Yes' : 'No'}</p>
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
        <button onClick={clearLogs}>Clear Logs</button>
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

### 4. Payment Integration

```tsx
function PaymentIntegration() {
  const { createPayment, user } = useXhiloPi();
  const { isReady } = useXhiloPiReady();

  if (!isReady || !user) {
    return <div>Please wait for Pi SDK to initialize and log in</div>;
  }

  const handlePayment = async () => {
    const result = await createPayment(
      {
        amount: 5.0,
        memo: 'Test Payment',
        metadata: { userId: user.uid }
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
    <div className="payment-integration">
      <h3>Payment Integration</h3>
      <p>User: {user.username}</p>
      <button onClick={handlePayment}>Make Payment</button>
    </div>
  );
}
```

### 5. Conditional Rendering

```tsx
function ConditionalContent() {
  const { user } = useXhiloPiUser();
  const { isReady } = useXhiloPiReady();

  // Show loading state
  if (!isReady) {
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
  return (
    <div className="loading-spinner">
      <div className="spinner" />
      <p>Loading Pi SDK...</p>
    </div>
  );
}

function AuthenticatedHeader() {
  const { user, logout } = useXhiloPiUser();

  return (
    <header className="authenticated-header">
      <h1>Welcome, {user.username}!</h1>
      <button onClick={logout}>Logout</button>
    </header>
  );
}

function MainDashboard() {
  const { user } = useXhiloPiUser();

  return (
    <div className="main-dashboard">
      <h2>Dashboard</h2>
      <p>User ID: {user.uid}</p>
      <p>Wallet: {user.walletAddress}</p>
    </div>
  );
}

function UserActions() {
  const { createPayment } = useXhiloPi();

  return (
    <div className="user-actions">
      <h3>Actions</h3>
      <button onClick={() => createPayment(/* ... */)}>
        Make Payment
      </button>
    </div>
  );
}
```

### 6. Error Handling

```tsx
function ErrorHandling() {
  const { initialize } = useXhiloPiReady();
  const [error, setError] = useState(null);

  const handleInitialize = async () => {
    setError(null);
    
    try {
      const result = await initialize();
      if (!result.success) {
        setError(result.message);
      }
    } catch (err) {
      setError(err.message);
    }
  };

  return (
    <div className="error-handling">
      <h3>Error Handling</h3>
      
      {error && (
        <div className="error-message">
          <p>Error: {error}</p>
          <button onClick={() => setError(null)}>Dismiss</button>
        </div>
      )}
      
      <button onClick={handleInitialize}>
        Initialize Pi SDK
      </button>
    </div>
  );
}
```

## 🎨 Styling

### CSS for Provider Components

```css
.user-management {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
}

.user-info {
  background: #f8f9fa;
  padding: 15px;
  border-radius: 4px;
  margin-bottom: 20px;
}

.user-actions {
  display: flex;
  gap: 10px;
}

.logout-btn {
  background: #dc3545;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 4px;
  cursor: pointer;
}

.debug-panel {
  background: #f8f9fa;
  border: 1px solid #dee2e6;
  border-radius: 8px;
  padding: 20px;
  margin: 20px 0;
}

.debug-info {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 10px;
  margin-bottom: 20px;
}

.debug-info p {
  margin: 5px 0;
  padding: 5px;
  background: white;
  border-radius: 4px;
}

.logs-container {
  max-height: 300px;
  overflow-y: auto;
  background: white;
  border: 1px solid #dee2e6;
  border-radius: 4px;
  padding: 10px;
}

.log-entry {
  display: flex;
  gap: 10px;
  padding: 5px 0;
  border-bottom: 1px solid #f1f3f4;
  font-family: monospace;
  font-size: 12px;
}

.log-entry:last-child {
  border-bottom: none;
}

.log-timestamp {
  color: #6c757d;
  min-width: 80px;
}

.log-type {
  font-weight: bold;
  min-width: 60px;
}

.log-type.log-LOG { color: #007bff; }
.log-type.log-WARN { color: #ffc107; }
.log-type.log-ERROR { color: #dc3545; }
.log-type.log-SUCCESS { color: #28a745; }

.log-message {
  flex: 1;
  word-break: break-word;
}

.loading-spinner {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 200px;
}

.spinner {
  width: 40px;
  height: 40px;
  border: 4px solid #f3f3f3;
  border-top: 4px solid #3498db;
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin-bottom: 20px;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.error-message {
  background: #f8d7da;
  color: #721c24;
  border: 1px solid #f5c6cb;
  border-radius: 4px;
  padding: 10px;
  margin: 10px 0;
}
```

## ⚠️ Important Notes

1. **Provider Required** - All provider hooks must be used within `XhiloPiProvider`
2. **Context Sharing** - All components share the same Pi state
3. **Performance** - Re-renders when Pi state changes
4. **Error Handling** - Handle cases where context is not available
5. **Type Safety** - All hooks provide full TypeScript support

## 🔗 Related Documentation

- [useXhiloPi](./useXhiloPi.md) - Full Pi Network context access
- [useXhiloPiUser](./useXhiloPiUser.md) - User information and logout
- [useXhiloPiReady](./useXhiloPiReady.md) - SDK initialization status
- [useXhiloPiLogs](./useXhiloPiLogs.md) - Console logs for debugging
- [useXhiloPiNetwork](./useXhiloPiNetwork.md) - Direct Pi Network functionality
