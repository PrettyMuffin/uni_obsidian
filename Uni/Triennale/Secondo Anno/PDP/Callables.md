Fino a prima delle callables abbiamo usato come lavoro da seguire dei `Runnable`, cioè dei blocchi privi di risultato.

L'interfaccia `Callable` ci permette di definire dei compiti che producono un risultato

```java
public interface Callable<V> {
	V call() throws Exception;
}
```

Un semplice [[Executors]] non esegue `Callable`: è necessario scegliere un `ExecutorService`, che espone i metodi necessari.

```java
public interface ExecutorService extends Executor 
```

Il metodo con cui sottoponiamo un compito all'`ExecutorService` è `submit`

```java
<T> Future<T> submit(Callable<T> task);
```

Il metodo `submit` ci ritorna un `Future` dello stesso tipo del `Callable`.

## `Future<V>`
Un `Future` rappresenta un calcolo che prima o poi ritornerà un valore.
È possibile verificare se il calcolo è stato completato, ottenere il valore risultante, o controllare se sia ancora in corso.
```java
public interface Future<T>
```

L'interfaccia `Future` mette a disposizione un metodo
```java
T get()
```

che blocca l'esecuzione e aspetta che la `Callable` finisca per poi ritornare il valore.
```java
boolean isDone()
```
Ritorna true se il `Future` ha completato l'esecuzione

>[!example]- ScheduledFuture
> ```java title:FactorialBuilder
> class FactorialBuilder implements Supplier<Callable<Integer>> {
>    @Override
>    public Callable<Integer> get() {
>        return () -> {
>            int random = (int) (Math.random() * 10);
>            int result = random;
>            for (int i = 1; i <= random; i++) {
>                result *= i;
>            }
>            System.out.printf("%s is goind to wait %d milliseconds\n", Thread.currentThread().getName(), result);
>            Thread.sleep(result);
>            return result;
>        };
>    }
>}
>```
>Questo è un `Supplier` di numeri fattoriali, il thread aspetta quel numero di millisecondi prima di ritornare e finire
> ```java title:ScheduledFuture
> public class ScheduledFuture {
>    public static void main(String[] args) {
>        ThreadPoolExecutor executor = (ThreadPoolExecutor) Executors.newFixedThreadPool(5); // implementazione ExecutorService
>        Supplier<Callable<Integer>> supplier = new FactorialBuilder();
>        List<Future<Integer>> futures = new ArrayList<Future<Integer>>();
>
>        for (int i = 0; i < 10; i++) {
>            futures.add(executor.submit(supplier.get()));
>        }
>        while (executor.getCompletedTaskCount() < futures.size()) {
>            try {
>                System.out.printf("Completed Tasks: %2d: %s\n", executor.getCompletedTaskCount(), format(futures));
>                TimeUnit.MILLISECONDS.sleep(50);
>            } catch (InterruptedException | ExecutionException e) {
>                e.printStackTrace();
>            }
>        }
>        System.out.println("Finished Tasks");
>    }
>
>    private static String format(List<Future<Integer>> futures) throws InterruptedException, ExecutionException {
>        StringBuilder done = new StringBuilder();
>        for (Future<Integer> future : futures) {
>            if (future.isDone())
>                done.append(String.format("%d", future.get()));
>            else
>                done.append("_");
>        }
>        return done.toString();
>    }
>}
>```

Con a disposizione una lista di `Callables`, un `ExecutorService` ci permette di:
1. Ottenere un risultato di un Future che ha terminato (non necessariamente il primo, ma probabilmente uno dei primi)
   ```java
   <T> T invokeAny (Collection < ? extends Callable <T>> tasks)
   ```
   >[!example]- AnyFutures
   >```java
   >ThreadPoolExecutor executor = (ThreadPoolExecutor) Executors.newFixedThreadPool(4);
        >var supplier = new FactorialBuilder();
        >var callables = new ArrayList<Callable<Integer>>();
        >for (int i = 0; i < 10; i++) {
            >callables.add(supplier.get());
        >}
        >System.out.println("Scheduling computations");
        >executor.invokeAny(callables);
        >System.out.println("Done scheduling.");
   >```
2. Ottenere una lista di `Future` nel momento in cui sono tutti completati
   ```java
   <T> List<Future<T>> invokeAll(Collection< ? extends Callable<T>> tasks)
   ```
   Ritorna una lista di `Future` e non una lista di risultati pk ci potrebbero essere dei `Future` che sono andati in eccezione.
   >[!example]- AllFutures
   >```java
   >ThreadPoolExecutor executor = (ThreadPoolExecutor)Executors.newFixedThreadPool(4);
        >var supplier = new FactorialBuilder();
        >var callables = new ArrayList<Callable<Integer>>();
        >for (int i = 0; i < 10; i++) {
            >callables.add(supplier.get());
        >}
        >System.out.println("Scheduling computations");
        >executor.invokeAll(callables);
        >System.out.println("Done scheduling.");
        >```

>[!important] Terminazione
>Un `ExecutorService` rimane sempre in attesa di nuovi compiti da eseguire, impedendo alla JVM di terminare.


Per permettere alla JVM di fermarsi bisogna esplicitamente fermare il servizio.
```java
void shutdown()
```
Questo metodo fermerà l'executor una volta che tutti i thread all'interno dell'executor hanno finito le loro task
```java
boolean awaitTermination(long timeout, TimeUnit unit)
```
Questo dice o aspetta tot numero di tempo determinato anche dall'enum `TimeUnit` o aspetta la terminazione dell'executor. Ritorna `true` se l'executor sta ancora andando, `false` se il tempo è scaduto
```java
boolean isTerminated()
```
autoesplicativo
Se invece è urgente terminare l'executor si può usare
```java
List<Runnable> shutdownNow()
```
Ritorna una lista di `Runnable`che rappresentano i thread che non hanno terminato l'esecuzione.


