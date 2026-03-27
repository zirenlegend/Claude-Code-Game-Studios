# Cocos Creator — Deprecated APIs

Last verified: 2026-03-27

If an agent suggests any API in the "Deprecated" column, it MUST be replaced
with the "Use Instead" column.

## 2.x → 3.x Migration (Critical for Upgrading Projects)

### Node Properties

| Deprecated (2.x) | Use Instead (3.x) | Notes |
|------------------|-------------------|-------|
| `node.width`, `node.height` | `node.getComponent(UITransform).width` | Size now on UITransform |
| `node.anchorX`, `node.anchorY` | `node.getComponent(UITransform).anchorX` | Anchor now on UITransform |
| `node.setContentSize()` | `uitransform.setContentSize()` | |
| `node.color` | `sprite.color` (on render component) | Color on render components only |
| `node.opacity` | `sprite.color.a` or `UIOpacity.opacity` | Use UIOpacity for non-render nodes |
| `node.skew` | `UISkew` component (3.8.6+) | Add UISkew component to node |
| `node.group` | `node.layer` | Returns bit flag value (2^n) |
| `node.zIndex` | `node.setSiblingIndex()` | Direct sibling order control |
| `node.x`, `node.y`, `node.z` | `node.position.x` (read-only) | Use `setPosition(x, y, z)` to modify |
| `node.position = value` | `node.setPosition(value)` | Position is read-only property |

### Resource Loading

| Deprecated (2.x) | Use Instead (3.x) | Notes |
|------------------|-------------------|-------|
| `cc.loader.load()` | `resources.load()` | New resource manager API |
| `cc.loader.loadRes()` | `resources.load()` | |
| `cc.loader.loadResDir()` | `resources.loadDir()` | |
| `cc.loader.getRes()` | `resources.get()` | |
| `cc.loader.release()` | `resources.release()` or `assetManager.releaseAsset()` | |
| `cc.loader` | `assetManager` | Bundle-based loading system |

### Dynamic Loading Paths (3.x)

| Deprecated | Use Instead | Notes |
|------------|-------------|-------|
| `resources.load('image', SpriteFrame)` | `resources.load('image/spriteFrame', SpriteFrame)` | Must specify sub-resource path |
| `resources.load('image', Texture2D)` | `resources.load('image/texture', Texture2D)` | Must specify sub-resource path |

### Animation & Actions

| Deprecated (2.x) | Use Instead (3.x) | Notes |
|------------------|-------------------|-------|
| `cc.Action` system | Tween API | Entire Action system removed |
| `cc.moveTo()` | `tween().to()` | Use Tween system |
| `cc.sequence()` | `tween().then()` | Chain tweens |
| `cc.spawn()` | `tween().parallel()` | Parallel tweens |
| `cc.repeatForever()` | `Tween.repeatForever()` | |
| `cc.reverseTime()` | `Tween.reverseTime()` | |
| `animation.addClip()` | `animation.createState()` | |
| `animation.getClips()` | `animation.clips` | Property access |
| `animation.playAdditive()` | `animation.crossFade()` | |
| `animation.getAnimationState()` | `animation.getState()` | |

### Physics (2D)

| Deprecated (2.x) | Use Instead (3.x) |
|------------------|-------------------|
| `cc.Collider` | `Collider2D` |
| `cc.BoxCollider` | `BoxCollider2D` |
| `cc.CircleCollider` | `CircleCollider2D` |
| `cc.PolygonCollider` | `PolygonCollider2D` |
| `cc.RigidBody` | `RigidBody2D` |

### Physics (3D)

| Deprecated (2.x) | Use Instead (3.x) |
|------------------|-------------------|
| `cc.Collider3D` | `Collider` |
| `cc.BoxCollider3D` | `BoxCollider` |
| `cc.SphereCollider3D` | `SphereCollider` |
| `cc.RigidBody3D` | `RigidBody` |

### Camera

| Deprecated (2.x) | Use Instead (3.x) |
|------------------|-------------------|
| `camera.backgroundColor` | `camera.clearColor` |
| `camera.cullingMask` | `camera.visibility` |
| `camera.findCamera()` | Removed |
| `camera.alignWithScreen()` | Removed |
| `camera.main` | `camera.camera` or find manually |
| `camera.zoomRatio` | `camera.fov` or ortho size |
| `camera.getScreenToWorldPoint()` | `camera.screenToWorld()` |
| `camera.getWorldToScreenPoint()` | `camera.worldToScreen()` |
| `camera.getRay()` | `camera.screenPointToRay()` |

### Audio

| Deprecated (2.x) | Use Instead (3.x) |
|------------------|-------------------|
| `cc.audioEngine` | `AudioSource` component |
| `audioEngine.play()` | `audioSource.play()` |
| `audioEngine.pause()` | `audioSource.pause()` |
| `audioEngine.stop()` | `audioSource.stop()` |
| `audioEngine.setVolume()` | `audioSource.volume` property |
| `audioEngine.setLoop()` | `audioSource.loop` property |

### Global Constants

| Deprecated (2.x) | Use Instead (3.x) |
|------------------|-------------------|
| `CC_BUILD` | `BUILD` |
| `CC_TEST` | `TEST` |
| `CC_EDITOR` | `EDITOR` |
| `CC_PREVIEW` | `PREVIEW` |
| `CC_DEV` | `DEV` |
| `CC_DEBUG` | `DEBUG` |
| `CC_JSB` | `JSB` |
| `CC_WECHATGAME` | `WECHATGAME` |
| `CC_RUNTIME` | `RUNTIME_BASED` |
| `CC_SUPPORT_JIT` | `SUPPORT_JIT` |

### Platform Constants

| Deprecated (2.x) | Use Instead (3.x) |
|------------------|-------------------|
| `sys.BAIDU_GAME` | `sys.BAIDU_MINI_GAME` |
| `sys.VIVO_GAME` | `sys.VIVO_MINI_GAME` |
| `sys.OPPO_GAME` | `sys.OPPO_MINI_GAME` |
| `sys.HUAWEI_GAME` | `sys.HUAWEI_QUICK_GAME` |
| `sys.XIAOMI_GAME` | `sys.XIAOMI_QUICK_GAME` |
| `sys.JKW_GAME` | `sys.COCOSPLAY` |
| `sys.ALIPAY_GAME` | `sys.ALIPAY_MINI_GAME` |
| `sys.BYTEDANCE_GAME` | `sys.BYTEDANCE_MINI_GAME` |

## Patterns (Not Just APIs)

| Deprecated Pattern | Use Instead | Why |
|--------------------|-------------|-----|
| `getComponent()` by script name | `getComponent()` by class name | Scripts use class name, not file name |
| `node.x = 100` | `node.setPosition(100, node.position.y)` | Position is read-only |
| `find()` in `update()` | Cache in `onLoad()` | Performance: O(n) search every frame |
| Physics auto-callbacks | Manual registration | Must register collision callbacks explicitly |