# Finding: Broken Access Control — Client-Side Only

**Severity**: High
**Category**: Broken Access Control (OWASP Top 10 A01:2021)
**URLs**:
- http://localhost:3000/administration
- http://localhost:3000/accounting
- http://localhost:3000/score-board

## Description
Le route sensibili restituiscono HTTP 200 senza autenticazione.
Il controllo di accesso è implementato solo nel router Angular
lato client — non esiste validazione server-side sull'accesso
alle pagine stesse.

## Steps to Reproduce
curl -s -o /dev/null -w "%{http_code}" http://localhost:3000/administration
→ 200 OK senza token, sessione o cookie

## Impact
Un attaccante che conosce o enumera i path interni
accede a funzionalità amministrative senza autenticazione.
La discovery dei path è banale via analisi del bundle JS.

## Evidence
for path in administration accounting score-board; do
  code=$(curl -s -o /dev/null -w "%{http_code}" \
    http://localhost:3000/$path)
  echo "$code  /$path"
done
→ 200 /administration
→ 200 /accounting
→ 200 /score-board

## Remediation
Implementare autenticazione e autorizzazione server-side
su ogni route sensibile — indipendentemente dal routing
client-side. Il server non deve mai fidarsi del fatto che
il client "non mostri" il link.
