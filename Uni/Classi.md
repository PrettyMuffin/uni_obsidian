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

### Protected
È individuata dalla parola chiave `protected`, i cui membri risultano accessibili alle [classi derivate](Ereditarietà) da `B` ma non risultano accessibili alle classi esterne a `B`.
>[!example] Esempio Protected
>```cpp
>class orario {
>...
>protected:
>	int sec;
>};
>```

## [[Costruttori]]

## [[Overload Di Operatori]]

## [[Distruttore]]

## [[Classi Annidate]]

## Dichiarazioni Incomplete
>[!def] Dichiarazione incompleta #Definizione 
>Una classe `C` può usare puntatori e riferimenti ad una classe `D` che non è definita, ma 
>che è dichiarata solo tramite una cosiddetta *dichiarazione incompleta*, ovvero una mera
>dichiarazione del nome della classe `D`.
>>[!example] Esempio
>>```cpp
>>class D;
>>
>>class C {
>>D* p;
>>D* m() { ... }
>>void n(D*) { ... }
>>D& f() { ... }
>>};
>>
>>class D {
>> // dichiarazione completa di D
>>};
>>```