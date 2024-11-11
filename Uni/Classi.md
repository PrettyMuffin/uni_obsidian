**Tags:** [[P.A.O]] [[Essenziali]]
## Definizione
Una classe si specifica in due "parti" o "fasi":
- _Interfaccia_: Chiamata anche dichiarazione della classe, dove vengono definite le variabili e i metodi appartenenti alla classe, e si fa in un file header `.h`;

```cpp
// orario.h
#infdef ORARIO_H
#define ORARIO_H
class orario {
public:
	int Ore();
	int Minuti();
	int Secondi();
private:
	int sec;
};
#endif
``` 

- _Definizione_: Dove viene scritta la logica dei metodi, si fa in un file `.cpp`
```cpp
  // orario.cpp
  #include "orario.h"
  int orario::Ore() { return sec / 360; }
  
  int orario::Minuti() { return (sec / 60) % 60; }
  int orario::Secondi() { return sec % 60; }
```
Per definire i metodi utilizziamo l'operatore di [scoping](Namespace#^11f9a8), questo perché quando si definisce una classe si definisce anche un [[Namespace]] proprio di quella classe;

Dunque ora è possibile utilizzare la classe `orario` all'interno del programma
```cpp
#include "orario.h"
#include <iostream>

using std::cout;
using std::endl;

void main() {
	orario mezzanotte;
	cout << mezzanotte.Secondi() << endl;
}
``` 
---
## Puntatore this
Quando viene dichiarato un oggetto come `mezzanottte` di tipo `orario` viene riservata una zona di memoria per il valore del campo dati intero `sec`.
==Dunque ogni oggetto `orario` un proprio campo dati `sec`==.


>[!note] N.B.
In memoria un'unica copia del codice (oggetto) dei metodi:
>- `Secondi()`
>- `Minuti()`
>- `Ore()`

Ed è tal codice che viene eseguito quando vengono fatte le chiamate:
```cpp
mezzanotte.Secondi()
mezzanotte.Minuti()
mezzanotte.Ore()
```

>[!def]- Oggetto di invocazione #Definizione 
>L'oggetto di invocazione sarebbe la variabile dell'oggetto che effettua la chiamata al metodo della classe.
>All'interno del metodo viene definito tramite un parametro implicito `*this`, che appunto fa riferimento all'oggetto che ha invocato tale metodo, in quanto in memoria i metodi vengono salvati una sola volta e non una volta per oggetto. ^76e1c5

>[!example]
In una invocazione come `mezzanotte.secondi()` diremo che `mezzanotte` è l'[oggetto di invocazione](#^76e1c5)

```cpp 
// orario.cpp
int orario::Secondi() { return sec % 60; }
// esplicitando il parametro this diventerebbe
int orario::Secondi(orario* this) { return this->sec % 60; }

// mentre la chiamata
int s = mezzanotte.Secondi();
// esplicitando il parametro sarebbe
int s = Secondi(&mezzanotte);
```

---
## Public vs Private
Nella precedente definizione di `orario` abbiamo usato le keyword `public` e `private`.
Questi sono degli *specificatori d'accesso* in quanto indicano se i campi dati / metodi sono appartenenti alla parte [pubblica](#Public)  o [privata](#Private) della classe.
### Private
È la parte della classe che non vogliamo che l'utente usi, in quanto serve per l'implementazione dei servizi che mettiamo a disposizione nella parte [pubblica](#Public) della classe.
### Public
È la parte che vogliamo rendere accessibile all'utente della classe.
In questa parte mettiamo a disposizione le funzioni e i servizi che vogliamo dare all'utilizzatore della classe.

>[!example]- Classe
>```cpp
>// complesso.h
>class complesso {
>private:
>	double re, im;
>public:
>	void iniz_compl(double, double);
>	double reale();
>	double immag();
>};
>
>// complesso.cpp
>void complesso::iniz_compl(double r, double i) {
>	re = r;
>	im = i;
>}
>double reale() { return re; }
>
>double immag() { return im; }
>```

>[!question]- E se non si specifica nulla?
>Allora sono di default membri della parte [privata](#Private)


## Costruttori

### Perché?
Prendiamo come esempio orario. Quando creiamo un nuovo oggetto i valori dei campi dati non vengono inizializzati, dunque assumono dei valori di default, chee non necessariamente sono quelli che vogliamo noi.
Tuttavia se proviamo a inizializzarlo:
>[!error] Sec è un campo privato => errore di compilazione
> ```cpp
>	orario mezzanotte;
>	mezzanotte.sec = 0;
> ```
### Definizione
Si devono dunque usare i cosiddetti *costruttori*:
>[!important] Costruttore #Definizione 
>Metodo con lo stesso nome della classe e senza tipo di ritorno che vengono invocati automaticamente quando viene dichiarano, quindi costruito, un oggetto.
>Solitamente stanno nella parte [pubblica](#Public) della classe.

>[!example]- Esempio
> ```cpp 		
>class orario {
>	public:
>		orario(); // costruttore
>	private:
>		int sec;
>};
>
>orario::orario() { sec = 0; }
>
>int main() {
>	orario mezzanotte;
>	std::cout << mezzanotte.Ore() << std::endl;
>}
> ```



Si posso definire più costruttori purché differiscano nella lista dei parametri.
Il costruttore senza parametri viene detto di ==default o standard==.
```cpp 
class orario {
public:
	orario(); // default
	orario(int, int);	// costruttore ore-minuti
	orario(int, int, int); // costruttore ore-minuti-secondi
};
```

Quindi per utilizzare il costruttore si fa così:
```cpp
orario o(); // default
orario o(12, 22);
orario o(12, 45, 22);
```

>[!attention] Disponibilità Costruttore Standard
>Se in una classe non viene dichiarato esplicitamente alcun costruttore, è automaticamente disponibile il costruttore standard.
>Una volta però dichiarato almeno un costruttore, il costruttore standard non è più disponibile

### Tipi di costruttori e oggetti anonimi

Nella seguente assegnazione:
```cpp
orario o;
o = orario(12, 33, 25);
```
il costruttore a tre parametri crea un cosiddetto **oggetto anonimo**:
>[!important] Oggetto Temporaneo-Anonimo #Definizione 
>È un oggetto che non possiede l-valore (indirizzo di memoria), e il suo tempo di vita termina non appena viene usato per assegnarlo alla variabile.

^cd9d7d

Dunque scrivere: `orario o = orario(12, 33, 25)` è come fare `int a = 4` in quanto né il valore litterale `4` né `orario(12, 33, 25)` possiedono l-valore, ma solo r-valore.

>[!important] Costruttore di copia #Definizione 
>L'invocazione del costruttore di copia crea un nuovo oggetto *copia 1* mediante una copia di campo dati per campo dati.
>>[!example]- Esempio
>> ```cpp
>> orario adesso(11, 55);
>> orario copia;
>> copia = adesso;
>> orario copia1 = adesso; // costruttore di copia
>> orario copia2(adesso); // costruttore di copia
>> ```

^cae878

### Costruttori come convertitori di tipo
I costruttori ad **un solo parametro**. Come:
```cpp
orario::orario(int o) {
	if (o < 0 || o > 23) sec = 0;
	else sec = o;
}
```
funzionano anche come **convertitori di tipo**, ovvero vengono invocati per effettuare conversioni implicite del tipo
```cpp
orario x;
x = 8;
```

>[!info]
>La conversione implicita avviene a run-time mediante un'invocazione al costruttore ad un argomento che costruisce un [oggetto temporaneo](#^cd9d7d).

#### Explicit keyword
In qualche caso possiamo non volere che un costruttore ad un parametro sia richiamato implicitamente come convertitore di tipo.
Basta allora usare la keyword `explicit`:
```cpp
class orario{
public:
	explicit orario(int); // costruttore esplicito 1 parametro
	...
};

```
>[!error]
>Con `orario` definita come sopra, abbiamo errore di compilazione:
> ```cpp
> orario x = 8;
> ```

>È anche possibile definire in una classe C una conversione implicita dal tipo *C* al tipo *T* tramite degli **operatori espliciti di conversione**. #Definizione 

>[!example]- orario -> int
>```cpp
>class orario {
>public:
>	operator int() { return sec; }
>	...
>};
>orario o(14, 37);
>int x = o; // OK: viene chiamato implicitamente l'operatore int() di o
>```


## Overloading di operatori
Vogliamo ora aggiungere alla classe `orario` una operazione che permetta di sommare le ore di una durata temporale di un orario.
### Perché?
Per sommare possiamo usare il metodo
```cpp
orario orario::Somma(const orario& o){
	orario aux;
	aux.sec = (sec + o.sec) % 86400;
	return aux;
}
```
Per poi usarlo:
```cpp
orario ora(22,45);
orario DUE_ORE_E_UN_QUARTO(2,15);
ora = ora.somma(DUE_ORE_E_UN_QUARTO);
```
un modo più elegante è quello di fare **overloading**[^1] , dell'operatore somma.
```cpp
class orario {
public:
	orario operator+(orario);
	...
};

orario orario::operator+(orario o) {
	orario aux;
	aux.sec = (sec + o.sec) % 86400;
	return aux;
}
```
Per infine ottenere:
```cpp
orario ora(22,45);
orario DUE_ORE_E_UN_QUARTO(2,15);
ora = ora + DUE_ORE_E_UN_QUARTO;
```

### Definizione
>[!def] Overloading Operator
>In generale la ridefinizione di un operatore *OP* si ottiene definendo una funzione il cui identificatore è `operatorOP`.
>Ogni occorrenza dell'operatore *OP* viene equivale all'invocazione di tale funzione.

>[!question]- Possiamo fare overloading[^1] di tutti gli operatori?
>No, non possiamo fare overloading degli operatori
>- `.` : operatore di selezione di membro ;
>- `? :` : operatore ternario;
>- `::` : operatore di scoping;
>- `sizeof`;
>- `typeid` : operatore di identificazione dinamica;

>[!important] Proprietà irremovibili
>Non si possono cambiare:
>- Posizione (prefissa, postfissa);
>- Numero di operandi;
>- Precedenza;
>- Associatività

[^1]:cioè ridefinizione di un metodo