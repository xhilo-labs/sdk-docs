# usePiAds Hook

Advanced ads functionality with backend verification and reward processing.

## 🚀 Basic Usage

```tsx
import { usePiAds } from '@xhilo/pi-sdk/react';

function AdsComponent() {
  const { 
    showAd, 
    verifyRewardedAd, 
    checkEligibility, 
    getAdStats,
    isProcessing,
    error 
  } = usePiAds();
  
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
const ads = usePiAds();

// Sandbox mode for testing
const ads = usePiAds(true);

// With allowed origins
const ads = usePiAds(false, ['https://myapp.com']);
```

## 📤 Return Values

### Core State
- **`isProcessing`** - `boolean` - Whether ad operation is processing
- **`lastAdResult`** - `any` - Result of the last ad operation
- **`lastEligibility`** - `any` - Result of the last eligibility check
- **`error`** - `string | null` - Current error message

### Ad Methods
- **`showAd(adType)`** - Show an ad (interstitial or rewarded)
- **`isAdReady(adType)`** - Check if ad is ready to show
- **`requestAd(adType)`** - Request an ad to be loaded

### Verification Methods
- **`verifyRewardedAd(adId, rewardAmount?, metadata?)`** - Verify rewarded ad completion
- **`checkEligibility(adType, maxDailyViews?)`** - Check user eligibility for ads
- **`getAdStats()`** - Get user ad statistics

## 📝 Detailed Examples

### 1. Basic Ad Display

```tsx
function BasicAdComponent() {
  const { showAd, isProcessing, error } = usePiAds();
  
  const handleShowInterstitial = async () => {
    const result = await showAd('interstitial');
    if (result.success) {
      console.log('Interstitial ad shown');
    } else {
      console.error('Failed to show ad:', result.message);
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

### 2. Ad with Eligibility Check

```tsx
function EligibilityAdComponent() {
  const { 
    showAd, 
    checkEligibility, 
    isProcessing, 
    error 
  } = usePiAds();
  
  const [isEligible, setIsEligible] = useState(false);
  
  const checkUserEligibility = async () => {
    const result = await checkEligibility('rewarded', 5); // Max 5 ads per day
    setIsEligible(result.success);
  };
  
  const handleShowAd = async () => {
    if (!isEligible) {
      await checkUserEligibility();
      return;
    }
    
    const result = await showAd('rewarded');
    if (result.success) {
      console.log('Rewarded ad shown');
      // Check eligibility again after showing ad
      await checkUserEligibility();
    }
  };
  
  return (
    <div>
      <button onClick={handleShowAd} disabled={isProcessing || !isEligible}>
        {isEligible ? 'Watch Ad' : 'Check Eligibility'}
      </button>
      {error && <p className="error">Error: {error}</p>}
    </div>
  );
}
```

### 3. Rewarded Ad with Verification

```tsx
function RewardedAdComponent() {
  const { 
    showAd, 
    verifyRewardedAd, 
    isProcessing, 
    error 
  } = usePiAds();
  
  const handleRewardedAd = async () => {
    const result = await showAd('rewarded');
    if (result.success) {
      // Verify the ad completion with backend
      const verification = await verifyRewardedAd(
        result.adId, 
        10, // Reward amount
        { source: 'game', level: 5 } // Metadata
      );
      
      if (verification.success) {
        console.log('Ad verified, user rewarded!');
      } else {
        console.error('Ad verification failed:', verification.message);
      }
    }
  };
  
  return (
    <button onClick={handleRewardedAd} disabled={isProcessing}>
      {isProcessing ? 'Processing...' : 'Watch Ad for Rewards'}
    </button>
  );
}
```

### 4. Ad Statistics

```tsx
function AdStatsComponent() {
  const { getAdStats, isProcessing } = usePiAds();
  const [stats, setStats] = useState(null);
  
  const loadStats = async () => {
    const result = await getAdStats();
    if (result.success) {
      setStats(result.data);
    }
  };
  
  useEffect(() => {
    loadStats();
  }, []);
  
  return (
    <div>
      <button onClick={loadStats} disabled={isProcessing}>
        Refresh Stats
      </button>
      {stats && (
        <div>
          <h3>Ad Statistics</h3>
          <p>Total Ads Watched: {stats.totalAds}</p>
          <p>Rewards Earned: {stats.totalRewards}</p>
          <p>Today's Ads: {stats.todayAds}</p>
        </div>
      )}
    </div>
  );
}
```

## 🎯 When to Use This Hook

### ✅ Use `usePiAds` when:
- You need **full ads functionality** with backend verification
- You want to **verify rewarded ads** on the server
- You need **eligibility checking** and **ad statistics**
- You're building a **production app** with ads

### ❌ Don't use `usePiAds` when:
- You only need **simple ad display** (use `usePiAdsSimple`)
- You don't have a **backend** for verification
- You're just **prototyping** or testing

## 🔧 Configuration

### Backend Integration
```tsx
// The hook automatically handles backend verification
// Make sure your backend has the ads verification endpoints:
// - POST /api/ads/verify-rewarded
// - GET /api/ads/eligibility
// - GET /api/ads/stats
```

### Error Handling
```tsx
function AdWithErrorHandling() {
  const { showAd, error, isProcessing } = usePiAds();
  
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
- ✅ **Full Backend Integration**: Ready for production ads verification
- ✅ **TypeScript Support**: Complete type safety
- ✅ **Error Handling**: Comprehensive error management

## 🔗 Related Documentation

- [usePiAdsSimple](./usePiAdsSimple.md) - Simple ads without backend
- [Backend Ads Actions](../backend/README.md#ads-verification) - Server-side verification
- [Components](./components.md) - UI components for ads
