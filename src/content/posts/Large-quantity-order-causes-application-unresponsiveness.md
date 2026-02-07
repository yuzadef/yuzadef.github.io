---
title: "Large quantity order causes application unresponsiveness (Partial DoS)"
pubDate: "2025-2-2"
---
## Summary
A logic flaw in the group ordering system allows users to set item quantities to extremely high values (e.g., 100,000,000), causing the application to become unresponsive for all users involved in the group order. This affects both the user placing the order and other participants, leading to degraded performance or complete unresponsiveness. The issue is triggered by modifying the item quantity through the API, which disrupts access to the group order details and checkout. This flaw can be repeatedly exploited to cause significant performance issues, potentially resulting in customer frustration, abandonment, and reputational damage. The recommended fix is to implement a validation on the quantity parameter to limit excessively high values and prevent performance degradation.

## Initial report
```markdown
Description
A logic flaw in the group ordering system allows a user to set the quantity of an item to an extremely high value (e.g., 100,000,000). This results in the application becoming unresponsive for all users who have access to the group order. This flaw affects both the ordering user and other participants in the same group, causing degraded application performance or complete unresponsiveness.

Proof-Of-Concept (PoC)
To reproduce the issue, suggested to have 2 test users for testing -> userA (host) and userB (participant)

1. As userA, create a group order and invite another user (userB) into the group order.
2. As userB, add an item into his own basket and intercept the request using a proxy tool (e.g., Burp Suite Proxy).
the request should look like below
'''
PATCH /v1/group_order/679e483e31933c4fff20deb9/participants/me/basket HTTP/2
Host: restaurant-api.example.com
Cookie: .....
Authorization: Bearer .....

{
	"items":[
		{
			"id":"aedfa52e82483054dfda4f20",
			"baseprice":740,
			"options":[],
			"end_amount":740,
			"count":100000000,   // the issued parameter
			"checksum":"d0b73abe8346b51aca64895cf8c10ecd"
		}
		],
		"menu_items_source":"consumer-assortment"
}
'''

3. Modify the 'count' of the item to '100000000' and send the request.
4. Refresh the browser if necessary and observe that the page becomes unresponsive.
5. Now, as userA, attempt to view the group order details and observe the same unresponsiveness (refer see_group_order_details_button.png)

Expected result
The application should handle large order quantities gracefully, either by rejecting them with an error message or processing them without affecting performance.

Actual result
The application becomes unresponsive, preventing affected users from accessing the group orders. The flaw impacts all users in the group order, making the group order details/checkout to be inaccessible. In some cases, this may lead to complete application unresponsiveness

Attack scenario
The attacker could repeatedly exploit this flaw across multiple group orders, continuously causing application disruptions for affected users. Over time, affected users may become frustrated with the inability to complete their orders and abandon the platform in favor of a competitor, leading to potential customer churn and reputational damage for the business.

Impact
Affected users unable to access group order details/checkout. The unresponsiveness may extend to other parts of the application, affecting usability. Potential degradation in overall application performance.

Recommended fix
Implement a validation on the 'count' parameter to restrict excessively high values. Set a reasonable maximum limit for order quantities.
```

## Report timeline
**2/2/2025:** *Submission created*

**4/2/2025:** *Triager updated severity to Medium (4.3)*

**4/2/2025:** *Triager's commented: Thank you for your submission. We were able to reproduce your finding and have validated your submission. The company will review your report and get back to you as soon as possible with a final decision.*

**4/2/2025:** *Company requested feedback: Could you please clarify if the attacker should be explicitly invited into the target group order by the group order host user?*

**5/2/2025:** *Researcher responded: Yes, the attacker needs to be explicitly invited into the group order by the host user, this can happen through normal participation or social engineering. As long as the attacker obtains the group order invitation code, they can repeatedly join the target group order using guest account and disrupt the victim’s activities with unresponsiveness.*

**5/2/2025:** *Company commented: Thanks for confirming it! The severity level was corrected based on it. Hope to see new reports from you!*

**5/2/2025:** *Bounty awarded to researcher*

---

*Submitted via Intigriti*
