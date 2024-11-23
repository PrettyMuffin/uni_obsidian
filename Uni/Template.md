Tags:  [[P.A.O]] [[Essenziali]]
## Template di Funzione
>[!def] Template di Funzione #Definizione 
>Un template di funzione è una descrizione di un metodo che il compilatore può usare
>per generare automaticamente istanze particolari di una funzione che differiscono per il
>tipo degli argomenti.
>>[!example] Fuzione `min()` templetizzata
>>```cpp
>>template \<class T> // \ serve per obsidian, non fa parte della sintassi
>>T min(T a, T b) {
>>	return a < b ? a : b;
>>}
>>```

>La parola chiave `template` appare sia all'inizio delle dichiarazioni che alla definizione di un template di funzione.

È seguita dalla lista (non vuota) dei *parametri dei template* separati da virgole e racchiusi tra simboli "<" e ">". #Definizione 
I parametri possono essere di 2 tipi: ^17261e
- **Tipo**: sono preceduti dalla parola chiave `class` e che saranno istanziati con un tipo qualsiasi. ^432528
- **Valore**: sono parametri preceduti dal tipo di appartenenza del valore e devono essere istanziati con un valore costante del valore dichiarato;

>[!def] Istanziazione del Template #Definizione 
>È la "costruzione" di una specifica funzione per alcuni parametri attutali di tipo e valore dallo schema del template.

Vi sono 2 tipi di istanziazioni:
### Implicita
Dove i tipi e valori dei parametri sono [dedotti](#Algoritmo%20di%20Deduzione) automaticamente dai parametri attuali passati al template;
   >[!example] Istanziazione Implicita
   >```cpp
   >int main() {
   >int i, j, k; orario r, s, t;
   >...
   >// istanziazione implicita
   >k = min(i, j); // istanzia: int min(int, int)
   >t = min(r, s); // istanzia: orario min(orario, orario)
   >}
   >```
   >
   
>[!warning] N.B.
>Nell'istanziazione implicita il tipo di ritorno dell'istanza del template non viene considerato nella deduzione degli argomenti

Inoltre nella istanziazione implicita sono ammesse essenzialmente quattro sole tipologie di conversioni dal tipo dell'argomento attuale al tipo dei parametri dei template:
- Conversione da l-valore a r-valore (`T&` ==> `T`);
- Da array a puntatore (`T[]` ==> `T*`);
- Conversione di qualificazione costante (`T` ==> `const T`);
- Conversione da r-valore a riferimento costante, cioè da r-valore di tipo `T` a `const T&`
#### Algoritmo di Deduzione
L'algoritmo di deduzione degli argomenti di un template procede esaminando tutti gli argomenti attuali nella chiamata del template di funzione da sinistra verso destra.
> Se si trova uno stesso parametro `T` del template che appare più volte come parametro  di tipo, l'argomento dedotto per `T` da ogni argomento attuale deve essere *esattamente lo stesso*.

>[!failure] Es.
>```cpp error:4
>int main() {
>	int i; double d, e;
>	...
>	e = min(i, d);
>}
>```
>Non compila perché deduce due diversi argomenti del template: `int` e `double`

### Esplicita

^78b975

Si tratta di istanziare esplicitamente ogni singolo parametro del template:
>[!example]
>```cpp
>int main() {
>	int i; double d, e;
>	...
>	e = min\<double>(i, d);
>}
>```
>Compila correttamente e istanzia `double min(double, double)` ==> converte implicitamente i da `int` => `double`

Solitamente si usa l'istanziazione esplicita solamente quando risulti necessaria per risolvere ambiguità o per usare particolari istanze del template in contesti in cui la [deduzione](#Algoritmo%20di%20Deduzione) non è possibile.

### Modello di compilazione dei template di funzione
Come abbiamo detto, la definizione di un template di funzione è solamente uno schema per la definizione di un numero potenzialmente infinito di funzioni.
> Di per sé la definizione di template non può essere compilata in qualche forma di code macchina per poi essere eseguito.
> Il compilatore quando incontra un template di funzione, si limita a memorizzare una rappresentazione interna di tale definizione, senza effettuare alcuna tipologia di compilazione
> 

>Solo quando incontrerà un uso effettivo di [template di funzione](Template%di%Funzione) genererà il codice macchina corrispondente alla particolare istanza di funzione utilizzata

>[!question] Domande Legittime
>1. Affinché il comilatore non possa generare l'istanza della funzione, de definizione del template deve essere visibile nel punto in cui compare la chiamata di funzione?
>2. Occorre mettere le definizioni del template di funzioni in un file header da includere in ogni file che richiede una istanziazione dei template?
>3. Oppure nel file header si mettono soltanto le dichiarazioni dei template di funzione mentre le definizioni dei template si possono mettere in un file da "compilare" separatamente?

Con la *compilazione per inclusione* (`#include <>`) le definizioni dei template vengono messe in file header.
Il compilatore usa quindi queste definizioni per generare il codice di tutte le istanze del template utilizzate nel file che sta compilando.
Questo modello di compilazione presenta 2 sostanziali problemi
1. Il file header contiene tutti i dettagli della definizione dei template. Tali dettagli risultano quindi visibili all'utente e ciò quindi va contro il principio dell'[information hiding](Programmazione%20Ad%20Oggetti#^f42cc0);
2. Se la stessa istanza di un template viene usata in più file compilati separatamente il codice per tale istanza viene generato più volte dal compilatore.
   >[!success] No Run-Time problem
   >Nonostante ciò questo non crea incovenienti a tempo di esecuzione in quanto al momento del collegamento dei file compilati separatamente il linker usa soltanto una delle istanze compilate, mentre le altre vengono ignorate.

   >[!failure] Rallentamento Compilazione
   >Tuttavia la compilazione ripetuta delle stesse istanze e l'inclusione di file header molto lunghi può rallentare il processo di compilazione in modo significativo.
   >>[!success] Soluzione
   >>Molti compilatori (g++ compreso) alleviano il problema dell'efficienza con un'opzione.
   >>In g++ abbiamo
   >>```txt
   >>g++ -fno-implicit-templates
   >>```

## Template di [[Classi]]
### Definizione

>[!def] Template di classe #Definizione 
>Un *template di classe* è essenzialmente la descrizione di un metodo (modello) che il compilatore può usare per generare automaticamente istanze particolari di una [classe](Classi) che differiscono solo per il tipo di alcuni campi dati, metodi, [classi interne](Classi%20Annidate).

^d7c6d3

Come per i [template di funzione](#Template%20di%20Funzione) anche nei template di classe vi sono 2 tipologie di parametri:
-  [*Parametri di tipo*](#^c6acce)
- [*Parametri per valore*](#^dfaf06)

A differenza dei template di funzione, dove in mancanza di [parametri attuali](#^17261e)  il compilatore cerca di
[dedurre](#Algoritmo%20di%20Deduzione) automaticamente i tipi, con i template di classe **occorre sempre istanziare in modo [esplicito](#Esplicita)** i parametri dei template.
> Le regole per l'uso dei valori di default nei template di classe sono analoghe per l'uso di valori di default per le funzioni.

>[!example] Valore di default
>```cpp
>template\<class T = int, int size = 1024>
>class Buffer {
>	...
>};
>
>int main() {
>	Buffer<> ib;
>	Buffer\<string> sb;
>	Buffer\<string, 500> sbs;
>}
>```

### Istanziazione di un template di classe
La definizione di un template di classe specifica come si possono costruire delle istanze di classe
qualora vengano forniti i valori dei parametri del template.
>[!attention] Uguale template, istanziazioni diverse
>`Queue<int> qi;` costruisce effettivamente una classe "coda di interi".
>Se per un'istruzione `Queue<string> qs;` il compilatore costruisce anche la classe "coda di stringhe" allora le due classi, benché costruite usando lo stesso template di classe, sono classi *completamente distinte*. In particolare, `Queue<int>` non ha accesso alla parte [privata](Classi#Private) di `Queue<string>`.

Al di fuori di definizioni o dichiarazioni di template possono comparire solo nomi di istanze di template di classe.
>[!important] N.B.
>L'occorrenza del nome di una istanza di un template di classe non è sufficiente perché il compilatore generi tale istanza. Occorre che il nome compaia in un solo contesto che richieda un utilizzo effettivo di tale definizione.
>>[!example] Es
>>```cpp
>>template \<class T> class Queue;
>>void Stampa(const Queue\<int>& q) {
>>	Queue\<int>* pqi = &q;
>>	...
>>}
>>```
>>Il compilatore non genera istanza `Queue<int>` quando incontra le due occorrenze del nome dell'istanza.
>>Questo perché non è necessaria l'istanza di `Queue<int>` per copiare un riferimento o un puntatore a `Queue`. Non è quindi necessaria la definizione del template, ma basta la sua [dichiarazione incompleta](Classi#Dichiarazioni%20Incomplete).

>[!question] Dunque quand'è che viene effetivamente istanziato un template di classe?
>Viene ovviamente istanziato un template di classe quando:
>- È necessario operare su oggetti della classe:
>  >[!example] Es
>  >```cpp
>  >template \<class T> class Queue {
>  >	...
>  >};
>  >a
>  >void Stampa(Queue\<int> q) {
>  >	Queue\<int> qi;
>  >	...
>  >}
>  >```
>  
>	Questo perché l'istanza di `Queue<int>` del template serve per allocare lo spazio per i due oggetti `q` e `qi`.
>- Quando si effettuano operazioni con `sizeof`: il compilatore infatti è costretto a generare l'istanza `Queue<int>` con
>  >[!example] Es.
>  >```cpp
>  >template \<class T> class Queue { ... };
>  >void Stampa(const Queue\<int>& q) {
>  >	Queue\<int>* pqi = &q;
>  >	pqi++;
>  >	...
>  >}
>  >```
>
> 	perché questa istanza della classe serve per calcolare la quantità `sizeof(Queue<int>)` di cui occorre incrementare il puntatore per eseguire `pqi++`.

### Metodi di template di classe
Come per le normali [classi](Classi), in un template di classe la definizione di un metodo può comparire sia all'esterno che all'interno della classe.
>[!info] Istanziazione e di metodi
>Un metodo di un template di classe non viene istanziato quando viene istanziata la classe, ma se e soltanto quando il programma usa effettivamente quel metodo.

>[!important] Definizione e Dichiarazione di template di classe
>La dichiarazione e la definizione di un template di classe sono entrambe poste in un file header che deve essere incluso in ogni file che voglia usare il template di classe.
>Questo file header, quindi, conterrà anche le definizioni esterne dei metodi del template di classe.



### Dichiarazioni [friend](Friend) in template di classe
In un template di classe possono apparire 3 tipologie di dichiarazioni [friend](Friend).
1. Dichiarazione nel template di classe `C` di una classe o funzione [friend](Friend) non template.
   ```cpp
   class A { ... int fun(); ... };
   template <class T>
   class C {
   friend int A::fun();
   friend class B;
   friend bool test();
   }; 
   ```
	In questo caso, la classe `B`, la funzione `test()` e il metodo `A::fun()` della classe `A` sono friend di *tutte* le istanze di `C`.
	>[!note]
	>Si noti che non serve dichiarare o definire `B` e `test()` prima del template `C` affinché sia visibile a `C`, mentre occorre che la classe `A` sia stata dichiarata prima del template `C` affinché sia visibile a `C` che la classe `A` inlclude un metodo `fun()`

2. ==Dichiarazione del template di classe `C` di un template di classe o di un template di funzione [friend](Friend) associato.==
   In questo caso tutte le istanze della classe template `C` hanno come amica *una e una sola corrispondente istanza* del template di classe [friend](Friend) associato o del template di funzione [friend](Friend) associato.
   ```cpp
   template <class T> class A { ... int fun(); ...};
   
   template <class T> class B { ... };
   
   // dichiarazione incompleta del template di classe C
   template <class T1, class T2> class C;
   
   // dichiarazione del template di funzione test
   // associato a C
   template<class T1, class T2> bool test(C<T1, T2>);
   
   template<class T1, class T2> class C {
	  friend int A<T1>::fun(); 
	  friend class B<T2>;
	  friend bool test<T1,T2>(C);
   };
   ```

	Si tratta del caso più comune e significativo;
 ^69285b
3. Dichiarazione nel template di classe `C` di un template di classe o di un template di funzione [friend](Friend) non associato, cioè aventi i parametri disgiunti dai parametri di `C`. In questo caso, i template di classe o di funzione dichiarati friend sono friend di ogni istanza del template di classe `C`.
   ```cpp
   template<class T>
   class C {
   T t;
   
   template<class Tp>
   friend int A<Tp>::fun();
   
   template<class Tp>
   friend class B;
   
   template<class Tp>
   friend bool test(C<Tp>);
   };
   ```


### Membri [statici](Static) in template di classe
>[!important]
Anche in un template di classe possono essere dichiarati campi dati e metodi statici, in tal caso ogni istanza del template di classe ha dei propri campi dati e metodi [statici](Static), ==distinti da quelli delle altre istanze della classe==

Per esempio
```cpp
template<class T> 
class Queue {
private: 
	static int contatore;
	...
};

template<class T>
int Queue<T>::contatore = 0;
```

Un campo dati [statico](Static) è istanziato e quindi inizializzato dalla definizione del template soltanto se viene viene effettivamente usato.
> ==La mera definizione del campo dati statico non provoca allocazione di memoria.==
### Template di [classe annidati](Classi%20Annidate)
All'interno di un template di classe possono essere dichiarati altri template di classe annidati sia [associati](#^69285b) e non associati.
>[!example]
>Un modo migliore per permettere l'uso di `QueueItem` soltanto alla classe `Queue` è quello di [annidare](Classi%20Annidate) nella parte [privata](Classi#Private) della definizione di [template di classe](#^d7c6d3) `Queue` la definizione del template di classe `QueueItem`.
>```cpp
>template\<class T>
>class Queue {
>private:
>	class QueueItem {
>	public:
>		QueueItem(const T& val);
>		T info;
>		QueueItem* next;
>	};
>	...
>};
>```

### Tipi e template impliciti in template di classi

Il C++ standard prevede che l'uso di tipi e di template di classe o di funzione che dipendono da un [parametro di tipo](#^432528) debba essere disambiguato tramite le parole chiavi `typename` (per i tipi) e `template` (per i template sia di classe che di [funzione](#Template di Funzione)).
In altri termini, ==non è ammesso l'uso implicito di tipi e template che dipendono da [parametri di tipo](#^432528)==.
>[!example] Ad esempio:
>```cpp
>template\<class T> class C {
>public:
>	class D { // classe annidata
>	public:
>		T x;
>	};
>	a
>	template\<class U> class E { // template di classe annidata
>	public:
>		T x;
>		void fun1() { return; }
>	};
>	a
>	template\<class U> void fun2() { // template di funzione
>		T x;
>		return;
>	}
>};
>```
>L'uso dei nomi `D`, `E`, e `fun2` che dipendono da un [parametro di tipo](#^432528), ad esempio nel seguente template di funzione `templateFun`, deve essere disambiguato:
>```cpp
>template\<class T>
>void templateFun(typename C\<T>::D d) {
>	// C\<T>:: D è un uso di un tipo che dipende dal parametro T
>	typename C\<T>::D d2 = d;
>	
>	// (1) E\<int> è un uso del template di classe annidata 
>	// che dipende dal parametro T
>	// (2) C\<T>::E\<int> è un uso di un tipo che dipende dal 
>	// parametro T
>	tpyename C\<T>::template E\<int> e;
>	e.fun1();
>	a
>	// c.fun2\<int> è un uso del template di funzione che dipende dal
>	// parametro T
>	C\<T> c;
>	c.template fun2\<int>();
>}
>```