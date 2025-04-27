## Definizione
Sia $(\Omega, F, P)$ uno spazio di probabilità
>[!def] Probabilità condizionale di **$\mathbf{A}$ dato $\mathbf{B}$**
>Sia $B \in F$ un evento con $P(B) > 0$.
>Per $A \in F$, la quantità:
>$$
>P(A|B) := \frac{P(A \cap B)}{P(B)}
>$$
>>[!nota]- Interpretazione
>>$P(A|B)$ è il grado di fiducia che si assegna all'evento $A$ supponendo che si sia verificato $B$.
>>> Nota:
>>> $$
>>> P(B|B) = 1 \quad \text{mentre} \quad P(B^c | B) = 0
>>> $$

>[!example]- Estrazioni di palline da un'urna **senza reinserimento**
>$$
>\text{modello per estrazioni con reinserimento} = 
\begin{cases}
>\Omega = \{ 1, \dots, N \} ^ n, \quad w=(w_{1},\dots,w_{n})\\
>P = \text{Distribuzione uniforme su } \Omega
>\end{cases}
>$$
>$\tilde{\Omega} = \{ w \in \Omega : w_{i} \ne w_{j} \text{ per } i \ne j\}$ -> evento che rappresenta l'estrazione di solo palline distinte.
>Abbiamo Posto:
>$$
>\tilde{P}(A) = \frac{P(A \cap \tilde{\Omega})}{P(\tilde{\Omega})} \quad A \subseteq \Omega
>$$
>La rispettiva probabilità condizionata di $A$ dato $\tilde{\Omega}$ è:
>$$
>\tilde{P}(A) = P(A|\tilde{\Omega})
>$$

## Proprietà delle probabilità condizionali
Sia $(\Omega, F, P)$ uno spazio di probabilità:
1. Sia $B \in F$ con $P(B) > 0$. Allora la mappa $\tilde{F} \to A \mapsto P(A|B) \in [0,1]$ definische una misura di probabilità su $\tilde{F}$ -> $(\Omega, \tilde{F}, P(.|B))$ è uno spazio di probabilità.
   In particolare: $P(A^c | B) = 1 - P(A|B)$
   > Nota:
   > Anche $(B, \tilde{F}_{B}, P(.|B))$ è uno spazio di probabilità, dove:
   > $$\tilde{F}_{B} = \{ A \cap B: A \in F \} \quad \text{è la }\sigma \text{ algebra indotta su }B $$

2. **Regola della catena / Regola di Moltiplicazione**: #Definizione 
   Siano $A_{1},\dots, A_{n} \in F$ eventi con $P(\bigcap_{i = 1}^n A_{i}) > 0$.
   Allora:
   $$
   P\left( \bigcap_{i =1}^n A_{i} \right) = P(A_{1}) \cdot \prod_{i = 2}^n P(A_{i} | A_{1} \cap \dots \cap A_{i - 1})
   $$
   In particolare se $P(A_{1} \cap A_{2}) > 0$ allora:
   $$
   P(A_{1} \cap A_{2}) = P(A_{1}) \cdot P(A_{2}| A_{1})
   $$
3. **Formula delle probabilità totali** (versione condizionale)