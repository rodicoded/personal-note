# Contatori PWA

Piccola Progressive Web App per creare e gestire contatori personali (es. ripetizioni di esercizi, abitudini, conteggi vari). Funziona offline, si può installare sulla schermata home e salva tutto nel browser: niente server, niente account, niente dipendenze.

## Funzionalità

- **Aggiungi contatori** con un titolo (pulsante *+ Aggiungi* o tasto Invio).
- **Incrementa / decrementa** con i pulsanti `−` e `+`, oppure scrivi il valore direttamente nel campo numerico.
- **Nota modificabile** sotto ogni titolo: clicca sul testo grigio per aggiungere una nota o una data (salvata quando esci dal campo).
- **Riordina** trascinando le card tramite la maniglia `☰`.
- **Elimina** un contatore con 🗑️ (nessuna conferma: l'eliminazione è immediata).
- **Offline e installabile** grazie al service worker e al manifest.

## Struttura

```
personal-note/
├── index.html      # Interfaccia, stile e logica dell'app (tutto in un file)
├── manifest.json   # Manifest PWA (nome, colori, icona)
├── sw.js           # Service worker: cache dei file per l'uso offline
└── icone/          # Icone dell'app
```

## Avvio in locale

Il service worker richiede che la pagina sia servita via `http://localhost` o `https://` (non funziona aprendo il file con doppio clic, `file://`). Dalla cartella del progetto:

```bash
# Con Python
python -m http.server 8000

# Oppure con Node
npx serve .
```

Poi apri <http://localhost:8000>.

## Installazione sul telefono

1. Pubblica la cartella su un hosting statico con HTTPS (es. GitHub Pages, Netlify, Vercel).
2. Apri l'indirizzo dal browser del telefono.
3. **Android (Chrome):** menu ⋮ → *Installa app* / *Aggiungi a schermata Home*.
   **iOS (Safari):** pulsante Condividi → *Aggiungi alla schermata Home*.

## Dati

I contatori sono salvati nel `localStorage` del browser con la chiave `app_contatori_data_v2`. Di conseguenza:

- i dati restano **solo su quel dispositivo e quel browser** (nessuna sincronizzazione);
- cancellare i dati del sito o della cronologia **cancella anche i contatori**.

Ogni contatore ha questa forma:

```json
{ "id": 1731600000000, "titolo": "Flessioni", "nota": "14/11", "valore": 10 }
```

## Pubblicare un aggiornamento

Il service worker serve i file dalla cache, quindi dopo aver modificato `index.html` (o altri file) **aumenta la versione** in `sw.js`:

```js
const CACHE_NAME = 'contatori-pwa-v1.2'; // era v1.1
```

Alla successiva apertura il browser installa la nuova versione ed elimina la cache vecchia. Potrebbe servire chiudere e riaprire l'app una volta per vedere le modifiche.

## Limitazioni note

- Il riordino con trascinamento usa il Drag & Drop HTML5, che **non funziona sui touchscreen** della maggior parte dei browser mobili.
- La cartella `icone/` non è nell'elenco dei file in cache di `sw.js`, quindi l'icona viene scaricata solo quando c'è connessione.
- Nessuna funzione di esportazione/backup dei dati.
