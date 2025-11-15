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

## Report timeline
**4/10/2025:** *Submission created*

**10/7/2025:** *Triager's commented: Thank you for your bug submission to Yahoo! We are already aware of this issue and are working towards a fix. Please continue to send us vulnerability reports!*

**10/7/2025:** *Submission status updated to "Duplicate"*

---

*Submitted on Intigriti*
