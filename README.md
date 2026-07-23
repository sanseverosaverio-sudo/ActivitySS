# Activity Register — Engineering Ops Log

Dashboard locale per la gestione delle attività quotidiane: elenco attività, dashboard con grafici, e un tab "Progress Report" per generare report esecutivi (con Gantt) da presentare a management/VP.

Nessun server, nessun database, nessun account: è un singolo file HTML autosufficiente.

## Avanzamento attività e criticità

Ogni attività ha:
- **Barra di avanzamento 0-100%** impostabile a mano. L'app registra **data e ora** in cui imposti la percentuale: da questo il Progress Report calcola se sei **in anticipo o in ritardo**, confrontando la % dichiarata con la quota di tempo lavorativo effettivamente trascorso tra inizio e fine pianificati. Il timestamp si aggiorna solo quando cambi davvero la percentuale, così una modifica ad altri campi non falsa il calcolo.
- Sezione **Issues & Lessons Learned** (pieghevole): criticità riscontrate, **ore perse** a causa loro, possibili soluzioni, e next step per evitarle in futuro. Compaiono nella card attività e nel Progress Report, con due KPI dedicati: *Behind Schedule* e *Hours Lost to Issues*.

## Leggibilità

La palette è stata ricalcolata verificando i rapporti di contrasto WCAG. In precedenza il grigio secondario (`--muted-2`) era a 3.00:1, sotto il minimo AA di 4.5:1 — difficile da leggere in ambiente illuminato. Ora tutti i colori di testo superano AA nel caso peggiore:

| Colore | Contrasto (worst case) |
|---|---|
| testo principale | 14.2:1 |
| grigio primario | 8.5:1 |
| grigio secondario | 5.5:1 |
| arancio / teal / rosso / giallo | 5.5-9.4:1 |

Le dimensioni dei font più piccoli (8.5-10px) sono state portate a un minimo di 10.5-11px.

Il **Gantt** ha barre più alte con riempimento scuro che indica la % completata, etichette su due righe (titolo + prodotto), linee di griglia verticali e marcatore "Today". Gli stili di stampa forzano `print-color-adjust`, così le barre restano visibili e a colori anche nel **PDF esportato** (i browser altrimenti eliminano gli sfondi in stampa).

## Archiviazione ed eliminazione

Attività, componenti e fornitori (nel tab New Products) hanno un pulsante **Archive/Restore**: archiviare nasconde la voce dalle liste di default (senza cancellarla) — usa la casella **"Show Archived"** vicino ai filtri per rivederle in qualsiasi momento. È reversibile ("Restore" per farla riapparire).

Esiste anche l'**eliminazione permanente** (pulsante Delete/✕ separato da Archive), che invece cancella davvero il record (e per i componenti/fornitori, anche i file allegati in IndexedDB).

**Gestione prodotti**: dal footer, pulsante **"Manage Products…"**, apre un pannello dove ogni prodotto (inclusi quelli già presenti come Product A/Alpha/Beta/ecc.) può essere:
- **Archiviato** — sparisce dalle liste di creazione nuove attività/componenti, ma resta filtrabile nei dati storici
- **Eliminato permanentemente** — anche se già usato da attività/componenti esistenti; in tal caso viene mostrato un avviso con quante attività/componenti lo referenziano, spiegando che quei record manterranno il nome del prodotto come testo ma quel valore non sarà più selezionabile/filtrabile come voce a sé nella lista prodotti

## Contenuto del pacchetto

```
.
├── index.html      → l'intera applicazione (UI, logica, grafici)
├── README.md       → questo file
└── .gitignore
```

## Pubblicazione su GitHub Pages

1. Crea un nuovo repository su GitHub (pubblico o privato, entrambi supportano Pages con account Pro/Team/Org; con account free serve repo pubblico).
2. Carica il contenuto di questa cartella nella root del repository (branch `main`), ad esempio:
   ```bash
   git init
   git add .
   git commit -m "Activity Register dashboard"
   git branch -M main
   git remote add origin https://github.com/<tuo-utente>/<tuo-repo>.git
   git push -u origin main
   ```
3. Su GitHub: **Settings → Pages**.
4. In **Build and deployment → Source**, seleziona **Deploy from a branch**.
5. In **Branch**, seleziona `main` e cartella `/ (root)`, poi **Save**.
6. Dopo un paio di minuti la dashboard sarà online su:
   `https://<tuo-utente>.github.io/<tuo-repo>/`

Nessuna build, nessuna dipendenza da installare: è HTML/CSS/JS puro.

## Dati e privacy — tutto resta locale

- Tutte le attività e i prodotti inseriti vengono salvati **solo nel browser** dell'utente (tramite `localStorage`), sul dispositivo/browser specifico usato.
- Non esiste alcun invio di dati a server esterni: GitHub Pages serve solo i file statici, non riceve né memorizza le attività inserite.
- Dati salvati in un browser **non sono visibili** aprendo la stessa pagina da un altro browser o dispositivo.
- Svuotare la cache/i dati di navigazione del browser (o usare la navigazione in incognito) cancella i dati salvati.

### Backup ed esportazione

In fondo alla pagina sono disponibili:
- **Export Backup (JSON)** — scarica un file `.json` con tutte le attività e i prodotti.
- **Import Backup** — carica un backup precedente (sostituisce i dati correnti, con conferma).
- **Load Sample Data** — ripopola la dashboard con attività di esempio dimostrative.

Consigliato: esportare un backup periodicamente, specialmente prima di svuotare la cache del browser o passare a un altro dispositivo.

## Ore R&D

Quando si spunta **R&D Support Required** (nel form o nelle bozze di Voice Capture), compare un campo per indicare **quante ore di R&D** sono state effettivamente usate su quell'attività. In Dashboard sono disponibili:

- KPI **R&D Hours Used** (totale nel periodo selezionato)
- Grafico **R&D Hours by Product** — ore R&D sommate per prodotto
- Grafico **R&D Hours by Activity** — ore R&D per singola attività (le 12 con più ore, nel periodo selezionato)

Entrambi i grafici rispettano il filtro data (From/To) già presente in Dashboard.

## Calcolo delle ore

Tutte le ore mostrate in Dashboard, Progress Report e nelle card delle attività **contano solo le ore lavorative**, non la differenza di calendario grezza:

- Solo **lunedì-venerdì** (sabato e domenica non contano)
- Solo dalle **9:00 alle 18:00**, con la **pausa pranzo 13:00-14:00 esclusa** (8 ore effettive al giorno)
- Notti e weekend intermedi in attività multi-giorno non vengono conteggiati

Esempio: un'attività che inizia venerdì alle 16:00 e finisce lunedì alle 10:00 conta **3 ore** (2h venerdì pomeriggio + 1h lunedì mattina), non 66 ore di calendario.

Il Gantt chart nel Progress Report continua invece a mostrare la durata reale sul calendario (per la corretta rappresentazione visiva della timeline) — solo le **somme numeriche delle ore** (KPI, grafici, tabelle) usano il calcolo a ore lavorative.

## Struttura dell'app (tab)

1. **Activity List** — form di inserimento (titolo, descrizione, prodotto, priorità, date inizio/fine, tipo attività NPI/Support/Altro, ECR/OCN con numero, note, supporto R&D, stato completata) + elenco filtrabile. Include il pulsante **🎤 Voice Capture** (vedi sotto).
2. **Dashboard** — KPI, grafici a torta/barre su tempo per prodotto, tipo attività e priorità, tabelle di dettaglio con percentuali. Filtro per intervallo di date.
3. **Progress Report** — selezione libera di attività (per qualsiasi criterio: prodotto, stato, ecc.) → report esecutivo con riquadri *Completed / WIP / Next Steps*, Gantt generale sulla timeline delle attività selezionate, grafici con valori/percentuali, e pulsante **Print / Export PDF** (richiede un browser reale — non l'anteprima file in-app — per aprire il dialogo di stampa/esportazione PDF).

**Campi per componente** (sezione "Component Details", espandibile): Type, Part Number, Legacy PN, ECN Number, Description, Source, DFM Updates, Last Update, Quality Critical (flag), Qty per kit, Inventory, TRO Approved (flag), **Final Reports (upload file)**, Notes.

**Campi per fornitore/quotazione** (uno o più per componente, in sezioni espandibili):
- *Sourcing & Commercial*: Mfg Name, Mfg Part Number, Reseller, M/B (Make/Buy), Contact for Quote, Lead Time (settimane), **fasce prezzo multiple** (una riga per ogni scaglione Qty/MOQ → Unit Cost, con totale calcolato automaticamente — un fornitore quota quasi sempre prezzi diversi per volumi diversi), e subito sotto l'**upload dei file di quotazione**
- *Dates*: PR/IR/SR Date, Target PO Date, Target Forecast, Effective PO Date, Promise Date, Effective Arrival Date
- *Quality & Status*: Sample Parts, Samples Validation, Supplier Validation (distinta dalla validazione campioni — es. audit/qualifica del fornitore), Compliance, Note fornitore
- Priority (1. CRITICAL/1. HIGH/2. MED/3. LOW), Pendings (R&D/Quote/LT/CER/Validation/TRO/Quality/PO/Supply-chain), Owner — sempre visibili in alto
- File allegati categorizzati (Quotation/Compliance/Sample Report/Validation Report/Other)

Questi campi replicano quelli del foglio "Materials" di un dashboard NPI di riferimento fornito dall'utente, mantenendo però il supporto nativo a **più fornitori in parallelo per componente** (il foglio originale ne prevedeva uno solo per riga).

## Tab 04 — New Products (RFQ / Supplier Tracker)

Traccia il processo di introduzione di nuovi prodotti/componenti, dalla richiesta di quotazione alla produzione in serie.

**Panoramica avanzamento prodotto** (in cima al tab): per ogni prodotto esistente mostra:
- **Stato Quotazioni** e **Stato Validazione** — calcolati automaticamente in % (quanti componenti hanno un fornitore rispettivamente "Selected"/"Passed" sul totale dei componenti tracciati per quel prodotto)
- **Work Instructions**, **FAI Production**, **First 100u Batch**, **Mass Production** — 4 stati impostati manualmente (dropdown), uno per prodotto

**Componenti &amp; Fornitori**: per ogni componente (es. "Alloggiamento in alluminio") puoi tracciare più fornitori in parallelo, ciascuno con: Stato Quotazione (Not Requested/Requested/Received/Selected/Rejected), Stato Sample, Stato Compliance, Stato Validazione — e **caricare file** (quotazioni, report compliance, report campioni/validazione) categorizzati per tipo, uno per fornitore.

**Nota sui file allegati:** sono salvati tramite IndexedDB del browser (non localStorage, che ha limiti di spazio troppo bassi per documenti) — restano quindi solo su questo browser/dispositivo. Il limite per singolo file è 8MB. L'**Export Backup** include i dati di componenti/fornitori/stati ma **non il contenuto dei file allegati** (solo i loro nomi) — i documenti stessi non sono portabili tramite backup JSON.

## Registro cartaceo → Photo Import

Per chi preferisce segnare le attività a penna durante la giornata (es. in officina, senza smartphone a portata), sono inclusi due strumenti aggiuntivi, raggiungibili anche dal footer dell'app principale:

### 🖨️ activity-template-print.html — Template stampabile
Foglio A4 orizzontale con una riga per attività e tutti i campi dell'app: Prodotto, Descrizione, Inizio, Fine, Priorità (caselle H/M/L), Tipo (caselle N/S/O), Supporto R&D (casella + ore), ECR#, OCN#, Note. Apri il file nel browser e usa "Print / Save as PDF". **Scrivi in stampatello maiuscolo** e **spunta le caselle con una X piena** per la massima affidabilità in fase di scansione.

### 📷 photo-import.html — Scansione e importazione
1. Fotografa (o carica) il foglio compilato.
2. Trascina i 4 angoli arancioni sui **soli bordi esterni delle righe compilate** (non includere la riga dei titoli) — corregge automaticamente la prospettiva della foto.
3. Indica quante righe hai compilato → il tool ritaglia la griglia ed elabora ogni cella:
   - **Priorità, Tipo, Supporto R&D**: rilevati per via automatica analizzando se la casella è spuntata (affidabile, non serve leggere la scrittura)
   - **Prodotto, Descrizione, Date, ECR#, OCN#, Ore R&D, Note**: lette via riconoscimento testo (OCR) — meno affidabile sulla scrittura a mano, quindi **ogni campo è mostrato con l'immagine della cella originale accanto**, modificabile prima di salvare
4. Rivedi/correggi ogni riga, poi **"Add to Activity Register"** — le attività vengono **aggiunte** a quelle esistenti, mai sostituite o cancellate.

**Nota tecnica:** la libreria di riconoscimento testo (Tesseract.js) viene scaricata una volta da CDN pubblico al primo utilizzo (richiede quindi una connessione internet quella prima volta), ma l'elaborazione della foto avviene sempre interamente nel browser — l'immagine non viene mai caricata su alcun server. Photo Import legge/scrive lo stesso storage locale dell'app principale, quindi funziona automaticamente insieme ad essa se ospitati sullo stesso dominio GitHub Pages.

## Voice Capture (100% locale)

Dal tab **Activity List**, il pulsante **🎤 Voice Capture** apre una finestra che permette di:

1. Registrare un messaggio vocale (usa il riconoscimento vocale integrato del browser — funziona su Chrome/Edge; su browser non supportati si può scrivere il testo direttamente).
2. Analizzare il testo con un **parser locale a regole**: separa automaticamente più attività dette in sequenza e prova a riconoscere prodotto, priorità, date/orari, durata, tipo attività, riferimenti ECR/OCN e necessità di supporto R&D dalle parole chiave usate.
3. Rivedere e correggere ogni bozza generata (tutti i campi sono modificabili) prima di confermare l'aggiunta al registro.

**Importante:** né l'audio né il testo trascritto vengono mai inviati a server esterni — tutta l'elaborazione avviene nel browser. Il parser è a regole (keyword-based), quindi può sbagliare con frasi complesse o ambigue: per questo motivo le attività vengono sempre proposte come bozze da confermare, mai salvate automaticamente.

Parole chiave riconosciute (esempi): `alta/media/bassa priorità`, `urgente`, `NPI`, `supporto team`, `R&D`, `ECR 1234`, `OCN 5678`, `oggi/domani/dopodomani`, giorni della settimana, `alle 9`/`alle 14:30`, `per 2 ore`.

## Compatibilità

- Nessuna dipendenza esterna richiesta per il funzionamento (i grafici sono generati con SVG nativo, non con librerie esterne).
- I font (Google Fonts) sono caricati da CDN solo per l'estetica: se non disponibili (es. offline), l'app ricade automaticamente su font di sistema senza perdere funzionalità.
- Layout responsive: testato per schermi da smartphone a desktop.
