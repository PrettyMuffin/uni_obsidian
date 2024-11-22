>[!def] Dichiarazione incompleta #Definizione 
>Una classe `C` può usare puntatori e riferimenti ad una classe `D` che non è definita, ma 
>che è dichiarata solo tramite una cosiddetta *dichiarazione incompleta*, ovvero una mera
>dichiarazione del nome della classe `D`.
>>[!example] Esempio
>>```cpp
>>class D;
>>
>>class C {
>>D* p;
>>D* m() { ... }
>>void n(D*) { ... }
>>D& f() { ... }
>>};
>>
>>class D {
>> // dichiarazione completa di D
>>};
>>```
