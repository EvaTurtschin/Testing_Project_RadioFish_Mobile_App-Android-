# BUG-05: Widget — album art delay, ghost Back button, widget disappears

**Severity:** Medium  
**Status:** 🔴 Open  
**Affected versions:** v4  
**Discovered:** May 2025

---

## Issue 1 — Album art not shown immediately in widget

**Steps to reproduce:**
1. Start playback
2. Minimize app to widget (notification bar)
3. Observe widget immediately after minimizing

**Expected:** Album art is displayed immediately  
**Actual:** Widget shows without album art; image loads after a delay.  
Workaround: expand app and minimize again — art loads on second attempt.

---

## Issue 2 — Ghost "Back" button appears after unpausing

**Steps to reproduce:**
1. Start playback
2. Minimize to widget
3. Tap Pause in the widget
4. Tap Play to resume

**Expected:** Only Play button is shown while paused; only Pause after resuming  
**Actual:** For ~1 second after resuming, both **Play and Back buttons are visible simultaneously**

Back button should not exist in this app — it is a live radio stream with no rewind functionality.  
This suggests incorrect media session action configuration.

---

## Issue 3 — Widget disappears for several seconds

**Steps to reproduce:**
1. Start playback
2. Minimize to widget
3. Observe widget over time

**Expected:** Widget is stable and always visible  
**Actual:** Widget disappears from notification bar for 2–5 seconds, then reappears.  
Recorded on video: [media/widget-disappears.mp4](../media/widget-disappears.mp4)

---

## Root Cause (likely)

- Issue 1: Album art loaded asynchronously without placeholder or preloading
- Issue 2: `MediaSession` actions not configured correctly — `ACTION_SKIP_TO_PREVIOUS` should be removed for a live stream
- Issue 3: Notification rebuild on state change causes temporary removal from notification bar

## Fix Recommendation

- Issue 1: Preload album art before updating notification, add placeholder
- Issue 2: Remove `ACTION_SKIP_TO_PREVIOUS` from `MediaSession` for live stream
- Issue 3: Update notification in-place instead of removing and re-adding