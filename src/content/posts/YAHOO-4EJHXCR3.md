---
title: 'Open Redirect on Yahoo's acquisition'
pubDate: '2025-10-4'
status: 'Duplicate'
---

## Initial report
```
Hi,

I would like to report an open redirect issue on www.redacted.com.

Description
An attacker can redirect a user to any external website by appending %5c to the end of URL as such: https://www.engadget.com/%5cevil.com

Steps to Reproduce
1. Visit the following URL: https://www.engadget.com/%5cevil.com
2. This will redirect you to https://evil.com

Proof of Concept
1. Visit the following URL: https://www.engadget.com/%5cyahoo-inti.netlify.app/login which simulates how credential harvesting can be achieve through this Open Redirect vulnerability.
2. The login page serves the Engadget login page (email and password). On submission, JavaScript sends the credentials as query parameters to http://yahoo-inti.netlify.app via a GET request using fetch. Immediately after, the browser redirects to https://login.engadget.com/account/challenge/fail to simulate a failed login.

Browsers Verified In
Firefox 143.0.3
Chrome 140.0.7339.208
Edge 141.0.3537.57

Impact
By modifying untrusted URL input to a malicious site, an attacker may successfully launch a phishing scam and steal user credentials by hosting a fake login.engadget.com and fetching the credentials, leading to account takeover. Because the server name in the modified URL is identical to the original site, phishing attempts may have a more trustworthy appearance.

Suggested fix
Don’t treat backslashes as allowed parts of a destination. If a URL path contains \ or %5c, reject it or convert it to a safe value.
```
