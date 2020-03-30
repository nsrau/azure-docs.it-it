---
title: Raccogliere il log attività di Azure nell'area di lavoro di Log AnalyticsCollect Azure activity log in Log Analytics workspace
description: Raccogliere il log attività di Azure nei log di monitoraggio di Azure e usare la soluzione di monitoraggio per analizzare e cercare il log attività di Azure in tutte le sottoscrizioni di Azure.Collect the Azure Activity Log in Azure Monitor Logs and use the monitoring solution to analyze and search the Azure activity log across all your Azure subscriptions.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 407bff10e2480c5210d3057bcccd6c60e591c165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055315"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Raccogliere e analizzare i log delle attività di Azure nell'area di lavoro Log Analytics in Monitoraggio di AzureCollect and analyze Azure activity logs in Log Analytics workspace in Azure Monitor

> [!WARNING]
> È ora possibile raccogliere il log attività in un'area di lavoro di Log Analytics usando un'impostazione di diagnostica simile alla modalità di raccolta dei log delle risorse. Vedere Raccogliere e analizzare i log delle attività di [Azure nell'area di lavoro Log Analytics in Monitoraggio di Azure.See Collect and analyze Azure activity logs in Log Analytics workspace in Azure Monitor](diagnostic-settings-legacy.md).

Il [log attività di Azure](platform-logs-overview.md) fornisce informazioni dettagliate sugli eventi a livello di sottoscrizione che si sono verificati nella sottoscrizione di Azure.The Azure Activity Log provides insight into subscription-level events that have occurred in your Azure subscription. In questo articolo viene descritto come raccogliere il log attività in un'area di lavoro di Log Analytics e come utilizzare la soluzione di [monitoraggio](../insights/solutions.md)di Activity Log Analytics , che fornisce query di log e visualizzazioni per l'analisi di questi dati. 

La connessione del log attività a un'area di lavoro di Log Analytics offre i vantaggi seguenti:Connecting the Activity Log to a Log Analytics workspace provides the following benefits:

- Consolidare il log attività da più sottoscrizioni di Azure in un'unica posizione per l'analisi.
- Memorizzare le voci del log attività per più di 90 giorni.
- Correlare i dati del log attività con altri dati di monitoraggio raccolti da Monitoraggio di Azure.Correlate Activity Log data with other monitoring data collected by Azure Monitor.
- Usare [le query di log](../log-query/log-query-overview.md) per eseguire analisi complesse e ottenere informazioni approfondite sulle voci del log attività.

## <a name="connect-to-log-analytics-workspace"></a>Connettersi all'area di lavoro di Log AnalyticsConnect to Log Analytics workspace
È possibile connettere una singola area di lavoro al log attività per più sottoscrizioni nello stesso tenant di Azure.A single workspace can be connected to the Activity Log for multiple subscriptions in the same Azure tenant. Per la raccolta tra più tenant, vedere Raccogliere log attività di [Azure in un'area](activity-log-collect-tenants.md)di lavoro di Log Analytics tra sottoscrizioni in tenant di Azure Active Directory diversi.

> [!IMPORTANT]
> È possibile che venga visualizzato un errore con la procedura seguente se i provider di risorse Microsoft.OperationalInsights e Microsoft.OperationsManagement non sono registrati per la sottoscrizione. Per registrare questi provider, vedere [Tipi e provider di risorse di Azure.See Azure resource providers](../../azure-resource-manager/management/resource-providers-and-types.md) and types to register these providers.

Utilizzare la procedura seguente per connettere il log attività all'area di lavoro di Log Analytics:Use the following procedure to connect the Activity Log to your Log Analytics workspace:

1. Dal menu **Aree di lavoro** di Log Analytics nel portale di Azure selezionare l'area di lavoro per raccogliere il log attività.
1. Nella sezione **Origini dati area di lavoro** del menu dell'area di lavoro selezionare Log attività di **Azure.**
1. Fare clic sull'abbonamento da connettere.

    ![Aree di lavoro](media/activity-log-export/workspaces.png)

1. Fare clic su **Connetti** per connettere il log attività nella sottoscrizione all'area di lavoro selezionata. Se la sottoscrizione è già connessa a un'altra area di lavoro, fare clic su **Disconnetti** prima per disconnetterla.

    ![Connetti aree di lavoro](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analizzare nell'area di lavoro di Log AnalyticsAnalyze in Log Analytics workspace
Quando si connette un log attività a un'area di lavoro log Analytics, le voci verranno scritte nell'area di lavoro in una tabella denominata **AzureActivity** che è possibile recuperare con una [query di log.](../log-query/log-query-overview.md) La struttura di questa tabella varia a seconda della [categoria di voce di registro.](activity-log-view.md#categories-in-the-activity-log) Vedere Schema di [eventi del log attività](activity-log-schema.md) di Azure per una descrizione di ogni categoria.

## <a name="activity-logs-analytics-monitoring-solution"></a>Soluzione di monitoraggio di Activity Logs Analytics
La soluzione di monitoraggio di Azure Log Analytics include più query di log e visualizzazioni per l'analisi dei record del log attività nell'area di lavoro di Log Analytics.The Azure Log Analytics monitoring solution includes multiple log queries and views for analyzing the Activity Log records in your Log Analytics workspace.

### <a name="install-the-solution"></a>Installare la soluzione
Utilizzare la procedura descritta in Installare una soluzione di monitoraggio per installare la soluzione **Activity Log Analytics.Use** the procedure in Install a [monitoring solution](../insights/solutions.md#install-a-monitoring-solution) to install the Activity Log Analytics solution. Non è necessaria alcuna configurazione aggiuntiva.

### <a name="use-the-solution"></a>Usare la soluzione
Le soluzioni di monitoraggio sono accessibili dal menu Monitoraggio nel portale di Azure.Monitoring solutions are accessed from the **Monitor** menu in the Azure portal. Selezionare **Altro** nella sezione **Insights** per aprire la pagina **Panoramica** con i riquadri della soluzione. Nel riquadro **Log attività** di Azure viene visualizzato un conteggio del numero di record **AzureActivity** nell'area di lavoro.

![Riquadro Log attività di Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Fare clic sul riquadro Log attività di Azure per aprire la visualizzazione Log attività di **Azure.Click** the **Azure Activity Logs** tile to open the Azure Activity Logs view. La visualizzazione include le parti di visualizzazione nella tabella seguente. Ogni parte elenca fino a 10 elementi corrispondenti ai criteri di tale parte per l'intervallo di tempo specificato. È possibile eseguire una query di log che restituisce tutti i record corrispondenti facendo clic su **Visualizza tutto** nella parte inferiore della parte.

![Dashboard Log attività di Azure](media/collect-activity-logs/activity-log-dash.png)

| Parte di visualizzazione | Descrizione |
| --- | --- |
| Voci del Log attività di Azure | Mostra un grafico a barre dei primi totali dei record di registrazione del log attività di Azure per l'intervallo di date selezionato e un elenco dei primi 10 chiamanti dell'attività. Fare clic sul grafico a barre per eseguire una ricerca di log per `AzureActivity`. Fare clic su un elemento chiamante per eseguire una ricerca nel log che restituisca tutte le voci del Log attività per tale elemento. |
| Log attività per stato | Mostra un grafico ad anello per lo stato del log attività di Azure per l'intervallo di date selezionato e un elenco dei primi dieci record di stato. Fare clic sul grafico per `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`eseguire una query di log per . Fare clic su un elemento di stato per eseguire una ricerca nei log che restituisca tutte le voci del registro attività per tale record di stato. |
| Log attività per risorsa | Visualizza il numero totale di risorse con registri attività ed elenca le prime dieci risorse con i conteggi dei record per ogni risorsa. Fare clic sull'area totale per eseguire una ricerca di log per `AzureActivity | summarize AggregatedValue = count() by Resource`, che mostra tutte le risorse di Azure disponibili per la soluzione. Fare clic su una risorsa per eseguire una query di log che restituisca tutti i record di attività per tale risorsa. |
| Log attività per provider di risorse | Visualizza il numero totale di provider di risorse che producono i registri attività ed elenca i primi dieci. Fare clic sull'area totale `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`per eseguire una query di log per , che mostra tutti i provider di risorse di Azure.Click the total area to run a log query for , which shows all Azure resource providers. Fare clic su un provider di risorse per eseguire una query di log che restituisca tutti i record di attività per il provider. |

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sul [Registro attività](platform-logs-overview.md).
- Altre informazioni sulla piattaforma dati di Monitoraggio di Azure .Learn more about the [Azure Monitor data platform](data-platform.md).
- Utilizzare [le query di log](../log-query/log-query-overview.md) per visualizzare informazioni dettagliate dal log attività.
