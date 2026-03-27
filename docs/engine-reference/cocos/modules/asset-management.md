# Cocos Asset Management — Quick Reference

Last verified: 2026-03-27 | Engine: Cocos Creator 3.8

## What Changed Since ~3.7 (LLM Cutoff)

### 2.x → 3.x Migration
- `cc.loader` → `assetManager`
- Bundle-based loading system
- `loader.load()` → `resources.load()`
- Must specify sub-resource paths for images

## Current API Patterns

### Asset Bundle Loading
```typescript
// Load bundle
assetManager.loadBundle('remote-bundle', (err, bundle) => {
    if (err) return;
    bundle.load('prefabs/Enemy', Prefab, (err, prefab) => {
        const enemy = instantiate(prefab);
    });
});

// Load bundle from remote URL
assetManager.loadBundle('https://cdn.example.com/bundle', callback);
```

### Resources Loading
```typescript
// Load single asset
resources.load('prefabs/Player', Prefab, (err, prefab) => {
    if (err) return;
    const player = instantiate(prefab);
});

// Load with progress
resources.load('textures/', SpriteFrame, (finish, total) => {
    console.log(`Loading: ${finish}/${total}`);
}, (err, assets) => {
    // assets is array of loaded SpriteFrames
});
```

### Sub-resource Path (Critical in 3.x)
```typescript
// Image at 'sprites/hero' configured as sprite-frame
// WRONG:
resources.load('sprites/hero', SpriteFrame, callback);  // Returns ImageAsset

// CORRECT:
resources.load('sprites/hero/spriteFrame', SpriteFrame, callback);
resources.load('sprites/hero/texture', Texture2D, callback);
```

### Asset Release
```typescript
// Release single asset
resources.release('sprites/hero/spriteFrame', SpriteFrame);

// Release bundle
const bundle = assetManager.getBundle('my-bundle');
bundle?.releaseAll();
```

### Reference Counting
```typescript
// Assets loaded via resources are reference counted
// Add reference when storing
asset.addRef();

// Release reference
asset.decRef();
```

## Hot Update Pattern
```typescript
// Check for update
const manifestUrl = 'https://cdn.example.com/manifest.json';
// Use hot update manager for version checking and patching
```

## Common Mistakes
- Loading image without sub-resource path (returns ImageAsset, not SpriteFrame)
- Not releasing assets (memory leaks)
- Loading in `update()` loop (performance issues)
- Not handling load errors