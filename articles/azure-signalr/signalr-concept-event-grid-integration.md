---
title: Reagire agli eventi del servizio Azure SignalR
description: Usare griglia di eventi di Azure per sottoscrivere gli eventi del servizio Azure SignalR. Gli altri servizi downstream possono essere attivati da questi eventi.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158199"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reazione agli eventi del servizio Azure SignalR

Gli eventi del servizio Azure SignalR consentono alle applicazioni di reagire alle connessioni client connesse o disconnesse usando architetture senza server moderne. e senza la necessità di usare codice complesso o servizi di polling costosi e inefficienti.  Gli eventi vengono invece inviati attraverso [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) ai sottoscrittori, ad esempio [Funzioni di Azure](https://azure.microsoft.com/services/functions/), [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/) o anche al listener http personalizzato in uso, e si paga solo ciò che si usa.

Gli eventi del servizio Azure SignalR vengono inviati in modo affidabile al servizio griglia di eventi, che fornisce servizi di recapito affidabili alle applicazioni tramite criteri avanzati per i tentativi e il recapito dei messaggi non recapitabili. Per altre informazioni, vedere [recapito dei messaggi di griglia di eventi e riprovare](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Modello di Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Stato senza server
Gli eventi del servizio Azure SignalR sono attivi solo quando le connessioni client sono in stato senza server. In generale, se un client non viene indirizzato a un server Hub, entra nello stato senza server. La modalità classica funziona solo quando l'hub, a cui si connettono le connessioni client, non ha un server Hub. Tuttavia, per evitare problemi, è consigliabile usare la modalità senza server. Per ulteriori informazioni sulla modalità di servizio, vedere [How to Choose Service Mode](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Eventi del servizio Azure SignalR disponibili
Griglia di eventi usa le [sottoscrizioni di eventi](../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Le sottoscrizioni di eventi del servizio Azure SignalR supportano due tipi di eventi:  

|Nome evento|Descrizione|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Generato quando una connessione client è connessa.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Generato quando una connessione client viene disconnessa.|

## <a name="event-schema"></a>Schema di eventi
Gli eventi del servizio Azure SignalR contengono tutte le informazioni necessarie per rispondere alle modifiche apportate ai dati. È possibile identificare un evento del servizio SignalR di Azure con la proprietà eventType che inizia con "Microsoft. SignalRService". Informazioni aggiuntive sull'utilizzo delle proprietà degli eventi di griglia di eventi sono documentate nello [schema di eventi di griglia di eventi](../event-grid/event-schema.md).  

Di seguito è riportato un esempio di un evento connesso alla connessione client:
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

Per altre informazioni, vedere [schema degli eventi del servizio SignalR](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su griglia di eventi e assegna agli eventi del servizio Azure SignalR un tentativo:

> [!div class="nextstepaction"]
> [Provare un'integrazione di griglia di eventi di esempio con il servizio Azure signalr](./signalr-howto-event-grid-integration.md)
> [su griglia di eventi](../event-grid/overview.md)
