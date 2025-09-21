# useXhiloPiUser Hook

A specialized hook that provides access to the current authenticated user from the Pi Network context.

## 📋 Overview

`useXhiloPiUser` is a focused hook that only provides user-related information and functions. It's useful when you only need user data without the full Pi Network functionality.

## 🚀 Basic Usage

```tsx
import { useXhiloPiUser } from '@xhilo/pi-sdk';

function UserComponent() {
  const { user, isAuthenticated, logout } = useXhiloPiUser();

  if (!isAuthenticated) {
    return <div>Please log in</div>;
  }

  return (
    <div>
      <h2>Welcome, {user.username}!</h2>
      <p>Wallet: {user.walletAddress}</p>
      <button onClick={logout}>Logout</button>
    </div>
  );
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

- **`user`** - `PiUser | null` - Current authenticated user
- **`isAuthenticated`** - `boolean` - Whether user is authenticated
- **`logout`** - `() => Promise<PiOperationResult>` - Logout function

### `PiUser` Interface

```tsx
interface PiUser {
  uid: string;              // User ID
  username: string;         // Username
  walletAddress: string;    // Wallet address
  accessToken: string;      // Access token
}
```

## 🎯 When to Use This Hook

### ✅ Use `useXhiloPiUser` when:
- You only need **user information** and logout functionality
- You want to **avoid importing** the full Pi Network hook
- You're building **user-specific components**
- You need **lightweight user management**
- You want **focused user functionality**

### ❌ Don't use `useXhiloPiUser` when:
- You need **payment functionality** (use `useXhiloPi` or `useXhiloPiNetwork`)
- You need **ad functionality** (use `useXhiloPi` or `useXhiloPiNetwork`)
- You need **full Pi Network features** (use `useXhiloPi` or `useXhiloPiNetwork`)

## 📝 Detailed Examples

### 1. User Profile Display

```tsx
function UserProfile() {
  const { user, isAuthenticated, logout } = useXhiloPiUser();

  if (!isAuthenticated) {
    return <div>Please log in to view your profile</div>;
  }

  return (
    <div className="user-profile">
      <div className="profile-header">
        <h2>User Profile</h2>
        <button onClick={logout} className="logout-btn">
          Logout
        </button>
      </div>
      
      <div className="profile-info">
        <div className="info-item">
          <label>Username:</label>
          <span>{user.username}</span>
        </div>
        
        <div className="info-item">
          <label>User ID:</label>
          <span>{user.uid}</span>
        </div>
        
        <div className="info-item">
          <label>Wallet Address:</label>
          <span className="wallet-address">{user.walletAddress}</span>
        </div>
      </div>
    </div>
  );
}
```

### 2. User Menu Component

```tsx
function UserMenu() {
  const { user, isAuthenticated, logout } = useXhiloPiUser();
  const [isOpen, setIsOpen] = useState(false);

  if (!isAuthenticated) {
    return <LoginButton />;
  }

  return (
    <div className="user-menu">
      <button 
        className="user-menu-trigger"
        onClick={() => setIsOpen(!isOpen)}
      >
        <span className="user-avatar">
          {user.username.charAt(0).toUpperCase()}
        </span>
        <span className="username">{user.username}</span>
        <span className="dropdown-arrow">▼</span>
      </button>
      
      {isOpen && (
        <div className="user-menu-dropdown">
          <div className="user-info">
            <p className="username">{user.username}</p>
            <p className="user-id">ID: {user.uid}</p>
          </div>
          
          <div className="menu-actions">
            <button onClick={() => {
              setIsOpen(false);
              // Navigate to profile
            }}>
              View Profile
            </button>
            
            <button onClick={() => {
              setIsOpen(false);
              // Navigate to settings
            }}>
              Settings
            </button>
            
            <button 
              onClick={() => {
                setIsOpen(false);
                logout();
              }}
              className="logout-btn"
            >
              Logout
            </button>
          </div>
        </div>
      )}
    </div>
  );
}
```

### 3. Conditional Content Based on User

```tsx
function ConditionalContent() {
  const { user, isAuthenticated } = useXhiloPiUser();

  return (
    <div>
      {isAuthenticated ? (
        <AuthenticatedContent user={user} />
      ) : (
        <UnauthenticatedContent />
      )}
    </div>
  );
}

function AuthenticatedContent({ user }) {
  return (
    <div>
      <h2>Welcome back, {user.username}!</h2>
      <p>You're logged in and ready to use the app.</p>
      <UserDashboard />
    </div>
  );
}

function UnauthenticatedContent() {
  return (
    <div>
      <h2>Welcome to Pi App</h2>
      <p>Please log in to access your account.</p>
      <LoginForm />
    </div>
  );
}
```

### 4. User-Specific Features

```tsx
function UserFeatures() {
  const { user, isAuthenticated } = useXhiloPiUser();

  if (!isAuthenticated) {
    return <div>Please log in to access features</div>;
  }

  return (
    <div className="user-features">
      <h3>Your Features</h3>
      
      <div className="feature-card">
        <h4>Personal Dashboard</h4>
        <p>View your personal statistics and activity</p>
        <button>Open Dashboard</button>
      </div>
      
      <div className="feature-card">
        <h4>Wallet Management</h4>
        <p>Manage your Pi wallet: {user.walletAddress}</p>
        <button>Manage Wallet</button>
      </div>
      
      <div className="feature-card">
        <h4>Account Settings</h4>
        <p>Update your account preferences</p>
        <button>Settings</button>
      </div>
    </div>
  );
}
```

### 5. User Status Indicator

```tsx
function UserStatusIndicator() {
  const { user, isAuthenticated } = useXhiloPiUser();

  return (
    <div className="status-indicator">
      <div className={`status-dot ${isAuthenticated ? 'online' : 'offline'}`} />
      <span className="status-text">
        {isAuthenticated ? `Logged in as ${user.username}` : 'Not logged in'}
      </span>
    </div>
  );
}
```

### 6. User Data Display

```tsx
function UserDataDisplay() {
  const { user, isAuthenticated } = useXhiloPiUser();

  if (!isAuthenticated) {
    return null;
  }

  return (
    <div className="user-data">
      <h3>User Information</h3>
      
      <div className="data-grid">
        <div className="data-item">
          <label>Username:</label>
          <span>{user.username}</span>
        </div>
        
        <div className="data-item">
          <label>User ID:</label>
          <span className="user-id">{user.uid}</span>
        </div>
        
        <div className="data-item">
          <label>Wallet Address:</label>
          <span className="wallet-address">
            {user.walletAddress}
            <button 
              onClick={() => navigator.clipboard.writeText(user.walletAddress)}
              className="copy-btn"
            >
              Copy
            </button>
          </span>
        </div>
        
        <div className="data-item">
          <label>Access Token:</label>
          <span className="access-token">
            {user.accessToken.substring(0, 20)}...
            <button 
              onClick={() => navigator.clipboard.writeText(user.accessToken)}
              className="copy-btn"
            >
              Copy
            </button>
          </span>
        </div>
      </div>
    </div>
  );
}
```

### 7. Logout Confirmation

```tsx
function LogoutButton() {
  const { logout } = useXhiloPiUser();
  const [showConfirm, setShowConfirm] = useState(false);

  const handleLogout = async () => {
    const result = await logout();
    if (result.success) {
      console.log('Logout successful');
      setShowConfirm(false);
    } else {
      console.error('Logout failed:', result.message);
    }
  };

  return (
    <div className="logout-section">
      {showConfirm ? (
        <div className="logout-confirmation">
          <p>Are you sure you want to logout?</p>
          <div className="confirmation-buttons">
            <button 
              onClick={handleLogout}
              className="confirm-btn"
            >
              Yes, Logout
            </button>
            <button 
              onClick={() => setShowConfirm(false)}
              className="cancel-btn"
            >
              Cancel
            </button>
          </div>
        </div>
      ) : (
        <button 
          onClick={() => setShowConfirm(true)}
          className="logout-btn"
        >
          Logout
        </button>
      )}
    </div>
  );
}
```

## 🎨 Styling Examples

### CSS for User Components

```css
.user-profile {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
}

.profile-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.profile-info {
  display: grid;
  gap: 15px;
}

.info-item {
  display: flex;
  justify-content: space-between;
  padding: 10px;
  background: #f8f9fa;
  border-radius: 4px;
}

.wallet-address {
  font-family: monospace;
  font-size: 12px;
  word-break: break-all;
}

.user-menu {
  position: relative;
}

.user-menu-trigger {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: white;
  cursor: pointer;
}

.user-menu-dropdown {
  position: absolute;
  top: 100%;
  right: 0;
  min-width: 200px;
  background: white;
  border: 1px solid #ddd;
  border-radius: 4px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
  z-index: 1000;
}

.status-indicator {
  display: flex;
  align-items: center;
  gap: 8px;
}

.status-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
}

.status-dot.online {
  background: #28a745;
}

.status-dot.offline {
  background: #dc3545;
}
```

## ⚠️ Important Notes

1. **Provider Required** - Must be used within `XhiloPiProvider`
2. **User State** - Automatically updates when user logs in/out
3. **Performance** - Lightweight hook with minimal re-renders
4. **Type Safety** - Full TypeScript support for user data
5. **Error Handling** - Handle cases where user is not authenticated

## 🔗 Related Hooks

- [`useXhiloPi`](./useXhiloPi.md) - Full Pi Network context
- [`useXhiloPiReady`](./useXhiloPiReady.md) - Check if Pi is ready
- [`useXhiloPiLogs`](./useXhiloPiLogs.md) - Access console logs
