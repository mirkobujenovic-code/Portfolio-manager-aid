# 📊 Dashboard — Comitato di investimento V2

Stato dell'ultima revisione per ogni titolo. Aggiornato dal comitato dopo ogni
`/rivaluta`. La raccomandazione riflette l'ultimo memo, non un flusso in tempo reale:
vale finché non rilanci la revisione.

| Ticker | Ultima revisione | Raccom. | Confidenza | Conviction | Traiettoria | Memo |
|--------|------------------|---------|------------|------------|-------------|------|
| _ACME (esempio)_ | 2026-06-21 | RIDUCI | Media | 5/10 ↓ | Margine in erosione 3 trim. | `memos/ACME_2026-06-21.md` |
| | | | | | | |

---

### Legenda raccomandazioni
- **MANTIENI** — la tesi regge, nessuna azione
- **AUMENTA** — la tesi si è rafforzata oltre il previsto
- **RIDUCI** — un'ipotesi si è indebolita ma la tesi non è rotta
- **ESCI** — kill criterion attivato o tesi rotta

### Colonna Traiettoria
Sintetizza l'andamento delle metriche-chiave nel tempo (da `data/TICKER.md`). Le frecce
↑↓→ indicano la direzione della conviction. Un deterioramento lento qui è un segnale
anche senza una notizia eclatante.

### Comandi
- `/nuova-tesi TICKER` → crea la tesi a mente fredda (una volta per titolo)
- `/rivaluta TICKER` → comitato su un titolo, quando una notizia ti agita
- `/portafoglio` → revisione periodica di tutti i titoli (trimestrale)
