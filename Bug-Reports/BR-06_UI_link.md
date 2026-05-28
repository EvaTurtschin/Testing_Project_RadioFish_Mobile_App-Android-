# BUG-06: Site link inaccessible — overlapped by Android system navigation bar

**Severity:** High  
**Status:** 🔴 Open  
**Affected versions:** v4  
**Discovered:** May 2025

---

## Steps to Reproduce

1. Open the app
2. Navigate to the screen containing the website link
3. Attempt to tap the link

## Expected Result

Link is visible and tappable. Tapping opens the website.

## Actual Result

The link is positioned at the very bottom of the screen and is **physically overlapped  
by the Android system navigation bar** (Home / Back / Recent Apps buttons).  
The element cannot be tapped. No scroll is available to reach it.

Screenshot: [media/link-overlapped-navbar.png](../media/link-overlapped-navbar.png)

---

## Root Cause

UI layout does not account for system window insets.  
Content is positioned without padding for the navigation bar height,  
causing the bottom element to be hidden behind the system UI.

## Fix Recommendation

Apply `WindowInsetsCompat` to add bottom padding equal to navigation bar height:

```kotlin
ViewCompat.setOnApplyWindowInsetsListener(rootView) { view, insets ->
    val navBarHeight = insets.getInsets(WindowInsetsCompat.Type.navigationBars()).bottom
    view.setPadding(0, 0, 0, navBarHeight)
    insets
}
```

Or in Jetpack Compose:
```kotlin
Modifier.navigationBarsPadding()
```

---

## Impact

Element is completely inaccessible — user cannot reach the link at all.  
Severity is High despite being a UI issue because the feature is entirely blocked,  
not just visually degraded.

## Device Info

Tested on: Samsung (Android 14, gesture navigation enabled)  
Issue likely affects all devices with gesture navigation or tall navigation bars.