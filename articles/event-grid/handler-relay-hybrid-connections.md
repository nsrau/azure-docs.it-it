---
title: Connessione ibrida di Inoltro di Azure come gestore eventi per gli eventi di Griglia di eventi di Azure
description: Descrive come usare connessioni ibride di Inoltro di Azure come gestori eventi di Griglia di eventi di Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3807e2d125d652b8f5ed7c9dec1b972d69f699f6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270203"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Connessione ibrida di Inoltro di Azure come gestore eventi per gli eventi di Griglia di eventi di Azure
Un gestore eventi è la posizione in cui l'evento viene inviato. Il gestore esegue altre azioni per elaborare l'evento. Per gestire gli eventi vengono configurati automaticamente diversi servizi di Azure, tra cui **Inoltro di Azure**. 

Usare **connessioni ibride di Inoltro di Azure** per inviare eventi alle applicazioni all'interno di una rete aziendale che non hanno un endpoint accessibile pubblicamente.

## <a name="tutorials"></a>Esercitazioni
Per un esempio relativo all'uso delle connessioni ibride di Inoltro di Azure come gestore eventi, vedere l'esercitazione seguente. 

|Titolo  |Descrizione  |
|---------|---------|
| [Esercitazione: Instradare eventi a Connessioni ibride](custom-event-to-hybrid-connection.md) | Invia un evento personalizzato a una connessione ibrida esistente per l'elaborazione da parte dell'applicazione listener. |

## <a name="rest-example-for-put"></a>Esempio REST (per PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

> [!NOTE]
> La distribuzione di eventi a una connessione ibrida di inoltro di Azure in **un altro tenant** non è supportata. 

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di gestori eventi supportati, vedere l'articolo [Gestori eventi](event-handlers.md). 