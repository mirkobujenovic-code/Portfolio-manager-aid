# Specifiche — Tool di screening settoriale per azioni US

Documento di progetto. Consegnalo a Claude Code come primo file della cartella di lavoro,
prima di chiedere qualsiasi codice.

Autore delle specifiche: sessione di progettazione del 18 agosto 2026.
Ambiente di destinazione: Windows, Python già installato.

---

## 1. Cosa deve fare il programma

Individuare azioni US sopra i 10 miliardi di capitalizzazione che sono **di buona qualità
ma temporaneamente penalizzate dal mercato**, distinguendole da quelle penalizzate a ragione.

Il programma copre gli stadi meccanici. Gli stadi di giudizio (perché è sceso, se la causa
regge) restano all'analista e non vanno automatizzati.

| Stadio | Chi lo esegue |
|---|---|
| 1 · Universo | programma |
| 2 · Classificazione | programma (etichetta) + analista (casi ambigui) |
| 3 · Dislocazione | programma |
| 4 · Qualità | programma |
| 5 · Archetipo | analista |
| 6 · Stabilizzazione | programma (segnali) + analista (lettura) |

Il primo settore è **software US**. La struttura deve essere replicabile su altri settori
cambiando solo i file di configurazione, mai il codice.

---

## 2. Vincoli reali, verificati sul campo

Non sono ipotesi: sono stati testati chiamando le API il 18 agosto 2026.

### FMP — piano gratuito
Funziona **solo** l'endpoint profilo società:

```
GET /stable/profile?symbol=XXX
```

Restituisce: `price`, `marketCap`, `beta`, `range` (formato `"min-max"` a 52 settimane),
`change`, `changePercentage`, `volume`, `averageVolume`, `companyName`, `industry`,
`sector`, `country`, `exchange`, `cik`, `ipoDate`, `isEtf`, `isFund`, `isAdr`.

Bloccati sul gratuito, richiedono piani superiori:
- `search-company-screener` — lo screener (Starter+)
- `batch-quote` — quote multiple in una chiamata (Premium+)
- tutti gli endpoint bilancio, ratio, key-metrics

### Alpha Vantage — piano gratuito
25 chiamate al giorno, 5 al minuto. Endpoint utili: `COMPANY_OVERVIEW`, `CASH_FLOW`,
`BALANCE_SHEET`, `INCOME_STATEMENT`, `EARNINGS`, `GLOBAL_QUOTE`.
`REALTIME_BULK_QUOTES` è a pagamento.

### Trappola già scoperta, da evitare
Il campo `MarketCapitalization` di Alpha Vantage **si aggiorna più lentamente del prezzo**.
Derivare il prezzo come `marketCap / sharesOutstanding` produce valori sbagliati: su Synopsys
dava 389 contro un prezzo reale di 413. Usare sempre `GLOBAL_QUOTE` (Alpha Vantage) o il
campo `price` del profilo (FMP).

### Conseguenza architetturale
Con i piani gratuiti: **FMP per lo screening largo** (un profilo per titolo, illimitato) e
**Alpha Vantage per l'approfondimento** sui sopravvissuti (max 25 chiamate al giorno).
Il codice deve supportare entrambi e degradare in modo pulito quando un endpoint è bloccato.

---

## 3. Struttura dei file

```
screening/
  config/
    settori/
      software.yaml          universo, bucket, soglie specifiche
    soglie_comuni.yaml       invarianti validi per tutti i settori
    .env                     FMP_API_KEY, ALPHAVANTAGE_API_KEY  (mai su git)
  src/
    fetch.py                 chiamate API, retry, rate limiting, cache
    metrics.py               tutte le formule, funzioni pure senza I/O
    screen.py                applica soglie, assegna bucket e stato
    report.py                genera JSON e dashboard HTML
  templates/
    dashboard.html           modello con segnaposto per i dati
  data/
    raw/                     risposte API grezze, cache su disco
    software-2026-08-18.json output dello screening
    note/                    note e tesi scritte a mano, mai sovrascritte
  output/
    dashboard.html           file finale da aprire nel browser
  main.py                    orchestratore
  requirements.txt
  README.md
```

**Regola non negoziabile: la cache dei dati grezzi.** Ogni risposta API va salvata su disco
con timestamp. Ricalcolare con soglie diverse non deve mai richiedere di riscaricare.
È ciò che rende possibile la calibrazione descritta al punto 9.

`metrics.py` deve contenere **funzioni pure**: input numeri, output numeri, nessuna chiamata
di rete e nessuna lettura di file. Solo così sono testabili.

---

## 4. Le formule

### 4.1 Drawdown dal massimo a 52 settimane
```
drawdown = (prezzo / massimo_52w) - 1
```

### 4.2 Distanza dal minimo a 52 settimane
```
sopra_minimo = (prezzo / minimo_52w) - 1
```

### 4.3 Drawdown residuo — sottoperformance rispetto al settore
Isola quanta parte del calo non è spiegata dal movimento del settore.
```
drawdown_atteso  = beta_settoriale * drawdown_settore
drawdown_residuo = drawdown_effettivo - drawdown_atteso
```
Il `drawdown_settore` si calcola sulla mediana dei titoli del settore, oppure su un ETF di
riferimento (per il software: IGV). Il `beta_settoriale` è il beta del titolo contro il
settore, non contro il mercato — se non calcolabile, usare il beta di mercato dal profilo
FMP e annotare l'approssimazione.

### 4.4 Quota di de-rating
Separa la compressione del multiplo dal taglio delle stime. In logaritmi le due componenti
si sommano esattamente.
```
r_prezzo   = ln(prezzo_oggi / prezzo_al_picco)
r_multiplo = ln(multiplo_oggi / multiplo_al_picco)
r_utili    = ln(eps_fwd_oggi / eps_fwd_al_picco)

quota_de_rating = r_multiplo / r_prezzo        # vale che r_prezzo ≈ r_multiplo + r_utili
```
Sopra il 70% significa che il mercato ha cambiato idea sull'azienda, non l'azienda.
Sotto il 50% è un reset degli utili e il prezzo ha solo seguito.

**Il dato difficile è `eps_fwd_al_picco`**: serve lo storico del consenso, che nessuna delle
due fonti gratuite conserva. Approssimazione accettabile: ricostruire l'EPS trailing a
12 mesi alla data del picco dall'endpoint `EARNINGS` di Alpha Vantage. Il codice deve
**marcare esplicitamente il valore come approssimato**, mai presentarlo come esatto.

### 4.5 Compressione del multiplo contro la propria storia
Terza forma di dislocazione, quella che nessuno screener trova.
```
percentile_multiplo = percentile del multiplo forward attuale
                      nella distribuzione dei suoi valori a 3 anni
```
Sotto il 25° percentile è dislocazione anche senza alcun calo di prezzo.

### 4.6 Compensi in azioni sui ricavi
```
sbc_su_ricavi = stock_based_compensation_TTM / ricavi_TTM
```
Da `CASH_FLOW` di Alpha Vantage, campo `stockBasedCompensation`, sommato sui 4 trimestri.

### 4.7 Margine di cassa al netto dei compensi in azioni
La metrica che ha eliminato Zscaler. Sempre calcolare entrambe le versioni e mostrarle
affiancate.
```
fcf              = flusso_operativo - capex
margine_fcf      = fcf / ricavi
fcf_netto_sbc    = fcf - stock_based_compensation
margine_fcf_netto = fcf_netto_sbc / ricavi
```

### 4.8 Rule of 40
```
rule_of_40 = crescita_ricavi_percentuale + margine_fcf_netto_percentuale
```
Usare **sempre** la versione al netto della SBC. Con quella lorda, Zscaler segnava 53
(promosso) invece di 29 (bocciato).

### 4.9 Variazione del numero di azioni
Aggiunta dopo il caso Synopsys, dove la SBC era al 10,3% (superata) ma le azioni erano
cresciute del 23% per l'emissione legata all'acquisizione Ansys.
```
diluizione_3y = (azioni_oggi / azioni_3_anni_fa) - 1
```
Da misurare **indipendentemente dalla causa**: compensi, acquisizioni, aumenti di capitale.

### 4.10 Segnali di stabilizzazione
Servono almeno due su quattro.
```
nuovi_minimi_recenti  = il minimo degli ultimi 30 giorni è > minimo_52w * 1.02
sopra_media_50        = prezzo > sma_50
forza_relativa        = rendimento_30g_titolo - rendimento_30g_settore > 0
rsi_divergenza        = nuovo minimo di prezzo senza nuovo minimo di RSI(14)
```

---

## 5. Le soglie

Vanno in `config/`, mai nel codice. Questi sono i valori di partenza, **non calibrati**.

### soglie_comuni.yaml — validi per ogni settore
```yaml
universo:
  market_cap_min: 10_000_000_000
  volume_medio_min: 500_000
  paesi: [US]
  borse: [NYSE, NASDAQ]
  escludi_etf: true
  escludi_fondi: true
  escludi_adr: true
  anni_storia_min: 3

dislocazione:
  drawdown_min: 0.20          # soglia di ammissione
  drawdown_allarme: 0.50      # oltre, segnala rottura strutturale
  percentile_multiplo_max: 25 # forma C
  quota_de_rating_min: 0.70

qualita_invarianti:
  fcf_positivo_anni_su_5: 4
  copertura_interessi_min: 4.0
  debito_netto_ebitda_max: 3.0
  crescita_ricavi_min: 0.0    # decelerazione sì, contrazione no
  trimestri_margine_lordo_non_in_calo: 6
  diluizione_3y_max: 0.06
```

### settori/software.yaml
```yaml
nome: Software
industrie_fmp:
  - "Software - Application"
  - "Software - Infrastructure"
etf_riferimento: IGV

soglie:
  margine_lordo_min: 0.65
  rule_of_40_min: 30
  sbc_su_ricavi_max: 0.15
  debito_netto_ebitda_max: 2.0

percentile_nel_bucket:
  metriche: [roic, margine_lordo, conversione_fcf, stabilita_margini]
  quantile_min: 0.60          # top 40%
  metriche_da_superare: 3
```

**Importante per la replicabilità.** Cambiando settore cambiano le soglie del terzo strato
(la SBC è centrale nel software, irrilevante in una utility) e soprattutto **l'asse di
segmentazione**: nel software è l'esposizione alla disruption AI, nei semiconduttori sarà
il ciclo, nell'healthcare la scadenza dei brevetti. Restano invece identici le tre forme di
dislocazione, la scomposizione del ribasso, i tre strati di qualità e i cinque archetipi.

---

## 6. Classificazione in bucket

Nel software i bucket misurano l'esposizione alla tesi di disruption AI.

**Passaggio grossolano, automatico, da sotto-industria.** Mappa in `software.yaml`:

```yaml
bucket:
  bassa:   # output non generabile da un modello, lock-in tecnico
    parole_chiave: [EDA, semiconductor design, security, firewall, observability,
                    database, infrastructure, identity, network]
    esempi: [SNPS, CDNS, PANW, CRWD, ZS, CHKP, OKTA, GEN, FFIV, DDOG, DT, MDB,
             SNOW, NTNX, FROG, DOCN, FTNT, NET, RBRK]
  media:   # SaaS verticale, dati proprietari, workflow regolamentato
    esempi: [INTU, VEEV, MANH, TRMB, PTC, ADSK, ADBE, JKHY, SSNC, IOT, TOST,
             ORCL, PLTR, TYL, GWRE]
  alta:    # licenza per postazione, output replicabile da un modello
    esempi: [CRM, NOW, HUBS, ZM, DOCU, TEAM, TWLO, U, APP, WDAY]
```

**Passaggio fine, manuale, solo sui casi ambigui.** Tre domande fattuali, da porre
all'analista e registrare nel JSON:
1. Il pricing scala col numero di dipendenti del cliente?
2. L'output è generabile da un modello?
3. Il valore sta nei dati proprietari o nel software?

**Decisione di progetto rivista.** Il bucket alto era stato escluso, ma è lì che si concentra
la dislocazione maggiore. Non va escluso: va sottoposto a una barra più alta — pricing non
a postazione, oppure crescita che accelera nonostante la narrativa, oppure evidenza che
l'AI stia aumentando l'uso del prodotto anziché sostituirlo.

---

## 7. Stato di ogni titolo

Tre valori, che guidano il colore nella dashboard:

- `verde` — ha superato tutti gli stadi calcolati finora, resta candidato
- `giallo` — da chiarire: dati mancanti, classificazione dubbia, archetipo con riserva
- `rosso` — eliminato, con il campo `eliminato_a` che indica lo stadio

Nel JSON ogni titolo porta anche `gates`, un array di sei valori
(`pass` / `fail` / `warn` / `pend` / vuoto) che alimenta la barra degli stadi.

---

## 8. Generazione della dashboard

Il meccanismo è volutamente semplice.

`templates/dashboard.html` contiene il segnaposto:
```html
<script>
/* @@DATI@@ */
</script>
```

`report.py` legge il JSON, lo serializza e sostituisce il segnaposto, scrivendo
`output/dashboard.html`. Il modello non viene mai modificato dal programma.

Il file HTML di riferimento esiste già ed è allegato al progetto
(`screening-software-us.html`): contiene i quattro tab, la barra degli stadi, il codice
colore verde/giallo/rosso e la sezione metodologia. Va usato come modello di partenza,
estraendone i dati per lasciare il segnaposto.

**Note dell'analista.** Sono l'unica cosa che il programma non deve mai sovrascrivere.
Vanno tenute in `data/note/` come file separati per ticker e reinserite a ogni rigenerazione.

**Evoluzione successiva, non subito.** Per avere un pulsante di aggiornamento dentro la
dashboard serve un piccolo server locale (FastAPI o Flask) che custodisca le chiavi API e
esponga un endpoint di refresh. La chiave non deve mai finire dentro l'HTML.

---

## 9. Casi di verifica

Prima di scrivere qualunque altra cosa, verificare che le formule riproducano questi valori.
Sono dati reali FMP del 18 agosto 2026. I prezzi cambieranno: quello che deve tornare è la
**formula applicata a questi input**, quindi usarli come test unitari con valori fissi.

| Ticker | Prezzo | Min 52w | Max 52w | Drawdown atteso | Sopra minimo atteso |
|---|---|---|---|---|---|
| ORCL | 143.73 | 114.50 | 345.72 | −58.4% | +25.5% |
| INTU | 353.93 | 252.84 | 719.10 | −50.8% | +40.0% |
| TYL  | 343.21 | 270.71 | 575.93 | −40.4% | +26.8% |
| GWRE | 180.09 | 102.30 | 272.60 | −33.9% | +76.0% |
| ADBE | 265.85 | 190.12 | 370.86 | −28.3% | +39.8% |
| WDAY | 195.25 | 110.36 | 249.85 | −21.9% | +76.9% |

Test sui compensi in azioni — Zscaler, esercizio chiuso il 31 luglio 2025, dati Alpha Vantage:
```
ricavi_TTM            = 3_173_564_000
sbc_TTM               =   782_986_000
flusso_operativo_TTM  = 1_100_973_000
capex_TTM             =   210_713_000

attesi:
  sbc_su_ricavi       = 24.7%   → oltre la soglia del 15%, eliminato
  margine_fcf         = 28.0%
  margine_fcf_netto   =  3.4%
  rule_of_40 (netta)  = 25.4 + 3.4 = 28.8   → sotto 30, eliminato
  rule_of_40 (lorda)  = 25.4 + 28.0 = 53.4  → passerebbe, ed è l'errore da evitare
```

---

## 10. Fasi di sviluppo

Non scrivere tutto insieme. Ogni fase deve funzionare prima della successiva.

**Fase 1 — Scaricamento e dislocazione.** `fetch.py` più `metrics.py`. Legge una lista di
ticker da file, scarica i profili FMP, calcola drawdown e distanza dal minimo, salva JSON.
Criterio di successo: i sei casi di verifica tornano esatti.

**Fase 2 — Qualità.** Aggiunge Alpha Vantage per bilanci e rendiconto, con rate limiting a
25 al giorno e cache che non riscarica mai due volte. Criterio di successo: il test Zscaler
torna esatto.

**Fase 3 — Dashboard.** `report.py` genera l'HTML dal modello. Criterio di successo: un
comando e il file si apre aggiornato nel browser.

**Fase 4 — Universo automatico.** Richiede un piano FMP a pagamento. Sostituisce la lista
manuale con lo screener. Criterio di successo: sul software US ritrova almeno i 38 nomi già
noti più Tyler, Guidewire, Workday, Fortinet, Cloudflare e Rubrik.

**Fase 5 — Interattività.** Server locale con pulsante di aggiornamento. Solo se serve
davvero.

---

## 11. Errori già commessi, da non ripetere

Elencati perché sono costati tempo in fase di progettazione.

**Prezzo derivato dalla capitalizzazione.** Il campo capitalizzazione è più vecchio del
prezzo. Usare sempre il prezzo diretto.

**Universo incompleto e invisibile.** La lista iniziale, esportata da uno screener esterno,
non conteneva Tyler Technologies, Guidewire, Workday, Fortinet, Cloudflare e Rubrik — tutti
qualificati, e due fra i più dislocati del settore. È l'errore più pericoloso perché ciò che
manca non compare da nessuna parte. Il programma deve **registrare i criteri esatti** con cui
ha costruito l'universo, in modo che siano verificabili.

**Classificazione settoriale sbagliata.** Trimble risultava software su una fonte e hardware
su un'altra. Dove possibile, confrontare due tassonomie e segnalare i disaccordi anziché
sceglierne una in silenzio.

**Diluizione misurata solo dai compensi.** Vedi formula 4.9.

**Quota di de-rating fuorviante.** Presuppone che le stime siano informative. Quando il
mercato prezza una minaccia strutturale pluriennale, le stime sono la variabile ritardataria
e la quota risulta vicina al 100% per l'intero settore, perdendo ogni potere discriminante.
Il programma deve calcolarla ma **segnalare quando l'intero settore supera la soglia**,
perché in quel caso il filtro non sta filtrando.

---

## 12. Calibrazione

Le soglie del punto 5 sono ipotesi ragionevoli, non numeri validati. Prima di fidarsene:

Aggiungere un comando che stampi la **mortalità per stadio**: quanti titoli entrano e quanti
sopravvivono a ogni cancello. Se lo stadio qualità ne elimina 55 su 60, la barra è troppo
alta e si sta costruendo un muro. Grazie alla cache, provare margine lordo al 60, 65 e 70
deve richiedere secondi e zero chiamate API.

**Retrotest contro il bias di sopravvivenza.** Per ogni titolo de-ratato che poi rimbalza ce
n'è uno dove la narrativa aveva ragione, e quello non si ricorda perché è sparito dal radar.
Le regole vanno testate sulla popolazione completa che le soddisfaceva negli anni 2022-2025,
non sui casi che si ricordano. Se la quota di de-rating non separa i vincitori dai perdenti
nei dati storici, il filtro è sbagliato e va cambiato.

---

## 13. Cosa questo programma non fa

Non decide cosa comprare. Produce una lista corta di candidati con i motivi tracciabili di
ogni scarto. Le decisioni di ingresso, dimensionamento e uscita restano fuori dal codice.

Non sostituisce lo stadio archetipo, che richiede di leggere comunicati, trimestrali e
notizie per capire perché un titolo è sceso e se quella ragione regge.

Non è consulenza finanziaria e le sue soglie non sono validate.
