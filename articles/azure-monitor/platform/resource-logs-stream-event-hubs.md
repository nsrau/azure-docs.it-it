---
title: Trasmettere i log delle risorse di Azure a un hub eventi
description: Informazioni su come trasmettere i log delle risorse di Azure a un hub eventi.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 1d7a533658b6c72caae9649d7e5a9c4fad117245
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262414"
---
# <a name="stream-azure-resource-logs-to-azure-event-hubs"></a>Trasmettere i log delle risorse di Azure a hub eventi di Azure
I [log delle risorse](resource-logs-overview.md) in Azure forniscono dati avanzati e frequenti sul funzionamento interno di una risorsa di Azure. Questo articolo descrive i log delle risorse di streaming a hub eventi per inviare i dati a sistemi esterni, ad esempio SIEM di terze parti e altre soluzioni di log Analytics.


## <a name="what-you-can-do-with-resource-logs-sent-to-an-event-hub"></a>Cosa si può fare con i log delle risorse inviati a un hub eventi
Trasmettere i log delle risorse in Azure a hub eventi per fornire le funzionalità seguenti:

* **Trasmettere log a sistemi di registrazione e telemetria** di terze parti: trasmettere tutti i log delle risorse a un singolo hub eventi per inviare tramite pipe i dati di log a uno strumento Siem o log Analytics di terze parti.
* **Creazione di una piattaforma di registrazione e telemetria personalizzata** : la natura di pubblicazione-sottoscrizione altamente scalabile degli hub eventi consente di inserire in modo flessibile i log delle risorse in una piattaforma teletry personalizzata. Per informazioni dettagliate, vedere [progettazione e dimensionamento di una piattaforma di telemetria su scala globale in hub eventi di Azure](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .

* Consente **di visualizzare l'integrità del servizio tramite lo streaming dei dati in Power bi** : usare hub eventi, analisi di flusso e Power BI per trasformare i dati di diagnostica in informazioni quasi in tempo reale sui servizi di Azure. Vedere [analisi di flusso e Power bi: Dashboard di analisi in tempo reale per i dati](../../stream-analytics/stream-analytics-power-bi-dashboard.md) di streaming per informazioni dettagliate su questa soluzione.

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
Se non si dispone già di un hub eventi, è necessario [crearne](../../event-hubs/event-hubs-create.md) uno. Se in precedenza sono stati trasmessi i log delle risorse a questo spazio dei nomi di hub eventi, l'hub eventi verrà riusato.

Il criterio di accesso condiviso per lo spazio dei nomi definisce le autorizzazioni di cui dispone il meccanismo di streaming. Per lo streaming a hub eventi sono necessarie autorizzazioni di gestione, invio e ascolto. È possibile creare o modificare i criteri di accesso condiviso nella portale di Azure nella scheda Configura per lo spazio dei nomi di hub eventi.

Per aggiornare l'impostazione di diagnostica in modo da includere il flusso, è necessario avere l'autorizzazione ListKey per la regola di autorizzazione di hub eventi. Lo spazio dei nomi di Hub eventi non deve necessariamente trovarsi nella stessa sottoscrizione in cui vengono creati i log, purché l'utente che configura l'impostazione abbia l'accesso RBAC appropriato a entrambe le sottoscrizioni e queste ultime si trovino entrambe nello stesso tenant AAD.

## <a name="create-a-diagnostic-setting"></a>Creare un'impostazione di diagnostica
I log delle risorse non vengono raccolti per impostazione predefinita. Inviarli a un hub eventi e ad altre destinazioni creando un'impostazione di diagnostica per una risorsa di Azure. Per informazioni dettagliate, vedere [creare un'impostazione di diagnostica per raccogliere log e metriche in Azure](diagnostic-settings.md) .

## <a name="stream-data-from-compute-resources"></a>Trasmettere dati da risorse di calcolo
Il processo in questo articolo è per le risorse non di calcolo, come descritto in [Panoramica dei log delle risorse di Azure](diagnostic-settings.md).
Trasmettere i log delle risorse dalle risorse di calcolo di Azure usando l'agente di Diagnostica di Azure di Windows. Per informazioni dettagliate, vedere [trasmettere i dati diagnostica di Azure nel percorso critico tramite hub eventi](diagnostics-extension-stream-event-hubs.md) .


## <a name="consuming-log-data-from-event-hubs"></a>Utilizzo dei dati di log da Hub eventi
Quando si utilizzano i log delle risorse di hub eventi, sarà il formato JSON con gli elementi nella tabella seguente.

| Nome dell'elemento | Descrizione |
| --- | --- |
| records |Matrice di tutti gli eventi di log nel payload. |
| time |Ora in cui si è verificato l'evento. |
| category |Categoria di log per l'evento. |
| resourceId |ID risorsa della risorsa che ha generato l'evento. |
| operationName |Nome dell'operazione. |
| livello |facoltativo. Indica il livello dell'evento di log. |
| proprietà |Proprietà dell'evento. Questi possono variare per ogni servizio di Azure, come descritto in [](). |


Di seguito sono riportati i dati di output di esempio di hub eventi:

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

* [Trasmettere i log Azure Active Directory con monitoraggio di Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Scopri di più sui log delle risorse di Azure](resource-logs-overview.md).
* [Introduzione a hub eventi](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

