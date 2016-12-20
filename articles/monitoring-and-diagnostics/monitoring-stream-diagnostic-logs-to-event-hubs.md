---
title: Trasmettere log di diagnostica di Azure a Hub eventi | Microsoft Docs
description: Informazioni su come trasmettere log di diagnostica di Azure a Hub eventi.
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 42bc4845-c564-4568-b72d-0614591ebd80
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4ff5fb57cba6dea1bee9d2e2d25f6fcf8354ce79


---
# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>Trasmettere log di diagnostica di Azure a Hub eventi
I **[log di diagnostica di Azure](monitoring-overview-of-diagnostic-logs.md)** possono essere trasmessi quasi in tempo reale a qualsiasi applicazione con l'opzione "Esporta in hub eventi" incorporata nel portale oppure abilitando l'ID regola del bus di servizio in un'impostazione di diagnostica tramite i cmdlet di Azure PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Che cosa si può fare con i log di diagnostica e Hub eventi
Ecco alcuni esempi di come è possibile usare la funzionalità di trasmissione per i log di diagnostica:

* **Trasmettere log a sistemi di telemetria e registrazione di terze parti** : in futuro, la funzionalità di trasmissione di Hub eventi diventerà il meccanismo di invio dei log di diagnostica a soluzioni di analisi di log e SIEM di terze parti.
* **Visualizzare lo stato di integrità del servizio mediante la trasmissione di dati sul "percorso critico" a Power BI** : Hub eventi, analisi di flusso e Power BI permettono di trasformare facilmente i dati di diagnostica in informazioni quasi in tempo reale sui servizi di Azure. [Questo articolo della documentazione offre un'utile panoramica della configurazione di Hub eventi, dell'elaborazione di dati con analisi di flusso e dell'uso di Power BI come output](../stream-analytics/stream-analytics-power-bi-dashboard.md). Ecco alcuni suggerimenti per la configurazione dei log di diagnostica:
  
  * Gli hub eventi per una categoria di log di diagnostica vengono creati automaticamente quando si seleziona l'opzione nel portale o si abilita tramite PowerShell. Nello spazio dei nomi del bus di servizio è quindi consigliabile selezionare gli hub eventi il cui nome inizia con "insights-".
  * Ecco una query di esempio di analisi di flusso che è possibile usare per analizzare tutti i dati di log in una tabella di Power BI:

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

* **Compilare una piattaforma di registrazione e telemetria personalizzata** : se è disponibile una piattaforma di telemetria personalizzata o si intende crearne una, le caratteristiche di pubblicazione-sottoscrizione altamente scalabili di Hub eventi offrono grande flessibilità per l'inserimento dei log di diagnostica. [Vedere la guida all'uso di Hub eventi in una piattaforma di telemetria su scala globale di Dan Rosanova](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Abilitare la trasmissione dei log di diagnostica
È possibile abilitare la trasmissione dei log di diagnostica a livello di codice, tramite il portale o tramite l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx). In ognuno di questi casi, occorre scegliere uno spazio dei nomi del bus di servizio in cui viene creato un hub eventi per ogni categoria di log che viene abilitata. Una **categoria di log** di diagnostica è un tipo di log che una risorsa può raccogliere. Per selezionare le categorie di log da raccogliere per una determinata risorsa è possibile usare il pannello Diagnostica del portale di Azure.

![Categorie di log nel portale](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [!WARNING]
> Per abilitare la trasmissione dei log di diagnostica da risorse di calcolo, ad esempio le macchine virtuali o Service Fabric, è necessario [seguire una procedura diversa](../event-hubs/event-hubs-streaming-azure-diags-data.md).
> 
> 

### <a name="via-powershell-cmdlets"></a>Tramite i cmdlet di PowerShell
Per abilitare la trasmissione tramite i [cmdlet di Azure PowerShell](insights-powershell-samples.md), è possibile usare il cmdlet `Set-AzureRmDiagnosticSetting` con i parametri seguenti:

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

L'ID regola del bus di servizio è una stringa nel formato seguente: `{service bus resource ID}/authorizationrules/{key name}`. Ad esempio, `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`.

### <a name="via-azure-cli"></a>Tramite l'interfaccia della riga di comando di Azure
Per abilitare la trasmissione tramite l'[interfaccia della riga di comando di Azure](insights-cli-samples.md), è possibile usare il comando `insights diagnostic set` come segue:

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Usare lo stesso formato per l'ID regola del bus di servizio, come illustrato per il cmdlet di PowerShell.

### <a name="via-azure-portal"></a>Tramite il portale di Azure
Per abilitare la trasmissione tramite il portale di Azure, passare alle impostazioni di diagnostica di una risorsa e selezionare "Esporta in Hub eventi".

![Esporta in hub eventi nel portale](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

Per passare alla configurazione, selezionare uno spazio dei nomi del bus di servizio esistente. Se si trasmettono i log di diagnostica per la prima volta, nello spazio dei nomi selezionato viene creato l'hub eventi. Se esistono già risorse che trasmettono tale categoria di log a questo spazio dei nomi, l'hub eventi vi viene trasmesso. I criteri definiscono le autorizzazioni del meccanismo di trasmissione. Al momento, per trasmettere a un hub eventi sono necessarie autorizzazioni di gestione, lettura e invio. Per creare o modificare i criteri di accesso condiviso dello spazio dei nomi del bus di servizio nel portale classico è possibile usare la scheda "Configura" dello spazio dei nomi del bus di servizio. Per aggiornare una di queste impostazioni di diagnostica, il client deve avere l'autorizzazione ListKey per la regola di autorizzazione del bus di servizio.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Come utilizzare i dati di log da Hub eventi
Di seguito è riportato un esempio di dati di output da Hub eventi:

```
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Nome dell'elemento | Description |
| --- | --- |
| records |Matrice di tutti gli eventi di log nel payload. |
| time |Ora in cui si è verificato l'evento. |
| category |Categoria di log per l'evento. |
| resourceId |ID risorsa della risorsa che ha generato l'evento. |
| operationName |Nome dell'operazione. |
| level |Facoltativo. Indica il livello dell'evento di log. |
| properties |Proprietà dell'evento. |

Per un elenco di tutti i provider di risorse che supportano la trasmissione a Hub eventi, vedere [questo articolo](monitoring-overview-of-diagnostic-logs.md).

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sui log di diagnostica di Azure](monitoring-overview-of-diagnostic-logs.md)
* [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)




<!--HONumber=Nov16_HO3-->


