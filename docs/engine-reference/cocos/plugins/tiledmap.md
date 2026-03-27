# Cocos Creator 3.8 — Tiled Map

**Last verified:** 2026-03-27
**Status:** Production-Ready
**Extension:** Built-in (tiled-map)

---

## Overview

**Tiled Map** is a popular 2D tile map editor. Cocos Creator provides built-in
support for importing and rendering Tiled maps (.tmx files).

**Use Tiled Map for:**
- 2D platformer levels
- RPG world maps
- Puzzle game grids
- Strategy game terrain

**Knowledge Gap:** Tiled map import workflow improved in 3.x.
Model may suggest 2.x patterns.

---

## Core Concepts

### 1. **TiledMap Component**
- Main component for rendering Tiled maps
- Parses .tmx files and renders tile layers

### 2. **TMX Asset**
- Map file exported from Tiled Editor
- Contains layer data, tilesets, object groups

### 3. **Tile Layers**
- Visual layers containing tiles
- Rendered by TiledMap component

### 4. **Object Groups**
- Non-visual layers for game logic
- Colliders, spawn points, triggers

---

## Installation

### Install Tiled Editor

Tiled Map is an external tool. Install the Tiled Editor first:

```
1. Download Tiled Editor: https://www.mapeditor.org/
   - Windows: Installer or Portable
   - macOS: DMG or Homebrew (brew install tiled)
   - Linux: Flatpak or package manager

2. Create your map in Tiled Editor:
   - Choose orientation: Orthogonal (most common for 2D)
   - Set tile size (e.g., 32x32, 64x64)
   - Set map size (width x height in tiles)

3. Add tilesets and create layers

4. Export as .tmx format
```

### Import to Cocos Creator

Tiled Map support is built-in. No extension installation required.

```
1. Drag .tmx file to assets folder
2. Drag associated tileset images (.png) to same folder
3. Keep all resources in same directory
4. Cocos auto-creates TiledMapAsset
```

**Important:** The .tmx file, .tsx tileset files, and .png images must be in the same directory for correct import.

---

## Setup

### Add TiledMap Component

```typescript
import { _decorator, Component, TiledMap } from 'cc';
const { ccclass, property } = _decorator;

@ccclass('LevelController')
export class LevelController extends Component {
    @property({ type: TiledMap })
    tiledMap: TiledMap | null = null;

    start() {
        if (this.tiledMap) {
            // Access map data
            const mapSize = this.tiledMap.getMapSize();
            console.log(`Map size: ${mapSize.width}x${mapSize.height}`);
        }
    }
}
```

---

## API Reference

### Accessing Layers

```typescript
// Get tile layer
const groundLayer = this.tiledMap.getLayer('Ground');
if (groundLayer) {
    // Get tile at position (in tile coordinates)
    const gid = groundLayer.getTileGIDAt(x, y);

    // Set tile
    groundLayer.setTileGIDAt(newGid, x, y);

    // Remove tile
    groundLayer.removeTileAt(x, y);
}

// Get object group (for colliders, spawn points, etc.)
const objectGroup = this.tiledMap.getObjectGroup('Colliders');
if (objectGroup) {
    const objects = objectGroup.getObjects();
    for (const obj of objects) {
        console.log(`Object: ${obj.name} at (${obj.x}, ${obj.y})`);
    }
}
```

### Map Properties

```typescript
// Map size in tiles
const mapSize = this.tiledMap.getMapSize();
// Tile size in pixels
const tileSize = this.tiledMap.getTileSize();

// Convert tile coordinates to world position
const worldPos = groundLayer.getPositionAt(tileX, tileY);

// Convert world position to tile coordinates
const tilePos = groundLayer.getTilePosition(worldX, worldY);
```

### Object Group Parsing

```typescript
parseSpawnPoints(): void {
    const spawnGroup = this.tiledMap.getObjectGroup('SpawnPoints');
    if (!spawnGroup) return;

    const objects = spawnGroup.getObjects();
    for (const obj of objects) {
        const type = obj.getProperty('type') as string;
        const x = obj.offset.x;
        const y = obj.offset.y;

        switch (type) {
            case 'player':
                this.spawnPlayer(x, y);
                break;
            case 'enemy':
                this.spawnEnemy(x, y);
                break;
        }
    }
}
```

---

## Collision Setup

### Using Object Layers for Colliders

```
1. In Tiled Editor:
   - Create Object Layer named "Colliders"
   - Draw rectangles over solid areas
   - Add property "collider" = "solid"

2. In Cocos:
   - Parse object layer
   - Create physics colliders from objects
```

```typescript
setupColliders(): void {
    const colliderGroup = this.tiledMap.getObjectGroup('Colliders');
    if (!colliderGroup) return;

    const objects = colliderGroup.getObjects();
    for (const obj of objects) {
        // Create node with collider
        const node = new Node('Collider');
        const collider = node.addComponent(BoxCollider2D);

        // Set collider size from Tiled object
        collider.size.width = obj.width;
        collider.size.height = obj.height;

        // Position in world space
        node.setPosition(obj.offset.x, obj.offset.y);
    }
}
```

---

## Performance Tips

### Tileset Optimization
- Use single tileset per map when possible
- Pack tiles into texture atlas
- Limit tileset size to 2048x2048

### Layer Optimization
- Minimize number of layers (3-5 typical)
- Use object layers only for necessary data
- Consider merging visual layers

### Culling
```typescript
// Enable culling for off-screen tiles (default: true)
tiledMap.enableCulling = true;
```

---

## Common Issues

### Tiles Not Rendering
1. Check tileset image path is correct
2. Verify tileset is in same folder as .tmx
3. Ensure image import settings are correct

### Object Position Offset
- Tiled Y-axis points down; Cocos Y-axis points up
- May need to invert Y: `y = mapHeight - tiledY`

### Large Map Performance
- Split into chunks
- Load chunks dynamically
- Use object pooling for dynamic tiles

---

## Dynamic Tile Modification

### Runtime Tile Changes

```typescript
// Change tiles at runtime
updateTile(x: number, y: number, newGid: number): void {
    const layer = this.tiledMap.getLayer('Ground');
    if (layer) {
        layer.setTileGIDAt(newGid, x, y);
    }
}

// Remove tile (create hole/destroyed terrain)
removeTile(x: number, y: number): void {
    const layer = this.tiledMap.getLayer('Ground');
    if (layer) {
        layer.removeTileAt(x, y);
    }
}

// Get tile info for game logic
getTileType(x: number, y: number): string {
    const layer = this.tiledMap.getLayer('Ground');
    if (!layer) return 'empty';

    const gid = layer.getTileGIDAt(x, y);
    // Use tile GID to determine type
    // GID 0 = empty, other values = specific tiles
    return this.gidToType[gid] || 'unknown';
}
```

### Tile Property Access

```typescript
// Access custom tile properties defined in Tiled
getTileProperty(x: number, y: number, propName: string): any {
    const layer = this.tiledMap.getLayer('Ground');
    if (!layer) return null;

    const gid = layer.getTileGIDAt(x, y);
    const tileset = this.tiledMap.getTilesetByGID(gid);
    if (!tileset) return null;

    // Get properties from tileset
    const tileData = tileset.getTileData(gid - tileset.firstGid);
    return tileData?.properties?.[propName];
}
```

---

## Debugging

### Visual Debug Mode

```typescript
// Enable culling visualization
tiledMap.enableCulling = true;

// Debug: Show map bounds
debugMapBounds(): void {
    const mapSize = this.tiledMap.getMapSize();
    const tileSize = this.tiledMap.getTileSize();

    console.log(`Map: ${mapSize.width}x${mapSize.height} tiles`);
    console.log(`Tile: ${tileSize.width}x${tileSize.height} pixels`);
    console.log(`World: ${mapSize.width * tileSize.width}x${mapSize.height * tileSize.height} pixels`);
}
```

### Common Debug Scenarios

| Issue | Debug Method |
|-------|--------------|
| Tiles not rendering | Check tileset image path, verify .png import |
| Wrong tile positions | Remember Tiled Y-axis down, Cocos Y-axis up |
| Object layer empty | Check object group name matches exactly |
| Collision offset | Verify object coordinates vs tile coordinates |
| Missing properties | Check property names in Tiled Editor |

### Inspector Properties

In Cocos Editor, select TiledMap node to view:
- **Tmx Asset**: The .tmx file reference
- **Enable Culling**: Optimize off-screen tile rendering

### Validate Map Structure

```typescript
validateMap(): void {
    // Check all layers exist
    const expectedLayers = ['Ground', 'Decorations', 'Colliders'];
    for (const name of expectedLayers) {
        const layer = this.tiledMap.getLayer(name);
        if (!layer) {
            console.error(`Missing layer: ${name}`);
        }
    }

    // Check object groups
    const spawnGroup = this.tiledMap.getObjectGroup('SpawnPoints');
    if (!spawnGroup) {
        console.warn('No spawn points defined');
    }
}
```

---

## Sources
- https://docs.cocos.com/creator/3.8/manual/en/assets/tiledmap.html
- https://doc.mapeditor.org/en/stable/