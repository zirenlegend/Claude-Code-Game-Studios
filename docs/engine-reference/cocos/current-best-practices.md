# Cocos Creator — Current Best Practices

Last verified: 2026-03-27 | Engine: Cocos Creator 3.8

Practices that are **new or changed** since the model's training data (~3.7).
This supplements (not replaces) the agent's built-in knowledge.

## Rendering Pipeline (3.8)

- **Custom Render Pipeline (CRP)**: New rendering architecture with built-in post-processing
  - Enable in: Project Settings → Graphics → New Render Pipeline
  - Supports: Anti-aliasing (MSAA, FXAA, SMAA), Super-resolution, Ambient Occlusion, Bloom
  - Custom pipelines share same mechanism as built-in pipelines

- **Post-Processing Effects**: Full-screen effects now built-in
  - Configure in Camera component or render pipeline settings
  - Supports custom post-processing shaders

## Animation (3.8)

- **Procedural Animation**: New system for runtime-generated animations
  - Create animations programmatically without pre-made clips
  - Useful for procedural character movement, IK, physics-driven animation

## Text Rendering (3.8)

- **High Precision Text**: Improved text rendering system
  - Crisp text at any scale
  - Better font rendering on mobile devices

## Physics (3.8)

- **Character Controller**: New built-in component for 3D character movement
  - Handles slope limits, step offsets, collision response
  - Replaces custom character controller implementations

## 2D Rendering (3.8.7+)

- **Sorting Layers**: Unified with 3D render layer definitions
  - Configure in Project Settings → Sorting Layers
  - More flexible 2D rendering order control

## Spine Animation (3.8.6+)

- **Multi-version Support**: Both Spine 3.8 and Spine 4.2 supported
  - Select version in Engine Feature Trimming panel
  - Per-project Spine version selection

## Platform Support (3.8)

- **HarmonyOS Next**: Full native support
  - Game controller support
  - Native audio playback
  - WebAssembly initialization fixes

- **Android 16KB Page Size**: Support for newer Android devices
  - Required for Google Play targeting Android 15+

- **WebGL2 & WebGPU**: MSAA support for web platforms

## Resource Management (3.x)

- **Asset Bundles**: Organize resources by loading context
  - `assetManager.loadBundle()` for remote/dynamic content
  - Bundle versioning for hot updates
  - Avoid monolithic bundles; split by scene/feature

- **Sub-resource Paths**: When loading images dynamically
  ```typescript
  // Image set as sprite-frame at path 'sprites/hero'
  resources.load('sprites/hero/spriteFrame', SpriteFrame, callback);
  // NOT: resources.load('sprites/hero', SpriteFrame, callback);
  ```

## Component Patterns (3.x)

- **UITransform Required**: All 2D nodes need UITransform for size/anchor
  ```typescript
  const transform = node.getComponent(UITransform);
  transform.setContentSize(width, height);
  transform.setAnchorPoint(0.5, 0.5);
  ```

- **UIOpacity for Non-Render Nodes**: Control opacity without Sprite
  ```typescript
  const opacity = node.getComponent(UIOpacity) || node.addComponent(UIOpacity);
  opacity.opacity = 128; // Semi-transparent
  ```

- **UISkew (3.8.6+)**: Skew requires dedicated component
  ```typescript
  const skew = node.getComponent(UISkew) || node.addComponent(UISkew);
  skew.setSkew(skewX, skewY);
  ```

## Physics Callbacks (3.x)

- **Explicit Registration**: Collision callbacks must be registered
  ```typescript
  const collider = this.getComponent(Collider2D);
  collider.on(Contact2DType.BEGIN_CONTACT, this.onBeginContact, this);
  collider.on(Contact2DType.END_CONTACT, this.onEndContact, this);
  ```

## Performance Optimization

- **Cache Component References**: Never use `getComponent()` in `update()`
  ```typescript
  // GOOD: Cache in onLoad
  private _rigidBody: RigidBody2D | null = null;
  onLoad() { this._rigidBody = this.getComponent(RigidBody2D); }
  update(dt: number) { this._rigidBody?.applyForce(...); }
  ```

- **Object Pooling**: Essential for frequently spawned objects
  - Pool bullets, particles, enemies
  - Reuse nodes instead of instantiate/destroy

- **Batching**: Minimize draw calls
  - Use SpriteAtlas for UI
  - Share materials across similar meshes

## TypeScript Strict Mode

- **Enabled by Default**: Cocos Creator 3.x uses TypeScript strict mode
  - Null checks are enforced
  - Explicit type annotations required
  - Disable in Project Settings → Script → Enable Relaxed Mode (not recommended)