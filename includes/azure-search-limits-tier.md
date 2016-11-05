| Risorsa | Free | Basic (anteprima) | S1 | S2 |
| --- | --- | --- | --- | --- |
| Numero massimo di servizi in ogni piano per ogni sottoscrizione <sup>1</sup> |1 |12 |12 |1 |
| Scalabilità massima per ogni piano <sup>2</sup> |N/D |3 unità di ricerca (fino a 3 repliche e 1 partizione) |36 unità di ricerca |36 unità di ricerca |

<sup>1</sup> Viene eseguito il provisioning di ogni servizio in un determinato piano tariffario, con limiti al numero di servizi di cui è possibile effettuare il provisioning in ogni piano all'interno di una singola sottoscrizione di Azure. Nel periodo di anteprima i piani sono disponibili con uno sconto del 50% rispetto al prezzo intero.

<sup>2</sup> I limiti di scalabilità sono definiti in termini di unità di ricerca (SU) per ogni piano. Le unità di ricerca sono l'unità fatturabile per una **replica** o una **partizione**. Sono necessarie entrambe per l'archiviazione, l'indicizzazione e le operazioni di query. Vedere [Pianificazione della capacità in Ricerca di Azure](../articles/search/search-capacity-planning.md) per le combinazioni valide di repliche e partizioni entro il limite massimo di 3 o 36 unità rispettivamente per **Basic** e **Standard**. Poiché il piano **Gratuito** è basato su risorse condivise usate da più sottoscrittori, l'aumento delle istanze non viene fornito a questo livello.

<!---HONumber=AcomDC_0601_2016-->