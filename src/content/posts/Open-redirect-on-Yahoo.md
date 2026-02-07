---
title: "Open Redirect on Yahoo's acquisition"
pubDate: "2025-10-4"
---
## Summary
An open redirect vulnerability was found on www.redacted.com
, where appending %5c (a URL-encoded backslash) to a URL allows an attacker to redirect users to a malicious site. This can be exploited for phishing attacks, where users are redirected to a fake login page that mimics the original site, capturing their credentials and leading to potential account takeovers. The issue is reproducible across multiple browsers, including Firefox, Chrome, and Edge. To fix this, it's recommended that the server reject or sanitize URLs containing backslashes or the %5c character to prevent malicious redirects. Unfortunately, escalating to XSS was not possible due to the strong WAF and CSP in place.

## Initial report
```markdown
Hi,

I would like to report an open redirect issue on www.redacted.com.

Description
An attacker can redirect a user to any external website by appending %5c to the end of URL as such: https://www.redacted.com/%5cevil.com

Steps to Reproduce
1. Visit the following URL: https://www.redacted.com/%5cevil.com
2. This will redirect you to https://evil.com

Proof of Concept
1. Visit the following URL: https://www.redacted.com/%5cyahoo-inti.netlify.app/login which simulates how credential harvesting can be achieved through this Open Redirect vulnerability.
2. The login page serves the login page (email and password). On submission, JavaScript sends the credentials as query parameters to http://yahoo-inti.netlify.app via a GET request using fetch. Immediately after, the browser redirects to https://login.redacted.com/account/challenge/fail to simulate a failed login.

Browsers Verified In:
- Firefox 143.0.3
- Chrome 140.0.7339.208
- Edge 141.0.3537.57

Impact
By modifying untrusted URL input to a malicious site, an attacker may successfully launch a phishing scam and steal user credentials by hosting a fake login.redacted.com and fetching the credentials, leading to account takeover. Because the server name in the modified URL is identical to the original site, phishing attempts may have a more trustworthy appearance.

Recommended fix
Don’t treat backslashes as allowed parts of a destination. If a URL path contains \ or %5c, reject it or convert it to a safe value.
```

## Fix bypassed
After 2 months from when the initial report was submitted, the Open Redirect vulnerability was fixed by the security team and fortunately, I was notified about it. Receiving that notification, I immediately went back to the target and test the same payload again for Open Redirect -> `%5cevil.com` -- and YES, the vulnerability was fixed. I was glad that it was fixed because at the same time, I knew the security team didn't anticipate double URL-encoding and end up not pushing a fix for it. To confirm, I test the endpoint again with the same Open Redirect payload but this time, instead of single-URL encoding (%5c), double-URL encoded the payload to `%255c` which translates to `%5c` and then `\`. Not so surprising, the Open Redirect payload was successful and redirect me to `evil.com`.

As usual, where there is Open Redirect, it is always worth it to test for XSS, but unfortunately, the WAF implemented on that domain carefully checks for `javascript` which is required to craft an XSS payload. At the time, I was focusing on a different application and didn't want to waste too much time trying to bypass the WAF, so I end up submitting another report to the company, accepted and received bounty with bonus for the submission.

## Report timeline
**4/10/2025:** *Submission created*

**10/7/2025:** *Triager's commented: Thank you for your bug submission to Yahoo! We are already aware of this issue and are working towards a fix. Please continue to send us vulnerability reports!*

**10/7/2025:** *Submission status updated to "Duplicate"*

**24/11/2025:** *New submission created for the Open Redirect bypass*

**26/11/2025:** *Submission status updated to "Pending"*

**12/12/2025:** *Submission status updated to "Accepted" and received bounty with bonus from the company*
---

*Submitted on Intigriti*
