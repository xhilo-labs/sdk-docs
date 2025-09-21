# useXhiloPiReady Hook

A specialized hook that provides information about the Pi Network SDK initialization status and readiness.

## 📋 Overview

`useXhiloPiReady` is a focused hook that only provides initialization and readiness information. It's useful when you need to check if the Pi SDK is ready before performing operations.

## 🚀 Basic Usage

```tsx
import { useXhiloPiReady } from '@xhilo/pi-sdk';

function LoadingComponent() {
  const { isReady, isInitialized, initialize } = useXhiloPiReady();

  if (!isInitialized) {
    return (
      <div>
        <p>Initializing Pi SDK...</p>
        <button onClick={initialize}>Initialize Now</button>
      </div>
    );
  }

  if (!isReady) {
    return <div>Pi SDK is initializing, please wait...</div>;
  }

  return <div>Pi SDK is ready!</div>;
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

- **`isReady`** - `boolean` - Whether the Pi SDK is fully ready for use
- **`isInitialized`** - `boolean` - Whether the Pi SDK is initialized
- **`initialize`** - `() => Promise<PiOperationResult>` - Initialize the Pi SDK

## 🎯 When to Use This Hook

### ✅ Use `useXhiloPiReady` when:
- You need to **check SDK readiness** before operations
- You want to **show loading states** during initialization
- You need **initialization control** without full Pi functionality
- You're building **loading components** or **splash screens**
- You want **lightweight readiness checking**

### ❌ Don't use `useXhiloPiReady` when:
- You need **user authentication** (use `useXhiloPiUser`)
- You need **payment functionality** (use `useXhiloPi` or `useXhiloPiNetwork`)
- You need **full Pi Network features** (use `useXhiloPi` or `useXhiloPiNetwork`)

## 📝 Detailed Examples

### 1. Basic Loading Screen

```tsx
function LoadingScreen() {
  const { isReady, isInitialized, initialize } = useXhiloPiReady();

  if (!isInitialized) {
    return (
      <div className="loading-screen">
        <div className="loading-content">
          <h2>Welcome to Pi App</h2>
          <p>Initializing Pi SDK...</p>
          <button onClick={initialize} className="init-btn">
            Initialize Pi SDK
          </button>
        </div>
      </div>
    );
  }

  if (!isReady) {
    return (
      <div className="loading-screen">
        <div className="loading-content">
          <div className="spinner" />
          <p>Pi SDK is initializing, please wait...</p>
        </div>
      </div>
    );
  }

  return <MainApp />;
}
```

### 2. Conditional Rendering Based on Readiness

```tsx
function App() {
  const { isReady, isInitialized } = useXhiloPiReady();

  return (
    <div className="app">
      <Header />
      
      {!isInitialized ? (
        <InitializationPrompt />
      ) : !isReady ? (
        <LoadingState />
      ) : (
        <MainContent />
      )}
      
      <Footer />
    </div>
  );
}

function InitializationPrompt() {
  const { initialize } = useXhiloPiReady();

  return (
    <div className="init-prompt">
      <h2>Pi SDK Not Initialized</h2>
      <p>Please initialize the Pi SDK to continue</p>
      <button onClick={initialize}>Initialize</button>
    </div>
  );
}

function LoadingState() {
  return (
    <div className="loading-state">
      <div className="loading-spinner" />
      <p>Loading Pi SDK...</p>
    </div>
  );
}

function MainContent() {
  return (
    <div className="main-content">
      <h2>Pi SDK Ready!</h2>
      <p>You can now use all Pi Network features</p>
    </div>
  );
}
```

### 3. Status Indicator

```tsx
function StatusIndicator() {
  const { isReady, isInitialized } = useXhiloPiReady();

  const getStatus = () => {
    if (!isInitialized) return { text: 'Not Initialized', color: 'red' };
    if (!isReady) return { text: 'Initializing', color: 'orange' };
    return { text: 'Ready', color: 'green' };
  };

  const status = getStatus();

  return (
    <div className="status-indicator">
      <div 
        className={`status-dot status-${status.color}`}
        title={`Pi SDK Status: ${status.text}`}
      />
      <span className="status-text">{status.text}</span>
    </div>
  );
}
```

### 4. Initialization with Error Handling

```tsx
function InitializationWithErrorHandling() {
  const { isReady, isInitialized, initialize } = useXhiloPiReady();
  const [isInitializing, setIsInitializing] = useState(false);
  const [error, setError] = useState(null);

  const handleInitialize = async () => {
    setIsInitializing(true);
    setError(null);

    try {
      const result = await initialize();
      if (!result.success) {
        setError(result.message);
      }
    } catch (err) {
      setError(err.message);
    } finally {
      setIsInitializing(false);
    }
  };

  if (isReady) {
    return <div>Pi SDK is ready!</div>;
  }

  return (
    <div className="init-container">
      <h2>Initialize Pi SDK</h2>
      
      {error && (
        <div className="error-message">
          <p>Initialization failed: {error}</p>
          <button onClick={() => setError(null)}>Dismiss</button>
        </div>
      )}
      
      <button 
        onClick={handleInitialize}
        disabled={isInitializing || isInitialized}
        className="init-btn"
      >
        {isInitializing ? 'Initializing...' : 'Initialize Pi SDK'}
      </button>
      
      {isInitialized && !isReady && (
        <p>Pi SDK is initializing, please wait...</p>
      )}
    </div>
  );
}
```

### 5. Progress Indicator

```tsx
function ProgressIndicator() {
  const { isReady, isInitialized } = useXhiloPiReady();
  const [progress, setProgress] = useState(0);

  useEffect(() => {
    if (!isInitialized) {
      setProgress(0);
    } else if (!isReady) {
      // Simulate progress
      const interval = setInterval(() => {
        setProgress(prev => {
          if (prev >= 90) return prev;
          return prev + 10;
        });
      }, 200);

      return () => clearInterval(interval);
    } else {
      setProgress(100);
    }
  }, [isInitialized, isReady]);

  if (isReady) {
    return <div>Pi SDK is ready!</div>;
  }

  return (
    <div className="progress-container">
      <h3>Initializing Pi SDK</h3>
      
      <div className="progress-bar">
        <div 
          className="progress-fill"
          style={{ width: `${progress}%` }}
        />
      </div>
      
      <p className="progress-text">
        {isInitialized ? 'Almost ready...' : 'Starting initialization...'}
      </p>
      
      <div className="progress-percentage">
        {progress}%
      </div>
    </div>
  );
}
```

### 6. Retry Initialization

```tsx
function RetryInitialization() {
  const { isReady, isInitialized, initialize } = useXhiloPiReady();
  const [retryCount, setRetryCount] = useState(0);
  const [lastError, setLastError] = useState(null);

  const handleRetry = async () => {
    setRetryCount(prev => prev + 1);
    setLastError(null);

    const result = await initialize();
    if (!result.success) {
      setLastError(result.message);
    }
  };

  if (isReady) {
    return <div>Pi SDK is ready!</div>;
  }

  return (
    <div className="retry-container">
      <h2>Pi SDK Initialization</h2>
      
      {lastError && (
        <div className="error-message">
          <p>Error: {lastError}</p>
        </div>
      )}
      
      <div className="retry-info">
        <p>Retry attempts: {retryCount}</p>
        <p>Status: {isInitialized ? 'Initialized' : 'Not Initialized'}</p>
      </div>
      
      <button 
        onClick={handleRetry}
        disabled={isInitialized && !isReady}
        className="retry-btn"
      >
        {isInitialized ? 'Retry' : 'Initialize'}
      </button>
      
      {retryCount > 3 && (
        <p className="help-text">
          Having trouble? Please refresh the page or contact support.
        </p>
      )}
    </div>
  );
}
```

### 7. Component with Readiness Check

```tsx
function ComponentWithReadinessCheck() {
  const { isReady, isInitialized } = useXhiloPiReady();

  // Don't render the component until Pi SDK is ready
  if (!isReady) {
    return (
      <div className="loading-placeholder">
        <div className="spinner" />
        <p>Loading Pi features...</p>
      </div>
    );
  }

  return (
    <div className="pi-features">
      <h3>Pi Network Features</h3>
      <p>Pi SDK is ready! You can now use all features.</p>
      {/* Your Pi-related components here */}
    </div>
  );
}
```

## 🎨 Styling Examples

### CSS for Loading Components

```css
.loading-screen {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: #f8f9fa;
}

.loading-content {
  text-align: center;
  padding: 40px;
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

.spinner {
  width: 40px;
  height: 40px;
  border: 4px solid #f3f3f3;
  border-top: 4px solid #3498db;
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin: 0 auto 20px;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.status-indicator {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 12px;
  background: #f8f9fa;
  border-radius: 4px;
}

.status-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
}

.status-dot.status-red {
  background: #dc3545;
}

.status-dot.status-orange {
  background: #fd7e14;
}

.status-dot.status-green {
  background: #28a745;
}

.progress-container {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
}

.progress-bar {
  width: 100%;
  height: 8px;
  background: #e9ecef;
  border-radius: 4px;
  overflow: hidden;
  margin: 20px 0;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #3498db, #2ecc71);
  transition: width 0.3s ease;
}

.progress-text {
  text-align: center;
  margin: 10px 0;
  color: #666;
}

.progress-percentage {
  text-align: center;
  font-weight: bold;
  color: #3498db;
}
```

## ⚠️ Important Notes

1. **Provider Required** - Must be used within `XhiloPiProvider`
2. **Readiness States** - `isInitialized` and `isReady` are different states
3. **Performance** - Lightweight hook with minimal re-renders
4. **Error Handling** - Handle initialization errors gracefully
5. **User Experience** - Provide clear feedback during initialization

## 🔗 Related Hooks

- [`useXhiloPi`](./useXhiloPi.md) - Full Pi Network context
- [`useXhiloPiUser`](./useXhiloPiUser.md) - User information
- [`useXhiloPiLogs`](./useXhiloPiLogs.md) - Access console logs
