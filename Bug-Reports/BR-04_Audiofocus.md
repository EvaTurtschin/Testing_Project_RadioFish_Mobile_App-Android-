# BUG-04: Audio plays simultaneously with calls, Telegram audio, Instagram video

**Severity:** High  
**Status:** 🔴 Open  
**Affected versions:** v4  
**Discovered:** May 2025

---

## Steps to Reproduce

**Scenario A — Incoming call (silent mode):**
1. Start radio playback
2. Receive an incoming call (phone in silent mode)
3. Observe audio behaviour

**Scenario B — Telegram audio message:**
1. Start radio playback
2. Open Telegram and play an audio message

**Scenario C — Instagram video:**
1. Start radio playback
2. Open Instagram and play a video with sound

## Expected Result

- Scenario A: Radio pauses when call is received, resumes after call ends
- Scenario B: Radio pauses or reduces volume during audio message, resumes after
- Scenario C: Radio pauses when Instagram video starts playing

## Actual Result

In all three scenarios: **radio continues playing at full volume simultaneously**  
with the other audio source. No pause, no ducking.

---

## Investigation

Checked Audio Focus events via logcat:

```bash
adb logcat | grep -i audiofocus
```

No `AudioFocus` requests or loss handling observed from `com.radiofish.app`.  
This indicates `AudioFocusRequest` is either not implemented or  
`onAudioFocusChange` callback is not handled.

---

## Root Cause

Audio Focus mechanism not implemented. Android requires apps to:
1. Request `AUDIOFOCUS_GAIN` when starting playback
2. Handle `AUDIOFOCUS_LOSS_TRANSIENT` — pause playback
3. Handle `AUDIOFOCUS_LOSS_TRANSIENT_CAN_DUCK` — reduce volume
4. Handle `AUDIOFOCUS_GAIN` after loss — resume playback

None of these are handled in the current version.

## Fix Recommendation

Implement `AudioFocusRequest` in `MusicService`:

```kotlin
val focusRequest = AudioFocusRequest.Builder(AudioManager.AUDIOFOCUS_GAIN)
    .setOnAudioFocusChangeListener { focusChange ->
        when (focusChange) {
            AudioManager.AUDIOFOCUS_LOSS_TRANSIENT -> pause()
            AudioManager.AUDIOFOCUS_LOSS_TRANSIENT_CAN_DUCK -> lowerVolume()
            AudioManager.AUDIOFOCUS_GAIN -> resume()
            AudioManager.AUDIOFOCUS_LOSS -> stop()
        }
    }
    .build()
```

---

## Impact

High user impact — simultaneous audio from radio and other sources  
is a core usability issue for a background audio app.  
Affects all standard Android interaction patterns: calls, messaging, other media.