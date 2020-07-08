---
title: Funzione di Azure come gestore dell'evento per gli eventi di Griglia di eventi di Azure
description: Spiega in che modo usare le funzioni di Azure come gestori degli eventi di Griglia di eventi.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0152a9fc11562744f83ab9d20466a3dcc8e2e6e0
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800425"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Funzione di Azure come gestore dell'evento per gli eventi di Griglia di eventi

Un gestore eventi è la posizione in cui l'evento viene inviato. Il gestore esegue un'azione per elaborare l'evento. Diversi servizi di Azure vengono configurati automaticamente per gestire eventi, uno dei quali è **Funzioni di Azure**. 

Usare **Funzioni di Azure** in un'architettura serverless per rispondere agli eventi da Griglia di eventi. Quando si usa una funzione di Azure come gestore, l'uso del trigger della Griglia di eventi è consigliato rispetto al trigger HTTP generico. Griglia di eventi convalida automaticamente i trigger di Griglia di eventi. Con i trigger HTTP generici è necessario implementare autonomamente la [risposta di convalida](webhook-event-delivery.md).

Per altre informazioni, vedere [Trigger Griglia di eventi per Funzioni di Azure](../azure-functions/functions-bindings-event-grid.md) per una panoramica dell'uso del trigger di Griglia di eventi nelle funzioni.

## <a name="tutorials"></a>Esercitazioni

|Titolo  |Descrizione  |
|---------|---------|
| [Avvio rapido: Gestire eventi con la funzione](custom-event-to-function.md) | Invia un evento personalizzato a una funzione per l'elaborazione. |
| [Esercitazione: Automatizzare il ridimensionamento delle immagini caricate con Griglia di eventi](resize-images-on-storage-blob-upload-event.md) | Gli utenti caricano le immagini tramite l'app Web nell'account di archiviazione. Quando viene creato un BLOB di archiviazione, Griglia di eventi invia un evento all'app per le funzioni, che ridimensiona l'immagine caricata. |
| [Esercitazione: trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md) | Quando Hub eventi crea un file di Acquisizione, Griglia di eventi invia un evento a un'app per le funzioni. L'app recupera il file di Acquisizione ed esegue la migrazione dei dati a un data warehouse. |
| [Esercitazione: Esempi dell'integrazione del bus di servizio di Azure in Griglia di eventi di Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Griglia di eventi invia i messaggi dall'argomento del bus di servizio a un'app per le funzioni e a un'app per la logica. |

## <a name="rest-example-for-put"></a>Esempio REST (per PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di gestori eventi supportati, vedere l'articolo [Gestori eventi](event-handlers.md). 
