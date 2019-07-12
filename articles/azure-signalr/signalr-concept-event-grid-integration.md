---
title: Reazione a eventi di servizio Azure SignalR
description: Usare griglia di eventi di Azure per sottoscrivere gli eventi di servizio Azure SignalR.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 06/12/2019
ms.topic: conceptual
ms.service: azure-signalr
ms.openlocfilehash: 02f88c5953d499b30f2ea3408318f70a72f42b0f
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789189"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reazione a eventi di servizio Azure SignalR

Eventi di SignalR Service di Azure consentono alle applicazioni di reagire alle connessioni client connesso o disconnesso usando moderne architetture senza server. e senza la necessità di usare codice complesso o servizi di polling costosi e inefficienti.  Gli eventi vengono invece inviati attraverso [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) ai sottoscrittori, ad esempio [Funzioni di Azure](https://azure.microsoft.com/services/functions/), [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/) o anche al listener http personalizzato in uso, e si paga solo ciò che si usa.

Eventi di SignalR Service di Azure vengono inviati in modo affidabile per il servizio griglia di eventi che fornisce servizi di distribuzione affidabile per le applicazioni attraverso rich ripetere i criteri e il recapito non recapitabilità. Per altre informazioni, vedere [recapito dei messaggi di griglia di eventi e di ripetizione dei tentativi](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Modello di Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Stato senza server
Eventi di SignalR Service di Azure sono attivi solo quando le connessioni client sono nello stato senza server. In generale, se un client non instrada a un server hub, passa allo stato senza server. Lavoro in modalità classica solo quando l'hub, che si connettono le connessioni client, non è un server hub. Tuttavia, in modalità senza server è consigliabile evitare la presenza di problemi. Per altre informazioni dettagliate sulla modalità di servizio, vedere [come scegliere la modalità servizio](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Eventi disponibili servizio Azure SignalR
Griglia di eventi usa le [sottoscrizioni di eventi](../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Le sottoscrizioni di eventi di Azure SignalR Service supportano due tipi di eventi:  

|Nome evento|Descrizione|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Generato quando una connessione client è connesso.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Generato quando una connessione client è disconnesso.|

## <a name="event-schema"></a>Schema di eventi
Gli eventi di Azure SignalR Service contengono tutte le informazioni che necessarie per rispondere alle modifiche nei dati. È possibile identificare un servizio Azure SignalR evento con la proprietà eventType inizia con "Microsoft.SignalRService". Altre informazioni sull'utilizzo delle proprietà di evento di griglia di eventi sono documentate al [schema di eventi di griglia di eventi](../event-grid/event-schema.md).  

Ecco un esempio di un evento connesso connessione client:
```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Per altre informazioni, vedere [dello schema di eventi di SignalR Service](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su griglia di eventi e provare gli eventi di servizio Azure SignalR:

> [!div class="nextstepaction"]
> [Provare un'integrazione di griglia di eventi di esempio con il servizio Azure SignalR](./signalr-howto-event-grid-integration.md)
> [sulla griglia di eventi](../event-grid/overview.md)
