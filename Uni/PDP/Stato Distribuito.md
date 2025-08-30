Abbiamo giustificato la necessità di realizzare un sistema distribuito con la ricerca di:
- affidabilità
- suddivisione del carico
- distribuzione dei risultati

Le stesse caratteristiche possono essere desiderabili per un insieme di dati:
- disponibili continuamente in presenza di guasti
- quantità superiore a quella gestibile da una sola macchina
- accessibilità da parte di più posizioni geografiche

Un insieme di dati gestito da un sistema distribuito è disponibile, accessibile e coerente solo nella misure in cui i singoli nodi riescono a rimanere allineati, coordinati e concordi fra loro.
La distribuzione dello stato è effettivamente implementata attraverso la suddivisione gerarchica dello stato stesso, senza condivisione fra i nodi.

## Consenso
Se il requisito principale è la resistenza ai fallimenti o le prestazioni particolarmente elevate (in spazio o velocità) non abbiamo altra scelta che replicare lo stato in più di un nodo, in modo da avere che:
- Un guasto non porti a perdita di dati
- aggiungendo nodi aumenti la capacità di risposta del sistema

Ma vendo più nodi che contengono lo stesso dato, nasce il problema del **consenso**, ovvero di garantire che tutti i nodi del sistema contengano la stessa versione di un dato.
