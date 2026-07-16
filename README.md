# Activity Register — Engineering Ops Log

Dashboard locale per la gestione delle attività quotidiane: elenco attività, dashboard con grafici, e un tab "Progress Report" per generare report esecutivi (con Gantt) da presentare a management/VP.

Nessun server, nessun database, nessun account: è un singolo file HTML autosufficiente.

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

## Struttura dell'app (tab)

1. **Activity List** — form di inserimento (titolo, descrizione, prodotto, priorità, date inizio/fine, tipo attività NPI/Support/Altro, ECR/OCN con numero, note, supporto R&D, stato completata) + elenco filtrabile.
2. **Dashboard** — KPI, grafici a torta/barre su tempo per prodotto, tipo attività e priorità, tabelle di dettaglio con percentuali. Filtro per intervallo di date.
3. **Progress Report** — selezione libera di attività (per qualsiasi criterio: prodotto, stato, ecc.) → report esecutivo con riquadri *Completed / WIP / Next Steps*, Gantt generale sulla timeline delle attività selezionate, grafici con valori/percentuali, e pulsante **Print / Export PDF** (richiede un browser reale — non l'anteprima file in-app — per aprire il dialogo di stampa/esportazione PDF).

## Compatibilità

- Nessuna dipendenza esterna richiesta per il funzionamento (i grafici sono generati con SVG nativo, non con librerie esterne).
- I font (Google Fonts) sono caricati da CDN solo per l'estetica: se non disponibili (es. offline), l'app ricade automaticamente su font di sistema senza perdere funzionalità.
- Layout responsive: testato per schermi da smartphone a desktop.
