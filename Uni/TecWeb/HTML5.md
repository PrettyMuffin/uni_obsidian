## Intestazione
L'intestazione è la parte contenuta tra i tag `<head>` e `</head>` viene chiamata **intestazione** o semplicemente, sezione **head**.
In questa sezione si trovano tutti i tag che impartiscono direttive ai browser quali:
- Titolo (Obbligatorio)
- Comandi meta
- Richiami ai fogli di stile, script

Tutti ciò che si trova all'interno della struttura head non sarà visualizzato ma interpretato dal browser.
==La sezione **head** quindi è una zona destinata ad uso esclusivo dei soli comandi che impartiscono direttive ben specifiche==. 

>[!info] Tag sezioni
>- `<title>` (obbligatorio), `link`, `meta`, `base`, `style` e `script`
>- `link` definisce un collegamento ad una risorse esterna (CSS, shortcut icon, etc)
>  Gli attributi più comuni sono: `href`, `rel`(relazione)
>  ```html
>  <link rel="stylesheet" type="text/css" href="/file.css" />
>  <link rel="shortcut incon" href="/img.co" />
>  <link rel="next" title="Next Page" href="/next.html" />
>  ```
>- `base` definisce la posizione di base per i collegamenti
>  `<base href="/miacartella/" />`

### Tag META
La sezione head contiene una serie di comandi, chiamati _MetaComandi_, che non producono alcuna variazione visiva sulla pagina, ma sono indispensabili per altre attività quali la validazione e la lettura da parte dei motori di ricerca. 
I _metacomandi_ inseriscono informazioni aggiuntive sul contenuto del documento che si sta creando, come ad esempio l’autore. ==Non esistono limitazioni sul numero di metatag inseriti==.
Ci sono due tipi di tag meta:
- `http-equiv`
- `name`

##### Tag Meta http-equiv
L'attributo `http-equiv` sta per "HTTP equivalent". Il suo scopo è dire al browser: "Fai finta che questo comando sia arrivato in un header HTTP dal server".
Il browser, durante la fase di parsing (analisi) iniziale dell'HTML, incontra questo tag `<meta>` e lo processa **come se fosse un'istruzione di sistema**, prima ancora di aver renderizzato il contenuto vero e proprio della pagina.
```html
<meta http-equiv="refresh" content="15" />
<meta http-equiv="refresh" content="1;URL=nuovaURL" />
<meta http-equiv="expires" content="5" />
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
```

###### L'Analogia: L'Istruttore e l'Assistente

Pensa a questa situazione:

1. Lo Scenario Corretto (Server HTTP): Un insegnante (**il server HTTP**) dà un'istruzione a tutta la classe prima di distribuire un foglio di esercizi: _"Questo documento è in italiano e va letto dall'alto verso il basso"_. Poi distribuisce il foglio (**il documento HTML**). La classe (**il browser**) sa già come comportarsi con il documento prima ancora di leggerlo.
    
2. Lo Scenario Meta http-equiv: L'insegnante distribuisce il foglio di esercizi senza dire nulla. Ma in cima al foglio, la prima riga in grassetto recita: **"ISTRUZIONE PER LA CLASSE: comportatevi come se il professore vi avesse detto che questo documento è in italiano"**. La classe legge questa istruzione e poi processa il documento di conseguenza.
    

Il tag `<meta http-equiv>` fa esattamente questo: **simula un'intestazione HTTP** direttamente all'interno del documento HTML, poiché per qualche motivo (ad esempio, non si ha accesso alla configurazione del server) non è possibile farlo dal server vero e proprio.

##### Tag Meta Name
Sono dati riguardanti altri dati. In questo caso, sono informazioni _sul_ documento HTML, non il contenuto del documento stesso. Servono a categorizzare, descrivere e fornire un contesto alla pagina.
Il funzionamento si basa su una coppia **nome-valore**:
- `name=""`: Definisce **il tipo di informazione** che stai fornendo (es. "autore", "descrizione").
- `content=""`: Definisce **il valore effettivo** di quell'informazione (es. "Mario Rossi", "Questa è una guida sui meta tag").

###### L'Analogia: La Scheda Catalogo della Biblioteca
Pensa a questa situazione:
1. Il Libro (Il documento HTML): È il contenuto principale, la storia, le informazioni che l'utente finale vuole effettivamente leggere.
2. La Scheda del Catalogo (I tag `<meta name>`): Nella biblioteca, ogni libro ha una scheda che non fa parte della storia del libro stesso, ma fornisce **informazioni _sul_ libro**. Questa scheda contiene campi predefiniti come "Autore", "Titolo", "Data di Pubblicazione", "Argomento". Il bibliotecario e i lettori usano queste schede per capire di cosa parla il libro senza doverlo leggere per intero.
    
Il tag `<meta name="">` fa esattamente questo: **crea una scheda di metadati** che descrive il documento HTML. A differenza di `http-equiv` che dava _istruzioni al browser_, `name` fornisce _informazioni sul documento_ a chiunque (o qualsiasi cosa) lo stia leggendo: browser, motori di ricerca, bot dei social media, ecc.
```html
<meta name="description" content="Una guida semplice e completa per comprendere il funzionamento e l'utilizzo dei tag meta in HTML.">
<meta name="keywords" content="meta tag, HTML, SEO, guida, web development">
<meta name="author" content="Mario Rossi">
<meta name="robots" content="index, follow">
```
- **`description`**: Fornisce il breve riassunto che vedi sotto il titolo blu nei risultati di ricerca. È il tuo "biglietto da visita".
    
- **`keywords`**: Una volta fondamentale, oggi è largamente ignorato dai motori di ricerca principali a causa di abusi. Rimane utile per ricerche interne al sito.
    
- **`author`**: Chi ha scritto la pagina.
    
- **`robots`**: Istruisce i "robot" dei motori di ricerca su cosa fare ("indicizza questa pagina", "segui i link al suo interno").

Alcuni metadati possono influenzare direttamente come il browser imposta la visualizzazione.
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="theme-color" content="#3a7bd9">
```
- **`viewport`**: **Assolutamente cruciale per il web moderno.** Dice al browser di adattare la larghezza della pagina alla larghezza dello schermo del dispositivo (mobile, tablet) e di partire senza zoom. Senza di questo, i siti su mobile appaiono "zoommati out".
- **`theme-color`**: Suggerisce al browser di colorare la barra degli strumenti (su Chrome per Android) o altri elementi dell'interfaccia con un colore specifico, per un'esperienza più integrata.

Piattaforme come Facebook e LinkedIn hanno creato i loro propri "nomi" standardizzati (protocollo Open Graph) per controllare come un link viene visualizzato quando viene condiviso.
```html
<meta property="og:title" content="La Guida Definitiva ai Meta Tag">
<meta property="og:description" content="Scopri tutto ciò che serve sapere...">
<meta property="og:image" content="https://example.com/anteprima.jpg">
<meta property="og:url" content="https://example.com/guida-meta-tag">
```
- **`og:title`**: Il titolo della "scheda" sul social.
- **`og:image`**: L'immagine di anteprima. È forse il tag più importante per massimizzare l'engagement su un social.
- Nota: Qui si usa `property` invece di `name`, ma il concetto è identico.

>[!important] Creare valori Propri
>Il sistema `name` è **estendibile**. Si possono creare valori propri:
>```html
><meta name="piattaforma" content="WordPress 6.0">
><meta name="livello_difficolta" content="Principiante">
><meta name="tempo_di_lettura" content="5 minuti">
>```

## Corpo del Documento
slide 38 html