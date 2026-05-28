# BUG-02: App crashes — NoSuchFieldError, ExoPlayer version conflict

**Severity:** Critical  
**Status:** ✅ Fixed in v4  
**Affected versions:** v3  
**Discovered:** May 2025

---

## Steps to Reproduce

1. Install v3 of the app via Google Play beta
2. Launch the app

## Expected Result

App opens normally.

## Actual Result

App crashes immediately and enters a **crash loop** —  
`MusicService` restarts every ~2 seconds and crashes again.  
6+ crash cycles observed within 30 seconds.

---

## Stack Trace

```
E AndroidRuntime: FATAL EXCEPTION: main
E AndroidRuntime: Process: com.radiofish.app
E AndroidRuntime: java.lang.NoSuchFieldError:
  No field playbackProperties of type
  Lcom/google/android/exoplayer2/MediaItem$PlaybackProperties
  in com.google.android.exoplayer2.MediaItem
W ActivityManager: Rescheduling restart of crashed service
  com.radiofish.app/com.doublesymmetry.trackplayer.service.MusicService
  in 1000ms
```

Full log: [crash_v2_NoSuchFieldError.txt](../logs/02_Crash_v2_NoSuchFieldError.txt)

---

## Root Cause

Field `playbackProperties` was removed/renamed in a newer version of ExoPlayer 2,  
but another dependency (`react-native-track-player`) still references the old field name.  
This is a version mismatch between ExoPlayer modules.

## Fix Recommendation

- Align all ExoPlayer-related dependency versions
- Migrate to **Media3** (`androidx.media3`) to avoid future compatibility issues

---

## Notes

Crash loop behaviour makes this worse than BUG-01 —  
the service consumes resources continuously until the process is killed.