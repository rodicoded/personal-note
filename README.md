# Personal Note

App personale per tenere sotto controllo i piccoli numeri e le liste di tutti i giorni: contatori, obiettivi e lista della spesa, in un'unica pagina. È una Progressive Web App: funziona offline, si installa sulla schermata Home del telefono e salva tutto sul dispositivo. Niente server, niente account, niente dipendenze.

## Sezioni

Il menu ☰ in alto a sinistra (si chiude anche con uno swipe verso sinistra) porta a:

### I miei contatori
- **+** in alto a destra apre il popup *Nuovo contatore* (titolo, nota, valore iniziale).
- Pulsanti **−** e **+**, oppure scrivi il valore direttamente nel campo.
- La nota grigia sotto il titolo è modificabile con un tocco.
- **⋮** apre le azioni: *Modifica*, *Sposta*, *Elimina*.
- **Formule** (popup → *Avanzate* → *Inserisci formula*): calcoli in stile Excel sul valore, il cui risultato compare sotto il numero della carta. Si possono usare `Valore`, numeri, `+ - * / ^` e parentesi, es. `=Valore*3`, `=Valore^2`, `=Valore^(1/2)`.

### Obiettivi
- Carte con un indicatore circolare della percentuale di completamento.
- Toccando una carta si apre il dettaglio: titolo modificabile, descrizione, uno slider per la percentuale e uno spazio libero per le note.

### Lista della Spesa
- Blocco note con una casella a inizio riga: **spuntata** = da comprare (testo in evidenza), **non spuntata** = non serve o è già nel carrello (testo opaco).
- Invio crea una nuova riga, Backspace a inizio riga la unisce alla precedente, incollando un elenco ogni riga diventa una voce.
- **Tab** in basso per avere più liste indipendenti (+ per aggiungerne una, tocco sul titolo per rinominarla).
- **Condividi** copia negli appunti solo le voci spuntate (della tab corrente o di tutte), pronte da incollare in un'app di messaggi.

### Cestino
Contatori e obiettivi eliminati finiscono qui: si possono **ripristinare** nella posizione originale o eliminare definitivamente.

### Impostazioni
Tema chiaro, scuro o automatico (segue il sistema) e colore principale dell'app.

### Riordinare
Tieni premuto su una carta per circa mezzo secondo e trascinala, oppure usa *⋮ → Sposta* e le frecce ▲ ▼.

## Struttura

```
personal-note/
├── index.html      # Interfaccia, stile e logica dell'app (tutto in un file)
├── manifest.json   # Manifest PWA (nome, colori, icona)
├── sw.js           # Service worker: cache dei file per l'uso offline
└── icone/          # Icona dell'app: icona.svg (sorgente) e PNG per Android/iOS
```

Per cambiare l'icona modifica `icone/icona.svg` e rigenera i PNG alle stesse dimensioni (192, 512, 512 maskable a sfondo pieno, 180 per iOS).

## Avvio in locale

Il service worker richiede che la pagina sia servita via `http://localhost` o `https://` (aprendo il file con doppio clic, `file://`, non funziona). Dalla cartella del progetto:

```bash
python -m http.server 8000
```

Poi apri <http://localhost:8000>.

## Installazione sul telefono

1. Pubblica la cartella su un hosting statico con HTTPS (es. GitHub Pages, Netlify, Vercel).
2. Apri l'indirizzo dal browser del telefono.
3. **Android (Chrome):** menu ⋮ → *Installa app* / *Aggiungi a schermata Home*.
   **iOS (Safari):** pulsante Condividi → *Aggiungi alla schermata Home*.

## Dati

Tutto è salvato nel `localStorage` del browser, quindi:

- i dati restano **solo su quel dispositivo e quel browser** (nessuna sincronizzazione);
- cancellare i dati del sito **cancella anche il contenuto dell'app**;
- se lo storage non è disponibile (es. navigazione privata) l'app funziona lo stesso, ma non conserva nulla alla chiusura.

| Chiave | Contenuto |
| --- | --- |
| `app_contatori_data_v2` | Contatori |
| `app_contatori_obiettivi` | Obiettivi |
| `app_contatori_spesa_tabs` | Tab e righe della lista della spesa |
| `app_contatori_cestino` | Elementi nel cestino |
| `app_contatori_tema` | Tema e colore scelti |

> I nomi delle chiavi non vanno cambiati: chi usa già l'app perderebbe i propri dati.

## Pubblicare un aggiornamento

Il service worker serve i file dalla cache, quindi dopo ogni modifica **aumenta la versione** in `sw.js`:

```js
const CACHE_NAME = 'personal-note-v1.9'; // incrementa a ogni rilascio
```

Alla successiva apertura il browser installa la nuova versione ed elimina la cache vecchia. Può servire chiudere e riaprire l'app una volta.

## Limitazioni note

- Nessuna funzione di esportazione/backup dei dati.
