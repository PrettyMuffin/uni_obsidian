Tags: [[P.A.O]] 
Talvolta durante l'esecuzione di una qualsiasi funzione, si possono riscontrare delle situazioni "eccezionali" in cui non è chiaro come la funzione possa procedere.
## `throw` e `try/cath`

La funzione in cui la situazione eccezionale si verifica lancia una cosiddetta *eccezione* tramite il costrutto `throw`.
```cpp
telefonata bolletta::Estrai_Una() {
if(Vuota()) throw Ecc_Vuota();
...
}
class Ecc_vuota {};
```

L'esecuzione di `throw` comporta la terminazione dell'esecuzione della funzione `Estrai_Una()` con il lancio dell'eccezione `Ecc_Vuota()` alla funzione chiamante, che può anche essere il `main()`.
Nella funzione chiamante, il codice contenente la chiamata o le chiamate alla funzione è racchiuso in un blocco `try` ed è seguito da una lista di procedure, le clausole `catch`, che catturano e gestiscono le eccezioni sollevate.
La funzione `abort()` (che si importa da `#include <cstdlib>`) provoca la terminazione "brutale" del `main()`.
```cpp
try { b.Estrai_Una(); }
catch(Ecc_Vuota) {
	cout << "La bolletta è vuota" << endl;
	abort();
}
```

>[!info] Missing `return` o `abort()`
> Se una clausola `cath` non contiene l'istruzione `return` o `abort()` l'esecuzione continua dal punto di programma che immediatamente segue le clausole `catch` del blocco `try`.
> 

È anche possibile usare la seguente sintassi compattata quando l'inter corpo di una funzione è contenuto in un blocco `try`.
```cpp
orario somma() try {
	orario o1, o2;
	cin >> o1 >> o2;
	return o1 + o2
}
catch(err_sint) { cerr << "Errore di sintassi"; return orario();}
catch(fine_file) { cerr << "Errore EOF"; abort();}
catch(err_ore) { cerr << "Errore ore"; return orario();}
catch(err_min) { cerr << "Errore minuti"; return orario();}
catch(err_sec) { cerr << "Errore secondi"; return orario();}
```

sebbene le eccezioni siano spesso oggetti si qualche classe, ==una `throw` può lanciare un'espressione di qualsiasi tipo==.
>[!example] Es.
>```cpp
>enum Errori { ErrSint, ErrEOF, ErrOre, ErrMin, ErrSec };
>if (t.secondi > 59) throw ErrSec;
>...
>```

### Ricerca della clausola `cath`
Quando in una funzione `F()` viene sollevata una eccezione tramite una istruzione `throw` inizia la ricerca della clausola `catch` in grado di catturarla.
- Se la si trova l'eccezione viene catturata e viene eseguito il codice della `catch`; ==eventualmente al termine dell'esecuzione del corpo della `catch` il controllo dell'esecuzione passa al punto di programma che immediatamente segue l'ultimo blocco `catch`==.
- Se non la si trova oppure le l'istruzione `throw` non era collocata all'interno di un blocco `try` la ricerca continua nella funzione che ha invocato la funzione `F()`. Questo processo continua fino a che viene individuata una clausola `catch` oppure si arriva fino al `main()` dove il programma termina con l'errore di eccezione non gestita.
### `catch` generica
Quando si esce da una funzione a causa del sollevamento di una eccezione vengono richiamati i distruttori per le variabili locali della funzione.
Questo non garantisce il recupero di eventuale memoria dinamica allocata dalla funzione o la chiusura di file aperti dalla funzione. → Possibile memory leak.
```cpp
class A { public ~A() { cout << "~A"; }};
void F() { A* p = new A[3]; throw 1; delete[] p}

int main() {
	try { F(); }
	catch (int) { cout << "int "; }
	cout << "fine";
} // Stampa: int fine
// NON STAMPA ~A ~A ~A
```
>[!warning] 
>Se viene sollecata una eccezione e questa non viene catturata all'interno della funzione si esce dalla funzione senza rilasciare la risorsa.
>Possiamo gestire questo caso con la `catch` *generica*.
>```cpp
>risorsa rs;
>...
>user() try {
>	rs.use();
>	... // codice che può sollevare errori
>	rs.release()
>}
>catch (...) { // catch generica
>	rs.release();
>	throw
>}
>```
>La sintassi `catch (...)` denota una `catch` generica in grado di catturare tutte le eccezioni possibili. Quindi, se di definisce un blocco `catch` generico prima di altri blocchi `catch`, questi blocchi non potranno mai essere eseguiti: si tratterebbe di un errore logico di programmazione.
>Una `catch` generica deve quindi sempre essere l'ultima nella lista delle `catch` che seguono un blocco `try`.
>

### Rilanciare eccezioni
È possibile che una clausola `catch` si accorga di non poter gestire direttamente una eccezione.
Può rilanciare l'eccezione alla funzione chiamante tramite una `throw`.
```cpp
orario somma() try {
	orario o1, o2;
	cin >> o1 >> o2;
	return o1 + o2
}
catch(err_sint) { cerr << "Errore di sintassi"; return orario();}
catch(fine_file) { cerr << "Errore EOF"; throw;}
catch(err_ore) { cerr << "Errore ore"; return orario();}
catch(err_min) { cerr << "Errore minuti"; return orario();}
catch(err_sec) { cerr << "Errore secondi"; return orario();}
```

>[!note]
>Si noti che una `catch` che come unica azione rilanci l'eccezione al chiamante non è significativa, in quanto si comporta come se non fosse presente.

Se durante il blocco `try` non si verificano eccezioni, le `catch` relative a tale blocco vengono saltate e l'esecuzione continua al punto di programma che immediatamente segue l'ultimo blocco di `catch`.

>[!important] Importante
>La `catch` che cattura un'eccezione è la prima `catch` incontrata durante la ricerca che abbia un *tipo compatibile* con il tipo dell'eccezione lanciata.
>Le regole che definiscono la compatibilità sono:
>- Il tipo `T` è uguale al tipo `E`;
>- `E` è un sottotipo derivato pubblicamente da `T`;
>- `T` è un tipo puntatore `B*` ed `E` è puntatore a tipo `D*` dove `D` è classe derivata da `B`;
>- `T` è un riferimento `B&` ed `E` è tipo riferimento `D&` dove `D` è una classe derivata da `B`;
>- `T` è il tipo `void*` ed `E` è un qualsiasi tipo di puntatore;
>- Non possono essere applicate conversioni implicite
>
>Notiamo quindi che a causa delle regole 2 e 3, se si definisce una `catch` che cattura un oggetto di una classe base prima di una `catch` che cattura un oggetto di una sua classe derivata si commette un errore logico di progettazione. Infatti, la `catch` della classe base catturerà tutti gli oggetti delle classi derivate e quindi la `catch` della classe derivata non potrà mai essere eseguita.

Le `catch` possono essere definite in svariati modi a seconda del contesto e delle esigenze.
Alcuni comportamenti tipici sono i seguenti:
- Esaminare l'errore ed invocare `terminate()`;
- Rilanciare un'eccezione
- Convertire un tipo di eccezione ad un altro, lanciando un'eccezione diversa;
- Cercare di ripristinare il funzionamento, in modo che il programma possa continuare dal punto di programma che immediatamente segue l'ultima `catch`;
- analizzare la situazione che ha causato l'errore, eliminare eventualmente la causa e riprovare a chiamare la funzione che ha causato originariamente l'eccezione;
- restituire un valore di stato all'ambiente;
