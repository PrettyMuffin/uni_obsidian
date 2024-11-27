Tags: [[P.A.O]] [[Essenziali]]
## SottoClassi
Possiamo usare la classe `orario` per definire una nuova classe `dataora` che eredita da essa tutte le proprietà di `orario` ed a cui attribuiamo le ulteriori proprietà che ci interessano per modellare il concetto di orario con data.
```cpp title:dataora.h
class dataora: public orario {
public:
	int Giorno() const;
	int Mese() const;
	int Anno() const;
private:
	int giorno, mese, anno;
};
```
diciamo dunque che la classe `dataora` è *derivata* della classe `orario`.

>[!def] Terminologie analoghe #Definizione 
>Diremo che `orario` è una \__ e `dataora` è una \_\_:
>- classe base; classe derivata;
>- superclasse; sottoclasse;
>- supertipo(diretto); sottotipo (diretto)

La parola chiave `public` si tratta di uno specificatore di accesso che precede il nome della classe base, ed indica una *derivazione pubblica*.
 ==Ciò significa che tutti i membri della classe base vengono implicitamente ereditati dalla classe derivata, che li può usare liberamente e direttamente come fossero membri propri.==
 Nella maggior parte dei casi la *classe derivata* avrà ulteriori membri propri oltre a quelli ereditati. ^8c15ae

>[!def] **Caratteristica fondamentale dell'ereditarietà** #Definizione 
>Ogni oggetto della classe derivata è utilizzabile anche come oggetto della classe base.
In altri termini, ogni oggetto di una classe derivata può essere convertito implicitamente in un oggetto di una classe base.

>[!def] Relazione **is-a** #Definizione 
> Un oggetto di un sottotipo di `T`, è *in particolare* anche di tipo `T`.

^9ec763

>[!def] Gerarchia di classi #Definizione 
Dalla relazione *is-a* si può dedurre anche che una classe `D` derivata direttamente da una classe base `B` può a sua volta agire da classe base per qualche classe `E` che derivi direttamente da `D`.

>[!note] Conversioni implicite
>In ogni [gerarchia di classi](#Gerarchie%20di%20classi), le conversioni implicite da sottotipo a supertipo valgono lungo tutta la gerachia:
>Dato `B`: supertipo, `D`: sottotipo
>- [D ==> B](#^9ec763) (tra oggetti);
>- D& ==> B&;
>- D* ==> B*;
>==Il viceversa non vale==.

Se `D` sottotipo di `b`, `b` è un oggetto di tipo `B` e `d` un oggetto di tipo `D` allora l'assegnazione `b = d`; estrae da `d` il sottooggetto di tipo `B` che esso contiene ingorando quindi l'ulteriore informazione contenuta in `d` specifica della sottoclasse `D`.
==D'altronde, la parte di `d` specifica della classe `D`, ovvero quella non ereditata da `B`, non può essere contenuta nella memoria allocata per contenere l'oggetto `b`.==
Quindi, in C++, quando si parla di polimorfismo si intende principalmente la capacità di un puntatore o riferimento ad una classe base `B` di riferirsi a una qualsiasi classe derivata da `B`.
### Tipo Statico e Tipo dinamico
Supponiamo che `D` è sottoclasse di `B`.
```cpp
D d; B b;
D* pd = &d; 
B* pb = &b;
pb = pd;
```

>Il tipo `B*` viene anche detto *il tipo statico*[^1] del puntatore `pb`. #Definizione 

Dopo l'assegnazione `pb = pd`; che è legale per polimorfismo, `pb` punta all'oggetto `d` della classe `D`: di dice quindi che il *tipo dinamico*[^2] di `pb` dopo una tale assegnazione è `D*`.

Mentre il tipo statico è determinato dalla dichiarazione del puntatore e non cambia, il tipo dinamico di un puntatore può quindi variare a run-time.

>[!info]
>La notazione di tipo dinamico è un mero concetto astratto che si usa per ragionare logicamente sull'evoluzione dinamica del tipo degli oggetti a cui puntano le variabili puntatore.
>==Sia per il compilatore che per il sistema run-time che gestisce l'esecuzione di un programma, ogni puntatore ha solo e solamente il tipo con cui è stato dichiarato nel programma, ovvero il suo tipo statico.==

> Concetti e terminologia sono analoghi per i riferimenti

```cpp
D d;
B b;
D& rd = d;
B& rb = d;
```

### Gerarchie di classi
Abbiamo già osservato che una classe derivata può a sua volta essere usata come classe base per un ulteriore passo di derivazione.
>[!example]
>```cpp
>// tpo enumerazione settimana
>enum settimana { lun, mar, mer, gio, ven, sab, dom};
>class dataorasett : public dataora {
>public:
>	settimana GiornoSettimana() const;
>private:
>	settimana giornosettimana;
>};
>```
>In questo modo abbiamo definito una semplice gerarchia di tre classi, che rappresentiamo tramite la seguenti figura: detta *diagramma della gerarchia* #Definizione 
>```mermaid
>
>flowchart TD
>
>n1["dataorasett"] --> n2["dataora"] --> n3["orario"]
>
>```

### Accessibilità
>[!question] Una classe derivata ha accesso alla parte [privata](Classi#Private) di una sua classe base?
>NO.
>La parte privata di una qualsiasi classe `B` è inaccessibile alle classi derivate da `B` come lo è per ogni altra classe diversa da `B`.
>```cpp error:2
>dataora::set2000() {
>sec = 0; // NO, illegale!
>giorno = 1;
>mese = 1;
>anno = 200;
>}
>```

Questo si risolve ponendo [protected](Classi#Protected) il sec. 
Dunque la classe `dataora` è ottenuta per [derivazione pubblica](#^8c15ae): `class dataora : public orario`.
Con la derivazione pubblica i campi [protetti](Classi#Protected) e [pubblici](Classi#Public) della classe base mantengono lo stesso livello di accessibilità anche nella classe derivata.

> Oltre alla derivazione pubblica:
> - *Derivazione Privata*: utilizza `private`, rende privati nella classe derivata i membri protetti e pubblici della classe base.
> - *Derivazione Protetta*: utilizza `protected`, rende protetti nella classe derivata i mebri pubblici e protetti della classe base.
> >[!info] N.B:
> >Entrambe le derivazioni non hanno effetto sui campi e metodi [privati](Classi#Private).
> a
> 
|Membro / Derivazione| public |  protected | private |
| --- | --- | --- | --- |
|`private` | inaccessibile | inaccessibile | inaccessibile|
|`protected`| protetto | protetto | privato |
|`public`| pubblico | protetto | privato |

La forma senz'altro più diffusa è quella `public`, detta anche **ereditarietà di tipo**. 
Questo perché permette di realizzare la relazione [is-a](#^9ec7639) => ==Un oggetto di classe derivata è anche un oggetto di classe base==.

>[!important] Concetti di membro protetto
>È importante osservare che il concetto di membro protetto di una classe va contro il [principio dell'information hiding](Programmazione%20Ad%20Oggetti#^f42cc0) (perché la modifica di un membro privato potenzialmente potrebbe richiedere la successiva modifica di tutte le classi derivate).
>Dunque il l'attributo *protected* va usato con parsimonia ==> la prassi generale è quella di dichiarare campi dati privati.

##### Ereditarietà privata vs Relazione has-a
>[!def] Ereditarietà Privata
>Significa "essere implementati in termini di".
>De `D` deriva privatamente da `B` significa che in `D` si è interessati ad alcune funzionalità di `B` e non si è invece interessati ad una relazione concettuale di subtyping tra `D` e `B`.



[^1]:si riferisce al fatto che il tipo è staticamente derivabile dal codice sorgente, ossia viene determinato dal compilatore.
[^2]:si riferisce al fatto che il tipo è deducibile solamente a run-time.