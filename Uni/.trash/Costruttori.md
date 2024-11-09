Tags: [[P.A.O]] [[Classi]]

## Perché?

Prendiamo come esempio orario. Quando creiamo un nuovo oggetto i valori dei campi dati non vengono inizializzati, dunque assumono dei valori di default, ne non necessariamente sono quelli che vogliamo noi.  
Tuttavia:

Sec è un campo privato

```cpp
orario mezzanotte;
mezzanotte.sec = 0;
```

## Definizione

Si devono dunque usare i cosiddetti _costruttori_:

Costruttore

Metodo con lo stesso nome della classe e senza tipo di ritorno che vengono invocati automaticamente quando viene dichiarano, quindi costruito, un oggetto.  
Solitamente stanno nella parte [pubblica](app://obsidian.md/index.html#Public) della classe. [#Definizione](app://obsidian.md/index.html#Definizione)

Esempio

Si posso definire più costruttori purché differiscano nella lista dei parametri.  
Il costruttore senza parametri viene detto di ==default==.

```cpp
	class orario {
	public:
		orario(); // default
		orario(int, int);	// costruttore ore-minuti
		orario(int, int, int); // costruttore ore-minuti-secondi
	};
```