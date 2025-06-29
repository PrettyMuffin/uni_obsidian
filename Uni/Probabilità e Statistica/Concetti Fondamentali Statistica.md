# Teoria delle Probabilità
Studio di modelli matematici per "esperimenti aleatori"[^1]:
- Lancio di moneta;
- Condizioni meteo;
- esito votazioni;
- ...

## Statistica Descrittiva
La statistica descrittiva si pone come obbiettivo quello di presentare e riassumere i dati attraverso *statistiche*, cioè funzioni di dati.
Per prima cosa consideriamo i dati univariati, cioè successione finite (vettori) di numeri reali:
Sia $(x_{i})_{i \in \{1,\dots, n\} } \in \mathbb{R}$ l'insieme ordinato dei dati *campione* di *numerosità* $n$.

## Statistiche Elementari
### Statistiche al centro dei dati
#### Descrizione Standard
Sia $(x_{i})_{i \in \{1,\dots, n\} } \in \mathbb{R}$ un campione di dati.
>[!def] Media Campionaria #Definizione 
>Si dice **media campionaria** di $(x_{i})_{i \in \{1,\dots, n\} } \in \mathbb{R}$ la quantità
>$$
> \bar{x} = \frac{1}{n} \sum_{i=1}^n x_{i}
>$$
>Questa corrisponde alla media aritmetica
> >[!note] 
> >La media campionaria viene influenzata da valori estremi
> >

^33dd17

La media campionaria si può riscrivere come una media pesata dei differenti valori dei dati:
Sia $v_{i},\dots,v_{k} \in \mathbb{R}$ tali che:
$$
\{v_{j}: j \in \{1,\dots,k\} \} = \{x_{i}: i \in \{1,\dots, n\} \} \quad \land \quad v_{i} \ne v_{j} \quad per \quad  i \ne j 
$$

>[!def] Media Pesata #Definizione 
> La media pesata consiste nel assegnare dei pesi ai valori, del campione, proporzionale alla loro **frequenza assoluta**, ovvero a quante volte compaiono nel campione.
> $$
> \bar{x} = \sum_{j=1}^k v_{j} \cdot \frac{f_{j}}{n}
> $$
> - $f_{j}$: ==frequenza assoluta==
> - $\frac{f_{j}}{n}$: ==frequenza relativa== ~> pesi
>

>[!def] Valori Modali
>Valori che hanno frequenza assoluta massima
>

>[!def] Moda campionaria
>Se esiste un unico valore con frequenza massima allora si dice moda campionaria

#### Descrizione Alternativa
Sia $(x_{i})_{i \in \{1,\dots, n\} } \in \mathbb{R}$ un campione e $\sigma$ una permutazione di $\{1,\dots, n\}$ tale che
$$
x_{\sigma(1)} \leq x_{\sigma(2)} \leq \dots \leq x_{\sigma(n)}
$$
campione riordinato in ordine crescente.
La [media campionaria](#^33dd17) di $(x_{i})_{i \in \{1,\dots, n\} }$ è data da:
$$
\overline{m} = 
\begin{cases} 
 x_{\sigma\left( \frac{n+1}{2} \right)} \quad &  \text{se n è dispari} \\
\frac{1}{2} \cdot\left( x_{\sigma\left( \frac{n}{2} \right)} + x_{\sigma\left( \frac{n}{2} + 1 \right)} \right) \quad &  \text{se n è pari}
\end{cases}
$$
>[!note] N.B:
>Il caso in cui n è pari equivale a prendere la media dei 2 valori mediani

### Statistiche d'ordine
Casi più semplici:
- min
- max
Se $\sigma$ è la permutazione che riordina i dati in ordine crescente (come sopra), allora
$$
x_{\sigma(1)} = min\{x_{i}:i\in \{1,\dots, n\}\} \quad x_{\sigma(n)} = max\{x_{i}:i\in \{1,\dots, n\}\}
$$
### Statistiche della dispersione dei dati
Sia $(x_{i})_{i \in \{1,\dots, n\} } \in \mathbb{R}$ un campione univariato di numerosità $n\ge2$.
###### Obbiettivo:
Misurare le deviazioni dalla media campionaria dando più peso alle deviazioni grandi
>[!def] Varianza Campionaria #Definizione
>La *varianza campionaria* di $(x_{i})_{i \in \{1,\dots, n\} }$ è data da:
>$$
>s^2 = \frac{1}{n - 1} \cdot \left( \sum_{i=1}^n (x_{i} - \overline{x})^2  \right)
>$$
>- $\overline{x}$ è la [media campionaria](#^33dd17).
>
>Il prefattore $\frac{1}{n-1}$ invece di $\frac{1}{n}$ serve ad avere uno stimatore corretto.
>A parte questa differenza (prefattore), possiamo interpretare la varianza campionaria come la media campionaria degli scarti quadratici.
>Eleviamo gli scarti al quadrato per enfatizzare le deviazioni grandi, come posto dall'obbiettivo.
>Infatti:
>>[!note]- Oss. Utile per i calcoli
>>$$
>>\begin{split}
>>s^2 & =  \frac{1}{n - 1} \sum_{i = 1}^n (x_{i}-\overline{x})^2 \\
>> & = \frac{1}{n - 1} \sum_{i=1}^n(x_{i}^2 - 2x_{i}\cdot\overline{x}+\overline{x}^2) \\
>> & = \frac{1}{n-1} \sum_{i=1}^n x_{i}^2 - 2\frac{\overline{x}}{n-1}\sum_{i=1}^n x_{i} + \frac{n}{n-1}\overline{x}^2 \\
>> & = \frac{1}{n-1}\sum_{i=1}^n x_{i}^2 - 2 \frac{n}{n-1} \cdot \overline{x}^2 + \frac{n}{n-1} \overline{x}^2 \\
>> & = \frac{1}{n-1}\sum_{i=1}^n x_{i}^2 + \frac{n}{n-1} \cdot \overline{x}^2
\end{split}
>>$$
>

^6c6c18

>[!def] Deviazione Standard Campionaria #Definizione 
>La *deviazione standard campionaria* coincide alla varianza campionaria sotto radice
>$$
> s = \sqrt{s^2} = \sqrt{ \frac{1}{n - 1} \cdot \left( \sum_{i=1}^n (x_{i} - \overline{x})^2  \right) }
>$$

^dce63e

##### Osservazione 
Siano $a,b \in \mathbb{R}$ Poniamo
$$
y_{i} = a \cdot x_{i} + b \quad i \in \{1,\dots,n\}
$$
Sia: 
- $s_y^2$ la [varianza campionaria](#^6c6c18) di $y_i$ campione trasformato
- $s_x^2$ la [varianza campionaria](#^6c6c18) di $x_i$ campione originale
Allora
$$
\begin{align}
s_{y}^2 &= a^2 \cdot s_{x}^2 \\
s_{y}&=|a| \cdot s_{x}
\end{align}
$$
###### Conclusione:
La [deviazione standard](#^dce63e) ha la stessa unità di misura dei dati, inoltre [varianza ](#^6c6c18) e [deviazione standard](#^dce63e)
permettono di stimare la proporzione dei dati che sono "vicini" o "lontani" dalla [media campionaria](#^33dd17).
#### [[Disuguaglianza di Chebyshev (campionaria)]] 

### Statistiche per la distribuzione dei dati
Sia $(x_{i})_{i \in \{1,\dots,n\}}$ un campione univariato e sia $\sigma$ una permutazione che riordina i dati in ordine crescente;

$$
x_{\sigma(1)} \leq \dots \leq x_{\sigma(n)}
$$
>[!def] Percentile Campionario k-esimo #Definizione 
>Il *percentile campionario k-esimo*, con $k \in \{0,\dots, 100\}$ nel campione è dato da:
>$$
>\overline{P}_{k} = 
>\begin{cases}
> x_{\sigma\left( \left\lceil  \frac{n\cdot k}{100}  \right\rceil  \right)} &\text{ se } \frac{n \cdot k}{100} \not\in \mathbb{N} \\
> \frac{1}{2} \left(x_{\sigma\left( \frac{n \cdot k}{100} \right)} + x_{\sigma\left( \frac{n \cdot k}{100} + 1 \right)} \right) &\text{altrimenti}
>\end{cases}
>$$
>>N.B.
>>$\overline{P}_{50}$ corrisponde alla **mediana campionaria**.

**Interpretazione:**

$$
\frac{\#\{i \in \{1,\dots,n\}: x_{i} \leq \overline{P}_{k}\}}{n} \leq \frac{k}{100}
$$
$$

\frac{\#\{i \in \{1,\dots,n\}: x_{i} > \overline{P}_{k}\}}{n} \leq 1 - \frac{k}{100}
$$


## [[Dati d-Variati]]

[^1]: osservazione riguardo a un qualunque fenomeno il cui esito non è determinabile con certezza a priori