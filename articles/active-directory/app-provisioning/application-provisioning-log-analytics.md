---
title: Informazioni sul modo in cui il provisioning si integra con i log di monitoraggio di Azure in Azure Active Directory.
description: Informazioni sul modo in cui il provisioning si integra con i log di monitoraggio di Azure in Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 167ed7e5c00452db4ee77e10236fec3ff86f0439
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175101"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>Informazioni sul modo in cui il provisioning si integra con i log di monitoraggio di Azure

Il provisioning si integra con i log di monitoraggio di Azure e Log Analytics. Con il monitoraggio di Azure è possibile eseguire operazioni quali la creazione di cartelle di lavoro, note anche come dashboard, l'archiviazione dei log di provisioning per 30 giorni e la creazione di query e avvisi personalizzati. Questo articolo illustra come eseguire l'integrazione dei log di provisioning con i log di monitoraggio di Azure. Per altre informazioni sul funzionamento dei log di provisioning in generale, vedere [log di provisioning](../reports-monitoring/concept-provisioning-logs.md).

## <a name="enabling-provisioning-logs"></a>Abilitazione dei log di provisioning

Si dovrebbe avere già familiarità con il monitoraggio di Azure e Log Analytics. In caso contrario, passare a informazioni su di essi e quindi tornare a informazioni sui log di provisioning delle applicazioni. Per altre informazioni sul monitoraggio di Azure, vedere [Panoramica di monitoraggio di Azure](../../azure-monitor/overview.md). Per altre informazioni sui log di monitoraggio di Azure e Log Analytics, vedere [Panoramica delle query di log in monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md).

Dopo aver configurato il monitoraggio di Azure, è possibile abilitare i log per il provisioning delle applicazioni. L'opzione è disponibile nella pagina **impostazioni di diagnostica** .

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="Accedere alle impostazioni di diagnostica" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="Abilitare i log di provisioning dell'applicazione" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> Se è stato appena eseguito il provisioning di un'area di lavoro, è possibile che sia necessario un po' di tempo prima di inviare i log. Se viene visualizzato un messaggio di errore che indica che la sottoscrizione non è registrata per l'uso di *Microsoft. Insights* , quindi controlla di nuovo dopo alcuni minuti.
 
## <a name="understanding-the-data"></a>Informazioni sui dati
Il flusso di dati sottostante che esegue il provisioning invia i visualizzatori log è quasi identico. I log di monitoraggio di Azure ricevono quasi lo stesso flusso dell'interfaccia utente portale di Azure e dell'API di Azure. Esistono solo alcune **differenze** nei campi del log, come illustrato nella tabella seguente. Per ulteriori informazioni su questi campi, vedere [List provisioningObjectSummary](/graph/api/provisioningobjectsummary-list?preserve-view=true&tabs=http&view=graph-rest-beta).

|Log di Monitoraggio di Azure   |Interfaccia utente del portale di Azure   |API di Azure |
|----------|-----------|------------|
|errorDescription |reason |resultDescription |
|status |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Cartelle di lavoro di Monitoraggio di Azure

Le cartelle di lavoro di monitoraggio di Azure offrono un Canvas flessibile per l'analisi dei dati. Forniscono inoltre la creazione di report visivi avanzati all'interno del portale di Azure. Per altre informazioni, vedere [Cenni preliminari sulle cartelle di lavoro di monitoraggio di Azure](../../azure-monitor/platform/workbooks-overview.md).

Il provisioning dell'applicazione viene fornito con un set di cartelle di lavoro predefinite. È possibile trovarli nella pagina cartelle di lavoro. Per visualizzare i dati, è necessario assicurarsi che tutti i filtri (timeRange, jobID, appName) siano popolati. Sarà anche necessario assicurarsi di avere effettuato il provisioning di un'app, in caso contrario non saranno presenti dati nei log.

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="Cartelle di lavoro di provisioning delle applicazioni" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="Dashboard del provisioning delle applicazioni" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>Query personalizzate

È possibile creare query personalizzate e visualizzare i dati nei dashboard di Azure. Per informazioni, vedere [creare e condividere i dashboard dei dati log Analytics](../../azure-monitor/log-query/get-started-queries.md). Vedere anche [Panoramica delle query di log in monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md).

Di seguito sono riportati alcuni esempi per iniziare a eseguire il provisioning delle applicazioni.

Eseguire una query sui log per un utente a in base al relativo ID nel sistema di origine:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

Riepiloga conteggio per ErrorCode:
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

Riepiloga il numero di eventi al giorno per azione:
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

Accetta eventi 100 e proprietà chiave del progetto:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>Avvisi personalizzati

Monitoraggio di Azure consente di configurare avvisi personalizzati in modo che sia possibile ricevere notifiche sugli eventi principali correlati al provisioning. È ad esempio possibile che si desideri ricevere un avviso relativo a picchi di errori. O forse picchi in Disabilita o Elimina. Un altro esempio in cui si potrebbe desiderare di ricevere un avviso è la mancanza di alcun provisioning, che indica che si è verificato un errore.

Per altre informazioni sugli avvisi, vedere [rispondere agli eventi con gli avvisi di monitoraggio di Azure](../../azure-monitor/learn/tutorial-response.md).

Avvisa in caso di picchi di errori. Sostituire jobID con il jobID per l'applicazione.

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="Avvisa in caso di picchi di errori." lightbox="media/application-provisioning-log-analytics/alert1.png":::

Potrebbe essersi verificato un problema che ha causato l'arresto dell'esecuzione del servizio di provisioning. Usare l'avviso seguente per rilevare se non sono presenti eventi di provisioning durante un determinato intervallo di tempo.

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="Potrebbe essersi verificato un problema che ha causato l'arresto dell'esecuzione del servizio di provisioning." lightbox="media/application-provisioning-log-analytics/alert2.png":::

Avvisa in caso di picchi in Disabilita o Elimina.

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="Avvisa in caso di picchi in Disabilita o Elimina." lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>Contributi della community

Stiamo adottando un approccio open source e basato sulla community per le query e i dashboard del provisioning delle applicazioni. Se è stata compilata una query, un avviso o una cartella di lavoro che si ritiene utile ad altri utenti, assicurarsi di pubblicarla nel [repository GitHub AzureMonitorCommunity](https://github.com/microsoft/AzureMonitorCommunity). Quindi, invia un messaggio di posta elettronica con un collegamento. Verrà esaminato e pubblicato nel servizio in modo che altri utenti possano trarre vantaggio. È possibile scrivere a provisioningfeedback@microsoft.com.

## <a name="next-steps"></a>Passaggi successivi

- [Log Analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Introduzione alle query nei log di Monitoraggio di Azure](../../azure-monitor/log-query/get-started-queries.md)
- [Creare e gestire gruppi di azione nel portale di Azure](../../azure-monitor/platform/action-groups.md)
- [Installare e usare le viste di analisi dei log per Azure Active Directory](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [API dei log di provisioning](/graph/api/resources/provisioningobjectsummary?preserve-view=true&view=graph-rest-beta.md)