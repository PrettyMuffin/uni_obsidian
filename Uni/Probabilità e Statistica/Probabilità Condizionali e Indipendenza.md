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
## Definizione

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
> Ora $|E_{1}| = 6^2 = |E_{2}| = |E_{3}|$ -> $P(E_{1}) = P(E_{2}) = P(E_{3}) = \frac{6^2}{6^3} = \frac{1}{6}$
> Dall'altra parte: $|E_{1} \cap E_{2}| = 6 =|E_{1} \cap E_{3}| = |E_{2} \cap E_{3}|$ -> $P(E_{1} \cap E_{2}) = P(E_{1} \cap E_{3}) = P(E_{2} \cap E_{3}) = \frac{6}{6^2} = \frac{1}{36}$
> -> $E_{1}, E_{2}, E_{3}$ sono indipendenti **a due a due**
> ---
> Sia $D$ = "il terzo dado dà un numero dispari" -> $D = \{ w \in \Omega : w_{3} \in \{ 1, 3,5 \} \}$ -> $|D| = 3 \cdot 6^2$ -> $P(D) = \frac{3 \cdot 6^2}{6^3} = \frac{1}{2}$
> Ora $|E_{1} \cap D| = 3 \cdot 6 = |E_{2} \cap D|$ -> $P(E_{1} \cap D) = P(E_{2} \cap D) = \frac{3 \cdot 6}{6^3} = \frac{1}{12}$ ma $\frac{1}{12} = P(E_{1}) \cdot P(D) = P(E_{2}) \cdot P(D)$ -> $E_{1}, D$ e anche $E_{2}, D$ sono indipendenti
> Invece:
> $E_{3} \cap D = \emptyset \to P(E_{3} \cap D) = 0$ mentre $P(E_{3}) \cdot P(D) = \frac{1}{12} \ne P(E_{3} \cap D)$ -> $E_{3}, D$ **non** indipendenti.

>[!example] **Indipendenza di più di due eventi**
>Nell'esempio di prima:
>- $A$ = "primo e secondo dado danno lo stesso numero" -> $A=\{ w \in \Omega : w_{1} = w_{2} \}$
>- $B$ = "secondo e terzo dado danno lo stesso numero" -> $B = \{ w \in \Omega : w_{2} = w_{3} \}$
>- $C$ = "primo e terzo dado danno lo stesso numero" -> $C = \{ w \in \Omega : w_{1} = w_{3} \}$
>
>$|A| = |B| = |C| = 6^2$ -> $P(A) = P(B) = P(C) = \frac{1}{6}$ $|A \cap B| = |A \cap C| = |B \cap C| = 6$ 
>-> $P(A) = P(B) = P(C) = \frac{1}{6}$ $|A \cap B| = |A \cap C| = |B \cap C| = 6$
>-> $P(A \cap B) = P(A \cap C) = P(B \cap C) = \frac{6}{6^3} = \frac{1}{36}$
>-> $P(A \cap B) = P(A) \cdot P(B)$
>-> $A, B, C$ sono indipendenti a due a due
> ---
> Qui però: $|A \cap B \cap C| = 6$
> -> $P(A \cap B \cap C) = \frac{6}{6^3} = \frac{1}{36}$
> -> $P(A \cap B \cap C) \ne P(A) \cdot P(B) \cdot P(C)$
> -> $A, B, C$ **non** indipendenti a tre / come famiglia


## Indipendenza di Famiglie di Eventi

>[!def] **Indipendenti Come Famiglia** #Definizione 
>Siano $A_{1}, \dots, A_{n}$ eventi in uno spazio di probabilità $(\Omega, F, P)$ dove $n\geq 2$.
>Allora $A_{1}, \dots, A_{n}$ si dicono _indipendenti come famiglia_ se per ogni scelta di $\mathcal{J}\subseteq \{ 1,\dots, n \} \text{ con } \mathcal{J}\neq \emptyset$ si ha:
>$$
>P\left( \bigcap_{j\in \mathcal{J}}A_{j} \right) = \prod_{j \in \mathcal{J}}P(A_{j})
>$$
>---
>Funziona analogamente per famiglie arbitrarie di eventi: ovvero abbiamo $(A_{i})_{i \in \mathcal{I}} \subseteq F$ una famiglia di eventi dove $\mathcal{I} \neq \emptyset$.
>Allora $(A_{i})_{i \in \mathcal{I}}$ si dice **indipendente** come famiglia (oppure **famiglia di eventi indipendenti**) se per ogni scelta di $\mathcal{J} \subseteq \mathcal{I}$ **finito** non-vuoto si ha:
>$$
>P\left( \bigcap_{j \in \mathcal{J}} A_{j} \right) = \prod_{j \in \mathcal{J}}P(A_{j})
>$$
>cioè gli eventi $A_{j}, j\in \mathcal{J}$, sono indipendenti.
>>[!warning] **Attenzione**
>>Nella condizione equivalente, si perdono sempre intersezioni di $n$ eventi, ma ==bisogna guardare tutte le combinazioni degli insiemi== $B_{i}$ con $B_{i} = A_{i}$ o $B_{i} = A^c_{i}$.
>>La condizione $P(A_{1} \cap \dots \cap A_{n}) = P(A_{1}) \cdot \dots \cdot P(A_{n})$ **NON** è dunque sufficiente per l'indipendenza di $A_{1} \cdot \dots \cdot A_{n}$ se $n \geq 3$

## Modello matematico per $n$ prove ripetute e indipendenti
>[!def] **Modello per $n$ prove ripetute indipendenti**
>Sia $q \in [0,1]$, e sia $n \in \mathbb{N}$.
>Uno spazio di probabilità $(\Omega, F, P)$ con eventi $C_{1}\dots C_{n}$ si dice un _modello per $n$ prove ripetute e indipendenti_  con probabilità di successo $q$ se:
>1. $C_{1} \dots C_{n}$ sono indipendenti come famiglia;
>2. $P(C_{i}) = q \quad \forall i \in \{ 1\dots n \}$
>
>Ovvero la definizione di _eventi indipententi ed equiprobabili_.

### Nota:
>[!Important] Un modello per $n$ prove ripetute e indipendenti con probabilità di successo $q \in [0,1]$ si può costruire esplicitamente:

$$
\begin{align}
\Omega &= \{ 0,1 \}^n \\
F &= P(\Omega)
\end{align}
$$
$P$ definita mediante la sua densità discreta $p$:
###### Caso $n = 1$
$$
p(w) =
\begin{cases}
q &\text{se } w = 1 \\
1 - q &\text{se } w = 0
\end{cases}
\quad w \in \{ 0,1 \}
$$

###### Caso Generale
Per $\overline{w} = (w_{1}\dots w_{n}) \in \Omega = \{ 0, 1 \}^n$
$$
\begin{align}
p(w) &= q^{k(w)} \cdot (1 - q)^{n - k(w)} \text{ dove } \\
k(w) &= \# \{ i \in \{ 1 \dots n \} : w_{i} = 1 \} \to \text{numero di successi}
\end{align}
$$

Gli eventi $C_{1}\dots C_{n}$ in questo modello:
$$
C_{i} = \{ w \in \Omega: w_{i} = 1 \} \text{ "l'i-esima prova ha successo"}
$$
-> $C_{1} \dots C_{n}$ sono indipendenti come famiglia rispetto alla misura di probabilità $P$ e $P(C_{i}) = q$

**Infatti**:
$$
\begin{align}
P(C_{i}) &= \sum_{w \in C_{i}} p(w) \\
&=\sum_{\tilde{w} \in \{ 0, 1 \}^{n-1}} p((1, \tilde{w}))
\end{align}
$$
>NOTA BENE:
>$$
>\begin{align}
p((1, \tilde{w})) &= q^{k((1, \tilde{w})} \cdot (1-q)^{n - k((1, \tilde{w}))} \\
&= q \cdot q^{k(\tilde{w})} \cdot (1 - q)^{n - 1 - k(\tilde{w})}
\end{align}
>$$

Ora:
$$
\begin{align}
\sum_{w \in \{0, 1\}^{n - 1}} q^{k(\tilde{w})} \cdot (1 - q)^{n - 1 -k(\tilde{w})} \\
\sum_{k = 0}^{n - 1}\sum_{w \in \{ 0,1 \}^{n - 1} : k(\tilde{w}) = k}q^k \cdot (1 - q)^{n - 1 - k}
\end{align}
$$

Pag 14 lezione 8