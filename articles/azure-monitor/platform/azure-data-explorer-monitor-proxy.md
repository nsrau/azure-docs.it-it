---
title: Eseguire query sui dati in monitoraggio di Azure con Esplora dati di Azure (anteprima)
description: Usare Esplora dati di Azure per eseguire query tra prodotti tra Esplora dati di Azure, aree di lavoro Log Analytics e applicazioni Application Insights classiche in monitoraggio di Azure.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 2a21d7a06e8a92022b620704d1fb51a303da3ae0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185981"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Eseguire query sui dati in monitoraggio di Azure con Esplora dati di Azure (anteprima)
Il cluster del proxy di Esplora dati di Azure consente di eseguire query tra prodotti tra Esplora dati di Azure, Log Analytics aree di lavoro e applicazioni Application Insights classiche in monitoraggio di Azure. È possibile eseguire il mapping delle aree di lavoro Log Analytics in monitoraggio di Azure o nelle app Application Insights classiche come cluster proxy. È quindi possibile eseguire una query sul cluster proxy usando gli strumenti Esplora dati di Azure e farvi riferimento in una query tra cluster. Questo articolo illustra come connettersi a un cluster proxy, aggiungere un cluster proxy ad Azure Esplora dati interfaccia utente Web ed eseguire query sulle aree di lavoro Log Analytics o sulle app di Application Insights classiche da Azure Esplora dati.

Il diagramma seguente illustra il flusso del proxy del Esplora dati di Azure:

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-monitor-proxy-flow.png" alt-text="Flusso del proxy di Esplora dati di Azure.":::


> [!NOTE]
> Il proxy di Azure Esplora dati è in anteprima pubblica. [Connettersi al proxy](#connect-to-the-proxy) per abilitare la funzionalità proxy per i cluster. 

## <a name="connect-to-the-proxy"></a>Connettersi al proxy
Per connettere l'area di lavoro Log Analytics o l'app Application Insights classica, aprire l'[interfaccia utente Web di Azure Esplora dati](https://dataexplorer.azure.com/clusters). Verificare che il cluster nativo di Azure Esplora dati, ad esempio cluster della *Guida* , sia visualizzato nel menu a sinistra prima di connettersi al cluster Log Analytics o Application Insights.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Cluster nativo di Azure Esplora dati.":::

Fare clic su **Aggiungi cluster** , quindi aggiungere l'URL del cluster Log Analytics o Application Insights in uno dei formati seguenti. 
    
* Per Log Analytics: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
* Per Application Insights: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

Fare clic su **Aggiungi** per creare la connessione.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Aggiungere il cluster.":::
 
> [!NOTE]
> Se si aggiunge una connessione a più di un cluster proxy, assegnare a ognuno un nome diverso. In caso contrario, avranno lo stesso nome nel riquadro sinistro.

Una volta stabilita la connessione, il cluster Log Analytics o Application Insights verrà visualizzato nel riquadro sinistro con il cluster di Azure Esplora dati nativo. 

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics e i cluster di Azure Esplora dati.":::
 
> [!NOTE]
> Il numero di aree di lavoro di monitoraggio di Azure di cui è possibile eseguire il mapping è limitato a 100.

## <a name="create-queries-using-azure-monitor-data"></a>Creare query usando i dati di monitoraggio di Azure

È possibile eseguire le query usando gli strumenti client che supportano le query kusto, ad esempio: kusto Explorer, Azure Esplora dati Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens e REST API.

> [!NOTE]
> La funzionalità proxy di Azure Esplora dati viene usata solo per il recupero dei dati. Per altre informazioni, vedere [supporto della funzione](#function-supportability).

> [!TIP]
> * Il nome del database deve avere lo stesso nome della risorsa specificata nel cluster proxy. I nomi fanno distinzione tra maiuscole e minuscole.
> * In query tra cluster assicurarsi che la denominazione delle app Application Insights e delle aree di lavoro Log Analytics sia corretta.
>     * Se i nomi contengono caratteri speciali, questi vengono sostituiti dalla codifica URL nel nome del cluster proxy. 
>     * Se i nomi includono caratteri che non soddisfano [le regole del nome dell'identificatore KQL](/azure/data-explorer/kusto/query/schema-entities/entity-names), vengono sostituiti dal carattere di tratteggio **-** .

### <a name="direct-query-from-your-log-analytics-or-application-insights-proxy-cluster"></a>Eseguire una query diretta dal cluster Log Analytics o Application Insights proxy

Eseguire query sul cluster Log Analytics o Application Insights. Verificare che il cluster sia selezionato nel riquadro sinistro. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the Log Analaytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Eseguire query Log Analytics area di lavoro.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-proxy-cluster-and-the-azure-data-explorer-native-cluster"></a>Query incrociata del cluster di Log Analytics o Application Insights proxy e del cluster Azure Esplora dati native

Quando si eseguono query tra cluster dal proxy, verificare che nel riquadro sinistro sia selezionato il cluster nativo di Azure Esplora dati. Gli esempi seguenti illustrano la combinazione di tabelle cluster di Azure Esplora dati usando l'operatore [Union](/azure/data-explorer/kusto/query/unionoperator) con un'area di lavoro log Analytics.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```
L'uso dell' [ `join` operatore](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor), anziché Union, potrebbe richiedere un [Suggerimento](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor#join-hints) per eseguirlo in un cluster nativo di Azure Esplora dati (e non nel proxy). 

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Unire i dati di un cluster Esplora dati di Azure in un tenant con una risorsa di monitoraggio di Azure in un altro

Le query tra tenant non sono supportate dal proxy di Esplora dati di Azure. È stato eseguito l'accesso a un singolo tenant per eseguire la query che si estende su entrambe le risorse.

Se la risorsa di Azure Esplora dati si trova nel tenant ' A ' e Log Analytics area di lavoro si trova nel tenant ' B ', usare uno dei due metodi seguenti:

- Esplora dati di Azure consente di aggiungere ruoli per le entità in tenant diversi. Aggiungere l'ID utente nel tenant ' B ' come utente autorizzato nel cluster Esplora dati di Azure. Verificare che la proprietà *[' TrustedExternalTenant '](/powershell/module/az.kusto/update-azkustocluster)* nel cluster Esplora dati di Azure contenga il tenant ' B '. Eseguire la query incrociata completamente nel tenant ' B '.

- Usare [Lighthouse](../../lighthouse/index.yml) per proiettare la risorsa di monitoraggio di Azure nel tenant ' A '.

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Connettersi a cluster di Esplora dati di Azure da tenant diversi

Kusto Explorer accede automaticamente al tenant a cui appartiene originariamente l'account utente. Per accedere alle risorse in altri tenant con lo stesso account utente, `tenantId` è necessario specificare in modo esplicito nella stringa di connessione: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Supporto della funzione

Il cluster proxy di Azure Esplora dati supporta le funzioni sia per Log Analytics che per Application Insights. Questa funzionalità consente alle query tra cluster di fare riferimento direttamente a una funzione tabulare di monitoraggio di Azure.

Il proxy supporta i comandi seguenti:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

Nell'immagine seguente viene illustrato un esempio di esecuzione di query su una funzione tabulare dall'interfaccia utente Web di Azure Esplora dati. Per usare la funzione, eseguire il nome nella finestra query.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Eseguire una query su una funzione tabulare dall'interfaccia utente Web di Azure Esplora dati.":::
 
> [!NOTE]
> Monitoraggio di Azure supporta solo funzioni tabulari che non supportano parametri.

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
- Informazioni su come [scrivere query in Azure Esplora dati](/azure/data-explorer/write-queries).