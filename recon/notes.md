# Recon Notes — Juice Shop

## Technology Stack
- Frontend: Angular SPA (app-root, modulepreload)
- Bundle principale: main.js (783KB)
- Chunk lazy-loaded: 10 chunk separati
- Font: VT323 (Google Fonts)
- Cookie: cookieconsent library

## Route estratte da main.js
- Totale route identificate: 44
- Route sensibili: administration, accounting, score-board
- Route auth: login, register, forgot-password, 2fa, last-login-ip
- Route Web3: wallet, wallet-web3, web3-sandbox, juicy-nft

## API endpoint identificati
- /api/BasketItems
- /api/Cards
- /api/Complaints
- /api/Deliverys
- /api/Hints
- /api/Products
- /api/Quantitys
- /api/SecurityAnswers
- /api/SecurityQuestions

## Osservazioni
- score-board referenziato nel bundle ma non linkato dalla UI
- administration e accounting idem — broken access control potenziale
- CORS: Access-Control-Allow-Origin: * su tutti gli endpoint
- CSP assente — nessun controllo sugli script eseguibili
- X-Recruiting: /#/jobs — information disclosure via header custom
