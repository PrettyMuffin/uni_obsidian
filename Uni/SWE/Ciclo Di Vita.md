La storia di un prodotto SW attraversa un insieme di **stati**:
1. Concezione (_opportunity e stakeholders_)
2. Sviluppo
3. Utilizzo e manutenzione
4. ritiro

>[!def] Ciclo di vita
>L'insieme degli stati possibili è detto _ciclo di vita_.
>La vita del prodotto SW può essere descritta come un automa a stati finiti dove lo stato è il grado di maturazione raggiunto in quel momento dal prodotto SW.
>Mentre l'arco è l'attività svilta sul prodotto SW per farne avanzare lo stato.

Quali siano gli stati e quali le regole di attivazione degli archi dipende da obblighi (requirements), metodo (way of working), opportunità.

Un progetto SW "spinge" un prodotto SW lungo un segmento di ciclo di vita.

>[!def] Modelli di ciclo di vita
>Descrivono quali stati e quali transizioni privilegiare in un ciclo di vita e quindi quali processi attivare in esso.

Aderire a un modello di ciclo di vita consente di determinare quali processi serva attuare e quindi: pianificare, organizzare, eseguire e controllare lo svolgimento delle corrispondenti attività.

## Processo
>[!def] Processo di ciclo di vita
>Raggruppa e codifica (secondo _best practice_) le attività da svolgere per effettuare correte transizione di stato nel ciclo di vita di un prodotto SW.
>

>[!def] Processo
> Insieme di attività **correlate** e **coese** che trasformano ingressi (bisogni) in uscite (prodotti) secondo regole date, consumando risorse nel farlo.
> ![[Pasted image 20251004103108.png]]

Come abbiamo detto nella [[SWE/Introduzione|Introduzione]]: un insieme di attività è efficace quando raggiunge gli obiettivi attesi ed è efficiente quando raggiunge gli obiettivi senza sprecare risorse.
La combinazione di efficienza ed efficacia si chiama **economicità**.

## Standard
Adottare **standard** di processo aiuta a raggiungere l'economicità, per questo esistono standard generali, che definiscono il modello di riferimento del dominio SWE.
Esistono modelli di azione che dicono cosa fare, come, quando e possono essere descrittivi o prescrittivi.
Per finire ci sono i modelli di valutazione che misurano la distanza tra un particolare way of working e la best practice di riferimento.
### ISO/IEC 12207: 1995
È il modello più noto e riferito:
- Ha natura descrittiva
- Ne esistono altri, specializzati di dominio industriale, prevalentemente prescrittivi

È un modello "ad alto livello" in quanto:
- Identifica processi che agiscono sul ciclo di vita del SW
- Ha struttura modulare che richiede specializzazione
- Specifica la responsabilità sui processi
- Identifica i prodotti dei processi

![[Pasted image 20251004104049.png]]

- `Primary`: Ogni progetto deve avere almeno uno dei processi `primary`
- `Supporting`: Sono di supporto, aiutano con i processi `primary`
- `Organisatinoal`: Sono fondamentali per creare un ambiente che permetta ai progetti di essere avviati, supportati e controllati.