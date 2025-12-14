## Task 1

> Why are reverse proxies valuable for security?

Reverse proxies encapsulate the backend logic by hiding it behind policies meant to enhance security and regulate incoming requests. Specifically, a reverse proxy can:

- **Hide the internal server specifics** and various backend services under a singular IP, making reconnaissance and targeted exploitation more difficult
- **Reduce the attack surface** by requiring only a couple or a small controlled set of proxy ports to be open instead of many
- **Simplify traffic control**, since monitoring, filtering, inspection, rate limiting, and blocking traffic only requires policy changes at one point
- **Protect against DDoS and brute-force attacks** by absorbing, filtering, distributing, and blocking requests before they reach the target server
- **Manage TLS** encryption and decryption, freeing the backend logic from managing certificates and decoupling it from security policies
- **Inject security headers** into responses to enable the built-in protection policies in users' browsers, protecting the users from browser-side exploits


> Why do hidden direct app ports reduce attack surface?

The attack surface size depends on **the number of distinct actions**, both legitimate and non-legitimate, that an attacker can feasibly perform to try to find exploitable vulnerabilities.

By hiding direct app ports under a reverse proxy, all system "actions" get encapsulated under a singular set of policies, and, therefore, the set of all feasible actions available to the attacker **gets smaller**.

The screenshot below demonstrates that `nginx` has published host ports, while the Juice Shop app does not:

```bash
control@Master-mind:~/DevSecOpsLab11/F25-DevSecOps-Intro/labs/lab11$ # Only Nginx should have published host ports; Juice Shop should have none
docker compose ps
NAME            IMAGE                           COMMAND                  SERVICE   CREATED              STATUS              PORTS
lab11-juice-1   bkimminich/juice-shop:v19.0.0   "/nodejs/bin/node /j…"   juice     About a minute ago   Up About a minute   3000/tcp
lab11-nginx-1   nginx:stable-alpine             "/docker-entrypoint.…"   nginx     About a minute ago   Up About a minute   0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp, 80/tcp, 0.0.0.0:8443->8443/tcp, [::]:8443->8443/tcp
```

___
## Task 2

- **X-Frame-Options**: `DENY`
	- Protects from clickjacking, a type of attack used to execute malicious code while embedding the target site in an iframe of the legitimate site. This security header is interpreted by the browser as a restriction on opening the target site in any iframe.
- **X-Content-Type-Options**: `nosniff`
	- Protects from XSS attacks achieved by content type forgery due to browsers guessing ("sniffing") the content type from superficial indicators which can often be manipulated. This security header instructs the browser to not guess the content type and trust the "Content-Type" header instead.
- **Strict-Transport-Security (HSTS)**: `max-age=31536000`;` includeSubDomains`; `preload`
	- Protects from protocol downgrading attacks, such as switching from HTTPS to HTTP to enable a man-in-the-middle attack. This security header strictly instructs the browser to exclusively use HTTPS.
- **Referrer-Policy**: `strict-origin-when-cross-origin`
	- Protects from exposure of sensitive url parameters in the "referral" header during cross-origin requests. This security header instructs the browser to only expose the referrer domain instead of the full url.
- **Permissions-Policy**: `camera=(), geolocation=(), microphone=()`
	- Protects from malicious use of the user's camera, microphone, and geolocation caused by unintended script execution. This security header instructs the browser to strictly disable the camera, location, and microphone permissions for the site.
- **COOP/CORP**: `same-origin`
	- Protects from tab-napping, side-channel attacks and any attacks that require the malicious actors to load target pages/resources. This security header instructs the browser to only load resources when the requesting domain is explicitly allowed and to virtually separate processes serving different tabs/windows.
- **CSP-Report-Only**: `default-src 'self'`; `img-src 'self' data:`; `script-src 'self' 'unsafe-inline' 'unsafe-eval'`; `style-src 'self' 'unsafe-inline'`
	- Doesn't explicitly protect from any dangers, but helps to develop strong protection policies from XSS and data injection attacks. This header instructs the browser to only report violation of policies instead of blocking the actions that caused them, which is useful when developing and refining the policies.

___
## Task 3

The following questions are answered based on `ssltest` output.

> Which TLS protocols are supported?

- **Offered:** TLS 1.2, TLS 1.3
- **Not offered:** SSLv2, SSLv3, TLS 1.0, TLS 1.1

> Which cipher suites are supported?

TLS 1.2 (in server order):
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (ECDHE-RSA-AES256-GCM-SHA384) – AEAD, forward secrecy
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (ECDHE-RSA-AES128-GCM-SHA256) – AEAD, forward secrecy

TLS 1.3 (in server order):
- TLS_AES_256_GCM_SHA384 – AEAD, forward secrecy
- TLS_CHACHA20_POLY1305_SHA256 – AEAD, forward secrecy, good for non‑AES hardware
- TLS_AES_128_GCM_SHA256 – AEAD, forward secrecy

> Why is TLS v1.2 or higher required?

Older versions of TLS are considered obsolete, since they allow many legacy and vulnerable algorithms. TLS v1.2 is still used for compatibility with older systems since it is not vulnerable enough to be too dangerous. TLS v1.3 is the most secure and recommended standard at this time.

> Which notable warning and vulnerabilities are present?

- As expected, chain of trust and revocation mechanism are highlighted, since the certificate is self signed
- No other vulnerabilities detected

> Do HSTS headers appear only on HTTPS responses?

Yes
