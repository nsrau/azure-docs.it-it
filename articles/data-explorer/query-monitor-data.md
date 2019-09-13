---
title: Eseguire query sui dati in monitoraggio di Azure con Esplora dati di Azure (anteprima)
description: In questo argomento, eseguire query sui dati in monitoraggio di Azure creando un proxy di Esplora dati di Azure per le query tra prodotti con Application Insights e Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 43d91bff6b8b67e79a9549c1524f918166c9adc4
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934005"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Eseguire query sui dati in monitoraggio di Azure con Esplora dati di Azure (anteprima)

Il cluster di Azure Esplora dati proxy (proxy ADX) è un'entità che consente di eseguire query tra prodotti tra Esplora dati di Azure, [Application Insights (ai)](/azure/azure-monitor/app/app-insights-overview)e [log Analytics (la)](/azure/azure-monitor/platform/data-platform-logs) nel servizio [monitoraggio di Azure](/azure/azure-monitor/) . È possibile eseguire il mapping delle aree di lavoro Log Analytics di monitoraggio di Azure o delle app Application Insights come cluster proxy. È quindi possibile eseguire una query sul cluster proxy usando gli strumenti Esplora dati di Azure e farvi riferimento in una query tra cluster. Questo articolo illustra come connettersi a un cluster proxy, aggiungere un cluster proxy ad Azure Esplora dati interfaccia utente Web ed eseguire query sulle aree di lavoro di intelligenza artificiale o della Esplora dati di Azure.

Il flusso del proxy del Esplora dati di Azure: 

![Flusso proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Prerequisiti

> [!NOTE]
> Il proxy ADX è in modalità di anteprima. Per abilitare questa funzionalità, contattare il team di [ADXProxy](mailto:adxproxy@microsoft.com) .

## <a name="connect-to-the-proxy"></a>Connettersi al proxy

1. Verificare che il cluster nativo di Azure Esplora dati, ad esempio cluster della *Guida* , sia visualizzato nel menu a sinistra prima di connettersi al cluster Log Analytics o Application Insights.

    ![Cluster nativo ADX](media/adx-proxy/web-ui-help-cluster.png)

1. Nell'interfaccia utente https://dataexplorer.azure.com/clusters) di Esplora dati di Azure, selezionare **Aggiungi cluster**.

1. Nella finestra **Aggiungi cluster** :

    * Aggiungere l'URL al cluster LA o di intelligenza artificiale. Ad esempio: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Selezionare **Aggiungi**.

    ![Aggiungere cluster](media/adx-proxy/add-cluster.png)

    Se si aggiunge una connessione a più di un cluster proxy, assegnare a ognuno un nome diverso. In caso contrario, avranno lo stesso nome nel riquadro sinistro.

1. Una volta stabilita la connessione, il cluster di LA o di intelligenza artificiale verrà visualizzato nel riquadro sinistro con il cluster ADX nativo. 

    ![Log Analytics e cluster di Azure Esplora dati](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Eseguire le query

Per eseguire query sui cluster proxy, è possibile usare kusto Explorer, ADX Web Explorer, Jupyter Kqlmagic o l'API REST. 

> [!TIP]
> * Il nome del database deve avere lo stesso nome della risorsa specificata nel cluster proxy. I nomi fanno distinzione tra maiuscole e minuscole.
> * In query tra cluster assicurarsi che la denominazione delle app Application Insights e delle aree di lavoro Log Analytics sia corretta.
>     * Se i nomi contengono caratteri speciali, questi vengono sostituiti dalla codifica URL nel nome del cluster proxy. 
>     * Se i nomi includono caratteri che non soddisfano [le regole del nome dell'identificatore KQL](/azure/kusto/query/schema-entities/entity-names), vengono **-** sostituiti dal carattere di tratteggio.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Eseguire query sul cluster nativo di Azure Esplora dati 

Eseguire query sul cluster di Esplora dati di Azure, ad esempio la tabella *StormEvents* nel cluster della *Guida* . Quando si esegue la query, verificare che nel riquadro sinistro sia selezionato il cluster nativo di Azure Esplora dati.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Tabella StormEvents query](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Eseguire una query sul cluster LA o di intelligenza artificiale

Quando si eseguono query sul cluster LA o AL, verificare che nel riquadro sinistro sia selezionato il cluster LA o l'intelligenza artificiale. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Area di lavoro di query](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Eseguire una query sul cluster di i o AI dal proxy ADX  

Quando si eseguono query sul cluster LA o al dal proxy, verificare che il cluster nativo di ADX sia selezionato nel riquadro sinistro. Nell'esempio seguente viene illustrata una query dell'area di lavoro LA usando il cluster ADX nativo

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Eseguire query da Azure Esplora dati proxy](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Query incrociata del cluster LA o di intelligenza artificiale e del cluster ADX dal proxy ADX 

Quando si eseguono query tra cluster dal proxy, verificare che il cluster nativo di ADX sia selezionato nel riquadro sinistro. Negli esempi seguenti viene illustrata la combinazione di tabelle `union`del cluster ADX (tramite) con l'area di lavoro.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Cross query dal proxy di Esplora dati di Azure](media/adx-proxy/cross-query-adx-proxy.png)

L'uso dell' [ `join` operatore](/azure/kusto/query/joinoperator), anziché Union, può richiedere un [`hint`](/azure/kusto/query/joinoperator#join-hints) per eseguirlo in un cluster nativo di Azure Esplora dati (e non nel proxy). 

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
