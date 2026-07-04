# [OWASP Juice Shop] Finding #2 — DOM-Based XSS
## Come ho trovato un XSS nel campo di ricerca usando solo il terminale

**Stack**: curl + grep + bash + browser (solo per conferma)
**Severity**: Medium
**Categoria**: DOM-Based Cross-Site Scripting (CWE-79)

---

### Contesto

Seconda challenge di OWASP Juice Shop.
Obiettivo: sfruttare una vulnerabilità XSS nel campo di ricerca.
Approccio: reconnaissance da terminale, exploitation nel browser.

---

### Reconnaissance da terminale

Prima del payload, ho capito come funziona l'endpoint:

curl -s "http://localhost:3000/rest/products/search?q=apple"

Risposta: JSON con lista prodotti. Il campo description
conteneva HTML grezzo non sanitizzato:

"description": "Can be <a href=\"/#recycle\">sent back</a>"

Primo segnale: il server non sanitizza l'HTML nei dati.

---

### Identificazione del tipo di XSS

curl -s "http://localhost:3000/rest/products/search?q=TESTTOKEN123"
→ {"status":"success","data":[]}

Il token non appare nella risposta — il server non riflette
il parametro q nel JSON. Conclusione: DOM-based XSS puro.
Il payload vive solo nel browser, non passa per il server.
curl da solo non può rilevarlo.

---

### I tre tipi di XSS — chiariti empiricamente

Reflected:  input → server → risposta HTTP → DOM
Stored:     input → DB → risposta HTTP → DOM
DOM-based:  input → DOM direttamente (Angular legge l'URL)

Il server non è coinvolto nel terzo caso.
Questo è il motivo per cui molti scanner automatici
non lo rilevano — richiedono un browser reale.

---

### Exploitation

Payload inserito nel campo di ricerca del browser:
<iframe src="javascript:alert(`xss`)">

Risultato: alert() eseguito immediatamente.
Challenge completata nello score board.

---

### Root Cause

Angular legge il parametro q dall'URL e lo inserisce
nel DOM tramite innerHTML invece di textContent.

innerHTML interpreta HTML arbitrario.
textContent lo tratta come testo letterale.

Una sola parola di differenza nel codice.
Impatto: esecuzione di JavaScript arbitrario
nel browser di qualsiasi utente che clicchi un link malevolo.

---

### Takeaway

DOM-based XSS è il più difficile da rilevare con tool automatici.
Richiede analisi del codice JavaScript lato client.
La reconnaissance da terminale ti dice dove guardare —
ma l'exploitation richiede un browser.

CSP (Content Security Policy) blocca javascript: URI
anche quando innerHTML è usato — difesa in profondità reale.

#ethicalhacking #xss #owasp #webappsecurity
#bugbounty #javascript #penetrationtesting
