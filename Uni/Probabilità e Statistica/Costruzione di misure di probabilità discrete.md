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

^3a9c55

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
---

>[!example] ### Esempio Pratico

#### Estrazione di $n$ palline **con reinserimento**.

-> **Modello**: palline numerate $1...N$,  $M$ = numero di palline rosse.
$$
\Omega := \{ 1\dots N \}^n
$$
>Per simmetria: tutti i singoletti equiparabili -> $P$ [distribuzione uniforme discreta](Spazi%20di%20Probabilità#^71d2d9) su $\Omega$.

---

-> Eventi d'interesse:
$$
A_{k} = \text{"esattamente k delle n palline estratte sono rosse"}
$$
$$
A_{k} = \left\{  w \in \Omega : \sum_{i = 1}^n \perp_{\{ 1\dots M \}} (w_{i}) = k  \right\} = \{ w \in \Omega : \#\{ i \in \{ 1\dots n \} : w_{i} \in \{ 1\dots M \} \} = k \}
$$
$$
P(A_{k}) = \frac{|A_{k}|}{|\Omega|}
$$

--- 

-> Calcolare $|A_{k}|$:
Gli elementi di $A_k$ sono univocamente determinati da tre *scelte successive*:
1. scelta delle posizioni delle $k$ palline rosse (quindi anche delle verdi)-> $\binom{n}{k}$ alternative
   > Ricorda:
   > $$
   > \binom{n}k = \frac{n!}{k!(n-k)!} = \binom{n}{n-k}
    $$

2. scelta dei numeri delle $k$ palline rosse -> $M^k$ alternative
3. scelta dei numeri delle $n-k$ palline verdi -> $(N - M)^{n-k}$ alternative

>[!important] Principio fondamentale del calcolo combinatorio #Definizione 
>$$
>|A_{k}| = \binom{n}{k} \cdot M^k \cdot (N - M)^{n-k}
>$$

^47c391

Dato che $\Omega = N^n$ e $N^n = N^k \cdot N^{n-k}$ allora abbiamo che:
$$
P(A_{k}) = \binom{n}{k} \cdot \left( \frac{M}{N} \right)^k \left(\frac{N-M}{N}\right)^{n-k}
$$
>[!info] Nota:
> - $\frac{M}{N}$-> proporzione di palline rosse nell'urna
> - $\frac{N - M}{N} = 1 - \frac{M}{N}$ -> proporzione delle palline verdi

Da cui possiamo ottenere una [densità discreta](#^3a9c55) su $\{ 0 \dots n \}$
$$
\tilde{p}(k) := \binom{n}k \cdot \left(\frac{M}{N}\right)^k \cdot \left(\frac{N-M}{N}\right)^{n-k}
$$

---

#### Estrazione di $n$ palline **senza** reinserimento

-> $\Omega = \{ 1\dots N \}^n$ come prima, $P$ distribuzione uniforme su $\Omega$
Stavolta però poniamo $\tilde{\Omega} = \{ w \in \Omega : w_{i} \ne w_{j}, i \ne j \}$ -> esiti senza ripetizioni di numeri (ovvero palline diverse).
Definiamo una nuova misura di probabilità:
$$
\tilde{P}(A) := \frac{P(A \cap \tilde{\Omega})}{P(\tilde{\Omega})} \quad A \subseteq \Omega
$$
Dalla definizione di $P$ otteniamo che:
$$
\tilde{P}(A) = \frac{|A \cap \tilde{\Omega}|}{|\tilde{\Omega}|} \quad A \subseteq \Omega
$$
> Nota: $\tilde{P}(\Omega) = 1$

---

>[!question] $\tilde{P}(A_{k}) =?$ -> Quanto vale la probabilità di esattamente $k$ palline rosse rispetto a $\tilde{P}$?

> Nota:
> $$
> A_{k} \cap \tilde{\Omega} = \left\{  w \in \tilde{\Omega} : \sum_{i = 1}^n \perp_{\{ 1\dots M \}}(w_{i}) = k  \right\} $$

---

Non ci resta ora che da calcolare:
- $|\tilde{\Omega}|$
- $|A_{k} \cap \tilde{\Omega}|$

-> $|\tilde{\Omega}| = N \cdot (N - 1) \dots  (N - n + 1) = \frac{N!}{(N-n)!}$
-> Gli elementi di $A_{k} \cap \tilde{\Omega}$ sono determinati da 3 scelte successive:
1. scelte delle posizioni delle palline rosse (quindi anche di quelle verdi)
   $$
\binom{n}{k} = \frac{n!}{k! (n-k)!} = \binom{n}{n-k}
   $$
2. scelte dei numeri delle $k$ palline rosse:
   $$
   \frac{M!}{(M-k)!} \text{ alternative (numeri senza ripetizioni!!!!)}
   $$
3. scelta dei numeri delle $n - k$ palline verdi:
   $$
   \frac{(N-M)!}{[N-M-(n-k)!]}
   $$

Ora applico il [principio fondamentale del calcolo combinatorio](#^47c391) e ottengo:
$$
|A_{k} \cup \tilde{\Omega}| = \binom{n}{k} \cdot \frac{M!}{(M-K)!} \cdot \frac{(N - M)!}{[N-M-(n-k)!]}
$$
Da cui ottengo:
$$
\tilde{P}(A_{k}) = \frac{(N-n)!n! \cdot M! \cdot(N-M)!}{N!k!(n-k)! \cdot (M-k)! \cdot [N - M - (n - k)]!} = \frac{\binom{M}{k} \cdot \binom{N - M}{n - k}}{\binom{N}{n}}
$$
Poniamo
$$
\tilde{p}(k) = \tilde{P}(A_{k}) =\frac{\binom{M}{k} \cdot \binom{N - M}{n - k}}{\binom{N}{n}} \quad k\in \{ 0,\dots, n \land M\}
$$
Allora $\tilde{p}$ densità discreta su $\{ 0,\dots, n \land M \}$.
Infatti:
$$
\tilde{\Omega} = \bigcup_{k =0}^{n\land M}(A_{k} \cap \tilde{\Omega}) \quad \text{e} \quad A_{i} \cap A_{j} = \emptyset \quad \text{se} \quad i \ne j
$$
Questo implica:
$$
1 = \tilde{P}(\tilde{\Omega}) = \sum_{k = 0}^{n \land M}\tilde{P}(A_{k}\cap \tilde{\Omega}) = \sum_{k=0}^{n \land M}\tilde{P}(A_{k})
$$
> Nota:
> Il passaggio:
> $$\tilde{P}(\tilde{\Omega}) = \sum_{k=0}^{n \land M}\tilde{P}(A_{k} \cap \tilde{\Omega}) $$
> È derivato dalla regola $A_{\sigma}$ vista nella definizione di spazio di misura di probabilità
> Mentre:
> $$1 = \tilde{P}(\tilde{\Omega})$$
 > È la definizione di misura di probabilità.