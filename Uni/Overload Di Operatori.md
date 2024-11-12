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