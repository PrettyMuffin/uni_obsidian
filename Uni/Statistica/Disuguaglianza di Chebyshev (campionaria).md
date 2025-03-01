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
> \quad \frac{\# \{i \in \{1,\dots,n\} : |x_{i}-\overline{x}| < \alpha \cdot s\}}{n} + \quad \frac{\# \{i \in \{1,\dots,n\} : |x_{i}-\overline{x}| \geq \alpha \cdot s\}}{n} = 1
> $$
