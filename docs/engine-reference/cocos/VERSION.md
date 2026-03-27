# Cocos Creator — Version Reference

| Field | Value |
|-------|-------|
| **Engine Version** | Cocos Creator 3.8 |
| **Release Date** | 2024 |
| **Project Pinned** | 2026-03-27 |
| **Last Docs Verified** | 2026-03-27 |
| **LLM Knowledge Cutoff** | May 2025 |

## Knowledge Gap Warning

The LLM's training data likely covers Cocos Creator up to ~3.7. Version 3.8 and later may
introduce changes that the model does NOT know about. Always cross-reference this directory
before suggesting Cocos API calls.

## Post-Cutoff Version Timeline

| Version | Release | Risk Level | Key Theme |
|---------|---------|------------|-----------|
| 3.7 | Early 2024 | LOW | Architecture refactor, performance improvements (likely in training data) |
| 3.8 | Mid 2024 | MEDIUM | Custom Render Pipeline, post-processing, procedural animation, Character Controller |
| 3.9+ | Late 2024+ | HIGH | Check official docs for latest features |

## Major Changes from 2.x to 3.x

### Breaking Changes
- **Complete API Rewrite**: 3.x is not backward compatible with 2.x
- **Resource System**: `cc.loader` → `assetManager` with bundle support
- **Node Properties**: `size`/`anchor` → `UITransform` component; `color` → render component
- **Animation**: Action system removed entirely, use Tween API
- **Audio**: `cc.audioEngine` removed, use AudioSource component
- **Physics**: Component renaming (`cc.Collider` → `Collider2D`, etc.)
- **Position**: `node.position` is read-only, use `setPosition()`

### New Features (3.x)
- **TypeScript-First**: Strict mode enabled by default, decorator-based components
- **Asset Bundles**: Bundle-based loading with hot update support
- **Custom Render Pipeline (3.8+)**: Post-processing effects (AA, bloom, AO)
- **Procedural Animation (3.8+)**: Runtime-generated animations
- **Character Controller (3.8+)**: Built-in 3D character movement
- **High Precision Text (3.8+)**: Crisp text at any scale

### Deprecated Systems
| Deprecated | Use Instead | Notes |
|------------|-------------|-------|
| `cc.Action` system | Tween API | Entire action system removed |
| `cc.audioEngine` | AudioSource component | Audio system rewritten |
| `cc.loader` | `assetManager` | Bundle-based loading |
| `node.x`, `node.y` direct assignment | `setPosition()` | Position is read-only |
| `node.group` | `node.layer` | Bit flag based |
| `node.zIndex` | `setSiblingIndex()` | Sibling order control |
| `cc.Skeleton` | `sp.Skeleton` | Spine integration changed |

## Verified Sources

- Official docs: https://docs.cocos.com/creator/3.8/
- API Reference: https://docs.cocos.com/creator/3.8/api/
- Release Notes: https://www.cocos.com/creator-download
- GitHub: https://github.com/cocos/cocos-engine
- Forum: https://discuss.cocos2d-x.org/

## Key Subsystems

| Subsystem | Specialist Agent | Key Files |
|-----------|-----------------|-----------|
| TypeScript/Scripts | `cocos-typescript-specialist` | Component patterns, lifecycle, decorators |
| Shaders/Rendering | `cocos-shader-specialist` | .effect files, materials, render pipeline |
| UI System | `cocos-ui-specialist` | Widget, Layout, Canvas, screen adaptation |
| Native Extension | `cocos-native-specialist` | JSB bindings, C++ plugins, platform SDKs |
| Asset Management | `cocos-asset-specialist` | Asset Bundles, hot updates, resource loading |

## Engine Characteristics

### Strengths
- **Cross-platform**: One codebase for iOS, Android, Web, Windows, macOS
- **2D Excellence**: Best-in-class 2D game engine with powerful UI system
- **TypeScript**: Modern TypeScript development with full type support
- **Hot Update**: Built-in support for over-the-air updates
- **Lightweight**: Smaller runtime compared to Unity/Unreal
- **Open Source**: MIT license, full source available

### Best For
- Mobile 2D games (casual, puzzle, card, strategy)
- Mobile mid-core games
- Web games and instant games
- Cross-platform mobile-first projects
- Teams wanting TypeScript and modern tooling

### Not Ideal For
- AAA 3D games (use Unreal)
- Complex 3D with advanced rendering (use Unity/Unreal)
- Console-first development (Unity/Unreal have better console support)

## TypeScript/JavaScript Boundary

Cocos Creator uses TypeScript as its primary language:

- **TypeScript**: All game logic, components, managers
- **JavaScript**: Rarely used directly, TypeScript compiles to JS
- **C++ (Native)**: Performance-critical code via JSB bindings

The `cocos-native-specialist` handles the TypeScript/C++ boundary for
performance-critical systems.

## Common Migration Notes

### From Cocos Creator 2.x to 3.x
- Complete API restructure — many 2.x APIs removed
- New component lifecycle (`onLoad`, `start`, `update`)
- Different asset loading (`assetManager` vs `loader`)
- New render pipeline architecture
- TypeScript-first development

## Coordination

When working with Cocos Creator:
1. Read this VERSION.md first for version context
2. Consult the appropriate specialist agent for subsystem work
3. Check official docs for APIs introduced after May 2025
4. Use WebSearch to verify uncertain APIs