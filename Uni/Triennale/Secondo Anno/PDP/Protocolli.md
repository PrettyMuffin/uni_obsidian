Abbiamo più volte detto, parlando di messaggi e connessioni, che le due parti devono stabilire un _protocollo_ per delimitare le parti funzionali della comunicazione stessa:
- Inizio del messaggio
- Completamento della comunicazione
- Disposizione ed interpretazione del contenuto

Ovviamente, per rendere più semplice la comunicazione fra programmi differenti è preferibile usare un protocollo standard, adatto al caso d'uso della comunicazione stessa.
Fin dall'inizio di internet, il lavoro sugli standard di comunicazione è stato cruciale per raggiungere gli obbiettivi dell'architettura di rete.

## OSI Layers
È detto Modello OSI (Open Systems Interconnection) ed individua i diversi livelli della comunicazione in cui le funzionalità dei protocolli usati vanno a collocarsi.
Il modello è contemporaneo all'elaborazione della famiglia degli "Internet Protocols" da parte della IETF.
Il suo uso però è più generale e riguarda sopratutto la collocazione del ruolo funzionale di un apparato o di un software.
Il modello OSI è formato da 7 livelli

| # Livello       | Unità     | Funzionalità                                                  | Protocolli                      |
| --------------- | --------- | ------------------------------------------------------------- | ------------------------------- |
| 7 Applicazione  | Dati      | Applicazioni: web, stream video/audio, file                   | HTTP/S, FTP, SMTP, XMPP         |
| 6 Presentazione | Dati      | Codifica, compressione, crittografia                          | TLS, GZIP, HTTP/S               |
| 5 Sessione      | Dati      | Collegamento persistente <br>con scambio di più messaggi      | PPTP; RPC; SMPP                 |
| 4 Trasporto     | Segmento  | Affidabilità (connessioni) <br>e distribuzione (multicasting) | TCP, UDP, RSVP, QUIC            |
| 3 Rete          | Pacchetto | Comunicazione strutturata ed indirizzata                      | IPv4/v6/Sec, ICMP, RIP          |
| 2 Datalink      | Frame     | Suddivisione della comunicazione                              | PPP, Wifi, Zigbee, FDDI         |
| 1 Fisico<br>    | Bit       | Specifiche trasmissive                                        | USB, Ethernet, Bluethooth, SATA |

### FTP
Uno dei primi protocolli ad essere definiti è proprio FTP (File Transfer Protocol).
Il suo uso è lo spostamento di file da e verso un server specifico, operato con un client del protocollo stesso.
Il protocollo funziona in due modalità: modalità attiva e modalità passiva.
1. **Attiva**:
   - Il client si connette al server, e apre una porta in ingresso
   - Il client informa il server della porta a cui può essere contattato
   - Il server apre una connessione verso il client sulla porta indicata
1. **Passiva**:
   - Il client si connette al server con quella che diventa la connessione di controllo
   - Chiede al server la modalità, e ottiene una porta da chiamare
   - Il client apre una seconda connessione, per i dati, alla porta indicata

I comandi hanno una sintassi semplice e leggibile, e permettono di navigare le cartelle esposte dal server, importare le caratteristiche dello scambio (tipo di file, encoding) ed iniziare un caricamento o scaricamento di un file.
Per molti anni è stato il protocollo in assoluto più usato per il trasferimento di dati in forma di file.
Le risposte all'esecuzione dei comandi hanno un codice di stato interpretabile da un programma:
- 1xx: Risposta positiva preliminare
- 2xx: Risposta positiva completa
- 3xx: Risposta positiva intermedia
- 4xx: Risposta negativa Transitoria
- 5xx: Risposta negativa definitiva

Sebbene in alcuni limitati ambiti sia occasionalmente ancora in uso, le problematiche di networking e di sicurezza lo hanno reso con il tempo sempre meno pratico e consigliabile da usare.

### SMTP
SMTP (Simple Mail Transfer Protocol) è un'altro dei protocolli "storici" di internet, e probabilmente uno dei più usati ancora oggi.
SMTP si evolve da una serie di tecniche e standard precedenti che riconducevano lo scambio di mail allo scambio di file (attraverso FTP o UUCP).
Si adatta invece a trasferire messaggi fra macchine che solo occasionalmente si connettono fra loro (per es. client su rete telefonica commutata).
Per design, non c'è nessuna autenticazione e il trasporto è in chiaro, in ASCII. Questo rendeva possibile fino a tutti gli anni '90, inviare una mail semplicemente aprendo una connessione Telnet verso il server che accettava la posta, e scrivendo a mano i comandi del protocollo con header e contenuto della mail.
L'autenticazione, la gestione di encoding (il protocollo prevedeva unicamente caratteri ASCII stampabili) e file binari viene gestita da revisioni successive che introducono gli standard MIME per gli allegati e SMTP-UTF8 per i caratteri Unicode.
Nello stesso tempo, prevale la parte del protocollo relativa all'invio delle mail su quella relativa alla ricezione.
Il protocollo descrive anche i ruoli dei vari attori del processo di consegna dei messaggi (client, server di inoltro, server di conservazione, ecc.) mentre invece non specifica in modo approfondito il contenuto del singolo messaggio al di là del formato del destinatario.

### NTP
Network Time Protocol è probabilmente il protocollo da più tempo in uso attivo. Il suo utilizzo è documentato da prima del 1985.
Il suo uso permette di mantenere sincronizzati i computer collegati ad internet, ed è in grado di avvisare di eventi rilevanti come secondi intercalare. ==Non trasporta informazioni riguardanti l'ora legale==.
Il protocollo si basa su di una organizzazione a livelli (_strata_) dei server partecipanti.
![[Pasted image 20250912153944.png]]
Al livello zero vi sono le sorgenti di riferimento: GPS, orologi atomici, ecc.
Al livello 1 vi sono server collegati fisicamente ad una sorgente di riferimento.
Dal livello 2 in poi, un nodo si collega o a suoi _peer_ nello stesso livello o ad un nodo di livello superiore.
La prima versione di NTP gestiva un _timestamp_ di 64 bit:
- 32b - secondi a partire da 1900-01-01
- 32b - frazioni fino a 233 picosecondi di risoluzione

La versione attuale, NTPv4 gestisce un _timestamp_ di 128 bit:
- 32b - _Era number_ per risolvere i rollover
- 32b - Dall'ultimo epoch, cioè dall'ultimo rollover
- 64b - Frazione di secondi

NTP non solo trasporta informazioni, ma la misura del comportamento dei messaggi è essa stessa parte dello scopo del protocollo:
![[Pasted image 20250912154605.png]]
Cioè è parte integrante del protocollo misurare la latenza fra il server e il client e usare quella misura per correggere l'informazione ricevuta.
Inoltre, il protocollo può funzionare sia in modo asimmetrico, fra un client di _strata_ inferiore ed un server immediatamente superiore, che in modo simmetrico fra nodi dello stesso livello che si sincronizzano fra loro.

### HTTP
Dalla sua introduzione, nel '91, Hypertext Transfer Protocol è diventato il protocollo di maggior successo, e la base della rete come la conosciamo oggi.
HTTP è un protocollo strutturato nello scambio di una richiesta con un risposta fra un client ed un server.
È un protocollo testuale, fatto per essere facilmente gestito da apparati intermedi (chache, balancers, proxy) in modo estensibile ed efficiente.
HTTP/1, /2 e HTTPS sono protocolli di livello 7, che presumono la disponibilità di un protocollo affidabile, solitamente TCP utilizzando i socket.
HTTP/3 supera questo concetto lavorando principalmente su UDP (Datagrams).
La principale differenza fra le versioni è la gestione delle connessioni:

| HTTP    | Policy                                                            |
| ------- | ----------------------------------------------------------------- |
| HTTP1   | Una connessione per richiesta                                     |
| HTTP1.1 | Riuso della connessione per più richieste                         |
| HTTP2   | Singola connessione per stream multipli, bidirezionali, asincroni |
| HTTP3   | Trasporto QUIC e UDP                                              |
#### Richiesta
Una richiesta HTTP ha una struttura molto precisa:
Una volta stabilita la connessione il client deve mandare `GET /images/logo.png HTTP1.1`.
Che contiene il metodo (`GET`) url richiesta (`/images/logo.png`) versione HTTP (`HTTP1.1`) poi cr+lf (carriage return + line field).
Dopo questa riga possono esserci zero o più header, costituiti dal nome e valore separati da due punti che di nuovo sono separate da cr+lf.
```http
Host: www.example.com
Accept-Language: en
```

Di header ce ne sono moltissimi e una parte standardizzati, altri ne possiamo definire noi.
Dopo di questo ci deve essere una riga vuota.
Un corpo della richiesta, che è opzionale
L'url indicata nella richiesta è detta _risorsa_ alla quale si vuole accedere, con lo scopo di ottenere una _rappresentazione_ del suo stato, o di modificarlo fornendo una _rappresentazione_ dello stato desiderato.
Per ciascuna parte del protocollo ci sono regole spesso molto specifiche da seguire, in modo da definire il comportamento della richiesta, o di un apparato intermedio, in una quantità di situazioni.
I metodi disponibili sono:

| Sigla     | Scopo                                                                     |
| --------- | ------------------------------------------------------------------------- |
| `GET`     | Richiesta idempotente di lettura                                          |
| `HEAD`    | Richiesta idempotante di metadati                                         |
| `POST`    | Invio di informazioni verso una risorsa con lo scopo di crearne una nuova |
| `PUT`     | Modifica di una risorsa esistente                                         |
| `PATCH`   | Modifica **parte** di una risorsa                                         |
| `DELETE`  | Elimina una risorsa                                                       |
| `CONNECT` | Richiede una connessione verso un'altra destinazione                      |
| `OPTIONS` | Richiesta delle operazioni supportate da una risorsa                      |
| `TRACE`   | Richiede l'eco di quanto ricevuto<br>                                     |
`GET`, `HEAD`, `PUT`, `DELETE`, `OPTIONS`, `TRACE` implementati correttamente dovrebbero essre _idempotenti_, cioè resistenti alla ripetizione.
`POST`, `CONNECT`, e `PATCH` invece normalmente non si presumono tali.

#### Risposta
La risposta ha una struttura simile:
Una prima riga composta da protocollo, stato, descrizione dello stati, cr+lf
```http
HTTP/1.1 200 OK
```

Zero o più header, costituiti da nome e valore, sempre separati da due punti:
```http
Content-Type: text/html
```
che descrivono i metadati della risposta.
Riga vuota
Un corpo della riposta, opzionale.

Lo stato della risposta ha un uso simile a quello di [[#FTP]]:
- 1xx - Informazione (richiesta ricevuta, in lavorazione)
- 2xx - Successo
- 3xx - Redirezione verso un'altro indirizzo, ovvero la risorsa è disponibile ma a un indirizzo diverso.
- 4xx - Errore del client, ovvero la richiesta è sbagliata per cause che il client può risolvere
- 5xx - Errore del server, errore su cui il client non può operare.

Gli header possono controllare moltissimi aspetti della gestione della richiesta e della risposta.
Oltre ad un (vasto) elenco di header "standard", è sempre possibile aggiungerne di proprietari per scopi applicativi.


>[!info] Concludendo
>Oltre allo scopo originario di trasportare HTML, HTTP è ora usatissimo per scambiare messaggi messaggi in moltissimi formati (JSON, XML e altri) testuali e non, raggiungendo una utilità inimmaginabile al momento della sua ideazione.

## WebSockets
Dopo la diffusione delle prime implementazioni di AJAX e dei primi siti completamente moderni, la direzione client/server della struttura di HTTP comincia ad essere un problema.
Si sente la mancanza di uno strumento che consenta al server di inviare, di sua iniziativa, dati asincroni al client, questo perché prima il server doveva aspettare l'iniziativa del client per inviare dati.
La prima soluzione è il "protocollo" _Comet_, che funziona gestendo il timeout della connessione TCP.
Il client effettua una richiesta con il solo scopo di attendere una (eventuale) risposta del server.
Allo scadere della connessione, il client ripete la richiesta.

Durante l'elaborazione di HTML5, viene ideato un protocollo bidirezionale da aggiungere allo standard.
Questo protocollo viene denominato _Websockets_ e la sua prima implementazione esce in Chrome 4 nel Dicembre 2009.

Websockets non è indipendente: si appoggia ad una richiesta HTTP, e usa le funzioni di quel protocollo per negoziare un _upgrade_ della comunicazione e passare ad una connessione persistente e bidirezionale.
Il server risponde alla richiesta del client con un codice `101 Switching Protocols`, segnalando così il cambio di protocollo usato sulla stessa connessione della richiesta.

Una volta negoziato il protocollo, server e client si scambiano _Frame_ che costituiscono frammenti di _messaggi_.
I messaggi possono trasportare _dati_ diretti alle applicazioni in ascolto sia lato server che lato client, oppure _controlli_ della connessione, per es. per verificare la sua salute o iniziarne la chiusura.
Appoggiandosi al protocollo HTTP, WebSockets ne può sfruttare la facilità di gestione per attraversare Proxy ed apparati intermedi.
A seconda delle condizioni la connessione può essere inoltrata in modo trasparente o usando il comando `HTTP CONNECT` per richiedere la esplicita collaborazione dell'apparato.

## Bittorrent
Bittorrent è stato uno dei primi protocolli _peer-to-peer_, ideati cioè per realizzare reti di nodi paritari o quasi.
Lo scopo era principalmente gestire l'esplosione della domanda di contenuti multimediali, sfruttando la banda dei singoli nodi per non sovraccaricare un server centrale nell'ottenere un contenuto.
Una rete BitTorrent in un determinato momento comprende alcuni nodi _seed_, e molti nodi _client_.
![[Pasted image 20250912164655.png]]
I nodi _seed_ contengono l'intero contenuto da distribuire.
Ogni nodo _client_ ne ha una parte, ma è in grado di individuare le parti che gli mancano, e chiederle non solo all'uploader, ma ad uno qualsiasi dei _seed_ o degli altri client che già posseggono quel contenuto.

Il protocollo permette ai nodi _client_ di contattarsi a vicenda e organizzare lo scambio di parti del contenuto, evitando così, tutti insieme, di oberare di lavoro un solo _seed_ ed ottimizzando le performance globali del sistema.
Una volta completato il download, il client può passare al ruolo di _seed_ e continuando a partecipare alla diffusione del contenuto.

L'uso di hash crittografici per verificare le singole parti dei contenuti scaricati permettono di verificarne l'integrità, individuare le parti mancanti e negoziare con i nodi più veloci il loro scambio.
L'effetto complessivo è quello di raggiungere velocità molto elevate senza la necessità di un centro stella sempre presente in funzione di coordinatore.
