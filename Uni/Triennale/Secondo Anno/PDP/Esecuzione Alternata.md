Abbiamo analizzato diversi paradigmi, ovvero quattro diversi modi di organizzare il codice per ottenere diverse caratteristiche funzionali nella risoluzione di un problema.
La concorrenza ci permette di sfruttare meglio le risorse.
La distribuzione ci permette di espanderci oltre il singolo nodo di calcolo.
La reattività ci permette di minimizzare la latenza di risposta, nel momento in cui vogliamo superare i limiti delle soluzioni tradizionali a concorrenza e distribuzione.
Il modello ad attori che ci permette di astrarre oltre l'asincronia e la distribuzione.

In tutti questi paradigmi abbiamo sempre lavorato in Java, la JVM applica le strategie legate alla modalità JIT (Just In Time) per bilanciare l'efficienza di esecuzione.
Questo però ha un costo in termini di tempo di avvio del programma.
Quando Java e la JVM sono stati inventati, questa scelta era vantaggiosa: la performance, a regime, raggiungeva livelli da accettabili ad ottimi, e il codice era facilmente portabile.
Oggi le cose sono cambiate.

## ServerLess
I primi siti web "dinamici" funzionavano nel modo seguente
![[Pasted image 20250912103301.png]]
Il programma era dunque un eseguibile che, prendendo un standard input, emetteva un HTML come output.
Questa modalità di funzionamento venne formalizata nello standard: Common Gateway Interface.
Molto presto diventa evidente la penalità di performance dovuta all'avvio di un processo esterno al web server.
Nasce l'application server, per mantenere sempre pronte alla risposta delle applicazioni.
Java e la JVM quindi nascono in una situazione in cui è normale per un server rimanere in esecuzione, in attesa di richieste.
L'efficienza di persegue gestendo più applicazioni sulla stessa macchine, che ne condividono le risorse.
Dopo vent'anni, la situazione cambia con aws lambda.
Oggi, un servizio cosiddetto _serveless_ permette di acquisire le risorse necessarie all'esecuzione di una sola richiesta, pagando unicamente i millisecondi usati per costruire la risposta.
Il tempo di startup e la dimensione di un application server, modalità tipiche di erogazione di molti tipi di applicazioni, diventano uno svantaggio.
Un server non è più sempre in attesa di richieste: esiste per il tempo strettamente necessario ad erogare una sola risposta.
Per la JVM è un problema particolarmente pressante: sebbene le prestazioni a regime siano di ottimo livello, lo diventano solo dopo diversi secondi di attività.
La strategia della JIT si trova in direzione opposta di dove sta andando la tecnologia, per questo nasce il progetto GraalVM nasce come progetto di riscrittura del compilatore Java in Java stesso, per poi focalizzare sulla sola pare di frontend del compilatore.
In seguito a questo cambio di rotta, diventa un sistema completo che include:
- un nuovo compilatore JIT
- un compilatore ahead-of-time per Java
- una specifica di codice intermedio ("Truffle") che può essere utilizzata anche da altri linguaggi
- un back-end che usa il sistema LLVM ed un runtime minimale per ottenere un eseguibile nativo da una rappresentazione Truffle.
- Strumentazione agnostica rispetto al linguaggio per il debug e l'ottimizzazione

GraalVM è in grado di compilare un qualsiasi bytecode da un linguaggio sulla JVM in un eseguibile nativo. Attraverso Truffle, può supportare anche altri linguaggi.
La compilazione ahead-of-time, in un linguaggio come Java, ha sicuramente i suoi problemi: in particolare, il sistema di Classloading e le feature di reflection devono essre limitate o descritte in appositi file di configurazione per rendere prevedibile il codice disponibile al runtime.
Quello che si ottiene però è una sostenuta riduzione della dimensione dell'eseguibile finale (che comprende anche le parti necessarie della libreria standard) ma soprattutto del tempo di avvio.

## Java To Container
Attraverso un framework che supporta GraalVM, possiamo adattare un nostro precedente esempio per osservare come ottenerne un eseguibile nativo già impacchettato in un container.
Scegliamo per questo esempio il framework [Micronaut](https://micronaut.io/).
Rispetto a VertX, Micronaut è un framework abbastanza diverso: è maggiormente _opinionated_ e propone una impostazione precisa di progetto, scegliendo all'interno dell'insieme delle tecnologie supportate le implementazioni delle funzioni necessarie.
In realtà questo approccio è dettato dalla necessità di selezionare ed integrare specificamente quei comportamenti che sono utilizzabili con GraalVM e compatibili con il processo di compilazione ahead-of-time.
Questa selezione è particolarmente evidente nel modo consigliato di preparazione di un progetto: un'apposita area del sito permette di generare un progetto d'esempio scegliendo i parametri tecnologici di cui si ha bisogno.
Riusiamo il progetto TicTacToe.

## Coordinated Restore At Checkpoint
Possiamo confrontare pro e contro degli approcci AOT e JIT secondo le seguenti caratteristiche:
![[Pasted image 20250912115105.png]]
Ciascuno dei due approcci ha pro e contro.
C'è tuttavia spazio per cercare una soluzione che possa coniugare il breve tempo di avvio con alte prestazioni a regime e alta compatibilità.
Tale soluzione nasce da uno dei concetti che abilita l'approccio AOT: la separazione fra _inizializzazione_ ed _esecuzione_ vera e propria.
Questa soluzione si chiama **Coordinated Restore At Checkpoint** è un progetto di OpenJdk.
Il suo obbiettivo è fornire una API per "congelare" lo stato di una JVM e poterlo riavviare, in un altro momento, in un'altra macchina.
L'approccio dunque consiste nel salvare, in un momento preciso, ben definito ed esplicito, lo stato della memoria della JVM.
Con una API apposita il codice può essere informato della pausa e del riavvio, ed adottare le misure necessarie. Il progetto si occupa della gestione delle risorse esterne.
![[Pasted image 20250912115839.png]]
Quello che fa è dunque eseguire una volta tutto quello che c'è da fare nello startup time, poi si salva uno snapshot, e poi, dato che comunque ci deve essere un minimo di tempo di avvio, ricominciare a eseguire a regime.
Nel modo proposto, quindi, si è in grado di eliminare il tempo di startup (esattamente come AOT) mantenendo però le stesse prestazioni a regime perché abbiamo mantenuto la JVM intatta.
Quindi possiamo riscrivere la tabella di prima come:
![[Pasted image 20250912120631.png]]

Rimangono tuttavia alcuni ostacoli importanti all'adozione di questi metodi:
- I problemi di compatibilità non sono completamente eliminati: è necessario adattare il codice che accede alle risorse di sistema perché sia compatibile con la pausa ed il riavvio.
- Sono necessarie alcune funzioni di sistema operativo per avere una funzionalità completa
- Gli artefatti prodotti sono più grandi del caso normale, in quanto contengono anche l'immagine dello stato della JVM salvato
- Il compilatore AOT GraalVM è sempre più performante, riducendo sempre di più le differenze di prestazioni a regime
- Altri progetti stanno affrontando lo stesso problema con soluzioni più semplici da usare come il progetto _Leyden_.

### Progetto Leyden
Il progetto Leyden si appoggia alle tecniche sviluppate da GraalVM per la Ahead-of-Time compilation per portare sull'esecuzione JIT i benefici di avvio più rapido dovuti alla fase di analisi del codice tipica dell'AOT.
L'approccio prevede di raccogliere dati, attraverso dei _training runs_, sulla struttura dell'applicazione durante l'avvio, per salvare tale struttura già inizializzata in una _cache_ pronta al caricamento al momento dell'esecuzione vera e propria.
Il progetto è guardato con grande attenzione perché più semplice e meno invasivo di CRaC, ed egualmente efficace.