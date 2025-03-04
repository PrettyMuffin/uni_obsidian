Tags: [[P.A.O]] [[Introduzione]]

## Perché?
Uno dei problemi della [programmazione modulare](https://en.wikipedia.org/wiki/Modular_programming) è il problema dell'inquinamento dello spazio dei nomi

>[!definition] Inquinamento dello spazio dei nomi
>Magari è necessario dare lo stesso nome a due funzioni / variabili che svolgono la stessa funzione ma in contesti diversi
>Questo può provocare dei conflitti tra identificatori nella [programmazione modulare](https://en.wikipedia.org/wiki/Modular_programming) come mostra questo esempio
>```cpp
>// file "Complex. h"
>struct Complex{
>... // implementazione
>... // di Complex
>};
>double module(Complex c);
>-------------------------------------------------------------------------
>// Qualche altro file .h
>#include <Complex.h>
>// Dichiarazione Illegale 
>// errore di compilazione
>
>struct Complex{
>... // implementazione 2
>... // di Complex
>};
>
>void f() {
> // si vorrebbero utilizzare
 > // entrambi i tipi di Complex
>}

Per questo nasce il meccanismo dei `namespace`.
Il _namespace_ permette di incapsulare dentro un "contenitore" dei nomi che altrimenti inquinerebbero il namespace globale (quello di default).
Per questo si usano i namespace quando ci si aspetta che il codice sia usato in ambienti software esterni.
## Definizione
Il namespace di definisce così:

```cpp
namespace newSpace {
	struct Complex {
		...
	};
	double module(Complex c);
	... // altri membri del namespace
};
```

Il namespace `newSpace` identifica uno spazio dei nomi separato dal namespace globale.

==Per accedere alle funzioni e definizioni all'interno di un namespace è necessario `::`== ovvero l'operatore di _scoping_.
L'operatore di scoping ha questa sintassi : `<namespace>::<oggetto / funzione>` ^11f9a8

```cpp
#include <Lib_UNO.h>
#include <Lib_DUE.h>

void funzione(){
	SPAZIO_UNO::Complex var1;
	SPAZIO_UNO::f(var1);
	SPAZIO_DUE::Complex var2;
	SPAZIO_DUE::g(var2);
	SPAZIO_DUE::g(var1); // errore di compilazione	
}

``` 

### Alias vs Direttiva D'Uso vs Dichiarazione D'Uso

Un **alias** di namespace permette di associare a un namespace esistente un nome alternativo es.:  ^0511f7

```cpp
#include <Lib_UNO.h>
#include <Lib_DUE.h>

namespace UNO = SPAZIO_UNO;
namespace DUE = SPAZIO_DUE;

void funzione(){
	UNO::Complex var1;
	UNO::f(var1);
	DUE::Complex var2;
	DUE::g(var2);
}
``` 

Una **Direttiva d'uso** rende tutte le dichiarazioni di un namespace visibili in modo tale che si possa fare riferimento a loro senza l'operatore di [scoping](#^11f9a8)

```cpp
#include <Lib_UNO.h>
#include <Lib_DUE.h>

using namespace SPAZIO_UNO;

void funzione(){
	Complex var1;
	f(var1);
}
``` 

La **Dichiarazione d'uso** fornisce invece un meccanismo più selettivo di visibilità dei nomi, in quanto si può specificare cosa scegliere di rendere visibile del rispettivo namespace.

```cpp
#include <Lib_UNO.h>
#include <Lib_DUE.h>

using SPAZIO_UNO::Complex;
using SPAZIO_DUE::Complex; // errore, sarebbe come non avere affatto la distinzione dei namespace, in quanto stiamo mettendo dentro il namespace globale le 2 definizioni di Complex

void funzione(){
	Complex var1;
	f(var1);
	SPAZIO_DUE::Complex var2; // non posso fare 
	SPAZIO_DUE::g(var2);
}
``` 