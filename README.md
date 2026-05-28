# [Radio.Fish](https://radio.fish) — Android Beta Testing

Manual QA of a React Native radio streaming app for Android.  
Testing conducted on a real Samsung device (Android 14, One UI).

---

## About the App

**Radio.Fish** is a single-station radio streaming app with:
- Live audio stream in two formats: AAC 64kbps / MP3 192kbps
- Like tracks → save to Favourites
- Playback history with links to YouTube, Spotify, Apple Music
- News feed sourced from a Telegram channel

**Platform:** Android (React Native, targetSDK 35)  
**Test type:** Beta testing via Google Play invitation  
**Testing period:** May 2025

---

## Tools

| Tool | Purpose |
|---|---|
| ADB + Android Studio Logcat | Crash analysis, log collection |
| Charles Proxy | Network traffic interception |
| Real device: Samsung (Android 14) | Chosen specifically for Samsung One UI background process behaviour |

---

## What Was Tested

- [Test Plan & Test Cases](test-plan/test-cases.md)

Key areas:
- App launch and stability across versions
- Audio playback and format switching (AAC64 / MP3192)
- Audio Focus behaviour (calls, notifications, other audio apps)
- Network conditions (offline, slow connection, server errors)
- System interruptions (calls, lock screen, background)
- Widget behaviour
- Favourites, history, third-party app integrations
- UI / navigation

---

## Bug Summary

| ID | Title | Severity | Status |
|---|---|---|---|
| [BUG-01](bug-reports/BUG-01-SecurityException.md) | App crashes on launch — SecurityException (Android 13+ incompatibility) | Critical | ✅ Fixed |
| [BUG-02](bug-reports/BUG-02-NoSuchFieldError.md) | App crashes — NoSuchFieldError, ExoPlayer version conflict, crash loop | Critical | ✅ Fixed |
| [BUG-03](bug-reports/BUG-03-ForegroundServiceType.md) | MissingForegroundServiceTypeException on every launch (Android 14) | Critical | 🔴 Open |
| [BUG-04](bug-reports/BUG-04-AudioFocus.md) | Audio plays simultaneously with calls, Telegram audio, Instagram video | High | 🔴 Open |
| [BUG-05](bug-reports/BUG-05-Widget.md) | Widget: album art delay, ghost Back button, widget disappears | Medium | 🔴 Open |
| [BUG-06](bug-reports/BUG-06-UI-link.md) | Site link inaccessible — overlapped by Android system navigation bar | High | 🔴 Open |

---

## Key Findings

**Crash progression across versions:**

Each release fixed one crash but introduced another — a sign of dependency updates without full regression testing on the target Android version.

```
v1 → SecurityException       ExoPlayer2 + Android 13: missing RECEIVER_EXPORTED flag
v2 → SecurityException       Same crash, partial fix only
v3 → NoSuchFieldError        ExoPlayer version conflict, crash loop every ~2 sec
v4 → App launches ✓          MissingForegroundServiceTypeException persists in background
```

**Audio Focus not implemented:**  
Radio plays simultaneously with incoming calls (silent mode), Telegram audio messages, and Instagram videos. Confirmed via `adb logcat | grep -i audiofocus` — no focus request or loss handling observed from the app.

**Samsung-specific behaviour:**  
`FreecessController` (Samsung One UI) killed `MusicService` after ~4 min of inactivity — confirmed in logcat. Would not be reproducible on emulator or Pixel device.

---

## Recommendations to Developers

1. Migrate from ExoPlayer 2 to **Media3** (`androidx.media3`) — resolves compatibility issues with Android 13/14 out of the box
2. Add `foregroundServiceType="mediaPlayback"` to `AndroidManifest.xml`
3. Implement `AudioFocusRequest` with `onAudioFocusChange` callback
4. Request `POST_NOTIFICATIONS` permission at runtime (Android 13+)
5. Test on Samsung device — background service behaviour differs from stock Android

---

## Repository Structure

```
radiofish-beta-testing/
├── README.md
├── test-plan/
│   └── test-cases.md
├── bug-reports/
│   ├── BUG-01-SecurityException.md
│   ├── BUG-02-NoSuchFieldError.md
│   ├── BUG-03-ForegroundServiceType.md
│   ├── BUG-04-AudioFocus.md
│   ├── BUG-05-Widget.md
│   └── BUG-06-UI-link.md
├── logs/
│   ├── crash-v1-SecurityException.txt
│   ├── crash-v2-NoSuchFieldError.txt
│   └── crash-v3-ForegroundServiceType.txt
└── media/
    ├── widget-disappears.mp4
    └── link-overlapped-navbar.png
```