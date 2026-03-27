# Cocos TypeScript — Quick Reference

Last verified: 2026-03-27 | Engine: Cocos Creator 3.8

## What Changed Since ~3.7 (LLM Cutoff)

### 3.x Architecture
- TypeScript-first development
- Strict mode enabled by default
- Decorator-based component system

## Current API Patterns

### Component Decorators
```typescript
import { _decorator, Component, Node } from 'cc';
const { ccclass, property } = _decorator;

@ccclass('PlayerController')
export class PlayerController extends Component {
    @property({ type: Node })
    targetNode: Node | null = null;

    @property({ type: CCFloat })
    moveSpeed: number = 5.0;

    @property({ type: Prefab })
    bulletPrefab: Prefab | null = null;
}
```

### Property Decorator Options
```typescript
@property({
    type: CCFloat,
    min: 0,
    max: 100,
    step: 1,
    tooltip: "Movement speed in units per second"
})
moveSpeed: number = 5.0;

@property({ type: [Node] })
waypoints: Node[] = [];
```

### Lifecycle Methods
```typescript
onLoad() {
    // Initialize references, set up events
    // Called once when component is loaded
}

start() {
    // Logic that depends on other components
    // Called after all components' onLoad
}

update(deltaTime: number) {
    // Per-frame logic — keep minimal
}

lateUpdate(deltaTime: number) {
    // Post-update (camera follow, UI)
}

onEnable() {
    // Component activated
}

onDisable() {
    // Component deactivated
}

onDestroy() {
    // Clean up event listeners!
}
```

### Event Handling
```typescript
onLoad() {
    input.on(Input.EventType.KEY_DOWN, this.onKeyDown, this);
    this.node.on(Node.EventType.TOUCH_START, this.onTouchStart, this);
}

onDestroy() {
    // MUST remove listeners to prevent memory leaks
    input.off(Input.EventType.KEY_DOWN, this.onKeyDown, this);
    this.node.off(Node.EventType.TOUCH_START, this.onTouchStart, this);
}
```

### Async Patterns
```typescript
async loadAsset(): Promise<Prefab | null> {
    return new Promise((resolve) => {
        resources.load('prefabs/Enemy', Prefab, (err, prefab) => {
            resolve(err ? null : prefab);
        });
    });
}

// Usage
const prefab = await this.loadAsset();
```

## Strict Mode Considerations
- All variables must have explicit types
- Null checks enforced (`| null` for nullable)
- Disable strict mode: Project Settings → Script → Enable Relaxed Mode

## Common Mistakes
- Missing `@ccclass` decorator (component won't register)
- Not removing event listeners in onDestroy (memory leaks)
- Using `any` type excessively (defeats TypeScript benefits)
- `getComponent()` in `update()` (performance)