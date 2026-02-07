---
title: "Guest users can rejoin a locked group order due to logic flaw"
pubDate: "2025-1-27"
---

## Overview

Group ordering is designed to be simple: invite people, build an order together, then lock it when everything’s final. Once locked, no one new should be able to join == end of story.

However, a logic flaw in the group order access control breaks this expectation. While authenticated users are correctly blocked from rejoining a locked group order, guest users are not. If a guest leaves a group order before it’s locked, they can later rejoin it using the original invitation link—even after the group has been locked.

This creates an inconsistency in how the system enforces participation rules and opens up a small but meaningful security gap.

---

## What Went Wrong

At a high level, the system treats authenticated users and guest users differently when enforcing group lock restrictions.

- **Authenticated users** who leave a group order are correctly prevented from rejoining once the order is locked.
- **Guest users**, on the other hand, are not subject to the same restriction. They can reuse an old invitation link to rejoin the group even after the lock is in place.

From a business-logic perspective, this is unintended behavior. A locked group order should be immutable in terms of participants, regardless of authentication status.

---

## Reproducing the Issue

The issue can be demonstrated with three users:

- Two authenticated users
- One guest user

### Steps

1. An authenticated user creates a group order from the discovery page.
2. The group invitation link is shared with another authenticated user and a guest user.
3. Both users join the group order.
4. Both users leave the group order.
5. The original creator locks the group order.
6. Both users attempt to rejoin using the original invitation link.

### Result

- The authenticated user is blocked (expected behavior).
- The guest user successfully rejoins the locked group (unexpected behavior).

---

## Expected vs Actual Behavior

### Expected

Once a group order is locked, **no participants, new or previously removed should be able to join**, regardless of whether they are authenticated or guests.

### Actual

- Authenticated users are correctly blocked from rejoining.
- Guest users can still rejoin using the original invite link.

---

## Impact

While this issue doesn’t expose sensitive data or allow account takeover, it **violates core business logic** and creates inconsistent enforcement of access rules.

---

## Recommended Fix

- Apply the same rejoin restrictions to guest users as authenticated users.
- Once a group order is locked, invalidate the invitation link for everyone, or explicitly check the lock state before allowing any join action—guest or authenticated.

Consistency is the key here.

---

## Disclosure Timeline

- **January 27, 2025** – Report submitted  
- **January 30, 2025** – Triager confirmed the issue was a duplicate of a prior report  
- **July 10, 2025** – Status updated to *Duplicate*

---

*Reported via Intigriti*
