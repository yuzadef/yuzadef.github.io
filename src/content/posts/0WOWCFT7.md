---
title: "Removed participants can still retrieve group order details via the restaurant-api"
pubDate: "2025-6-2"
---
## Summary
A vulnerability was found in the restaurant API where users who are removed from a group order still retain access to order details through the API. After a participant (userB) is removed by the host (userA), userB can still query the group order details via both authenticated and guest endpoints. This allows unauthorized users to track order activities even after being removed, violating expected access control policies. The API should check if the user is still a valid participant in the group order and return a 403 Forbidden response for unauthorized requests. Currently, removed users can still retrieve data, exposing sensitive order details.

## Initial report
```markdown
Description
After a participant (userB) is removed from a group order by the host (userA), userB should no longer have access to the group order. However, the restaurant-api endpoint still allows userB to retrieve group order details, even after modifications made by the remaining participants. This violates expected access control policies and allows unauthorized users to track group order activities still.

Proof-Of-Concept (PoC)
To reproduce the issue, suggested to have at least 3 test account: userA (host), userB (auth participant) & userC (guest participant)

1. As userA, create a new group order and invite userB and userC into the group order.
2. While navigating around, use a proxy tool (e.g., Burp Suite Proxy) to intercept the issued restaurant-api endpoint and send the requests to Burp Suite Repeater. The request should look like below.

Authenticated participants endpoint
'''
GET /v1/group_order/{group_id} HTTP/2
Host: restaurant-api.redacted.com
Cookie: {REDACTED}
Authorization: {REDACTED}
'''

Guest participants endpoint
'''
GET /v1/group_order/guest/{group_id} HTTP/2
Host: restaurant-api.redacted.com
Cookie: {REDACTED}
'''

3. As userA, remove the two users earlier from the group order.
4. Expected that userB or userC should no longer have access to the group order on the front-end.
5. Go back to the intercepted request earlier and replay the request. Test with both authenticated & guest participant endpoint and notice that the removed users can still retrieve group order details, even after modifications have been made by the remaining participants.

Expected behavior
When userB or userC is removed from a group order, their access should be revoked both from the front-end and API.
The API should return a 403 Forbidden response upon request (Insufficient rights).

Actual behavior
Removed users retain API access and can retrieve group order details in real-time, even after changes made.
The API does not enforce proper authorization checks for removed users.

Impact
Exposes order details to unauthorized users and tracking of group order activities even after removal.

Suggested fix
Ensure the issued API endpoint to verify that the requesting user is still a participant in the group order. If not, return a 403 Forbidden response if the user is not authorized to access the data.
```

## Report timeline
**6/2/2025:** *Submission created*

**6/2/2025:** *Triager's commented: Thank you for choosing our platform for your bug-bounty adventures! When validating your report, we noticed that this exact issue has been reported before. Therefore, we will have to close your report as duplicate. This means that you will still receive some reputation points. Looking forward to your next submission!*

**10/7/2025:** *Submission status updated to "Duplicate"*

---

*Submitted on Intigriti*
