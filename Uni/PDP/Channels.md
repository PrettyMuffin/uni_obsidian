Per completare la panoramica sui metodi di gestione dei [socket](Primitive%20Di%20Networking#Sockets) ci manca un'astrazione che ci permetta di ascoltare e reagire a più richieste di connessione.
```java
public interface Channel extends Closeable
```

==Un `Channel` rappresenta un canale di I/O, che può essere aperto o chiuso, su cui inviamo dei messaggi o su cui riceviamo dei messaggi.==

```java
// A channel to a network socket
public interface NetworkChannel extends Closeable
```

Un `NetworkChannel` rappresenta una comunicazione su di una rete. Può:
- Essere legato (con l'operazione `bind`) ad un indirizzo.
- dichiarare le opzioni che supporta.

```java
public abstract class AsynchronousServerSocketChannel implements AsynchronousChannel, NetworkChannel
```

Questo è un `NetworkChannel` asincrono basato su di un server socket. Ci permette, in modo asincrono, di accettare connessioni e gestirle.
Questo comporta che il canale si prende in carico per conto nosto tutta la gestione e la contabilità dei socket, buffer... si prende in carico anche la gestione della concorrenza, per cui noi non controlliamo più l'ordine di esecuzione ma ci facciamo richiamare quando succede qualcosa. Questo comporta che però dobbiamo riscrivere il codice all'interno di questo modello.
```java
// Un handler per consumare il risultato di una
// operazione I/O asincrona
// V Il tipo del risultato dell'operazione I/O
// A Il tipo dell'oggetto attaccato all'operazione I/O
interface CompletionHandler<V, A>
```

Quindi noi dovremmo categorizzare tutte le operazioni di I/O che facciamo e scrivere un `CompletionHandler` per ciascuna di esse.
Questa interfaccia ci permette di accettare un messaggio e dire al sistema qual è la prossima operazione, in questo modo possiamo costruire un grafo della sequenza della nostra interazione e il sistema va a seguire la nostra comunicazione tramite questo grafo.
Lo fa tante volte quante sono le connessioni ricevute.
Il metodo principale di questa interfaccia è:
```java
// Invocata quando un'operazione è completata
// result: il risultato dell'operazione I/O.
// attachment: il tipo dell'oggetto attaccato all'operazione I/O quando era stata inizializzata.
void completed(V result, A attachment)
```

```java
// Invotata quando un'operazione fallisce
// exc: L'eccezione da indicare perché l'I/O è fallita.
// attachment: Il tipo dell'oggetto attaccato all'operazione I/O quando era stata inizializzata.
void failded(Throwable exc, A attachment)
```

Implementando un `CompletionHandler` possiamo esprimere il comportamento del server alla prossima interazione, in maniera asincrona.

- `attachment` ci permette di far circolare le informazione di contesto riguardo allo stato della conversazione. Ovvero ogni volta che inizia un nuovo processo di comunicazione viene creato un nuovo attachment e viene passato a tutti i `CompletionHandler` che fanno parte della stessa comunicazione.
  Per cui se la nostra comunicazione parte accettando un socket, tutti i `CompletionHandler` che hanno a che fare con quella iterazione, riceveranno lo stesso attachment. In questo modo risolviamo la questione del passaggio del contesto.

La gestione delle operazioni I/O richiede quindi di specificare sempre l'attachment da far circolare ed il `CompletionHandler` che gestisce il comportamento.
vale a dire:
```java
// accetta una connessione 
public abstract < A > void accept(A attachment, CompletionHandler<AsynchronousSocketChannel, ? super A> handler)
```

```java
// Legge una sequenza di byte dal Channel nel buffer dato
public final < A > void read(ByteBuffer dst, A attachment, CompletionHandler< Integer, ? super A> handler)
```

```java
// Scrive una sequenza di byte nel channel dal buffer dato
public final < A > void write(ByteBuffer src, A attachment, CompletionHandler< Integer, ? super A> handler)
```

Questo richiede di riorganizzare il nostro codice, ma ci permette di gestire molte più connessioni.

Un'alternativa all'uso di un `CompeltionHandler` è data dalla versione dei metodi che ritorna un [`Future`](Callables#`Future<V>`).
per cui ogni operazione ritorna un `Future`:

```java
public abstract Future< AsynchronousSocketChannel > accept()
public abstract Future< Integer > read(ByteBuffer dst)
public abstract Future< Integer > write(ByteBuffer dst)
```

Qui invece che spargere il codice nei `CompletionHandler` lo teniamo in un blocco che però interagisce con i `Future`.

La principale differenza tra `CompletionHandler` e `Future` è che nel caso del `Future` se il blocco di codice è unico per tutta la conversazione, il thread che la gestisce è unico e rimane allocato per l'intera durata della conversazione.
Mentre con il `CompletionHandler` non abbiamo un "nostro" thread in attesa, abbiamo delegato tutto quanto al canale.

## Esempio

```java title:Server
ExecutorService pool = Executors.newFixedThreadPool(4);
AsynchronousChannelGroup group = AsynchronousChannelGroup.withThreadPool(pool);
AsynchronousServerSocketChannel serverSocket = AsynchronousServerSocketChannel.open().bind(new InetAddress("127.0.0.1", GAME_PORT) 16); // buffer di 16 connessioni

pool.submit(()-> {
	serverSocket.accept(
		new GameAttachment(1, new Game(), serverSocket, group),
		new AcceptPlayerO()
	);
});
```

```java
public class AcceptPlayerO implements CompletionHandler<AsynchronousSocketChannel, GameAttachment> {
	@Override
	public void completed(AsynchronousSocketChannel result, GameAttachment attachment) {
		System.out.println(Thread.currentThread().getName() + " : game " + attachment.id + " connected player O");
		attachment.server.accept(attachment.playerO(result), new WriteFirstStatus());
	}
}
```

```java
public class WriteFirstStatus implements CompletionHander<AsynchronousSocketChannel, GameAttachment> {
	@Override
	public void completed(AsynchronousSocketChannel result, GameAttachment attachment) {
		attachment = attachment.playerX(result);
		GameResult status = attachment.game.status();
		AsynchronousSocketChannel socket = attachment.players[status.next];
		byte[] bytes = (status.toString() + "\n").getBytes();
		socket.write(wrap(bytes), attachment, new ReadPlayer());
		if(attachment.id <= 5) {
			attachment.server.accept(new GameAttachment(attachment.id + 1, new Game(), attachment.server, new AcceptPlayerO()));
		} else {
			attachment.group.shutdown();
		}
	}
}
```

```java
public class ReadPlayer implements CompletionHandler<Integer, GameAttachment> {
	@Override
	public void completed(Integer result, GameAttachment attachment) {
		GameResult status = attachment.game.status();
		AsynchronousSocketChannel socket = attachment.player[status.next];
		attachment.readBuf.clear();
		socket.read(attachment.readBuf, attachment, new WriteStatus())
	}
}
```

![[Pasted image 20250828172243.png]]