---
title: Eseguire query sui dati in monitoraggio di Azure con Esplora dati di Azure (anteprima)
description: In questo argomento, eseguire query sui dati in monitoraggio di Azure creando un proxy di Esplora dati di Azure per le query tra prodotti con Application Insights e Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: c7e98c31c0db1db3051ad66df6526dcbddb265c5
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560423"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Eseguire query sui dati in monitoraggio di Azure con Esplora dati di Azure (anteprima)

Il cluster di Azure Esplora dati proxy (proxy ADX) è un'entità che consente di eseguire query tra prodotti tra Esplora dati di Azure, [Application Insights (ai)](/azure/azure-monitor/app/app-insights-overview)e [log Analytics (la)](/azure/azure-monitor/platform/data-platform-logs) nel servizio [monitoraggio di Azure](/azure/azure-monitor/) . È possibile mappare le aree di lavoro Log Analytics di monitoraggio di Azure o le app Application Insights come cluster proxy. È quindi possibile eseguire una query sul cluster proxy usando gli strumenti Esplora dati di Azure e farvi riferimento in una query tra cluster. Questo articolo illustra come connettersi a un cluster proxy, aggiungere un cluster proxy ad Azure Esplora dati interfaccia utente Web ed eseguire query sulle aree di lavoro di intelligenza artificiale o della Esplora dati di Azure.

Il flusso del proxy del Esplora dati di Azure: 

![Flusso proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Prerequisites

> [!NOTE]
> Il proxy ADX è in modalità di anteprima. [Connettersi al proxy](#connect-to-the-proxy) per abilitare la funzionalità proxy ADX per i cluster. Contattare il team di [ADXProxy](mailto:adxproxy@microsoft.com) per qualsiasi domanda.

## <a name="connect-to-the-proxy"></a>Connettersi al proxy

1. Verificare che il cluster nativo di Azure Esplora dati, ad esempio cluster della *Guida* , sia visualizzato nel menu a sinistra prima di connettersi al cluster Log Analytics o Application Insights.

    ![Cluster nativo ADX](media/adx-proxy/web-ui-help-cluster.png)

1. Nell'interfaccia utente di Azure Esplora dati (https://dataexplorer.azure.com/clusters)selezionare **Aggiungi cluster**.

1. Nella finestra **Aggiungi cluster** aggiungere l'URL al cluster la o di intelligenza artificiale. 
    
    * Per LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Per intelligenza artificiale: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Selezionare **Aggiungi**.

    ![Aggiungere cluster](media/adx-proxy/add-cluster.png)

    Se si aggiunge una connessione a più di un cluster proxy, assegnare a ognuno un nome diverso. In caso contrario, avranno lo stesso nome nel riquadro sinistro.

1. Una volta stabilita la connessione, il cluster di LA o di intelligenza artificiale verrà visualizzato nel riquadro sinistro con il cluster ADX nativo. 

    ![Log Analytics e cluster di Azure Esplora dati](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Eseguire le query

È possibile eseguire le query usando gli strumenti client che supportano le query kusto, ad esempio: kusto Explorer, interfaccia utente Web di ADXri, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, API REST.

> [!TIP]
> * Il nome del database deve avere lo stesso nome della risorsa specificata nel cluster proxy. I nomi fanno distinzione tra maiuscole e minuscole.
> * In query tra cluster assicurarsi che la denominazione delle app Application Insights e delle aree di lavoro Log Analytics sia corretta.
>     * Se i nomi contengono caratteri speciali, questi vengono sostituiti dalla codifica URL nel nome del cluster proxy. 
>     * Se i nomi includono caratteri che non soddisfano [le regole del nome dell'identificatore KQL](/azure/kusto/query/schema-entities/entity-names), verranno sostituiti dal trattino **-** carattere.

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Eseguire LA query diretta dal cluster del proxy di ADX o di intelligenza artificiale

Eseguire query sul cluster LA o per intelligenza artificiale. Verificare che il cluster sia selezionato nel riquadro sinistro. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Area di lavoro di query](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Query incrociata del cluster proxy di ADX o di intelligenza artificiale e del cluster nativo ADX 

Quando si eseguono query tra cluster dal proxy, verificare che il cluster nativo di ADX sia selezionato nel riquadro sinistro. Negli esempi seguenti viene illustrata la combinazione di tabelle del cluster ADX (usando `union`) con l'area di lavoro.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![Cross query dal proxy di Esplora dati di Azure](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

L'uso dell' [operatore`join`](/azure/kusto/query/joinoperator), anziché di Union, potrebbe richiedere un [`hint`](/azure/kusto/query/joinoperator#join-hints) per eseguirlo in un cluster nativo di Esplora dati Azure (e non nel proxy). 

## <a name="additional-syntax-examples"></a>Esempi di sintassi aggiuntivi

Quando si chiamano i cluster Application Insights (AI) o Log Analytics (LA) sono disponibili le opzioni di sintassi seguenti:

|Descrizione della sintassi  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Database all'interno di un cluster che contiene solo la risorsa definita in questa sottoscrizione (**scelta consigliata per le query tra cluster**) |   cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster che contiene tutte le app e le aree di lavoro in questa sottoscrizione    |     cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster che contiene tutte le app e le aree di lavoro nella sottoscrizione e che sono membri di questo gruppo di risorse    |   cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster che contiene solo la risorsa definita in questa sottoscrizione      |    cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Passaggi successivi

[Scrivere query](write-queries.md)
