---
title: Configurare i log di diagnostica - Hub eventi in Azure | Microsoft Docs
description: Informazioni su come configurare log attività e di diagnostica per gli hub eventi in Azure.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 04/28/2020
ms.author: shvija
ms.openlocfilehash: 3010ee7b996c9d3e96082edeb9447c960da321bd
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509779"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Configurare i log di diagnostica per un hub eventi di Azure

È possibile visualizzare due tipi di log per Hub eventi di Azure:

* **[Log attività](../azure-monitor/platform/platform-logs-overview.md)**: questi log contengono informazioni sulle operazioni eseguite in un processo. I log sono sempre attivati. È possibile visualizzare le voci del log attività selezionando **log attività** nel riquadro sinistro per lo spazio dei nomi dell'hub eventi nel portale di Azure. Ad esempio: "crea o Aggiorna spazio dei nomi", "crea o aggiorna Hub eventi".

    ![Log attività per uno spazio dei nomi di hub eventi](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Log di diagnostica](../azure-monitor/platform/platform-logs-overview.md)**: è possibile configurare i log di diagnostica per una visualizzazione più completa di tutto ciò che accade in un processo. I log di diagnostica coprono le attività che si verificano dal momento della creazione del processo fino alla sua eliminazione, inclusi gli aggiornamenti e le attività che si verificano durante l'esecuzione del processo.

    La sezione seguente illustra come abilitare i log di diagnostica per uno spazio dei nomi di hub eventi.

## <a name="enable-diagnostic-logs"></a>Abilitare i log di diagnostica
I log di diagnostica sono disabilitati per impostazione predefinita. Per abilitare i log di diagnostica, eseguire i passaggi seguenti:

1.  Nella [portale di Azure](https://portal.azure.com)passare allo spazio dei nomi di hub eventi. 
2. Selezionare **impostazioni di diagnostica** in **monitoraggio** nel riquadro a sinistra e quindi selezionare **+ Aggiungi impostazione di diagnostica**. 

    ![Pagina impostazioni di diagnostica-Aggiungi impostazione di diagnostica](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. Nella sezione **Dettagli categoria** selezionare i **tipi di log di diagnostica** che si vuole abilitare. Sono disponibili informazioni dettagliate su queste categorie più avanti in questo articolo. 
5. Nella sezione **Dettagli destinazione** impostare la destinazione dell'archivio (destinazione) desiderata; ad esempio un account di archiviazione, un hub eventi o un'area di lavoro Log Analytics.

    ![Pagina Aggiungi impostazioni di diagnostica](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  Selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni di diagnostica.

    Le nuove impostazioni diventano effettive in circa 10 minuti. Trascorso questo tempo, i log vengono visualizzati nella destinazione di archiviazione configurata, nel riquadro **Log di diagnostica**.

    Per altre informazioni sulla configurazione della diagnostica, vedere la [panoramica dei log di diagnostica di Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Categorie dei log di diagnostica

Hub eventi acquisisce i log di diagnostica per le categorie seguenti:

- **Log di archiviazione**: log correlati agli archivi di Hub eventi, in particolare quelli relativi agli errori di archiviazione.
- **Log operativi**: informazioni su ciò che avviene durante il funzionamento di Hub eventi, in particolare il tipo di operazione, come creazione dell'hub eventi, risorse usate e stato dell'operazione.
- **Log di ridimensionamento automatico**: informazioni sulle operazioni di scalabilità automatica eseguite in uno spazio dei nomi di hub eventi. 
- **Log coordinatore Kafka** : informazioni sulle operazioni del coordinatore Kafka correlate a hub eventi. 
- **Log utente Kafka**: informazioni sulle operazioni utente Kafka correlate a hub eventi. 
- **Evento di connessione rete virtuale (VNet) di hub eventi**: informazioni sugli eventi di connessione alla rete virtuale di hub eventi. 
- **Log utente chiave gestiti dal cliente**: informazioni sulle operazioni correlate alla chiave gestita dal cliente. 


    Tutti i log vengono archiviati in formato JavaScript Object Notation (JSON). Ogni voce presenta campi stringa che usano il formato descritto nelle sezioni seguenti.

## <a name="archive-logs-schema"></a>Schema dei log di archiviazione

Le stringhe JSON dei log di archiviazione includono gli elementi elencati nella tabella seguente:

Name | Descrizione
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
message | Messaggio di errore.
category | ArchiveLogs

Il codice seguente è un esempio di stringa JSON di log di archiviazione:

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

## <a name="operational-logs-schema"></a>Schema di log operativi

Le stringhe JSON dei log operativi includono gli elementi elencati nella seguente tabella:

Name | Descrizione
------- | -------
ActivityId | ID interno, usato a scopo di rilevamento.
EventName | Nome operazione.  
resourceId | ID della risorsa di Azure Resource Manager.
SubscriptionId | l'ID sottoscrizione.
EventTimeString | Durata dell'operazione.
EventProperties | Proprietà dell'operazione.
Stato | Stato dell'operazione.
Chiamante | Chiamante dell'operazione (Portale di Azure o client di gestione).
category | OperationalLogs

Il codice seguente è un esempio di stringa JSON di log operativo:

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

## <a name="autoscale-logs-schema"></a>Schema dei log di scalabilità automatica
Il file JSON del log di scalabilità automatica include gli elementi elencati nella tabella seguente:

| Name | Descrizione |
| ---- | ----------- | 
| trackingId | ID interno, usato a scopo di traccia |
| resourceId | ID interno, che contiene l'ID sottoscrizione di Azure e il nome dello spazio dei nomi |
| message | Messaggio informativo, che fornisce informazioni dettagliate sull'azione con aumento automatico. Il messaggio contiene il valore precedente e quello corrente dell'unità di velocità effettiva per uno spazio dei nomi specificato e che cosa ha attivato l'inflat della TU. |

## <a name="kafka-coordinator-logs-schema"></a>Schema dei log coordinatore Kafka
Il log JSON del coordinatore Kafka include gli elementi elencati nella tabella seguente:

| Name | Descrizione |
| ---- | ----------- | 
| requestId | ID richiesta, usato a scopo di traccia |
| resourceId | ID interno, che contiene l'ID sottoscrizione di Azure e il nome dello spazio dei nomi |
| operationName | Nome dell'operazione eseguita durante il coordinamento del gruppo |
| clientId | ID client |
| namespaceName | Nome spazio dei nomi | 
| subscriptionId | ID della sottoscrizione di Azure |
| message | Messaggio informativo, che fornisce informazioni dettagliate sulle azioni eseguite durante il coordinamento del gruppo di consumer. |

## <a name="kafka-user-error-logs-schema"></a>Schema dei log degli errori utente Kafka
Il file JSON del log degli errori utente Kafka include gli elementi elencati nella tabella seguente:

| Name | Descrizione |
| ---- | ----------- |
| trackingId | ID di traccia, usato a scopo di traccia. |
| namespaceName | Nome spazio dei nomi |
| eventhub | Nome hub eventi |
| partitionId | Partition ID |
| groupId | ID gruppo |
| ClientId | ID client |
| resourceId | ID interno, che contiene l'ID sottoscrizione di Azure e il nome dello spazio dei nomi |
| message | Messaggio informativo, che fornisce informazioni dettagliate su un errore |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Schema di eventi di connessione alla rete virtuale di hub eventi

L'evento JSON della connessione rete virtuale (VNet) degli hub eventi include gli elementi elencati nella tabella seguente:

| Name | Descrizione |
| ---  | ----------- | 
| subscriptionId | ID della sottoscrizione di Azure |
| namespaceName | Nome spazio dei nomi |
| IpAddress | Indirizzo IP di un client che si connette al servizio Hub eventi |
| action | Azione eseguita dal servizio Hub eventi durante la valutazione delle richieste di connessione. Le azioni supportate sono **AcceptConnection** e **RejectConnection**. |
| reason | Fornisce un motivo per cui è stata eseguita l'azione |
| count | Numero di occorrenze per l'azione specificata |
| resourceId | ID risorsa interna, che contiene l'ID sottoscrizione e il nome dello spazio dei nomi. |

## <a name="customer-managed-key-user-logs"></a>Log utente chiave gestiti dal cliente
JSON del log utente chiave gestita dal cliente include gli elementi elencati nella tabella seguente:

| Name | Descrizione |
| ---- | ----------- | 
| category | Tipo di categoria per un messaggio. È uno dei valori seguenti: **Error** e **info** |
| resourceId | ID risorsa interna, che include l'ID sottoscrizione di Azure e il nome dello spazio dei nomi |
| keyVault | Nome della risorsa Key Vault |
| Key | Nome della chiave Key Vault. |
| Versione | Versione della chiave di Key Vault |
| operazione | Nome di un'operazione eseguita per rispondere alle richieste |
| code | Codice di stato |
| message | Message, che fornisce informazioni dettagliate su un errore o un messaggio informativo |



## <a name="next-steps"></a>Passaggi successivi
- [Introduzione a Hub eventi](event-hubs-what-is-event-hubs.md)
- [Panoramica dell'API di hub eventi](event-hubs-api-overview.md)
- Introduzione all'Hub eventi
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
