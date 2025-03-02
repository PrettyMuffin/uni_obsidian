Sia $(x_{i})_{i \in \{1,\dots,n\}}$ un campione univariato.
Siano $\overline{x}$ la sua [media campionaria](Concetti%20Fondamentali#^33dd17) e $s$ la [deviazione standard campionaria](Concetti%20Fondamentali#^dce63e).
Se $s>0$ allora $\forall \alpha > 0$: 
$$
 \text{I:} \quad \frac{\# \{i \in \{1,\dots,n\} : |x_{i}-\overline{x}| < \alpha \cdot s\}}{n} \geq 1 - \frac{n - 1}{n - \alpha^2} > 1 - \frac{1}{\alpha^2}
  $$
  
$$
 \text{II:} \quad \frac{\# \{i \in \{1,\dots,n\} : |x_{i}-\overline{x}| \leq \alpha \cdot s\}}{n} \ge 1 - \frac{n - 1}{n - \alpha^2} > 1 - \frac{1}{\alpha^2}
  $$

$$
 \text{III:} \quad \frac{\# \{i \in \{1,\dots,n\} : |x_{i}-\overline{x}| \geq \alpha \cdot s\}}{n} \leq 1 - \frac{n - 1}{n - \alpha^2} < 1 - \frac{1}{\alpha^2}
  $$
  
$$
 \text{IV (unilaterale):} \quad \frac{\# \{i \in \{1,\dots,n\} : x_{i}-\overline{x} \geq \alpha \cdot s\}}{n} < 1 - \frac{1}{1+\alpha^2}
  $$
>[!note] Osservazione
> Se $s = 0$ allora $x_i = \overline{x} \quad \forall i\in\{1,\dots,n\}$
> $I,II,III$ interessanti per $\alpha >= 1$ .
> Parte di $I$ segue dalla $III$ e dal fatto che:
> $$
> \quad \frac{\# \{i \in \{1,\dots,n\} : |x_{i}-\overline{x}| < \alpha \cdot s\}}{n} + \frac{\# \{i \in \{1,\dots,n\} : |x_{i}-\overline{x}| \geq \alpha \cdot s\}}{n} = 1
> $$
> Dunque sono complementari.

###### Dim. $III$
> Per la dimostrazione è necessario il concetto di **funzione indicatrice** #Definizione di un insieme $A$:
> $$
>\perp_{A}(x) = 
>\begin{cases}
>1 \quad & \text{se } x \in A \\ \\
>0 \quad & \text{altrimenti}
>\end{cases}
>$$
> Sarebbe quindi come una funzione booleana che ritorna vero se $x \in A$.

$$
\frac{\# \{i \in \{1,\dots,n\} : |x_{i}-\overline{x}| \geq \alpha \cdot s\}}{n} = \frac{1}{n} \sum_{i=1}^n \perp_{[\alpha \cdot s, \infty]}(|x_{i} - \overline{x}|)
$$
Questo è vero in quanto mettiamo in proporzione in numero di elementi che sono nell'intervallo $[\alpha \cdot s, \infty)$ rispetto al numero totale di elementi, dunque ne troviamo la percentuale. (amo la statistica).
Inoltre
$$
\frac{1}{n} \sum_{i=1}^n \perp_{[\alpha^2 \cdot s^2, \infty)}(|x_{i} - \overline{x}|^2) \leq \frac{1}{n}\sum_{i=1}^n\frac{(x_{i}-\overline{x})^2}{\alpha^2 \cdot s^2}
$$
Questa disuguaglianza è vera perché dato che $\perp$ restituisce solo 1 o 0 sarà sicuramente $\leq$ della frazione$\frac{(x_{i}-\overline{x})^2}{\alpha^2 \cdot s^2}$ in quanto  (se $x_{i} - \overline{x} \in [\alpha^2 \cdot s^2, \infty)$) sarà sicuramente $\ge$ di 1 pk il numeratore è maggiore del denominatore.
$$
\frac{1}{n}\sum_{i=1}^n\frac{(x_{i}-\overline{x})^2}{\alpha^2 \cdot s^2} = \frac{n-1}{n\cdot \alpha^2\cdot s^2} \cdot \frac{1}{n-1} \cdot \sum_{i=1}^n(x_{i}-\overline{x})^2 = \frac{n-1}{n\cdot \alpha^2\cdot s^2} \cdot s^2
$$
$$
 \frac{n-1}{n\cdot \alpha^2} < \frac{1}{\alpha^2}
$$
come volevasi dimostrare.

---
Scegliendo $\alpha\in\{2,3,5\}$ otteniamo dalla disuguaglianza di Chebyshev:
- Da $I$:
	$$
	 \begin{align} \\
 \frac{\# \{i \in \{1,\dots,n\} : |x_{i}-\overline{x}| < 2 \cdot s\}}{n} &> \frac{3}{4} = 75\% \\
 \frac{\# \{i \in \{1,\dots,n\} : |x_{i}-\overline{x}| < 3 \cdot s\}}{n} &> \frac{8}{9} \approx 89\% \\
 \frac{\# \{i \in \{1,\dots,n\} : |x_{i}-\overline{x}| < 5 \cdot s\}}{n} &> \frac{25}{25} \approx 96\% \\
     \end{align}
	 $$
- Analogamente da $III$:
  $$
\begin{align}
\frac{\# \{i \in \{1,\dots,n\} : |x_{i}-\overline{x}| \geq 2 \cdot s\}}{n} &< \frac{1}{4} = 25\% \\
\frac{\# \{i \in \{1,\dots,n\} : |x_{i}-\overline{x}| \geq 3 \cdot s\}}{n} &< \frac{1}{9} \approx 11\% \\
\frac{\# \{i \in \{1,\dots,n\} : |x_{i}-\overline{x}| \geq 5 \cdot s\}}{n} &< \frac{1}{25} \approx 4\%
\end{align}
  $$
  
>[!important] In particolare si evince
> 1. meno del 25% dei dati prende valori che distano più di $2\cdot s$ dalla [media campionaria](Concetti%20Fondamentali#^33dd17).
> 2. meno del 4% dei dati prende valori che distano più di $5 \cdot s$ dalla [media campionaria](Concetti%20Fondamentali#^33dd17).
> 3. più del 75% dei dati predono valori che distano meno di $2 \cdot s$ dalla [media campionaria](Concetti%20Fondamentali#^33dd17). 
> 4. più del 96% dei dati prendono valori che distano meno di $5 \cdot s$ dalla [media campionaria](Concetti%20Fondamentali#^33dd17).
> 
> Questo tipo di stime valgono sempre, sono quindi di tipo "worst case".