---
title: Log di diagnostica di Hub eventi in Azure | Microsoft Docs
description: Informazioni su come analizzare i log di diagnostica da Hub eventi in Microsoft Azure.
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
ms.date: 02/01/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 4d7d0e1f5ffb395bf91bbdac1ab4b9ec3f9dee1c
ms.openlocfilehash: cb8c7930ee423543c91366de64220ee55609a4cf


---
# <a name="event-hub-diagnostic-logs"></a>Log di diagnostica di Hub eventi

## <a name="introduction"></a>Introduzione
Hub eventi restituisce due tipi di log: 
* [Log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), che sono sempre abilitati e offrono informazioni approfondite sulle operazioni eseguite sui processi.
* [Log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), che sono configurabili dall'utente e offrono informazioni più approfondite su ciò che accade durante il processo, a partire da quando viene creato e aggiornato, mentre viene eseguito, fino a quando viene eliminato.

## <a name="how-to-enable-diagnostic-logs"></a>Come abilitare i log di diagnostica
I log di diagnostica sono **disattivati** per impostazione predefinita. Per abilitarli, seguire questa procedura:

Accedere al Portale di Azure, passare al pannello del processo di streaming e usare il pannello "Log di diagnostica" in "Monitoraggio".

![navigazione al pannello dei log di diagnostica](./media/event-hubs-diagnostic-logs/image1.png)  

Fare quindi clic sul collegamento "Attiva diagnostica"

![attivazione dei log di diagnostica](./media/event-hubs-diagnostic-logs/image2.png)

Nella diagnostica aperta modificare lo stato su "On" (Attivo).

![modifica dello stato dei log di diagnostica](./media/event-hubs-diagnostic-logs/image3.png)

Configurare la destinazione di archiviazione desiderata (account di archiviazione, hub eventi, Log Analytics) e selezionare le categorie di log che si desidera raccogliere (esecuzione, creazione). Salvare quindi la nuova configurazione di diagnostica.

Dopo essere stata salvata, la configurazione richiederà circa 10 minuti per risultare attiva, quindi i log inizieranno a essere visualizzati nella destinazione di archiviazione configurata, visualizzabile nel pannello "Log di diagnostica":

Altre informazioni sulla configurazione sono disponibili nella pagina relativa ai [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostic-logs-categories"></a>Categorie dei log di diagnostica
Esistono due categorie di log di diagnostica attualmente acquisiti:

* **ArchivalLogs:** acquisisce i log correlati all'archivio Hub eventi, in particolare quelli relativi agli errori di archiviazione.
* **OperationalLogs:** acquisisce ciò che avviene durante l'operazione Hub eventi, in particolare i tipi di operazione come creazione dell'Hub eventi, risorse usate e stato dell'operazione.

## <a name="diagnostic-logs-schema"></a>Schema dei log di diagnostica
Tutti i log vengono archiviati in formato JSON e per ogni voce sono presenti i campi stringa in base al seguente formato:


### <a name="archive-error-schema"></a>Schema degli errori di archiviazione
Nome | Descrizione
------- | -------
TaskName | Descrizione dell'attività non riuscita
ActivityId | ID interno per scopi di rilevamento
trackingId | ID interno per scopi di rilevamento
resourceId | ID risorsa ARM
eventHub | Nome completo dell'Hub eventi (include il nome spazio dei nomi)
partitionId | La partizione scritta all'interno dell'Hub eventi
archiveStep | ArchiveFlushWriter
startTime | Ora di inizio di un errore
errori | Numero di volte in cui si è verificato un errore
durationInSeconds | Durata dell'errore
Message | Messaggio di errore
category | ArchiveLogs

#### <a name="example-archive-log"></a>Log di archiviazione di esempio

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
Nome | Descrizione
------- | -------
ActivityId | ID interno per scopi di rilevamento
EventName | Nome operazione           
resourceId | ID risorsa ARM
SubscriptionId | ID sottoscrizione
EventTimeString | Durata dell'operazione
EventProperties | Proprietà dell'operazione
Stato | Stato dell'operazione
Chiamante | Chiamante dell'operazione (portale o client di gestione)
category | OperationalLogs

#### <a name="example-operation-log"></a>Log di operazioni di esempio

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


<!--HONumber=Feb17_HO1-->


