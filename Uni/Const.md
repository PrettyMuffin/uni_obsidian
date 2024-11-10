Tag: [[P.A.O]] [[Essenziali]] ^9fdc98

## Perché 

>[!def]- Side effect #Definizione 
>L'invocazione di un metodo su un [oggetto di invocazione](Classi#^76e1c5) può modificare lo stato di quell'oggetto, ossia i suoi campi dati.
>Si dice anche il il metodo provoca ***side effect*** sull'[oggetto di invocazione](Classi#^76e1c5).

^b969cd

## Definizione

>Per evitare modifiche non volute dell'oggetto di invocazione da parte di un metodo si può dichiarare quel metodo **costante**. #Definizione 

```cpp
class orario {
public:
	void StampaSecondi() const;
	...
};

void orario::StampaSecondi() const {
	cout << sec << endl;
}
```
Il compilatore controlla che nella definizione del metodo dichiarato come *costante* non compaia alcuna istruzione che possa causare [side effect](#^b969cd), ad esempio, assegnazioni ai campi dati dell'oggetto di invocazione o invocazioni di metodi non costanti sull'oggetto di invocazione o sui suoi campi dati.
Se la trova viene segnalato *errore*.

## Oggetti Costanti
>[!def] Oggetto Costante #Definizione 
>Anche un oggetto può essere dichiarato costante: ciò significa che tale oggetto non può venire modificato dopo che è stato [costruito](Classi#Costruttori#Definizione).
Anche i suoi campi dati diventano costanti, il che ha senso dato che modificandoli cambieremo lo stato dell'oggetto marcato 
>>[!error] Assegnazione illegale
>>```cpp
>>const orario LE_DUE(14);
>>const orario LE_TRE(15);
>>LE_DUE = LE_TRE;
>> ```

>[!important] Utilizzo di const
>In C++ non mettere `const` alla fine di un metodo che non provoca side effects è di fatto un errore logico oltre che a non permettere ad un oggetto costante l'utilizzo di quel metodo, che altrimenti ne farebbe uso.

>[!important] `*this` in un metodo costante
>L'oggetto di invocazione di un metodo costante diventa costante ==> il [puntatore `*this`](Classi#Puntatore%20this) dell'[oggetto di invocazione](Classi#^76e1c5) ha tipo `const C*`.
>Un oggetto costante invoca solo e soltanto metodi marcati **const**. Un ovvia eccezione tuttavia sono i costruttori.

##