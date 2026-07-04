# [OWASP Juice Shop] Finding #1 — Score Board Discovery
## Come ho trovato una pagina nascosta senza aprire il browser

**Stack**: curl + grep + bash
**Severity**: Informational
**Tecnica**: JavaScript bundle analysis

---

### Contesto

OWASP Juice Shop è una web application intenzionalmente
vulnerabile. La prima challenge richiede di trovare lo
score board — una pagina non linkata dall'UI.

L'obiettivo: trovarla usando solo il terminale.

---

### Reconnaissance

Primo passo: capire con cosa ho a che fare.

curl -sI http://localhost:3000

Risposta immediata: Angular SPA, bundle JavaScript pubblico,
nessun Content Security Policy. Il codice dell'applicazione
è scaricabile e analizzabile da chiunque.

---

### Il metodo

Il router Angular definisce tutti i path dell'applicazione
nel bundle main.js. Se quel file non è offuscato, i path
sono leggibili in chiaro.

curl -s http://localhost:3000/main.js -o main.js
grep -oP 'path:"[^"]{2,40}"' main.js | sort -u

Output: 44 route estratte, inclusa:
path:"score-board"

---

### Verifica accesso

curl -s -o /dev/null -w "%{http_code}" \
  http://localhost:3000/score-board
→ 200

Nessun token. Nessuna sessione. Nessun cookie.
Il server serve la pagina a chiunque conosca il path.

---

### Il finding

Security through obscurity non è sicurezza.
Nascondere un link dall'UI non protegge la risorsa.
Il path è nel bundle JS — pubblico, scaricabile, analizzabile.

La difesa corretta: autenticazione server-side su ogni
endpoint sensibile, indipendentemente dal routing client.

---

### Takeaway

Prima di testare qualsiasi applicazione web, analizza
i bundle JavaScript. Contengono route, endpoint API,
e a volte molto di più.

#ethicalhacking #webappsecurity #owasp #bugbounty
#penetrationtesting #javascript #security
