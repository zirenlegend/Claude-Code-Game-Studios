# Cocos Animation — Quick Reference

Last verified: 2026-03-27 | Engine: Cocos Creator 3.8

## What Changed Since ~3.7 (LLM Cutoff)

### 3.8 Changes
- **Procedural Animation**: New system for runtime-generated animations
  - Create animations programmatically
  - Useful for procedural character movement, IK, physics-driven animation

### 2.x → 3.x Migration
- **Action system removed entirely** — use Tween system
- `animation.addClip()` → `animation.createState()`
- `animation.getClips()` → `animation.clips`
- `animation.playAdditive()` → `animation.crossFade()`
- `animation.getAnimationState()` → `animation.getState()`

## Current API Patterns

### Tween System (Replaces Actions)
```typescript
// Basic tween
tween(this.node)
    .to(1.0, { position: new Vec3(100, 0, 0) })
    .to(0.5, { scale: new Vec3(2, 2, 2) })
    .call(() => { console.log('Animation complete'); })
    .start();

// With easing
tween(this.node)
    .to(1.0, { position: targetPos }, { easing: 'quadOut' })
    .start();

// Parallel tweens
tween(this.node)
    .parallel(
        tween().to(1.0, { position: new Vec3(100, 0, 0) }),
        tween().to(1.0, { scale: new Vec3(2, 2, 2) })
    )
    .start();
```

### Animation Component
```typescript
const animation = this.getComponent(Animation);
const state = animation.getState('walk');
state.speed = 1.5;
animation.play('walk');
animation.crossFade('run', 0.3);
```

### Procedural Animation (3.8+)
```typescript
// Programmatic animation creation
// See official docs for procedural animation API details
```

## Common Mistakes
- Using `cc.Action` APIs (removed in 3.x)
- Not cleaning up tween instances (can cause memory leaks)
- Mixing Animation component and Tween system incorrectly