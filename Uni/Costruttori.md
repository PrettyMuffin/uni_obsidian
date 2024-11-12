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

^cae878
### Costruttore di Copia Standard
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