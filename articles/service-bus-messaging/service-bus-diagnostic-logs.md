---
title: Log di diagnostica del bus di servizio di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica di tutti i log operativi e di diagnostica disponibili per il bus di servizio di Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f227f5a988ccd51425b4f43e87b4ed4d9af74e1f
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88064435"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Abilitare i log di diagnostica per il bus di servizio

Quando si inizia a usare lo spazio dei nomi del bus di servizio di Azure, potrebbe essere necessario monitorare come e quando lo spazio dei nomi viene creato, eliminato o accessibile. Questo articolo fornisce una panoramica di tutti i log operativi e di diagnostica disponibili.

Il bus di servizio di Azure supporta attualmente log attività e operativi, che acquisiscono *le operazioni di gestione* eseguite nello spazio dei nomi del bus di servizio di Azure. In particolare, questi log acquisiscono il tipo di operazione, tra cui la creazione delle code, le risorse usate e lo stato dell'operazione.

## <a name="operational-logs-schema"></a>Schema di log operativi

Tutti i log vengono archiviati in formato JavaScript Object Notation (JSON) nelle due posizioni seguenti:

- **AzureActivity**: Visualizza i log delle operazioni e delle azioni eseguite sullo spazio dei nomi nell'portale di Azure o tramite Azure Resource Manager distribuzioni di modelli.
- **AzureDiagnostics**: Visualizza i log delle operazioni e delle azioni eseguite sullo spazio dei nomi tramite l'API o i client di gestione nell'SDK del linguaggio.

Le stringhe JSON dei log operativi includono gli elementi elencati nella tabella seguente:

| Nome | Descrizione |
| ------- | ------- |
| ActivityId | ID interno, usato per identificare l'attività specificata |
| EventName | Nome operazione |
| ResourceId | ID della risorsa Azure Resource Manager |
| SubscriptionId | ID sottoscrizione |
| EventTimeString | Durata dell'operazione |
| EventProperties | Proprietà dell'operazione |
| Stato | Stato dell'operazione |
| Chiamante | Chiamante dell'operazione (il portale di Azure o il client di gestione) |
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

I log operativi acquisiscono tutte le operazioni di gestione eseguite nello spazio dei nomi del bus di servizio di Azure. Le operazioni sui dati non vengono acquisite a causa del volume elevato di operazioni dati eseguite nel bus di servizio di Azure.

> [!NOTE]
> Per semplificare il monitoraggio delle operazioni sui dati, è consigliabile usare la traccia lato client.

Le operazioni di gestione seguenti vengono acquisite nei log operativi: 

| Scope | Operazione|
|-------| -------- |
| Spazio dei nomi | <ul> <li> Creare lo spazio dei nomi</li> <li> Aggiorna spazio dei nomi </li> <li> Elimina spazio dei nomi </li> <li> Aggiornare i criteri SharedAccess dello spazio dei nomi </li> </ul> | 
| Coda | <ul> <li> Creare una coda</li> <li> Update Queue</li> <li> Eliminazione code </li> <li> Elimina la coda di eliminazione </li> </ul> | 
| Argomento | <ul> <li> Crea argomento </li> <li> Aggiornamento di un argomento </li> <li> Elimina argomento </li> <li> Elimina eliminazione dall'eliminazione dell'argomento </li> </ul> |
| Subscription | <ul> <li> Creazione di sottoscrizioni </li> <li> Aggiornamento di sottoscrizioni </li> <li> Eliminazione di sottoscrizioni </li> <li> Eliminazione di una sottoscrizione Delete </li> </ul> |

> [!NOTE]
> Attualmente, le operazioni di *lettura* non vengono registrate nei log operativi.

## <a name="enable-operational-logs"></a>Abilitare i log operativi

Per impostazione predefinita, i log operativi sono disabilitati. Per abilitare i log di diagnostica, eseguire le operazioni seguenti:

1. Nel [portale di Azure](https://portal.azure.com)passare allo spazio dei nomi del bus di servizio di Azure e quindi selezionare impostazioni di **diagnostica**in **monitoraggio**.

   ![Collegamento "impostazioni di diagnostica"](./media/service-bus-diagnostic-logs/image1.png)

1. Nel riquadro **impostazioni di diagnostica** selezionare **Aggiungi impostazione di diagnostica**.  

   ![Collegamento "Aggiungi impostazione di diagnostica"](./media/service-bus-diagnostic-logs/image2.png)

1. Configurare le impostazioni di diagnostica eseguendo le operazioni seguenti:

   a. Nella casella **Nome** immettere un nome per le impostazioni di diagnostica.  

   b. Selezionare una delle tre destinazioni seguenti per i log di diagnostica:  
   - Se si seleziona **archivia in un account di archiviazione**, è necessario configurare l'account di archiviazione in cui verranno archiviati i log di diagnostica.  
   - Se si seleziona **flusso in un hub eventi**, è necessario configurare l'hub eventi in cui si desidera trasmettere i log di diagnostica.
   - Se si seleziona **Invia a log Analytics**, è necessario specificare l'istanza di log Analytics a cui verrà inviata la diagnostica.  

   c. Selezionare la casella di controllo **OperationalLogs** .

    ![Riquadro "impostazioni di diagnostica"](./media/service-bus-diagnostic-logs/image3.png)

1. Selezionare **Salva**.

Le nuove impostazioni diventano effettive entro 10 minuti circa. I log vengono visualizzati nella destinazione di archiviazione configurata, nel riquadro **Log di diagnostica**.

Per altre informazioni sulla configurazione delle impostazioni di diagnostica, vedere la [panoramica dei log di diagnostica di Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul bus di servizio, vedere:

* [Introduzione al bus di servizio](service-bus-messaging-overview.md)
* [Introduzione al bus di servizio](service-bus-dotnet-get-started-with-queues.md)