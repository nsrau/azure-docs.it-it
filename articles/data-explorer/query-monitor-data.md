---
title: Eseguire query sui dati in Monitoraggio di Azure con Azure Data Explorer (anteprima)Query data in Azure Monitor with Azure Data Explorer (Preview)
description: In questo argomento eseguire query sui dati in Monitoraggio di Azure creando un proxy di Azure Data Explorer per le query tra prodotti con Application Insights e Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: c7e98c31c0db1db3051ad66df6526dcbddb265c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560423"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Eseguire query sui dati in Monitoraggio di Azure usando Azure Data Explorer (anteprima)Query data in Azure Monitor using Azure Data Explorer (Preview)

Il cluster proxy di Azure Data Explorer (proxy ADX) è un'entità che consente di eseguire query tra prodotti tra Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)e [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) nel servizio [Azure Monitor.](/azure/azure-monitor/) È possibile eseguire il mapping delle aree di lavoro di Log Analytics di Monitoraggio di Azure o delle app di Application Insights come cluster proxy. È quindi possibile eseguire query sul cluster proxy usando gli strumenti di Azure Data Explorer e farvi riferimento in una query tra cluster. L'articolo illustra come connettersi a un cluster proxy, aggiungere un cluster proxy all'interfaccia utente Web di Azure Data Explorer ed eseguire query sulle app di controllo dell'apizia o sulle aree di lavoro di LaNella da Azure Data Explorer.The article shows how to connect to a proxy cluster, add a proxy cluster to Azure Data Explorer Web UI, and run queries against your AI apps or LA workspaces from Azure Data Explorer.

Flusso proxy di Azure Data Explorer:The Azure Data Explorer proxy flow: 

![Flusso proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Prerequisiti

> [!NOTE]
> Il proxy ADX è in modalità anteprima. [Connettersi al proxy](#connect-to-the-proxy) per abilitare la funzionalità proxy ADX per i cluster. Contattare il team [ADXProxy](mailto:adxproxy@microsoft.com) per qualsiasi domanda.

## <a name="connect-to-the-proxy"></a>Connettersi al proxy

1. Verificare che il cluster nativo di Azure Data Explorer (ad esempio il cluster *della Guida)* venga visualizzato nel menu a sinistra prima di connettersi al cluster Log Analytics o Application Insights.Verify your Azure Data Explorer native cluster (such as help cluster) appears on the left menu before you connect to your Log Analytics or Application Insights cluster.

    ![Cluster nativo ADX](media/adx-proxy/web-ui-help-cluster.png)

1. Nell'interfaccia utente dihttps://dataexplorer.azure.com/clusters)Azure Data Explorer ( selezionare **Aggiungi cluster**.

1. Nella finestra **Aggiungi cluster** aggiungere l'URL al cluster LA o AI. 
    
    * Per LA:`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Per l'IA:`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Selezionare **Aggiungi**.

    ![Aggiungere cluster](media/adx-proxy/add-cluster.png)

    Se si aggiunge una connessione a più di un cluster di proxy, assegnare a ogni cluster un nome diverso. In caso contrario, avranno tutti lo stesso nome nel riquadro sinistro.

1. Una volta stabilita la connessione, il cluster LA o AI verrà visualizzato nel riquadro sinistro con il cluster ADX nativo. 

    ![Cluster di Log Analytics e Azure Data ExplorerLog Analytics and Azure Data Explorer clusters](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Eseguire le query

È possibile eseguire le query usando strumenti client che supportano le query Kusto, ad esempio: Kusto Explorer, ADX Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, REST API.

> [!TIP]
> * Il nome del database deve avere lo stesso nome della risorsa specificata nel cluster proxy. Per i nomi viene fatta distinzione tra maiuscole e minuscole.
> * Nelle query tra cluster verificare che la denominazione delle app di Application Insights e delle aree di lavoro di Log Analytics sia corretta.
>     * Se i nomi contengono caratteri speciali, vengono sostituiti dalla codifica URL nel nome del cluster proxy. 
>     * Se i nomi includono caratteri che non soddisfano le regole del **-** [nome dell'identificatore KQL](/azure/kusto/query/schema-entities/entity-names), vengono sostituiti dal carattere trattino.

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Query diretta dal cluster di proxy ADX DI LA o AI

Eseguire query nel cluster LA o AI. Verificare che il cluster sia selezionato nel riquadro sinistro. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Area di lavoro Query LA](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Query incrociata del cluster PROXY ADX DI LA o AI e del cluster nativo ADX 

Quando si eseguono query incrociate del cluster dal proxy, verificare che il cluster nativo ADX sia selezionato nel riquadro sinistro. Negli esempi seguenti viene illustrata `union`la combinazione di tabelle cluster ADX (utilizzando ) con l'area di lavoro LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![Tra query dal proxy di Azure Data ExplorerCross query from the Azure Data Explorer proxy](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

L'utilizzo [dell'operatore `join` ](/azure/kusto/query/joinoperator), [`hint`](/azure/kusto/query/joinoperator#join-hints) anziché dell'unione, potrebbe richiedere un oggetto per eseguirlo in un cluster nativo di Azure Data Explorer (e non nel proxy). 

## <a name="additional-syntax-examples"></a>Esempi di sintassi aggiuntivi

Le opzioni di sintassi seguenti sono disponibili quando si chiamano i cluster Application Insights (AI) o Log Analytics (LA):

|Descrizione della sintassi  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Database all'interno di un cluster che contiene solo la risorsa definita in questa sottoscrizione (**consigliata per le query inter-cluster**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster che contiene tutte le app/aree di lavoro in questa sottoscrizione    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster che contiene tutte le app/aree di lavoro nella sottoscrizione e sono membri di questo gruppo di risorse    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster che contiene solo la risorsa definita in questa sottoscrizione      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Passaggi successivi

[Scrivere query](write-queries.md)
