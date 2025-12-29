# Camera Splash Ads – Aggressive Pi-hole / ABP Filter List

## Overview

This repository provides an **aggressive ABP-style blocklist** designed to eliminate
**splash screen video ads and forced advertisements** commonly found in
**camera, IoT, and CCTV mobile applications**.

These ads typically appear **before access to live video**, often with
fake countdown timers or misleading “skip” buttons, which makes them
especially problematic in **security and surveillance contexts**.

This list was created to address a **real operational problem** where
traditional DNS-based ad blocking (including standard Pi-hole lists)
proved insufficient.

---

## Why this list exists

Many modern camera and IoT apps implement advertising in a way that is
**deliberately resistant to DNS-based blocking**:

- Ads are served from the **same CDN infrastructure** as legitimate content
- Video ads are delivered via **neutral, large-scale CDNs** (e.g. ByteDance / Pangle)
- Ad SDKs use **CNAME cloaking, shared domains, and HTTPS-only delivery**
- Blocking only the obvious ad domains is no longer enough

As a result:
- Standard blocklists (OISD, StevenBlack, Energized, etc.) do **not** stop these ads
- Pi-hole alone appears to be “not working”, even when correctly configured
- Endpoint-based blockers (e.g. AdGuard on-device) work, but are often **not deployable**

This list was created after **extensive real-world troubleshooting** to
provide a **network-level mitigation** where endpoint solutions are not possible.

---

## What this list does

This list aggressively blocks:

- Major mobile ad SDKs and mediation platforms:
  - Vungle
  - Mintegral
  - Liftoff
  - Rayjump
  - Pangle / TikTok Ads
- Video delivery CDNs used specifically for **splash and interstitial ads**
- ByteDance-related CDN domains frequently used to serve ad videos

The goal is **not cosmetic ad blocking**, but to:
- Prevent splash video ads from loading
- Force ad requests to timeout or fail
- Allow the app to continue to the main interface without showing ads

---

## What this list does NOT do

It is important to be explicit:

- ❌ This list does NOT guarantee compatibility with all apps
- ❌ This list is NOT suitable for global / network-wide use
- ❌ This list may break ad-supported apps unrelated to cameras
- ❌ This list does NOT replace endpoint-level content filtering

This list is a **targeted, aggressive workaround**, not a general-purpose adblocker.

---

## Recommended usage (IMPORTANT)

⚠️ **DO NOT APPLY THIS LIST GLOBALLY**

Best practice:

- Use **Pi-hole group management**
- Apply this list **only** to:
  - Mobile devices
  - Tablets
  - Dedicated camera monitoring devices
- Keep it isolated from general user traffic

This minimizes collateral damage while solving the specific problem.

---

## How to add this list to Pi-hole

### 1. Copy the RAW list URL

Use the RAW file URL, for example:

https://raw.githubusercontent.com/camammoli/lista_pihole/refs/heads/master/camera-splash-ads-aggressive.txt


*(Adjust filename if needed)*

---

### 2. Add to Pi-hole

1. Open the Pi-hole admin interface
2. Go to **Settings → Adlists**
3. Paste the RAW URL
4. Click **Save**
5. Go to **Tools → Update Gravity**

---

### 3. Assign to a group (recommended)

1. Go to **Group Management → Groups**
2. Create a group (e.g. `Cameras` or `Mobile-Cameras`)
3. Assign only the relevant devices
4. Assign this adlist to that group

---

## Expected behavior

When applied correctly:

- Splash video ads should **fail to load**
- The app may:
  - briefly show a black screen
  - pause for 1–2 seconds
  - then proceed to the camera view
- Core camera functionality should remain intact

This behavior is considered **acceptable and intentional**.

---

## Disclaimer

This list is provided **as-is**, without warranty.

It exists because certain vendors choose aggressive monetization models
that interfere with critical functionality (e.g. security cameras).
Use it responsibly and at your own risk.

---

## Contributions

Contributions are welcome, but please note:

- Keep the list **small and focused**
- Avoid adding generic ad domains
- Document why each new domain is required
- Avoid breaking core Google / Firebase services
