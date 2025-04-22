Se $\Omega \neq \emptyset$ è *al più numerabile* e $P$ è **misura di probabilità** su $F = P(\Omega)$, allora $\forall A \subseteq \Omega$,
$$
P(A) = \sum_{w \in A} P(\{ w \})
$$
$P$ è determinata dalle probabilità dei singoletti.
Ora basta scegliere una funzione $p: \Omega \mapsto [0,1]$ tale che $\sum_{w \in \Omega} p(w) = 1$ per determinare la misura di probabilità di $P$. Infatti $P(\{ w \}) := p(w) \quad w \in \Omega$.

>[!def] Densità Discreta #Definizione 
>Sia $\Omega \ne \emptyset$ arbitrario.
>Una funzione $p: \Omega \mapsto [0,1]$ tale che $\sum_{w \in \Omega} p(w) = 1$ si dice **densità discreta** su $\Omega$.
>>[!note] Nota:
>>L'insieme $\{ w \in \Omega : p(w) > 0 \}$ è al più numerabile anche quando $\Omega$ è più che numerabile.
>>>Infatti:
>>>$$
>>>\{ w \in \Omega: p(w) > 0 \} = \bigcup_{M \in \mathbb{N}}\left\{  w \in \Omega: p(w) \ge \frac{1}{M}  \right\}
>>>$$
>>>insieme di cardinalità minore o uguale M

Sia $\Omega \ne \emptyset$ arbitrario, e sia $F$ una $\sigma$-algebra in $\Omega$.
1. Se $p$ è una densità discreta su $\Omega$, allora
   $$
P(A) = \sum_{w \in A} p(w) \quad A \in F 
   $$
   Definisce una misura di probabilità su $F$: la **misura di probabilità indotta dalla densità discreta** $p$. #Definizione 
2. Se $\Omega$ è al più numerabile e $F$ contiene tutti i singoletti, allora $F = P(\Omega)$ e ogni misura di probabilità $P$ su $P(\Omega)$ corrisponde a una densità discreta.
   La corrispondenza biunivoca è data da:
    $$   p(w) := P(\{ w \}), w \in \Omega$$
    $$P(A) = \sum_{w \in A}p(w), A \subseteq \Omega$$ 

>[!example] Lancio di due monete non necessariamente equilibrate
>T = testa, C = croce.
>- Spazio Campionario: $\Omega = \{ T, C \} \times \{ T, C \} \rightarrow \Omega = \{ (T, T), (T, C), (C,T) , ( C, C) \}$
>
>###### Supponiamo che le due monete siano equilibrate
>Allora dobbiamo avere:
>$$
>P(\text{"prima moneta segna testa"}) = \frac{1}{2} = P(\text{"prima moneta segna croce"})
>$$
>$$
>P(\text{"seconda moneta segna testa"}) = \frac{1}{2} = P(\text{"seconda moneta segna croce"})
>$$
>Quindi
>$$
>P(\{ (T,T), (T, C) \}) = P(\{ (C, T), (C, C) \}) = \frac{1}{2}
>$$
>$$
>P(\{ (T, T), (C, T) \}) = P(\{ (T, C), (C, C) \}) = \frac{1}{2}
>$$
>Questo vale se $P$ è la distribuzione uniforme discreta, quindi densità discreta $p(w) = \frac{1}{4}, \forall w \in \Omega$.
>Ma non è l'unica soluzione.
>###### Supponiamo che la prima sia equilibrata, seconda truccata per far uscire solo testa
>Sia $P$ la misura di probabilità indotta dalla densità discreta:
>$$
>p((T,T)) = p((C, T)) = \frac{1}{2}, \quad p((T,C)) = p((C, C)) = 0
>$$
>Unica soluzione compatibile con ipotesi.

## Legame con la Statistica:
>[!def] Densità Campionaria #Definizione 
> Sia $(x_{i})_{i\in \{ 1\dots n \}}$ un campione d-variato di numerosità $n$.
> Definiamo $p: \mathbb{R}^d \mapsto [0,1]$ tramite:
> $$
> p(v) = \frac{\{ i \in \{ 1\dots n \}: x_{i} = v \}}{n} \quad v \in \mathbb{R}^d
> $$
> Allora $p$ è una densità discreta su $\mathbb{R}^d$, ovvero la **densità campionaria** di $(x_{i})_{i\in \{ 1\dots n \}}$.
> -> $p$ induce una misura di probabilità $P$ su $P(\mathbb{R}^d)$:
> $$
> P(A)= \sum_{v \in A} p(v) \quad A \subseteq \mathbb{R}^d
> $$
> -> $P$ ==misura campionaria== (empirica).
> >[!note] Nota:
> >1. $\{ v \in \mathbb{R}^d : p(v) > 0 \} = \{ x_{i} : i \in \{ 1\dots n \} \}$
> >2. $p(v)$ è la *frequenza relativa* del valore $v \in \mathbb{R}^d$

##### Proprietà fondamentali delle misure di probabilità
Sia $(\Omega, F, P)$ uno spazio di probabilità, e siano $A, B \in F$.
1. Se $A \subseteq B$, allora $P(B \setminus A) = P(B) - P(A)$. In particolare $P(A^c) = 1 - P(A)$
2. $P(A \cup B) = P(A) + P(B) - P(A \cap B)$. In particolare, se $A \cap B = \emptyset$ allora $P(A \cup B) = P(A) + P(B)$.
3. Se $(A_{n})_{n \in \mathbb{N}} \subseteq F$, allora
   $$
P\left( \bigcup_{n \in \mathbb{N}} A \right) \le \sum_{n \in \mathbb{N}}P(A_{n})
 $$
In particolare se sono disgiunti due a due($A_{i} \cap A_{j} = \emptyset, i \ne j$) allora:
$$
P\left( \bigcup_{n \in \mathbb{N}}A_{n} \right) = \sum_{n \in \mathbb{N}}P(A_{n})
$$

4. Sia $(A_{n})_{n \in \mathbb{N}} \subseteq F$ tale che $A_{i} \cap A_{j} = \emptyset, i \ne j$ e $\bigcup_{n \in \mathbb{N}} A_{n} = \Omega$ allora:
   $$
P(B) = \sum_{n} P(B \cap A_{n}) 
 $$
 In particolare:
 $$
 P(B) = P(B \cap A) + P(B \cap A^c)
 $$
5. Sia $(A_{n})_{n \in \mathbb{N}} \subseteq F$.
   - Se $A_{n}$ è **crescente**, cioè $A_{n} \leq A_{n+1}, \forall n \in \mathbb{N}$ allora:
     $$
   P(\bigcup_{n}A_{n}) = \lim_{ n \to \infty } P(A_{n})
    $$
    - Se $(A_{n})$ è **descrescente**, cioè $A_{n} \geq A_{n+1}, \forall n \in \mathbb{N}$ allora:
      $$
      P(\bigcap_{n} A_{n}) = \lim_{ n \to \infty } P(A_{n})
    $$