# Cocos Creator 3.8 — Spine Animation

**Last verified:** 2026-03-27
**Status:** Production-Ready
**Extension:** Built-in (spine-creator)

---

## Overview

**Spine** is a 2D skeletal animation tool widely used in mobile games. Cocos Creator
provides built-in Spine runtime support with both Spine 3.8 and Spine 4.2 support
(since 3.8.6).

**Use Spine for:**
- Character animations (walk, run, attack, idle)
- UI animations with skeletal deformation
- Complex animations with skin/attachment changes
- Animations requiring inverse kinematics (IK)

**Knowledge Gap:** Cocos Creator 3.8.6+ added multi-version Spine support.
The model may suggest older API patterns.

---

## Version Selection (3.8.6+)

### Select Spine Version

In Cocos Creator Editor:
```
Menu → Project → Engine Feature Trimming → Spine Version
Options: Spine 3.8 or Spine 4.2
```

**Note:** Only one version can be active per project. Choose based on your
Spine Editor version and required features.

### Version Differences

| Feature | Spine 3.8 | Spine 4.2 |
|---------|-----------|-----------|
| Physics constraints | No | Yes |
| IK constraints | Basic | Enhanced |
| Path constraints | Yes | Yes |
| Mesh deformation | Yes | Improved |
| Runtime performance | Good | Better |

---

## Core Concepts

### 1. **Skeleton Component**
- Main component for Spine animations
- Holds skeleton data and controls playback

### 2. **SkeletonData**
- Asset containing skeleton, animations, skins
- Imported from `.json` or `.skel` files

### 3. **Animations**
- Named animation clips (walk, run, attack)
- Can be mixed/blended

### 4. **Skins**
- Visual variations of the same skeleton
- Allow character customization

---

## Installation

### Enable Spine Extension

Spine support is built into Cocos Creator 3.x. No additional installation required.

### Select Spine Version (3.8.6+)

Cocos Creator 3.8.6+ supports both Spine 3.8 and Spine 4.2 runtimes:

```
Menu → Project → Engine Feature Trimming → Spine Version
Options: Spine 3.8 or Spine 4.2
```

**Important:** Only one Spine version can be active per project. Choose based on:
- Your Spine Editor version (must match runtime)
- Required features (Spine 4.2 has physics constraints)

### Import Spine Assets

```
1. Export from Spine Editor:
   - JSON format (recommended for debugging)
   - Binary format (.skel, smaller size, faster loading)

2. Required files:
   - .json or .skel (skeleton data)
   - .atlas (texture atlas info)
   - .png (texture image(s))

3. Drag all files to Cocos assets folder
   - Keep files in same directory
   - Cocos auto-creates SkeletonData asset
```

---

## Setup

### Add Skeleton Component

```typescript
import { _decorator, Component, sp } from 'cc';
const { ccclass, property } = _decorator;

@ccclass('SpinePlayer')
export class SpinePlayer extends Component {
    @property({ type: sp.Skeleton })
    skeleton: sp.Skeleton | null = null;

    start() {
        if (this.skeleton) {
            this.skeleton.setAnimation(0, 'idle', true);
        }
    }
}
```

---

## API Reference

### Playing Animations

```typescript
// Play animation on track 0, loop = true
skeleton.setAnimation(0, 'walk', true);

// Play once, then call callback
const entry = skeleton.setAnimation(0, 'attack', false);
entry.listener = {
    complete: () => {
        console.log('Attack animation complete');
        skeleton.setAnimation(0, 'idle', true);
    }
};
```

### Animation Queue

```typescript
// Queue animations
skeleton.setAnimation(0, 'idle', true);
skeleton.addAnimation(0, 'walk', false, 0);  // Play after idle
skeleton.addAnimation(0, 'run', true, 0);    // Play after walk
```

### Mixing/Blending

```typescript
// Set mix duration between animations
skeleton.setMix('walk', 'run', 0.2);  // 0.2s transition
skeleton.setMix('run', 'idle', 0.15);
```

### Skins

```typescript
// Set skin
skeleton.setSkin('warrior');

// Combine skins (for customization)
skeleton.setSkin('base');
const skin = skeleton.getSkin();
skin.addSkin(skeleton.getSkeletonData()!.findSkin('armor'));
skin.addSkin(skeleton.getSkeletonData()!.findSkin('helmet'));
skeleton.setSkin(skin);
```

### Slots and Attachments

```typescript
// Change attachment on a slot
skeleton.setAttachment('weapon_slot', 'sword');

// Get slot for manipulation
const slot = skeleton.findSlot('head');
if (slot) {
    slot.color = new Color(255, 0, 0, 255);  // Tint red
}
```

---

## Performance Tips

### Use Binary Format
```
.skel (binary) is ~50% smaller than .json
Load time is ~30% faster
```

### Cache Skeleton Data
```typescript
// SkeletonData can be shared across multiple Skeleton components
@property({ type: sp.SkeletonData })
sharedSkeletonData: sp.SkeletonData | null = null;

// Assign to multiple skeleton components
```

### Animation Optimization
- Avoid too many animation tracks (tracks 0-5 are typical)
- Use animation mixing sparingly
- Pool skeleton instances for frequent spawn/despawn

---

## Common Issues

### Animation Not Playing
1. Check SkeletonData is assigned
2. Verify animation name matches Spine Editor
3. Check default skin exists
4. Ensure texture is properly imported

### Flickering/Z-fighting
- Check slot draw order
- Verify premultiplied alpha setting

### Memory Leaks
```typescript
// Clear skeleton data when done
onDestroy() {
    if (this.skeleton) {
        this.skeleton.clearTracks();
    }
}
```

---

## Debugging

### Spine Debug View

```typescript
// Enable debug rendering
skeleton.debugBones = true;      // Show bone positions
skeleton.debugSlots = true;      // Show slot bounding boxes
skeleton.debugMesh = true;       // Show mesh triangles
```

### Common Debug Scenarios

| Issue | Debug Method |
|-------|--------------|
| Animation not playing | Check `skeleton.skeletonData` is assigned |
| Wrong animation name | Use `skeleton.getSkeletonData()!.getAnimationNames()` |
| Texture missing | Verify .atlas and .png paths |
| Attachment not showing | Check slot draw order, use `findSlot()` |
| Skin not applied | Call `skeleton.setSkin()` then `skeleton.setSlotsToSetupPose()` |

### Log Animation Events

```typescript
skeleton.setCompleteListener((trackEntry) => {
    console.log(`Animation complete: ${trackEntry.animation?.name}`);
});

skeleton.setEventListener((trackEntry, event) => {
    console.log(`Spine event: ${event.data.name}, value: ${event.intValue}`);
});
```

### Inspector Properties

In Cocos Editor, select Skeleton node to view:
- **Skeleton Data**: Assigned SkeletonData asset
- **Default Skin**: Initial skin name
- **Default Animation**: Animation to play on start
- **Premultiplied Alpha**: Transparency blending mode
- **Time Scale**: Animation playback speed

---

## Migration from 2.x

| 2.x API | 3.x API |
|---------|---------|
| `sp.SkeletonAnimation` | `sp.Skeleton` |
| `skeletonNode` | `skeleton: sp.Skeleton` component |
| `setMixDuration()` | `setMix()` |

---

## Sources
- https://docs.cocos.com/creator/3.8/manual/en/assets/spine.html
- http://esotericsoftware.com/spine-api-reference