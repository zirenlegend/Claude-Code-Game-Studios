# Cocos Physics — Quick Reference

Last verified: 2026-03-27 | Engine: Cocos Creator 3.8

## What Changed Since ~3.7 (LLM Cutoff)

### 3.8 Changes
- **Character Controller**: New built-in 3D character controller component
  - Slope limits, step offsets, collision response
  - Replaces custom implementations

### 2.x → 3.x Migration
- 2D: `cc.Collider` → `Collider2D`, `cc.RigidBody` → `RigidBody2D`
- 3D: `cc.Collider3D` → `Collider`, `cc.RigidBody3D` → `RigidBody`
- Collision callbacks require explicit registration

## Current API Patterns

### Collision Callback Registration (Required in 3.x)
```typescript
// 2D Physics
const collider = this.getComponent(Collider2D);
if (collider) {
    collider.on(Contact2DType.BEGIN_CONTACT, this.onBeginContact, this);
    collider.on(Contact2DType.END_CONTACT, this.onEndContact, this);
    collider.on(Contact2DType.PRE_SOLVE, this.onPreSolve, this);
    collider.on(Contact2DType.POST_SOLVE, this.onPostSolve, this);
}

// Cleanup in onDestroy
onDestroy() {
    const collider = this.getComponent(Collider2D);
    collider?.off(Contact2DType.BEGIN_CONTACT, this.onBeginContact, this);
}
```

### 3D Character Controller (3.8+)
```typescript
const controller = this.getComponent(CharacterController);
controller.move(velocity);
controller.stepOffset = 0.5;
controller.slopeLimit = 45;
```

### 2D RigidBody
```typescript
const rigidBody = this.getComponent(RigidBody2D);
rigidBody.type = ERigidBody2DType.Dynamic;
rigidBody.linearVelocity = new Vec2(100, 0);
rigidBody.applyForceToCenter(new Vec2(0, 100), true);
```

### Collision Matrix
- Configure in: Project Settings → Physics
- Separate from Node layers (independent collision groups)

## Common Mistakes
- Assuming auto-registration of collision callbacks (must register manually)
- Mixing 2D and 3D collision component names
- Not cleaning up collision listeners in onDestroy()