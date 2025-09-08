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