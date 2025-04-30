# Probabilità Condizionali
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
   Sia $(B_{i})_{i \in \mathcal{I}}$ una **partizione** al più numerabile di $\Omega$, cioè $\mathcal{I}$ al più numerabile, $B_{i} \cap B_{j} = \emptyset$ se $i \ne j$ e $\bigcup_{i \in \mathcal{I}} B_{i} = \Omega$ tale che $B_{i} \in F$ con $P(B_i) > 0, \forall i \in \mathcal{I}$.
   Allora $\forall A \in F$:
   $$
   P(A) = \sum_{i \in \mathcal{I}}P(A|B) \cdot P(B_{i})
   $$
   > Nota:
   > Se $B \in F$ è tale che $P(B) \in (0,1)$, allora:
   > $$P(A) = P(A|B) \cdot P(B) + P(A|B^c) \cdot (1-P(B)) \quad \forall A \in F$$ 
   > >[!warning] **Attenzione:**
   > >Si ha sempre:
   > >$$P(A^c | B) = 1 - P(A|B)$$
   > >Ma in generale:
   > >$$P(A|B^c) \ne 1 - P(A|B)$$


>[!teo] **Teorema di Beyes** #Definizione #Teorema
> Siano $A, B \in F$ eventi in $(\Omega, F, P)$ con $P(A) > 0$, $P(B) > 0$.
> Allora:
> $$
> P(A|B) = \frac{P(A)}{P(B)} \cdot P(B|A)
> $$
> >[!info]- Dim:
> >$$
> >P(A|B) = \frac{P(A \cap B)}{P(B)} = \frac{P(A)}{P(B)} \cdot \frac{P(A \cap B)}{P(A)} = \frac{P(A)}{P(B)} \cdot P(B|A)
> >$$
> 
> >[!cor] **Corollario**:
> >Siano $A, B \in F$ con $P(A) > 0$ e $P(B) \in (0,1)$. Allora:
> >$$
> >P(B|A) = \frac{P(A|B) \cdot P(B)}{P(A|B) \cdot P(B) + P(A | B^c) \cdot (1 - P(B))}
> >$$

---
# Indipendenza

>[!def] **Eventi Indipendenti** #Definizione 
>Siano $A, B$ eventi in uno spazio di probabilità $(\Omega, F, P)$.
>Allora $A, B$ si dicono **indipendenti** se:
>$$
>P(A \cap B) = P(A) \cdot P(B)
>$$
>>[!info] Osservazioni:
>>1. In generale l'indipendenza degli eventi dipende dalla misura di probabilità.
>>2. Se $P(A) \in \{ 0, 1 \}$ oppure $P(B) \in \{ 0, 1 \}$ -> $A, B$ sono indipendenti
>>3. Se $P(A) > 0$ e $P(B) > 0$ e $A \cap B = \emptyset$ -> A, B **non** indipendenti
>>4. Se $A, B$ sono indipendenti -> lo sono anche:
>>     $A, B^c$
>>     $A^c, B$
>>     $A^c, B^c$
>>5. Se $P(B) > 0$ allora $A, B$ indipendenti <-> $P(A|B) = P(A)$  

---
>[!example] **Lancio di 3 dadi regolari 6 facce**
>-> Modello: $\Omega = \{ 1\dots 6 \}^3$
>-> $F = P(\Omega )$ dunque [distribuzione uniforme discreta](Spazi%20di%20Probabilità#^71d2d9) su $\Omega$ 
> Consideriamo gli eventi:
> - $E_{1}$ = "Il primo dado segna 3" -> $E_{1} = \{ w \in \Omega : w_{1} = 3 \}$ 
> - $E_{2}$  = "Il secondo dado segna 2" -> $E_{2}= \{ w \in \Omega : w_{2} = 2 \}$
> - $E_{3}$ = "Il terzo dado segna 4" -> $E_{3} = \{ w \in \Omega : w_{3} = 4 \}$
>
> $P$ è una distribuzione uniforme -> $P(A) = \frac{|A|}{|\Omega|}$ dove $|\Omega| = 6^3 = 216$.
> 