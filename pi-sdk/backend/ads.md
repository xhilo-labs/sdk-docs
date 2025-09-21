# Ads Backend Actions

Backend actions for **Pi Network ads functionality** including rewarded ad verification, eligibility checking, and reward processing.

## 📋 Overview

The ads backend provides server-side functionality using the **Pi Platform API**:

- **Rewarded Ad Verification** - Verify ad completion using Pi Platform API (`GET /ads_network/status/:adId`)
- **Eligibility Checking** - Check if users can watch ads and receive rewards
- **Statistics Tracking** - Track ad viewing patterns and rewards
- **Reward Processing** - Create A2U payments for ad rewards

## 🚀 Available Actions

## 🔧 Customizable Functions

The ads system allows developers to customize business logic by providing custom functions:

```typescript
interface AdsCustomFunctions {
  checkUserAdEligibility?: (userId: string, adType: 'interstitial' | 'rewarded') => Promise<{ eligible: boolean; message: string }>;
  getUserAdViewsToday?: (userId: string, adType: 'interstitial' | 'rewarded', date: string) => Promise<number>;
  getUserAdViewsInPeriod?: (userId: string, adType: 'interstitial' | 'rewarded', startDate: string, endDate: string) => Promise<number>;
  logAdCompletion?: (userId: string, adId: string, adType: 'interstitial' | 'rewarded', rewardAmount: number, metadata: Record<string, any>, adStatus?: RewardedAdStatusDTO) => Promise<void>;
}
```

### Example Custom Implementation

```typescript
import { verifyRewardedAdAction, AdsCustomFunctions } from '@xhilo/pi-sdk/backend';

const customFunctions: AdsCustomFunctions = {
  checkUserAdEligibility: async (userId: string, adType: 'interstitial' | 'rewarded') => {
    // Your custom business logic
    const user = await getUserFromDatabase(userId);
    const viewsToday = await getAdViewsToday(userId, adType);
    
    if (user.isBanned) {
      return { eligible: false, message: 'User is banned from watching ads' };
    }
    
    if (viewsToday >= 5) {
      return { eligible: false, message: 'Daily ad limit reached' };
    }
    
    return { eligible: true, message: 'User is eligible' };
  },

  getUserAdViewsToday: async (userId: string, adType: 'interstitial' | 'rewarded', date: string) => {
    // Your database query
    const result = await db.query(
      'SELECT COUNT(*) FROM ad_completions WHERE user_id = ? AND ad_type = ? AND DATE(created_at) = ?',
      [userId, adType, date]
    );
    return result[0]['COUNT(*)'];
  },

  logAdCompletion: async (userId: string, adId: string, adType: 'interstitial' | 'rewarded', rewardAmount: number, metadata: Record<string, any>, adStatus?: RewardedAdStatusDTO) => {
    // Your database logging
    await db.query(
      'INSERT INTO ad_completions (user_id, ad_id, ad_type, reward_amount, metadata, mediator_granted_at, created_at) VALUES (?, ?, ?, ?, ?, ?, ?)',
      [userId, adId, adType, rewardAmount, JSON.stringify(metadata), adStatus?.mediator_granted_at, new Date()]
    );
  }
};

// Use with custom functions
const result = await verifyRewardedAdAction(
  { adId: 'ad_123', userId: 'user_456', rewardAmount: 0.1 },
  undefined, // PiBackendConfig
  customFunctions // Your custom functions
);
```

## 🚀 Available Actions

### `verifyRewardedAdAction`

Verifies a rewarded ad completion and processes the reward.

```typescript
import { verifyRewardedAdAction } from '@xhilo/pi-sdk/backend';

const result = await verifyRewardedAdAction({
  adId: 'ad_123456',
  userId: 'user_789',
  adType: 'rewarded',
  rewardAmount: 0.1,
  metadata: { source: 'main_menu' }
});
```

**Parameters:**
- `adId` - The ad identifier from the frontend
- `userId` - The user's Pi Network UID
- `adType` - Must be 'rewarded'
- `rewardAmount` - Amount of Pi to reward (default: 0.1)
- `metadata` - Additional metadata for tracking

**Returns:**
```typescript
interface VerifyRewardedAdResult {
  success: boolean;
  message: string;
  verified?: boolean;
  rewardAmount?: number;
  adId?: string;
}
```

### `getAdEligibilityAction`

Checks if a user is eligible to watch ads and receive rewards.

```typescript
import { getAdEligibilityAction } from '@xhilo/pi-sdk/backend';

const result = await getAdEligibilityAction({
  userId: 'user_789',
  adType: 'rewarded',
  maxDailyViews: 5
});
```

**Parameters:**
- `userId` - The user's Pi Network UID
- `adType` - 'interstitial' or 'rewarded'
- `maxDailyViews` - Maximum daily ad views allowed (default: 5)

**Returns:**
```typescript
interface AdEligibilityResult {
  success: boolean;
  message: string;
  eligible: boolean;
  viewsToday: number;
  maxViews: number;
  nextEligibleTime?: string;
}
```

### `getUserAdStatsAction`

Gets user's ad viewing statistics.

```typescript
import { getUserAdStatsAction } from '@xhilo/pi-sdk/backend';

const result = await getUserAdStatsAction('user_789');
```

**Parameters:**
- `userId` - The user's Pi Network UID

**Returns:**
```typescript
interface AdStats {
  today: {
    interstitial: number;
    rewarded: number;
  };
  thisWeek: {
    interstitial: number;
    rewarded: number;
  };
  thisMonth: {
    interstitial: number;
    rewarded: number;
  };
}
```

## 🔧 Backend Implementation

### Environment Variables

```bash
# For Pi Platform API (ad verification and U2A payments)
PI_API_KEY=your_pi_api_key

# For A2U payments (ad rewards)
PI_WALLET_PRIVATE_SEED=your_wallet_private_seed
```

### API Routes

#### POST `/api/ads/verify-rewarded`

```typescript
import { NextRequest, NextResponse } from 'next/server'
import { verifyRewardedAdAction } from '@xhilo/pi-sdk/backend'

export async function POST(request: NextRequest) {
  try {
    const body = await request.json()
    const { adId, userId, adType, rewardAmount, metadata } = body

    // Validate required fields
    if (!adId || !userId || !adType) {
      return NextResponse.json(
        { success: false, message: 'Missing required fields' },
        { status: 400 }
      )
    }

    // Verify the rewarded ad
    const result = await verifyRewardedAdAction({
      adId,
      userId,
      adType,
      rewardAmount: rewardAmount || 0.1,
      metadata: metadata || {}
    })

    return NextResponse.json(result)
  } catch (error) {
    return NextResponse.json(
      { success: false, message: 'Internal server error' },
      { status: 500 }
    )
  }
}
```

#### POST `/api/ads/eligibility`

```typescript
import { NextRequest, NextResponse } from 'next/server'
import { getAdEligibilityAction } from '@xhilo/pi-sdk/backend'

export async function POST(request: NextRequest) {
  try {
    const body = await request.json()
    const { userId, adType, maxDailyViews } = body

    // Check ad eligibility
    const result = await getAdEligibilityAction({
      userId,
      adType,
      maxDailyViews: maxDailyViews || 5
    })

    return NextResponse.json(result)
  } catch (error) {
    return NextResponse.json(
      { success: false, message: 'Internal server error' },
      { status: 500 }
    )
  }
}
```

#### GET `/api/ads/stats`

```typescript
import { NextRequest, NextResponse } from 'next/server'
import { getUserAdStatsAction } from '@xhilo/pi-sdk/backend'

export async function GET(request: NextRequest) {
  try {
    const { searchParams } = new URL(request.url)
    const userId = searchParams.get('userId')

    if (!userId) {
      return NextResponse.json(
        { success: false, message: 'Missing userId parameter' },
        { status: 400 }
      )
    }

    // Get ad statistics
    const result = await getUserAdStatsAction(userId)
    return NextResponse.json(result)
  } catch (error) {
    return NextResponse.json(
      { success: false, message: 'Internal server error' },
      { status: 500 }
    )
  }
}
```

## 🔐 Security Considerations

### Access Token Validation

All ad actions should validate the user's access token:

```typescript
// Extract access token from Authorization header
const authHeader = request.headers.get('authorization')
const accessToken = authHeader?.replace('Bearer ', '')

// Validate token with Pi Platform API
if (accessToken) {
  try {
    const userInfo = await getMeAction(accessToken);
    if (userInfo.data.uid !== userId) {
      return NextResponse.json(
        { success: false, message: 'User ID mismatch' },
        { status: 401 }
      );
    }
  } catch (error) {
    return NextResponse.json(
      { success: false, message: 'Invalid access token' },
      { status: 401 }
    );
  }
}
```

### Rate Limiting

Implement rate limiting to prevent abuse:

```typescript
// Check daily limits
const viewsToday = await getUserAdViewsToday(userId, adType, today);
if (viewsToday >= maxDailyViews) {
  return {
    success: false,
    message: `Daily limit reached (${viewsToday}/${maxDailyViews})`,
    eligible: false
  };
}
```

### Ad Verification

In production, implement proper ad verification:

```typescript
// Verify ad with Pi Platform (placeholder)
async function verifyAdWithPiPlatform(adId: string, userId: string): Promise<boolean> {
  // 1. Call Pi's ad verification API
  // 2. Check if the ad was actually watched
  // 3. Verify the ad ID is valid and not already used
  // 4. Check if the user is the same one who watched the ad
  
  // For now, simulate verification
  return true;
}
```

## 📊 Database Schema

### Ad Completions Table

```sql
CREATE TABLE ad_completions (
  id SERIAL PRIMARY KEY,
  user_id VARCHAR(255) NOT NULL,
  ad_id VARCHAR(255) NOT NULL,
  ad_type VARCHAR(50) NOT NULL,
  reward_amount DECIMAL(10, 2) DEFAULT 0,
  metadata JSONB,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  UNIQUE(user_id, ad_id)
);

CREATE INDEX idx_ad_completions_user_id ON ad_completions(user_id);
CREATE INDEX idx_ad_completions_created_at ON ad_completions(created_at);
```

### Ad Eligibility Table

```sql
CREATE TABLE ad_eligibility (
  id SERIAL PRIMARY KEY,
  user_id VARCHAR(255) NOT NULL,
  ad_type VARCHAR(50) NOT NULL,
  views_today INTEGER DEFAULT 0,
  last_reset_date DATE DEFAULT CURRENT_DATE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  UNIQUE(user_id, ad_type, last_reset_date)
);
```

## 🧪 Testing

### Unit Tests

```typescript
import { verifyRewardedAdAction } from '../src/actions/ads';

describe('verifyRewardedAdAction', () => {
  it('should verify rewarded ad successfully', async () => {
    const result = await verifyRewardedAdAction({
      adId: 'test_ad_123',
      userId: 'test_user_456',
      adType: 'rewarded',
      rewardAmount: 0.1
    });

    expect(result.success).toBe(true);
    expect(result.verified).toBe(true);
    expect(result.rewardAmount).toBe(0.1);
  });

  it('should reject non-rewarded ads', async () => {
    const result = await verifyRewardedAdAction({
      adId: 'test_ad_123',
      userId: 'test_user_456',
      adType: 'interstitial', // Wrong type
      rewardAmount: 0.1
    });

    expect(result.success).toBe(false);
    expect(result.message).toContain('Only rewarded ads');
  });
});
```

### Integration Tests

```typescript
import { getAdEligibilityAction } from '../src/actions/ads';

describe('getAdEligibilityAction', () => {
  it('should check eligibility correctly', async () => {
    const result = await getAdEligibilityAction({
      userId: 'test_user_456',
      adType: 'rewarded',
      maxDailyViews: 5
    });

    expect(result.success).toBe(true);
    expect(result.eligible).toBeDefined();
    expect(result.viewsToday).toBeGreaterThanOrEqual(0);
    expect(result.maxViews).toBe(5);
  });
});
```

## 🔗 Related Documentation

- [usePiAds Hook](../react/docs/usePiAds.md) - Frontend ads hook
- [Pi Platform Ads Documentation](https://developers.minepi.com/ads)
- [Backend Payment Actions](./payments.md) - Payment processing
- [Security Guide](../react/docs/security.md) - Access token validation
