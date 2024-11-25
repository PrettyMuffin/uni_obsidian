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
 Nella maggior parte dei casi la *classe derivata* avrà ulteriori membri propri oltre a quelli ereditati.

>[!def] **Caratteristica fondamentale dell'ereditarietà** #Definizione 
>Ogni oggetto della classe derivata è utilizzabile anche come oggetto della classe base.
In altri termini, ogni oggetto di una classe derivata può essere convertito implicitamente in un oggetto di una classe base.

>[!def] Relazione **is-a** #Definizione 
> Un oggetto di un sottotipo di `T`, è *in particolare* anche di tipo `T`.

^9ec763

>[!def] Gerarchia di classi #Definizione 
Dalla relazione *is-a* si può dedurre anche che una classe `D` derivata direttamente da una classe base `B` può a sua volta agire da classe base per qualche classe `E` che derivi direttamente da `D`.

>[!note] Conversioni implicite
>In ogni gerarchia di classi, le conversioni implicite da sottotipo a supertipo valgono lungo tutta la gerachia:
>Dato `B`: supertipo, `D`: sottotipo
>- [D ==> B](#^9ec763) (tra oggetti);
>- D& ==> B&;
>- D* ==> B*;
>==Il viceversa non vale==.

Se `D` sottotipo di `b`, `b` è un oggetto di tipo `B` e `d` un oggetto di tipo `D` allora l'assegnazione `b = d`; estrae da `d` il sottooggetto di tipo `B` che esso contiene ingorando quindi l'ulteriore informazione contenuta in `d` specifica della sottoclasse `D`.
==D'altronde, la parte di `d` specifica della classe `D`, ovvero quella non ereditata da `B`, non può essere contenuta nella memoria allocata per contenere l'oggetto `b`.==
Quindi, in C++, quando si parla di polimorfismo si intende principalmente la capacità di un puntatore o riferimento ad una classe base `B` di riferirsi a una qualsiasi classe derivata da `B`.


