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



## Dati Thread-Safe
