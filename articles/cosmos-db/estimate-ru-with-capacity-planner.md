---
title: Stimare i costi utilizzando il pianificatore di capacità di Azure Cosmos DB
description: Il pianificatore di capacità del database Cosmos di Azure consente di stimare la velocità effettiva (RU/s) necessaria e il costo per il carico di lavoro. In questo articolo viene descritto come utilizzare la nuova versione di Capacity Planner per stimare la velocità effettiva e il costo necessari.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68707630"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Stimare RU/s utilizzando il pianificatore di capacità di Azure Cosmos DB

La configurazione dei database e dei contenitori di Azure Cosmos con la giusta quantità di velocità effettiva di cui è stato eseguito il provisioning o le unità di [richiesta (RU/s)](request-units.md)per il carico di lavoro è essenziale per ottimizzare costi e prestazioni. Questo articolo descrive come usare il [pianificatore](https://cosmos.azure.com/capacitycalculator/) di capacità del database Cosmos di Azure per ottenere una stima delle RU/s necessarie e del costo del carico di lavoro. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Come stimare la velocità effettiva e i costi con Azure Cosmos DB capacity planner

Il pianificatore di capacità può essere utilizzato in due modalità.

|**Modalità**  |**Descrizione**  |
|---------|---------|
|Basic|Fornisce una rapida, alta RU/ s e la stima dei costi. Questa modalità presuppone le impostazioni predefinite di Azure Cosmos DB per i criteri di indicizzazione, la coerenza e altri parametri. <br/><br/>Usare la modalità di base per una stima rapida e di alto livello quando si valuta un carico di lavoro potenziale da eseguire in Azure Cosmos DB.|
|Avanzate|Fornisce una specifica RU/s e una stima dei costi, con la possibilità di ottimizzare impostazioni aggiuntive, ovvero criteri di indicizzazione, livello di coerenza e altri parametri che influiscono sul costo e sulla velocità effettiva. <br/><br/>Utilizzare la modalità avanzata quando si stimano RU/s per un nuovo progetto o si desidera una stima più dettagliata. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Stimare la velocità effettiva e i costi di provisioning utilizzando la modalità di base
Per ottenere una stima rapida del carico di lavoro utilizzando la modalità di base, passare a [Capacity Planner](https://cosmos.azure.com/capacitycalculator/). Immettere i parametri seguenti in base al carico di lavoro: 

|**Input**  |**Descrizione**  |
|---------|---------|
|Numero di regioni|Azure Cosmos DB è disponibile in tutte le aree di Azure. Selezionare il numero di aree necessarie per il carico di lavoro. Puoi associare un numero qualsiasi di regioni al tuo account Cosmos. Per altre informazioni, vedere distribuzione globale in Azure Cosmos DB.See [global distribution](distribute-data-globally.md) in Azure Cosmos DB for more details.|
|Multi-region writes (Scritture in più aree)|Se si abilitano le scritture in più aree, l'applicazione può leggere e scrivere in qualsiasi area di Azure.If you enable [multi-region writes](distribute-data-globally.md#key-benefits-of-global-distribution), your application can read and write to any Azure region. Se si disabilitano le scritture in più aree, l'applicazione può scrivere dati in una singola area. <br/><br/> Abilitare le scritture in più aree se si prevede di avere un carico di lavoro attivo-attivo che richiede scritture a bassa latenza in aree diverse. Ad esempio, un carico di lavoro IOT che scrive i dati nel database in volumi elevati in aree diverse. <br/><br/> Le scritture in più aree garantiscono la disponibilità di lettura e scrittura al 99,999%. Le scritture in più aree richiedono una velocità effettiva maggiore rispetto alle singole aree di scrittura. Per altre informazioni, vedere l'articolo come le RU sono diverse per le [aree a scrittura singola e multipla.](optimize-cost-regions.md)|
|Dati totali memorizzati (per regione)|Dati stimati totali archiviati in GB in una singola area.|
|Dimensioni dell'elemento|Dimensioni stimate dell'elemento di dati (ad esempio il documento), comprese tra 1 KB e 2 MB. |
|Letture/sec per area|Numero di letture previste al secondo. |
|Scritture/sec per area|Numero di scritture previste al secondo. |

Dopo aver compilato i dettagli richiesti, selezionare **Calcola**. La scheda **Stima dei costi** mostra il costo totale per l'archiviazione e la velocità effettiva di cui è stato eseguito il provisioning. È possibile espandere il collegamento **Mostra dettagli** in questa scheda per ottenere la suddivisione della velocità effettiva necessaria per le richieste di lettura e scrittura. Ogni volta che si modifica il valore di un campo, selezionare **Calcola** per ricalcolare il costo stimato. 

![Modalità di base di Capacity planner](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Stimare la velocità effettiva e i costi di provisioning utilizzando la modalità avanzata

La modalità avanzata consente di fornire ulteriori impostazioni che influiscono sulla stima RU/s. Per usare questa opzione, passare a Capacity Planner e accedere allo strumento con un account usato per Azure.To use this option, navigate to the [capacity planner](https://cosmos.azure.com/capacitycalculator/) and sign in to the tool with an account you use for Azure. L'opzione di accesso è disponibile nell'angolo destro. 

Dopo aver effettuato l'accesso, è possibile visualizzare campi aggiuntivi rispetto ai campi in modalità di base. Immettere i parametri aggiuntivi in base al carico di lavoro. 

|**Input**  |**Descrizione**  |
|---------|---------|
|API|Azure Cosmos DB è un servizio multimodello e multi-API. Per i nuovi carichi di lavoro, selezionare API SQL (Core). |
|Numero di regioni|Azure Cosmos DB è disponibile in tutte le aree di Azure. Selezionare il numero di aree necessarie per il carico di lavoro. Puoi associare un numero qualsiasi di regioni al tuo account Cosmos. Per altre informazioni, vedere distribuzione globale in Azure Cosmos DB.See [global distribution](distribute-data-globally.md) in Azure Cosmos DB for more details.|
|Multi-region writes (Scritture in più aree)|Se si abilitano le scritture in più aree, l'applicazione può leggere e scrivere in qualsiasi area di Azure.If you enable [multi-region writes](distribute-data-globally.md#key-benefits-of-global-distribution), your application can read and write to any Azure region. Se si disabilitano le scritture in più aree, l'applicazione può scrivere dati in una singola area. <br/><br/> Abilitare le scritture in più aree se si prevede di avere un carico di lavoro attivo-attivo che richiede scritture a bassa latenza in aree diverse. Ad esempio, un carico di lavoro IOT che scrive i dati nel database in volumi elevati in aree diverse. <br/><br/> Le scritture in più aree garantiscono la disponibilità di lettura e scrittura al 99,999%. Le scritture in più aree richiedono una velocità effettiva maggiore rispetto alle singole aree di scrittura. Per altre informazioni, vedere l'articolo come le RU sono diverse per le [aree a scrittura singola e multipla.](optimize-cost-regions.md)|
|Coerenza predefinita|Il database Cosmos di Azure supporta 5 livelli di coerenza, per consentire agli sviluppatori di bilanciare il compromesso tra scambi di coerenza, disponibilità e latenza. Per altre informazioni, vedere l'articolo sui livelli di [coerenza.](consistency-levels.md) <br/><br/> Per impostazione predefinita, il database Cosmos di Azure usa la coerenza della sessione, che garantisce la possibilità di leggere le proprie scritture in una sessione. <br/><br/> La scelta di una talesness forte o delimitata richiederà il doppio delle RU necessarie per le letture, rispetto alla sessione, al prefisso coerente e alla coerenza finale. La coerenza elevata con le scritture in più aree non è supportata e verrà automaticamente impostato su scritture a area singola con coerenza elevata. |
|Criterio di indicizzazione|Per impostazione predefinita, Azure Cosmos DB [indicizza tutte le proprietà](index-policy.md) in tutti gli elementi per query flessibili ed efficienti (mapping ai criteri di indicizzazione **automatica).** <br/><br/> Se si sceglie **off**, nessuna delle proprietà viene indicizzata. Ciò si traduce nella carica RU più bassa per le scritture. Selezionare **off** policy se si prevede di eseguire solo [letture di punti](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) (ricerche di valori chiave) e/o scritture e nessuna query. <br/><br/> I criteri di indicizzazione personalizzati consentono di includere o escludere proprietà specifiche dall'indice per una velocità effettiva di scrittura e archiviazione di livello inferiore. Per altre informazioni, vedere gli articoli relativi [ai criteri di indicizzazione](index-overview.md) e [ai criteri](how-to-manage-indexing-policy.md#indexing-policy-examples) di indicizzazione di esempio.|
|Dati totali memorizzati (per regione)|Dati stimati totali archiviati in GB in una singola area.|
|Modalità carico di lavoroWorkload mode|Selezionare **Stabile** se il volume del carico di lavoro è costante. <br/><br/> Selezionare **Variabile** se il volume del carico di lavoro cambia nel tempo.  Ad esempio, durante un giorno specifico o un mese. <br/><br/> Se si sceglie l'opzione del carico di lavoro variabile, sono disponibili le impostazioni seguenti:The following settings are available if you choose the variable workload option:<ul><li>Percentuale di tempo al picco: percentuale di tempo in un mese in cui il carico di lavoro richiede velocità effettiva di picco (più alta). <br/><br/> Ad esempio, se si dispone di un carico di lavoro con attività elevata durante le 9:00 – 18:00 di orario lavorativo nei giorni feriali, la percentuale di tempo al picco è: 45 ore di punta / 730 ore / mese - 6%.<br/><br/></li><li>Letture/sec per area al picco: numero di letture previste al secondo al picco.</li><li>Scritture/sec per area al picco: numero di scritture previste al secondo al picco.</li><li>Letture/sec per area fuori picco: numero di letture previste al secondo durante le ore non di punta.</li><li>Scritture/sec per area fuori picco: numero di scritture previste al secondo durante il picco.</li></ul>Con gli intervalli di picco e non di punta, è possibile ottimizzare i costi scalando a livello di [codice la velocità effettiva di cui è stato eseguito il provisioning](set-throughput.md#update-throughput-on-a-database-or-a-container) verso l'alto e verso il basso di conseguenza.|
|Dimensioni dell'elemento|La dimensione dell'elemento di dati (ad esempio il documento), compreso tra 1 KB e 2 MB. <br/><br/>È anche possibile caricare un documento di **esempio (JSON)** per una stima più accurata.<br/><br/>Se il carico di lavoro include più tipi di elementi (con contenuto JSON diverso) nello stesso contenitore, è possibile caricare più documenti JSON e ottenere la stima. Usare il pulsante **Aggiungi nuovo elemento** per aggiungere più documenti JSON di esempio.|

È inoltre possibile utilizzare il pulsante **Salva stima** per scaricare un file CSV contenente la stima corrente. 

![Modalità avanzata pianificazione capacità](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

I prezzi mostrati in Azure Cosmos DB capacity planner sono stime basate sui prezzi pubblici per la velocità effettiva e l'archiviazione. Tutti i prezzi sono indicati in dollari USA. Fare riferimento alla pagina dei prezzi di [Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) per visualizzare tutte le tariffe per area.  

## <a name="estimating-throughput-for-queries"></a>Stima della velocità effettiva per le query

Il calcolatore di capacità di Azure Cosmos presuppone letture di punti (lettura di un singolo elemento, ad esempio documento, in base all'ID e al valore della chiave di partizione) e scrive per il carico di lavoro. Per stimare la velocità effettiva necessaria per le query, eseguire la query su un set di dati rappresentativo in un contenitore Cosmos e [ottenere l'addebito RU](find-request-unit-charge.md). Moltiplicare la carica RU per il numero di query che si prevede di eseguire al secondo per ottenere l'elenco/UR/i totale necessario. 

Ad esempio, se il carico ``SELECT * FROM c WHERE c.id = 'Alice'`` di lavoro richiede una query, che viene eseguita 100 volte al secondo e l'addebito RU della query è 10 RU, sarà necessario 100 query / sec , 10 RU / query , 1000 RU/ s in totale per soddisfare queste richieste. Aggiungere queste RU/s alle RU/s necessarie per eventuali letture o scritture che si verificano nel carico di lavoro.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul modello di [determinazione dei prezzi di Azure Cosmos DB](how-pricing-works.md).
* Creare un nuovo [account Cosmos, un database e](create-cosmosdb-resources-portal.md)un nuovo contenitore .
* Informazioni su come [ottimizzare il costo della velocità effettiva](optimize-cost-throughput.md)con provisioning.
* Informazioni su come [ottimizzare i costi con la capacità riservata.](cosmos-db-reserved-capacity.md)

