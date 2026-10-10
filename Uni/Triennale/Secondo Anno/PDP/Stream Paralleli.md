Sub-paradigma della concorrenza

>[!def] Stream
> Lo Stream è una diversa rappresentazione dell'iterazione su di un insieme di oggetti.
> In particolare, rappresenta l'iterazione su di un insieme di cardinalità non nota, potenzialmente infinita.

In cambio di alcune restrizioni sulle operazioni possibili e della cessione del controllo sull'ordine di iterazione otteniamo:
- Una API per comporre passi di elaborazioni riusabili su di una successione di oggetti. Cioè lavorare con uno stream significa che da un lato cediamo il controllo alla libreria, ma dall'altro otteniamo che possiamo strutturare la nostra elaborazione in passi successivi e questi passi sono facilmente riusabili in elaborazioni differenti.
- Una reificazione della trasformazione, cioè il set di operazioni sul singolo elemento diventa un oggetto esso stesso e quindi può essere trattato come tale, che ne permette il riuso su sorgenti diverse ed il test in isolamento.
- La promozione di un approccio funzionale al trattamento dei dati. Ovvero usiamo funzioni pure di trasformazioni che trasformano oggetti in altri oggetti.
- Lo stile diventa più espressivo e dichiarativo per descrivere una ampia classe di elaborazioni.
- Un modello di esecuzione in cui esecuzione sequenziale e parallela non richiedono modifiche al codice (tanta roba).

>[!question] In cosa sono differenti gli Stream dalle Collezioni?
>La caratteristica principale di una collezione è la performance dell'accesso ai contenuti.
>L'algoritmo che usa quei contenuti è completamente estraneo a queste considerazioni.
>Mentre invece uno Stream richiede la definizione dell'algoritmo di calcolo che avverrà sopra i suoi elementi.
>Questi ultimi non possono essere acceduti direttamente, e l'algoritmo non ha nessun controllo sul loro recupero, perché gli elementi arrivano uno dopo l'altro dallo stream.
>Questo significa che è lo stream ad avere il controllo sull'algoritmo, e può usare questo controllo per prendere decisioni su come eseguirlo.

## Stream Flags (Java)
La sorgentedi uno Stream può dichiarare alcune _caratteristiche_ che gli operatori intermedi possono verificare e che l'operazione terminale usa per prendere decisioni sulla esecuzione.

| Flag       | Caratteristica                                                                                                        |
| ---------- | --------------------------------------------------------------------------------------------------------------------- |
| CONCURRENT | Prallelizzabile                                                                                                       |
| DISTINCT   | Elementi Distinti secondo `equals()`                                                                                  |
| IMMUTABLE  | Immutabile durante il consumo                                                                                         |
| NONNULL    | Elementi non nulli                                                                                                    |
| ORDERED    | Elementi ordinati dalla sequenza di apparizione,<br>ovvero la sequenza con cui appaiono in uno stream<br>è rilevante. |
| SIZED      | Dimensione nota                                                                                                       |
| SORTED     | Gli elementi hanno un ordine naturale, ovvero possono<br>essere sortati.                                              |
| SUBSIZED   | Suddivisioni di dimensione nota.                                                                                      |
L'operazione terminale, cioè quella che va ad avviare il calcolo, prima di avviare il calcolo ha piena visibilità di quali sono le caratteristiche della pipeline di esecuzione, e può quindi prendere decisioni in merito.
```java title:CountStream
long cnt = IntStream.range(1, 20)
		   .map((i)-> {
			   System.out.println(i);
			   return i * 2;
		   }).limit(5).count();
System.out.println(">" + cnt);
```
```text title:Output
> 5
```

Da notare che ha stampato solo `> 5` e non tutti gli elementi dello stream.
Questo perché l'operazione terminale `count()` si rende conto che l'operazione appena precedente è un `limit(5)`, dunque decide di ignorare completamente il `map` e ritorna 5.

Una pipeline può essere eseguita in modo sequenziale o parallelo semplicemente configurandola come tale.
```java
S parallel() // ritorna uno stream S che può essere eseguito in parallelo
S sequential() // ritorna uno stream S che può essere eseguito in parallelo 
```

Queste sono chiamate di configurazione e l'ultima determina come lo stream viene eseguito.
Una pipeline `ORDERED` e usiamo operatori che non cambiano questa caratteristica, garantisce di ritornare gli elementi nell'ordine in cui sono stati emessi.
I passi intermedi che mantengono questa caratteristica non modificano l'ordine.
==L'ordinamento è conservato, se possibile, anche dopo una esecuzione parallela.==
```java title:OrderedStream
IntStream.range(1, 20).parallel().map(i -> {
	System.out.println(i + " " + Thread.currentThread().getName());
	return i * 2;
}).forEachOrdered(i -> {
	System.out.println(">>> " + i);
});
```
```java title:Output
9 ForkJoinPool.commonPool-worker-8
2 ForkJoinPool.commonPool-worker-9
3 ForkJoinPool.commonPool-worker-2
12 main
17 ForkJoinPool.commonPool-worker-4
16 ForkJoinPool.commonPool-worker-7
5 ForkJoinPool.commonPool-worker-6
4 ForkJoinPool.commonPool-worker-11
7 ForkJoinPool.commonPool-worker-5
6 ForkJoinPool.commonPool-worker-1
19 ForkJoinPool.commonPool-worker-10
8 ForkJoinPool.commonPool-worker-3
1 ForkJoinPool.commonPool-worker-9
11 ForkJoinPool.commonPool-worker-8
10 ForkJoinPool.commonPool-worker-2
14 main
13 ForkJoinPool.commonPool-worker-4
15 ForkJoinPool.commonPool-worker-7
18 ForkJoinPool.commonPool-worker-6
>>> 2
>>> 4
>>> 6
>>> 8
>>> 10
>>> 12
>>> 14
>>> 16
>>> 18
>>> 20
>>> 22
>>> 24
>>> 26
>>> 28
>>> 30
>>> 32
>>> 34
>>> 36
>>> 38
```

Come si può notare mantiene l'ordine, ovvero stampa le corrispondenti 1 -> 2, 2 -> 4, 3 -> 6 ... questo per mantenere l'ordine degli elementi dello stream.
```java title:Output(forEach)
4 ForkJoinPool.commonPool-worker-11
2 ForkJoinPool.commonPool-worker-9
16 ForkJoinPool.commonPool-worker-7
17 ForkJoinPool.commonPool-worker-4
6 ForkJoinPool.commonPool-worker-1
7 ForkJoinPool.commonPool-worker-6
5 ForkJoinPool.commonPool-worker-8
12 main
19 ForkJoinPool.commonPool-worker-10
>>> 32
>>> 24
8 ForkJoinPool.commonPool-worker-3
14 main
>>> 28
13 main
>>> 26
11 main
>>> 22
10 main
>>> 20
1 main
>>> 2
18 main
>>> 36
9 ForkJoinPool.commonPool-worker-5
3 ForkJoinPool.commonPool-worker-2
>>> 4
>>> 14
>>> 34
>>> 8
15 ForkJoinPool.commonPool-worker-7
>>> 30
>>> 38
>>> 12
>>> 16
>>> 10
>>> 18
>>> 6
```

Questo invece è un output se invece di `forEachOrdered` facessi `forEach`, **attenzione: il fatto che ci siano delle stampe della `map` tra il `forEach` è perché è come la libreria ha ottimizzato le istruzioni**.

Una pipeline `SORTED` mantiene gli elementi ordinati secondo il loro ordinamento naturale o dato da un `Comparator`.

Una pipeline `SUBSIZED` vuol dire che può fornire efficientemente dei sottoinsiemi dimensionati per l'esecuzione parallela.

Le operazioni che passiamo ai passi intermedi devono essere:
- Non invasive: non devono modificare od interferire con gli elementi dello stream.
- (Nella maggior parte dei casi) prive di uno stato interno: devono essere cioè funzioni pure.

Alcune operazioni sono identificate come _short-circuiting_: significa che possono interrompere l'esecuzione della pipeline prima dell'esame di tutti gli elementi (es: `limit()` di prima).

```java
public class CandidateNumber {
	public final int n;
	public final List<Integer> divisors;

	CandidateNumber(int n, List<Integer> divisors) {
		this.n = n;
		this.divisors = divisors;
	}
}
// ######################################à
public class Divisors implements Function<Integer, CandidateNumber> {
	@Override
	public CandidateNumber apply(Integer n) {
		List<Integer> divis;
		// ...
		return new CandidateNumber(n, divis);
	}
}
// #########################################
public class Perfect implements Predicate<CandidateNumber> {
	@Override
	public boolean test(CandidateNumber c) {
		Integer sum = c.divisors.stream()
					  .collect(Collectors.summingInt((Integer n) -> n));
		return (sum + 1) == c.n
	}
}
// ##########################################
List<CandidateNumber> match = IntStream.range(30, 100000)
							  .boxed().parallel().map(new Divisors())
							  .filter(new Perfect()).findAny().stream()
							  .collect(Collectors.toList());
match.forEach(x -> { System.out.println(x); });
```

>[!def] Operazioni _stateful_
Alcune operazioni sono dette _stateful_: può necessitare di consumare tutto o gran parte dell'input per poter emettere l'output mantenendo le caratteristiche desiderate (per es. ordinamento).

## Spliterator
Per esprimere una sorgente in grado di essere parallelizzabile non è sufficiente l'API esposta da `Supplier` o `Iterator` perché entrambe non hanno la potenza necessaria per esplicitare le opportunità di suddividere uno stream in più sottostream da affidare a più linee di esecuzione contemporanee.
Servono infatti dei metodi che consentano alla sorgente di esplicitare le opportunità di suddivisione dello stream in rami di esecuzione indipendenti.
Questi metodi vengono forniti dall'interfaccia `Spliterator`, il cui metodo di avanzamento non è più `next`, ma `tryAdvance()`.
Questo nuovo metodo ribalta il funzionamento dell'iterator: non è l'utilizzatore che ottiene il nuovo elemento, ma è lo stream che fornisce l'elemento al codice che deve operarci sopra.
`Spliterator` aumenta l'espressività di `Iterator` aggiungendo metodi per:
- Stimare gli elementi.
- Esplicitare le caratteristiche della sorgente.
- Attraversare in massa gli elementi rimanenti.
- Suddividere l'iterazione in più rami.

### Metodi Principali
Un `Spliterator<T>` ha alcuni metodi chiave:

- `boolean tryAdvance(Consumer<? super T> action)`  
    → processa l’elemento successivo, se esiste. (simile a `Iterator.next()` ma con callback)
    
- `Spliterator<T> trySplit()`  
    → prova a dividere lo Spliterator in due: restituisce un nuovo Spliterator con metà degli elementi, oppure `null` se non è divisibile.
    
- `long estimateSize()`  
    → stima di quanti elementi rimangono da processare.
    
- `int characteristics()`  
    → ritorna delle "etichette" (costanti) che descrivono le proprietà della sorgente (es. `ORDERED`, `SORTED`, `SIZED`, `NONNULL`).

```java title:trySplit
import java.util.*;

public class SpliteratorPizza {
    public static void main(String[] args) {
        List<String> lista = Arrays.asList("1","2","3","4","5","6","7","8");

        Spliterator<String> s0 = lista.spliterator();    // tutto s0 = [1..8]
        Spliterator<String> s1 = s0.trySplit(); //metà s1 = [1..4] s0 = [4..8]
        Spliterator<String> s2 = s1.trySplit(); //metà della metà s2 = [1..2]                                                   s1 = [3..4]

        System.out.println("s0 (rimasto):");
        s0.forEachRemaining(System.out::print); // 5 6 7 8

        System.out.println("\ns1 (rimasto):");
        s1.forEachRemaining(System.out::print); // 3 4

        System.out.println("\ns2 (nuovo da s1):");
        s2.forEachRemaining(System.out::print); // 1 2
    }
}

```
#### Regola d’oro per ricordare funzionamento `trySplit()`

- **Il nuovo Spliterator restituito** → prende “una parte” della sezione.
    
- **Lo Spliterator che chiama `trySplit()`** → si riduce alla sezione rimanente.
    
In questo modo la collezione viene “spaccata” senza duplicazioni.


Quello che viene rimosso è la possibilità di rimuovere l'elemento, questo perché nello stream non ha senso come operazione.

```java
T reduce(T identity, BinaryOperator<T> accumulator)
```

```java
int res = IntStream.range(1, 1001).parallel()
		  .reduce(0, (a, b) -> {
			  System.out.println(a + "+" b + "=" (a+b) + " " +
			  Thread.currentThread.getName());
			  return a + b;
		  });
System.out.println(">>> " + res);
```

## Collector
Nell'operazione di riduzione gestita da `reduce` l'accumulazione del risultato avviene creando nuovi valori. Questo però in certi casi non è efficiente.
Ci sono delle situazioni in cui non possiamo permetterci di creare un nuovo oggetto per ogni operazione di reduce, ==ma abbiamo bisogno di gestire un **accumulatore mutabile** che possa essere più efficiente.==
Per fare questo abbiamo a disposizione l'interfaccia `Collector`, che definisce un collettore, cioè un accumulatore che in maniera efficiente può sommare nuovi elementi.
```java
// @T Il tipo dell'elemento di input
// @A Il tipo dell'accumulatore (contenitore intermedio)
// @R Il tipo del risultato della riduzione
interface Collector<T, A, R>
```

La classe `Collectors` permette di produrre dei `Collector` a partire dagli elementi di base:
- un `Supplier<A>` fornisce un nuovo contenitore vuoto.
- un `BiConsumer<A, T>` definisce come aggiungere un elemento al contenitore.
- un `BinaryOperator<A>` definisce come unire due contenitori (per parallelismo).
- un `Function<A, R>` che trasforma l'accumulatore nel risultato finale.

In pratica: un `Collector` è la **ricetta** che dice:
1. **Come creare un contenitore vuoto**.
2. **Come aggiungere elementi** al contenitore.
3. **Come unire due contenitori** (importante per stream paralleli).
4. **Come trasformare il contenitore finale** nel risultato desiderato.

Combinando questi elementi lo Stream ha tutte le parti della strategia per applicare l'algoritmo, e ha le caratteristiche della sorgente per calcolare come organizzare l'esecuzione.

```java title:StringCollector
String res = IntStream.range(1, 1001).boxed()
			 .map(i -> i.toString()).parallel().collect(
			 // supplier
			 () -> new StringBuffer(),
			 // accumulator
			 (acc, el) -> acc.append(el),
			 // combiner
			 (resA, resB) -> resA.append(resB)
			 ).toString();
System.out.println(">>>> " + res);
```

```java title:CustomCollector
import java.util.*;
import java.util.stream.*;
import java.util.function.*;

public class CollectorSomma {
    public static void main(String[] args) {
        Collector<Integer, int[], Integer> sommaCollector = new Collector<>() {
            @Override
            public Supplier<int[]> supplier() {
                // contenitore intermedio: array di 1 elemento (il totale)
                return () -> new int[1];
            }

            @Override
            public BiConsumer<int[], Integer> accumulator() {
                // come aggiungere un numero
                return (acc, n) -> acc[0] += n;
            }

            @Override
            public BinaryOperator<int[]> combiner() {
                // come unire due accumulatori (utile in parallelo)
                return (acc1, acc2) -> {
                    acc1[0] += acc2[0];
                    return acc1;
                };
            }

            @Override
            public Function<int[], Integer> finisher() {
                // come ottenere il risultato finale
                return acc -> acc[0];
            }

            @Override
            public Set<Characteristics> characteristics() {
                return Collections.emptySet();
            }
        };

        int somma = Stream.of(1, 2, 3, 4, 5)
                          .collect(sommaCollector);
        System.out.println("Somma = " + somma); // stampa: 15
    }
}
```

>[!question] Ma nel `Custom Collector` perché abbiamo usato `int[]` invece di un `Integer`?
>- In un Collector l’**accumulatore (`A`) deve essere mutabile**, cioè deve poter essere aggiornato a ogni chiamata dell’`accumulator()`.
>- `Integer` in Java è **immutabile**: ogni volta che fai `acc += n`, in realtà stai creando un nuovo oggetto `Integer`, non modificando quello esistente.
>- Questo non va bene perché il Collector si aspetta di **accumulare nello stesso contenitore**.
Esempio problematico con `Integer`:
>
>`Supplier<Integer> supplier = () -> 0; BiConsumer<Integer, Integer> accumulator = (acc, n) -> acc = acc + n; // ⚠️ non funziona`
>
>Qui `acc = acc + n` crea un nuovo `Integer`, ma non modifica l’oggetto passato, quindi l’accumulatore non cambia davvero.
>**Array di un elemento (`int[1]`)**
>
>- È un “trucchetto” per avere un contenitore mutabile molto leggero.
>    
>- Dentro l’array modifico la cella `acc[0]`, senza ricreare nulla.
>
>👉 Quindi: l’`Integer` non va bene perché è immutabile.  
>Meglio usare **un contenitore mutabile** (array, `AtomicInteger`, `List`, `StringBuilder`, ecc.) come accumulatore.

Gli stream sono quindi un'ottima astrazione per modellare in modo semplice algoritmi su collezioni di elementi.
Il nostro codice può essere eseguito in parallelo con un semplice cambio di configurazione.
Lo stream tuttavia di default decide autonomamente quanto parallelismo usare, attraverso il `ForkJoinPool`. 
>[!question] Quanto parallelismo possiamo richiedere?
>>[!def] Blocking Factor
>>Intensità del calcolo di un algoritmo
>>- Un algoritmo con BF = 0 occupa costantemente la CPU.
>>- Un algoritmo con BF = 1 è costantemente in attesa di I/O
>
>Definito il _blocking factor_ possiamo dire:
>$$
>\text{\# threads} \leq \frac{\text{\# di cores}}{1 - \text{BF}}
>$$
>Se il nostro algoritmo effettua molta I/O può essere utile modificare l'impostazione standard del `ForkJoinPool` per aumentare il numero di thread disponibili.

Allo stesso tempo l'uso dello Stream come modello di calcolo può grandemente semplificare l'aspetto e quindi la leggibilità e la manutenibilità del nostro codice:
>[!failure] Poco Leggibile
>```java
>public static double compute(int n, int k) {
>	int index = n;
>	int count = 0;
>	int result = 0;
>	
>	while(count < k) {
>		if(isPrime(index)) {
>			result += Math.sqrt(index);
>			count++;
>		}
>		index++;
>}
>return result;
>```

>[!success] Leggibile
>```java
>public static double() compute(int n, int k) {
>	return Stream.iterate(n, e -> e + 1)
>		   .filter(Sample::isPrime)
>		   .limit(k)
>		   .mapToDouble(Math.sqrt)
>		   .sum();
>}
>```

## Stream Gatherers
Da Java 24, una nuova API chiamata `Stream Gatheres` il cui scopo è facilitare l'implementazione di operazioni intermedie _stateful_.
Un `Gatherer` implementa l'omonima interfaccia fornendo 4 funzioni:
- _initializer_ crea un nuovo stato.
- _integrator_ unisce un elemento allo stato.
- _combiner_ combina due stati.
- _finisher_ finalizza il calcolo.

La libreria usa questi quattro elementi per implementare una generica strategia in cui il `Gatherer` raccoglie un elemento dallo stream, lo computa nel suo stato interno, decidere se eventualmente emettere altri elementi nello stream, e se necessario combina più stati relativi ad essecuzioni parallele, o cura l'emissione degli ultimi elementi se lo stream sorgente è terminato.
>[!example]
> "i 3 elementi più grandi presi dallo stream precedente".
> Questa operazione deve accumulare tutti gli elementi in ingresso, ed emettere 3 elementi solo quando lo stream in ingresso è terminato.

ChatGPT:
1. Splitierators, spiegazione migliore ed esempi
2. Collector: spiegazione migliore ed esempi
3. Steam Gatherers: spiegazione superficiale con esempi

## Quando usare gli stream e quando no

### Quando usare gli Stream
Gli Stream sono ideali quando il problema può essere visto come **pipeline di trasformazioni indipendenti** sui dati.
- **Operazioni su collezioni/array**  
    → ad esempio filtrare, mappare, raccogliere risultati.
- **Trasformazioni funzionali semplici**  
    → quando si tratta di prendere input, applicare trasformazioni e ottenere output, senza stato condiviso.
- **Problemi con molto calcolo per elemento**  
    → in questo caso il parallelismo (`parallelStream`) può dare benefici reali.
- **Codice dichiarativo e conciso**  
    → se vuoi privilegiare leggibilità e riduzione di boilerplate.
### Quando NON usare gli Stream
Ci sono casi in cui gli Stream diventano innaturali o inefficienti:
1. **Algoritmi con stato complesso o mutabile**
    - Es: algoritmi che richiedono aggiornamento continuo di variabili locali.
    - Gli Stream non gestiscono bene logiche iterative con dipendenze forti tra step.
2. **Algoritmi con logica di controllo complicata**
    - Es: backtracking, recursion, ricerca con interruzioni condizionali non banali.
    - In questi casi un ciclo `for` o `while` è molto più chiaro.
3. **Collezioni piccole**
    - L’overhead di costruzione dello Stream (e ancora di più di un `parallelStream`) può superare i benefici.
    - Per 100 elementi o meno spesso un semplice ciclo è più veloce.
4. **Debug e leggibilità**
    - Uno stream con 5–6 operazioni concatenate può diventare più difficile da leggere e fare debug rispetto a un ciclo esplicito.
5. **Operazioni con side effects**
   Gli Stream dovrebbero essere immutabili e senza side effects. Se modifichi variabili esterne, il codice diventa difficile da debugare.
   ```java
	List<String> list = Arrays.asList("a", "b", "c");
	int cout = 0;
	list.stream().forEach(s -> { count++; }); // Side effect! Non threadSafe
	```
6. **Interazioni con `break` o `return` prematuro**
7. **Performance Critiche**
   Gli Stream hanno un overhead, comincia ad essere lento per operazioni molto semplici


>[!important] Regola pratica
>
>- Se il problema è una **trasformazione funzionale su dati indipendenti** → **Stream**.
>    
>- Se richiede **controllo imperativo, stato mutabile o logiche complesse** → **ciclo tradizionale**.



>[!success] Adatto agli Stream
>Trovare i nomi con più di 3 lettere e trasformarli in maiuscolo:
>```java
>List<String> result = lista.stream()
>						    .filter(s -> s.length() > 3)
>						    .map(String:toUpperCase)
>						    .collect(Collectors.toList())
>```

>[!failure] Non adatto agli Stream
>Generare la squenza di Fibonacci:
>```java
>int a = 0, b = 1;
>for (int i = 0; i < 10; i++) {
>	int next = a + b;
>	a = b;
>	b = next;
>	System.out.println(next);
>}
>```
>Qui la logica dipende fortemente da **variabili mutabili** 
>Forzare questo problema in uno stream renderebbe il codice più complicato e meno leggibile

### Esempi

### ✅ Adatto agli Stream

Trovare i nomi con più di 3 lettere e trasformarli in maiuscolo:

`List<String> result = lista.stream()                            .filter(s -> s.length() > 3)                            .map(String::toUpperCase)                            .collect(Collectors.toList());`

- Qui la logica è **dichiarativa**, senza stato mutabile.
    
- Lo stream descrive bene la pipeline: _filtro → trasformazione → raccolta_.
    

---

### ⚠️ Non adatto agli Stream

Generare la sequenza di Fibonacci:

`int a = 0, b = 1; for (int i = 0; i < 10; i++) {     int next = a + b;     a = b;     b = next;     System.out.println(next); }`

- Qui la logica dipende fortemente da **variabili mutabili (`a`, `b`)**.
    
- Forzare questo problema in uno Stream renderebbe il codice più complicato e meno leggibile.
    

---

## 🚀 Conclusione

- Gli Stream sono potenti e concisi, ma hanno senso **solo quando il problema è naturalmente modellabile come pipeline di trasformazioni indipendenti**.
    
- Se invece il problema è più algoritmico, con logiche iterative e stato mutabile, è meglio restare con lo stile imperativo classico (`for`, `while`, recursion).