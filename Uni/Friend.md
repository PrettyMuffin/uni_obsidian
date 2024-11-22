Tags: [[Essenziali]] [[P.A.O]] [[Classi]]
Supponiamo di voler fare [overload](Overload%20Di%20Operatori#Overload%20con%20funzioni%20Esterne) di funzioni esterne, come ad esempio della funzione `operator<<()`.
Siamo costretti ad uscire dalla classe, ciò significa che non abbiamo più accesso ai campi
privati, ma solo a quelli pubblici. Finché la [classe](Classi) possiede metodi pubblici per poter
accedere ai campi privati è tutto ok, ma il momento in cui ciò non è possibile e necessitiamo
di usare i campi privati come facciamo?
La keyword ```friend``` ci viene in soccorso, in quanto da privilegi alla funzione / classe esterne
di poter accedere ai campi privati, come fossero amiche :).
La funzione che utilizza `friend` viene detta *funzione_amica della classe*. #Definizione 
>[!example] Sintassi
>```cpp title:bolleta.h
>class bolletta {
>// funzione esterna dichiarata friend
>	friend ostream& operator<<(ostream&, const bolletta&);
>};
>ostream& operator<<(ostream&, const bolletta&);
>```
>```cpp title:bolletta.cpp
>ostream& operator<<(ostream& os, const bolletta& b) {
>	os << "TELEFONATE IN BOLLETTA" << endl;
>	bolletta::nodo* p = b.first // per amicizia
>	int i = 1;
>	while(p) {
>		os << i++ << ") " << p->info << endl;
>		p = p->next;
>	}
>	return os;
>}
>```
