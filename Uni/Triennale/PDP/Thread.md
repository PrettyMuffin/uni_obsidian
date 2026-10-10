Nel linguaggio Java, un Thread è rappresentato da una istanza dell'omonima classe.
```java
class Thread {
	public Thread(Runnable target)
}
```

Il thread accetta un `Runnable` ovvero un oggetto il cui tipo implementa l'interfaccia `Runnable`, che contiene solo il metodo `run()` dove mettiamo il codice che vogliamo far eseguire al thread.

Il metodo principale è il metodo `start()`, che avvia un nuovo percorso di esecuzione che lavora all'interno della stessa JVM, condividendo lo stesso heap e quindi lo stesso stato complessivo.
==In pratica `start()` ritorna immediatamente e il codice prosegue la sua esecuzione, però nello stesso momento in cui `start()` ritorna, il codice affidato al thread comincia la sua esecuzione.==

## Interfaccia Runnable

>[!obj] Obiettivo
> L'interfaccia `Runnable` modella un compito da eseguire da cui **non ci si aspetta un risultato**.

```java
@FunctionalInterface
public interface Runnable {
	void run();
}
```

>[!example]- **ThreadSupplier**
>Fornitore di thread che aspettano del tempo
>```java
>class ThreadSupplier implements Supplier<Thread> {
>	Supplier<Long> waitTime;
>	public ThreadSupplier() {
>		waitTime = () -> Math.roung(Math.random() * 1000);
>	}
>	public ThreadSupplier(long wait) {
>		waitTime = () -> wait;
>	}
>	@Override
>	public Thread get() {
>		return new Thread(()-> {
>			String name = Thread.currentThread().getName();
>			long time = waitTime.get();
>			try {
>				Thread.sleep(time);
>			} catch(InterruptedException e) {
>				e.printStackTrace();
>			}
>		});
>	
>	}
>}
>```
>Thread singolo
>```java
>public static void main(String[] args) {
>	Thread a = new ThreadSupplier().get();
>	out.println("Starting Single Thread");
>	a.start();
>	out.println("Done Starting.");
>}
>```
>Thread multipli
>```java
>public static void main(String[] args) {
    >    Stream<Thread> threads = Stream.generate(new ThreadSupplier());
    >    System.out.println("Starting Threads");
    >    threads.limit(10).forEach((Thread thread) -> thread.start());
    >    System.out.println("Done Starting.");
    >}
>```

## Operazioni Sui Thread

```java
public Thread(ThreadGroup group, Runnable target, String name, long stackSize)
```

- `ThreadGroup`: Serve per propagare constraints di sicurezza (priorità, permessi...)

```java
public String getName()
```
ritorna il nome del thread, per capire quale thread abbiamo in mano, ==ogni thread ha un nome==.

```java
public boolean isAlive()
```
ritorna `true` se un thread è vivo, ovvero potenzialmente ha attività da fare, ovvero può essere messo in esecuzione in futuro.

```java
public void run()
```
Lancia il codice che è stato passato a Runnable, la differenza tra questo è `start()` è che `run()` **è sincrono** ovvero lancia il codice nella linea di esecuzione corrente.

```java
public static Thread currentThread()
```
Permette di ottenere l'oggetto che rappresenta la linea di esecuzione corrente.

```java
public static void sleep(long millis) throws InterruptedException
```
Dice al thread corrente di andare in pausa per almeno `millis` millisecondi

>[!example]- ThreadObserver
>>[!obj] Obiettivo
>>Thread che osserva lo stato di un altro thread.
>
>```java
>final Thread tgt = new ThreadSupplier(800L).get();
>final Thread observer = new Thread(()-> {
>	System.out.println("(Start) Target live: " + tgt.isAlive());
>	for(int i = 0; i < 10; i++ ) {
>		try {
>			Thread.sleep(100L);
>			System.out.println("Target live: " + tgt.isAlive());
>		} catch(InterruptedException e) {
>			System.out.println("Observer Interrupted");
>			e.printStackTrace();
>		}
>	}
>	System.out.println("(End) Target live: " + tgt.isAlive());
>});
>observer.start();
>tgt.start()
>```

## Stati Del Thread
![[Thread_States.png]]

### New
Il thread è appena stato creato, ovvero preparato per l'esecuzione con tutto ciò che gli necessita per essere eseguito correttamente.

### Runnable
Una volta chiamato `start()` il Thread diventa runnable. Il metodo `run()` **può** essere messo in esecuzione, **non che lo sia**.

### Running
Il Thread è effettivamente in esecuzione, ha a disposizione la CPU finché non gli viene sottratta o passa ad altro stato.

### Blocked
Il thread ha richiesto accesso ad una risorsa monitorata (per es. un canale di  I/O) e sta aspettando la disponibilità di dati.

### Waiting
Il thread si è posto in attesa di una risorsa protetta da un lock chiamando wait(object) e sta aspettando il suo turno.

### Timed_Waiting
Il thread si è posto in attesa di un determinato periodo di tempo (per es. con sleep(millis)), scaduto il quale ritornerà runnable.

### Terminated
Il meotodo `run()` è completato (correttamente o meno) ed il thread ha concluso il lavoro.


---

## Interruzioni ed Eccezioni

```java
public void interrupt()
```
Questo metodo interrompe il thread che lo chiama. Lanciando così una `InterruptedException`.

>[!example]- ThreadInterrupter
>```java
>public class ThreadInterrupter implements Runnable {
>    private Thread tgt;
>
>    public ThreadInterrupter(Thread target) {
>        tgt = target;
>    }
>
>    @Override
>    public void run() {
>        System.out.println("Target Thread alive: " + tgt.isAlive());
>        for (int i = 0; i < 4; i++) {
>            try {
>                Thread.sleep(1000L);
>                tgt.interrupt();
>                System.out.println("Target Thread interrupted");
>            } catch (InterruptedException e) {
>                System.out.println("Thread interrupted");
>                e.printStackTrace();
>            }
>        }
>        System.out.println("Target Thread alive: " + tgt.isAlive());
>    }
>
>    public static void main(String[] args) {
>        Thread tgt = new ThreadSupplier(2000L).get();
>        Thread interrputer = new Thread(new ThreadInterrupter(tgt));
>        interrputer.start();
>        tgt.start();
>    }
>}
>```
>Con questo codice abbiamo
>```text
>Target Thread alive: true
>Thread-0 will wait for 2000 milliseconds.
>Target Thread interrupted
>Thread-0 was interrupted.
>java.lang.InterruptedException: sleep interrupted
>	at java.base/java.lang.Thread.sleep0(Native Method)
>	at java.base/java.lang.Thread.sleep(Thread.java:509)
>	at src.ThreadSupplier.lambda$get$2(ThreadSupplier.java:23)
>	at java.base/java.lang.Thread.run(Thread.java:1583)
>Target Thread interrupted
>Target Thread interrupted
>Target Thread interrupted
>Target Thread alive: false
>```
>>[!question] Come si legge questo messaggio d'errore?
>>Intanto si nota che c'è stata una `InterruptedException`.
>>Per risalire al luogo dove c'è stata si legge lo `stackTrace()` in modo tale da sapere da dove è venuto.
>>Lo `stackTrace` si legge così in questo caso:
>>1. L'eccezione `InterruptedException` è avvenuta a `java.base/java.lang.Thread.sleep0(Native Method)` che sarebbe un metodo livello kernel di java.
>>2. Che è stato chiamato dal metodo `java.base/java.lang.Thread.sleep(Thread.java:509)`
>>3. Che è a sua volta stato chiamato da `src.ThreadSupplier.lambda$get$2(ThreadSupplier.java:23)`
>>4. Che a sua volta è stato chiamato da `java.base/java.lang.Thread.run(Thread.java:1583)`


```java
public void setUncaughtExceptionHandler(Thread.UncaughtExceptionHandler eh)
```
Il metodo **`setUncaughtExceptionHandler`** della classe **`Thread`** in Java permette di definire un gestore personalizzato per le eccezioni non catturate (**uncaught exceptions**) che si verificano durante l'esecuzione di un thread (ad esempio, un `NullPointerException` o `ArrayIndexOutOfBoundsException`).
In quel caso Java cerca:
1. Un **`UncaughtExceptionHandler`** specifico per quel thread (impostato con `setUncaughtExceptionHandler`).
    
2. Se non presente, usa l'**handler predefinito del thread group** (che di solito stampa lo stack trace su `System.err`).

```java title:Sintassi
thread.setUncaughtExceptionHandler(new Thread.UncaughtExceptionHandler() {
    @Override
    public void uncaughtException(Thread t, Throwable e) {
        // Logga o gestisci l'eccezione
        System.err.println("Eccezione nel thread " + t.getName() + ": " + e);
    }
});
```

```java title:lambda
thread.setUncaughtExceptionHandler((t, e) -> {
    System.err.println("Thread " + t.getName() + " ha lanciato: " + e);
});
```

>[!example] Demo
>```java
>public class UncaughtExceptionDemo {
>    public static void main(String[] args) {
>        Thread thread = new Thread(() -> {
>            // Simula un'eccezione non catturata
>            throw new RuntimeException("Errore critico!");
>        });
>
>        // Imposta un gestore personalizzato
>        thread.setUncaughtExceptionHandler((t, e) -> {
>            System.out.println("[CUSTOM HANDLER] Thread " + t.getName() 
>                + " crashed: " + e.getMessage());
>        });
>
>        thread.start();
>    }
>}
>```
>```text
>[CUSTOM HANDLER] Thread Thread-0 crashed: Errore critico!
>```
>Senza l'handler vedrei lo stack trace in console

# Virtual Thread
Una delle introduzioni più importanti di Java 21.

>[!def] Definizione
>Un _Virtual Thread_ è una linea di esecuzione gestita completamente dalla JVM, che non corrisponde ad un Thread di sistema operativo.
>Di conseguenza, mentre in una macchina di media potenza ci si può aspettare di riuscire a gestire intorno a 10000 threads, la stessa macchina può gestire milioni di Virtual Threads.

I Virtual Thread sono fatti per occupare molte meno risorse infatti sono circa 3-4 ordini di grandezza in meno rispetto ad un Thread si sistema operativo.

Dal punto di vista sintattico i Virtual Thread sono dei Thread normali, ma tutte le API bloccanti le gestiscono in modo particolare.
La JVM gestisce autonomamente la messa in attesa del Virtual Thread (_unmounting_) durante l'esecuzione di una operazione bloccante, per poi riprenderne l'esecuzione su di un nuovo Thread (_mounting_).
Di conseguenza è la JVM che si occupa di gestire quando un Virtual Thread viene "mountato" o "smontato" da un thread di sistema operativo; queste operazioni sono più economiche di un passaggio di Thread, con il risultato complessivo che si possono avere molte più linee di esecuzione senza pagarne completamente i costi di cambio contesto.
In generale, si possono considerare i Virtual Thread una forma di _concorrenza collaborativa_ gestita automaticamente dalla JVM, e che non richiede una programmazione esplicita da parte nostra.