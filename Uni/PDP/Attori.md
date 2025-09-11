Il paragirma [reattivo](Reattività) ci ha portato ad esaminare un modello di esecuzione particolare, con lo scopo di ottenere benefici specifici per un insieme di problemi con esigenze specifiche, anche se sempre più comuni.
L'implementazione esaminata, RxJava, non è tuttavia completamente conforme al _Reactive Manifesto_ perché non è basata sullo scambio di messaggi.
Gli autori del manifesto avevano in mente un modello molto precedente, molto peculiare, nato per requisiti ancora più specifici, ma che si è rivelato estremamente utile negli ambiti in cui il paradigma reattivo si muoveva.
Andiamo quindi ad esaminare un altro paradigma con la caratteristica però di essere in grado di fornire un'ottima implementazione del modello _Reactive_ e di soddisfare i requisiti del _Reactive Manifesto_.
Oggi il modello degli attori significa concorrenza e distribuzione ==su larga scala, e affidabilità di lunga durata==.
## Caratteristiche
>[!def] Attore
>Un Attore è una ==unità indipendente di elaborazione==, dotata di uno stato privato, inaccessibile dall'esterno.
>L'attore comunica unicamente con messaggi diretti ad altri attori di cui conosce il nome.

Un attore, in un determinato momento, ha un Comportamento che definisce la sua reazione ad un determinato messaggio.
Nel reagire ad un messaggio, un attore può:
- mutare il proprio stato interno
- creare nuovi attori
- inviare messaggi ad attori noti
- cambiare il suo comportamento

**All'interno dell'attore non c'è concorrenza:** l'esecuzione è strettamente sequenziale. 
Ovviamente è preferibile che la risposta sia il più veloce possibile.

Al di fuori dell'attore tutto è concorrente e distribuito:
- Un altro attore può trovarsi dovunque
- ogni attore è concorrente a tutti gli altri
- ogni messaggio è concorrente a tutti gli altri

Il fallimento di un attore è una eventualità assolutamente normale, che non ha alcuna conseguenza sulla stabilità del programma in sè.

Un attore può supervisionare gli attori che ha creato, ed essere notificato del loro fallimento.
La notifica è un messaggio, per il quale il supervisore deve predisporre un comportamento.
Le conseguenze di questo approccio sul modello di esecuzione sono profonde; il risultato è un sistema dove:
- L'avvio di nuovi processi/attori è estremamente economico
- L'affidabilità è molto elevata
- Le performance e l'efficienza sono molto alte
- La scalabilità è lineare o quasi, e molto stabile
- La distribuzione e la concorrenza sono caratteristiche primarie.

>[!important]
>L'elaborazione di grandi moli di dati asincroni, spesso modellati come eventi, è il tipo di problema in cui questo paradigma si esprime al meglio, abilitando risultati molto difficili da raggiungere in modo differente.

## Problematiche
Ovviamente, le caratteristiche del modello ad Attori hanno un costo. In questo caso, è un costo di natura concettuale.
Modellare un algoritmo come l'interazione di un insieme di attori concorrenti non è sempre naturale. Quando lo è, il risultato è estremamente efficace.
Molte delle euristiche e dei modi di pensare tipici della programmazione tradizionale sono in questo modello totalmente inutili, se non addirittura dannosi.
Il modello mentale in cui il problema va ricondotto è molto differente.
L'interazione non può fare assolutamente nessuna ipotesi nè sull'ordine della ricezione dei messaggi, né sulla loro affidabilità.
Questo costringe a dover considerare attentamente il modello del risultato e le varie possibilità di fallimento.
## Implementazione
Indirizzo di un Attore:
```java
public interface Address<T> {
	Address<T> tell(T msg);
}
```
Questa è l'interfaccia che ci permette di mandargli un messaggio, la parametrizziamo sul tipo di messaggio, così siamo certi di mandare ad un attore solo i messaggi che può ricevere.

---
Comportamento di un Attore:
```java
public interface Behavior<T> extends Function<T, Effect<T>> {}
```
È una funzione dal messaggio all'effetto del messaggio. Vale a dire al cambiamento di comportamento che quel messaggio ha sull'attore.
L'attore, per consumare un messaggio, lo riceve e mi deve ritornare l'effetto di quel messaggio.

Effetto della ricezione del messaggio:
```java
public interface Effect<T> extends Functions<Behavior<T>, Behavior<T>> //ritorna un Behavior<T>, dato che ultimo generico
{}
```

Vale a dire, il prossimo comportamento.
Un effect è una funzione da `Behavior<T>` a `Behavior<T>` che cambia da comportamento precedente a comportamento successivo.
Effetto:
```java
// mantenimento comportamento attuale
static <T> Effect<T> stay() {
	return old -> old;
}

// sostituzione comportamento attuale
static <T> Effect<T> become(Behavior<T> next) {
	return current -> next;
}

// Disattivazione nodo
static <T> Effect<T> die() {
	return become(msg -> {
		System.out.println("Dropping msg [" + msg + "]: the actor is inactive.");
		return stay(); 
	});
}
```

---
Costruzione dell'attore per la gestione della concorrenza, dato che appunto per definizione del paradigma, tutto quanto è concorrente, tranne gli attori, che sono [thread-safe](Sincronizzazione#Dati%20Thread-Safe).
```java
abstract class AtomicRunnableAddress<T> implements Address<T>, Runnable {
	AtomicInteger on = new AtomicInteger(0);
	
	
}

```

---
Ora cominciamo a scrivere il sistema che produce gli attori.
```java
public record System(Executor executor) {
	public <T> Address<T> actorOf(Function<Address<T>, Behavior<T>> initial) {
		var addr = new AtomicRunnableAddress<T>() {
			final ConcurrentLinkedQueue<T> mbox = new ConcurrentLinkedQueue<>();
			// otteniamo il Behavior passato eseguendo initial e prendendo Behavior<T> come ritorno inoltre this funziona pk AtomicRunnableAddress implementa Address
			Behavior<T> behavior = initial.apply(this); 
			
			public Address<T> tell(T msg) {
				mbox.offer(msg);
				async();
				return this;
			}
			
			void async() {
				if(!mbox.isEmpty() && on.compareAndSet(0, 1)) {
					try {
						executor.execute(this);
					} catch(Throwable t) {
						on.set(0);
						throw t;
					}
				}
			}
			
			public void run() {
				try {
					if(on.get() == 1) // significa che sta essendo eseguito dall'executor
						behavior = bahavior.apply(max.poll()).apply(behavior);
				} finally {
					// finito di processare, rimetto a 0 la "flag" così altri messaggi possono essere consumati
					on.set(0);
					async();
				}
			}
		}
		return addr;
	}
}
```

Esempio: attore che accetta un messaggio e poi si spegne
```java
System actorSystem = new System(Executors.newFixedThreadPool(4));
Address<String> actor = actorSystem.actorOf(
		addr -> msg -> {
			System.out.println("got msg: " + msg);
			return Effect.die();
		});
actor.tell("foo").tell("bar");
```
Questo attore accetta il primo messaggio `foo` e "rifiuta" ovvero non cambia behavior al messaggio `bar`.

## Akka
Sulla JVM, la più diffusa implementazione del modello ad attori è il framework Akka.
Prende a esplicito riferimento il modello di Erlang per implementare sulla JVM un sistema ad attori in grado di supportare applicazioni reattive e scalabili.
Le principali caratteristiche del framework sono:
- Compatibile con l'ecosistema della JVM, e quindi in grado di usare tutte le librerie e tecnologie disponibili per Java.
- Scritto in Scala, ma usabile attraverso una API Java.
- Nel modello di supervisione, questa è obbligatoria da parte del "genitore".

Gli attori comunicano fra loro in modo pilotato dai tipi: questo permette di codificare l'interazione nelle caratteristiche delle classi usate per guidare l'interazione.
È il compilatore stesso ad impedirci di inviare messaggi che non possono essere ricevuti.
Akka supporta una implementazione di [Reactive Streams](Reattività#Reactive%20Streams) che può sfruttare la scalabilità del sistema per raggiungere prestazioni considerevoli esponendo il modello di esecuzione degli stream.

### Implementazione Completa (non distribuiti)
![[Pasted image 20250911105709.png]]
Questi sono gli attori.
È necessario definire un attore che funge da supervisore fra i due.
Definiamo per prima cosa i messaggi: saluto e risposta.
```java title:Greet
public static final class Greet {
	public final String whom; // testo messaggio
	public final ActorRef<Greeted> replyTo;
	public Greet(String whom, ActorRef<Greeted> replyTo) {
		this.whom = whom;
		this.replyTo = replyTo;
	}
}
```

```java title:Greeted
public static final class Greeted {
	public final String whom; // testo messaggio
	public final ActorRef<Greet> from;
	public Greeted(String whom, ActorRef<Greet> from) {
		this.whom = whom;
		this.from = from;c
	}
}
```
Dobbiamo definire come si comporta l'attore che risponde al saluto al momento della creazione:
```java title:HelloWorld
public class HelloWorld extends AbstractBehavior<HelloWorld.Greet> {
	public static Behavior<Greet> create() {
		return Behaviors.setup(HelloWorld::new);
	}
	
	private HelloWorld(ActorContext<Greet> context) {
		super(context);
	}
	
	@Override
	public Receive<Greet> createReceive() {
		return newReceiveBuilder().onMessage(Greet.class, this::onGreet).build();
	}
	
	private Behavior<Greet> onGreet(Greet command) {
		getContext.getLog().info("Hello {}!", command.whom);
		command.replyTo.tell(new Greeted(command.whom, getContext().getSelf()));
		return this;
	}
}
```

Ora, l'attore che invia il saluto.
Il comportamento alla creazione:
```java title:HelloWorldBot
public class HelloWorldBot extends AbstractBehavior<HelloWorld.Greeted> {
	public static Behavior<HelloWorld.Greeted> create() {
		return Behaviors.setup(c -> new HelloWorldBot(c, max));
	}
	private final int max;
	private int greetingCounter;
	
	private HelloWorldBot(ActorContext<HelloWorld.Greeted> ctx, int max) {
		super(ctx);
		this.max = max;
	}
}
```
Se abbiamo raggiunto il massimo, interrompiamo le risposte terminando l'esecuzione dell'attore.
Altrimenti, rispondiamo a chi ci ha inviato il saluto.
```java
@Override
public Receive<HelloWorld.Greeted> createReceive() {
	return newReceiveBuilder().onMessage(HelloWorld.Greeted.class, this::onGreeted).build();
}
```

```java
private Behavior<HelloWorld.Greeted> onGreeted(HelloWorld.Greeted message) {
	greetingCounter++;
	getContext().getLog().info("Greeting {} for {}", greetingCounter, message.whom);
	if (greetingCounter == max) {
		return Behaviors.stopped();
	} else {
		message.from.tell(new HelloWorld.Greet(message.whom, getContext().getSelf()));
		return this;c
	}
}
```
L'attore che coreografa l'interazione deve rispondere ad un messaggio diverso:
```java title:HelloWorldMain
public class HelloWorldMain extends AbstractBehavior<HelloWorldMain.SayHello> {
	public static class SayHello {
		public final String name;
	 
		public SayHello(String name) {
			this.name = name;
		}
	}
}
```
Il suo stato è il riferimento all'attore creato al momento della costruzione:
```java
private final ActorRef<HelloWorld.Greet> greeter;

public static Behavior<SayHello> create() {
	return Behaviors.setup(HelloWorldMain::new);
}

private HelloWorldMain(ActorContext<SayHello> context) {
	super(context);
	greeter = context.spawn(HelloWorld.create(), "greeter");
}
```
Al ricevimento del messaggio d'avvio, crea l'attore di risposta, e invia il primo saluto per avviare la conversazione.
```java
@Override
public Receive<SayHello> createReceive() {
	return newReceiveBuilder().onMessage(SayHello.class, this::onSayHello).build();
}

private Behavior<SayHello> onSayHello(SayHello command) {
	ActorRef<HelloWorld.Greeted> replyTo = getContext().spawn(HelloWorldBot.create(3), command.name);
	greeter.tell(new HelloWorld.Greet(command.name, replyTo));
	return this;	
}
```
La classe principale costruisce il sistema di attori, e avvia il coreografo.
```java
public static void main(String[] args) {
	final ActorSystem<HelloWorldMain.SayHello> greeterMain = ActorSystem.create(HelloWorldMain.create(), "helloakka");
	greeterMain.tell(new HelloWorldMain.SayHello("Charles"));
	try {
		System.in.read();
	} catch (IOException ignored) {
	} finally {
		greeterMain.terminate();
	}
}
```