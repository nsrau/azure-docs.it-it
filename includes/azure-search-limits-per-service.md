L'archiviazione è vincolata dallo spazio su disco o da un limite rigido al *numero massimo* di indici o documenti, a seconda di quale venga raggiunto per primo.

| Risorsa | Gratuito | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Contratto di servizio (SLA) |No <sup>1</sup> |Sì |Sì |Sì |Sì |Sì |
| Archiviazione per partizione |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partizioni per servizio |N/D |1 |12 |12 |12 |3 <sup>2</sup> |
| Dimensioni della partizione |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |
| Repliche |N/D |3 |12 |12 |12 |12 |
| Numero massimo di indici |3 |5 |50 |200 |200 |1000 per partizione o 3000 per servizio | 
| Numero massimo di indicizzatori |3 |5 |50 |200 |200 |Nessun supporto per l'indicizzatore | 
| Numero massimo di origini dati |3 |5 |50 |200 |200 |Nessun supporto per l'indicizzatore | 
| Numero massimo di documenti |10.000 |1 milione |15 milioni per partizione o 180 milioni per servizio |60 milioni per partizione o 720 milioni per servizio |120 milioni per partizione o 1,4 miliardi per servizio |1 milione per indice o 200 milioni per partizione | 

<sup>1</sup> Con il Contratto di servizio non sono incluse funzionalità di anteprima e il livello gratuito. Per tutti i livelli fatturabili, i contratti di servizio diventano effettivi quando viene effettuato il provisioning di una ridondanza sufficiente per il servizio. Per il Contratto di servizio di query (lettura) sono necessarie due o più repliche. Per il contratto di servizio di query e indicizzazione (lettura-scrittura) sono necessarie tre o più repliche. Il numero di partizioni non è un fattore di cui tiene conto il Contratto di servizio. 

<sup>2</sup> Per S3 HD è previsto un limite rigido di 3 partizioni, inferiore al limite di partizioni per S3. Il limite minore di partizioni viene imposto perché il numero di indici per S3 HD è significativamente superiore. Dato che sono presenti limiti del servizio per entrambe le risorse di calcolo (archiviazione ed elaborazione) e per il contenuto (indici e documenti), il limite relativo al contenuto viene raggiunto per primo.

