---
title: "Guest users can rejoin a locked group order due to logic flaw"
pubDate: "2025-1-27"
---
## Summary
A logic flaw in the group order access control allows guest users to rejoin a locked group order using an existing invitation link, even if they had previously left. Once a group order is locked, no new participants—authenticated or guest—should be able to join, but guest users can still bypass this restriction by reusing the original invite link. Authenticated users who leave the group are properly prevented from rejoining, but this safeguard is not enforced for guests, leading to inconsistent behavior. The issue creates a security gap, allowing guests to bypass the intended lock on group participation. To fix this, guest users should be restricted from rejoining locked groups, just like authenticated users.

## Initial report
```markdown
A logic flaw in the group order access control mechanism allows guest users to rejoin a locked group order using an existing invitation link, even though they previously left. This should not be possible because once a group order is locked, no new participants (including previously removed users) should be able to join, regardless if they are authenticated or guest users.

3 test users would be required (2 authenticated users, 1 guest) to demonstrate the issue:

1. (Auth-UserA) Go to https://wolt.com/en/discovery and select any restaurants (e.g. McDonalds).
2. (Auth-UserA) Create a group order by clicking on the "Order together" button.
3. (Auth-UserA) Share the link to the other users (Auth-UserB) and (Guest-UserC) so they can join the group order.
4. Once Auth-UserB and Guest-UserC have joined the group order, now leave the group order.
5. (Auth-UserA) Lock the group order. Now nobody is able to join the group order as it has been locked.
6. Auth-UserB and Guest-UserC use the same group invitation link from before and notice that Guest-UserC is able to join the group order again but Auth-UserB is not able to. This should not be possible for guests or authenticated users because once a group order is locked, no new participants (including previously removed users) should be able to join.

Expected Behavior
Once a group order is locked, no new participants(including previously removed users) should be able to join, regardless of their authentication status.

Actual Behavior
Authenticated users who leave cannot rejoin a locked group (intended behavior). Guest users who leave can still rejoin using the original invitation link, bypassing the lock restriction (unintended behavior).

Impact
Guest users can bypass the group lock restriction, violating the intended business logic. The system correctly restricts authenticated users but fails to apply the same rule to guests, leading to inconsistent security behavior.

Recommended fix
Ensure that guest users who leave a group order are restricted similarly to authenticated users and prevented from rejoining once the group is locked.
```

## Report timeline
**27/1/2025:** *Submission created*

**30/1/2025:** *Triager's commented: Thank you for choosing our platform for your bug-bounty adventures! When validating your report, we noticed that this exact issue has been reported before. Therefore, we will have to close your report as duplicate. This means that you will still receive some reputation points. Looking forward to your next submission!*

**10/7/2025:** *Submission status updated to "Duplicate" and severity Medium to Low (3.1)*

---

*Submitted on Intigriti*
