# Cocos Audio — Quick Reference

Last verified: 2026-03-27 | Engine: Cocos Creator 3.8

## What Changed Since ~3.7 (LLM Cutoff)

### 3.8 Changes
- **HarmonyOS Next**: Native audio playback fixes
- Improved audio system stability

### 2.x → 3.x Migration
- `cc.audioEngine` removed entirely
- Use `AudioSource` component for all audio playback
- API changes: `getLoop()/setLoop()` → `loop` property

## Current API Patterns

### AudioSource Component
```typescript
@property({ type: AudioSource })
audioSource: AudioSource | null = null;

playSound() {
    if (this.audioSource) {
        this.audioSource.play();
    }
}

pauseSound() {
    this.audioSource?.pause();
}

stopSound() {
    this.audioSource?.stop();
}
```

### Audio Properties
```typescript
audioSource.clip = audioClip;      // Audio clip asset
audioSource.loop = true;           // Loop playback
audioSource.volume = 0.8;          // Volume 0-1
audioSource.mute = false;          // Mute
audioSource.playOnAwake = true;    // Auto-play
```

### One-shot Audio
```typescript
// For sound effects without persistent AudioSource
audioSource.playOneShot(audioClip, volume);
```

### Audio Manager Pattern
```typescript
export class AudioManager extends Component {
    private static _instance: AudioManager;

    @property({ type: AudioSource })
    musicSource: AudioSource | null = null;

    @property({ type: AudioSource })
    sfxSource: AudioSource | null = null;

    static get instance(): AudioManager {
        return AudioManager._instance;
    }

    onLoad() {
        AudioManager._instance = this;
    }

    playMusic(clip: AudioClip) {
        if (this.musicSource) {
            this.musicSource.clip = clip;
            this.musicSource.play();
        }
    }

    playSFX(clip: AudioClip) {
        this.sfxSource?.playOneShot(clip);
    }
}
```

## Common Mistakes
- Using `cc.audioEngine` APIs (removed in 3.x)
- Not setting up AudioSource component in scene
- Playing long audio files as one-shot (use dedicated AudioSource for music)