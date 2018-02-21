È possibile creare più servizi all'interno di una sottoscrizione, per ognuno dei quali viene eseguito il provisioning a un livello specifico, con l'unica limitazione data dal numero di servizi consentiti per ogni livello. Ad esempio, è possibile creare fino a 12 servizi a livello Basic e altri 12 servizi a livello S1 nella stessa sottoscrizione. Per altre informazioni sui livelli, vedere [Scegliere uno SKU o un piano tariffario per Ricerca di Azure](../articles/search/search-sku-tier.md).

È possibile aumentare i limiti massimi del servizio su richiesta. Se sono necessari più servizi nella stessa sottoscrizione, contattare il supporto di Azure.

| Risorsa | Gratuito | Basic | S1 | S2 | S3 | S3 HD <sup>1</sup> |
| --- | --- | --- | --- | --- | --- | --- |
| Numero massimo di servizi |1 |12 |12 |6 |6 |6 |
| Scalabilità massima in SU <sup>2</sup> |N/D <sup>3</sup> |3 SU <sup>4</sup> |36 unità di ricerca |36 unità di ricerca |36 unità di ricerca |36 unità di ricerca |

<sup>1</sup> S3 HD non supporta attualmente gli [indicizzatori](../articles/search/search-indexer-overview.md). 

<sup>2</sup> Le unità di ricerca sono unità di fatturazione allocate come *replica* o come *partizione*. Per l'archiviazione, l'indicizzazione e le operazioni di query sono necessarie entrambe le risorse. Per altre informazioni sul modo in cui vengono calcolate le unità di ricerca e un grafico sulle combinazioni valide che non superano i limiti massimi, vedere [Ridimensionare i livelli di risorse per le query e l'indicizzazione dei carichi di lavoro](../articles/search/search-capacity-planning.md). 

<sup>3</sup> Il livello gratuito si basa su risorse condivise usate da più sottoscrittori. Per questo livello, non esistono risorse dedicate per un singolo sottoscrittore. Per questo motivo, la scalabilità massima è contrassegnata come non applicabile.

<sup>4</sup> Il livello Basic ha una partizione fissa. A questo livello vengono usate SU aggiuntive per l'allocazione di più repliche in caso di aumento dei carichi di lavoro di query.

