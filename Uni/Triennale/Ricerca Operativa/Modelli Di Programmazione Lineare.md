>[!def] Modelli di programmazione matematica
> I _modelli di programmazione matematica_ sono modelli che descrivono le caratteristiche della soluzione ottima di un problema di ottimizzazione attraverso relazioni matematiche.
> Un modello di programmazione matematica è composto dai seguenti elementi:
> - **Insiemi**: Raggruppano gli elementi del sistema
> - **Parametri**: Sono i dati del problema e rappresentano delle quantità fissate che dipendono dai diversi elementi del sistema.
> - **Variabili decisionali o di controllo**: Sono le grandezze del sistema di cui non conosciamo il valore (assimilabili a delle incognite) e sulle quali possiamo agire per determinare diverse soluzioni alternative del problema
> - **Vincoli**: Sono delle relazioni matematiche che descrivono le condizioni di ammissibilità delle soluzioni.
>   Servono quindi per discriminare le combinazioni di valori delle variabili decisionali che rappresentano soluzioni accettabili del problema, da quelle che non lo sono.
> - **Funzione Obiettivo**: è la quantità da massimizzare o minimizzare, espressa come funzione delle variabili decisionali.
> 
> > Di fatto un modello di programmazione matematica _dichiara_ le caratteristiche della soluzione cercata (che cosa), piuttosto che _definire_ la strategia per la ricerca della soluzione stessa (come).
> > Un modello di programmazione matematica potrebbe essere paragonato ad un linguaggio dichiarativo, che indica cosa si vuole ottenere, piuttosto che a un linguaggio procedurale, che indica i passi per ottenere il risultato cercato.

I modelli di programmazione lineare sono una particolare classe di modelli di programmazione matematica in cui:
- ==La funzione obiettivo è un’espressione lineare delle variabili decisionali;==
- I vincoli sono determinati da un sistema di equazioni e/o disequazioni lineari.
  >[!warning] Attenzione
  >In questo corso si richiedono **MODELLI LINEARI**: le variabili, di qualsiasi natura esse siano, possono essere solamente moltiplicate per una costante e sommate fra loro.

La formulazione generale di un modello di programmazione lineare è la seguente:
$$
\begin{align}
\text{min(max)} z = \quad c_{1}x_{1} \quad &+ \quad c_{2}x_{2} \quad &+ \quad \dots \quad &+ \quad c_{j}x_{j} \quad &+ \quad \dots \quad &+ \quad c_{n}x_{n} \quad \text{(+cost.)} \\
\text{soggetto a }(s.t) & \\
 a_{11}x_{1} \quad &+ \quad a_{12}x_{2} \quad &+ \quad \dots \quad &+ \quad a_{1j} x_{j} \quad &+ \quad \dots \quad &+ \quad a_{1n} x_{n} \geq (=, \leq) \quad b_{1} \\
 a_{21}x_{1} \quad &+ \quad a_{22}x_{2} \quad &+ \quad \dots \quad &+ \quad a_{2j} x_{j} \quad &+ \quad \dots \quad &+ \quad a_{2n} x_{n} \geq (=, \leq) \quad b_{2} \\
 \dots \\
 a_{m1}x_{1} \quad &+ \quad a_{m2}x_{2} \quad &+ \quad \dots \quad &+ \quad a_{mj} x_{j} \quad &+ \quad \dots \quad &+ \quad a_{mn} x_{n} \geq (=, \leq) \quad b_{m}   \\ \\
&& x_{j} \in \mathbb{R}_{+} (x_{j} \in \mathbb{Z}_{+}) \quad \forall j = 1, \dots, n
\end{align}
$$
dove:
- $z$ è la funzione obiettivo da minimizzare ($min$) o massimizzare ($max$)
- $x_{j} \quad \forall j=1,\dots,n$ sono le **variabili decisionali** (incognite) che possono essere reali (eventualmente non negative), intere (eventualmente non negative) o binarie ($x_{j} \in \{ 0,1 \}$)
- $c_{j} \quad \forall j=1,\dots,n$ sono **coefficienti di costo** ($min$) o **di profitto** ($max$) e ==sono costanti note del problema== (parametri).
- $a_{ij} \quad \forall i=1,\dots,m \text{ e } \forall j = 1,\dots,n$ sono i **coefficienti tecnologici** (parametri)
- $b_{i} \quad \forall i=1,\dots,m$ sono i **termini noti** (parametri)

In base alla natura o dominio delle variabili decisionali, si parla di:
- Modelli di Programmazione Lineare (in senso stretto, PL) se tutte le variabili possono assumere valori reali;
- Modelli di Programmazione Lineare Intera (PLI) se tutte le variabili possono assumere valori interi;
- Modelli di Programmazione Lineare Intera Mista (PLIM) se alcune variabili possono assumere valori reali e altre valori interi.

## Costruzione di un Modello

### Testo del Problema
Un coltivatore ha a disposizione 12 ettari di terreno da coltivare a lattuga o a patate. Le risorse a sua disposizione, oltre al terreno, sono: 70 kg di semi di lattuga, 18 t di tuberi, 160 metri cubi (mc) di fertilizzante. Supponendo che il mercato sia in grado di assorbire tutta la produzione e che i prezzi siano stabili, la resa stimata per la coltivazione di lattuga è di 3000 e/ettaro e quella delle patate è di 5000 e/ettaro. L’assorbimento delle risorse per ogni tipo di coltivazione è di 7 kg di semi e 10 mc di fertilizzante per ettaro di lattuga, e 3 t di tuberi e 20 mc di fertilizzante per le patate. Stabilire quanto terreno destinare a lattuga e quanto a patate in modo da massimizzare la resa economica e sfruttando al meglio le risorse disponibili.
### Costruzione del Modello
#### Passo 1
Per prima cosa dobbiamo individuare quali sono le decisioni da prendere per risolvere il problema, ossia dobbiamo definire le variabili decisionali (incognite). In questo caso le decisioni da prendere riguardano la quantità di terreno da destinare a lattuga e la quantità di terreno da destinare a patate. 
Definiamo dunque le variabili:
- $x_{L}$: quantità di terreno, in ettari, da coltivare a lattuga;
- $x_P$: quantità di terreno, in ettari, da coltivare a patate;

#### Passo 2
Ora che abbiamo definito le variabili, dobbiamo determinare qual è l’obiettivo, ossia dobbiamo formulare la funzione obiettivo da ottimizzare. Nell'esempio, l’obiettivo è massimizzare il profitto, sapendo che la resa stimata per la coltivazione di lattuga è di 3000 €/ettaro e quella delle patate è di 5000 €/ettaro.
Servendoci delle variabili definite nel passo precedente, la funzione obiettivo può essere espressa nel seguente modo:
$$\text{max } z = 3000x_{L} + 5000x_{P}$$
#### Passo 3
Infine dobbiamo descrivere le condizioni di ammissibilità delle soluzioni, ovvero costruire un sistema di equazioni e/o disequazioni lineari che esprimono i vincoli che le variabili devono soddisfare per essere soluzioni ammissibili del problema.
In questo caso, sappiamo che:
1. Il coltivatore ha a disposizione 12 ettari di terreno da coltivare a lattuga e a patate.
   Quindi la somma degli ettari di terreno da destinare a lattuga (variabile $x_L$) e degli ettari di terreno da destinare a patate (variabile $x_{P}$ ) non deve superare i 12 ettari di terreno a disposizione. 
   Questo primo vincolo può essere espresso nel modo seguente:
  $$x_{L} + x_{P} \leq 12 \text{ (ettari disponibili)}$$
2. Il coltivatore ha a disposizione 70 kg di semi di lattuga e sappiamo che per coltivare un ettaro di terreno a lattuga sono necessari 7 kg di semi. 
   Quindi la quantità di semi necessaria per coltivare $x_L$ ettari di terreno a lattuga non deve eccedere i 70 kg di semi a disposizione. 
   Questo secondo vincolo può essere espresso nel modo seguente:
  $$7x_{L} \leq 70 \text{ (semi disponibili)}$$
3. Il coltivatore ha a disposizione 18 t di tuberi e, per coltivare un ettaro di terreno a patate sono necessarie 3 t di tuberi. 
   Quindi la quantità di tuberi necessaria per coltivare $x_{P}$ ettari di terreno a patate non deve eccedere le 18 t di tuberi a disposizione. 
   Questo terzo vincolo può essere espresso nel modo seguente:
  $$3x_{P} \leq 18 \text{ (tuberi disponibili)}$$
4. Il coltivatore ha a disposizione 160 mc di fertilizzante e la coltivazione di un ettaro di terreno a lattuga richiede 10 mc di fertilizzante mentre la coltivazione di un ettaro di terreno a patate richiede 20 mc di fertilizzante. 
   Quindi, la somma della quantità di fertilizzante necessaria per coltivare $x_L$ ettari di terreno a lattuga e della quantità di fertilizzante necessaria per coltivare $x_P$ ettari di terreno a patate, non deve superare le 160 mc di fertilizzante a disposizione. 
   Questo quarto vincolo può essere espresso nel modo seguente:
   $$10x_{L} + 20x_{P} \leq 160 \text{ (fertilizzante disponibile)}$$

5. Infine, dobbiamo specificare il dominio delle variabili scelte. 
   In questo caso le variabili decisionali sono $x_{L} \text{ e } x_{P}$ che rappresentano la quantità di terreno, in ettari, da destinare a lattuga e patate rispettivamente. 
   Trattandosi di ettari di terreno e non essendo presente nel testo del problema alcuna richiesta di interezza, tali variabili possono assumere valori reali. 
   Tuttavia, poiché non è possibile coltivare una quantità negativa di ettari di terreno, $x_{L} \text{ e } x_{P}$ devono essere non negative:
   $$x_{L}\geq 0, x_{P}\geq 0 \text{ (dominio)}$$

Il problema può ora essere formalizzato con il seguente modello di programmazione lineare:
$$
\begin{align}
\text{max} \quad 3000x_{L} &+ 5000x_{P} & \\
s.t. \quad \quad\quad\quad \text{ } & \\
x_{L} &+ x_{P} &\leq 12 \\
7x_{L}&+ 0x_{P}  &\leq 70 \\
0x_{L}&+ 3x_{P} &\leq 18 \\
10x_{L} &+ 20x_{P} &\leq 160 \\
x_{L} &, x_{P} &\geq 0
\end{align}
$$

## Schemi Base Di Modellazione
>[!important] N.B.
>Spesso (ma non sempre!) per modellare un problema reale, può essere individuare uno o più schemi generali che esprimono la situazione in esame e completare il modello con ulteriori vincoli e variabili.
### Modelli di copertura di costo minimo
$$
\begin{align}
\text{min } &\sum_{i\in I}C_{i}x_{i} \\
s.t & \\
&\sum_{i\in I} A_{i,j}x_{i} \geq D_{j} \quad \forall j \in J \\
& x_{i} \in \mathbb{R}_{+} [\mathbb{Z}_{+} |\{ 0, 1 \}| ] \quad \forall i \in I
\end{align}
$$
dove
- $I$ insieme delle risorse da acquistare;
- $J$ insieme delle domande da coprire;
- $C_i$ costo (unitario) per l'utilizzo della risorsa $i \in I$;
- $D_j$ ammontare della domanda di $j \in J$;
- $A_{i,j}$ capacità (unitaria) della risorsa $i$ di soddisfare la domanda $j$.
#### Esempi

### Modelli di mix ottimo di produzione
$$
\begin{align}
\text{max} & \sum_{i \in I}P_{i}x_{i} \\
s.t & \\
& \sum_{i \in I} A_{ij}x_{i} \leq Q_{j} \quad \forall j \in I \\
& x_{i} \in \mathbb{R}_{+} [\mathbb{Z}_{+} |\{ 0,1 \}|] \quad \forall i \in I
\end{align}
$$
dove
- $I$ insieme dei beni che possono essere prodotti
- $J$ insieme delle risorse disponibili
- $P_{i}$ profitto (unitario) per il bene $i \in I$
- $Q_j$ quantità disponibile della risorsa $j \in J$
- $A_{ij}$ quantità di risorsa $j$ necessaria per la produzione di un'unità del bene $i$.

#### Esempi
### Modelli di trasporto
$$
\begin{align}
\text{min } & \sum_{i \in I} \sum_{j \in J} C_{ij}x_{ij} & \\
s.t & &\\
& \sum_{j \in J} x_{ij} \leq O_{i} & \forall i \in I \\
& \sum_{i \in I} x_{ij} \geq D_{j} & \forall j \in J \\
& x_{ij} \in \mathbb{R}_{+} [\mathbb{Z}_{+} |\{ 0,1 \}|] & \forall i \in I, j \in J
\end{align}
$$
dove
- $I$ insieme dei centri di offerta
- $J$ insieme dei centri di domanda
- $C_{ij}$ costo (unitario) per il trasporto da $i \in I$ a $j \in J$
- $O_i$ ammontare dell'offerta in $i \in I$
- $D_j$ ammontare della domanda in $j \in J$

#### Esempi