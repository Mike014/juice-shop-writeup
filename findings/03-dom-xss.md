# Finding: DOM-Based XSS — Search Field

**Severity**: Medium
**Category**: XSS — DOM Based (CWE-79)
**Challenge**: DOM XSS (1★)
**Endpoint**: http://localhost:3000/#/search
**Payload**: <iframe src="javascript:alert(`xss`)">

## Description
Il campo di ricerca di Juice Shop è vulnerabile a DOM-based XSS.
Angular legge il parametro q dall'URL e lo inserisce nel DOM
tramite innerHTML invece di textContent, permettendo l'esecuzione
di HTML e JavaScript arbitrario nel browser della vittima.

## Steps to Reproduce

1. Verifica endpoint da terminale:
   curl -s -o /dev/null -w "%{http_code}" \
     "http://localhost:3000/rest/products/search?q=test"
   → 200 OK

2. Conferma assenza sanitizzazione server-side:
   curl -s "http://localhost:3000/rest/products/search?q=TESTTOKEN123"
   → {"status":"success","data":[]}
   Il token non appare nella risposta — DOM XSS puro

3. Payload inviato nel browser tramite campo di ricerca:
   <iframe src="javascript:alert(`xss`)">

4. Risultato: alert() eseguito nel browser
   Challenge completata nello score board

## Root Cause
Angular usa innerHTML per renderizzare il valore del
campo di ricerca invece di textContent.
innerHTML interpreta HTML arbitrario inclusi tag
con event handler e attributi javascript:.

## Perché curl non può rilevarlo
DOM-based XSS vive interamente lato client.
Il server non riflette il payload nella risposta HTTP.
Richiede un browser che esegua JavaScript per materializzarsi.

## Impact
- Esecuzione di JavaScript arbitrario nel browser della vittima
- Furto cookie di sessione (se HttpOnly non impostato)
- Redirect verso siti malevoli
- Modifica del DOM — phishing overlay

## Remediation
Sostituire innerHTML con textContent per il rendering
del termine di ricerca.
Implementare Content Security Policy (CSP) come difesa
in profondità — blocca l'esecuzione di javascript: URI.

## Evidence
Payload: <iframe src="javascript:alert(`xss`)">
Risultato: alert() popup con testo "xss"
