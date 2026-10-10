>[!obj] Obiettivo
> Gestire una [[#^22a8e9| sezione critica]] del codice in modo tale da mantenere la consistenza fra [[Thread]].
> 

>[!def] Sezione Critica
> Si dice _sezione critica_ la parte di codice in cui vengono acceduti i dati condivisi.
> Permettere a più thread di trovarsi contemporaneamente nella sezione critica porta ad errori.
> ==La soluzione è impedire a più thread di trovarsi insieme nella sezione critica.==

^22a8e9

## `synchronized`
In Java, `synchronized` è una parola chiave che applicata ad un blocco di istruzioni impedisce che sia percorso contemporaneamente da più di un Thread.
Non cambia il significato delle istruzioni, ma dice al compilatore che devono essere trattate in modo diverso.

```java
class SyncCounter implements SimpleCounter {
	private int state = 0;
	synchronized public void add() {
		int current = state;
		try {
			TimeUnit.MILLISECONDS.sleep(Math.round(Math.random() * 100));
		} catch(InterruptedException e) {
			e.printStackTrace();
		}
		state = current + 1;
	}
}
```

`synchronized` può decorare due tipi di raggruppamenti di istruzioni:
- un blocco di istruzioni semplice `{...}`
- un metodo

>[!important] N.B
>Tutti i blocchi sincronizzati di un oggetto condividono lo stesso `monitor lock`( o `intrinsic lock`) associato all'oggetto stesso.

Quando un Thread rilascia un `monitor` uscendo da un blocco `synchronized`, abbiamo una relazione di _happens-before_ fra l'azione di rilascio del `lock` e ogni successiva acquisizione dello stesso.

>[!def] Happens-Before
>È una garanzia forte fornita dal compilatore riguardo l'ordinamento dell'esecuzione delle istruzioni espresse dal codice.
>A livello sintattico `synchronized` è l'unico modo per poter dire "questo codice viene eseguito **prima** di quello che c'è dopo".


>[!important] N.B
>Tutti i `monitor` sono `reentrant`: un Thread può acquisire lo stesso `monitor lock` più volte senza temere di entrare in un _deadlock_ con se stesso.

Stabilire una relazione di **happens-before** è, ovviamente, costoso: richiede il supporto dell'hardware e limita la capacità del compilatore di ottimizzare il codice riordinandone le istruzioni.

## `wait()`
È un'alternativa a `synchronized`, ed è la gestione esplicita del monitor di un oggetto.
```java
void wait() throws InterruptedException
```

```java
void notify();
```


>[!info] Cosa fa
> Mette il thread corrente in attesa finché un altro thread invoca le funzioni `notify()` o `notifyAll()` per lo stesso oggetto.

Naturalmente per operare sul monitor dell'oggetto il Thread deve "averne a disposizione il monitor", cioè poter asserire la "proprietà" dell'oggetto; ciò avviene:
- Eseguendo un metodo `synchronized` dell'oggetto
- Eseguendo un blocco `synchronized` all'interno dell'oggetto
- Se l'oggetto è una `Class`, eseguendone un metodo `synchronized static`

Con `wait` si può esplicitamente rilasciare il controllo del `monitor` e attendere che qualcun altro lo notifichi che il `monitor` è tornato libero e può essere ripreso.

>[!important] Nota importante su `notify()`
>Il metodo `notify()` sveglia **un** thread che è in attesa, non **il** thread che è in attesa. 
>Questo significa che non abbiamo il controllo su quale thread viene fatto il `notify()`.

```java title:Esempio
class Named {
	public final String name;
	private boolean red = false;
	
	Named(String name) {
		this.name = name;
	}
	
	synchronized void perform() throws InterruptedException {
		if(!red) {
			red = true;
			wait(); // uguale a this.wait()
		} 
		else {
			red = false;
			notify(); // uguale a this.notify()
		}
	}
}

class Waiter implements Runnable {
	private final Named first, second;
	
	Waiter(Named first, Named second) {
		this.first = first;
		this.second = second;
	}
	
	// Quello che ci aspettiamo è:
	// Se questa linea di eseguzione è la prima ad accedere a fist, si
	// metterà in attesa.
	// Quando la seconda linea di esecuzione arriva, libera first, e si 
	// blocca su second.
	// first, essendo appena liberato sbloccherà a sua volta second
	// finendo l'esecuzione.
	@Override
	public void run() {
		var thread = Thread.currentThread().getName();
		println(thread + " waiting on " + first.name);
		String doing = first.name;
		try {
			first.perform();
			println(thread + " signalled on " + first.name);
			println(thread + " waiting on " + second.name);
			doing = second.name;
			second.perform();
		} catch (InterruptedException e) {
			println(thread + " interrupted on " + doing);
		}
		println(thread + " signalled on " + second.name);
	}
}

void main() {
	Named a = new Named("a"), b = new Named("b"), c = new Named("c"),
	d = new Named("d");
	new Thread(new Waiter(a, d), "Waiter 1").start();
	new Thread(new Waiter(b, d), "Waiter 1").start();
	new Thread(new Waiter(c, d), "Waiter 1").start();
	
	try {
		a.perform();
		b.perform();
		c.perform();
		d.perform();
	} catch (InterruptedException e) { e.printStackTrace(); }
}
```

![[Pasted image 20251004160713.png]]

>[!warning] La documentazione avvisa esplicitamente
>Un Thread può essere svegliato da un `wait()` senza nessun `notify()`.
>Viene definito "Surious wakeup".

## Locks
`synchronized` crea un blocco implicito, `wait()` ci costringe a gestire lo stato del blocco.
A volte però abbiamo bisogno di controllare esplicitamente le condizioni di blocco e sblocco della sezione critica.
Questo lo possiamo fare tramite l'interfaccia di tipo `Lock`
```java
public interface Lock;
```

In questo modo noi abbiamo un oggetto che ci rappresenta esplicitamente la condizione di blocco e lo possiamo andare a gestire in modo esplicito, svincolandoci dalla struttura sintattica del codice.

Questo pk `Lock` è un oggetto come gli altri, non dobbiamo usare `synchronized` o altre parole chiave per andare ad utilizzarlo.
Quindi possiamo chiamare `lock()` all'interno di un punto qualsiasi del codice.
Se `Lock` libero -> lo acquisiamo, rimaniamo in attesa altrimenti.

```java
void lock();
void unlock();
```

Java ci da una terza opzione:
```java
boolean tryLock();
```

`tryLock()` è una funzione che acquisisce il lock solo se è libero, ritornando `true`, se invece è occupato ==non andiamo in attesa==, bensì ci ritornerà false.

```java title:Esempio_con_lock
class LockedFriend {
	private final String name;
	private final Lock lock = new ReentrantLock();
	
	public LockedFriend(String name) {
		this.name = name;
	}
	
	public String getName() {
		return this.name;
	}
	
	public boolean impendingBow(LockedFriend bower) {
		boolean myLock = false, yourLock = false;
		try {
			myLock = lock.tryLock();
			yourLock = bower.lock.tryLock();
		} finally {
			if (!(myLock && yourLock)) {
				if(myLock) { lock.unlock(); }
				if(yourLock) { bower.lock.unlock(); }
			}
		}
		return myLock && yourLock;
	}
	
	public void bow(LockedFriend bower) {
		if(impendingBow(bower)) {
			try {
				out.format("%s: %s has " + "bowed to me\n", this.name, bower.getName());
				bower.bowBack(this);
			}
		} else {
			out.format("%s: %s started to bow to me, but saw that I was already bowing to him.\n", this.name, bower.getName());
			
		}
	}
	
	public void bowBack(LockedFriend bower) {
		out.format("%s: %s has bowed back to me!\n", this.name, bower.getName());
	}
}

void main() {
	final LockedFriend alphonse = new LockedFriend("Alphonse");
	final LockedFriend gaston = new LockedFriend("Gaston");
	
	new Thread(new BowLoop(alphonse, gaston)).start();
	new Thread(new BowLoop(alphonse, gaston)).start();
}

```

### Producer / Consumer
>[!def] Producer / Consumer
>È un pattern architetturale che modella un insieme di thread divisi in due gruppi:
>- _Producers_: threads che producono dati da elaborare.
>- _Consumers_: threads che elaborano i dati prodotti.

Con un `Lock` possiamo controllare manualmente la sezione critica: il Consumatore ammette solo un Thread alla volta nella sezione critica.
Il `Lock`, implicitamente, gestisce la coda dei Produttori in attesa, quindi i produttori producono dati, mettendosi in coda al `Lock` per depositare il loro prodotto (solo 1 ci riuscità), dall'altra parte il consumatore si sincronizza con quello stesso lock e consuma il dato prodotto.

```java
public ReentrantLock(boolean fair)
```

- `fair` significa che mantiene la coda dei Thread in una struttura dati in modo tale da garantire che prima o poi tutti vengano fatti accedere.
  Riducendo il rischio di _starvation_ a prezzo di una prestazione inferiore.

```java title:PrintQueue
public class PrintQueue implements Printer {
	private final Lock queueLock = new ReentrantLock();
	
	public void printJob(Object document) {
		queueLock.lock();
		try {
			Long duration = (long)(Math.random() * 10000);
			Thread.sleep(duration);
		} catch(InterruptedException e) {
			e.printStackTrace();
		} finally {
			queueLock.unlock();
		}
	}
}
```

```java title:Job
class Job implements Runnable {
	private Printer printQueue;
	
	public Job(Printer printQueue) {
		this.printQueue = printQueue;
	}
	
	@Override
	public void run() {
		printf("%s: Going to print a document\n" Thread.currentThread().getName());
		printQueue.printJob(new Object());
		printf("%s: The document has been printed\n" Thread.currentThread().getName());
	}
}
```

```java title:main
void main() {
	PrintQueue printQueue = new PrintQueue();
	Thread thread[] = new Thread[10];
	for(int i = 0; i < 10; i++) {
		thread[i] = new Thread(new Job(printQueue), "Thread " + i);
	}
	for(int i = 0; i < 10; i++) {
		thread[i].start();
	}
}
```
### Conditionals
A volte non tutti i Thread che cercano di acquisire un lock sono uguali: possono avere semantiche diverse e necessitare di essere segnalati in condizioni differenti.
Una `Condition` permette di separare l'accodamento in attesa dal possesso del lock che controlla l'attesa.
>[!important] Scopo
>Lo scopo è di poter gestire, ==su di un solo lock==, più condizioni di attesa distinte.

> Una `Condition` viene fornita direttamente da un `Lock`:
> ```java
> public Condition newCondition();
> 
> public void signal();
> public void signalAll();
> public void await();
> ```

```java title:Esempio_Conditionals
class CharSource {
	public boolean hasMoreLines()
	public Optional<String> getLine();
}

class Buffer {
	private LinkedList<String> buffer;
	private int maxSize;
	private ReentrantLock lock;
	private Condition lines, space;
	private boolean pendingLines;
	
	public Buffer(int maxSize) {
		this.maxSize = maxSize;
		pendingLines = true;
		buffer = new LinkedList<String>();
		lock = new ReentrantLock();
		lines = lock.newCondition();
		space = lock.newCondition();
	}
	
	public void insert(String line) {
		lock.lock();
		try {
			while(buffer.size() == maxSize) {
				space.wait();
			}
			buffer.offer(line);
			out.printf("%s: Inserted Line: %d\n", Thead.currentThread().getName(), buffer.size());
			lines.signalAll();
		} catch (InterruptedException e) {
			e.printStackTrace();
		} finally {
			lock.unlock();
		}
	}
	
	public Optional<String> get() {
		Optional<String> line = Optional.empty();
		lock.lock();
		try {
			while((buffer.size() == 0) && hasPendingLines()) {
				lines.wait();
			}
			if(hasPendingLines()) {
				line = Optional.ofNullable(buffer.poll());
				space.signalAll();
			} 
		} catch (InterruptedException e) {
			e.printStackTrace();
		} finally {
			lock.unlock();
		}
		return line;
	}
	
	public boolean hasPendingLines() {
		return pendingLines || buffer.size() > 0;
	}
	
	public void setPendingLines(boolean pendingLines) {
		this.pendingLines = pendingLines;
	}
}

```

```java title:Producer_Consumer

class Producer implements Runnable {
	private CharSource source;
	private Buffer buffer;
	
	@Override
	public void run() {
		buffer.setPendingLines(true);
		while(source.hasMoreLines()){
			source.getLine().ifPresent(line -> {
				buffer.insert(line);
				randomWait(50);
			})
		}
		buffer.setPendingLines(false);
	}
}

class Consumer implements Runnable {
	private Buffer buffer;
	
	@Override
	public void run() {
		while(buffer.hasPendingLines()) {
			buffer.get().ifPresent(line -> process(line));
		}
	}
	
	private void process(String line) {
		LockedBuffer.randomWait(250);
	}
}

void main() {
	CharSource source = new CharSource(100, 100);
	Buffer buffer = new Buffer(20);
	Thread producer = new Thread(new Producer(source, buffer), "Producer");
	Thread[] consumers = new Thread[] {
		new Thread(new Consumer(buffer)),
		new Thread(new Consumer(buffer)),
		new Thread(new Consumer(buffer))
	};
	producer.start();
	for(Thread t : consumers)
		t.start();
}
```

>[!waring] Attenzione
>Maneggiando direttamente i `Lock` si chiede al sistema di delegarci un notevole potere, ed insieme ne riceviamo una corrispondente responsabilità, in quanto scrivere questo tipo di codice non è affatto semplice.

### Semaphores
Per controllare l'accesso ad un insieme omogeneo di risorse, si usa in _semaforo_.
Un semaforo è simile ad un `Lock`, ma tiene un conteggio invece di un semplice stato libero/occupato.

```java
public Semaphore(int permits, boolean fair)
```

Se inizializzato come `fair`, l'ordinamento in attesa è **garantito FIFO**.
Il costo della fairness è giustificato quando il semaforo regola l'accesso ad un insieme di risorse. In caso di un uso diverso, un semaforo non `fair` è molto più efficiente.
Ad ogni acquisizione il numero di "permessi" disponibile diminuisce, mentre ad ogni "rilascio" il numero viene aumentato.
```java
public void acquire() // ne acquisisco 1
public void acquire(int permits) // ne posso acquisire un certo numero

public void release()
public void release(int permits)
```

>[!important] N.B.
>Il valore iniziale del semaforo non è un limite: può essere superato, e può essere anche negativo inizialmente.
>Mantenere la coerenza semantica sta all'utilizzatore.
>==A differenza di un lock, un `semaphore` può essere rilasciato da un Thread diverso a quello che lo ha acquisito==.

```java
protected void reducePermits(int reduction);
```

La maggior parte dei metodi di `Semaphore`:
- può lanciare `InterruptedException` se il thread viene interrotto durante l'attesa.
- lancia `IllegalArgumentException` se il parametro è negativo

```java
public boolean tryAcquire()
public boolean tryAcquire(int permits, long timeout, TimeUnit unit)
```
`tryAcquire()` ha un funzionamento simile a `tryLock()` solo che invece del lock, mi prendo 1 permesso.

>[!warning] Attenzione
> `tryAcquire()` è in grado di violare la fairness del semaforo
## Dati Thread-Safe
