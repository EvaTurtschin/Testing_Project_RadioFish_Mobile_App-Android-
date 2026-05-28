# BUG-01: App crashes on launch — SecurityException (Android 13+)

**Severity:** Critical  
**Status:** ✅ Fixed in v3  
**Affected versions:** v1, v2 (partial fix only)  
**Discovered:** May 2025

---

## Steps to Reproduce

1. Install app via Google Play beta invitation
2. Launch the app

## Expected Result

App opens normally, main screen loads.

## Actual Result

App crashes immediately. System dialog: "App keeps stopping".  
No content is shown.

---

## Stack Trace

```
E AndroidRuntime: FATAL EXCEPTION: main
E AndroidRuntime: Process: com.radiofish.app, PID: 12185
E AndroidRuntime: java.lang.SecurityException: com.radiofish.app:
  One of RECEIVER_EXPORTED or RECEIVER_NOT_EXPORTED should be specified
  when a receiver isn't being registered exclusively for system broadcasts
E AndroidRuntime:   at com.google.android.exoplayer2.ui.PlayerNotificationManager
                       .startOrUpdateNotification(PlayerNotificationManager.java:1165)
E AndroidRuntime:   at com.google.android.exoplayer2.ui.PlayerNotificationManager
                       .handleMessage(PlayerNotificationManager.java:1415)
```

Full log: [crash_v1_SecurityException.txt](../logs/01_Crash_v1_SecurityException.txt)

---

## Root Cause

`PlayerNotificationManager` (ExoPlayer 2) registers a `BroadcastReceiver`  
without specifying `RECEIVER_EXPORTED` or `RECEIVER_NOT_EXPORTED` flag.  
This flag is mandatory since Android 13 (API 33).

## Fix Recommendation

- Upgrade ExoPlayer 2 to **Media3** (`androidx.media3`) — issue resolved in newer versions
- Or patch `PlayerNotificationManager` to pass the required flag in `registerReceiver()`

---

## Notes

v2 attempted a fix but the crash persisted at the same location (`PlayerNotificationManager.java:1165`).  
Fully resolved in v3.