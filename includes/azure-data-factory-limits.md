Data Factory è un servizio multi-tenant che presenta i seguenti limiti predefiniti in modo che le sottoscrizioni dei clienti siano protette da ogni altro carico di lavoro. Molti dei limiti possono essere facilmente aumentati per la sottoscrizione fino al limite massimo contattando il supporto tecnico.

### <a name="version-2"></a>versione 2

| Risorsa | Limite predefinito | Limite massimo | 
| -------- | ------------- | ------------- | 
| Data factory in una sottoscrizione di Azure | 50 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Pipeline in una data factory | 2500 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Set di dati in una data factory | 2500 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Trigger in una data factory | 2500 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Servizi collegati in una data factory | 2500 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Runtime di integrazione in una data factory <sup>4</sup> | 2500 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Esecuzioni di pipeline simultanee per pipeline | 20 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Numero massimo di attività per pipeline | 20 | 30 |
| Numero massimo di parametri per pipeline | 20 | 30 |
| Byte per oggetto per gli oggetti pipeline <sup>1</sup> | 200 KB | 200 KB |
| Byte per oggetto per oggetti set di dati e servizio collegato <sup>1</sup> | 100 KB | 2000 KB |
| Unità di spostamento dati cloud <sup>3</sup> | 32 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Numero di tentativi delle esecuzioni di attività pipeline | 1 giorno (timeout) | 1 giorno (timeout) |
| Chiamate API in scrittura | 2500/ora<br/><br/> Questo limite è imposto da Azure Resource Manager, non da Azure Data Factory. | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Chiamate API in lettura | 12.500/ora<br/><br/> Questo limite è imposto da Azure Resource Manager, non da Azure Data Factory. | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |


### <a name="version-1"></a>versione 1

| **Risorsa** | **Limite predefinito** | **Limite massimo** |
| --- | --- | --- |
| Data factory in una sottoscrizione di Azure |50 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Pipeline in una data factory |2500 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Set di dati in una data factory |5000 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Sezioni simultanee per set di dati |10 |10 |
| Byte per oggetto per gli oggetti pipeline <sup>1</sup> |200 KB |200 KB |
| Byte per oggetto per oggetti set di dati e servizio collegato <sup>1</sup> |100 KB |2000 KB |
| Memorie centrali del cluster HDInsight su richiesta con una sottoscrizione<sup>2</sup> |60 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Unità di spostamento dati cloud <sup>3</sup> |32 |[Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Numero di tentativi delle esecuzioni di attività pipeline |1000 |MaxInt (32 bit) |

<sup>1</sup>pipeline, set di dati e oggetti servizio collegato rappresentano un raggruppamento logico del carico di lavoro. I limiti per questi oggetti non riguardano le quantità di dati che è possibile spostare ed elaborare con il servizio di Azure Data Factory. Data Factory è progettato per la scalabilità e la gestione di petabyte di dati.

<sup>2</sup> I core HDInsight su richiesta vengono allocati all'esterno della sottoscrizione che contiene la data factory. Di conseguenza, il limite massimo è il limite principale imposto dalla Data Factory per le memorie centrali HDInsight su richiesta e che è diverso dal limite principale associato alla sottoscrizione Azure.

<sup>3</sup> L'unità di spostamento dati cloud viene usata in un'operazione di copia da cloud a cloud. Si tratta di un'unità di misura che rappresenta la potenza, ossia la combinazione tra CPU, memoria e allocazione di risorse di rete, di una singola unità in Data Factory. È possibile ottenere una velocità effettiva di copia maggiore usando un numero più elevato di unità di spostamento dati per alcuni scenari. Per informazioni dettagliate, vedere la sezione [Unità di spostamento dati cloud](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units).

<sup>4</sup> Integration Runtime (IR) è l'infrastruttura di calcolo usata da Azure Data Factory per fornire le funzionalità di integrazione di dati seguenti in diversi ambienti di rete: spostamento dati, recapito di attività ai servizi di calcolo, esecuzione di pacchetti SSIS. Per altre informazioni, vedere [Panoramica di Integration Runtime](../articles/data-factory/concepts-integration-runtime.md).

| **Risorsa** | **Limite inferiore predefinito** | **Limite minimo** |
| --- | --- | --- |
| Intervallo di pianificazione |15 minuti |15 minuti |
| Intervallo tra tentativi |1 secondo |1 secondo |
| Valore di timeout del tentativo |1 secondo |1 secondo |

#### <a name="web-service-call-limits"></a>Limiti di chiamata del servizio Web
Azure Resource Manager presenta limiti per le chiamate API. È possibile effettuare chiamate API a una velocità all'interno di [limiti API di gestione risorse di Azure](../articles/azure-subscription-service-limits.md#resource-group-limits).
