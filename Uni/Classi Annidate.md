Tags: [[P.A.O]] 

>[!def] Classe Annidata #Definizione 
>Una classe definita internamente ad un'altra classe `C` viene detta *classe annidata* o *classe interna*.
>
>

La classe interna `A` può essere [pubblica](Classi#Public) o [privata](Classi#Private) in particolare:
- se `A` è pubblica allora posso definire oggetti di `A` esternamente a `C` usando l'[operatore di scoping](Namespace#^11f9a8).
```cpp
C::A obj;
```

- Se `A` è privata, allora è inaccessibile esternamente a `C`

>[!attention] N.B.
>Nella classe annidata `A` posso usare solamente i membri statici della classe contenitrice `C`.
>I membri della classe `A` in `C` non hanno privilegi speciali di accesso ai membri di `C`, così come i membri di `C` non hanno privilegi speciali di accesso ai membri della classe `A`.

```cpp error:8-9 ok:10-11
int x; // variabile globale
class C {
public:
	int x;
	static in s;
	class A {
		void f(int i) {
			int z = sizeof(x); // x si riferisce a C::x
			x = i;  // x si riferisce a C::x
			s = i; // Ok assegnazione a variabile statica
			::x = i; // Ok assegnazione alla variabile globale x
		}
		void g(C* p, int i) {
			p->x = i;
		}
	};
};
```
