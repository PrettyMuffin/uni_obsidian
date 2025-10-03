## Reactive Extensions
Per cercare astrazioni di livello più alto con cui gestire, in modo coordinato, le problematiche di sistemi concorrenti e distribuiti facciamo un passo indietro, recuperando uno strumento di cui abbiamo già parlato, rileggendolo sotto un'altra luce.
L'astrazione di esecuzione fornita dallo [stream](Stream%Paralleli) che avanza l'esecuzione, e che decide la sua struttura.
Tuttavia rimangono esigenze aperte:
- Manca un protocollo esplicito per gestire la terminazione dello stream
- Gli errori sono gestiti come eccezioni

Quindi abbiamo bisogno di qualcosa che ci permetta di definire come comportarsi quando lo stream finisce e qualcosa che ci dia un'aspetto più omogeneo anche alla gestione degli errori.
Alcune di queste idee diventano le "Reactive Extensions".
>[!def] ReactiveX
>ReactiveX è una libreria per comporre programmi asincroni ed "event-based" usando sequenze osservabili (tramite l'observer pattern).

Dunque le Reactive Extension forniscon una semantica per la definizione di elaborazioni asincrone di sequenze di oggetti.
È molto più di una API: è un intero modello di esecuzione.
Le basi del modello sono i seguenti concetti:
- Observable
- Scheduler
- Subscriber
- Subject

L'idea principale del modello è una implementazione dell'**Observer pattern** _done right_ .
Un `Observable` in RX è un oggetto concettualmente simile ad uno stream, che emette _nel tempo_ una sequenza di valori.
È possibile trasformare, filtrare ed elaborare questi valori in modo esteriormente simile alle analoghe implementazioni su di uno stream.
È possibile _osservare_ i valori emessi da un `Observable` fornendo il comportamento da adottare in caso di:
- valore ricevuto
- eccezione lanciata da un precedente componente
- termine del flusso di dati

![[Pasted image 20250908154619.png]]


Ogni `Observable` presenta questi tre comportamenti:

| Evento        | Successivo         | Observable      |
| ------------- | ------------------ | --------------- |
| sucessivo     | `T next()`         | `onNext(T)`     |
| errore        | lancia `Exception` | `onError(E)`    |
| completamento | `!hasNext()`       | `onCompleted()` |
```java
System.out.println("Defining...");
Observable.range(0, 10000).map(new RxDivisors())
	.filter(new RxPerfectPredicate())
	.subscribe((c)-> {          // onNext(T)
		System.out.println(c);
	}, (t) -> {                 // onError(E)
		t.printStackTrace();
	}, () -> {                  // onCompleted()
		System.out.println("DONE");
	});
System.out.println("Defined");
```

Il risultato è:
- Una semantica più ricca
- Maggiore regolarità nella composizione
- Indipendenza dal modello di esecuzione (sincrono/asincrono)

La maggior parte degli operatori sugli `Observable` accettano uno `Scheduler` come parametro.
Ogni operatore può così essere reso concorrente; lo `Scheduler` scelto permette di indicare il tipo di concorrenza desiderato.

```java title:Scheduler

System.out.println("Defining...");
Observable.range(0, 10000).map(new RxDivisors())
	.filter(new RxPerfectPredicate())
	.subscribeOn(Schedulers.computation()) // computation è uno scheduler adatto per compiti on alto blocking factor
	.subscribe(
		(c) -> { System.out.println(c); }
		(t) -> { t.printStackTrace(t); }
		() -> { System.out.println("Done"); done[0] = true;}
	);
System.out.println("Defined");
while(!done[0]) Thread.sleep(1000);
System.out.println("End");
```

Un `Subscriber` rappresenta un ascoltatore di un `Observable`: fornisce il codice che reagisce agli eventi per ottenere il risultato finale della catena di elaborazione.
Un `Subject` può consumare uno o più `Observable`, per poi comportarsi esso stesso come un `Observable` e quindi introdurre modifiche sostanziale nel flusso degli eventi.

Lo schema concettuale proposto da RX è estremamente utile per:
- costruire stream di elaborazione complessi e asincroni
- fornire un'interfaccia semplice ma facilmente componibile per trattare successioni di eventi nel tempo.
- scrivere algoritmi facili da portare da un linguaggio all'altro.
- strutturare una elaborazione concorrente di uno stream di dati su garanzie solide.
- gestire gli eventi provenienti da una UI con la stessa semplicità di dati provenienti da un file, da una sequenza di dati, o altra sorgente.

#### Reactive Manifesto
Le Reactive Extensions avviano presto un nuovo filone di ricerca per studiare come il modello da loro proposto di adatta alle necessità emergenti delle applicazioni di elaborazione di stream di dati, che nella prima metà degli anni 2010 sono ormai comuni.
Per dare una definizione precisa a questo nuovo tipo di applicazioni, e stabilire un concetto che potesse catalizzare il discorso della ricerca in una direzione ben precisa, alcuni autori di tecnologie del campo definiscono e pubblicano il **Reactive Manifesto**.
Il Reactive Manifesto definisce le caratteristiche dei sistemi _reattivi_.
- Pronti  alla risposta (_Responsive_)
- Resilienti (_Resilient_)
- Elastici (_Elastic_)
- Orientati ai messaggi (_Message Driven_)
![[Pasted image 20250908161805.png]]

Un sistema Reattivo deve essere _Pronto alla risposta_ in quanto la bassa latenza di interazione è un principio cardine dell'usabilità.
Un sistema deve quindi privilegiare la possibilità di fornire una risposta sempre ed in un tempo prevedibile e costante. Un errore è una risposta come le altre; ed allo stesso modo deve essere individuato e comunicato con le stesse tempistiche.

Un sistema Reattivo deve essre _Resiliente_ ovvero gestire i fallimenti continuando a rispondere con la stessa prontezza.
La resistenza agli errori si ottiene con la replicazione di componenti isolati, la coscienza che ogni parte del sistema può fallire, e la ripida creazione di nuovi componenti in sostituzione di quelli che sono andati in errore o non sono più disponibili.

Un sistema Reattivo deve essere _Elastico_, cioè in grado di consumare una quantità variabile di risorse in funzione del carico in ingresso.
Il mantenimento della latenza di risposta prevista si ottiene distribuendo il carico su di un maggior numero di risorse nel modo più lineare possibile, senza colli di bottiglia o punti di conflitto, suddividendo gli input in _shard_ distribuite automaticamente.
L'obbiettivo è una scalabilità efficace ed economica su hardware non specializzato.

Un sistema Reattivo deve essere _Orientato ai messaggi_ perché questa primitiva di comunicazione abilita le altre caratteristiche. 
Attraverso lo scambio di messaggi i componenti possono rimanere disaccoppiati, si possono indirizzare anche su nodi distribuiti, ed il carico può essere suddiviso fra copie in esecuzione su nodi differenti.
L'asincronia della comunicazione e l'assenza di blocchi permettono di consumare al meglio le risorse disponibili.

Queste caratteristiche impongono una organizzazione architetturale ben precisa: il _Manifesto_ intende dirigere lo sviluppo tecnologico in una precisa direzione vista come quella più adatta a supportare sistemi con le caratteristiche desiderate.

>[!important] Concludendo
==L'_Orientamento ai messaggi_ è il mezzo con cui il sistema di struttura in forma _Elastica_ e _Resiliente_ per ottenere il valore della _Prontezza della risposta_==

Sottoprodotti di questa architettura sono componenti manutenibili e facili da estendere, per inseguire il rapido cambiamento dei requisiti.

### Big Vs Fast
Negli stessi anni in cui aumentava l'interesse per ReactiveX come piattaforma, il problema del _Big Data_ diventa sempre più importante e mutava in qualcosa più difficile da trattare.
La soluzione impiegata fino ad allora, cioè l'esecuzione di _batch_ massivamente paralleli, non poteva più funzionare in quanto introduceva latenze superiori al periodo di utilità delle informazioni estratte.
Qualche autore comincia a chiamare il problema _Fast Data_: dati di dimensioni paragonabili al _Big Data_, in arrivo continuo; impensabile e/o inutile persisterli per elaborarli a partire dal supporto di salvataggio.
Per il loro volume, per la velocità di arrivo, e la bassa latenza richiesta nel reagire alla informazioni estratte, è necessario trattarli in diretta via via che si presentano.
Nel costruire un sistema massicciamente parallelo per estrarre informazioni da un flusso di dati continuo, uno dei principali problemi è l'armonizzazione delle varie differenze di velocità di elaborazione fra i vari componenti.
Il componente più lento diventa il collo di bottiglia e stabilità la velocità massima dell'elaborazione, per poi fallire soverchiato dai dati che arrivano troppo velocemente.
Da questo problema nasce la necessità di aggiungere alla semantica delle Reactive Extension, per esempio per capire qual è il componente più lento, che appaiono particolarmente adatte a questo tipo di problema, un nuovo concetto: **back-pressure**.
>[!def] Back-Pressure
>Resistenza che il componente successivo può opporre ai dati provenienti dal componente precedente della catena di elaborazione.
>Questo concetto permette ad ogni nodo della catena di dichiarare quanti dati è in grado di gestire.

## Reactive Streams

>[!obj] Obbiettivo
>L'obiettivo principale di Reactive Streams è quello di governare lo scambio di dati in streaming attraverso un confine asincrono, garantendo al contempo che il lato ricevente non sia costretto a bufferizzare quantità arbitrarie di dati.

Per raggiungere il suo obbiettivo Reactive Streams aumenta le interfacce e le garanzie fornite da ReactiveX introducendo l'esplicita gestione della _back-pressure_ per impedire che un nodo possa essere soverchiato dai dati inviati dal nodo precedente, o sovraccaricare quello successivo.
Inoltre viene inclusa nella considerazione dello standard anche la casistica in cui i diversi componenti di uno Stream non si trovino nello stesso nodo, ma siano distribuiti.
Sono comprese sia le API asincrone, sia i protocolli di comunicazione.
In questo modo, la mission di Reactive Streams comprende non solo le interfacce applicative, ma anche i protocolli di rete, la _back-pressure_ infatti è necessaria non solo fra thread, ma anche fra nodi di calcolo distribuito.
Il modello concettuale di Reactive Streams è compatto tanto quanto quello di ReactiveX:
- Publisher
- Subscriber
- Subscription
- Processor

### Publisher
Fornisce un numero potenzialmente infinito di elementi in sequenza, rispettando le richieste dei suoi `Subscriber`
```java
public interface Publisher< T > {
	public void subscribe(Subscriber< ? super T > s);
}
```

### Subscriber
Un `Subscriber` consuma gli elementi forniti da un `Publisher` ed è in grado di controllare il flusso degli elementi in arrivo.
```java
public interface Subscriber< T > {
	public void onSubscribe(Subscription s);
	public void onNext(T t);
	public void onError(Throwable t);
	public void onComplete();
}
```

### Subscription
Una `Subscription` rappresenta il legame fra un `Subscriber` e un `Publisher` e permette di controllarlo, per esempio interrompendolo
```java
public interface Subscription {
	public void request(long n);
	public void cancel();
}
```

### Processor
Un `Processor` è sia un `Subscriber` che un `Publisher`, e deve sottostare ad entrambi i contratti.
Rappresenta uno snodo di elaborazione intermedio in grado di alterare il flusso di elementi aggregando più `Publisher` o controllando più `Subscriber`
```java
public interface Processor< T, R > extends Subscriber< T >, Publisher< R > {}
```

## Operatori
>[!info] N.B.
>Faremo riferimento alla documentazione delle reactive extensions perché gli operatori di base sono gli stessi; a seconda della classe usata (`Observable` oppure `Publisher`) si comportano in modo appropriato

### Map
Trasforma gli elementi di uno stream, ottenendo uno stream di elementi trasformati
![[Pasted image 20250909152855.png]]

### FlatMap
Trasforma gli elementi di uno stream, concatenando gli elementi risultanti in un solo stream
![[Pasted image 20250909152950.png]]
Questa immagine mostra che non abbiamo garanzia sul fatto che tutti gli stream vengano messi in ordine, se infatti facciamo `flatMap` in parallelo  non abbiamo garanzie sul risultato.

### Filter
Emette uno stream contenente solo gli elementi che soddisfano un predicato.
![[Pasted image 20250909153157.png]]

### Skip
Emette uno stream saltando i primi _N_ elementi della sorgente
![[Pasted image 20250909153232.png]]
### Zip
Emette uno stream combinando a coppie gli elementi di due stream in ingresso
![[Pasted image 20250909153328.png]]
### Debounce
Emette un elemento solo se è passato un lasso di tempo dall'ultimo elemento della sorgente
![[Pasted image 20250909153421.png]]
Questo operatore evidenzia il fatto che questi stream sono considerati nel tempo.
### Window
Emette uno stream di partizioni dello stream sorgente
![[Pasted image 20250909153551.png]]
Si sceglie un criterio per il partizionamento, nella foto si sceglie di partizionare per gruppi di 3.
## Parallelismo
L'asincronia nell'esecuzione dei vari operatori è definita dallo Schedulatore usato per osservare un `Observable` o definire un operatore di uno stream.
Ciascuna implementazione fornisce degli schedulatori in relazione alla piattaforma in cui opera.

| Metodo           | Schedulatore                            |
| ---------------- | --------------------------------------- |
| `.io()`          | Per stream legati alle operazioni di IO |
| `.single()`      | Usa un singolo therad                   |
| `.computation()` | Per operatori legati al calcolo         |
| `.from(ex)`      | Usa l'[[Executors]] fornito             |

In RxJava, l'operatore `parallel()` permette di indicare che uno stream, da un certo punto in poi, va costruito come parallelo.
In questa modalità solo alcuni operatori sono consentiti (per questioni di semantica) ed è necessario specificare lo schedulatore da usare con il metodo `.runOn(scheduler)`.
Al contrario invece `sequential()` indica che da quel punto in poi la pipeline di elaborazione va nuovamente intesa come sequenziale.
A differenza degli [stream](Stream%20Paralleli) della libreria di base, è possibile indicare una precisa sezione della pipeline che viene configurata parallelamente.

```java
System.out.println("Defining...");
Flowable.range(0, 1000000).parallel(4)
	.runOn(Schedulers.computation()).map(new RxDivisors())
	.filter(new RxPerfectPredicate())
	.sequential()
	.subscribe(
		(c) -> { System.out.println(c); },
		(t) -> { t.printStackTrace(); },
		() -> { System.out.println("Done"); done[0] = true; }
	);

System.out.println("Defined");
while(!done[0]) Thread.sleep(1000);
```

## RX vs [Virtual Threads](Thread#Virtual%20Thread)
Le Reactive Extensions, nelle loro varie forme, ci hanno portato a livelli di espressività e performance che non si pensavano raggiungibili con i paradigmi tradizionali.
Ma sono la soluzione definitiva? Come si comportano con i Virtual Threads?
I [virtual threads](Thread#Virtual%20Thread) nascono come contrapposizione diretta al paradigma reattivo, in particolare alla necessità di riscrittura del codice per ottenere le elevate prestazioni.
Quelle stesse prestazioni sono ora alla portata di codice scritto tradizionalmente.
Quindi quello che fanno i virtual threads è alzare l'asticella da superare per passare alla programmazione reattiva.
