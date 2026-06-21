# Traiettoria metriche — ACME (ESEMPIO)

> ESEMPIO che mostra un DETERIORAMENTO LENTO da catturare. Nessuna singola riga fa
> scattare un kill criterion (il margine non è mai sotto il 45% per due trimestri, la
> quota non è mai sotto il 45%), eppure l'andamento è inequivocabile: margine
> 56→54→52%, quota 62→60→58%, crescita 30→26→22%, conviction 8→7→5. È esattamente il
> segnale che il comitato deve vedere PRIMA che un allarme suoni.

| Data | Margine lordo (%) | Quota accelerator (%) | Crescita ricavi DC (% YoY) | Raccom. | Conviction (1-10) | Memo |
|------|-------------------|-----------------------|----------------------------|---------|-------------------|------|
| 2026-03-15 | 56 | 62 | 30 | MANTIENI | 8 | `memos/ACME_2026-03-15.md` |
| 2026-05-02 | 54 | 60 | 26 | MANTIENI | 7 | `memos/ACME_2026-05-02.md` |
| 2026-06-21 | 52 | 58 | 22 | RIDUCI | 5 | `memos/ACME_2026-06-21.md` |

---

### Cosa nota il comitato qui
- **Margine lordo:** 56 → 54 → 52. Ancora sopra il kill criterion (45%), ma in erosione
  costante di ~2 punti a revisione. A questo ritmo l'ipotesi #2 (>55%) è già violata.
- **Quota di mercato:** 62 → 60 → 58. Tendenza coerente verso la soglia del 45%.
- **Crescita ricavi DC:** 30 → 26 → 22. Si avvicina al floor del 25% dell'ipotesi #3.
- **Conviction:** 8 → 7 → 5. Il calo costante è di per sé un segnale.

Nessun kill criterion è formalmente scattato, ma tre metriche-chiave puntano nella
stessa direzione e la conviction crolla: l'Arbiter passa a RIDUCI prima che la tesi si
rompa del tutto. Questo è il deterioramento lento che la V1 non vedeva.
