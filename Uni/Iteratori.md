Tags: [[P.A.O]] [[Friend]]
Nella manipolazione degli oggetti di una [classe](Classi) collezione si avverte ben presto la necessità
di poter accedere agli elementi di una collezione, ad esempio per poter scorrere tutti gli 
elementi della collezione.
```cpp title:prima_versione
class contenitore {
private:
	class nodo {
	public:
		int info;
		nodo* next;
		nodo(int x, nodo* p) : info(x), next(p) {}
	};
	nodo* first;
public:
	contenitore(): first(0) {}
	void aggiungi_in_testa(int x) { first = new nodo(x, first); }
};

```

Vogliamo definire una classe `iteratore` i cui oggetti rappresentano "indici" della classe `contenitore`.

```cpp title:prima_versione
class iteratore {
private:
	contenitore::nodo* punt; // nodo puntato dall'iteratore
public:
	bool operator==(iteratore i) const {
		return punt == i.punt;
	}
	interatore& operator++() { // ++ prefisso
		if(punt) punt = punt->next;
		return *this;
	}
};
```

> Sia il campo dati `punt` che i metodi di questa definizione della classe `iteratore` accedono alla parte [privata](Classi#Private) della classe `contenitore`.
> Dunque si necessita di una *[dichiarazione di amicizia](Friend) tra classi*.

```cpp ok:2,11
class contenitore {
friend class iteratore; // dichiarazione di amicizia
private:
	class nodo {
		...
	};
	nodo* first;
public:
	class iteratore {
		contenitore::nodo* punt; // per amicizia
	};
	...
};

```

La classe `contenitore` metterà allora a disposizione dei metodi pubblici per definire gli 
iteratori "iniziali" e "finali" su un certo oggetto e ridefinirà l'operatore di indicizzazione
`operator[]` con un parametro `it` di tipo `iteratore`.
>Questi metodi dovranno però accedere al puntatore privato di `iteratore`
>Garantiremo dunque questo accesso dichiarando `contenitore` [amica](Firiend) di `iteratore`.

>[!success] Versione Finale
>```cpp title:contenitore.h
>class contenitore {
>friend class iteratore; // dichiarazione di amicizia
>private:
>	class nodo {
>		...
>	};
>	nodo* first;
>public:
>	class iteratore {
>		contenitore::nodo* punt; // per amicizia
>	};
>	...
>	iteratore begin() const;
>	iteratore end() const;
>	int& operator[](iteratore) const;
>};
>```
>```cpp title:contenitore.cpp
>contenitore::iteratore contenitore::begin() const {
>	iteratore aux;
>	aux.punt = first; // per amicizia
>	return aux;
>}
>contenitore::iteratore contenitore::begin() const {
>	iteratore aux;
>	aux.punt = 0; // per amicizia
>	return aux;
>}
>int& contenitore::operator[](contenitore::iteratore it) const {
>	return it.punt->info; // per amicizia
>}
>```

>[!example] Uso di iteratori
>```cpp
>int somma_elementi(const contenitore& c) {
>	int s = 0;
>	for(contenitore::iteratore it = c.begin(); it != c.end(); ++i) {
>		s += c[it];
>	}
>	return s;
>}
>```
