# Cocos UI System — Quick Reference

Last verified: 2026-03-27 | Engine: Cocos Creator 3.8

## What Changed Since ~3.7 (LLM Cutoff)

### 3.8 Changes
- **High Precision Text**: New text rendering system for crisp text at any scale
- **Sorting Layers (3.8.7+)**: Unified with 3D render layers

### 2.x → 3.x Migration
- `node.size` → `UITransform.contentSize`
- `node.anchor` → `UITransform.anchorPoint`
- `node.color` → render component (Sprite, etc.)
- `node.opacity` → `UIOpacity` component
- `node.skew` → `UISkew` component (3.8.6+)
- `node.group` → `node.layer`
- `node.zIndex` → `node.setSiblingIndex()`

## Current API Patterns

### Widget Alignment
```typescript
const widget = node.getComponent(Widget);
widget.isAlignTop = true;
widget.top = 10;  // 10px from top
widget.isAlignHorizontalCenter = true;
```

### Safe Area Handling
```typescript
const safeArea = screen.getSafeArea();
const widget = this.getComponent(Widget);
if (widget) {
    widget.isAlignTop = true;
    widget.top = designSize.height - safeArea.y - safeArea.height;
}
```

### UITransform (Required for 2D)
```typescript
const transform = node.getComponent(UITransform);
transform.setContentSize(100, 50);
transform.setAnchorPoint(0.5, 0.5);
```

### UIOpacity for Non-Render Nodes
```typescript
// Add UIOpacity for fade effects on nodes without render components
const opacity = node.getComponent(UIOpacity) || node.addComponent(UIOpacity);
opacity.opacity = 200; // 0-255
```

### UISkew (3.8.6+)
```typescript
const skew = node.getComponent(UISkew) || node.addComponent(UISkew);
skew.setSkew(0.1, 0);  // SkewX, SkewY
```

## Layout Components

| Component | Use Case |
|-----------|----------|
| `HBoxLayout` | Horizontal arrangement |
| `VBoxLayout` | Vertical arrangement |
| `GridLayout` | Grid rows/columns |
| `FlowLayout` | Auto-wrapping layout |

## Common Mistakes
- Setting `node.color` directly (use render component)
- Forgetting UITransform for size/anchor operations
- Using `zIndex` instead of `setSiblingIndex()`
- Not handling safe area on notched devices