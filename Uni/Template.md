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

