# burp-notes

Testing is against juice-shop

If possible, do authenticated burp spidering. Sometimes this means making a fake user account to gain access to additional features.

Proxy
	• Always unhide hidden form fields
		○ Proxy
			§ Options
				□ Response Modification
					® Unhide hidden form fields
	• Prevent 304's (data is cached in browser responses) which prevent data from being sent from the server request, need to add some headers to the HTTP request. Can enable for all proxied requests.
		○ Proxy
			§ Options
				□ Match and Replace
					® Request Header: If-Modified-Since
					® Request Header: If-None-Match

Spidering
	• Automated
	• Manual
		○ Dynamic frontends need manual triggers e.g. SPAs.
	• Hybrid
	• Authenticated Spidering
		○ Login as user
		○ Set scope
		○ Spider the scope
		○ Save as session state file for diffing spidering events (e.g. compare accounts)
Compare site maps between a low priv user and a high priv user to determine if there is anything that would represent a privilege escalation via something like force browsing. (e.g. the spider saw it, so it may be possible).

Spidering in Burp 2 is now crawling

Audit will attempt to insert dangerous payloads and observe the result
	• Passive
	• Active
	• Javascript Active


Login form - returns invalid email address when attempting to login - this can allow user enumeration
	• Capture the traffic in burp
	• Send to intruder
	• Modify the positions to only point to the email
		○ Leave the attack type set to sniper
	• Set the payloads to the word list

SQL Injection
	• ; or 1=1 and email like ("%Bender%");--

XSS - Reflected and Stored XSS only

Cookie Manipulations

JSON injection

HTTP Verb Manipulation
	• When JSON is returned via a GET request, try and issuing a PUT to see if it can be updated via the same method. Some ORMs may support this CRUD model.

CSRF
	• Burp Pro has a CSRF PoC module
	• Process
		○ Send to repeater
			§ Right click on text -> engagement tools -> CSRF PoC
		○ Remove Cookie Tokens tokens from the request and test in browser
			§ This will run the PoC on a BURP web browser (for the CSRF activity)
		○ If the same response is received, this form is likely vulnerable to a CSRF attack
Clickjacking
	• Burp -> Clickbandit to copy the Javascript
	• Open web console in browser and paste the code to into the javascript console
		○ This will bring up a new web page in the browser
	• Select start and clickbandit will follow the mouse clicks and attempt to determine if an IFRAME can be injected into the DOM as any of the click points.
		○ Click through the menu UI to for burp to record and test the clicks
		○ Select finish from the top level burp IFRAME when complete
SSRF
	• Look at traffic and look for URLs, or IP addresses which may reference internal ones (or just those which are not accessible via the Burp box.
	• Collaborator client can be used for SSRF
		○ Burp -> Collaborator Client
		○ Do not close collaborator client
	• Copy the collaborator payload to the clip-board
		○ This will be replaced in the captured SSRF HTTP Request
	• Repeat the capture of the SSRF vulnerable Request, intercepting on the request
		○ Replace the target internal address with the collaborator address
			§ Make sure to pre-pend HTTP to the address
		○ This tool works by using PortSwigger to setup a dynamic external URL. If there is an SSRF vuln, the victim will attempt to first resolve this via address via DNS then reach out and pull the data request in the payload
	• Results will be stored in the collaborator window.
		○ Look for DNS - meaning they attempted to resolve the connections
		○ Look for HTTP - meaning they actually reached out to the external server for the request

Burp Extensions
	• Can be used for file vulns and things like that
	• B-Apps via the B-App store
		○ All B-Apps have their source code on a github page
			§ Github.com/portswigger
	• CO2
		○ SQLMap
	• Retire.js
		○ Outdate JS library scanner
	• Carbonator
		○ Headless burp for automation

Null Byte Injection Attack (%00)
	• Can be usd to download files not allowed to (kill interpretation of the filename)
	• Unicode
		○ %2500<.extension>
			§  %2500.pdf

Carbonator.- Running Headless
	• java -jar -Xmx2G -Djava.awt.headless=true /path/too/burp.jar http localhost 3000 /
	• java -jar -Xmx2G -Djava.awt.headless=true /path/too/burp.jar <protocol> <host> <port> <folder>
	• In headless mode, it acts in a passive proxy and will analyze the traffic for issues. With the carbonator plugin enabled, it will generate a report in the directory it was running in (html).
