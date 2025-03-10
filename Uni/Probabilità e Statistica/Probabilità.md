Uno **Spazio di probabilità** è un modello matematico per esprimere esperimenti aleatori, ha 3 componenti:
1. Insieme di tutti gli esiti possibili $\Omega$.
2. Insieme di tutte le affermazioni possibili sull'esito dell'esperimento. **Sistema degli Eventi** #Definizione: sottoinsieme $F$ del sistema delle parti di $\Omega$, quindi $F \leq P(\Omega)$ 
   >[!note]- Scelta Standard
   > Se $\Omega$ è un insieme finito o numerabile[^1] allora $F=P(\Omega)$.
   > Nell'esempio del lancio del dado: $F=P(\{1\dots 6\})$ $\rightarrow$ $\#F = 2^6$ in quanto ogni elemento può essere scelto (1) o meno (0), 2 scelte possibili per ogni elemento, dato che ce ne sono 6 allora abbiamo $2 \cdot 2 \cdot2 \cdot2 \cdot2 \cdot 2 = 2^6$.
   > In generale un sottoinsieme $A \subseteq \Omega$ corrisponde all'affermazione "si è verificato $w \in A$".

3. Assegnazione di un "grado di fiducia" a ogni affermazione ammissibile. **Misura di probabilità** #Definizione: mappa $P:F\rightarrow[0,1]$ dove 1 indica il massimo grado di fiducia, 0 indica il minimo.
   La valutazione degli eventi tramite la misura di probabilità deve essere coerente a:
   - $P(\Omega) = 1$
   - Se $A,B \in F$ con $A \cap B = \emptyset$ $\rightarrow$ $P(A \cup B) = P(A) + P(B)$
    Dove $P(A) = \frac{\#A}{\#\Omega}$ 

>[!info]- Interpretazione Logica delle operazioni Insiemistiche
>Sia $A,B \subseteq \Omega$ con $\Omega$ non vuoto.
>- $A \cap B$: A e B
>- $A \cup B$: A  o B
>- $A^c = \Omega \setminus A$:  "Non A"
>- $B \setminus A = B\cap A^c$: B e non A"
>- $A \Delta B = (A \setminus B) \cup (B \setminus A)$: "o A o B" (o esclusivo)
>- $(A \setminus B)^c$: "Se A allora B"

## [[Misura Di Probabilità]]





[^1]: Ovvero c'è una corrispondenza 1 a 1 con i numeri naturali, dunque è contabile. Es i reali non è un insieme numerabile