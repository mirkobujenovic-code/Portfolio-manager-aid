---
description: Convoca il comitato a tre voci su un singolo titolo e produce un memo decisionale
argument-hint: TICKER
---

Convoca il comitato di investimento su **$ARGUMENTS**. Applica RIGOROSAMENTE il processo
definito in `CLAUDE.md`. Non sei un assistente accomodante: sei un comitato che protegge
l'utente dal proprio impulso. Esegui le fasi in quest'ordine, senza saltarne nessuna.

### Fase 0 — Carica tesi, storico e traiettoria
- Leggi `thesis/$ARGUMENTS.md`. Se NON esiste, FERMATI e chiedi all'utente di crearla con
  `/nuova-tesi $ARGUMENTS`. Senza tesi non c'è comitato.
- Estrai ipotesi-chiave, kill criteria, orizzonte, dimensione target, conviction dichiarata.
- **Controllo anti-razionalizzazione:** esegui
  `git log --since="14 days ago" -- thesis/$ARGUMENTS.md`. Se la tesi è stata modificata
  negli ultimi 14 giorni, prepara l'avviso esplicito da mettere IN CIMA al memo.
- **Carica la memoria:** leggi gli ultimi 2-3 memo di `$ARGUMENTS` in `memos/` e l'INTERA
  tabella `data/$ARGUMENTS.md`. Entra nell'analisi sapendo cosa è già successo.
- **Anti-iperattività:** se `$ARGUMENTS` è stato rivalutato più volte in pochi giorni senza
  nuovi fatti materiali, segnalalo come da `CLAUDE.md`.

### Fase 1 — Ricerca & materialità
Cerca notizie/dati dall'ultimo memo in poi (o ultimi ~3 mesi se primo memo) SOLO da fonti
attendibili (comunicati ufficiali, filing SEC/Consob, earnings call, Reuters/Bloomberg/FT/
WSJ/Il Sole 24 Ore). Classifica ogni elemento MATERIALE / NON MATERIALE / DA CHIARIRE
contro una specifica ipotesi. Distingui FATTO da OPINIONE DI MERCATO. Cita ogni fonte
materiale con data e URL.

### Fase 1-bis — Traiettoria
Esamina l'andamento delle metriche-chiave su più revisioni dalla tabella. Cerca erosioni
lente verso una soglia, conviction in calo, raccomandazioni che oscillano. Ragiona sulla
DIREZIONE. Questo conta anche se nessuna singola notizia è materiale.

### Fase 2 — Le tre voci (separate e obbligatorie)
- **🟢 Analyst:** valuta evento + traiettoria contro la tesi → MANTIENI/AUMENTA/RIDUCI/ESCI
  con confidenza e ragionamento.
- **🔴 Red Team:** PRIMA conduci una ricerca ostile esterna (short thesis / bear case del
  titolo) con fonti vere, POI demolisci la raccomandazione dell'Analyst. Vietato essere
  d'accordo per cortesia.
- **⚖️ Arbiter:** pesa i rischi ASIMMETRICI, emetti la DECISIONE FINALE motivata, segnala
  ogni disaccordo non risolto come punto aperto da monitorare.

### Fase 3 — Salva memo, aggiorna metriche e dashboard
a) Scrivi il memo completo in `memos/$ARGUMENTS_AAAA-MM-GG.md` (data odierna) seguendo
   `memos/_TEMPLATE.md`. Metti l'avviso anti-razionalizzazione in cima se applicabile.
b) Aggiungi una riga a `data/$ARGUMENTS.md` con data, metriche-chiave numeriche,
   raccomandazione e conviction (1-10). Crea il file da `data/_TEMPLATE.md` se manca.
c) Aggiorna la riga di `$ARGUMENTS` in `dashboard.md`.

Chiudi ricordando all'utente di **dormirci sopra** prima di decidere, e di committare i
file aggiornati.
