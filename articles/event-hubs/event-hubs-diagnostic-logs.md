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
ms.openlocfilehash: 0fb5da965a9b13667b8a128e83a5a4cd2c2b28d7
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691849"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Configurare i log di diagnostica per un hub eventi di Azure

È possibile visualizzare due tipi di log per Hub eventi di Azure:

* **[Log attività](../azure-monitor/platform/platform-logs-overview.md)**: questi log contengono informazioni sulle operazioni eseguite in un processo. I log sono sempre attivati. È possibile visualizzare le voci del log attività selezionando **log attività** nel riquadro sinistro per lo spazio dei nomi dell'hub eventi nel portale di Azure. Ad esempio: "crea o Aggiorna spazio dei nomi", "crea o aggiorna Hub eventi".

    ![Log attività per uno spazio dei nomi di hub eventi](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Log di diagnostica](../azure-monitor/platform/platform-logs-overview.md)**: i log di diagnostica forniscono informazioni più complete sulle operazioni e sulle azioni eseguite sullo spazio dei nomi tramite l'API o i client di gestione nell'SDK del linguaggio. 
    
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

| Categoria | Descrizione | 
| -------- | ----------- | 
| Log di archiviazione | Acquisisce informazioni sulle operazioni di [acquisizione di hub eventi](event-hubs-capture-overview.md) , in particolare i log relativi agli errori di acquisizione. |
| Log operativi | Acquisisce tutte le operazioni di gestione eseguite nello spazio dei nomi di hub eventi di Azure. Le operazioni sui dati non vengono acquisite a causa del volume elevato di operazioni sui dati eseguite nell'hub eventi di Azure. |
| Log di ridimensionamento automatico | Acquisisce le operazioni di gonfiaggio automatico eseguite su uno spazio dei nomi di hub eventi. |
| Log coordinatore Kafka | Acquisisce le operazioni del coordinatore Kafka correlate a hub eventi. |
| Log degli errori utente Kafka | Acquisisce informazioni sulle API Kafka chiamate su Hub eventi. |
| Evento di connessione rete virtuale (VNet) di hub eventi | Acquisisce informazioni sugli indirizzi IP e sulle reti virtuali che inviano il traffico a hub eventi. |
| Log utente chiave gestiti dal cliente | Acquisisce le operazioni correlate alla chiave gestita dal cliente. |


Tutti i log vengono archiviati in formato JavaScript Object Notation (JSON). Ogni voce presenta campi stringa che usano il formato descritto nelle sezioni seguenti.

## <a name="archive-logs-schema"></a>Schema dei log di archiviazione

Le stringhe JSON dei log di archiviazione includono gli elementi elencati nella tabella seguente:

Nome | Descrizione
------- | -------
TaskName | Descrizione dell'attività non riuscita
ActivityId | ID interno, usato a scopo di rilevamento
trackingId | ID interno, usato a scopo di rilevamento
resourceId | ID della risorsa Azure Resource Manager
eventHub | Nome completo dell'hub eventi (include il nome dello spazio dei nomi)
partitionId | Partizione dell'hub eventi in corso di scrittura
archiveStep | valori possibili: ArchiveFlushWriter, DestinationInit
startTime | Ora di inizio di un errore
errori | Numero di volte in cui si è verificato l'errore
durationInSeconds | Durata dell'errore
message | Messaggio di errore
category | ArchiveLogs

Il codice seguente è un esempio di stringa JSON di log di archiviazione:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
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

Nome | Descrizione
------- | -------
ActivityId | ID interno, usato a scopo di rilevamento |
EventName | Nome operazione |
resourceId | ID della risorsa Azure Resource Manager |
SubscriptionId | ID sottoscrizione |
EventTimeString | Durata dell'operazione |
EventProperties | Proprietà dell'operazione |
Stato | Stato dell'operazione |
Chiamante | Chiamante dell'operazione (Portale di Azure o client di gestione) |
Categoria | OperationalLogs |

Il codice seguente è un esempio di stringa JSON di log operativo:

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>Schema dei log di scalabilità automatica
Il file JSON del log di scalabilità automatica include gli elementi elencati nella tabella seguente:

| Nome | Descrizione |
| ---- | ----------- | 
| TrackingId | ID interno, usato a scopo di traccia |
| ResourceId | ID della risorsa di Azure Resource Manager. |
| Message | Messaggio informativo, che fornisce informazioni dettagliate sull'azione con aumento automatico. Il messaggio contiene il valore precedente e quello corrente dell'unità di velocità effettiva per uno spazio dei nomi specificato e che cosa ha attivato l'inflat della TU. |

## <a name="kafka-coordinator-logs-schema"></a>Schema dei log coordinatore Kafka
Il log JSON del coordinatore Kafka include gli elementi elencati nella tabella seguente:

| Nome | Descrizione |
| ---- | ----------- | 
| RequestId | ID richiesta, usato a scopo di traccia |
| ResourceId | ID della risorsa Azure Resource Manager |
| Operazione | Nome dell'operazione eseguita durante il coordinamento del gruppo |
| ClientId | ID client |
| NomeSpazioDeiNomi | Nome spazio dei nomi | 
| SubscriptionId | ID della sottoscrizione di Azure |
| Message | Messaggio informativo o di avviso, che fornisce informazioni dettagliate sulle azioni eseguite durante il coordinamento del gruppo. |

### <a name="example"></a>Esempio

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Schema dei log degli errori utente Kafka
Il file JSON del log degli errori utente Kafka include gli elementi elencati nella tabella seguente:

| Nome | Descrizione |
| ---- | ----------- |
| TrackingId | ID di traccia, usato a scopo di traccia. |
| NomeSpazioDeiNomi | Nome spazio dei nomi |
| Eventhub | Nome hub eventi |
| PartitionId | Partition ID |
| GroupId | ID gruppo |
| ClientId | ID client |
| ResourceId | ID della risorsa di Azure Resource Manager. |
| Message | Messaggio informativo, che fornisce informazioni dettagliate su un errore |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Schema di eventi di connessione alla rete virtuale di hub eventi

L'evento JSON della connessione rete virtuale (VNet) degli hub eventi include gli elementi elencati nella tabella seguente:

| Nome | Descrizione |
| ---  | ----------- | 
| SubscriptionId | ID della sottoscrizione di Azure |
| NomeSpazioDeiNomi | Nome spazio dei nomi |
| IPAddress | Indirizzo IP di un client che si connette al servizio Hub eventi |
| Azione | Azione eseguita dal servizio Hub eventi durante la valutazione delle richieste di connessione. Le azioni supportate sono **accetta connessione** e **Nega connessione**. |
| Motivo | Fornisce un motivo per cui è stata eseguita l'azione |
| Conteggio | Numero di occorrenze per l'azione specificata |
| ResourceId | ID della risorsa di Azure Resource Manager. |

### <a name="example"></a>Esempio

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>Log utente chiave gestiti dal cliente
JSON del log utente chiave gestita dal cliente include gli elementi elencati nella tabella seguente:

| Nome | Descrizione |
| ---- | ----------- | 
| Categoria | Tipo di categoria per un messaggio. È uno dei valori seguenti: **Error** e **info** |
| ResourceId | ID risorsa interna, che include l'ID sottoscrizione di Azure e il nome dello spazio dei nomi |
| Insieme di credenziali delle chiavi | Nome della risorsa Key Vault |
| Chiave | Nome della chiave Key Vault. |
| Versione | Versione della chiave di Key Vault |
| Operazione | Nome di un'operazione eseguita per rispondere alle richieste |
| Codice | Codice stato |
| Message | Message, che fornisce informazioni dettagliate su un errore o un messaggio informativo |



## <a name="next-steps"></a>Passaggi successivi
- [Introduzione a Hub eventi](event-hubs-what-is-event-hubs.md)
- [Panoramica dell'API di hub eventi](event-hubs-api-overview.md)
- Introduzione all'Hub eventi
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
