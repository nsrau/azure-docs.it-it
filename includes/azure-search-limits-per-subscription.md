È possibile creare più servizi all'interno di una sottoscrizione, per ognuno dei quali viene eseguito il provisioning a un livello specifico, con l'unica limitazione data dal numero di servizi consentiti per ogni livello. Ad esempio, è possibile creare fino a 12 servizi a livello Basic e altri 12 servizi a livello S1 nella stessa sottoscrizione. 

È possibile aumentare i limiti massimi del servizio su richiesta. Se sono necessari più servizi nella stessa sottoscrizione, contattare il supporto di Azure.

Risorsa|Free|Basic|S1|S2|S3 <br/>(Anteprima) <sup>1</sup>  |S3 HD <br/>(Anteprima) <sup>1</sup> 
---|---|---|---|----|---|----
Numero massimo di servizi |1 |12 |12  |6 |6 |6 
Scalabilità massima in SU <sup>2</sup>|N/D <sup>3</sup>|3 SU <sup>4</sup> |36 unità di ricerca|36 unità di ricerca|36 unità di ricerca|12 SU <sup>5</sup>

<sup>1</sup> I livelli di anteprima vengono fatturati con uno sconto del 50% e passano al prezzo pieno quando il livello raggiunge la disponibilità generale (GA). Durante l'anteprima, non esiste alcun contratto di servizio (SLA). Per altre informazioni sui livelli, vedere [Scegliere uno SKU o un piano tariffario per Ricerca di Azure](../articles/search/search-sku-tier.md).

<sup>2</sup> Le unità di ricerca (SU) sono fatturabili per servizio, allocate come *replica* o come *partizione*. Per l'archiviazione, l'indicizzazione e le operazioni di query sono necessarie entrambe le risorse. Per informazioni sulle combinazioni valide che non superano i limiti massimi, vedere [Ridimensionare i livelli di risorse per le query e l'indicizzazione dei carichi di lavoro in Ricerca di Azure](../articles/search/search-capacity-planning.md). 

<sup>3</sup> Il livello gratuito si basa su risorse condivise usate da più sottoscrittori. Per questo livello, non esistono risorse dedicate per un singolo sottoscrittore. Per questo motivo, la scalabilità massima è contrassegnata come non applicabile.

<sup>4</sup> Il livello Basic ha una partizione fissa. A questo livello vengono usate SU aggiuntive per l'allocazione di più repliche in caso di aumento dei carichi di lavoro di query.

<sup>5</sup> Il livello S3 HD si basa sullo stesso hardware del livello S3, ma con una configurazione ottimizzata per un numero elevato di indici più piccoli. Come il livello Basic, ha una partizione molto grande con unità di ricerca aggiuntive che possono essere usate per le repliche.






<!--HONumber=Oct16_HO2-->


