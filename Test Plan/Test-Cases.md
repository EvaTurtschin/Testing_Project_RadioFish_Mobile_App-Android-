# Test Cases — Radio.Fish Android Beta

---

## 🔴 Smoke

| ID | Test Case | Tool |
|---|---|---|
| S-01 | App installs without errors | Play Store / ADB |
| S-02 | First launch — no crash, main screen loads | logcat |
| S-03 | Stream starts, audio is heard | Manual |
| S-04 | Pause / resume work | Manual |
| S-05 | Current track metadata displayed (title, album art) | Manual |
| S-06 | Like a track — track appears in Favourites | Manual |
| S-07 | Playback history loads | Manual |

---

## 🟠 Priority 2 — Playback

| ID | Test Case | What to Check |
|---|---|---|
| P-01 | Stream starts from cold launch | Time to first audio < 3–5 sec |
| P-02 | Long playback session (30+ min) | No crash, no memory leak |
| P-03 | Pause → resume | Resumes at live position, not from pause point |
| P-04 | Volume buttons | Work correctly during playback |
| P-05 | Switch AAC64 → MP3192 during playback | Stream restarts without crash |
| P-06 | Switch format → minimize → maximize | Selected format is preserved |
| P-07 | Wired headphones connected | Audio switches correctly |
| P-08 | Bluetooth headphones connected | No dropouts or delays |
| P-09 | Unplug headphones during playback | Playback pauses (Android standard) |
| P-10 | Controls in notification bar | Play/Pause work; no Back button |
| P-11 | Controls on lock screen | Play/Pause work |

---

## 🟠 Priority 2 — Favourites & History

| ID | Test Case | Tool |
|---|---|---|
| FL-01 | Like a track → appears in Favourites | Manual + Charles |
| FL-02 | Remove like → track removed from Favourites | Manual + Charles |
| FL-03 | Double-tap like quickly | Charles: 1 request sent, not 2 |
| FL-04 | Like a track while offline | Error shown or queued? |
| FL-05 | Open track from Favourites in YouTube | Correct search opens |
| FL-06 | Open track from Favourites in Spotify / Apple Music | Correct search opens |
| FL-07 | Track with special characters in title → open in external app | No crash, correct search |
| FL-08 | Open track from History in external app | Correct search opens |
| FL-09 | History persists after app restart | Manual |
| FL-10 | Track with no metadata in history | Placeholder shown, no crash |

---

## 🟡 Priority 3 — Network

| ID | Test Case | How to Reproduce |
|---|---|---|
| N-01 | Launch with no internet | Disable Wi-Fi + mobile data |
| N-02 | Lose network during playback | Disable Wi-Fi |
| N-03 | Restore network — does playback auto-resume? | Re-enable Wi-Fi |
| N-04 | Switch Wi-Fi → mobile data during stream | Manually in settings |
| N-05 | Slow network — AAC64 buffering | Charles → Throttle → 3G |
| N-06 | Slow network — MP3192 buffering | Charles → Throttle → 3G |
| N-07 | Switch format on slow network | Charles Throttle enabled |
| N-08 | Stream server returns 500 | Charles → Rewrite → 500 |

**Charles — what to verify:**
- AAC64 and MP3192 use different stream endpoints?
- HTTP or HTTPS?
- Response code: 200 or 206 Partial Content?
- Content-Type: `audio/mpeg` or `audio/aac`?
- How track metadata arrives — separate request or stream headers?
- Old stream request closed when format is switched?
- Like request — what's in the body? Any auth token?

---

## 🟡 Priority 3 — System Interruptions

| ID | Test Case | Expected Behaviour |
|---|---|---|
| I-01 | Incoming call during playback | Radio pauses; resumes after call ends |
| I-02 | Incoming call in silent mode | Radio pauses (Audio Focus is independent of ringer mode) |
| I-03 | Notification with sound from another app | Radio ducks (lowers volume briefly) or pauses |
| I-04 | Minimize → return after 5 min | Playback continues uninterrupted |
| I-05 | Minimize → open YouTube → play video → return | Radio pauses; does not auto-resume after YouTube |
| I-06 | Telegram audio message during playback | Radio pauses; resumes after message ends |
| I-07 | Lock screen | Radio continues playing |
| I-08 | Screen rotation | Playback not interrupted |
| I-09 | Do Not Disturb mode | Radio not silenced |

---

## 🟢 Priority 4 — Widget

| ID | Test Case | Status |
|---|---|---|
| W-01 | Album art displayed immediately on minimize | 🔴 BUG — loads with delay |
| W-02 | After pause → resume: only Play shown, no Back button | 🔴 BUG — Back briefly appears |
| W-03 | Widget stays visible consistently in notification bar | 🔴 BUG — disappears for seconds |
| W-04 | Play/Pause in widget work correctly | Manual |
| W-05 | Widget shows correct metadata for current track | Manual |
| W-06 | Widget updates when track changes | Manual |

---

## 🟢 Priority 4 — News / Telegram

| ID | Test Case | Tool |
|---|---|---|
| TG-01 | News content loads correctly | Manual + Charles |
| TG-02 | Telegram button opens correct channel | Manual |
| TG-03 | Telegram not installed — button opens web version or shows error | Manual |
| TG-04 | Offline — cached content shown or error message | Disable network |
| TG-05 | Opening News section does not interrupt playback | Manual |

---

## 🟢 Priority 4 — UI

| ID | Test Case | Status |
|---|---|---|
| U-01 | Website link not overlapped by Android navigation bar | 🔴 BUG |
| U-02 | Large system font size (Accessibility → Large/XL) — layout intact | Manual |
| U-03 | Track artwork loads, no broken images | Manual |
| U-04 | Track with no artwork — placeholder shown | Manual |
| U-05 | Track metadata updates in real time on track change | Manual |