| Risorsa | Free | Basic (anteprima) <sup>6</sup> | S1 | S2 <sup>7</sup> |
| --- | --- | --- | --- | --- |
| Numero massimo di servizi di ricerca <sup>8</sup> |1 per sottoscrizione |12 per sottoscrizione |12 per sottoscrizione |1 per sottoscrizione |
| Dimensioni massime di archiviazione <sup>1</sup> |50 MB o 10.000 documenti |2 GB per servizio |25 GB per partizione o 300 GB di documenti per servizio |100 GB per partizione o 1,2 TB per servizio |
| Numero massimo di documenti ospitati |10\.000 in totale |1 milione per servizio |15 milioni per partizione (fino a 180 milioni di documenti per servizio) |60 milioni per partizione (fino a 720 milioni di documenti per servizio) |
| Numero massimo di indici |3 |5 |50 |200 |
| Numero massimo di indicizzatori |3 |5 |50 |200 |
| Numero massimo di origini dati indicizzatori |3 |5 |50 |200 |
| Indice: numero massimo di campi per indice |1000 |100 <sup>5</sup> |1000 |1000 |
| Numero massimo di profili di punteggio per indice |16 |16 |16 |16 |
| Numero massimo di funzioni per profilo |8 |8 |8 |8 |
| Indicizzatori: carico di indicizzazione massimo per chiamata |10\.000 documenti |Limitato solo da numero massimo di documenti |Limitato solo da numero massimo di documenti |Limitato solo da numero massimo di documenti |
| Indicizzatori: tempo massimo di esecuzione |3 minuti |24 ore |24 ore |24 ore |
| Indicizzatore BLOB: dimensioni massime per un BLOB, MB |16 |16 |128 |256 |
| Indicizzatore BLOB: numero massimo di caratteri di contenuto estratti da un BLOB |32\.000 |64\.000 |4 milioni |4 milioni |
| Query al secondo (QPS, Query Per Second) <sup>2</sup> |N/D |~3 per replica |~15 per replica |~60 per replica |
| Aumento del numero di istanze: numero massimo di unità di ricerca (SU, Search Unit) <sup>3</sup> |N/D |Fino a 3 unità (3 repliche e 1 partizione) |36 unità |36 unità |
| Prezzi <sup>4</sup> |N/D |$75 per SU al mese |$250 per SU al mese |$1.000 per SU al mese |

<sup>1</sup> Le dimensioni di archiviazione corrispondono a una quantità fissa o al numero di documenti per ogni servizio, a seconda del valore raggiunto per primo.

<sup>2</sup> Query al secondo è un'approssimazione basata sull'euristica che usa carichi di lavoro simulati ed effettivi per calcolare i valori stimati. La velocità di query al secondo effettiva varia a seconda dei dati e della natura della query.

<sup>3</sup> Le unità di ricerca sono l'unità fatturabile per una replica o una partizione. Sono necessarie entrambe per l'archiviazione, l'indicizzazione e le operazioni di query. Per le combinazioni valide di repliche e partizioni entro il limite massimo di 3 o 36 unità di base e standard, vedere [Pianificazione della capacità](../articles/search/search-capacity-planning.md).

<sup>4</sup> Il prezzo è per il mercato degli Stati Uniti e illustra i costi relativi dei diversi livelli. I prezzi variano a seconda del mercato. Consultare la [pagina Prezzi](https://azure.microsoft.com/pricing/details/search/) per i prezzi in altre valute. La tariffa si riferisce alla singola unità di ricerca (SU, Search Unit). A livello S1, una configurazione di 3 unità di ricerca, ad esempio 3 repliche e 1 partizione, ha un costo medio di $750 al mese Se si passa a un numero inferiore di unità di ricerca entro il mese, la fattura viene ripartita in modo da addebitare solo ciò che si utilizza.

<sup>5</sup> Il livello di base è l'unico livello con un limite inferiore di 100 campi per indice.

<sup>6</sup> Il [livello di base](http://aka.ms/azuresearchbasic) è disponibile nel periodo di anteprima con uno sconto del 50% rispetto al prezzo intero.

<sup>7</sup> S2 richiede un ticket di servizio. Non può essere fornito nel portale. Contattare il supporto o inviare un e-mail a azuresearch_contact@microsoft.com per richiedere assistenza.

<sup>8</sup> È possibile aumentare il numero massimo di servizi per sottoscrizione per tutti i livelli ad eccezione di quello gratuito. Contattare il supporto o inviare un e-mail a azuresearch_contact@microsoft.com per richiedere assistenza.

<!---HONumber=AcomDC_0525_2016-->