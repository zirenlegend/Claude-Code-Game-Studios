# Cocos Native Extension — Quick Reference

Last verified: 2026-03-27 | Engine: Cocos Creator 3.8

## What Changed Since ~3.7 (LLM Cutoff)

### 3.8 Changes
- **HarmonyOS Next**: Full native support with game controllers
- **Android 16KB Page Size**: Support for newer devices
- **WebGPU**: MSAA support for web

### 2.x → 3.x Migration
- `jsb.FileUtils.getDataFromFile` returns `ArrayBuffer` (was `Uint8Array`)

## Current API Patterns

### JSB Binding Structure
```
native/plugins/
├── MyPlugin/
│   ├── include/
│   │   └── MyPlugin.h
│   ├── src/
│   │   ├── MyPlugin.cpp
│   │   └── MyPluginBinding.cpp
│   ├── platform/
│   │   ├── android/
│   │   ├── ios/
│   │   └── mac/
│   └── CMakeLists.txt
```

### TypeScript Declaration
```typescript
declare namespace myplugin {
    function initialize(): void;
    function processData(data: number[]): number[];
    function getPlatformInfo(): string;
}

// Wrapper with fallback
export class PluginWrapper {
    initialize(): void {
        if (typeof myplugin !== 'undefined') {
            myplugin.initialize();
        } else {
            console.warn('Native plugin not available in web');
        }
    }
}
```

### Performance Pattern
```typescript
// BAD: Many JSB calls
for (let i = 0; i < 1000; i++) {
    native.processPoint(points[i]);  // High overhead per call
}

// GOOD: Batch data
native.processPoints(points);  // One JSB call
```

### TypedArrays for Large Data
```typescript
const data = new Float32Array(10000);
native.processFloatArray(data);  // Efficient native transfer
```

## Platform Considerations

| Platform | Notes |
|----------|-------|
| Android | JNI layer, CMake build |
| iOS | Objective-C++ (.mm files) |
| Windows | Direct native API |
| macOS | Objective-C++ layer |
| HarmonyOS | Full support (3.8+) |
| Web | Must provide JS fallback |

## Common Mistakes
- Frequent JSB calls in hot paths (boundary overhead)
- No web/editor fallback (broken in browser)
- Memory leaks from unreleased native objects
- Blocking main thread with synchronous native calls