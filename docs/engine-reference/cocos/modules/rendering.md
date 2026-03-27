# Cocos Rendering — Quick Reference

Last verified: 2026-03-27 | Engine: Cocos Creator 3.8

## What Changed Since ~3.7 (LLM Cutoff)

### 3.8 Changes
- **Custom Render Pipeline (CRP)**: New rendering architecture with post-processing support
- **Built-in Post-Processing**: Anti-aliasing, super-resolution, ambient occlusion, bloom
- **High Precision Text**: Crisp text rendering at any scale
- **MSAA Support**: Added for WebGL2 and WebGPU

### 3.7 Changes
- **Custom Pipeline Preview**: Cyberpunk Demo showcased new pipeline capabilities
- **Performance Optimizations**: Core rendering improvements

### 2.x → 3.x Migration
- Complete rendering pipeline rewrite
- Material system overhauled
- Effect file format changed

## Current API Patterns

### Enable New Render Pipeline (3.8+)
```
Project Settings → Graphics → New Render Pipeline
Select: Standard (recommended) or Custom
```

### Post-Processing Setup (3.8+)
```typescript
// Enable on Camera
const camera = this.getComponent(Camera);
// Post-processing configured via render pipeline settings
```

### Effect File Structure
```yaml
CCEffect %{
  techniques:
    - name: opaque
      passes:
        - vert: standard-vs
          frag: standard-fs
          properties:
            mainTexture: { value: white }
            tintColor: { value: [1, 1, 1, 1], editor: { type: color } }
}%

CCProgram standard-vs %{
  precision highp float;
  #include <cc-global>
  #include <cc-local>
  // ...
}%
```

### Built-in Shaders Include
- `#include <cc-global>` — Global uniforms (time, screen size)
- `#include <cc-local>` — Local transforms
- `#include <cc-sprite>` — 2D sprite uniforms

## Performance Budgets

| Target | Frame Budget | GPU Time |
|--------|-------------|----------|
| Mobile 60fps | 16.6ms | 10-12ms |
| Mobile 30fps | 33.3ms | 25-28ms |
| Desktop 60fps | 16.6ms | 12-14ms |

## Common Mistakes
- Not enabling new render pipeline for post-processing effects
- Using `precision highp float` everywhere on mobile (use `mediump`)
- Texture samples in loops (exponential cost)
- Not using SpriteAtlas for 2D games (batching broken)