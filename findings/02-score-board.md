# Finding: Score Board — Hidden Page Discovery

**Severity**: Informational
**Category**: Security through Obscurity / Broken Access Control
**Challenge**: Score Board (1★)
**URL**: http://localhost:3000/#/score-board

## Description
La pagina score-board non è linkata dall'interfaccia utente
ma è referenziata nel bundle JavaScript dell'applicazione Angular.
Il path è scopribile tramite analisi statica del bundle main.js
senza autenticazione o interazione con l'applicazione.

## Steps to Reproduce

1. Scarica il bundle principale:
   curl -s http://localhost:3000/main.js -o main.js

2. Estrai le route Angular:
   grep -oP 'path:"[^"]{2,40}"' main.js | sort -u

3. Identifica score-board tra le route:
   path:"score-board"

4. Verifica accesso diretto:
   curl -s -o /dev/null -w "%{http_code}" http://localhost:3000/score-board
   → 200 OK senza autenticazione

## Root Cause
Il controllo di accesso è implementato solo nel router Angular
lato client. Il server non valida l'autenticazione prima di
servire le route sensibili.

## Impact
Un attaccante può enumerare l'intera struttura dell'applicazione
analizzando il bundle JavaScript pubblicamente accessibile.
Nessuna credenziale o sessione richiesta.

## Evidence
grep -oP 'path:"[^"]{2,40}"' main.js | grep score
→ path:"score-board"

curl -s -o /dev/null -w "%{http_code}" http://localhost:3000/score-board
→ 200

## Remediation
Non affidarsi alla sola oscurità del path come meccanismo
di sicurezza. Implementare autenticazione server-side su ogni
endpoint sensibile indipendentemente dal routing client-side.
