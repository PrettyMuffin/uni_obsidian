Tags: [[Essenziali]] [[P.A.O]]
## Definizione
Come per i [[Costruttori]] anche per i **distruttori** è previsto un *distruttore standard* per ogni classe.

>[!warning] Distruttore su oggetti con puntatori
>Se l'oggetto `x` include dei campi dati puntatore allora il distruttore standard si limita solo a rilasciare la memoria occupata dai campi dati puntatore, **ma non dealloca la memoria a cui puntano i campi dati puntatore** di `x` e questo comportamento del distruttore standard non è corretto.
>Per risolvere questo problema si fa uso della [distruzione profonda](Copie,%20Assegnazione,%20Distruzioni%20Profonde#Distruzione%20Profonda).
###### Sintassi
Il distruttore è un metodo senza parametri e senza tipo di ritorno.
Identificato da:
```cpp
class bolletta {
public:
~bolletta();
...
};
```

---

Il distruttore di oggetti viene invocato automaticamente al termine del tempo di vita di un oggetto.
In particolare:
1. Sulle variabili locali di una funzione al termine dell'esecuzione della funzione;
2. Sui parametri di una funzione passati per valore al termine dell'esecuzione della funzione;
3. Sull'oggetto anonimo ritornato come risultato di una funzione non appena esso sia usato;
>[!info] Distruzione di una funzione al momento del ritorno
>1. Vengono distrutte tutte le variabili globali a `F()`;
>2. Viene distrutto l'oggetto anonimo ritornato per valore da `F()` non appena viene usato;
>3. Vengono distrutti i parametri di `F()` passati per valore;

>[!important] Ordine di distruzione
>Un distruttore distrugge i campi dati di un oggetto nell'ordine inverso della costruzione.
>Ovvero se per una funzione $F(T_{1} f_{1}, \dots, T_{n} f_{n})$ distrugge da $f_{n}$ a $f_{1}$.

Quando un oggetto viene distrutto viene invocato il distruttore con il seguente comportamento:
1. Viene eseguito il corpo del distruttore;
2. Vengono chiamati di distruttori dei campi dati nell'ordine inverso alla loro [lista di inizializzazione](Costruttori#Liste di Inizializzazione), per un campo dati non classe.