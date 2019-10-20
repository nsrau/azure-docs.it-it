---
title: Log di diagnostica del bus di servizio di Azure | Microsoft Docs
description: Informazioni su come configurare i log di diagnostica per il bus di servizio in Azure.
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 41e0bdc1f04c9491ebe939f46b59ae4eb2bc7ab6
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592448"
---
# <a name="enable-diagnostic-logs-for-service-bus"></a>Abilitare i log di diagnostica per il bus di servizio

Quando si inizia a usare lo spazio dei nomi del bus di servizio di Azure, potrebbe essere necessario monitorare come e quando lo spazio dei nomi viene creato, eliminato o accessibile. Questo articolo fornisce una panoramica di tutti i log operativi/di diagnostica disponibili.

Il bus di servizio di Azure supporta attualmente log attività/operativi che acquisiscono **le operazioni di gestione** eseguite nello spazio dei nomi del bus di servizio di Azure. In particolare, questi log acquisiscono il tipo di operazione, tra cui la creazione delle code, le risorse usate e lo stato dell'operazione.

## <a name="operational-logs-schema"></a>Schema di log operativi

Tutti i log vengono archiviati in formato JavaScript Object Notation (JSON) nelle due posizioni seguenti.

- **AzureActivity** : consente di visualizzare i log delle operazioni/azioni eseguite sullo spazio dei nomi nel portale o tramite le distribuzioni Azure Resource Manager modello.
- **AzureDiagnostics** : Visualizza i log delle operazioni/azioni eseguite sullo spazio dei nomi usando l'API o i client di gestione nell'SDK del linguaggio.

Le stringhe JSON dei log operativi includono gli elementi elencati nella seguente tabella:

| name | Description |
| ------- | ------- |
| ActivityId | ID interno, usato per identificare l'attività specificata |
| EventName | Nome operazione |
| ResourceId | ID della risorsa Azure Resource Manager |
| SubscriptionId | ID sottoscrizione |
| EventTimeString | Durata dell'operazione |
| EventProperties | Proprietà dell'operazione |
| Status | Stato dell'operazione |
| Chiamante | Chiamante dell'operazione (Portale di Azure o client di gestione) |
| Categoria | OperationalLogs |

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

## <a name="what-eventsoperations-are-captured-in-operational-logs"></a>Quali eventi/operazioni vengono acquisiti nei log operativi?

I log operazioni acquisiscono tutte le operazioni di gestione eseguite nello spazio dei nomi del bus di servizio di Azure. Le operazioni sui dati non vengono acquisite a causa del volume elevato di operazioni dati eseguite nel bus di servizio di Azure.

> [!NOTE]
> Per tenere traccia delle operazioni sui dati, è consigliabile usare la traccia lato client.

Le operazioni di gestione seguenti vengono acquisite nei log operativi. 

| Scope | Operazione|
|-------| -------- |
| Spazio dei nomi | <ul> <li> Creare lo spazio dei nomi</li> <li> Aggiorna spazio dei nomi </li> <li> Elimina spazio dei nomi </li>  </ul> | 
| Coda | <ul> <li> Crea coda</li> <li> Aggiorna coda</li> <li> Eliminazione code </li> </ul> | 
| Argomento | <ul> <li> Crea argomento </li> <li> Aggiorna argomento </li> <li> Elimina argomento </li> </ul> |
| Sottoscrizione | <ul> <li> Creare la sottoscrizione </li> <li> Aggiorna sottoscrizione </li> <li> Elimina sottoscrizione </li> </ul> |

> [!NOTE]
> Attualmente, le operazioni di **lettura** non vengono registrate nei log operativi.

## <a name="how-to-enable-operational-logs"></a>Come si abilitano i log operativi?

Per impostazione predefinita, i log operativi sono disabilitati. Per abilitare i log di diagnostica, eseguire la procedura seguente:

1. Nella [portale di Azure](https://portal.azure.com)passare allo spazio dei nomi del bus di servizio di Azure e in **monitoraggio**fare clic su **impostazioni di diagnostica**.

   ![navigazione al pannello dei log di diagnostica](./media/service-bus-diagnostic-logs/image1.png)

2. Fare clic su **Aggiungi impostazione di diagnostica** per configurare le impostazioni di diagnostica.  

   ![attivazione dei log di diagnostica](./media/service-bus-diagnostic-logs/image2.png)

3. Configurare le impostazioni di diagnostica
   1. Digitare un **nome** per identificare le impostazioni di diagnostica.
   2. Selezionare una destinazione per la diagnostica.
      - Se si seleziona **account di archiviazione**, è necessario configurare l'account di archiviazione in cui verrà archiviata la diagnostica.
      - Se si seleziona **Hub eventi**, è necessario configurare l'hub eventi appropriato in cui verranno trasmesse le impostazioni di diagnostica.
      - Se si seleziona **log Analytics**, è necessario specificare l'istanza di log Analytics la diagnostica verrà inviata.
    3. Controllare **OperationalLogs**.

       ![modifica dello stato dei log di diagnostica](./media/service-bus-diagnostic-logs/image3.png)

4. Fare clic su **Salva**


Le nuove impostazioni diventano effettive in circa 10 minuti. Trascorso questo tempo, i log vengono visualizzati nella destinazione di archiviazione configurata, all'interno del pannello **Log di diagnostica**.

Per altre informazioni sulla configurazione della diagnostica, vedere la [panoramica dei log di diagnostica di Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul bus di servizio, vedere i collegamenti seguenti:

* [Introduzione al bus di servizio](service-bus-messaging-overview.md)
* [Introduzione al bus di servizio](service-bus-dotnet-get-started-with-queues.md)
