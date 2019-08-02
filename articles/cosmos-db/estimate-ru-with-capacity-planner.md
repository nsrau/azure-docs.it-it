---
title: Stimare i costi con Azure Cosmos DB Capacity Planner
description: Il Azure Cosmos DB Capacity Planner consente di stimare la velocità effettiva (UR/sec) richiesta e i costi del carico di lavoro. Questo articolo descrive come usare la nuova versione di Capacity Planner per stimare la velocità effettiva e i costi richiesti.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707630"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Stimare ur/sec usando Azure Cosmos DB Capacity Planner

La configurazione di database e contenitori di Azure Cosmos con la giusta quantità di velocità effettiva con provisioning o [unità richiesta (UR/sec)](request-units.md)per il carico di lavoro è essenziale per l'ottimizzazione dei costi e delle prestazioni. Questo articolo descrive come usare Azure Cosmos DB Capacity [Planner](https://cosmos.azure.com/capacitycalculator/) per ottenere una stima delle UR/sec richieste e dei costi del carico di lavoro. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Come stimare la velocità effettiva e i costi con Azure Cosmos DB Capacity Planner

Capacity Planner può essere usato in due modalità.

|**Modalità**  |**Descrizione**  |
|---------|---------|
|Basic|Fornisce una stima dei costi e delle UR veloce, ad alto livello. Questa modalità presuppone le impostazioni predefinite di Azure Cosmos DB per i criteri di indicizzazione, la coerenza e altri parametri. <br/><br/>Utilizzare la modalità di base per una stima rapida e di alto livello quando si valuta un potenziale carico di lavoro da eseguire su Azure Cosmos DB.|
|Avanzate|Fornisce una stima dei costi e delle UR più dettagliata, con la possibilità di ottimizzare le impostazioni aggiuntive, ovvero i criteri di indicizzazione, il livello di coerenza e altri parametri che influiscono sul costo e sulla velocità effettiva. <br/><br/>Utilizzare la modalità avanzata quando si stimano le UR/sec per un nuovo progetto o si desidera una stima più dettagliata. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Stima della velocità effettiva con provisioning e dei costi con la modalità di base
Per ottenere una rapida stima del carico di lavoro usando la modalità di base, passare a [Capacity Planner](https://cosmos.azure.com/capacitycalculator/). Immettere i parametri seguenti in base al carico di lavoro: 

|**Input**  |**Descrizione**  |
|---------|---------|
|Numero di aree|Azure Cosmos DB è disponibile in tutte le aree di Azure. Selezionare il numero di aree necessarie per il carico di lavoro. È possibile associare un numero qualsiasi di aree con l'account Cosmos. Per ulteriori informazioni, vedere [distribuzione globale](distribute-data-globally.md) in Azure Cosmos DB.|
|Multi-region writes (Scritture in più aree)|Se si abilitano le Scritture in più [aree](distribute-data-globally.md#key-benefits-of-global-distribution), l'applicazione può leggere e scrivere in qualsiasi area di Azure. Se si disabilitano le Scritture in più aree, l'applicazione può scrivere i dati in una singola area. <br/><br/> Abilitare le Scritture in più aree se si prevede un carico di lavoro attivo-attivo che richiede Scritture a bassa latenza in aree diverse. Ad esempio, un carico di lavoro di Internet delle cose che scrive i dati nel database in volumi elevati in aree diverse. <br/><br/> Scritture in più aree garantisce la disponibilità in lettura e scrittura del 99,999%. Le Scritture in più aree richiedono una velocità effettiva maggiore rispetto alle singole aree di scrittura. Per altre informazioni, vedere l'articolo [relativo alla modalità di utilizzo di ur per le aree singole e con più scritture](optimize-cost-regions.md) .|
|Totale dati archiviati (per area)|Totale dei dati stimati archiviati in GB in un'unica area.|
|Dimensioni dell'elemento|Dimensioni stimate dell'elemento dati (ad esempio documento), comprese tra 1 KB e 2 MB. |
|Letture/sec per area|Numero di letture previste al secondo. |
|Scritture/sec per area|Numero di scritture previste al secondo. |

Dopo aver compilato i dettagli richiesti, selezionare **Calcola**. La scheda **stima costo** Mostra il costo totale per l'archiviazione e la velocità effettiva con provisioning. È possibile espandere il collegamento **Mostra dettagli** in questa scheda per ottenere la suddivisione della velocità effettiva necessaria per le richieste di lettura e scrittura. Ogni volta che si modifica il valore di qualsiasi campo, selezionare **Calcola** per calcolare nuovamente il costo stimato. 

![Modalità di base di Capacity Planner](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Stima della velocità effettiva con provisioning e dei costi con la modalità avanzata

La modalità avanzata consente di fornire altre impostazioni che influiscano sulla stima delle UR/sec. Per usare questa opzione, passare a [Capacity Planner](https://cosmos.azure.com/capacitycalculator/) e accedere allo strumento con un account usato per Azure. L'opzione di accesso è disponibile nell'angolo destro. 

Dopo aver eseguito l'accesso, è possibile visualizzare campi aggiuntivi rispetto ai campi in modalità di base. Immettere i parametri aggiuntivi in base al carico di lavoro. 

|**Input**  |**Descrizione**  |
|---------|---------|
|API|Azure Cosmos DB è un servizio multimodello e multiapi. Per i nuovi carichi di lavoro, selezionare API SQL (Core). |
|Numero di aree|Azure Cosmos DB è disponibile in tutte le aree di Azure. Selezionare il numero di aree necessarie per il carico di lavoro. È possibile associare un numero qualsiasi di aree con l'account Cosmos. Per ulteriori informazioni, vedere [distribuzione globale](distribute-data-globally.md) in Azure Cosmos DB.|
|Multi-region writes (Scritture in più aree)|Se si abilitano le Scritture in più [aree](distribute-data-globally.md#key-benefits-of-global-distribution), l'applicazione può leggere e scrivere in qualsiasi area di Azure. Se si disabilitano le Scritture in più aree, l'applicazione può scrivere i dati in una singola area. <br/><br/> Abilitare le Scritture in più aree se si prevede un carico di lavoro attivo-attivo che richiede Scritture a bassa latenza in aree diverse. Ad esempio, un carico di lavoro di Internet delle cose che scrive i dati nel database in volumi elevati in aree diverse. <br/><br/> Scritture in più aree garantisce la disponibilità in lettura e scrittura del 99,999%. Le Scritture in più aree richiedono una velocità effettiva maggiore rispetto alle singole aree di scrittura. Per altre informazioni, vedere l'articolo [relativo alla modalità di utilizzo di ur per le aree singole e con più scritture](optimize-cost-regions.md) .|
|Coerenza predefinita|Azure Cosmos DB supporta 5 livelli di coerenza, per consentire agli sviluppatori di bilanciare il compromesso tra coerenza, disponibilità e compromessi di latenza. Per altre informazioni, vedere l'articolo relativo ai [livelli di coerenza](consistency-levels.md) . <br/><br/> Per impostazione predefinita, Azure Cosmos DB usa la coerenza di sessione, che garantisce la possibilità di leggere le proprie scritture in una sessione. <br/><br/> La scelta di un decadimento forte o ristretto richiede il doppio delle UR/sec richieste per le letture, rispetto alla sessione, al prefisso coerente e alla coerenza finale. La coerenza assoluta con le Scritture in più aree non è supportata e per impostazione predefinita le Scritture in una singola area verranno automaticamente eseguite con coerenza assoluta. |
|Criterio di indicizzazione|Per impostazione predefinita, Azure Cosmos DB [indicizza tutte le proprietà](index-policy.md) in tutti gli elementi per le query flessibili ed efficienti (esegue il mapping ai criteri di indicizzazione **automatica** ). <br/><br/> Se si sceglie **disattivato**, nessuna delle proprietà viene indicizzata. Ciò comporta un addebito delle unità richiesta più basso per le Scritture. Selezionare **off** Policy se si prevede di eseguire solo [letture di punti](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) (ricerche di valori chiave) e/o Scritture e nessuna query. <br/><br/> I criteri di indicizzazione personalizzati consentono di includere o escludere proprietà specifiche dall'indice per la velocità effettiva di scrittura e l'archiviazione inferiori. Per altre informazioni, vedere l'articolo relativo ai [criteri](index-overview.md) di indicizzazione e ai criteri di indicizzazione di [esempio](how-to-manage-indexing-policy.md#indexing-policy-examples)|
|Totale dati archiviati (per area)|Totale dei dati stimati archiviati in GB in un'unica area.|
|Modalità del carico di lavoro|Selezionare **Steady** se il volume del carico di lavoro è costante. <br/><br/> Selezionare **variabile** se il volume del carico di lavoro cambia nel tempo.  Ad esempio, durante un giorno o un mese specifico. <br/><br/> Le impostazioni seguenti sono disponibili se si sceglie l'opzione variabile del carico di lavoro:<ul><li>Percentuale di tempo massimo: Percentuale di tempo in un mese in cui il carico di lavoro richiede velocità effettiva massima (massima). <br/><br/> Se, ad esempio, si dispone di un carico di lavoro con attività elevate durante le 9.00 e 18 giorni lavorativi, la percentuale di tempo al picco è la seguente: 45 ore al picco/730 ore/mese = ~ 6%.<br/><br/></li><li>Letture/sec per area al picco: numero di letture previste al secondo al picco.</li><li>Scritture/sec per area al picco: numero di scritture previste al secondo al picco.</li><li>Letture/sec per area al di fuori del picco: numero di letture previste al secondo durante il periodo di indisponibilità.</li><li>Scritture/sec per area al di fuori del picco: numero di scritture previste al secondo durante il periodo di indisponibilità.</li></ul>Con gli intervalli di picco e di minore attività, è possibile ottimizzare il costo a livello di codice, aumentando di conseguenza [la velocità effettiva](set-throughput.md#update-throughput-on-a-database-or-a-container) con provisioning.|
|Dimensioni dell'elemento|Dimensione dell'elemento di dati (ad esempio documento), da 1 KB a 2 MB. <br/><br/>È anche possibile caricare un documento di **esempio (JSON)** per una stima più accurata.<br/><br/>Se il carico di lavoro include più tipi di elementi (con contenuto JSON diverso) nello stesso contenitore, è possibile caricare più documenti JSON e ottenere la stima. Usare il pulsante **Aggiungi nuovo elemento** per aggiungere più documenti JSON di esempio.|

È anche possibile usare il pulsante **Salva stima** per scaricare un file CSV contenente la stima corrente. 

![Modalità avanzata di Capacity Planner](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

I prezzi indicati in Azure Cosmos DB Capacity Planner sono stime basate sulle tariffe pubbliche dei prezzi per la velocità effettiva e l'archiviazione. Tutti i prezzi sono indicati in dollari statunitensi. Per visualizzare tutte le tariffe per area, fare riferimento alla [pagina dei prezzi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) .  

## <a name="estimating-throughput-for-queries"></a>Stima della velocità effettiva per le query

Il calcolatore di capacità di Azure Cosmos presuppone letture di punti (lettura di un singolo elemento, ad esempio documento, ID e valore della chiave di partizione) e scritture per il carico di lavoro. Per stimare la velocità effettiva necessaria per le query, eseguire la query su un set di dati rappresentativo in un contenitore Cosmos e [ottenere l'addebito delle UR](find-request-unit-charge.md). Moltiplicare le UR addebitate per il numero di query che si prevede di eseguire al secondo per ottenere il totale delle UR/sec richieste. 

Se, ad esempio, il carico di lavoro richiede ``SELECT * FROM c WHERE c.id = 'Alice'`` una query, che viene eseguita 100 volte al secondo e l'addebito delle UR della query è 10 UR, per soddisfare le richieste sono necessarie 100 query/sec * 10 ur/query = 1000 ur/s. Aggiungere le UR/sec alle UR/sec richieste per le operazioni di lettura o scrittura eseguite nel carico di lavoro.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [modello di determinazione prezzi di Azure Cosmos DB](how-pricing-works.md).
* Creare un nuovo [account, un database e un contenitore Cosmos](create-cosmosdb-resources-portal.md).
* Informazioni su come [ottimizzare il costo della velocità effettiva](optimize-cost-throughput.md)con provisioning.
* Informazioni su come [ottimizzare i costi con la capacità riservata](cosmos-db-reserved-capacity.md).

