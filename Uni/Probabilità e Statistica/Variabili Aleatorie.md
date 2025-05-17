>[!def]- **Variabili Aleatorie** #Definizione 
>Sono delle quantità che dipendono dall'esito di un esperimento aleatorio; sono quindi delle funzioni definite sullo spazio campionario.
>>[!example] lancio di 3 dadi da gioco
>>$X_{1}$ numero segnato dal primo dado
>>$X_{2}$ numero segnato dal secondo dado
>>$X_{3}$ numero segnato dal terzo dado

Le variabili aleatorie permettono una descrizione più conveniente di eventi; inoltre, permettono operazioni algebriche.

--- 
Sia $X : \Omega \mapsto E$ una funzione, $E$ insieme non vuoto.
>[!def] **Antiimmagine** #Definizione 
Per $B \subseteq E$, l'**antiimmagine** di $B$ sotto $X$ è data da:
>$$
>X^{-1}(B) := \{ w \in \Omega : X(w) \in B \}
>$$
>> Notazione:
>> $$
>> \{ X \in B \} := X^{-1}(B) = \{ w \in \Omega : X(w) \in B \}
>> $$

Nell'esempio dei 3 dadi:
-> Poniamo $\Omega = \{ 1, \dots, 6 \}^3$: spazio campionario
- $X_{1}(w) = w_{1}$
- $X_{2}(w) = w_{2}$
- $X_{3}(w) = w_{3}$
  
Interpretazione come prima: $X_{i}$ numero segnato dal dato $i$-esimo.

> Nota:
> $X_{1}, X_{2}, X_{3}$ sono delle funzione definite su $\Omega$ a valori in $\{ 1 \dots 6 \}$, ma anche in $\mathbb{R}$ -> $X_{1}, X_{2}, X_{3}$ funzioni $\Omega \to \mathbb{R}$.

Sia $B \subseteq \mathbb{R}$. Allora:
$$
\{ X_{i} \in B \} = \{ w \in \Omega : w_{i} \in B\}
$$
è l'evento: "Il dado $i$-esimo segna un numero in $B$".

--- 

>[!def] **$\sigma$-algebra generata da $X$** #Definizione
>Sia $X: \Omega \mapsto E$ una funzione, $E \ne \emptyset$.
>Se $\epsilon$ è una $\sigma$-algebra in $E$, allora:
>$$
>\sigma(X) := \{ X^{-1}(B): B \in \epsilon \}
>$$
>è una $\sigma$-algebra in $\Omega$ -> la $\sigma$-algebra generata da $X$ (rispetto a $\epsilon$)
>

###### Nell'esempio dei 3 dadi
- $E = \mathbb{R}$
- $\epsilon = P(\mathbb{R})$
- $\sigma(X_{1}) = \{ A \times \{ 1\dots 6 \}^2 : A \subseteq \{ 1 \dots 6 \} \}$
- $\sigma(X_{2}) = \{ \{ 1\dots 6 \} \times A \times \{ 1\dots 6 \} : A \subseteq \{ 1 \dots 6 \} \}$
- $\sigma(X_{3}) = \{ \{ 1\dots 6 \}^2 \times A: A \subseteq \{ 1 \dots 6 \} \}$

Con $Z = X_{1} + X_{2} + X_{3}$ abbiamo invece:
$$
\sigma(Z) = \{ \bigcup_{i \in \mathcal{J}} A_{j} : \mathcal{J} \subseteq \{ 3 \dots 18 \} \}
$$
Dove $A_j = \{ w \in \Omega : w_{1} + w_{2} + w_{3} = j\}$.
Poiché, per $B \subseteq \mathbb{R}$, l'antiimmagine diventa
$$
\begin{align}
Z^{-1}(B) &= \bigcup_{j \in \{ 3 \dots 18 \}}Z^{-1}(\{ j \})  \\
Z^{-1}(\{ j \}) &= A_{j} \text{ se } j \in \{ 3 \dots 18 \}
\end{align}
$$