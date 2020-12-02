---
title: Eseguire query sui dati in monitoraggio di Azure con Esplora dati di Azure (anteprima)
description: Usare Esplora dati di Azure per eseguire query tra prodotti tra Esplora dati di Azure, aree di lavoro Log Analytics e applicazioni Application Insights classiche in monitoraggio di Azure.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: dd29b18dda46a00966a0e5adea7e06be8f43ad35
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444958"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Eseguire query sui dati in monitoraggio di Azure con Esplora dati di Azure (anteprima)

Il Esplora dati di Azure supporta le query tra i servizi tra Esplora dati di Azure, [Application Insights (ai)](/azure/azure-monitor/app/app-insights-overview)e [log Analytics (la)](/azure/azure-monitor/platform/data-platform-logs). È quindi possibile eseguire una query nell'area di lavoro Log Analytics/Application Insights usando gli strumenti di Esplora dati di Azure e farvi riferimento in una query tra servizi. Questo articolo illustra come eseguire una query tra servizi e come aggiungere l'area di lavoro Log Analytics/Application Insights ad Azure Esplora dati interfaccia utente Web.

Flusso di query tra servizi Esplora dati di Azure: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="flusso del proxy di Esplora dati di Azure.":::

> [!NOTE]
> * La possibilità di eseguire query sui dati di monitoraggio di Azure da Azure Esplora dati, direttamente da Azure Esplora dati client Tools o indirettamente eseguendo una query su un cluster di Azure Esplora dati, è in modalità di anteprima.
>* Per eventuali domande, contattare il team di [query tra i servizi](mailto:adxproxy@microsoft.com) .

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Aggiungere un'area di lavoro Log Analytics/Application Insights agli strumenti client di Azure Esplora dati

1. Verificare che il cluster nativo di Azure Esplora dati, ad esempio cluster della *Guida* , sia visualizzato nel menu a sinistra prima di connettersi al cluster Log Analytics o Application Insights.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Cluster nativo di Azure Esplora dati.":::

 Nell'interfaccia utente di Esplora dati di Azure https://dataexplorer.azure.com/clusters) , selezionare **Aggiungi cluster**.

2. Nella finestra **Aggiungi cluster** aggiungere l'URL del cluster la o di intelligenza artificiale.

    * Per LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Per intelligenza artificiale: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Selezionare **Aggiungi**.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Aggiungere il cluster.":::
 
>[!NOTE]
>Se si aggiunge una connessione a più di un'area di lavoro Log Analytics/Application Insights, assegnare a ognuno un nome diverso. In caso contrario, avranno lo stesso nome nel riquadro sinistro.

 Una volta stabilita la connessione, l'area di lavoro Log Analytics o Application Insights verrà visualizzata nel riquadro sinistro con il cluster di Azure Esplora dati nativo.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics e i cluster di Azure Esplora dati.":::
 
> [!NOTE]
> Il numero di aree di lavoro di monitoraggio di Azure di cui è possibile eseguire il mapping è limitato a 100.

## <a name="create-queries-using-azure-monitor-data"></a>Creare query usando i dati di monitoraggio di Azure

È possibile eseguire le query usando gli strumenti client che supportano le query kusto, ad esempio: kusto Explorer, Azure Esplora dati Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Lens, REST API.

> [!NOTE]
> La capacità di query tra servizi viene utilizzata solo per il recupero dei dati. Per altre informazioni, vedere [supporto della funzione](#function-supportability).

> [!TIP]
> * Il nome del database deve avere lo stesso nome della risorsa specificata nella query tra servizi. I nomi fanno distinzione tra maiuscole e minuscole.
> * In query tra cluster assicurarsi che la denominazione delle app Application Insights e delle aree di lavoro Log Analytics sia corretta.
> * Se i nomi contengono caratteri speciali, vengono sostituiti dalla codifica URL nella query tra servizi.
> * Se i nomi includono caratteri che non soddisfano [le regole del nome dell'identificatore KQL](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names), vengono sostituiti dal carattere di tratteggio **-** .

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Eseguire query dirette sulle aree di lavoro Log Analytics o Application Insights da strumenti client di Azure Esplora dati

Eseguire query sulle aree di lavoro Log Analytics o Application Insights. Verificare che l'area di lavoro sia selezionata nel riquadro sinistro.
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Eseguire query Log Analytics area di lavoro.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Query incrociata del Log Analytics o Application Insights e del cluster nativo di Azure Esplora dati

Quando si eseguono query del servizio tra cluster, verificare che nel riquadro sinistro sia selezionato il cluster nativo di Azure Esplora dati. Gli esempi seguenti illustrano come combinare le tabelle del cluster di Azure Esplora dati [usando Unione](/azure/data-explorer/kusto/query/unionoperator) con log Analytics area di lavoro.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Query tra i servizi dalla Esplora dati di Azure.":::

L'uso dell' [ `join` operatore](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator), anziché Union, può richiedere un [`hint`](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator#join-hints) per eseguirlo in un cluster nativo di Azure Esplora dati.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Unire i dati di un cluster Esplora dati di Azure in un tenant con una risorsa di monitoraggio di Azure in un altro

Le query tra tenant tra i servizi non sono supportate. È stato eseguito l'accesso a un singolo tenant per eseguire la query che si estende su entrambe le risorse.

Se la risorsa di Azure Esplora dati si trova nel tenant ' A ' e Log Analytics area di lavoro si trova nel tenant ' B ', usare uno dei due metodi seguenti:

1. Esplora dati di Azure consente di aggiungere ruoli per le entità in tenant diversi. Aggiungere l'ID utente nel tenant ' B ' come utente autorizzato nel cluster Esplora dati di Azure. Verificare che la proprietà *[' TrustedExternalTenant '](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* nel cluster Esplora dati di Azure contenga il tenant ' B '. Eseguire la query incrociata completamente nel tenant ' B '.

2. Usare [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) per proiettare la risorsa di monitoraggio di Azure nel tenant ' A '.
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Connettersi a cluster di Esplora dati di Azure da tenant diversi

Kusto Explorer accede automaticamente al tenant a cui appartiene originariamente l'account utente. Per accedere alle risorse in altri tenant con lo stesso account utente, `tenantId` è necessario specificare in modo esplicito nella stringa di connessione: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Supporto della funzione

Le query di Azure Esplora dati cross Service supportano le funzioni sia per Application Insights che per Log Analytics.
Questa funzionalità consente alle query tra cluster di fare riferimento direttamente a una funzione tabulare di monitoraggio di Azure.
Con la query Cross Service sono supportati i comandi seguenti:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

Nell'immagine seguente viene illustrato un esempio di esecuzione di query su una funzione tabulare dall'interfaccia utente Web di Azure Esplora dati.
Per usare la funzione, eseguire il nome nella finestra query.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Eseguire una query su una funzione tabulare dall'interfaccia utente Web di Azure Esplora dati.":::

## <a name="additional-syntax-examples"></a>Esempi di sintassi aggiuntivi

Quando si chiamano i cluster Log Analytics o Application Insights, sono disponibili le opzioni di sintassi seguenti:

|Descrizione della sintassi  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Database all'interno di un cluster che contiene solo la risorsa definita in questa sottoscrizione (**scelta consigliata per le query tra cluster**) |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| Cluster che contiene tutte le app e le aree di lavoro in questa sottoscrizione    |     cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|Cluster che contiene tutte le app e le aree di lavoro nella sottoscrizione e che sono membri di questo gruppo di risorse    |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|Cluster che contiene solo la risorsa definita in questa sottoscrizione      |    cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sulla [struttura dei dati di log Analytics aree di lavoro e Application Insights](data-platform-logs.md).
- Informazioni su come [scrivere query in Azure Esplora dati](https://docs.microsoft.com/azure/data-explorer/write-queries).