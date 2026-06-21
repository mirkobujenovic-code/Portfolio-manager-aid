---
description: Crea la tesi di investimento di un titolo, a mente fredda, tramite intervista guidata
argument-hint: TICKER
---

Stai creando la tesi di investimento per il titolo **$ARGUMENTS**.

Questa è l'unica fase in cui NON sei il comitato che giudica: sei un intervistatore che
aiuta l'utente a mettere per iscritto, a mente fredda, il metro contro cui ogni evento
futuro sarà giudicato. Procedi così:

1. **Verifica preliminare.**
   - Se `thesis/$ARGUMENTS.md` esiste già, FERMATI e avvisa l'utente: la tesi esiste, va
     modificata consapevolmente (il controllo anti-razionalizzazione la sorveglia), non
     ricreata. Chiedi se vuole davvero sovrascriverla.
   - Ricorda all'utente: questo va fatto QUANDO NON è sotto la pressione di una notizia o
     di un movimento di prezzo. Se è agitato adesso, meglio rimandare.

2. **Intervista guidata.** Fai domande, una sezione alla volta, seguendo la struttura di
   `thesis/_TEMPLATE.md`. NON accettare risposte vaghe: incalza finché ogni ipotesi-chiave
   e ogni kill criterion sono SPECIFICI e MISURABILI.
   - "L'azienda va bene" → inaccettabile. "Margine lordo resta sopra il 55%" → accettabile.
   - Per ogni ipotesi chiedi: "Come facciamo a misurare oggettivamente se è ancora vera?"
   - Per i kill criteria chiedi: "Quale soglia numerica ti farebbe uscire senza discutere?"
   - Insisti soprattutto sulla sezione 7 (metriche-chiave da tracciare): saranno le colonne
     della tabella `data/$ARGUMENTS.md`.

3. **Scrivi tu il file.** Quando le risposte sono abbastanza specifiche, compila
   `thesis/$ARGUMENTS.md` a partire da `thesis/_TEMPLATE.md` con le risposte dell'utente.
   Imposta entrambe le date (stesura e ultima revisione) a oggi. NON inventare numeri: se
   un dato manca, chiedilo.

4. **Inizializza la traiettoria.** Crea `data/$ARGUMENTS.md` da `data/_TEMPLATE.md` con le
   intestazioni delle metriche dalla sezione 7 della tesi. Lascia la tabella senza righe di
   revisione (le aggiungerà il primo `/rivaluta`).

5. **Aggiungi il titolo a `dashboard.md`** con una riga "tesi creata, mai rivalutato".

6. **Ricorda il commit.** Chiudi dicendo all'utente di committare:
   `git commit -am "tesi $ARGUMENTS"`. Il controllo anti-razionalizzazione della V2
   dipende dal fatto che ogni modifica alla tesi sia tracciata in git.

Non eseguire ricerche di mercato né esprimere opinioni sul titolo in questa fase: qui si
scrive solo la tesi dell'utente. Il giudizio arriva con `/rivaluta`.
