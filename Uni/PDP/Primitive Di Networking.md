Le astrazioni che abbiamo a disposizione per la comunicazione fra più JVM corrispondono direttamente alle caratteristiche del protocollo TCP/IP ([Programmazione Distribuita](Programmazione%20Distribuita#Classi%20Libreria%20Standard))
- Sockets
- Datagrams

## Sockets
Un `Socket` è una **astrazione** per la comunicazione bidirezionale punto-punto fra due sistemi.
Questo rappresenta un client socket.
```java
package java.net;
public class Socket implements Closeable;
```

```java
public Socket(InetAdress address, int port, InetAddress localAddr, int localPort) throws IOException // costruttore più completo
```

- `localAddr`, `localPort` servono per dire la provenienza del socket.
  Posso specificare al sistema operativo che il collegamento arriva usando uno specifico dei tanti indirizzi che il nodo può possedere. Questo pk il mio nodo può essere collegato su più reti e quindi avere molteplici indirizzi locali quindi specificare il dove può essere essenziale per controllare meglio la connessione.

Un _client Socket_ è un socket per iniziare il collegamento verso un'altra macchina.
Un _server Socket_ è un socket per attendere che un'altra macchina ci chiami.
Quindi il nodo che inizia il collegamneto è il client, dall'altra parte devo dire al SO che sono in attesa di connessioni, di costruire un socket su una certa porta, questo lo faccio creando un server socket: oggetto fatto apposta per attendere che un altro nodo ci contatti.

```java
public ServerSocket implements Closeable{
	public ServerSocket(int port, int backlock, InetAddress bindAddr) throws IOException
}
```

- `backlog` con il backlog posso indicare al SO quante risorse dedicare alla coda delle connessioni in attesa.

Un`Socket` rappresenta un collegamento attivo, lato client, lo dicenta appena il collegamento (l'handshacke TCP/IP) è completato.
Lato server viene ritornato quando un collegamento viene ricevuto e completato.

```java
public Socket accept() throws IOException // metodo ServerSocket
```

Questo metodo blocca il socket finché non ricevo un collegamento.
>[!warning] Attenzione
>Questo significa che la mia linea di esecuzione è ora in mano ad un evento esterno, quindi il mio programma non è più definito solamente dalle istruzioni che scrivo io ma è definito anche da qualcosa che deve succedere da fuori.

Un `Socket` ci fornisce un `InputStream`[^1] e un `OutputStream`[^1] per ricevere e trasmettere dati nel collegamento per ricevere e trasmettere dati nel collegamento.
Quindi da un `Socket` collegato possiamo ottenere:
```java
public InputStream getInputStream() throws IOException;
public OutputStream getOutputStream() throws IOException;
```

>[!important] Importante
>`InputStream` e `OutputStream` sono stream di `byte` e non di testo.

Questi stream sono sottoposti a diverse regole:
- Sono [thread safe](Sincronizzazione#Dati%20Thread-Safe), ma un solo [thread](Thread) può scrivere o leggere per volta, pena eccezioni.
- I buffer sono limitati, questo perché modellano caratteristiche fisiche e quindi possono riempirsi ed in alcuni casi i dati in eccesso possono essere **silenziosamente** (senza eccezioni) scartati.
- lettura e scrittura possono bloccare il thread, per esempio bloccami se il buffer è vuoto (operazione di lettura).
- Alcune connessioni possono avere caratteristiche particolari (per es. urgent data)

Una volta terminato l'uso, il `Socket` va chiuso esplicitamente tramite il metodo `void close()`.
Avendo come astrazione della comunicazione gli Stream, la comunicazione via socket ha il difetto di richedere la definizione esplicita di un confine fra richieste e risposte.
Dallo Stream non possiamo sapere se la richiesta è terminata, e non possiamo segnalare di averlo inviato tutta la risposta. Quindi è compito del programmatore capire queste cose.

### Esempio
```java title:Server
try (
	ServerSocket serverSocket = new ServerSocket(portNumber);
	Socket socket = serverSocket.accept();
	PrintWriter out = new PrintWriter(socket.getOutputStream(), true); // permette di mandare righe di testo ad un OutputStream
	BufferedReader buf_in = new BufferedReader(new InputStreamReader(socket.getInputStream()));) // permette di leggere righe di testo da un InputStream, una lettura che attende finché non arriva il carattere di fine riga. 
{
	String inputLine;
	System.out.println("Receiveed data.");
	while((inputLine = buf_in.readLine()) != null) {
		System.out.println("Received: " + inputLine);
		out.println("Hello " + inputLine);
	}
	System.out.println("Server closing...");
}
```

Il protocollo fra client e server è "linea di testo terminata da \n".
Appena il server riceve il carattere `\n` (e non prima), BufferedReader::readline ritorna ed il server risponde.

```java title:Client
try (
	Socket socket = new Socket("127.0.0.1", portNumber);
	PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
	BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));)
{
	System.out.println("Connected, sending " + args[0]);
	out.println(args[0]);
	System.out.println("Got back: " + in.readline());
}
```

## Datagrams
Un `Datagram` è un'astrazione per l'invio di un pacchetto di informazioni singolo verso una destinazione o verso più destinazioni.
Il concetto di connessione è diverso rispetto al socket, e non c'è garanzia di ricezione o ordinamento in arrivo.
Quindi se noi mandiamo 3 messaggi, nulla ci garantisce che arrivino nell'ordine in cui li abbiamo mandati.
```java
package java.net;

public final class DatagramPacket {
	public DatagramPacket(byte[] buf, int length);
	public DatagramPacket(byte[] buf, int length, InetAddress address, int port); // indirizzo di destinazione
}
```

Un `DatagramPacket` è pur sempre un pacchetto UDP, quindi soggiace alle stesse limitazioni:
In particolare, la dimensione massima è di 64kb.


| Protocollo                              | MTU (bytes) (Maximum Trasmission Unit) ovvero dimensione massima per evitare la frammentazione del pacchetto, ovvero separazione pacchetto in pacchetti più piccoli. |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| IPv4 (link) collegamento punto-punto IP | 68                                                                                                                                                                   |
| IPv4 (host)                             | 576                                                                                                                                                                  |
| IPv4 (Ethernet)                         | 1500                                                                                                                                                                 |
| IPv6                                    | 1280                                                                                                                                                                 |
| 802.11                                  | 2304                                                                                                                                                                 |
Per inviare o ricevere abbiamo una sola classe, senza distinzione di operatività.
```java
public DatagramSocket(int port) throws SocketException
```
Questo per ascoltare una porta e attendere il pacchetto datagram.
Possiamo anche "connettere" una `DatagramSocket` già create ad un indirizzo, ma il significato è diverso.
```java
public void connect(InetAddress address, int port)
```

Questo dice "accetta pacchetti solo se vengono da questo indirizzo e da questa porta".

```java
public void send(DatagramPacket p) throws IOException
```
Permette di inviare un pacchetto dove ci ho già scritto l'indirizzo di destinazione

```java
public void receive(DatagramSocket p) throws IOException
```

Questa funzione mi mette in attesa e mette il pacchetto ricevuto dentro `p`.

```java
public void close() // chiudo il DatagramSocket
```

Con i `Datagam` la logica del protocollo è differente.
>[!success] Vantaggi
>- la dimensione del messaggio nota (e quindi l'informazione di ricezione completa)
>- la possibilità di inviare messaggi a più indirizzi contemporaneamente (multicast)

>[!failure] Svantaggi
>- Affidabilità: non c'è né garanzia né segnale di consegna
>- Reciprocità: c'è una sola direzione; la risposta richiede mettersi in ascolto
>- Dimensione: messaggi lunghi subiscono una forte penalità di affidabilità

```java title:EchoServer
public void run() {
	byte[] buf = new byte[256];
	DatagramPacket packet = new DatagramPacket(buf, buf.length);
	System.out.println("Server setup. Receiving...");
	try {
		socket.receive(packet);
		String received = new String(packet.getData(), 0, packet.getLength());
		System.out.println("Received: " + received);
	} catch (IOException e) { e.printStackTrace(); }
	finally { socket.close(); }
}
```

## HTTP Client
`Socket` e `Datagrams` sono primitive di livello molto basso rispetto a quello con cui si lavorano di solito.
Per l'appunto di solito si lavora con il protocollo HTTP. 
Fin dalle prime versioni infatti Java ha a disposizione delle classi per rappresentare in modo molto fedele le URL e le URI.
Tuttavia il loro uso oggi è scomodo. E tutti i costruttori di `java.net.URL` sono deprecati.
La costruzione dell'HTTP Client e delle richieste seguono il `Builder Pattern`: vengono concatenate chiamate a metodi che ritornano un oggetto la cui configurazione viene via via completata, per poi concludersi con l'ultimo metodo che ottiene l'oggetto completamente configurato.
==Una volta costruita la richiesta, la si può eseguire per ottenere una risposta.==
Un `builder` non ancora completato può essere usato più volte, copiato per essere ulteriormente modificato, e così via.

```java
HttpClient client = HttpClient.newHttpClient();
HttpRequest request = HttpRequest.newBuilder().uri(URI.create("https://httpbin.org/get")).build();
HTTPResponse<String> response = client.send(request, BodyHandlers.ofString());

System.out.println(response.statusCode());
System.out.println(response.body());
```

Il parametro `BodyHandler` permette di gestire come trattare il corpo della risposta.
Infatti la `HttpResponse<T>` è parametrizzata in `T` ed è proprio il `BodyHandler` che decide che tipo di `T`. Infatti usando `BodyHandlers.ofString()` abbiamo `T = String` dunque `HttpResponse<String>`.
La classe `BodyHandlers` contiene alcune strategie comuni.
Una richiesta può anche essere inviata in modo asincrono, in questo caso si ottiene un `CompletableFuture`, versione di `Future` che accetta istruzioni da eseguire al completamento del calcolo.
```java
client
	.sendAsync(request, BodyHandlers.ofString())
	.thenApply(HttpResponse::body)
	.thenAccept(System.out::println)
```

- `thenApply`: quando è arrivata la risposta, applica il metodo passato, nell'esempio equivale a `response.body()`.  **`HttpResponse::body`** è un **method reference** che equivale alla lambda: `httpResponse -> httpResponse.body()`
**`thenApply()`** prende il `HttpResponse<String>` completo e estrae solo il body (la stringa)
- `thenAccept()` consuma il risultato tramite il consumer.

tutto questo viene fatto quando la risposta è arrivata.
Il metodo HTTP da usare viene definito nella costruzione della richiesta:
```java
HttpRequest delete = HttpRequest.newBuilder()
	.DELETE() // può essere GET, POST, ....
	.uri(URI.create("https://httpbin.org/delete"))
	.build();
```

Per fornire il contenuto di una richiesta POST o PUT, si usa un parametro di tipo `BodyPublisher`, ovvero una classe che gestisce la "pubblicazione" del corpo della richiesta.
```java
HttpRequest post = HttpRequest.newBuilder()
	.uri(URI.create("https://httpbin.org/post"))
	.timeout(Duration.ofMinutes(2))
	.header("Content-Type", "application/x-www-form-urlencoded")
	.POST(BodyPublishers.ofString("foo=bar&baz=1"))
	.build();
```

La classe `HttpClient` fornisce inoltre la possibilità di controllare in modo molto fine la gestione dell'`Executor` che esegue le richieste, il protocollo usato, e molto altri dettagli della comunicazione.

## Tic-Tac-Toe 
>[!obj] Obbiettivo
>Realizzare un server che gestisce il gioco di TicTacToe fra due giocatori.
>Realizzare quindi un client che gioca scegliendo una casella libera a caso.

Il **Server** deve:
1. Rispondere alla prima connessione salutando il primo giocatore.
2. Rispondere alla seconda connessione salutando il secondo giocatore.
3. Richiedere la mossa da ciascun giocatore al suo turno, mostrandogli lo stato del gioco
4. Individuare la conclusione della partita e chiudere le connessioni.

Il **Client** deve:
1. Collegarsi al server
2. Interpretare la risposta con lo stato della partita
3. effettuare una mossa a caso fra quelle legali



[^1]: Per maggiore info sugli stream: [[Stream Paralleli]]