---
title: Trasmettere log della piattaforma di Azure a un hub eventi
description: Informazioni su come trasmettere i log delle risorse di Azure a un hub eventi per inviare dati a sistemi esterni, ad esempio SIEM di terze parti e altre soluzioni di analisi dei log.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658915"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Trasmettere i log della piattaforma Azure agli hub eventi di AzureStream Azure platform logs to Azure Event Hubs
[I log della piattaforma](platform-logs-overview.md) in Azure, inclusi i log attività di Azure e i log delle risorse, forniscono informazioni dettagliate su diagnostica e controllo per le risorse di Azure e la piattaforma di Azure da cui dipendono.  Questo articolo descrive i log della piattaforma di streaming agli hub eventi per inviare dati a sistemi esterni, ad esempio SIEM di terze parti e altre soluzioni di analisi dei log.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>Operazioni che è possibile eseguire con i registri della piattaforma inviati a un hub eventi
Trasmettere i log della piattaforma in Azure agli hub eventi per fornire le funzionalità seguenti:Stream platform logs in Azure to event hubs to provide the following functionality:

* **Eseguire il flusso dei log** nei sistemi di registrazione e telemetria di terze parti: trasmette tutti i log della piattaforma a un singolo hub eventi per reindirizzare i dati di log a uno strumento SIEM o di analisi dei log di terze parti.
  
* Creare una piattaforma di **telemetria e registrazione personalizzata:** la natura altamente scalabile di pubblicazione e sottoscrizione degli hub eventi consente di inserire in modo flessibile i log della piattaforma in una piattaforma di trasmissione personalizzata. Per informazioni dettagliate, vedere Progettazione e ridimensionamento di una piattaforma di [telemetria con scalabilità globale negli hub eventi di Azure.See Designing](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) and Sizing a Global Scale Telemetry Platform on Azure Event Hubs for details.

* **Visualizzare l'integrità del servizio trasmettendo i dati in Power BI:** usare Hub eventi, Analisi di flusso e Power BI per trasformare i dati di diagnostica in informazioni dettagliate quasi in tempo reale nei servizi di Azure.View service health by streaming data to Power BI – Use Event Hubs, Stream Analytics, and Power BI to transform your diagnostics data into near real-time insights on your Azure services. Per informazioni dettagliate su questa soluzione, vedere [Analisi di flusso e Power BI: dashboard](../../stream-analytics/stream-analytics-power-bi-dashboard.md) di analisi in tempo reale per lo streaming dei dati.

    Il codice SQL seguente è una query di esempio di Analisi di flusso che è possibile usare per analizzare tutti i dati di log in una tabella di Power BI:
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>Prerequisiti
È necessario [creare un hub eventi](../../event-hubs/event-hubs-create.md) se non ne è già presente uno. Se si dispone già di un'impostazione di diagnostica che usa questo spazio dei nomi Hub eventi, l'hub eventi verrà riutilizzato.

I criteri di accesso condiviso per lo spazio dei nomi definiscono le autorizzazioni di cui dispone il meccanismo di streaming. Lo streaming in Hub eventi richiede le autorizzazioni di gestione, invio e ascolto. È possibile creare o modificare i criteri di accesso condiviso nel portale di Azure nella scheda Configura per lo spazio dei nomi Hub eventi.

Per aggiornare l'impostazione di diagnostica per includere lo streaming, è necessario disporre dell'autorizzazione ListKey per la regola di autorizzazione Hub eventi. Lo spazio dei nomi di Hub eventi non deve necessariamente trovarsi nella stessa sottoscrizione in cui vengono creati i log, purché l'utente che configura l'impostazione abbia l'accesso RBAC appropriato a entrambe le sottoscrizioni e queste ultime si trovino entrambe nello stesso tenant AAD.

## <a name="create-a-diagnostic-setting"></a>Creare un'impostazione di diagnostica
Inviare i log della piattaforma a un hub eventi e ad altre destinazioni creando un'impostazione di diagnostica per una risorsa di Azure.Send platform logs to an event hub and other destinations by creating a diagnostic setting for an Azure resource. Per informazioni dettagliate, vedere [Creare l'impostazione di diagnostica per raccogliere log e metriche in Azure.See Create diagnostic setting to collect logs and metrics in Azure](diagnostic-settings.md) for details.

## <a name="collect-data-from-compute-resources"></a>Raccogliere dati dalle risorse di calcoloCollect data from compute resources
Le impostazioni di diagnostica raccoglieranno i log delle risorse per le risorse di calcolo di Azure come qualsiasi altra risorsa, ma non per il sistema operativo guest o i carichi di lavoro. Per raccogliere questi dati, installare [l'agente di Log Analytics.](log-analytics-agent.md) 


## <a name="consuming-log-data-from-event-hubs"></a>Utilizzo dei dati di log dagli hub eventiConsuming log data from event hubs
I log della piattaforma dagli hub eventi vengono utilizzati in formato JSON con gli elementi nella tabella seguente.

| Nome dell'elemento | Descrizione |
| --- | --- |
| record |Matrice di tutti gli eventi di log nel payload. |
| time |Ora in cui si è verificato l'evento. |
| category |Categoria di log per l'evento. |
| resourceId |ID risorsa della risorsa che ha generato l'evento. |
| operationName |Nome dell'operazione. |
| level |Facoltativa. Indica il livello dell'evento di log. |
| properties |Proprietà dell'evento. Questi possono variare per ogni servizio di Azure, come descritto in [](). |


Di seguito sono riportati i dati di output di esempio dagli hub eventi per un log di risorse:Following is sample output data from Event Hubs for a resource log:

```json
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



## <a name="next-steps"></a>Passaggi successivi

* [Ulteriori informazioni sui log delle risorse](platform-logs-overview.md).
* [Creare l'impostazione di diagnostica per raccogliere log e metriche in Azure.Create diagnostic setting to collect logs and metrics in Azure](diagnostic-settings.md).
* [Eseguire il flusso dei log di Azure Active Directory con Monitoraggio di Azure.](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Introduzione agli hub eventi](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

