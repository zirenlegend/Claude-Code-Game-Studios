# Cocos Creator — Breaking Changes

Last verified: 2026-03-27

Changes between Cocos Creator versions, focused on post-LLM-cutoff changes (3.7+).

## 3.7 → 3.8 (Mid 2024 — POST-CUTOFF, MEDIUM RISK)

| Subsystem | Change | Details |
|-----------|--------|---------|
| Rendering | Custom Render Pipeline (CRP) | New rendering pipeline with built-in post-processing effects: anti-aliasing, super-resolution, ambient occlusion, bloom |
| Rendering | Post-Processing System | Full-screen post-processing pipeline added. Requires enabling in Project Settings → Graphics → New Render Pipeline |
| Animation | Procedural Animation | New programmatic animation system for runtime-generated animations |
| UI | High Precision Text | Improved text rendering for crisp text at any scale |
| Physics | Character Controller | New built-in character controller component for 3D games |
| 2D | Spine Version Selection | Support for both Spine 3.8 and Spine 4.2 in same project (select in Engine Feature Trimming) |
| Platform | HarmonyOS Next Support | Full support for HarmonyOS Next platform |

## 3.6 → 3.7 (Early 2024 — NEAR CUTOFF, VERIFY)

| Subsystem | Change | Details |
|-----------|--------|---------|
| Rendering | Cyberpunk Demo Pipeline | Custom rendering pipeline preview (formalized in 3.8) |
| Architecture | Performance Optimization | Core engine performance improvements |
| Build | Build System Refactor | Improved build times and output organization |

## 2.x → 3.0 (MAJOR VERSION — BREAKING)

| Subsystem | Change | Details |
|-----------|--------|---------|
| **Core** | Complete API Rewrite | 3.x is not backward compatible with 2.x. New project architecture required. |
| **Resources** | `loader` → `assetManager` | New resource loading API with bundle support |
| **Nodes** | UI Properties Moved | `size`, `anchor` → `UITransform` component |
| **Nodes** | `color` → Render Component | Color now on render components (Sprite, etc.), not Node |
| **Nodes** | `opacity` → `UIOpacity` Component | Opacity handled via UIOpacity component for nodes without render components |
| **Nodes** | `skew` → `UISkew` Component (3.8.6+) | Skew requires adding UISkew component |
| **Nodes** | `group` → `layer` | Property renamed, values now use bit flags (2^n) |
| **Nodes** | `zIndex` Removed | Use `setSiblingIndex()` instead |
| **Nodes** | Position is Read-Only | `node.position` is readonly, use `setPosition()` |
| **Physics** | Component Rename | 2D: `cc.Collider` → `Collider2D`; 3D: `cc.Collider3D` → `Collider` |
| **Animation** | Action System Removed | Use Tween system instead of cc.Action |
| **Animation** | API Changes | `addClip` → `createState`, `getClips` → `clips`, `playAdditive` → `crossFade` |
| **Camera** | API Changes | `backgroundColor` → `clearColor`, `cullingMask` → `visibility` |
| **Audio** | API Changes | `audioEngine` removed, use AudioSource component |
| **Tween** | API Changes | `cc.repeatForever` → `Tween.repeatForever` |
| **Globals** | Constant Rename | `CC_BUILD` → `BUILD`, `CC_EDITOR` → `EDITOR`, etc. |
| **Globals** | Platform Rename | `BAIDU_GAME` → `BAIDU_MINI_GAME`, etc. |

## JSB Interface Changes (3.0+)

| Change | Details |
|--------|---------|
| `jsb.FileUtils.getDataFromFile` | Returns `ArrayBuffer` instead of `Uint8Array` |

## Build Output Changes (2.x → 3.x)

| Platform | Change |
|----------|--------|
| Web | Engine code unified in `cocos-js/` directory |
| Web | `settings.js` → `settings.json` |
| Web | Startup scripts: `index.js` + `application.js` (was `main.js`) |
| Native | Shared C++ code in `native/engine/common/` |
| Native | Platform-specific code in `native/engine/{platform}/` |
| Native | Build output in `proj/` directory |
| Mini Games | Adapter code in `libs/` as separate files |