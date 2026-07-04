# Finding: HTTP Security Header Analysis

**Severity**: Informational / Low
**Category**: Security Misconfiguration
**Source**: HTTP response headers — http://localhost:3000

## Headers presenti (corretto)
- X-Content-Type-Options: nosniff
- X-Frame-Options: SAMEORIGIN

## Headers critici assenti
- Content-Security-Policy (CSP)
- Strict-Transport-Security (HSTS)
- Permissions-Policy (usa il deprecato Feature-Policy)
- Referrer-Policy
- Cross-Origin-Opener-Policy

## Finding specifico
- Access-Control-Allow-Origin: * → CORS aperto, qualsiasi origine può leggere le response
- X-Recruiting: /#/jobs → header custom non standard, espone path interno (information disclosure)
- Feature-Policy → header deprecato, rimpiazzare con Permissions-Policy

## References
- MDN: https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers
- OWASP Secure Headers Project
