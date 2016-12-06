L'archiviazione è vincolata dallo spazio su disco o da un limite rigido al *numero massimo* di indici o documenti, a seconda di quale venga raggiunto per primo.

| Risorsa | Free | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Contratto di servizio (SLA) |No <sup>1</sup> |Sì |Sì |Sì |Sì |Sì |
| Archiviazione per partizione |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partizioni per servizio |N/D |1 |12 |12 |12 |3 <sup>2</sup> |
| Dimensioni della partizione |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |
| Repliche |N/D |3 |12 |12 |12 |12 |
| Numero massimo di indici |3 |5 |50 |200 |200 |1000 per partizione o 3000 per servizio |
| Numero massimo di documenti |10.000 |1 milione |15 milioni per partizione o 180 milioni per servizio |60 milioni per partizione o 720 milioni per servizio |120 milioni per partizione o 1,4 miliardi per servizio |1 milione per indice o 200 milioni per partizione |
| Query al secondo stimate |N/D |~3 per replica |~15 per replica |~60 per replica |~60 per replica |>60 per replica |

<sup>1</sup> Gli SKU gratuiti e in anteprima non includono Contratti di servizio. I contratti di servizio vengono applicati quando uno SKU è disponibile a livello generale.

<sup>2</sup> Per S3 HD è previsto un limite rigido di 3 partizioni, inferiore al limite di partizioni per S3. Il limite minore di partizioni viene imposto perché il numero di indici per S3 HD è significativamente superiore. Dato che sono presenti limiti del servizio per entrambe le risorse di calcolo (archiviazione ed elaborazione) e per il contenuto (indici e documenti), il limite relativo al contenuto viene raggiunto per primo.


<!--HONumber=Nov16_HO3-->


