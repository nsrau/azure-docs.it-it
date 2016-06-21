È possibile creare più servizi all'interno di una sottoscrizione, per ognuno dei quali viene eseguito il provisioning a un livello specifico, con l'unica limitazione data dal numero di servizi consentiti a ogni livello all'interno di una singola sottoscrizione di Azure. Il numero massimo di servizi per ogni livello è riportato di seguito. Come indicato, è possibile creare fino a 12 servizi a livello Basic e altri 12 a livello S1 nella stessa sottoscrizione.

Altri livelli prevedono un servizio per sottoscrizione. Se è necessario più di un livello S2, S3 o S3 HD per ogni sottoscrizione, è possibile contattare il supporto di Azure.

Risorsa|Free|Basic|S1|S2|S3 <sup>1</sup> <br/>(Anteprima) |S3 HD <sup>1</sup> <br/>(Anteprima) 
---|---|---|---|----|---|----
Numero massimo di servizi |1 |12 |12 |1 |1 |1 
Scalabilità massima in unità di ricerca <sup>2</sup>|N/D <sup>3</sup>|3 unità di ricerca <sup>4</sup> |36 unità di ricerca|36 unità di ricerca|36 unità di ricerca|12 unità di ricerca <sup>5</sup>

<sup>1</sup> I livelli di **anteprima** sono disponibili con uno sconto del 50% rispetto al prezzo intero. Prima della disponibilità a livello generale (GA), i livelli sono introdotti come funzionalità di anteprima. Durante l'anteprima, non esiste alcun contratto di servizio (SLA). Per altre informazioni sui livelli, vedere [Choose a SKU or tier for Azure Search](../articles/search/search-sku-tier.md) (Scegliere uno SKU o un livello per Ricerca di Azure).

<sup>2</sup> **Le unità di ricerca (SU, Search Unit)** sono unità fatturabili per servizio, allocate come una **replica** o una **partizione**. Per l'archiviazione, l'indicizzazione e le operazioni di query sono necessari entrambi i tipi di risorsa. Vedere [Pianificazione della capacità in Ricerca di Azure](../articles/search/search-capacity-planning.md) per combinazioni valide inferiori ai limiti massimi.

<sup>3</sup> Il livello **gratuito** si basa su risorse condivise usate da più sottoscrittori. Per questo livello, non esistono risorse dedicate per un singolo sottoscrittore. Per questo motivo, la scalabilità non è supportata.

<sup>4</sup> Il livello **di base** dispone di una partizione fissa. Le unità di ricerca vengono usate per allocare repliche per la scalabilità dei carichi di lavoro delle query.

<sup>5</sup> Il livello **S3 HD** si basa sullo stesso hardware del livello S3, ma con una configurazione ottimizzata per un numero elevato di indici più piccoli. Dispone di 1 partizione di dimensioni molto grandi anziché 12 partizioni più piccole e ha un massimo di 12 repliche, come il livello S3.

<!---HONumber=AcomDC_0608_2016-->