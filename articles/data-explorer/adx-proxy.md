---
title: Eseguire query sui dati in Monitoraggio di Azure usando Esplora dati di Azure (anteprima)
description: In questo argomento, eseguire query sui dati in Monitoraggio di Azure tramite la creazione di un proxy di Esplora dati di Azure per le query di prodotto incrociato con Application Insights e Log Analitica
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f363e59e6faa6b115eb40a2a5d35432f02299d52
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800211"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Eseguire query sui dati in Monitoraggio di Azure usando Esplora dati di Azure (anteprima)

Il cluster di proxy di Esplora dati di Azure (Proxy ADX) è un'entità che è possibile eseguire query di prodotto incrociato tra Esplora dati di Azure, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview), e [Log Analitica (LA)](/azure/azure-monitor/platform/data-platform-logs) di [Monitoraggio di azure](/azure/azure-monitor/) servizio. È possibile mappare le aree di lavoro di Analitica Log monitoraggio di Azure o le app di Application Insights come un cluster di proxy. È quindi possibile eseguire una query del cluster di proxy usando gli strumenti di Esplora dati di Azure e farvi riferimento in una query tra cluster. L'articolo illustra come connettersi a un cluster di proxy, aggiungere un cluster di proxy all'interfaccia utente Web di Esplora dati di Azure ed eseguire query per le app di intelligenza artificiale o LA le aree di lavoro da Esplora dati di Azure.

Il flusso di proxy di Esplora dati di Azure: 

![Flusso proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Prerequisiti

> [!NOTE]
> Il Proxy ADX è in modalità di anteprima. Per abilitare questa funzionalità, contattare il [ADXProxy](mailto:adxproxy@microsoft.com) team.

## <a name="connect-to-the-proxy"></a>Connettersi al proxy

1. Verificare il cluster nativo con Esplora dati di Azure (ad esempio *aiutare* cluster) viene visualizzato nel menu a sinistra prima di connettersi al cluster di Application Insights o Log Analitica.

    ![Cluster native ADX](media/adx-proxy/web-ui-help-cluster.png)

1. Nell'interfaccia utente di Esplora dati di Azure (https://dataexplorer.azure.com/clusters), selezionare **Cluster aggiungere**.

1. Nel **Cluster aggiungere** finestra:

    * Aggiungere l'URL al cluster LA o intelligenza artificiale. Ad esempio: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Selezionare **Aggiungi**.

    ![Aggiungere cluster](media/adx-proxy/add-cluster.png)

    Se si aggiunge una connessione a più di un cluster di proxy, assegnare ognuno un nome diverso. In caso contrario, tutti hanno lo stesso nome nel riquadro sinistro.

1. Dopo aver stabilita la connessione, il cluster per intelligenza artificiale o LA verrà visualizzato nel riquadro a sinistra con il cluster ADX nativo. 

    ![Cluster log Analitica ed Esplora dati di Azure](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Eseguire le query

È possibile usare Esplora Kusto, web ADX Esplora Jupyter Kqlmagic o l'API REST per eseguire una query i cluster di proxy. 

> [!TIP]
> * Nome del database deve avere lo stesso nome di risorsa specificata nel cluster di proxy. I nomi sono tra maiuscole e minuscole.
> * Nella query del cluster tra, assicurarsi che il [denominazione delle App e aree di lavoro](#application-insights-app-and-log-analytics-workspace-names) sia corretto.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Eseguire una query rispetto al cluster di Esplora dati di Azure nativo 

Eseguire query sul cluster Esplora dati di Azure (ad esempio *StormEvents* nella tabella *Guida* cluster). Quando si esegue la query, verificare che il cluster di Esplora dati di Azure nativo sia selezionato nel riquadro sinistro.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Una query sulla tabella StormEvents](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Eseguire una query sul cluster LA o intelligenza artificiale

Quando si eseguono query sul cluster LA o tutti, verificare che il cluster per intelligenza artificiale o LA sia selezionato nel riquadro sinistro. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![LA query dell'area di lavoro](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Eseguire query sul cluster per intelligenza artificiale o LA dal proxy ADX  

Quando si eseguono query sul cluster per intelligenza artificiale o LA dal proxy, verificare che il cluster native ADX sia selezionato nel riquadro sinistro. L'esempio seguente illustra una query dell'area di lavoro LA Usa il cluster ADX nativo

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Eseguire una query dal proxy di Esplora dati di Azure](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Cross query LA o AI cluster e cluster ADX dal proxy ADX 

Quando si eseguono query tra cluster dal proxy, verificare che il cluster native ADX sia selezionato nel riquadro sinistro. Gli esempi seguenti illustrano la combinazione di ADX cluster tabelle (utilizzando `union`) con LA dell'area di lavoro.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Avvengono query tra il proxy di Esplora dati di Azure](media/adx-proxy/cross-query-adx-proxy.png)

Usando il [ `join` operatore](/azure/kusto/query/joinoperator), invece di unione, possono richiedere un hint per l'esecuzione in un cluster nativo con Esplora dati di Azure (e non sul proxy). 

## <a name="additional-syntax-examples"></a>Esempi di sintassi aggiuntiva di

Quando si chiama il cluster di Application Insights (AI) o Log Analitica (LA), sono disponibili le opzioni di sintassi seguenti:

|Descrizione della sintassi  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Database interno di un cluster che contiene solo la risorsa definita in questa sottoscrizione (**consigliata per tra cluster query**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster che contiene tutte le app/le aree di lavoro in questa sottoscrizione    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster che contiene tutte le app/le aree di lavoro nella sottoscrizione e sono membri di questo gruppo di risorse    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster che contiene solo la risorsa definita in questa sottoscrizione      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

### <a name="application-insights-app-and-log-analytics-workspace-names"></a>App di Application Insights e i nomi dell'area di lavoro di Log Analitica

* Se i nomi contengono caratteri speciali, vengono sostituiti da URL codifica nel nome del proxy cluster. 
* Se i nomi includono caratteri che non soddisfano [sulle regole di denominazione identificatore KQL](/azure/kusto/query/schema-entities/entity-names), vengono sostituite dal trattino **-** carattere.

## <a name="next-steps"></a>Passaggi successivi

[Scrivere query](write-queries.md)