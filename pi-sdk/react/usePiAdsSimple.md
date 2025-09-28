# usePiAdsSimple Hook

Simple frontend-only ads display without backend requirements.

## 🚀 Basic Usage

```tsx
import { usePiAdsSimple } from '@xhilo/pi-sdk/react';

function SimpleAdsComponent() {
  const { showAd, isProcessing, error } = usePiAdsSimple();
  
  const handleShowAd = async () => {
    const result = await showAd('rewarded');
    if (result.success) {
      console.log('Ad shown successfully');
    }
  };
  
  return (
    <button onClick={handleShowAd} disabled={isProcessing}>
      {isProcessing ? 'Loading...' : 'Watch Ad'}
    </button>
  );
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
const ads = usePiAdsSimple();

// Sandbox mode for testing
const ads = usePiAdsSimple(true);

// With allowed origins
const ads = usePiAdsSimple(false, ['https://myapp.com']);
```

## 📤 Return Values

### Core State
- **`isProcessing`** - `boolean` - Whether ad operation is processing
- **`lastAdResult`** - `any` - Result of the last ad operation
- **`error`** - `string | null` - Current error message

### Ad Methods
- **`showAd(adType)`** - Show an ad (interstitial or rewarded)
- **`isAdReady(adType)`** - Check if ad is ready to show
- **`requestAd(adType)`** - Request an ad to be loaded

## 📝 Detailed Examples

### 1. Basic Ad Display

```tsx
function BasicAdComponent() {
  const { showAd, isProcessing, error } = usePiAdsSimple();
  
  const handleShowInterstitial = async () => {
    const result = await showAd('interstitial');
    if (result.success) {
      console.log('Interstitial ad shown');
    }
  };
  
  const handleShowRewarded = async () => {
    const result = await showAd('rewarded');
    if (result.success) {
      console.log('Rewarded ad shown');
    }
  };
  
  return (
    <div>
      <button onClick={handleShowInterstitial} disabled={isProcessing}>
        Show Interstitial
      </button>
      <button onClick={handleShowRewarded} disabled={isProcessing}>
        Show Rewarded Ad
      </button>
      {error && <p className="error">Error: {error}</p>}
    </div>
  );
}
```

### 2. Ad Ready Check

```tsx
function AdReadyComponent() {
  const { showAd, isAdReady, isProcessing } = usePiAdsSimple();
  const [adReady, setAdReady] = useState(false);
  
  const checkAdReady = async () => {
    const result = await isAdReady('rewarded');
    setAdReady(result.success);
  };
  
  const handleShowAd = async () => {
    if (!adReady) {
      await checkAdReady();
      return;
    }
    
    const result = await showAd('rewarded');
    if (result.success) {
      console.log('Ad shown successfully');
    }
  };
  
  return (
    <div>
      <button onClick={checkAdReady}>
        Check Ad Ready
      </button>
      <button onClick={handleShowAd} disabled={isProcessing || !adReady}>
        {adReady ? 'Show Ad' : 'Ad Not Ready'}
      </button>
    </div>
  );
}
```

### 3. Request Ad First

```tsx
function RequestAdComponent() {
  const { requestAd, showAd, isProcessing } = usePiAdsSimple();
  const [adRequested, setAdRequested] = useState(false);
  
  const handleRequestAd = async () => {
    const result = await requestAd('rewarded');
    if (result.success) {
      setAdRequested(true);
      console.log('Ad requested successfully');
    }
  };
  
  const handleShowAd = async () => {
    if (!adRequested) {
      await handleRequestAd();
      return;
    }
    
    const result = await showAd('rewarded');
    if (result.success) {
      console.log('Ad shown successfully');
      setAdRequested(false); // Reset for next ad
    }
  };
  
  return (
    <div>
      <button onClick={handleRequestAd} disabled={isProcessing}>
        Request Ad
      </button>
      <button onClick={handleShowAd} disabled={isProcessing}>
        {adRequested ? 'Show Ad' : 'Request Ad First'}
      </button>
    </div>
  );
}
```

### 4. Error Handling

```tsx
function AdWithErrorHandling() {
  const { showAd, error, isProcessing } = usePiAdsSimple();
  
  const handleShowAd = async () => {
    try {
      const result = await showAd('rewarded');
      if (!result.success) {
        console.error('Ad failed:', result.message);
        // Handle ad failure
      }
    } catch (err) {
      console.error('Unexpected error:', err);
      // Handle unexpected errors
    }
  };
  
  return (
    <div>
      <button onClick={handleShowAd} disabled={isProcessing}>
        Show Ad
      </button>
      {error && (
        <div className="error-message">
          <p>Ad Error: {error}</p>
          <button onClick={() => window.location.reload()}>
            Retry
          </button>
        </div>
      )}
    </div>
  );
}
```

## 🎯 When to Use This Hook

### ✅ Use `usePiAdsSimple` when:
- You need **simple ad display** without backend verification
- You're **prototyping** or **testing** ads functionality
- You don't have a **backend** for ads verification
- You want **minimal setup** for ads

### ❌ Don't use `usePiAdsSimple` when:
- You need **backend verification** for rewarded ads
- You want **eligibility checking** and **ad statistics**
- You're building a **production app** with ads (use `usePiAds`)

## 🔧 Configuration

### No Backend Required
```tsx
// This hook works without any backend configuration
// Perfect for frontend-only implementations
const ads = usePiAdsSimple();
```

### Error Handling
```tsx
function AdWithErrorHandling() {
  const { showAd, error, isProcessing } = usePiAdsSimple();
  
  const handleShowAd = async () => {
    try {
      const result = await showAd('rewarded');
      if (!result.success) {
        console.error('Ad failed:', result.message);
        // Handle ad failure
      }
    } catch (err) {
      console.error('Unexpected error:', err);
      // Handle unexpected errors
    }
  };
  
  return (
    <div>
      <button onClick={handleShowAd} disabled={isProcessing}>
        Show Ad
      </button>
      {error && (
        <div className="error-message">
          <p>Ad Error: {error}</p>
          <button onClick={() => window.location.reload()}>
            Retry
          </button>
        </div>
      )}
    </div>
  );
}
```

## 🆕 What's New in v0.2.14

- ✅ **Temporary Stub Implementation**: Currently returns stub responses
- ✅ **Frontend-Only**: No backend configuration required
- ✅ **TypeScript Support**: Complete type safety
- ✅ **Error Handling**: Comprehensive error management

## 🔗 Related Documentation

- [usePiAds](./usePiAds.md) - Full ads with backend verification
- [Components](./components.md) - UI components for ads
- [Backend Ads Actions](../backend/README.md#ads-verification) - Server-side verification
