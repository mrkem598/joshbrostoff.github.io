---
title: "Hiring Qualified InfoSec Engineers"

---
Information security is exploding.

But one of the biggest problems is the talent crunch.  Finding experienced resources is near impossible in this market.  Even if you can, how can you make sure those resources are actually qualified to do the work?

A few years ago, I wanted to learn more about information security and reached out to one of my co-workers in InfoSec.  He gave me a challenge that they use as part of their interview process.

This was a huge learning experience for me and urge anyone interested in InfoSec to give it a shot or use it as part of their hiring process.

**Phase 1:**
-Spin up an internet facing Linux box (I suggest Amazon, you can get a free EC2 node)
-Get a web server running with mySQL and serve out a webpage that takes in a form as a comment and posts it back on the screen for the visitor to see (kind of like a guest book)
-Have it scrub out XSS so if someone tried posting 'evil' code, it wouldn't display in the saved comment.  I'll leave your interpretation of what/how to scrub up to you.
-Whitelist 80/443 to the server from (IP ADDRESS OF YOUR COMPANY OR OTHER NETWORK), so I can try it at work but it's not exposed to the internet.
 
**Phase 2:**
You can do this on the same box or rebuild after phase 1 so you don't break that machine
-Install artillery (https://www.trustedsec.com/artillery/) and log all activity
-Whitelist all ports from (IP ADDRESS OF YOUR COMPANY OR OTHER NETWORK), so I can try connecting
-I'm going to connect to some ports (not SSH, HTTP, or HTTPS) and issue strings.
-Tell me what strings I issued according to the logs

