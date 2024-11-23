Tags: [[Essenziali]] [[P.A.O]]
### Perché?
Prendiamo come esempio orario. Quando creiamo un nuovo oggetto i valori dei campi dati non vengono inizializzati, dunque assumono dei valori di default, che non necessariamente sono quelli che vogliamo noi.
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
>Solitamente stanno nella parte [pubblica](Classi#Public) della classe.

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
Il costruttore senza parametri viene detto di ==default o standard==. ^a16ac4
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

^afd301

^cae878
### Costruttore di Copia Standard

^eb6713

Il *costruttore di copia* viene invocato automaticamente nei seguenti casi:
- Quando un oggetto viene invocato e inizializzato:
  >[!example]- es
  >```cpp
  >orario adesso(14, 30);
  >orario copia = adesso; // dichiara e inizializza copia
  >orario copia1(adesso); // dichiara e inizializza copia1
  >```
- Quando un oggetto viene passato come parametro per valore:
  >[!example]- es
  >```cpp
  >ora = ora.Somma(DUE_ORE_E_UN_QUARTO);
  >```
  >Viene costruito di copia l'oggetto utilizzato nella funzione `Somma()`, copiato da `DUE_ORE_E_UN_QUARTO`

- Quando una funzione ritorna per valore tramite l'istruzione `return`:
  >[!example]- es
  >```cpp
  >orario orario::Somma(orario o) {
  >	orario aux;
  >	aux.sec = o.sec + sec;
  >	return aux;
  >}
  >```
  >Viene costruito un [oggetto temporaneo anonimo](Costruttori#^cd9d7d)  che verrà [distrutto](Distruttore) (deallocato) non appena sia stato usato come valore di ritorno della funzione

Nel 3° caso lo standard propone un'ottimizzazione, sebbene sia sottile, in quanto dice:
>[!quote] Standard
>Ogni qual volta che si dovrebbe creare un oggetto temporaneo "inutile" usato solo per inizializzare un nuovo oggetto dello stesso tipo, tale oggetto non viene creato.

>Tale ottimizzazione è presente di default, per rimuovere tale ottimizzazione basta aggiungere al comando di compilazione il flag `-fno-elide-constructors`.

>[!attention]- Costruttore di copia standard vs Assegnazione standard
> ##### Costruttore di copia standard
> Il costruttore di copia standard *crea* un nuovo oggetto inizializzandolo da un altro oggetto dello stesso tipo.
> ##### Assegnazione standard
> L'assegnazione standard invece *modifica* un oggetto già esistente cambiandone i campi dati

## Liste di Inizializzazione
>[!def] Lista di inizializzazione #Definizione 
>Una esplicita *lista di inizializzazione* consiste in una lista di invocazioni a costruttori, possibilmente di [copia](#Costruttore%20di%20Copia%20Standard).

In una classe C con lista di campi dati $x_1 ... x_k$, un costruttore con lista di inizializzazione per i campi dati è definito tramite la seguente sintassi:
```cpp
C(T1,...,Tn) : xi(...), ..., xj(...) { // codice }
```

Il comportamento è il seguente:
1. Ordinatamente per ogni campo dati $x_i (1 \leq i \leq k)$ dove $k$ è il numero di campi dati della classe viene chiamato il rispettivo costruttore
   - o esplicitamente tramite una chiamata ad un costruttore $x_i$ definita nella lista di inizializzazione
   - o implicitamente (cioè non appare nella lista di inizializzazione) tramite una chiamata al [costruttore di default](#^a16ac4) di $x_i$.
2. Quindi viene eseguito il codice del costruttore
   - Naturalmente parliamo di costruttori e costruttori di copia anche per i campi dati di tipo non classe
   - La chiamata implicita al costruttore di default per un campo dati di tipo non classe, alloca lo spazio in memoria ma lascia indefinito il valore.
   - ==L'ordine in cui vengono invocati i costruttori, esplicitamente o implicitamente  è sempre determinato dalla lista ordinata dei campi dati==


>[!info]- Campi dati di tipo riferimento
>Analogamente ai campi dati dichiarati costanti, questo campo dati deve essere obbligatoriamente inizializzato tramite una chiamata al costruttore di copia inclusa nella lista di inizializzazione.

>[!info] Costruttori Standard
>Il comportamento dei costruttori standard di [default](#^a16ac4) e di [copia](#^afd301) è:
>- Una chiamata `C()` al [costruttore di default](#^a16ac4) standard di una classe `C` invoca ordinatamente per ogni campo dati *x* di `C` il corrispondente costruttore di default.
>  ==Dove per i tipi non classe tale "costruttore di default standard" semplicemente alloca la memoria per il campo dati *x*==.
>- Una chiamata `C(obj)` al [costruttore di copia](#^afd301) standard di una classe `C` invoca ordinatamente per ogni campo dati *x* di `C` il corrispondente costruttore di copia sul relativo campo dati `obj.x` dell'oggetto-parametro attuale `obj`.

```cpp 
class C {
public:
	C() { cout << "C0"; }
	C(const C&)  { cout << "Cc "; }
};

class D {
public:
	C c;
	D() { cout << "D0 " ;}
};

int main() {
	D x;
	cout << endl;
	// stampa C0 D0
	D y(x);
	cout << endl;
	// stampa Cc
}
```

```cpp 
class C {
public:
	C() { cout << "C0"; }
	C(const C&)  { cout << "Cc "; }
};

class D {
public:
	C c;
	D() { cout << "D0 " ;}
	D(const D&) { count << "Dc "; } 
};

int main() {
	D x;
	cout << endl;
	// stampa C0 D0
	D y(x);
	cout << endl;
	// stampa C0 Dc
}
```

>Si noti attentamente che la differenza in linea *19 e 20* è spiegata dal fatto che il [costruttore di copia](#^afd301) standard di una classe `D` (primo esempio) non è stato ridefinito, dunque invoca ordinatamente per ogni campo dati *x* di `D` il corrispondente [costruttore di copia](#^afd301) del tipo di *x*.
