Tags: [[P.A.O]]
La notazione `()` ereditata dal C per le *conversioni esplicite* è mantenuta per scopi di compatibilità in C++ ed è illustrata qui:
>[!example] Explicit Cast
>```cpp
>float x; char c; void* p;
>int i = (int) x;
>float y = (float) y;
>nodo* q = (nodo*) p;
>```

Queste operazioni sono potenzialmente "pericolose", in quanto è possibile che si perdano informazioni da un cast all'altro.

---

La sintassi del cast di C++ è del tipo
```txt
nome_cast <Tipo> (expr)
```
in cui:
- `nome_cast`: indica la tipologia di cast che può essere:
  - [const](#Const%20Cast)
  - [static](#Static%20Cast)
  - [reinterpreted](#Reinterpreted%20Cast)
  - [dynamic](#Dynamic%20Cast)
- `<Tipo>`: denota il tipo in cui deve essere convertito il valore di `(expr)`. Viene detto *tipo target* del cast.

## Const Cast 
==**Razato ti incula se lo fai**==.
Il `const_cast` ti permette di convertire un puntatore/riferimento di tipo `const T` ad un puntatore/riferimento `T` ==(rimuovendo dunque l'attributo `const`)== 
###### Sintassi
```txt
const_cast <T*> (puntatore costante)
const_cast <T&> (riferimento costante)
```

## Static Cast
Lo `static cast` permette di rendere esplicito l'uso di tutte le conversioni, implicite o meno, previste e/o permesse dal linguaggio o definite dal programmatore. Tutte le conversioni oggetto dell static cast si basano su informazione di tipo statica, cioè disponibile a compile-time.
Lo `static_cast` permette anche la conversione tra puntatori, in particolare quello di tipo `void*` a `Tipo*`.
>[!info]- Conversioni safe
>Qui sono riassunte le conversioni "safe" ovvero in cui non si perdono dati
>- `T&` ==> `T`
>- `T[]` ==> `T*`
>- `T` ==> `const T`
>- `T*` ==> `const T*`
>- `float` ==> `double` ==> `long double`
>- `char` ==> `short` ==> `int` ==> `long`
>- `unsigned char` ==> `...` ==> `unsigned long`
###### Sintassi
```txt
static_cast <Tipo> (expr)
```

## Reinterpreted Cast
###### Sintassi
```txt
reinterpreted_cast <T*> (puntatore)
reinterpreted_cast <T&> (puntatore)
```
Il `reinterpreted_cast` si limita a reinterpretare a basso livello la sequenza di bit con cui è rappresentato il valore puntato da `puntatore` come fosse una valore di tipo `T`.

## [Dynamic Cast](Ereditarietà#dynamic_cast)
Nel caso del `dynamic_cast` il "tipo dinamico" di `puntatore`/`riferimento` non è noto a
tempo di compilazione ma soltanto a tempo di esecuzione.
###### Sintassi
```txt
dynamic_cast <T*> (puntatore)
dynamic_cast <T&> (puntatore)
```