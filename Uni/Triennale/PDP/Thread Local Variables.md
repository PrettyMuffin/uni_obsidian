Possiamo usare un approccio diverso, abbiamo una variabile ma magari vogliamo che la linea di esecuzione che passa per quel codice abbia una sua versione di variabile, non condivisa con gli altri.
```java
public class ThreadLocal<T>
```

==Una variabile Thread Local esiste in una copia differente ed indipendente per ciascun [[Thread]] che attraversa la sua dichiarazione.==

```java
static <S> ThreadLocal<S> withInitial(Supplier< ? extends S> supplier)
```

Ci permette di dare del codice per inizializzare il valore nel momento in cui un thread passa per quella variabile e quindi ha bisogno di una sua copia personale.

```java
public T get()
```
Ha un risultato differente per ciascuna linea di esecuzione

```java
public void remove()
```
Un thread vuole togliere il valore da quella variabile
Oppure settarlo attraverso
```java
public void se(T value)
```

```java
protected T initialValue()
```
Posso chiedere che venga reimpostato il valore iniziale

```java
class LocalVar {
	private static final var nextId = new AtomicInteger(0); // ogni thread accede sempre allo stesso, per quello si mette AtomicInteger

	ThreadLocal<Integer> counter;

	LocalVar() {
		counter = ThreadLocal.withInitial(() -> nextId.IncrementAndGet());
	}

	Integer get() { return counter.get(); }
}
```

```java
class LocalReader implements Runnable {
	private final LocalVar var;
	private final int item;

	LocalReader(LocalVar var, int item) {
		this.var = var;
		this.item = item;
	}

	@Override
	public void run() {
		out.println(Thread.currentThread().getName() + ", item " + item + ": read " + var.get());
	}
}
```

```java
ExecutorService executor = Executors.newFixedThreadPool(4);
LocalVar var = new LocalVar();
IntStream.range(0, 20).forEach(a -> executor.execute(new LocalReader(var, a)));
executor.shutdown();
```