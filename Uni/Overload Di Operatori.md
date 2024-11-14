Vogliamo ora aggiungere alla classe `orario` una operazione che permetta di sommare le ore di una durata temporale di un orario.
## Perché?
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
>- Associatività;

>Fra gli argomenti dell'operatore ridefinito **deve** comparire almeno un argomento di un tipo definito da utente, cosicché gli operatori predefiniti non possono essere modificati.

Infatti se avessimo ridefinito l'operatore '+' per la classe `orario` il compilatore non avrebbe accettato l'espressione
```cpp 
ora = ora + DUE_ORE_E_UN_QUARTO
``` 
Questo è vero per tutti gli operatori ad eccezione di:
- = (operatore di assegnazione);
- & (operatore di indirizzo);
- , (operatore virgola);
Per questi operatori C++ fornisce una definizione standard per ogni classe.


## Overload con funzioni Esterne
### Perché?
Il motivo appare evidente quando vogliamo fare overloading dell'operatore di output `<<` 
```cpp
#include <iostream>
std::ostream& orario::operator<<(ostream& os) const {
	return os << Ore() << ':' << Minuti() << ':' << Secondi();
}
```

>[!attention] N.B:
>Il primo parametro implicito di un operatore binario con 1 solo parametro è l'oggetto di invocazione, posto solitamente come primo operando, mentre il parametro passato e il secondo operando

Notando e seguendo questo dunque abbiamo:
```cpp
le_quattro << cout;
le_tre << cout;
```

mentre noi vorremmo:
```cpp
cout << le_tre << " vengono prima delle " << le_quattro;
```

per far questo il parametro di tipo `ostream` deve avere la posizione dell'oggetto di invocazione e ciò non è possibile se specifichiamo l'overloading dell'operator << all'interno della classe.
Siamo dunque costretti ad andare fuori.
### Sintassi
La sintassi è abbastanza semplice e "intuitiva", per quanto C++ possa essere intuitivo:
```cpp
ostream& operator<<(ostream& os, const orario& o) {
	return os << o.Ore() << ':' << o.Minuti() << ':' << o.Secondi();
}
```

>[!warning] Problema
>1. Il fatto che andiamo fuori dalla classe significa che non abbiamo accesso alla parte `private` della classe
>2.  Non abbiamo più la commutatività valida se come primo operando abbiamo un oggetto senza l-valore ([oggetto anonimo](Costruttori#^cd9d7d))
>   >[!failure]- Es
>   >```cpp
>   >orario x(12,23);
>   >orario y = 4 + y; // oppure
>   >orario y = orario(2) + y;
>   >```
>
> Questi problemi nascono dal fatto che queste funzioni dovrebbero essere del tutto interne alla classe, ma per un bad design del linguaggio siamo costretti ad andare fuori.
> Questo problema si risolve con la keyword [`friend`](Friend).



[^1]:cioè ridefinizione di un metodo
