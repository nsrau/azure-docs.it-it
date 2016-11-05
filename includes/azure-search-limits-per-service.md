L'archiviazione è vincolata dallo spazio su disco o da un limite rigido al *numero massimo* di indici o documenti, a seconda di quale venga raggiunto per primo.

| Risorsa | Free | Basic | S1 | S2 | S3 <br/>(Anteprima) | S3 HD <br/>(Anteprima) |
| --- | --- | --- | --- | --- | --- | --- |
| Contratto di servizio (SLA) |No <sup>1</sup> |Sì |Sì |Sì |No <sup>1</sup> |No <sup>1</sup> |
| Spazio di archiviazione per servizio |50 MB |2 GB |300 GB |1,2 TB |2,4 TB |200 GB |
| Partizioni per servizio |N/D |1 |12 |12 |12 |1 |
| Dimensioni della partizione |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |
| Repliche |N/D |1 |12 |12 |12 |12 |
| Numero massimo di indici |3 |5 |50 |200 |200 |1000 |
| Numero massimo di documenti |10\.000 |1 milione |180 milioni per servizio, 15 milioni per partizione |720 milioni per servizio, 60 milioni per partizione |1,4 miliardi per servizio, 120 milioni per partizione |200 milioni per servizio, 1 milione per indice |
| Query al secondo stimate |N/D |~3 per replica |~15 per replica |~60 per replica |>60 per replica |>60 per replica |

<sup>1</sup> Gli SKU Gratuito e Anteprima non includono contratti di servizio. I contratti di servizio vengono applicati quando uno SKU è disponibile a livello generale.

<!---HONumber=AcomDC_0608_2016-->