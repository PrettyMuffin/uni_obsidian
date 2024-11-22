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
I parametri possono essere di 2 tipi:
- **Tipo**: sono preceduti dalla parola chiave `class` e che saranno istanziati con un tipo qualsiasi.
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

## Modello di compilazione dei template di funzione
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

