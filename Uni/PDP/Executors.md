>[!obj] Obbiettivo
>Creare un [[Thread]] per ogni operazione da fare può velocemente diventare costoso.
>L'amministrazione dei [[Thread]]  impegnati, allo stesso modo, si complica al crescere del numero degli oggetti.
La soluzione è cedere parte del controllo al sistema, in cambio di maggiore semplicità ed efficienza.
Questo oggetto si chiama **Executor**.
>```java
>public interface Executor
>```

È un'interfaccia perché ce ne sono diversi, ma tutti fanno sostanzialmente la stessa cosa.
```java
void execute(Runnable command)
```
ovvero eseguire un comando per conto nostro in maniera asincrona. Quindi noi scriviamo semplicemente i nostri `Runnable` e questo servizio li esegue al posto nostro.

```java title:FixedThreadPool
Executor executor = Executors.newFixedThreadPool(4);
var threads = Stream.generate(new ThreadSupplier());
System.out.println("Scheduling runnables");
threads.limit(10).forEach((thread) -> executor.execute(thread));
System.out.println("Done scheduling");
```

###### Esempi di Executors
- `FixedThreadPool` ha un insieme finito di thread che prende e esegue, alternando i lavori di ciascun thread quando quest'ultimo ha finito l'esecuzione precedente.
- `SingleThreadPool` come suggerisce il nome utilizza un singolo thread per eseguire i comandi che gli vengono assegnati.
- `CachedThreadPool` Riusa thread già creati, ne crea nuovi se necessario. Contro: se gli diamo subito tipo 10.000 thread che non terminano immediatamente questo crea 10.000 thread.
- `ScheduledThreadPool` Esegue i compiti con una temporizzazione, quindi significa che possiamo dirgli "esegui questo runnable tra 10 minuti".
- `WorkStealingPool` Punta ad usare tutti i processori disponibili. Specializzato per il framework di fork/join, ovvero un'attività ne genera delle altre.
- `ThreadPerTask` Per ogni compito avvia un nuovo thread.
- `VirtualThreadPerTask` Per ogni compito avvia un nuovo [virtual thread](Thread#Virtual%20Thread) per task.

