---
title: Log di diagnostica di Hub eventi in Azure | Microsoft Docs
description: Informazioni su come configurare log di diagnostica per gli hub eventi in Azure.
keywords: 
documentationcenter: 
services: event-hubs
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/27/2017
ms.author: sethm;babanisa
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 11f7b111df8b426c00334bebd82d5877817419d8
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---
# <a name="event-hubs-diagnostic-logs"></a>Log di diagnostica di Hub eventi

È possibile visualizzare due tipi di log per Hub eventi di Azure:
* **[Log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**. Questi log contengono informazioni sulle operazioni eseguite in un processo. I log sono sempre attivati.
* **[Log di diagnostica](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**. È possibile configurare i log di diagnostica per una visualizzazione più completa su tutto ciò che accade in un processo. I log di diagnostica coprono le attività che si verificano dal momento della creazione del processo fino alla sua eliminazione, inclusi gli aggiornamenti e le attività che si verificano durante l'esecuzione del processo.

## <a name="turn-on-diagnostic-logs"></a>Attivare i log di diagnostica
I log di diagnostica sono disabilitati per impostazione predefinita. Per abilitare i log di diagnostica:

1.  Nel Portale di Azure passare al pannello Processo di streaming.

2.  In **Monitoraggio** andare al pannello **Log di diagnostica**.

    ![Navigazione tra i pannelli per trovare i log di diagnostica](./media/event-hubs-diagnostic-logs/image1.png)  

3.  Fare clic su **Attiva diagnostica**.

    ![Attivare i log di diagnostica](./media/event-hubs-diagnostic-logs/image2.png)

4.  Per **Stato** fare clic su **Attivato**.

    ![Modifica dello lo stato dei log di diagnostica](./media/event-hubs-diagnostic-logs/image3.png)

5.  Impostare la destinazione di archiviazione desiderata, ad esempio un account di archiviazione, un hub eventi o Azure Log Analytics.

6.  Selezionare le categorie di log da raccogliere, ad esempio **Esecuzione** o **Creazione e modifica**.

7.  Salvare le nuove impostazioni di diagnostica.

Le nuove impostazioni diventano effettive in circa 10 minuti. Trascorso questo tempo, i log vengono visualizzati nella destinazione di archiviazione configurata, all'interno del pannello **Log di diagnostica**.

Per altre informazioni sulla configurazione della diagnostica, vedere la [panoramica dei log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-categories"></a>Categorie dei log di diagnostica
Hub eventi consente di acquisire i log di diagnostica per due categorie:

* **ArchivalLogs**: log correlati agli archivi dell'hub eventi, in particolare quelli relativi agli errori di archiviazione.
* **OperationalLogs**: acquisisce informazioni su ciò che avviene durante il funzionamento dell'hub eventi, in particolare il tipo di operazione, come creazione dell'hub eventi, risorse usate e stato dell'operazione.

## <a name="diagnostic-logs-schema"></a>Schema dei log di diagnostica
Tutti i log vengono archiviati in formato JavaScript Object Notation (JSON). Ogni voce presenta campi stringa nel formato descritto negli esempi seguenti.

### <a name="archive-logs-schema"></a>Schema dei log di archiviazione

Le stringhe JSON dei log di archiviazione includono gli elementi elencati nella tabella seguente:

Nome | Descrizione
------- | -------
TaskName | Descrizione dell'attività non riuscita.
ActivityId | ID interno, usato a scopo di rilevamento.
trackingId | ID interno, usato a scopo di rilevamento.
resourceId | ID della risorsa di Azure Resource Manager.
eventHub | Nome completo dell'hub eventi, include il nome dello spazio dei nomi.
partitionId | Partizione dell'hub eventi per l'operazione di scrittura.
archiveStep | ArchiveFlushWriter
startTime | Ora di inizio di un errore.
errori | Numero di volte in cui si è verificato un errore.
durationInSeconds | Durata dell'errore.
Message | Messaggio di errore.
category | ArchiveLogs

Di seguito è riportato un esempio di stringa JSON di log di archiviazione:

```json
{
     "TaskName": "EventHubArchiveUserError",
     "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
     "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
     "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
     "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
     "partitionId": "1",
     "archiveStep": "ArchiveFlushWriter",
     "startTime": "9/22/2016 5:11:21 AM",
     "failures": 3,
     "durationInSeconds": 360,
     "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
     "category": "ArchiveLogs"
}
```

### <a name="operation-logs-schema"></a>Schema dei log operazioni

Le stringhe JSON dei log operazioni includono gli elementi elencati nella tabella seguente:

Nome | Descrizione
------- | -------
ActivityId | ID interno, usato a scopo di rilevamento.
EventName | Nome operazione.  
resourceId | ID della risorsa di Azure Resource Manager.
SubscriptionId | l'ID sottoscrizione.
EventTimeString | Durata dell'operazione.
EventProperties | Proprietà dell'operazione.
Status | Stato dell'operazione.
Chiamante | Chiamante dell'operazione (Portale di Azure o client di gestione).
category | OperationalLogs

Di seguito è riportato un esempio di stringa JSON di log dell'operazione:

```json
Example:
{
     "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
     "EventName": "Create EventHub",
     "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
     "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
     "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
     "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
     "Status": "Succeeded",
     "Caller": "ServiceBus Client",
     "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione a Hub eventi](event-hubs-what-is-event-hubs.md)
* [Panoramica dell'API di Hub eventi](event-hubs-api-overview.md)
* [Introduzione all'Hub eventi](event-hubs-csharp-ephcs-getstarted.md)

