---
title: 'How Self-Stored XSS can be levraged to achieve a full ATO'
pubDate: '2025-09-01'
---

## What is Seld-Stored XSS?

Self-stored XSS (Cross-Site Scripting) is a type of XSS attack where malicious scripts are injected into a website and stored permanently on the server, such as in a database, user profile, or comment section. When other users visit the affected page, the stored script executes in their browsers, potentially stealing data, hijacking sessions, or performing unauthorized actions. It differs from reflected XSS, which relies on user input to deliver the payload immediately.

## The vulnerable context

The target is a freelance platform connecting freelancers with clients for digital tasks like design, writing, and web development. Freelancers can post/list their offered services on the app for consumers to hire.

In the posting/listing feature, a few fields are required before they can proceed with listing their services on the app, this includes service name, service descriptions, purchase amount, etc, the usual thing you see in job listing applications.

All the inputs supplied are reflected on the app, so just like any hunter, I tested for HTML injection. Unfortunately, all the HTML payloads were escaped gracefully and I couldn't think of anyway to bypass the defense mechanism.

As I was going to move on from the feature, I decided to test the same HTMLi payload '<u>Underline</u>' in the 'service description' field but inside the 'preview' mode. The HTML payload was rendered as HTML.




