---
title: Reagire agli eventi del servizio SignalR di AzureReact to Azure SignalR Service events
description: Usare Griglia di eventi di Azure per sottoscrivere gli eventi del servizio SignalR di Azure.Use Azure Event Grid to subscribe to Azure SignalR Service events. Altri servizi downstream possono essere attivati da questi eventi.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158199"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reazione agli eventi del Servizio Azure SignalR

Gli eventi del servizio SignalR di Azure consentono alle applicazioni di reagire alle connessioni client connesse o disconnesse tramite architetture senza server moderne. e senza la necessità di usare codice complesso o servizi di polling costosi e inefficienti.  Gli eventi vengono invece inviati attraverso [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) ai sottoscrittori, ad esempio [Funzioni di Azure](https://azure.microsoft.com/services/functions/), [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/) o anche al listener http personalizzato in uso, e si paga solo ciò che si usa.

Gli eventi del servizio SignalR di Azure vengono inviati in modo affidabile al servizio Griglia di eventi, che fornisce servizi di recapito affidabili alle applicazioni tramite criteri di ripetizione dei tentativi avanzati e recapito di messaggi non recapitabili. Per ulteriori informazioni, vedere Recapito dei messaggi di Griglia di eventi [e riprovare.](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)

![Modello di Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Stato senza server
Gli eventi del servizio SignalR di Azure sono attivi solo quando le connessioni client sono in stato senza server. In generale, se un client non viene instradato a un server hub, passa allo stato serverless. La modalità classica funziona solo quando l'hub, a cui si connettono le connessioni client, non dispone di un server hub. Tuttavia, la modalità senza server è consigliata per evitare qualche problema. Per ulteriori informazioni sulla modalità di servizio, vedere [Come scegliere la modalità servizio](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Eventi disponibili del servizio SignalR di AzureAvailable Azure SignalR Service events
Griglia di eventi usa le [sottoscrizioni di eventi](../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Le sottoscrizioni di eventi del servizio SignalR di Azure supportano due tipi di eventi:Azure SignalR Service event subscriptions support two types of events:  

|Nome evento|Descrizione|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Generato quando è connessa una connessione client.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Generato quando una connessione client viene disconnessa.|

## <a name="event-schema"></a>Schema di eventi
Gli eventi del servizio SignalR di Azure contengono tutte le informazioni necessarie per rispondere alle modifiche nei dati. È possibile identificare un evento del servizio SignalR di Azure con la proprietà eventType inizia con "Microsoft.SignalRService". Ulteriori informazioni sull'utilizzo delle proprietà degli eventi Griglia di eventi sono documentate nello [schema degli eventi Griglia di eventi](../event-grid/event-schema.md).  

Di seguito è riportato un esempio di evento connesso alla connessione client:Here is an example of a client connection connected event:
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

Per ulteriori informazioni, vedere [SignalR Service events schema](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su Griglia di eventi e provare gli eventi del servizio SignalR di Azure:Learn more about Event Grid and give Azure SignalR Service events a try:

> [!div class="nextstepaction"]
> [Provare un'integrazione](./signalr-howto-event-grid-integration.md)
> di griglia di eventi di esempio con il servizio SignalR di Azure[Su Griglia di eventiTry](../event-grid/overview.md) a sample Event Grid integration with Azure SignalR Service About Event Grid
