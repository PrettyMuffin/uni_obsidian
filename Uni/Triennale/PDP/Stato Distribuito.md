Abbiamo giustificato la necessità di realizzare un sistema distribuito con la ricerca di:
- affidabilità
- suddivisione del carico
- distribuzione dei risultati

Le stesse caratteristiche possono essere desiderabili per un insieme di dati:
- disponibili continuamente in presenza di guasti
- quantità superiore a quella gestibile da una sola macchina
- accessibilità da parte di più posizioni geografiche

Un insieme di dati gestito da un sistema distribuito è disponibile, accessibile e coerente solo nella misure in cui i singoli nodi riescono a rimanere allineati, coordinati e concordi fra loro.
La distribuzione dello stato è effettivamente implementata attraverso la suddivisione gerarchica dello stato stesso, senza condivisione fra i nodi.

## Consenso
Se il requisito principale è la resistenza ai fallimenti o le prestazioni particolarmente elevate (in spazio o velocità) non abbiamo altra scelta che replicare lo stato in più di un nodo, in modo da avere che:
- Un guasto non porti a perdita di dati
- aggiungendo nodi aumenti la capacità di risposta del sistema

Ma vendo più nodi che contengono lo stesso dato, nasce il problema del **consenso**, ovvero di garantire che tutti i nodi del sistema contengano la stessa versione di un dato.

Per risolvere questo problema serve scrivere un algoritmo del consenso.
Il problema è stato denominato da Leslie Lamport come il problema dei _Generali Bizantini_ :
>[!quote] Problema dei Generali Bizantini
>Diversi generali di un esercito Bizantino assediano una città. Devono raggiungere un consenso unanime su di una decisione: attaccare o ritirarsi.
>Un attacco parziale è molto più svantaggioso di un attacco o di una ritirata completa.
>I generali sono in accampamenti diversi e possono comunicare solo scambiandosi messaggi l'uno con l'altro.
>Tuttavia, non ci sono garanzie sulla loro lealtà: alcuni generali possono tradire, inviando messaggi contraddittori o non rispondendo a messaggi ricevuti.

Il problema modella il caso in cui un nodo di un sistema distribuito si comporta in modo diverso ad ogni risposta.
Di fatto, non è conoscibile dall'esterno se il suo comportamento è corretto o se il nodo è guasto.
Lo strumento per affrontare la soluzione di tale problema è un _algoritmo di consenso_.
Cioè un metodo che gestisce i nodi e la loro comunicazione e che da delle garanzie rispetto al fatto che indipendentemente dalla condizione dei nodi si può raggiungere una decisione unanime. 
### Paxos
L'algoritmo _PAXOS_ è basato su di un protocollo a tre fasi che garantisce l'assenza di blocchi nel caso di un guasto singolo.
I possibili comportamenti dei nodi sono modellati con delle macchine a stati per garantire la copertura di tutti i casi.
Nell'algoritmo PAXOS i nodi assumono dei precisi ruoli:
- leader:
- votanti:
- ascoltatori:
- proponente:
- client:

Il Proponente inoltra la richiesta del Client ai Votanti per raggiungere un Quorum. Una volta che un sufficiente numero di votanti appartiene ad un Quorum, la richiesta è stata accolta.
Gli Ascoltatori, ricevendo messaggi dai Votanti, forniscono ridondanza addizionale in caso di guasti.
Il Leader coordina il protocollo ed è in grado di fermarlo in caso di eccessivi fallimenti in attesa che possa essere ripresa la normale attività.
Una variante dell'algoritmo è in grado di fornire garanzie di correttezza anche in presenza di Guasti Bizantini: ovvero di nodi che si comportano in maniera versata, ovvero in maniera diversa con ogni nodi.
### Raft
Raft è un algoritmo di consenso scritto con l'obbiettivo di essere più comprensibile di PAXOS.
Raft suddivide il problema del consenso in sottoproblemi per rendere il modello più semplice e l'implementazione più agevole.
Il concetto principale è la replicazione di una macchina a stati.
Il ruolo del leader e la sua elezione sono concetti più enfatizzati che in PAXOS, e garantiscono la correttezza dell'algoritmo.

### CAP Theorem
Tramite il consenso, possiamo ottenere che il sistema distribuito abbia uno stato coerente, e che quindi ogni nodo possa rispondere allo stesso modo alla richiesta di un dato.
>[!question] Ma quali sono i limiti ed i compromessi che dobbiamo prevedere di dover fare in caso di guasti?
>>[!def] CAP Theorem
>>Il teorema **CAP** definisce tre caratteristiche di un database distribuito:
>>- Consistenza: Ogni lettura riceve o il valore più recente o un errore.
>>- Disponibilità (_Availability_): Ogni richiesta riceve una risposta valida (ma non necessariamente l'ultimo valore).
>>- Tolleranza alla separazione (_Partition Tolerance_): Il sistema funziona anche se la rete fallisce per un insieme di nodi, cioè viene partizionata.
>>
>>Però solo _due_ delle proprietà CAP possono essere garantite contemporaneamente.
>>Siccome ogni rete può fallire, in pratica il teorema afferma che: _in caso di partizione di rete un sistema può essere o consistente o disponibile, ma non entrambe le cose_.
>
>Vale a dire che un database distribuito può essere costruito in due modi; in caso di partizione di rete
>- La consistenza viene garantita ma alcune richieste non possono essere soddisfatte e vanno in errore.
>- Tutte le richieste ritornano un valore, ma alcune potrebbero non ritornare l'ultimo valore non scritto.

#### PACELC
Una estensione del teorema CAP. 
Considera anche il caso dell'operatività normale:
- In caso di (P) Partizione, si deve scegliere fra (A) disponibilità e (C) consistenza
- (E) altrimenti (ovvero in condizione normale) , fra (L) latenza e (C) consistenza

La maggior parte dei sistemi NoSQL si orienta verso PA/EL.
I database relazionali tipicamente sono invece PC/EC.
### CRDT
Finora abbiamo lavorato nell'ipotesi che il sistema distribuito conservi dati che vengono scritti da una sola fonte o che solo occasionalmente arrivino da più di una direzione.
In alcuni sistemi distribuiti invece è normale che ogni nodo abbia nuove informazioni da fornire e che queste vadano riconciliate e riunite con quelle prodotte indipendentemente da un altro nodo:
- sistemi di telepresenza/chat
- editor collaborativi
- eventi in real-time con molti partecipanti

In questi sistemi l'esigenza non è quella di raggiungere un consenso su di un dato.
Al contrario, ogni nodo produce nuove versioni dello stesso dato che ogni altro nodo deve riunire a quelle prodotte localmente.
Ci sono due principali soluzioni a questo problema:
- Operational Transformation
- Conflict-Free Replicated Data-Type
#### Operational Transformation
L'approccio OT è il seguente:
- Ogni nodo produce delle modifiche al documento in corso di elaborazione
- Le modifiche sono propagate agli altri nodi
- Ogni nodo trasforma le sue modifiche ricevute in modo da applicarle al suo stato del documento

Sebbene ci siano state implementazioni di successo, le OT non hanno preso piede: l'approccio sembra mancare di generalità e si rivela molto complesso da implementare.
#### Conflict-Free Replicated Data-Type
È una soluzione più recente, queste sono un insieme di strutture dati, quindi un insieme di oggetti che si possono implementare e replicare.
E all'interno del singolo caso d'uso, se si riesce a mappare un singolo caso d'uso, a una di queste strutture dati abbiamo la garanzia che il sistema funziona.
Ovvero accogliere le modifiche dagli altri nodi a quella struttura dati e attraverso queste modifiche è possibile collaborare con gli altri nodi.
Una CRDT può essere replicata su più nodi di un sistema, modificata indipendentemente, ma fornisce la garanzia che esiste un modo di riconciliare tutte le possibili modifiche risolvendo ogni possibile conflitto.
>[!example] Esempio CRDT
>Un flag booleano che possa solo passare da falso a vero è una banale CRDT.
>Anche se più nodi effettuano la modifica, il risultato può essre tranquillamente replicato senza che ci siano problemi a riunire le differenti versioni.

Diverse strutture dati con queste caratteristiche sono note ed implementate:
- Grow-only Counter
- Positive-Negative Counter
- Grow-only Set
- 2-Phase Set
- Last-Write-Wins Set

