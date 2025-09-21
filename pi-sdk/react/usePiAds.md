# usePiAds Hook

Dedicated hook for **Pi Network ads functionality** with backend verification, eligibility checking, and reward processing for both interstitial and rewarded ads.

## 📋 Overview

`usePiAds` provides a comprehensive solution for integrating Pi Network ads into your application, including:

- **Ad Display**: Show interstitial and rewarded ads
- **Backend Verification**: Verify rewarded ads and process rewards
- **Eligibility Checking**: Check if users can watch ads and receive rewards
- **Statistics**: Track ad viewing patterns and rewards

## 🚀 Basic Usage

```tsx
import { usePiAds } from '@xhilo/pi-sdk';

function AdComponent() {
  const { 
    showAd, 
    verifyRewardedAd, 
    checkEligibility, 
    isProcessing, 
    error 
  } = usePiAds();

  const handleShowRewardedAd = async () => {
    // Check eligibility first
    const eligibility = await checkEligibility('rewarded', 5);
    if (!eligibility.success || !eligibility.data?.eligible) {
      alert('You have reached your daily ad limit');
      return;
    }

    // Show the rewarded ad
    const result = await showAd('rewarded');
    if (result.success && result.data?.result === 'AD_REWARDED') {
      // Verify the ad and process reward
      const verification = await verifyRewardedAd(
        result.data.adId!, 
        0.1, // 0.1 Pi reward
        { source: 'main_menu' }
      );
      
      if (verification.success) {
        alert(`You earned ${verification.data?.rewardAmount} Pi!`);
      }
    }
  };

  return (
    <div>
      <button onClick={handleShowRewardedAd} disabled={isProcessing}>
        {isProcessing ? 'Loading...' : 'Watch Ad for Pi'}
      </button>
      {error && <div style={{ color: 'red' }}>Error: {error}</div>}
    </div>
  );
}
```

## 🔧 Parameters

### `UsePiAdsReturn`

```tsx
interface UsePiAdsReturn {
  // Ad display methods
  showAd: (adType: 'interstitial' | 'rewarded') => Promise<PiOperationResult<ShowAdResponse>>;
  isAdReady: (adType: 'interstitial' | 'rewarded') => Promise<PiOperationResult<IsAdReadyResponse>>;
  requestAd: (adType: 'interstitial' | 'rewarded') => Promise<PiOperationResult<RequestAdResponse>>;
  
  // Rewarded ad verification
  verifyRewardedAd: (adId: string, rewardAmount?: number, metadata?: Record<string, any>) => Promise<PiOperationResult<AdRewardData>>;
  
  // Eligibility checking
  checkEligibility: (adType: 'interstitial' | 'rewarded', maxDailyViews?: number) => Promise<PiOperationResult<AdEligibilityData>>;
  getAdStats: () => Promise<PiOperationResult<any>>;
  
  // State
  isProcessing: boolean;
  lastAdResult: ShowAdResponse | null;
  lastEligibility: AdEligibilityData | null;
  error: string | null;
}
```

### `AdRewardData`

```tsx
interface AdRewardData {
  adId: string;
  rewardAmount: number;
  adType: 'rewarded';
  metadata?: Record<string, any>;
}
```

### `AdEligibilityData`

```tsx
interface AdEligibilityData {
  eligible: boolean;
  viewsToday: number;
  maxViews: number;
  nextEligibleTime?: string;
}
```

## 📤 Return Values

- **`showAd`** - `(adType) => Promise<PiOperationResult<ShowAdResponse>>` - Display an ad
- **`isAdReady`** - `(adType) => Promise<PiOperationResult<IsAdReadyResponse>>` - Check if ad is ready
- **`requestAd`** - `(adType) => Promise<PiOperationResult<RequestAdResponse>>` - Request an ad
- **`verifyRewardedAd`** - `(adId, rewardAmount?, metadata?) => Promise<PiOperationResult<AdRewardData>>` - Verify rewarded ad
- **`checkEligibility`** - `(adType, maxDailyViews?) => Promise<PiOperationResult<AdEligibilityData>>` - Check ad eligibility
- **`getAdStats`** - `() => Promise<PiOperationResult<any>>` - Get ad statistics
- **`isProcessing`** - `boolean` - Whether an ad operation is in progress
- **`lastAdResult`** - `ShowAdResponse | null` - Result of the last ad shown
- **`lastEligibility`** - `AdEligibilityData | null` - Last eligibility check result
- **`error`** - `string | null` - Current error message

## 🎯 When to Use This Hook

### ✅ Use `usePiAds` when:
- You want to **display ads** (interstitial or rewarded)
- You need **rewarded ad verification** with backend processing
- You want to **check user eligibility** for ads
- You need **ad statistics** and tracking
- You want **reward processing** for ad completion
- You're building **monetization features**

### ❌ Don't use `usePiAds` when:
- You only need **basic ad display** without verification
- You don't need **backend integration** for ads
- You're building **simple applications** without monetization

## 📝 Detailed Examples

### 1. Basic Interstitial Ad

```tsx
import { usePiAds } from '@xhilo/pi-sdk';

function InterstitialAdExample() {
  const { showAd, isAdReady, isProcessing } = usePiAds();

  const handleShowInterstitial = async () => {
    // Check if ad is ready
    const ready = await isAdReady('interstitial');
    if (!ready.success || !ready.data?.ready) {
      alert('No ad available right now');
      return;
    }

    // Show the ad
    const result = await showAd('interstitial');
    if (result.success) {
      console.log('Interstitial ad result:', result.data?.result);
    }
  };

  return (
    <button onClick={handleShowInterstitial} disabled={isProcessing}>
      {isProcessing ? 'Loading Ad...' : 'Show Interstitial Ad'}
    </button>
  );
}
```

### 2. Rewarded Ad with Verification

```tsx
import { usePiAds } from '@xhilo/pi-sdk';

function RewardedAdExample() {
  const { 
    showAd, 
    verifyRewardedAd, 
    checkEligibility, 
    isProcessing, 
    error 
  } = usePiAds();

  const handleRewardedAd = async () => {
    try {
      // Check eligibility
      const eligibility = await checkEligibility('rewarded', 3);
      if (!eligibility.success || !eligibility.data?.eligible) {
        alert(`You've reached your daily limit (${eligibility.data?.viewsToday}/${eligibility.data?.maxViews})`);
        return;
      }

      // Show rewarded ad
      const adResult = await showAd('rewarded');
      if (!adResult.success) {
        throw new Error(adResult.message);
      }

      // Handle different ad results
      switch (adResult.data?.result) {
        case 'AD_REWARDED':
          // User completed the ad, verify and reward
          const verification = await verifyRewardedAd(
            adResult.data.adId!,
            0.2, // 0.2 Pi reward
            { 
              source: 'level_completion',
              level: 5,
              timestamp: new Date().toISOString()
            }
          );

          if (verification.success) {
            alert(`🎉 You earned ${verification.data?.rewardAmount} Pi!`);
          } else {
            alert('Ad completed but reward verification failed');
          }
          break;

        case 'AD_CLOSED':
          alert('Ad was closed before completion');
          break;

        case 'AD_DISPLAY_ERROR':
        case 'AD_NETWORK_ERROR':
        case 'AD_NOT_AVAILABLE':
          alert('Ad error: ' + adResult.data?.result);
          break;

        case 'USER_UNAUTHENTICATED':
          alert('Please log in to watch ads');
          break;
      }
    } catch (err: any) {
      console.error('Rewarded ad error:', err);
      alert('Error: ' + err.message);
    }
  };

  return (
    <div>
      <button onClick={handleRewardedAd} disabled={isProcessing}>
        {isProcessing ? 'Processing...' : 'Watch Ad for 0.2 Pi'}
      </button>
      {error && <div style={{ color: 'red' }}>Error: {error}</div>}
    </div>
  );
}
```

### 3. Ad Statistics Dashboard

```tsx
import { usePiAds } from '@xhilo/pi-sdk';
import { useState, useEffect } from 'react';

function AdStatsDashboard() {
  const { getAdStats, checkEligibility } = usePiAds();
  const [stats, setStats] = useState(null);
  const [eligibility, setEligibility] = useState(null);

  useEffect(() => {
    loadStats();
  }, []);

  const loadStats = async () => {
    const statsResult = await getAdStats();
    if (statsResult.success) {
      setStats(statsResult.data);
    }

    const eligibilityResult = await checkEligibility('rewarded');
    if (eligibilityResult.success) {
      setEligibility(eligibilityResult.data);
    }
  };

  return (
    <div className="ad-stats-dashboard">
      <h3>Ad Statistics</h3>
      
      {stats && (
        <div className="stats-grid">
          <div className="stat-card">
            <h4>Today</h4>
            <p>Interstitial: {stats.today.interstitial}</p>
            <p>Rewarded: {stats.today.rewarded}</p>
          </div>
          
          <div className="stat-card">
            <h4>This Week</h4>
            <p>Interstitial: {stats.thisWeek.interstitial}</p>
            <p>Rewarded: {stats.thisWeek.rewarded}</p>
          </div>
          
          <div className="stat-card">
            <h4>This Month</h4>
            <p>Interstitial: {stats.thisMonth.interstitial}</p>
            <p>Rewarded: {stats.thisMonth.rewarded}</p>
          </div>
        </div>
      )}

      {eligibility && (
        <div className="eligibility-status">
          <h4>Ad Eligibility</h4>
          <p>Status: {eligibility.eligible ? '✅ Eligible' : '❌ Not Eligible'}</p>
          <p>Views Today: {eligibility.viewsToday}/{eligibility.maxViews}</p>
          {eligibility.nextEligibleTime && (
            <p>Next Available: {new Date(eligibility.nextEligibleTime).toLocaleString()}</p>
          )}
        </div>
      )}

      <button onClick={loadStats}>Refresh Stats</button>
    </div>
  );
}
```

### 4. Ad Manager Component

```tsx
import { usePiAds } from '@xhilo/pi-sdk';
import { useState } from 'react';

function AdManager() {
  const { 
    showAd, 
    isAdReady, 
    requestAd, 
    verifyRewardedAd, 
    checkEligibility,
    isProcessing 
  } = usePiAds();

  const [adType, setAdType] = useState<'interstitial' | 'rewarded'>('rewarded');
  const [rewardAmount, setRewardAmount] = useState(0.1);

  const handleAdFlow = async () => {
    try {
      // 1. Request ad
      console.log('Requesting ad...');
      const requestResult = await requestAd(adType);
      if (!requestResult.success) {
        throw new Error(requestResult.message);
      }

      // 2. Check if ready
      console.log('Checking if ad is ready...');
      const readyResult = await isAdReady(adType);
      if (!readyResult.success || !readyResult.data?.ready) {
        throw new Error('Ad not ready');
      }

      // 3. Show ad
      console.log('Showing ad...');
      const showResult = await showAd(adType);
      if (!showResult.success) {
        throw new Error(showResult.message);
      }

      // 4. Handle result
      if (adType === 'rewarded' && showResult.data?.result === 'AD_REWARDED') {
        console.log('Verifying rewarded ad...');
        const verifyResult = await verifyRewardedAd(
          showResult.data.adId!,
          rewardAmount,
          { source: 'ad_manager' }
        );
        
        if (verifyResult.success) {
          alert(`Reward processed: ${verifyResult.data?.rewardAmount} Pi`);
        }
      }
    } catch (error: any) {
      console.error('Ad flow error:', error);
      alert('Error: ' + error.message);
    }
  };

  return (
    <div className="ad-manager">
      <h3>Ad Manager</h3>
      
      <div className="controls">
        <label>
          Ad Type:
          <select value={adType} onChange={(e) => setAdType(e.target.value as any)}>
            <option value="interstitial">Interstitial</option>
            <option value="rewarded">Rewarded</option>
          </select>
        </label>

        {adType === 'rewarded' && (
          <label>
            Reward Amount:
            <input 
              type="number" 
              value={rewardAmount} 
              onChange={(e) => setRewardAmount(parseFloat(e.target.value))}
              step="0.1"
              min="0.1"
            />
          </label>
        )}
      </div>

      <button onClick={handleAdFlow} disabled={isProcessing}>
        {isProcessing ? 'Processing...' : 'Start Ad Flow'}
      </button>
    </div>
  );
}
```

## 🔧 Backend API Requirements

Your backend needs to implement these endpoints using the **Pi Platform API**:

### POST `/api/ads/verify-rewarded`
```json
{
  "adId": "string",
  "userId": "string",
  "adType": "rewarded",
  "rewardAmount": 0.1,
  "metadata": {}
}
```

**Response:**
```json
{
  "success": true,
  "message": "Rewarded ad verified successfully",
  "data": {
    "verified": true,
    "rewardAmount": 0.1,
    "adId": "ad_123",
    "adStatus": {
      "identifier": "ad_123",
      "mediator_ack_status": "granted",
      "mediator_granted_at": "2024-01-01T12:00:00.000Z",
      "mediator_revoked_at": null
    }
  }
}
```

### POST `/api/ads/eligibility`
```json
{
  "userId": "string",
  "adType": "rewarded",
  "maxDailyViews": 5
}
```

**Response:**
```json
{
  "success": true,
  "message": "User is eligible to watch rewarded ads",
  "data": {
    "eligible": true,
    "viewsToday": 2,
    "maxViews": 5,
    "nextEligibleTime": "2024-01-02T00:00:00.000Z"
  }
}
```

### GET `/api/ads/stats?userId=string`

**Response:**
```json
{
  "success": true,
  "message": "Ad statistics retrieved successfully",
  "data": {
    "today": {
      "interstitial": 3,
      "rewarded": 2
    },
    "thisWeek": {
      "interstitial": 15,
      "rewarded": 8
    },
    "thisMonth": {
      "interstitial": 60,
      "rewarded": 25
    }
  }
}
```

## ⚠️ Important Notes

1. **Backend Integration Required** - Rewarded ad verification requires backend API endpoints
2. **User Authentication** - Ads require user authentication for eligibility checking
3. **Reward Processing** - Rewarded ads create A2U payments for rewards
4. **Rate Limiting** - Implement daily limits to prevent abuse
5. **Error Handling** - Always handle ad display errors gracefully
6. **Testing** - Test with sandbox mode before production

## 🔗 Related Hooks

- [`useXhiloPiNetwork`](./useXhiloPiNetwork.md) - Core Pi Network functionality
- [`usePiPayments`](./usePiPayments.md) - Payment processing
- [`usePiSimplePayments`](./usePiSimplePayments.md) - Simple payments

## 📚 Related Documentation

- [Pi Platform Ads Documentation](https://developers.minepi.com/ads)
- [Backend Ads Actions](../backend/ads.md) - Backend ad verification
- [Security Guide](./security.md) - Access token validation
