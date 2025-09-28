# usePiAdsSimple Hook

A simplified frontend-only hook for displaying Pi Network ads without backend integration. Perfect for basic ad display functionality or when you don't need backend verification.

## 📋 Overview

`usePiAdsSimple` provides a lightweight way to display Pi Network ads without requiring backend integration. This is ideal for:

- **Frontend-only applications** that don't have a backend
- **Simple ad display** without reward processing
- **Testing and development** scenarios
- **Quick prototyping** of ad functionality

## 🚀 Installation

```bash
npm install @xhilo/pi-sdk@0.2.14
```

## 📖 Basic Usage

```tsx
import { usePiAdsSimple } from '@xhilo/pi-sdk';

function MyComponent() {
  const { showAd, isAdLoading, error } = usePiAdsSimple();

  const handleShowAd = async () => {
    const result = await showAd('rewarded');
    
    if (result.success) {
      console.log('Ad displayed successfully');
    } else {
      console.error('Failed to show ad:', result.message);
    }
  };

  return (
    <div>
      <button 
        onClick={handleShowAd}
        disabled={isAdLoading}
      >
        {isAdLoading ? 'Loading...' : 'Watch Ad'}
      </button>
      
      {error && <p>Error: {error}</p>}
    </div>
  );
}
```

## 🎯 API Reference

### **Hook Return Value**

```typescript
interface UsePiAdsSimpleReturn {
  showAd: (adType: 'interstitial' | 'rewarded') => Promise<PiOperationResult<AdResult>>;
  isAdLoading: boolean;
  error: string | null;
}
```

### **Parameters**

#### `showAd(adType)`

Displays a Pi Network ad of the specified type.

**Parameters:**
- `adType` (string): The type of ad to display
  - `'interstitial'` - Full-screen ad between content
  - `'rewarded'` - Ad that provides rewards to users

**Returns:**
- `Promise<PiOperationResult<AdResult>>` - Result of the ad display operation

**Example:**
```tsx
const result = await showAd('rewarded');

if (result.success) {
  console.log('Ad ID:', result.data?.adId);
  console.log('Ad result:', result.data?.result);
} else {
  console.error('Ad failed:', result.message);
}
```

### **State Properties**

#### `isAdLoading`
- **Type:** `boolean`
- **Description:** Indicates whether an ad is currently being loaded or displayed
- **Usage:** Use to show loading states or disable buttons during ad operations

#### `error`
- **Type:** `string | null`
- **Description:** Contains the last error message, if any
- **Usage:** Display error messages to users

## 🎨 Usage Examples

### **Basic Ad Display**

```tsx
import { usePiAdsSimple } from '@xhilo/pi-sdk';

function BasicAdExample() {
  const { showAd, isAdLoading, error } = usePiAdsSimple();

  return (
    <div className="ad-container">
      <h3>Watch an Ad</h3>
      
      <button 
        onClick={() => showAd('rewarded')}
        disabled={isAdLoading}
        className="ad-button"
      >
        {isAdLoading ? 'Loading Ad...' : 'Watch Rewarded Ad'}
      </button>
      
      {error && (
        <div className="error-message">
          Error: {error}
        </div>
      )}
    </div>
  );
}
```

### **Multiple Ad Types**

```tsx
import { usePiAdsSimple } from '@xhilo/pi-sdk';

function MultipleAdsExample() {
  const { showAd, isAdLoading, error } = usePiAdsSimple();

  const handleInterstitialAd = async () => {
    const result = await showAd('interstitial');
    if (result.success) {
      console.log('Interstitial ad shown');
    }
  };

  const handleRewardedAd = async () => {
    const result = await showAd('rewarded');
    if (result.success) {
      console.log('Rewarded ad shown');
    }
  };

  return (
    <div>
      <button 
        onClick={handleInterstitialAd}
        disabled={isAdLoading}
      >
        Show Interstitial Ad
      </button>
      
      <button 
        onClick={handleRewardedAd}
        disabled={isAdLoading}
      >
        Show Rewarded Ad
      </button>
      
      {error && <p>Error: {error}</p>}
    </div>
  );
}
```

### **With Loading States**

```tsx
import { usePiAdsSimple } from '@xhilo/pi-sdk';

function LoadingStateExample() {
  const { showAd, isAdLoading, error } = usePiAdsSimple();

  return (
    <div className="ad-section">
      <h3>Ad Experience</h3>
      
      {isAdLoading ? (
        <div className="loading">
          <div className="spinner"></div>
          <p>Loading ad...</p>
        </div>
      ) : (
        <button 
          onClick={() => showAd('rewarded')}
          className="primary-button"
        >
          Watch Ad for Rewards
        </button>
      )}
      
      {error && (
        <div className="error-banner">
          <p>⚠️ {error}</p>
        </div>
      )}
    </div>
  );
}
```

## 🔄 Ad Result Types

### **AdResult Interface**

```typescript
interface AdResult {
  adId?: string;           // Unique identifier for the ad
  result: 'AD_DISPLAYED' | 'AD_REWARDED' | 'AD_FAILED' | 'AD_CANCELLED';
  message?: string;        // Additional information about the result
}
```

### **Result Values**

- **`AD_DISPLAYED`** - Ad was successfully displayed to the user
- **`AD_REWARDED`** - Ad was completed and user should receive rewards
- **`AD_FAILED`** - Ad failed to load or display
- **`AD_CANCELLED`** - User cancelled the ad before completion

## ⚠️ Important Notes

### **No Backend Integration**
- This hook does **not** verify ads with your backend
- No reward processing or user tracking
- Perfect for frontend-only applications

### **Pi Network SDK Required**
- Requires the Pi Network SDK to be properly initialized
- Make sure to wrap your app with `XhiloPiProvider`

### **Error Handling**
- Always check the `success` property of the result
- Handle errors gracefully with user-friendly messages
- Use the `error` state for displaying error information

## 🔗 Related Hooks

- **[usePiAds](./usePiAds.md)** - Full ads functionality with backend integration
- **[useXhiloPiNetwork](./useXhiloPiNetwork.md)** - Core Pi Network functionality
- **[useXhiloPiReady](./useXhiloPiReady.md)** - SDK initialization status

## 🎯 When to Use

### **Use `usePiAdsSimple` when:**
- ✅ You need basic ad display functionality
- ✅ You don't have a backend for ad verification
- ✅ You're building a frontend-only application
- ✅ You're prototyping or testing ad functionality
- ✅ You want simple, lightweight ad integration

### **Use `usePiAds` when:**
- ✅ You need backend verification of ads
- ✅ You want to process rewards server-side
- ✅ You need user eligibility checking
- ✅ You want comprehensive ad analytics

## 📚 Examples

For complete examples, see:
- **[Components Overview](./components.md)** - UI component examples
- **[Developer Flow](./dev-flow.md)** - Complete implementation guide
- **[Templates](../templates/)** - Full application templates

---

*This hook provides a simple way to integrate Pi Network ads into your React application without backend complexity.*
