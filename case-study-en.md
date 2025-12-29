# Success Case  
## Eliminating splash ads in camera applications using Pi-hole

---

## Introduction

The need to document this case arose from a concrete and recurring situation:
**intrusive advertising appearing in critical security camera applications,
with no clear solution using traditional blocklists**.

After weeks of testing, tuning, and troubleshooting, a working solution was achieved.
At that point, it became clear that both the problem and its resolution were not isolated,
but rather part of a broader pattern likely affecting many camera and IoT users.

This document exists to **explain why it was necessary to create a dedicated list,
how that solution was reached, and under which conditions it works**, so others can
recognize the problem and reproduce the solution in an informed way.

---

## Context

The environment consisted of **28 security cameras**, managed through
**two different mobile applications**:

- **EyePlus** (approximately 10 cameras)
- **XMEye** (the remaining cameras)

Although these are different applications from different vendors,
**both exhibited exactly the same advertising behavior**.

In both applications, the following issues appeared:

- **Forced video advertisements** on app startup
- Ads shown **before accessing live video**
- **Fake or misleading countdown timers**
- Redirections to additional advertising content

This behavior was particularly problematic because it:
- delayed access to live camera feeds
- interfered with urgent monitoring scenarios
- degraded functionality directly related to security

---

## Infrastructure

- **DNS server**: Pi-hole running on Raspberry Pi  
- **Clients**: Android mobile phones  
- **Network**: Home / small office LAN  
- **Cameras**:
  - 10 cameras managed via EyePlus
  - 18 cameras managed via XMEye
- **Key constraints**:
  - Endpoint blockers (AdGuard, etc.) could not be installed
  - Camera apps and firmware could not be changed
  - Cameras worked exclusively with their proprietary apps
  - The solution had to be **network-level**

---

## Initial problem

Despite having Pi-hole correctly configured and using multiple well-known
blocklists (OISD, StevenBlack, Energized, etc.):

- Ads continued to appear in **both EyePlus and XMEye**
- Pi-hole logs showed normal DNS activity
- Blocking obvious advertising domains had no effect

This initially led to a common but incorrect conclusion:
that **Pi-hole was “not working”**, when in reality the issue lay in
**how modern mobile apps deliver advertising**.

---

## Technical analysis

Reviewing the Pi-hole *Query Log* revealed that both applications,
despite being different products, shared **the same advertising ecosystem**.

### 1. Use of modern advertising SDKs

Both EyePlus and XMEye relied on multiple mobile monetization SDKs, including:

- Vungle
- Mintegral
- Liftoff
- Rayjump
- Pangle / TikTok Ads

These SDKs use techniques such as:
- dynamic subdomains
- CNAME cloaking
- HTTPS-only delivery

---

### 2. Ads served from shared CDN infrastructure

The **splash video ads themselves** were not served directly from SDK domains,
but from large-scale CDN infrastructure, primarily associated with ByteDance:

- domains shared with legitimate content
- endpoints designed to appear neutral
- no reliable way to distinguish ad vs non-ad content by hostname alone

This explains why:
- standard blocklists failed
- blocking only the SDK domains did not stop the videos

---

### 3. Confirming Pi-hole’s limitations

It was verified that:

- Pi-hole resolved DNS correctly
- Pi-hole blocked domains when applicable
- However, **Pi-hole cannot inspect HTTPS content or internal paths**

When **AdGuard** was enabled on a phone, ads disappeared,
confirming that successful blocking occurred **beyond the DNS layer**.

---

## Critical constraints

The situation was constrained by non-negotiable factors:

- ❌ No additional software could be installed on phones
- ❌ TLS inspection proxies were not an option
- ❌ EyePlus and XMEye could not be replaced
- ❌ Cameras could not be replaced
- ❌ The solution had to be immediate and demonstrable

The deadline to resolve the issue was **extremely short (hours)**.

---

## Implemented solution

### Adopted approach

A **controlled but aggressive** approach was chosen, based on:

- Complete blocking of advertising SDKs
- Blocking of specific CDNs used for splash video delivery
- Strict use of **Pi-hole group management**
- Limiting impact exclusively to mobile devices

---

### Technical implementation

1. A **dedicated group** was created in Pi-hole (e.g. `Cameras`)
2. Explicit blocking was applied to:
   - monetization SDKs
   - CDNs associated with splash ads
3. Critical services were explicitly allowed:
   - Google / Firebase
   - APIs required for login and notifications
4. All rules were consolidated into a **dedicated ABP-style list**

This list was later published as a public repository to facilitate:
- maintenance
- reuse
- community review

---

## Results

After applying the solution:

- ✅ EyePlus stopped showing ads
- ✅ XMEye stopped showing ads
- ✅ Access to live video became immediate again
- ⚠️ In some cases, a brief black screen appears (expected behavior)

Both applications continued to function correctly.

The outcome was considered **operationally successful**.

---

## Lessons learned

1. Many camera apps share the same advertising backend
2. Not all advertising can be blocked with standard lists
3. Shared CDNs are the main challenge for DNS-level blocking
4. Pi-hole has clear limitations, but remains valuable
5. Aggressive lists must:
   - remain small
   - be well documented
   - be applied per group only

---

## Conclusion

This case demonstrates that it is possible to mitigate intrusive advertising
even in closed, proprietary applications, provided that the modern content
delivery model is understood and a **surgical, controlled approach** is accepted.

The solution is not universal, but it is **reproducible** for similar scenarios
in camera, IoT, and CCTV applications.

---

## Final note

This document does not aim to promote indiscriminate blocking.
It documents a **technical response to aggressive monetization models**
that interfere with critical security-related functionality.

Use this approach responsibly.
