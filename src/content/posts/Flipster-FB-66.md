---
title: "Reflected XSS -> Cookie Tossing to steal money from unaware victims"
pubDate: "2025-12-5"
---
## Summary
Before anything, as usual, due to the program's policy, all target will be refered to as `target.com`. The target is a high-performance crypto perpetuals trading platform engineered by traders, optimized for trading. With the target's nature being a cryptocurrency platform, you will soon understand why the impact of this chained vulnerability could pose a critical security impact to it's users. A chained bug of Open Redirect, which escalated to Reflected XSS and leveraging the Cookie Tossing technique, an attacker could end up stealing money from unaware victims.

## More about the vulnerability
The target's in-scope includes the main application sitting on `www.target.com` as usual. Immediately navigating to the application, I was welcomed with the "Select your nationality" page (`https://www.target.com/en/select-nationality`). The page content itself is not interesting as it just asks the user to select the nationality which will then redirects to the login page.

A quick fuzzing for parameters using Arjun reveals a parameter called `returnUrl`. Appending the parameter to the URL with the value of `https://ev
