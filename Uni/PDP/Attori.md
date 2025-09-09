Il paragirma [reattivo](Reattività) ci ha portato ad esaminare un modello di esecuzione particolare, con lo scopo di ottenere benefici specifici per un insieme di problemi con esigenze specifiche, anche se sempre più comuni.
L'implementazione esaminata, RxJava, non è tuttavia completamente conforme al _Reactive Manifesto_ perché non è basata sullo scambio di messaggi.
Gli autori del manifesto avevano in mente un modello molto precedente, molto peculiare, nato per requisiti ancora più specifici, ma che si è rivelato estremamente utile negli ambiti in cui il paradigma reattivo si muoveva.
Andiamo quindi ad esaminare un altro paradigma con la caratteristica però di essere in grado di fornire un'ottima implementazione del modello _Reactive_ e di soddisfare i requisiti del _Reactive Manifesto_.
Oggi il modello degli attori significa concorrenza e distribuzione ==su larga scala, e affidabilità di lunga durata==.
## Caratteristiche
>[!def] Attore
>Un Attore è una ==unità indipendente di elaborazione==, dotata di uno stato privato, inaccessibile dall'esterno.
>L'attore comunica unicamente con messaggi diretti ad altri attori di cui conosce il nome.

Un attore, in un determinato momento, ha un Comportamento che definisce la sua reazione ad un determinato messaggio.
Nel reagire ad un messaggio, un attore può:
- mutare il proprio stato interno
- creare nuovi attori
- inviare messaggi ad attori noti
- cambiare il suo comportamento

**All'interno dell'attore non c'è concorrenza:** l'esecuzione è strettamente sequenziale. 
Ovviamente è preferibile che la risposta sia il più veloce possibile.

Al di fuori dell'attore tutto è concorrente e distribuito:
- Un altro attore può trovarsi dovunque
- ogni attore è concorrente a tutti gli altri
- ogni messaggio è concorrente a tutti gli altri

Il fallimento di un attore è una eventualità assolutamente normale, che non ha alcuna conseguenza sulla stabilità del programma in sè.

Un attore può supervisionare gli attori che ha creato, ed essere notificato del loro fallimento.
La notifica è un messaggio, per il quale il supervisore deve predisporre un comportamento.
Le conseguenze di questo approccio sul modello di esecuzione sono profonde; il risultato è un sistema dove:
- L'avvio di nuovi processi/attori è estremamente economico
- L'affidabilità è molto elevata
- Le performance e l'efficienza sono molto alte
- La scalabilità è lineare o quasi, e molto stabile
- La distribuzione e la concorrenza sono caratteristiche primarie.

>[!important]
>L'elaborazione di grandi moli di dati asincroni, spesso modellati come eventi, è il tipo di problema in cui questo paradigma si esprime al meglio, abilitando risultati molto difficili da raggiungere in modo differente.

## Problematiche
Ovviamente, le caratteristiche del modello ad Attori hanno un costo. In questo caso, è un costo di natura concettuale.
Modellare un algoritmo come l'interazione di un insieme di attori concorrenti non è sempre naturale. Quando lo è, il risultato è estremamente efficace.
Molte delle euristiche e dei modi di pensare tipici della programmazione tradizionale sono in questo modello totalmente inutili, se non addirittura dannosi.
Il modello mentale in cui il problema va ricondotto è molto differente.
L'interazione non può fare assolutamente nessuna ipotesi nè sull'ordine della ricezione dei messaggi, né sulla loro affidabilità.
Questo costringe a dover considerare attentamente il modello del risultato e le varie possibilità di fallimento.

## Implementazione
