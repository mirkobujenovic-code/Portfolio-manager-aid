# Comitato di investimento V2 — guida

Sistema basato su Claude Code per togliere l'emotività dalle decisioni sui singoli
titoli. Non monitora in tempo reale e non esegue ordini: quando una notizia ti agita
(o periodicamente), lanci un comando e un **comitato a tre voci** rivaluta la tua tesi
e ti consegna un memo da leggere a mente fredda.

## Cosa c'è di nuovo nella V2

Tre accorgimenti che chiudono le falle della prima versione:

1. **Controllo anti-razionalizzazione della tesi.** Se modifichi `thesis/TICKER.md`
   negli ultimi 14 giorni, il comitato lo rileva via git e lo dichiara in cima al memo
   con un avviso. Impedisce che, in un momento emotivo, tu riscriva la tesi per
   giustificare un impulso. (Per questo git è ora OBBLIGATORIO — vedi sotto.)

2. **Red Team con ricerca ostile separata.** Il Devil's Advocate, prima di leggere
   l'Analyst, cerca attivamente il bear case / lo short thesis del titolo da fonti
   esterne. Porta munizioni vere, non solo l'inverso logico: un challenge molto più duro.

3. **Memoria della traiettoria.** Ogni revisione salva le metriche-chiave in una
   tabella `data/TICKER.md`. Il comitato la rilegge sempre e ragiona sull'ANDAMENTO,
   non solo sul valore puntuale — così cattura i deterioramenti lenti (es. margine
   56→54→52%) che nessun singolo kill criterion vede.

## Le tre voci
- 🟢 **Analyst** — valuta l'evento e la traiettoria contro la tua tesi
- 🔴 **Red Team** — ricerca ostile esterna + attacca la valutazione dell'Analyst
- ⚖️ **Arbiter** — pesa entrambi e decide, segnalando i disaccordi aperti

## Installazione (una volta sola)

1. **Installa Claude Code:**
   ```bash
   npm install -g @anthropic-ai/claude-code
   ```
   (Richiede Node.js 18+.)

2. **Metti questa cartella** dove vuoi ed entraci:
   ```bash
   cd percorso/della/investment-committee-v2
   ```

3. **Inizializza git — OBBLIGATORIO in V2** (serve al controllo anti-razionalizzazione):
   ```bash
   git init && git add -A && git commit -m "setup comitato v2"
   ```
   Da qui in poi, dopo ogni modifica a una tesi, fai un commit. Così il sistema sa
   quando e come le tue tesi cambiano.

4. **Avvia Claude Code** dalla cartella:
   ```bash
   claude
   ```
   Legge automaticamente `CLAUDE.md` con tutte le regole del comitato.

## Uso quotidiano

**Per ogni titolo, una volta — crea la tesi (a mente fredda!):**
```
/nuova-tesi AAPL
```
Claude ti guida con domande finché le ipotesi sono specifiche. Non scrivi tu il file:
lo compila lui dalle tue risposte. Poi committa: `git commit -am "tesi AAPL"`.

**Quando una notizia ti agita, o ogni X settimane:**
```
/rivaluta AAPL
```

**Revisione periodica di tutto il portafoglio (es. trimestrale):**
```
/portafoglio
```

**Per vedere lo stato di tutto:** apri `dashboard.md`.

## Struttura cartelle
```
investment-committee-v2/
├── CLAUDE.md            ← regole del comitato (caricato in automatico)
├── dashboard.md         ← cruscotto, indice di tutti i titoli
├── thesis/              ← una tesi per titolo (la tua, a mente fredda)
│   ├── _TEMPLATE.md
│   └── _ESEMPIO.md
├── memos/               ← storico dei memo decisionali, datati
│   └── _TEMPLATE.md
├── data/                ← tabelle metriche = MEMORIA della traiettoria
│   ├── _TEMPLATE.md
│   └── _ESEMPIO.md      ← mostra un deterioramento lento da catturare
└── .claude/commands/    ← /nuova-tesi, /rivaluta, /portafoglio
```

## Promemoria
- Rileggi il memo, **dormici sopra**, e solo dopo decidi. Il sistema esiste per
  inserire quella pausa, non per sostituire il tuo giudizio.
- Se ti accorgi di voler cambiare la tesi solo per giustificare un impulso: è
  esattamente il momento per cui hai costruito tutto questo. L'avviso anti-
  razionalizzazione è lì per renderti quel momento visibile.
- Spendi token solo quando lanci `/rivaluta` o `/portafoglio`: on-demand.
