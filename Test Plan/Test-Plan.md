# Test Plan — Radio.Fish Android Beta

**Version:** 1.0  
**Date:** May 2025  
**Tester:** Eva Turtschin  
**Type:** Manual beta testing  

---

## 1. Objective

Validate core functionality, stability, and usability of the Radio.Fish Android app  
across multiple beta releases. Identify crashes, functional defects, and UX issues  
before public release.

---

## 2. Scope

### In scope
- App installation and launch stability
- Audio playback and stream format switching (AAC64 / MP3192)
- Audio Focus behaviour (system interruptions)
- Network conditions and error handling
- Widget (notification bar player) behaviour
- Favourites and playback history
- Third-party integrations (YouTube, Spotify, Apple Music, Telegram)
- UI layout and accessibility

### Out of scope
- Automated testing
- Performance benchmarking
- Security / penetration testing
- iOS version

---

## 3. Test Environment

| Parameter | Value |
|---|---|
| Device | Samsung (real device) |
| OS | Android 14 (One UI) |
| App | Radio.Fish — React Native, targetSDK 35 |
| Distribution | Google Play beta (invitation) |
| Tools | ADB, Android Studio Logcat, Charles Proxy |

**Why real Samsung device (not emulator):**  
Samsung One UI applies aggressive background process management via `FreecessController`,  
which kills services that stock Android would keep alive. Critical for a background audio app.

---

## 4. Test Approach

1. **Smoke first** — confirm app launches and plays audio before deeper testing
2. **Logcat on every launch** — collect crash data from day one
3. **Version tracking** — re-test fixed bugs on each new release, check for regressions
4. **Charles Proxy** — inspect network traffic for stream URLs, metadata, API calls
5. **Real interaction scenarios** — test Audio Focus with actual calls, Telegram, other apps

---

## 5. Entry Criteria

- App installed via Google Play beta link
- ADB connection established (`adb devices` shows device)
- Charles Proxy configured on device Wi-Fi

## 6. Exit Criteria

- All Critical bugs resolved
- Smoke test suite passes
- Audio Focus behaves correctly for calls and other audio apps
- Widget stable across standard usage scenarios

---

## 7. Risk Areas

| Risk | Why |
|---|---|
| Android 14 compatibility | targetSDK=35 — strict foreground service and receiver requirements |
| Samsung background management | `FreecessController` kills services more aggressively than stock Android |
| ExoPlayer version conflicts | Multiple dependency upgrades in progress across beta versions |
| Audio Focus | Not implemented as of v4 — affects core use case |

---

## 8. Test Cases

See [test-cases.md](test-cases.md)

---

## 9. Bug Reports

| ID | Title | Severity | Status |
|---|---|---|---|
| [BUG-01](../bug-reports/BUG-01-SecurityException.md) | SecurityException on launch (Android 13+) | Critical | ✅ Fixed |
| [BUG-02](../bug-reports/BUG-02-NoSuchFieldError.md) | NoSuchFieldError — ExoPlayer conflict, crash loop | Critical | ✅ Fixed |
| [BUG-03](../bug-reports/BUG-03-ForegroundServiceType.md) | MissingForegroundServiceTypeException (Android 14) | Critical | 🔴 Open |
| [BUG-04](../bug-reports/BUG-04-AudioFocus.md) | Audio plays simultaneously with calls and other apps | High | 🔴 Open |
| [BUG-05](../bug-reports/BUG-05-Widget.md) | Widget: album art delay, ghost Back button, disappears | Medium | 🔴 Open |
| [BUG-06](../bug-reports/BUG-06-UI-link.md) | Site link overlapped by Android navigation bar | High | 🔴 Open |