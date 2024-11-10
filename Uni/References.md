Tag: [[P.A.O]] [[Classi]]
## Definizione
>[!def] Referencs #Definizione 
Le *references*, chiamate anche aliases, sono degli altri nomi per chiamare le variabili.
Una volta che una referenza viene inizializzata con il **riferimento** ad una variabile, nel codice si può usare sia il riferimento che la variabile su cui viene fatto il riferimento.
Di fatto "under the hood" c'è un puntatore che crea questo riferimento, ma i reference **non devono essere usati come un *puntatore* ma come *alias*.**

## Referencs vs Puntatori
Ci sono 3 grandi differenze tra *reference* e *puntatori*:
1. **Non** puoi avere NULL references: una reference punta sempre ad una variabile;
2. Una volta fatta la referenza ad un oggetto non è più possibile cambiare la referenza ad un altro oggetto;
3. Una referenza **deve** essere inizializzata quando viene creata

>[!attention] N.B: Valore Reference
>È indispensabile che quando si inizializzi una reference, il valore di inizializzazione ***deve*** necessariamente avere *l-valore*, dunque non può essere né essere un [oggetto anonimo](Classi#Tipi%20di%20costruttori%20e%20oggetti%20anonimi) né un valore litterale
>>[!example]- Es:
>>>[!error] Oggetto anonimo
>>>```cpp
>>>orario& o = orario(12);
>>>```
>>
>>>[!error] Valore litterale
>>>```cpp
>>>int& i = 15;
>>>```
>>
>>>[!success] OK
>>>```cpp 
>>> int i = 15;
>>> int& r = i; 
>>>```
###### Sintassi:
La sintassi delle references è: 
`<tipo>& <nome_variabile> = <variabile su cui fare reference`
>[!example]
>```cpp
>int i;
>double d;
>int& r = i;
>double& s = s;
>i = 5;
>cout << "Value of i : " << i << endl; // 5
>cout << "Value of i reference : " << r << endl; // 5
>d = 6.7;
>cout << "Value of d : " << d << endl; // 6.7
>cout << "Value of d reference : " << s << endl; // 6.7
>```

## References in funzioni
### References come parametri
Vediamo come avviene lo scambio tra variabili con il *reference*.
```cpp
// function declaration: reference parameters
void swap(int& x, int& y);
void swap_p(int* x, int* y)

int main () {
	// local variable declaration
	int a = 1; int b = 2;
	cout << "Before swap, value of a :" << a << endl; // 1
	cout << "Before swap, value of b :" << b << endl; // 2
	// calling a function to swap the values
	swap_r(a, b);
	swap_p(&a, &b);
	cout << "After swap, value of a :" << a << endl; // 2
	cout << "After swap, value of b :" << b << endl; // 1
}

void swap_r(int& x, int& y) {
	int temp = x;
	x = y;
	y = temp
}

void swap_p(int* x, int* y) {
	int temp = *x;
	*x = *y;
	*y = temp
}
```
Notiamo che a differenza dello `swap()` con i reference, non si usa l'operatore `&` per ottenere *l-valore* delle variabili `a` e `b`.
==In quanto la reference gestisce la referenziazione e deferenziazione del proprio puntatore per il programmatore.==
#### References come valori di ritorno
>Quando una funzione ritorna una reference, ritorna un puntatore implicito al proprio valore di ritorno,

Per questo, una funzione può essere usata nella parte di sinistra di un'assegnazione

```cpp
int v[] = {3, 2, 6, 8, 5};
int& setValue(int i) {
	return v[i]; // return a reference to i-th element
}
int main () {
	for (int i = 0; i < 5; i++ )
	cout << v[i] << " "; // 3 2 6 8 5
	cout << endl;

	setValue(1) = 9; setValue(3) = 7;

	for (int i = 0; i < 5; i++ )
	cout << v[i] << " "; // 3 9 6 9 5
}
```

>[!warning] N.B:
> Quando si ritorna una reference, bisogna stare ultra attenti a non ritornare una variabile locale della funzione in quanto avviene una dangling reference.
> >[!error] Dangling reference
> >```cpp
> >int& fun(int& a) {
> > 	int q;
> > 	return q; // Compile time error
> > }
> >```
>
> >[!success] OK
> >```cpp
> >int& fun(int& a) {
> >	...
> >	return a; // safe, fuori dallo scope della funzione
> >}
> >```

### Parametro per valore vs Riferimento [Costante](Const)
Passare un parametro per valore richiama il [costruttore di copia](Classi#^cae878), mentre con i reference passa solo il rifermento.
Questo si traduce in un enorme aumento prestazionale del programma:
>[!example] Es.
>```cpp
>class C {
>	int a[1000]; // 4 * 1000 bytes
>} ;
>bool byValue(C x) { return true; }
>
>bool byConstReference(const C& x) { return true; }
>
>int main() {
>	C obj;
>	for(int i=0; i<10000000; i++) byValue(obj); // 3.368 sec
>	for(int i=0; i<10000000; i++) byConstReference(obj); // 0.031 sec
>}
>```
>In questo esempio abbiamo un aumento prestazionale di 108x volte
