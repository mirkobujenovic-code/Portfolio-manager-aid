# Criteri di settore — SOFTWARE (USA)

> Questo file È la memoria dei criteri. Ogni screening e ogni scarto devono citare una
> regola scritta qui, con il suo codice (es. `Q2`, `V4`). Se un criterio non è qui, non
> esiste: non si scarta un titolo per un motivo non scritto.

**Stato:** bozza da ratificare — le soglie sono proposte ragionate, non ancora confermate
**Ultima modifica:** 2026-09-08
**Sottosettore:** Software applicativo e infrastrutturale (GICS: Software & Services)

---

## Filosofia

Cerchiamo **aziende di qualità il cui PREZZO si è dislocato, non il cui BUSINESS si è
rotto.** Tutta l'architettura serve a separare queste due cose. Il ribasso è la condizione
di ingresso; la qualità è ciò che rende il rimbalzo probabile invece che sperato.

Il rischio dell'approccio contrarian è comprare un business in declino secolare perché
"costa poco". I filtri Q e i veto R esistono per questo.

---

## Architettura a quattro stadi

Non un elenco piatto di filtri in AND — restituirebbe zero risultati. Quattro stadi con
funzioni diverse:

| Stadio | Codice | Funzione | Effetto |
|--------|--------|----------|---------|
| 1. Universo + dislocazione | `U` | filtri rigidi | migliaia → decine |
| 2. Cancelli di qualità | `Q` | superare tutti | decine → una dozzina |
| 3. Punteggio | `S` | ordina i sopravvissuti | classifica |
| 4. Veto | `R` | red flag, esclusione secca | rimuove a prescindere dal punteggio |

---

## Stadio 1 — Universo e dislocazione (`U`)

Filtri rigidi, automatizzabili via screener.

| # | Criterio | Soglia proposta | Perché |
|---|----------|-----------------|--------|
| `U1` | Mercato | USA, azioni ordinarie, listing primario | Perimetro dichiarato |
| `U2` | Capitalizzazione | ≥ $10B | Perimetro dichiarato |
| `U3` | Volume medio 3 mesi | ≥ 500.000 azioni/giorno | Liquidità, perimetro dichiarato |
| `U4` | Industria | Software—Application / Software—Infrastructure | Sottosettore |
| `U5` | Storia pubblica | ≥ 3 anni di bilanci | Senza storico non si giudica una traiettoria; esclude IPO recenti |
| `U6` | **Dislocazione** | **≥ 35% sotto il massimo a 52 settimane** | È il filtro che definisce l'intera strategia |

### Nota su `U6` — la dislocazione
Registra sempre **due** numeri, non uno: il calo dal massimo a 52 settimane e quello dal
massimo storico. Servono a distinguere casi diversi:
- **Caduta rapida** (>25% in meno di un mese): shock da earnings o notizia singola. Spesso
  il setup contrarian migliore, ma anche quello dove il rischio "tesi rotta" è più alto.
- **Erosione lenta** (calo su 12-18 mesi): de-rating strutturale. Più pericoloso — spesso
  il mercato sta prezzando un declino reale, non un'esagerazione.

Questa distinzione non scarta nulla in automatico: è un dato che l'analista deve avere in
mano prima di giudicare.

---

## Stadio 2 — Cancelli di qualità (`Q`)

Devono essere superati **tutti**. Servono a garantire che sotto il prezzo caduto ci sia un
business vero. Soglie calibrate sul software, non generiche.

| # | Criterio | Soglia proposta | Perché questa soglia |
|---|----------|-----------------|----------------------|
| `Q1` | Margine lordo | ≥ 65% | Sotto il 60% probabilmente non è software puro ma servizi travestiti: modello economico diverso |
| `Q2` | Margine FCF (TTM) | ≥ 10% | Contrarian + qualità significa cassa generata, non bruciata. Chi brucia cassa in una correzione dipende dai mercati per sopravvivere |
| `Q3` | Crescita ricavi (TTM) | ≥ 5% | Sotto, è un cubetto di ghiaccio che si scioglie: il rimbalzo non arriva |
| `Q4` | Debito netto / EBITDA | ≤ 3,0x (o cassa netta) | Il software non deve essere levereggiato; la leva trasforma una correzione in un problema di solvibilità |

---

## Stadio 3 — Punteggio (`S`)

Non escludono: **ordinano** i sopravvissuti allo stadio 2. Serve a decidere quali 3-5
titoli meritano l'analisi profonda, che è costosa.

| # | Metrica | Eccellente | Buono | Debole |
|---|---------|-----------|-------|--------|
| `S1` | **Regola del 40** (crescita % + margine FCF %) | ≥ 40 | 30-40 | < 30 |
| `S2` | ROIC | ≥ 15% | 10-15% | < 10% |
| `S3` | SBC / ricavi | ≤ 10% | 10-15% | > 15% |
| `S4` | Diluizione annua azioni | ≤ 1% | 1-3% | > 3% |
| `S5` | Sconto su EV/Sales vs **propria** mediana 5 anni | ≥ 40% | 25-40% | < 25% |
| `S6` | FCF yield | ≥ 5% | 3-5% | < 3% |
| `S7` | Net Revenue Retention | ≥ 115% | 105-115% | < 105% |

### Nota su `S5` — economicità relativa
Il confronto è con la **storia del titolo stesso**, non con il mercato o con i concorrenti.
Un software a 8x EV/Sales può essere l'occasione del decennio se ha sempre trattato a 15x;
uno a 4x può essere caro se il suo business è peggiorato in modo permanente. Il contrarian
compra lo sconto rispetto a sé stesso, verificando che il "sé stesso" non sia cambiato.

### Nota su `S7` — dato non automatizzabile
La NRR non è nei feed standard: va estratta dalla presentazione trimestrale o dal 10-K.
La compila l'agente di settore in fase di analisi, non lo screener.

---

## Stadio 4 — Veto (`R`)

Una sola di queste **esclude il titolo**, indipendentemente da quanto sia a sconto o da
quanto sia alto il punteggio. Sono le trappole contabili e strutturali del software.

| # | Red flag | Soglia | Perché è un veto |
|---|----------|--------|------------------|
| `R1` | SBC / ricavi | > 25% | L'utile "adjusted" è finzione: gli azionisti pagano il personale con diluizione |
| `R2` | Divario GAAP vs non-GAAP | in allargamento da ≥ 3 trimestri | Il management sta spostando i costi fuori dalla metrica che comunica |
| `R3` | Restatement, cambio revisore o filing in ritardo | negli ultimi 24 mesi | Rischio contabile non prezzabile |
| `R4` | Avviamento / totale attivo | > 50% **e** ≥ 3 acquisizioni in 3 anni | Roll-up: la crescita è comprata, non generata. Rischio svalutazioni |
| `R5` | Concentrazione clienti | cliente singolo > 15% dei ricavi | Un solo contratto può rompere la tesi |
| `R6` | DSO (giorni di incasso) | +15% YoY senza spiegazione | Ricavi riconosciuti ma non incassati: qualità dei ricavi in calo |
| `R7` | Billings / ricavi differiti | in decelerazione più rapida dei ricavi per ≥ 2 trimestri | I ricavi futuri stanno rallentando prima di quelli riportati |
| `R8` | Debito netto / EBITDA | > 4,0x | Solvibilità |

---

## Tassonomia della caduta (qualitativo — compito dell'analista)

Superati gli stadi 1-4, resta **la domanda che decide tutto: perché è caduto?** Nessun
numero risponde. L'analista deve classificare la dislocazione in una di queste categorie e
dichiararlo nell'analisi.

| Cat. | Causa della caduta | Giudizio | Procede? |
|------|-------------------|----------|----------|
| `A` | Compressione dei multipli (tassi, rotazione settoriale, macro) | Il business è intatto, è cambiato solo il prezzo | ✅ caso migliore |
| `B` | Rallentamento della crescita ciclico (budget IT congelati) | Reversibile se il ciclo gira | ✅ con cautela |
| `C` | Errore di esecuzione una tantum (guidance mancata, riorganizzazione) | Reversibile se il management è credibile | ✅ con cautela |
| `D` | Minaccia competitiva strutturale (es. disruption AI del prodotto) | Il fossato si sta erodendo davvero | ❌ scarta |
| `E` | Problema contabile o di governance | Non prezzabile | ❌ scarta |
| `F` | Declino secolare del prodotto o del mercato | Il ribasso è giustificato | ❌ scarta |

**Solo A, B e C passano all'analisi profonda.** D, E, F si scartano citando la categoria.

> ⚠️ La categoria `D` è oggi la più insidiosa nel software: molti cali del 2025-26 sono il
> mercato che si chiede se l'AI renda obsoleto il prodotto. Distinguere una paura eccessiva
> (opportunità) da una disruption reale (trappola) è il lavoro più difficile e più
> importante dell'agente di settore.

---

## Catalizzatore

Un titolo di qualità e a sconto può restare tale per anni. Prima di promuoverlo a
candidato, l'analista deve nominare **almeno un catalizzatore identificabile** entro
12-18 mesi: ritorno alla crescita, margini in recupero, cambio di management, fine di un
investimento pesante, riacquisto azioni, uscita da un contenzioso, catalizzatore di settore.

Nessun catalizzatore nominabile → **monitora**, non investire.

---

## Disponibilità dei dati

| Automatizzabile via screener (FMP) | Richiede lettura dei filing (agente) |
|-----------------------------------|--------------------------------------|
| `U1-U6`, `Q1-Q4`, `S1-S6`, `R1`, `R4`, `R6`, `R8` | `S7` (NRR), `R2`, `R3`, `R5`, `R7`, tassonomia, catalizzatore |

Lo screening automatico porta da migliaia a ~10-15 nomi; il resto è lavoro dell'analista
sui documenti.

---

## Punti da ratificare

Prima di considerare questo file operativo, vanno confermate o corrette:

1. **`U6` — soglia di dislocazione al 35%.** È il parametro che definisce la strategia.
   Più alta (50%) = pochi nomi, molto stressati. Più bassa (25%) = più nomi, meno
   dislocati.
2. **`Q3` — crescita minima al 5%.** Accetteresti un'azienda in leggero calo di ricavi ma
   molto redditizia e a forte sconto, oppure la crescita è imprescindibile?
3. **`S1` — regola del 40 a 40.** Per società mature da $10B+ è esigente; 30 è più
   realistico. Quale vuoi come riferimento?
4. **Soglia di ingresso all'analisi:** quanti titoli vuoi che l'agente analizzi a fondo
   per ciclo di screening? (proposta: i primi 3-5 per punteggio)
