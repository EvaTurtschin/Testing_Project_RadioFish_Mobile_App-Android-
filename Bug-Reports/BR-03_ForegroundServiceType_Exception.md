# BUG-03: MissingForegroundServiceTypeException on every launch (Android 14)

**Severity:** Critical  
**Status:** 🔴 Open  
**Affected versions:** v4  
**Discovered:** May 2025

---

## Steps to Reproduce

1. Install v4 of the app via Google Play beta
2. Launch the app
3. Check logcat

## Expected Result

App launches without exceptions. MusicService starts as a foreground service silently.

## Actual Result

App launches and is usable, but `FATAL EXCEPTION` is thrown in the background  
on every launch. `MusicService` starts foreground service without specifying a type —  
violates Android 14 requirements.

```
E AndroidRuntime: FATAL EXCEPTION
E AndroidRuntime: android.app.MissingForegroundServiceTypeException:
  Starting FGS without a type
  at MusicService.kt:392 — startForeground()
```

Full log: [crash_v3_ForegroundServiceTypeException.txt](../logs/03_Crash_v3_ForegroundServiceTypeException.txt)

---

## Root Cause

Android 14 (API 34) requires `foregroundServiceType` to be declared  
in `AndroidManifest.xml` and passed to `startForeground()`.  
For audio playback apps the correct type is `mediaPlayback`.

## Fix Recommendation

1. Add to `AndroidManifest.xml`:
```xml
<service
    android:name=".MusicService"
    android:foregroundServiceType="mediaPlayback" />
```

2. Update `startForeground()` call in `MusicService.kt:392`:
```kotlin
startForeground(
    NOTIFICATION_ID,
    notification,
    ServiceInfo.FOREGROUND_SERVICE_TYPE_MEDIA_PLAYBACK
)
```

---

## Notes

App remains functional despite the exception on tested Samsung device (Android 14).  
Behaviour on other devices or Android versions may differ —  
the exception could cause a crash or service termination.