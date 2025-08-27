>[!def]
>Teoria e tecniche per la gestione di più processi **su** **macchine diverse** in modo coordinato allo svolgimento di un unico compito.

Un insieme di macchine che esegue un algoritmo distribuito è detto sistema distribuito.

## Motivazioni e Caratteristiche
### Motivazioni
Le principali motivazione che storicamente hanno portato ad ideare la distribuzione di un algoritmo su più nodi di calcolo sono:
#### Affidabilità
Le attività possono proseguire sui nodi che sono (ancora) in linea, rispetto a quelli che sono fermi o in errore.
#### Suddivisione del carico
Una mole di lavoro più grande delle capacità di una sola macchine può essere suddivisa fra più nodi per essere eseguita in modo concorrente.
Questo è oggi il caso d'uso più comune, perché, invece di affrontare problemi più grandi di una macchina grande, si vogliono affrontare problemi con più macchine piccole. 
Perché il costo della capacità di calcolo è esponenziale in base al singolo nodo.
Questo può voler dire riscrivere il problema in modo simile a come abbiamo visto nello [stream](Stream%20Paralleli).

#### Diffusione
Gli utenti di più macchine possono accedere ai risultati del lavoro da uno qualsiasi dei nodi.
Nel caso io ho una popolazione di utenti molto spersa, posso migliorare il funzionamento del mio sistema se riesco a portare i dati di interesse di quei utenti più vicino geograficamente a loro.

### Caratteristiche
#### Concorrenza dei componenti
I vari nodi operano contemporaneamente e si possono considerare linee di esecuzioni separate, quindi tutti i metodi visti per la concorrenza possono sicuramente tornare utili.
#### Totale asincronia
L'ordine temporale degli eventi non è strettamente condiviso, e se importante deve essere imposto con opportuni mezzi.
#### Fallimenti Imprescrutabili
I nodi possono guastarsi o fallire indipendnetemente uno dall'altro. Un guasto è indistinguibile da un ritardo di consegna o una lentezza di un nodo.

---
## Comunicazione
I nodi comunicano fra loro scambiandosi messaggi in quanto non condividono altra risorse che il collegamento alla rete.
Le principali astrazioni disponibili riguardano quindi l'invio di un messaggio e l'attesa della ricezione di un messaggio.

### Messaggi e metodi
Uno dei primi tentativi di rendere più facile la gestione dell'invio e della ricezione dei messaggi in un sistema distribuito è stato quello di riportarlo a qualcosa di più simile ad una chiamata locale, mascherando la distinzione fisica fra le due macchine chiamante e chiamato.
>[!def] RPC - _Remote Procedure Call_
>Indica un sistema per rendere trasparente la localizzazione del codice chiamato, rendendolo il più possibile simile ad una chiamata locale, cioè far si che il più possibile fosse indistinguibile chiamare una procedura locale o chiamare una procedura remota.
>L'adattamento locale/remoto viene implementato da un componente spesso chiamato **stub**.

Una RPC comporta i seguenti passi:
1. Il client chiama lo _stub_ locale
2. Lo _stub_ mette i parametri in un messaggio (_marshalling_)
3. Lo _stub_ invia il messaggio al nodo remoto.
4. Sul nodo remoto, un _server stub_ attende il messaggio
5. Il _server stub_ estrae i parametri (_unmarshalling_)
6. Il _server stub_ chiama la procedura locale 
7. La risposta del _server stub_ segue il percorso da 1.

Nei linguaggi OOP questo Meccanismo prende il nome di _RMI-Remote Methond Invocation_ in quanto l'obiettivo è rendere lo scambio di messaggi equivalente alla chiamate del metodo di un oggetto locale.
Al problema della comunicazione di aggiunge quello dell'indirizzamento dell'oggetto destinatario della chiamata.
Il componente server viene chiamato _Object Broker._

Java implementa fin dalla prime versioni un sistema di _RMI_ nella sua libreria standard.

Una chiamata RMI comporta passi aggiuntivi:
1. Il client chiama lo _stub_ locale
2. Lo _stub_ prepara i parametri in un messaggio
3. Lo _stub_ invia il messaggio. Il client è bloccato.
4. Il server riceve il messaggio.
5. Il server controlla il messaggio e cerca l'oggetto chiamato
6. Il server recupera i parametri e chiama il metodo destinatario.
7. L'oggetto esegue il metodo e ritorna il risultato
8. Il _server_ prepara il risultato in un messaggio
9. Il server inva il messaggio allo _stub_
10. Lo _stub_ recupera il risultato dal messaggio
11. Lo _stub_ ritorna al client il risultato.

L'evoluzione del panorama delle reti ha reso poco pratico l'uso di tecnologie RMI, che sopravvivono solo in ambiti controllati (interazioni server/server), ed in forme a volte molto diverse da quelle originarie.
Non si cerca più di nascondere la complessità della chiamata remota, ma di rendere meno impegnativo possibile partecipare ad un servizio distribuito.
## Serializzazione
Un passo fondamentale evidenziato dai sistemi di RMI, ma chie è necessario in generale, è la cosiddetta _serializzazione_, ovvero il modo con cui un oggetto viene predisposto per la trasmissione in un messaggio.
Si dice _serializzare_ usare un meccanismo di codifica che prende direttamente l'oggetto e lo traduce in messaggio per eseguire il passo di _mashalling_.
Al contrario, deserializzare corrisponde al passo di _unmarshalling_.
Java ha un meccanismo di serializzazione nativo, tramite l'interfaccia `java.io.Serializable`.
Le problematiche che la serializzazione deve affrontare sono:
- gestire il cambiamento strutturale delle classi.
- serializzare grafi di oggetti.
- indicare oggetti che non possono/non devono essere serializzati
- assicurare l'integrità e l'affidabilità dei dati serializzati
- rendere _marshalling/unmarshalling_ efficienti in tempo e spazio

Per tutte queste motivazione l'uso della serializzazione nativa di Java è sconsigliato nella pratica normale.
Non solo, è particolarmente sconsigliato per problematiche di sicurezza.
Inoltre, per il modo in cui funzionano reti e sistemi distribuiti oggi, sono diventati praticabili/preferibili in molte situazioni protocolli testuali trasportati da HTTP e umanamente leggibili
Per questo motivo, non parleremo di serializzazione e useremo nei nostri esempi protocolli testuali semplici.

## Classi Libreria Standard
Utilizzeremo le classiche primitive del modello TCP/IP:
- Sockets (Connessioni TCP)
- Datagrams (Connessioni UDP)

L'astrazione `Channel` per unificare le operazioni di I/O su canali differenti (file, rete, hardware).
Le implementazioni asincrone di `java.nio` per un'esecuzione più efficiente sfruttando a fondo le funzionalità fornita dal SO opsite.
Vedremo `HTTPClient` in tutte le sue modalità d'uso per fare richieste `HTTP` sincrone o meno. Ed eventualmente la classe `URL` per le richieste davvero più semplici.
Su queste basi, l'ecosistema Java ha a disposizione una grande scelta di robuste ed efficaci librerie per la  realizzazione di topologie anche molto complesse.