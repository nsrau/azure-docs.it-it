---
title: Log di diagnostica del bus di servizio di Azure Documenti Microsoft
description: Questo articolo offre una panoramica di tutti i log operativi e di diagnostica disponibili per il bus di servizio di Azure.This article provides an overview of all operational and diagnostics logs that are available for Azure Service Bus.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: c8eba538a7015648611e6054ce85b381dcfc9105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760999"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Abilitare i log di diagnostica per il bus di servizioEnable diagnostics logs for Service Bus

Quando si inizia a usare lo spazio dei nomi del bus di servizio di Azure, è possibile monitorare come e quando lo spazio dei nomi viene creato, eliminato o accessibile. In questo articolo viene fornita una panoramica di tutti i log operativi e di diagnostica disponibili.

Il bus di servizio di Azure supporta attualmente i log operativi e di attività, che acquisiscono le operazioni di gestione eseguite nello spazio dei nomi del bus di servizio di Azure.Azure Service Bus currently supports activity and operational logs, which capture *management operations* that are performed on the Azure Service Bus namespace. In particolare, questi log acquisiscono il tipo di operazione, tra cui la creazione delle code, le risorse usate e lo stato dell'operazione.

## <a name="operational-logs-schema"></a>Schema di log operativi

Tutti i log vengono archiviati in formato JSON (JavaScript Object Notation) nelle due posizioni seguenti:

- **AzureActivity:** visualizza i log delle operazioni e delle azioni condotte sullo spazio dei nomi nel portale di Azure o tramite distribuzioni di modelli di Azure Resource Manager.AzureActivity : Displays logs from operations and actions that are conducted against your namespace in the Azure portal or through Azure Resource Manager template deployments.
- **AzureDiagnostics:** visualizza i log delle operazioni e delle azioni condotte sullo spazio dei nomi tramite l'API o i client di gestione nell'SDK del linguaggio.

Le stringhe JSON di log operative includono gli elementi elencati nella tabella seguente:Operational log JSON strings include the elements listed in the following table:

| Nome | Descrizione |
| ------- | ------- |
| ActivityId | ID interno, utilizzato per identificare l'attività specificata |
| EventName | Nome operazione |
| ResourceId | ID della risorsa Azure Resource Manager |
| SubscriptionId | ID sottoscrizione |
| EventTimeString | Durata dell'operazione |
| EventProperties | Proprietà dell'operazione |
| Stato | Stato dell'operazione |
| Chiamante | Chiamante dell'operazione (portale di Azure o client di gestione)Caller of operation (the Azure portal or management client) |
| Category | OperationalLogs |

Di seguito è riportato un esempio di stringa JSON di log operativo:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Eventi e operazioni acquisiti nei log operativi

I log operativi acquisiscono tutte le operazioni di gestione eseguite nello spazio dei nomi del bus di servizio di Azure.Operational logs capture all management operations that are performed on the Azure Service Bus namespace. Data operations are not captured, because of the high volume of data operations that are conducted on Azure Service Bus.

> [!NOTE]
> Per migliorare il monitoraggio delle operazioni sui dati, è consigliabile usare la traccia lato client.

Le seguenti operazioni di gestione vengono acquisite nei log operativi:The following management operations are captured in operational logs: 

| Scope | Operazione|
|-------| -------- |
| Spazio dei nomi | <ul> <li> Creare lo spazio dei nomi</li> <li> Spazio dei nomi Update </li> <li> Elimina spazio dei nomi </li>  </ul> | 
| Coda | <ul> <li> Creare una coda</li> <li> Update Queue</li> <li> Eliminazione code </li> </ul> | 
| Argomento | <ul> <li> Crea argomento </li> <li> Aggiornamento di un argomento </li> <li> Elimina argomento </li> </ul> |
| Subscription | <ul> <li> Creazione di sottoscrizioni </li> <li> Aggiornamento di sottoscrizioni </li> <li> Eliminazione di sottoscrizioni </li> </ul> |

> [!NOTE]
> Attualmente, le operazioni di *lettura* non vengono rilevate nei registri operativi.

## <a name="enable-operational-logs"></a>Abilitare i registri operativi

I registri operativi sono disabilitati per impostazione predefinita. Per abilitare i log di diagnostica, eseguire le operazioni seguenti:To enable diagnostics logs, do the following:

1. Nel [portale di Azure](https://portal.azure.com)passare allo spazio dei nomi del bus di servizio di Azure e quindi, in **Monitoraggio,** selezionare **Impostazioni di diagnostica**.

   ![Il collegamento "Impostazioni di diagnostica"](./media/service-bus-diagnostic-logs/image1.png)

1. Nel riquadro **Impostazioni di diagnostica** selezionare Aggiungi **impostazione diagnostica**.  

   ![Il collegamento "Aggiungi impostazione diagnostica"](./media/service-bus-diagnostic-logs/image2.png)

1. Configurare le impostazioni di diagnostica eseguendo le operazioni seguenti:Configure the diagnostics settings by doing the following:

   a. Nella casella **Nome** immettere un nome per le impostazioni di diagnostica.  

   b. Selezionare una delle tre destinazioni seguenti per i log di diagnostica:  
   - Se si seleziona **Archivia in un account di archiviazione,** è necessario configurare l'account di archiviazione in cui verranno archiviati i log di diagnostica.  
   - Se si seleziona **Flusso a un hub eventi**, è necessario configurare l'hub eventi a cui si desidera trasmettere i log di diagnostica.
   - Se si seleziona **Invia a Log Analytics**, è necessario specificare a quale istanza di Log Analytics verrà inviata la diagnostica.  

   c. Selezionare la casella di controllo **OperationalLogs.**

    ![Il riquadro "Impostazioni di diagnostica"](./media/service-bus-diagnostic-logs/image3.png)

1. Selezionare **Salva**.

Le nuove impostazioni diventano effettive in circa 10 minuti. I log vengono visualizzati nella destinazione di archiviazione configurata nel riquadro **Log di diagnostica.**

Per altre informazioni sulla configurazione delle impostazioni di diagnostica, vedere [Panoramica dei log di diagnostica di Azure.For](../azure-monitor/platform/diagnostic-logs-overview.md)more information about configuring diagnostics settings, see the overview of Azure diagnostics logs.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul bus di servizio, vedere:To learn more about Service Bus, see:

* [Introduzione al bus di servizio](service-bus-messaging-overview.md)
* [Introduzione al bus di servizio](service-bus-dotnet-get-started-with-queues.md)
