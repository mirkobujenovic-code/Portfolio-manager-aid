---
description: Revisione periodica dell'intero portafoglio — comitato su ogni titolo con tesi
---

Revisione periodica (es. trimestrale) di TUTTO il portafoglio. Per ogni titolo che ha una
tesi, esegui il processo completo del comitato.

1. **Inventario.** Elenca tutti i file `thesis/*.md` ESCLUDENDO i template (`_TEMPLATE.md`,
   `_ESEMPIO.md`). Ogni file è un titolo da rivalutare. Se non c'è nessuna tesi, dillo e
   fermati.

2. **Per ogni titolo**, applica l'intero processo di `CLAUDE.md` esattamente come fa
   `/rivaluta TICKER`:
   - Fase 0: tesi + controllo anti-razionalizzazione (`git log --since="14 days ago"`) +
     memoria (ultimi memo e tabella `data/TICKER.md`).
   - Fase 1 e 1-bis: ricerca, materialità, traiettoria.
   - Fase 2: le tre voci (Analyst, Red Team con ricerca ostile esterna, Arbiter).
   - Fase 3: salva `memos/TICKER_AAAA-MM-GG.md`, aggiungi riga a `data/TICKER.md`,
     aggiorna `dashboard.md`.

   Lavora un titolo alla volta: non mescolare le analisi tra titoli diversi.

3. **Sintesi di portafoglio (alla fine).** Dopo aver rivalutato tutti i titoli, scrivi una
   sintesi:
   - Titoli con raccomandazione diversa da MANTIENI (le azioni da considerare).
   - Titoli in deterioramento lento della traiettoria, anche se ancora MANTIENI.
   - Eventuali concentrazioni di rischio o ipotesi correlate tra più titoli.
   - Segnala se la dimensione complessiva delle posizioni a rischio richiede attenzione.

4. **Tono e regole.** Valgono tutte le regole di `CLAUDE.md`: solo fonti attendibili,
   filtro di materialità, niente esecuzione di ordini, niente entusiasmo. La sintesi serve
   a far decidere l'utente a mente fredda, non a spingere operazioni.

Chiudi ricordando di **dormirci sopra** e di committare i file aggiornati.
