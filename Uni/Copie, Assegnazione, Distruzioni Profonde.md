## Perché?
La copia profonda è la soluzione al [[Problema dell'interferenza]].

>[!def] Regola del 3
>Quando in una classe vi sono puntatori, generalmente è un'ottima idea creare:
>- [[#Assegnazione Profonda]]
>- [[#Copia Profonda]]
>- [[#Distruzione Profonda]]

Vogliamo che l'assegnazione di `bolletta` effettui copie profonde (ovvero tutta la lista puntata da `first`).
Per ciò aggiungiamo a `bolletta` due metodi [statici](Static) di utilità di utilità che quindi dichiariamo [privati](Classi#Private):
- `copia()`
- `distruggi()`
```cpp title:bolletta.h
class bolletta {
...
private:
	static nodo* copia(nodo*);
	static void distruggi(nodo*);
...
};
```
```cpp title:bolletta.cpp
bolletta::nodo* bolletta::copia(nodo* p) {
if (!p) return 0;
return new nodo(p->info, copia(p->next));
}

void bolletta::distruggi(nodo* p) {
	if (p) {
		distruggi(p->next);
		delete p;
	}
}
```
## Assegnazione Profonda

Usando `copia` e `distruggi` possiamo provare a fare un tentativo di definizione di `operator=()`:
>[!failure] Memory Leak
>```cpp
>bolletta& bolletta::operator=(const bolletta& b) {
>	first = copia(b.first);
>	return *this;
>}
>```
>Questo codice ha 2 grossi bug:
>1. Memory leak: ovvero la lista precedentemente putata da `first` non viene deallocata
>2. Se si esegue una assegnazione tipo `x = x` viene effettuata inutilmente la copia della lista di elementi.

>[!success] Ok, Desgin Pattern Assegnazione Profonda
>```cpp
>bolletta& bolletta::operator=(const bolletta& b) {
>	if(this  != b) { // operator != tra puntatori
>		distruggi(first);
>		first = copia(b.first);
>	}
>	return *this
>}
>```
>
## Copia Profonda
Per la copia profonda non c'è molto da dire: basta che una volta invocato il [costruttore di copia](Costruttori#^eb6713) nella sua [lista di inizializzazione](Costruttori#Liste di Inizializzazione) quando costruisco first invoco `copia`

```cpp title:bolletta.cpp
bolletta::bolletta(const bolletta& b): first(copia(b.first)) {}
```


## Distruzione Profonda
La *distruzione profonda* è analoga alla copia profonda in quando logica. Fa uso del [distruttore](Distruttore) per eliminare gli oggetti puntati dal puntatore.
In questo caso invoco `distruggi` su first
```cpp title:bolletta.cpp
bolletta::~bolletta() {
	distruggi(first);
}
```