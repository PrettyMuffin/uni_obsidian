Tag: [[P.A.O]] 

I metodi della classe `orario` che abbiamo scritto finora sono stati pensati per operaro su un oggetto specifico.
Ma se scrivessimo un metodo dove l'oggetto di invocazione è *inutile*?
>[!failure] Oggetto invocazione o inutile
>```cpp
>class orario {
>public:
>	orario OraDiPranzo() const;
>	...
>};
>
>orario orario::OraDiPranzo() const { return orario(13, 15); }
>
>// main.cpp
>void main() {
>	orario o;
>	cout << o.OraDiPranzo() << endl;
>}
>```
>In questo essempio o è inutile se non di ostacolo, in quanto il metodo OraDiPranzo() non fa uso alcuno dell'oggetto di invocazione.

>La keyword `static` risolve questo problema, in quanto permette di associare sia metodi che campi dati all'intera classe invece che a singoli oggetti della classe.

>[!success] Uso di `static`
>```cpp
>class orario {
>public:
>	static orario OraDiPranzo();
>	...
>};
>
>orario orario::OraDiPranzo() { return orario(13, 15); }
>
>// main.cpp
>void main() {
>	cout << orario.OraDiPranzo() << endl;
>}
>```
>a
>>[!question] E il [ `const`](Const)  non si usa più?
>>No! Dato che non facciamo uso di alcun [oggetto di invocazione](Classi#^76e1c5), non ha senso usare [const](Const), perché non c'è nessun [oggetto di invocazione](Classi#^76e1c5) su cui fare [side effect](Const#^b969cd).

> La memoria per i campi dati statici è unica per tutti gli oggetti ed è alloccata una volta per tutte all'inzio.

>[!important] Inizializzazione campi `static`
>I campi `static` non si possono inizializzare all'interno della definizione della classe.
>La loro inizializzazione deve essere necessariamente esterna alla classe ed è **sempre** richiesta qualora si cerchi di accedere al dato.
>>[!failure]- Inizializzazione interna
>>```cpp
>>class C {
>>	int dato;
>>	public:
>>	C(int);
>>	static int cont = 0;
>>};
>>```
>
>>[!success]- Inizializzazione Esterna nel file `.cpp`
>>```cpp
>>// C.h
>>class C {
>>	int dato;
>>	public:
>>	C(int);
>>	static int cont;
>>};
>>// file C.cpp
>>int C::cont = 0;
>>C::C(int n) { dato = n};
>>```
