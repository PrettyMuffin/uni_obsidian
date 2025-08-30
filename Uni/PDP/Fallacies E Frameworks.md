## 8 Fallacità della [[Programmazione Distribuita]]
1. La rete è affidabile
2. Latenza è zero
3. Banda infinita
4. La rete è sicura
5. Topologia non cambia
6. C'è solo un amministratore
7. Trasporto cosa zero
8. La rete è omogenea

### 1. La rete è affidabile
Non è necessario un guasto hardware: ci sono moltissimi modi in cui una rete può essere inaffidabile.
Significa che il software deve lavorare molto di più sulla gestione e sulla correzione degli errori.
Un software distribuito deve tener conto della possibilità che le connessioni si interrompano o vengano negate, o i messaggi vadano persi.
Va bilanciato il rischio/costo di un tale evento con il costo della ridondanza e dell'affidabilità.

### 2. Latenza zero
>[!def] Latenza
>Tempo trascorso tra invio messaggio e ricezione risposta.

C'è un limite fisico alla velocità di un messaggio: la velocità della luce.
È impossibile, per es. raggiungere una latenza inferiore ai 30ms fra le due coste dell'Atlantico.

Se il messaggio trasporta delle informazioni che dipendono dal tempo per una risoluzione maggiore di qualche secondo, la latenza dev'essere calcolata come parte della comunicazione.

### 3. Banda infinita
La banda disponibile è sempre meno un problema, i dati da trasferire tendono ad aumentare altrettanto velocemente.
Inoltre, in molte situazioni i protocolli di rete danno priorità all'affidabilità della comunicazione invece che all'economia di trasmissione.
Se il messaggio da trasferire è particolarmente grande, va gestito come un insieme di parti da trasferire singolarmente.
Le performance di un protocollo devono considerare anche la quantità di dati "amministrativi" che trasporta.

### 4. La rete è sicura
La diffusione delle reti ha portato, ovviamente, a maggiori opportunità per gli attaccanti e a maggiori possibilità di errore per i costruttori di dispositivi.
Se il contenuto del messaggio ha una qualsiasi importanza o trasporta qualche informazione minimamente confidenziale, il protocollo deve essere reso sicuro:
- autenticato
- autorizzato
- integro
- confidenziale

### 5. Topologia non cambia
L'ubiquità dell'accesso ha come conseguenza che un dispositivo è spesso in movimento da un punto di accesso ad un altro.
Il risultato è che la topologia del grafo che costituisce la rete è in continua evoluzione.
È necessario che la comunicazione sia in grado di usare un metodo di indirizzamento dinamico e che non dipenda dalle caratteristiche fisiche della rete.

### 6. C'è un solo amministratore
La diffusione e frammentazione delle reti significa anche frammentazione delle responsabilità nella gestione delle reti stesse.
Sempre più di rado si può imporre requisiti al percorso delle connessioni (porte aperte, feature del trasporto) perché sempre più organizzazioni sono coinvolte nella loro gestione.
Bisogna puntare sempre alla massima semplicità nella struttura e nei requisiti del protocollo di comunicazione.
Requisiti particolari potrebbero non poter essere soddisfatti da uno qualsiasi dei nodi della rete, impedendo il transito dei dati.

### 7. Trasporto costa zero
Anche il costo del trasporto sta continuamente calando, ma non è mai zero. Sia in termini monetari, sia nache in termini energetici: la maggior parte dei dispositivi è a batteria.
Nel caso di dispositivi IoT, il bilancio energetico diventa il costo preponderante.
Come per le altre voci, efficienza e sintesi devono essere criteri importanti nella costruzione di un protocollo di comunicazione.

### 8. La rete è omogenea
Le reti non sono più costituite da una sola tecnologia, da un solo mezzo di comunicazione; un messaggio attraversa una varietà di mezzi trasmissivi, metodi di comunicazione e apparati differenti.
Il protocollo di comunicazione deve evitare di richiedere caratteristiche di trasporto particolari, ma affidarsi il più possibile alle funzioni più standard.

## Web Framework
Framework che servono a costruire una parte di un programma distribuito dove ci sono molte parti che fanno richieste e di solito un insieme di nodi che rispondono a tali richieste.
Con una comunicazione che si basa su HTTP.
Normalmente non si lavora ad un livello così basso come può essere il [Socket](Primitive%20Di%20Networking#Sockets), ma si usano delle astrazioni:
- per semplificare la struttura del codice.
- per sfruttare implementazioni già testate e corrette.

### Esempi con framework Vert.X
Vert.X è la proposta di Eclipse foundation per la realizzazione di applicazioni web e microservizi scalabili e moderni.
Le sue principali caratteristiche sono:
- modularità e compatezza delle dipendenza
- event-driven, fortemente asincrono
- poliglotta
- altamente scalabile
- approccio non-standard
- "unopinionated": non sceglie i componenti per fare un certo lavoro, ci sono moduli che utilizzano componenti differenti per fare lo stesso lavoro.
- semplice e pragmatico
- open source
- supportato dalla fondazione Eclipse

Usando i moduli vertx-core e vertx-web possiamo reimplementare il server TicTacToe come applicazione web e valutare le modifiche al protocollo e al codice che ci ritroviamo a fare.
#### Esempio - API
Dobbiamo definire, documentare quali sono le richieste che il server accetta.
Per prima cosa dobbiamo pianificare l'API che il servizio esporrà.
Per rendere l'esempio più concreto, esporremo sia una API web usabile dal browser, sia una JSON usabile da un client automatico.
##### Home Page
`GET /` 
HTML: pagina HTML di benvenuto

##### Ingresso al gioco
`POST /game`
HTML: redirect al form di gioco
JSON: url dove ottenere lo stato del gioco

##### Mossa in un gioco
`POST /game/{id}`
`move = x`

HTML: form con lo stato di gioco
JSON: stato del gioco

>[!question] Come chiamare l'API?
>```bash
>$ curl -X POST -H "Accept: application/json" \ http://localhost:8080/game
>{"game":"/game/1134141953"}
>$ curl -H "Accept: application/json" \ http://localhost:8080/game/1134141953
>```

##### Codice
L'astrazione che il modulo vertx-web ci mette a disposizione è un `router` che ci consente di associare ad un `URL` una labda.
Questa può modificare la risposta importando esito e contenuto.
Il modo in cui il nostro codice interagisce con il framework è definito da questa astrazione.

```java title:NetGame
class NetGame {
	String[] playerId = new String[2];
	private optional<Game> game = Optional.empty();
}
```

```java
class GameServer {
	// giochi che si stanno giocando
	ConcurrentMap< String, NetGame > games = new ConcurrentHashMap<>();
	
	// coda di giochi in cui c'è un solo giocatore ovvero giochi in partenza
	BlockingQueue< NetGame > openings = new LinkedBlockingQueue<>(16);
	
	public Optional< GameIndex> status(String playerId) {
		var res = Optional.empty();
		if(games.containsKey(playerId) && games.get(playerId).started()) {
			int idx = games.get(playerId) playerId[0].equals(playerId) ? 0 : 1;
			res = Optional.of(new GameIndex(indx, games.get(playerId).status()));
		}
		return res;
	}
}
```

```java
public static void main() {
	GameServer gameServer = new GameServer();
	Vertx vertx = Vertx.vertx();
	HttpServerOptions options = new HttpServerOptions().setLogActivity(true);
	HttpServer server = vertx.createHttpServer(options);
	Router router = Router.router(vertx);
	
	// setup body handler per tutte le route
	router.route().handler(BodyHandler.create());
}
```

noi scriviamo le istruzioni per costruire il nostro server, per associare a una richiesta con certe caratteriche il codice che crea la risposta.
Quindi c'è una parte precisa del nostro codice dove avviene questa costruzione. Questo è un approccio che non tutti i framework hanno. non è un approccio comune.
Dato che questa costruzione è programmatica possiamo sceglere di farla in un sercondo momento.
Una caratteriscica è dunque la grande scalabilità e il fatto che permette di costruire un sistema le cui caratteristiche e il cui funzionamento può cambiare molto durante l'esecuzione.
Il modo in cui il server risponde a richieste è attraverso una serie di istruzioni:
```java
router.get("/").produces("text/html").handler(ctx -> { // context 
	// prendo la risposta dal contesto
	// la completo con la stringa "JOIN_FORM" che è
	// la stringa del form con cui il browser entra nel gioco
	ctx.response().end(JOIN_FORM);
});
```

Il fatto che abbiamo a disposizione il contesto, e quindi lo stato del server possiamo intervenire durante l'esecuzione molto in profondità e dinamicamente sulle caratteristiche del server su come il server funziona.

```java
router.post("/game").produces("text/html").handler(ctx -> {
	GameLocation loc = newGameLocation(gameServer.create());
	// questa risposta non ha un contenuto, ma solo uno stato e un header
	ctx.response().setStatusCode(302).putHeader("Location", loc.game).end();
})

router.post("/game").produces("application/json").handler(ctx -> {
	GameLocation loc = newGameLocation(gameServer.create());
	ctx.response().putHeader("content-type", "application/json").end(location.toJson(mapper));
})
```

>[!important] TODO
==uso di link e strutture http per trasportare lo stato dell'applicazione: chiedere meglio a ChatGPT.==

## Perché usare un Framework?
Un framework fornisce un ambiente all'interno del quale un insieme di casi d'uso fondamentali è reso facile, efficiente e sicuro da implementare.
Un framework per applicazioni web, per esempio, rende facile:
- specificare le rotte a cui rispondere.
- costruire le risposte.

Lo stesso framework, cercherà di rendere trasparente, ed eventualmente configurabile:
- la gestione dei dettagli del protocollo
- la sicurezza nel trattamento della comunicazione
- la suddivisione delle risorse fra le varie parti del sistema

Gli autori del framework scelgono le loro priorità fra semplicità d'uso, sicurezza ed efficienza.
Come utenti, noi otteniamo automaticamente ogni incremento su ciascuna di queste dimensioni.

## Perché NON usare un Framwork?
Un caso d'uso che non è fra quelli prescelti come importanti dagli autori del framework, può essere molto, molto complesso da implementare.
Un framework per applicazioni web, per esempio, può rendere difficile:
- rispondere a richieste esotiche.
- controllare finemente l'erogazione della risposta.

Lo stesso framework web, potrebbe rendere oscuro, oppure inaspettatamente:
- fallire l'esecuzione di una richiesta a causa di un baco nella sua imlementazione
- aprire una falladi sicurezza a causa di un default errato
- permettere il sovraccarico del sistema a causa di una mancata limitazione delle risorse.

Gli autori del framework scelgono le loro priorità fra semplicità d'uso, sicurezza ed efficienza.
Come utenti, dipendiamo completamente da loro per l'ordine con cui queste caratteristiche sono implementate, e dobbiamo sopportare i costi di aggiornamento se l'interfaccia fra il framework ed il nostro codice cambia.

