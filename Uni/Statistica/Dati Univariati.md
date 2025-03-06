Sia $(x_{i})_{i \in \{1,\dots,n\}}$ un campione univariato di numerosità $n$, allora:
$$
(x_{i})_{i \in \{1,\dots, n\}} = (x_{i,1}\dots x_{i,d}) \in \mathbb{R}^d \quad \forall i \in \{1,\dots,n\} 
$$
>[!note] Nota:
>Per $k \in \{1,\dots,d\}$, $(x_{i,k})_{i\in\{1,\dots,n\}}$ è campione univariato.

>[!important] Obbiettivo
>Misurare la dipendenza tra componenti: nel caso più semplice ho $d = 2$, ovvero campioni bivariati.

Sia ora $(x_{i},y_{i})_{i \in \{1, \dots, n\}}$ un campione bivariato, vogliamo la quantità che misura la dipendenza *linear-affine* tra le due componenti oppure due campioni univariati con la stessa numerosità.
>[!def] Covarianza Campionaria #Definizione 
>La **covarianza campionaria** tra $(x_i)$ e $(y_{i})$  è data da:
>$$
>cov_{x,y} = \frac{1}{n-1} \sum_{i = 1}^n(x_{i}-\overline{x}) \cdot (y_{i} - \overline{y})
>$$
>- $\overline{x}$: [media campionaria](Concetti%20Fondamentali#^33dd17) di $x_i$;
>- $\overline{y}$: [media campionaria](Concetti%20Fondamentali#^33dd17) di $y_i$;
>
>>[!note]
>>Se  $x_{i}=y_{i}\forall i$ allora: $cov_{x,y} = s_{x}^2 = s_{y}^2$
>
>
>>[!warning] Attenzione
>>La [varianza](Concetti%20Fondamentali#^6c6c18) di un campione è sempre $>0$, mentre $cov$ può essere negativa per campioni diversi

Per avere però una misura della dipendenza tra 2 campioni che ==non== dipende dall'unità di misura di quest'ultimi, si normalizza la covarianza usando le deviazioni standard [deviazione standard](Concetti%20Fondamentali#^dce63e).

>[!def] Correlazione Campionaria #Definizione 
>La **correlazione campionaria** tra $x_{i}$ $y_{i}$ è data da:
>$$
>corr_{x,y} = \frac{cov_{x,y}}{s_{x} \cdot s_{y}}
>$$
>- $s_x$: [deviazione standard](Concetti%20Fondamentali#^dce63e) di $x$;
>- $s_{y}$: [deviazione standard](Concetti%20Fondamentali#^dce63e) di $y$;

la correlazione campionaria si può anche scrivere come:
$$
corr_{x,y} = \frac{\sum_{i=1}^n(x_{i} - \overline{x}) \cdot (y_{i} - \overline{y})}{\sqrt{ \sum_{i=1}^n (x_{i} - \overline{x})^2 } \cdot \sqrt{ \sum_{i=1}^n(y_{i} - \overline{y}) }}
$$
dove il prefattore $\frac{1}{n-1}$ si semplifica.
>[!important] La correlazione campionaria assume valori compresi tra -1 e 1.
>In particolare:
>- $corr_{x,y} = 1 \Longleftrightarrow \exists a,b \in \mathbb{R} : a > 0 \quad \forall i \in \{1,\dots,n\}:y_{i}=a\cdot x_{i} +b$.
>  Una correlazione positiva $\rightarrow$ valori vanno nella stessa "direzione", ovvero al crescere di uno cresce anche l'altro.
>- $corr_{x,y} = -1 \Longleftrightarrow \exists a,b \in \mathbb{R} : a < 0 \quad \forall i \in \{1,\dots,n\}:y_{i}=a\cdot x_{i} +b$.
>  Una correlazione negativa $\rightarrow$ valori che vanno in "direzioni" opposte, ovvero al crescere di uno l'altro decresce, sempre seguendo però una retta.
>
>Dunque i due campioni hanno una correlazione di tipo lineare, la più forte, in quanto uno si può esprimere in funzione di una retta dell'altro.
>==La dipendenza *linear-affine* è massima se $|corr_{x,y}| = 1$ mentre la dipendenza *linear-affine* è minima se $corr_{x,y} = 0$.==

>[!def] Matrice di Covarianza #Definizione 
> Per un campione d-variato $(x_{i})_{i\in\{1\dots n\}}$ possiamo definire una **matrice di covarianza**:
> $$
> C = (C_{k,l})_{k,l \in \{1\dots d\}} \text{ con } c_{k,l} = cov_{x^{(k)}, x^{(l)}}
> $$
> - $x^{(k)} = (x_{ik})_{i\in \{1\dots n\}}$ 
> - $x^{(l)} = (x_{il})_{i\in \{1\dots n\}}$ 
>  
>Analogamente si definisce la matrice di correlazioni

La matrice di covarianza da una misura delle dipendenza *linear_affini*  tra le componenti di un campione d-variato.

> - $C$ è una matrice **simmetrica**;
> - $v^tCv \geq 0 \quad \forall v\in\mathbb{R}^d$ $\rightarrow$ $C$ è semidefinita positiva $\rightarrow$ $\exists \lambda_{1} \dots \lambda_{d} \in \mathbb{R}$ e vettori $v^{(1)}\dots v^{(n)} \in \mathbb{R}^d : \lambda_{1} \geq \dots \geq \lambda_{d} \geq 0$.
>   Il prodotto scalare $(v^{(k)}, v^{(l)}) = \delta_{k,l}$ $\rightarrow$ base ortonormale e $C = [v^{(1)}\dots v^{(d)}] \cdot diag(\lambda_{1}\dots \lambda_{n}) \cdot [v^{(1)}\dots v^{(d)}]^t$.
>   >[!note] Oss.
>   >Dato che ho gli autovalori ordinati in ordine crescente, i primi $n$ autovalori indicano quali direzioni sono le più significative della relazione, di conseguenza quali campioni sono i più influenti.