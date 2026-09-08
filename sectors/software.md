# Software US — riconciliazione dei criteri

**Aggiornato:** 2026-09-08

> Questo file non contiene più soglie. Dopo l'integrazione della specifica del
> 18-08-2026 le fonti autorevoli sono:
> - **`screening/SPECIFICHE.md`** — il progetto completo: stadi, formule, vincoli API,
>   casi di verifica, fasi di sviluppo. È il documento di riferimento.
> - **`screening/config/*.yaml`** — i numeri, versionati e modificabili senza toccare codice.
> - **`screening/templates/dashboard-riferimento.html`** — il modello della dashboard, con
>   la metodologia estesa (tre forme di dislocazione, cinque archetipi, tre strati di qualità).
>
> Qui resta solo ciò che serve a un lettore umano: **cosa è cambiato e cosa resta aperto.**

---

## Cosa ha sostituito la mia bozza

La bozza che avevo scritto il 08-09 è stata superata: la specifica del 18-08 è più matura
su quasi tutto. Dove divergevano, ha vinto la specifica:

| Punto | Mia bozza | Specifica (adottata) | Perché vince la specifica |
|-------|-----------|----------------------|---------------------------|
| Rule of 40 | crescita + margine FCF **lordo** | **al netto della SBC** | Caso Zscaler: 53,4 lordo (promosso) vs 28,8 netto (bocciato). La mia versione lasciava passare un titolo che va scartato |
| Dislocazione | solo drawdown ≥ 35% | **tre forme in OR** oltre il 20% | Il solo drawdown esclude i casi migliori: il titolo fermo mentre il settore sale, e quello più economico pur essendo ai massimi |
| Diluizione | ≤ 1-3% annuo | **≤ 6% su 3 anni, per qualsiasi causa** | Caso Synopsys: SBC al 10,3% superata, ma azioni +23% per l'emissione Ansys. La causa non conta, l'effetto sì |
| Perché è caduto | 6 categorie descrittive (A-F) | **5 archetipi + 1 anti-archetipo, ciascuno con un test** | I miei descrivevano, i suoi diagnosticano da firme osservabili (dove cade il gap, stime intatte o no, peer fermi o no) |
| Leva | ≤ 3,0x | **≤ 2,0x nel software** | Nel de-rating il rifinanziamento arriva quando il multiplo è compresso |
| Soglie | proposte a tavolino | **dichiarate non calibrate**, con procedura di calibrazione | Onestà intellettuale: sa di non sapere |

Le uniche due cose mie che sopravvivono perché mancavano nella specifica: **NRR** e
**catalizzatore** (vedi sotto, correzioni 4 e 5).

---

## Le otto correzioni che proporrei

In ordine di importanza. Le prime tre sono contraddizioni interne al documento, non
questioni di gusto.

### 1. Il funnel arriva a zero: le soglie sono un muro, non un filtro
Il risultato reale del 18-08: **69 → 38 → 16 → 6 → 2 → 0**. Zero titoli pronti, due
arrivati all'archetipo. E applicando la regola sulla diluizione a 3 anni (§4.9, max 6%),
**Synopsys esce** (+23% di azioni), lasciando di fatto la sola Intuit.

Uno screener che produce 0-1 nomi non sta selezionando: sta rifiutando. Il collo è lo
stadio qualità, che elimina 10 su 16 (62%). La §12 prevede già l'antidoto — stampare la
mortalità per stadio e ricalcolare dalla cache — ma **non è ancora stato eseguito**.

> **Prima di scrivere altro codice: eseguire la calibrazione.** È l'unico modo di sapere
> se margine lordo al 65% e Rule of 40 a 30 stanno selezionando qualità o stanno solo
> alzando un muro.

### 2. La quota di de-rating è configurata come cancello e documentata come inutilizzabile
La §5 la mette a `quota_de_rating_min: 0.70`. La §11 dice che quando l'intero settore
supera la soglia il filtro non filtra — che è **esattamente la situazione del software
oggi**, sotto la narrativa AI. Così com'è, elimina i titoli per un fattore comune a tutti.

**Correzione applicata nella config:** declassata da cancello a etichetta diagnostica
(`usa_come_cancello: false`), con allarme quando il settore intero supera la soglia.

### 3. I bucket sono liste di ticker: contraddicono la lezione più importante della §11
La §11 dice che l'errore più pericoloso è l'universo incompleto, «perché ciò che manca non
compare da nessuna parte». Poi la §6 assegna i bucket con liste di ticker scritte a mano:
un titolo non in lista resta non classificato e sparisce in silenzio — lo stesso errore,
uno stadio più avanti.

**Correzione applicata:** i bucket si assegnano per regole (sotto-industria + le tre
domande fattuali), le liste restano come seed/override, e i non classificati escono
**gialli**, mai scartati in silenzio.

### 4. Manca la Net Revenue Retention
Probabilmente la singola metrica di qualità più predittiva nel software, assente dal
documento. Non è nei feed gratuiti, ma è in ogni presentazione trimestrale. Aggiunta come
campo obbligatorio in fase di analisi: sotto 105% è un problema, sopra 115% è eccellenza.

### 5. Manca il catalizzatore
Qualità e sconto possono coesistere per anni senza che succeda nulla. Lo stadio 6
(stabilizzazione) è timing tecnico, non una ragione fondamentale per cui il mercato
dovrebbe cambiare idea. **Aggiunto:** nessun catalizzatore nominabile entro 18 mesi →
verdetto `monitora`, mai `candidato`.

### 6. Il collo di bottiglia Alpha Vantage è sottostimato
25 chiamate al giorno, e lo stadio qualità richiede ~4 endpoint per titolo
(`CASH_FLOW`, `BALANCE_SHEET`, `INCOME_STATEMENT`, `EARNINGS`): **circa 6 titoli al
giorno**. Per i 16 dislocati servono tre giorni. Non è un dettaglio di rate limiting, è un
vincolo strutturale sul ciclo di lavoro.

> ⚠️ **Da verificare prima di progettare intorno a questo limite:** la sessione Claude Code
> ha ora FMP e Alpha Vantage collegati come server MCP, che potrebbero non avere gli stessi
> limiti delle chiavi gratuite. Un test cambierebbe l'architettura.

### 7. Il beta di mercato come proxy del beta settoriale distorce il drawdown residuo
La §4.3 ammette l'approssimazione e chiede di annotarla. Ma sui titoli software ad alto
beta l'approssimazione **sovrastima sistematicamente il drawdown atteso**, quindi
sottostima il residuo: nasconde proprio i titoli che stanno sottoperformando il settore,
cioè quelli che la forma B dovrebbe trovare. Il beta settoriale è calcolabile dalle serie
storiche che entrambe le API forniscono. In alternativa, declassare la forma B a
diagnostico finché non è calcolata bene.

### 8. «Margine lordo non in calo per 6 trimestri» è fragile e sistemicamente distorto
Un solo trimestre a −10 punti base fa fallire il cancello. Peggio: il documento stesso
osserva che i costi di inferenza AI stanno comprimendo i margini lordi **di tutto il
settore** — quindi il cancello elimina per un fenomeno sistemico, non per un difetto
aziendale.

**Correzione applicata:** pendenza di regressione su 8 trimestri con tolleranza, e
confronto con la mediana di settore invece che soglia assoluta.

---

## Cosa NON cambierei

- **La soglia di dislocazione al 20%** (io avevo proposto 35%). Con tre forme in OR, 20% è
  la soglia di *ammissione*, non di conviction — e il funnel mostra che è già selettiva
  (38 → 16). La mia proposta era tarata su una sola forma.
- **L'ordine degli stadi**, dal filtro più economico al più costoso. È la scelta di
  progetto più solida del documento.
- **La cache obbligatoria dei dati grezzi.** È ciò che rende possibile la calibrazione, ed
  è la ragione per cui il tool Python va costruito comunque invece di fare tutto con
  l'agente: deterministico, testabile, a costo zero per ricalcolo.
- **I casi di verifica.** Ho ricontrollato l'aritmetica di tutti e sette: i sei drawdown e
  il test Zscaler tornano esatti. Sono buoni test unitari.

---

## Domande aperte per te

1. **Calibrazione prima o dopo?** Il mio consiglio è prima — costruire le fasi 1-2 e girare
   la mortalità per stadio, senza toccare le soglie finché non ci sono i dati.
2. **Il bucket "alta esposizione"** concentra la dislocazione maggiore ed è dove la tesi
   contrarian ha più senso, ma è anche dove il rischio di categoria `D`/declino secolare è
   reale. La barra aggiuntiva proposta ti convince?
3. **Testiamo i limiti dei server MCP** prima di progettare intorno alle 25 chiamate/giorno?
