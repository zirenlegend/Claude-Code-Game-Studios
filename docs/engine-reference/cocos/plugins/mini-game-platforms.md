# Cocos Creator 3.8 — Mini Game Platforms

**Last verified:** 2026-03-27
**Status:** Production-Ready
**Extension:** Built-in build plugins

---

## Overview

Cocos Creator provides first-class support for Chinese mini game platforms.
These platforms require specific SDK integrations and build configurations.

**Supported Platforms:**
- WeChat Mini Game (微信小游戏)
- ByteDance Mini Game (抖音小游戏)
- Alipay Mini Game (支付宝小游戏)
- Baidu Mini Game (百度小游戏)
- Xiaomi Quick Game (小米快游戏)
- Huawei Quick Game (华为快游戏)
- OPPO Mini Game (OPPO小游戏)
- vivo Mini Game (vivo小游戏)

**Knowledge Gap:** Platform SDKs update frequently.
Always check latest platform documentation before building.

---

## Platform Constants (3.x)

```typescript
import { sys } from 'cc';

// Platform detection
if (sys.isNative) {
    // Native iOS/Android/Windows/macOS
} else if (sys.isBrowser) {
    // Web browser
}

// Mini game platform detection
if (sys.platform === sys.Platform.WECHAT_GAME) {
    // WeChat Mini Game specific code
} else if (sys.platform === sys.Platform.BYTEDANCE_MINI_GAME) {
    // ByteDance Mini Game
} else if (sys.platform === sys.Platform.ALIPAY_MINI_GAME) {
    // Alipay Mini Game
}
```

### Platform Constants Table

| 2.x Constant | 3.x Constant |
|--------------|--------------|
| `sys.BAIDU_GAME` | `sys.BAIDU_MINI_GAME` |
| `sys.VIVO_GAME` | `sys.VIVO_MINI_GAME` |
| `sys.OPPO_GAME` | `sys.OPPO_MINI_GAME` |
| `sys.HUAWEI_GAME` | `sys.HUAWEI_QUICK_GAME` |
| `sys.XIAOMI_GAME` | `sys.XIAOMI_QUICK_GAME` |
| `sys.ALIPAY_GAME` | `sys.ALIPAY_MINI_GAME` |
| `sys.BYTEDANCE_GAME` | `sys.BYTEDANCE_MINI_GAME` |

---

## Build Configuration

### 1. WeChat Mini Game

```
Build Panel → WeChat Mini Game

Required Settings:
- App ID: Your WeChat AppID
- Subpackage: Enable for large games
- Orientation: Portrait/Landscape
```

### 2. ByteDance Mini Game

```
Build Panel → ByteDance Mini Game

Required Settings:
- App ID: Your ByteDance AppID
- App Name: Game display name
- Version: Semantic version
```

---

## Platform API Access

### WeChat Mini Game

```typescript
// Access WeChat API
declare const wx: any;

// User login
wx.login({
    success: (res: any) => {
        console.log('Login success:', res.code);
    }
});

// Share
wx.shareAppMessage({
    title: 'Play my game!',
    imageUrl: 'share.png'
});

// Vibrate
wx.vibrateShort({ type: 'medium' });

// Show toast
wx.showToast({
    title: 'Achievement unlocked!',
    icon: 'success'
});
```

### ByteDance Mini Game

```typescript
// Access ByteDance API
declare const tt: any;

// User login
tt.login({
    success: (res: any) => {
        console.log('Login success:', res.code);
    }
});

// Show video ad
tt.showRewardedVideoAd({
    adUnitId: 'your-ad-unit-id',
    success: () => {
        // Reward user
    }
});
```

### Baidu Mini Game

```typescript
// Access Baidu API
declare const swan: any;

// User login
swan.login({
    success: (res: any) => {
        console.log('Login success:', res.code);
    }
});

// Show toast
swan.showToast({
    title: '操作成功',
    icon: 'success'
});
```

### Xiaomi Quick Game

```typescript
// Access Xiaomi API
declare const qg: any;

// User login (Xiaomi uses different flow)
qg.login({
    success: (res: any) => {
        console.log('Login success:', res.data.token);
    }
});

// Vibrate
qg.vibrateShort();
```

### Huawei Quick Game

```typescript
// Access Huawei API
declare const hbs: any;

// User login
hbs.login({
    success: (res: any) => {
        console.log('Login success:', res.accessToken);
    }
});

// Check if Huawei Quick Game environment
if (typeof hbs !== 'undefined') {
    // Huawei-specific features
}
```

### OPPO Mini Game

```typescript
// Access OPPO API
declare const qg: any;  // Same global as Xiaomi

// Platform check
if (sys.platform === sys.Platform.OPPO_MINI_GAME) {
    qg.login({
        success: (res: any) => {
            console.log('OPPO login success');
        }
    });
}
```

### vivo Mini Game

```typescript
// Access vivo API
declare const qg: any;  // Same global as OPPO/Xiaomi

if (sys.platform === sys.Platform.VIVO_MINI_GAME) {
    qg.login({
        success: (res: any) => {
            console.log('vivo login success');
        }
    });
}
```

---

## Platform-Specific Features

### Storage

```typescript
// WeChat
wx.setStorageSync('key', 'value');
const value = wx.getStorageSync('key');

// ByteDance
tt.setStorageSync('key', 'value');
const value = tt.getStorageSync('key');

// Cross-platform wrapper
export function saveData(key: string, value: any): void {
    if (typeof wx !== 'undefined') {
        wx.setStorageSync(key, value);
    } else if (typeof tt !== 'undefined') {
        tt.setStorageSync(key, value);
    } else {
        sys.localStorage.setItem(key, JSON.stringify(value));
    }
}
```

### Audio (Background Music)

```typescript
// Mini games require user interaction before playing audio
// Use touch event to initialize

onTouchStart() {
    if (typeof wx !== 'undefined') {
        // WeChat requires bgm management
        const bgm = wx.createInnerAudioContext();
        bgm.src = 'audio/bgm.mp3';
        bgm.loop = true;
        bgm.play();
    }
}
```

---

## Subpackaging

### Configuration

```
Project Settings → Build → Subpackage

Define subpackages:
- main: Core game (4MB limit for WeChat)
- stage1: Level pack 1
- stage2: Level pack 2
```

### Loading Subpackages

```typescript
// Load subpackage
assetManager.loadBundle('stage1', (err, bundle) => {
    if (!err) {
        bundle.load('scenes/Level1', Scene, (err, scene) => {
            director.runScene(scene);
        });
    }
});
```

---

## Performance Guidelines

### Memory
- WeChat mini game memory limit: ~1GB
- Keep loaded assets minimal
- Release unused resources aggressively

### Package Size
- Main package: ≤4MB (WeChat)
- Total size: ≤20MB (including subpackages)
- Use texture compression
- Compress audio files

### Draw Calls
- Target: <100 draw calls
- Use SpriteAtlas
- Batch similar sprites

---

## Common Issues

### Login Fails
1. Check AppID is correct
2. Verify domain whitelist in platform console
3. Ensure network is accessible

### Audio Not Playing
- Mini games require user interaction first
- Call audio play in touch/click handler

### Build Errors
1. Update platform SDK in build settings
2. Check engine version compatibility
3. Verify subpackage size limits

---

## Advertising System

### Rewarded Video Ads

```typescript
// WeChat Rewarded Video
class AdManager {
    private rewardedVideoAd: any = null;

    initRewardedVideo(adUnitId: string): void {
        if (typeof wx !== 'undefined') {
            this.rewardedVideoAd = wx.createRewardedVideoAd({
                adUnitId: adUnitId
            });

            this.rewardedVideoAd.onClose((res: any) => {
                if (res && res.isEnded) {
                    // User watched full video - give reward
                    this.grantReward();
                } else {
                    // User closed early - no reward
                    console.log('Video not completed');
                }
            });

            this.rewardedVideoAd.onError((err: any) => {
                console.error('Ad error:', err);
            });
        }
    }

    showRewardedVideo(): void {
        if (this.rewardedVideoAd) {
            this.rewardedVideoAd.show().catch(() => {
                // Ad not ready, load first
                this.rewardedVideoAd.load().then(() => {
                    this.rewardedVideoAd.show();
                });
            });
        }
    }

    grantReward(): void {
        // Give coins, items, etc.
        console.log('Reward granted!');
    }
}
```

### Banner Ads

```typescript
// WeChat Banner Ad
createBannerAd(adUnitId: string): any {
    if (typeof wx === 'undefined') return null;

    const bannerAd = wx.createBannerAd({
        adUnitId: adUnitId,
        style: {
            left: 0,
            top: 0,
            width: 300
        }
    });

    bannerAd.onResize((size: any) => {
        // Center banner
        const systemInfo = wx.getSystemInfoSync();
        bannerAd.style.left = (systemInfo.windowWidth - size.width) / 2;
        bannerAd.style.top = systemInfo.windowHeight - size.height;
    });

    bannerAd.onError((err: any) => {
        console.error('Banner ad error:', err);
    });

    return bannerAd;
}
```

### Interstitial Ads

```typescript
// WeChat Interstitial Ad
class InterstitialAdManager {
    private interstitialAd: any = null;

    init(adUnitId: string): void {
        if (typeof wx !== 'undefined') {
            this.interstitialAd = wx.createInterstitialAd({
                adUnitId: adUnitId
            });

            this.interstitialAd.onClose(() => {
                // Preload next ad
                this.interstitialAd.load();
            });
        }
    }

    show(): void {
        if (this.interstitialAd) {
            this.interstitialAd.show().catch(() => {
                this.interstitialAd.load().then(() => {
                    this.interstitialAd.show();
                });
            });
        }
    }
}
```

### ByteDance Ads

```typescript
// ByteDance Video Ad
if (typeof tt !== 'undefined') {
    const videoAd = tt.createRewardedVideoAd({
        adUnitId: 'your-ad-unit-id'
    });

    videoAd.onClose((res: any) => {
        if (res.isEnded) {
            // Reward user
        }
    });

    videoAd.show();
}
```

---

## Payment System (IAP)

### WeChat Payment

```typescript
// WeChat In-App Purchase
class PaymentManager {
    // Request payment
    requestPayment(orderInfo: any): Promise<void> {
        return new Promise((resolve, reject) => {
            if (typeof wx === 'undefined') {
                reject(new Error('Not WeChat environment'));
                return;
            }

            wx.requestMidasPayment({
                mode: 'game',
                env: 0,  // 0 = formal, 1 = sandbox
                offerId: orderInfo.offerId,
                currencyType: 'CNY',
                platform: 'android',
                buyQuantity: orderInfo.quantity,
                success: () => {
                    // Payment success
                    this.verifyPayment(orderInfo.orderId);
                    resolve();
                },
                fail: (err: any) => {
                    console.error('Payment failed:', err);
                    reject(err);
                }
            });
        });
    }

    // Verify payment with server
    async verifyPayment(orderId: string): Promise<void> {
        // Call your backend to verify
        // const response = await fetch('/api/verify-payment', {...});
    }
}
```

### ByteDance Payment

```typescript
// ByteDance In-App Purchase
if (typeof tt !== 'undefined') {
    tt.pay({
        orderInfo: {
            appId: 'your-app-id',
            orderId: 'order-123',
            totalAmount: 100,  // in cents
            subject: 'Game Coins'
        },
        success: (res: any) => {
            console.log('Payment success:', res);
        },
        fail: (err: any) => {
            console.error('Payment failed:', err);
        }
    });
}
```

### Order Management

```typescript
// Cross-platform order manager
class OrderManager {
    async createOrder(product: Product): Promise<Order> {
        // 1. Create order on your server
        const order = await this.createOrderOnServer(product);

        // 2. Request platform payment
        await this.requestPlatformPayment(order);

        // 3. Verify and deliver
        await this.verifyAndDeliver(order);

        return order;
    }

    private async createOrderOnServer(product: Product): Promise<Order> {
        // Server-side order creation
        // Returns order info needed for platform payment
        return {
            orderId: 'xxx',
            offerId: 'yyy',
            quantity: product.quantity,
            amount: product.price
        };
    }

    private async requestPlatformPayment(order: Order): Promise<void> {
        // Platform-specific payment (see above)
    }

    private async verifyAndDeliver(order: Order): Promise<void> {
        // Verify with server, then deliver items
    }
}
```

---

## Sources
- https://docs.cocos.com/creator/3.8/manual/en/publish/publish-wechatgame.html
- https://developers.weixin.qq.com/minigame/dev/guide/
- https://microapp.bytedance.com/docs/ZH/mini-game/Develop/guide/