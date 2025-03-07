>[!def] Misura di Probabilità su $F$
>Sia $F$ una $\sigma$-algebra in $\Omega$. Una mappa $P:F \rightarrow [0,1]$ si dice misura di probabilità su $F$ se:
>1. $P(\Omega) = 1$
>2. Se $(A_{i})_{i\in \mathbb{N}} \subset F$ è una successione di eventi disgiunti due a due, cioè $A_{i} \cap A_{j} = \emptyset$ con $i \ne j$ allora:
>   $$
>   P(\bigcup_{i\in \mathbb{N}}A_{i}) = \sum_{i \in \mathbb{N}} P(A_{i})
>   $$
>>[!note] Osservazione
>>(1.) $\Rightarrow$ addittività finita: $P(A \cup B) = P(A) + P(B)$
>>(2.) $\Rightarrow$ $P(\emptyset) = 0$