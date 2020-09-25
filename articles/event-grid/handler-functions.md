---
title: Funzione di Azure come gestore dell'evento per gli eventi di Griglia di eventi di Azure
description: Spiega in che modo usare le funzioni di Azure come gestori degli eventi di Griglia di eventi.
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 87aeb78729dcc7bec9f193fab389e5c0952e63d5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270321"
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
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>Abilita batch
Per una velocità effettiva più elevata, abilitare la suddivisione in batch nella sottoscrizione. Se si usa il portale di Azure, è possibile impostare il numero massimo di eventi per batch e le dimensioni del batch preferite, in chili byte, al momento della creazione di una sottoscrizione o dopo la creazione. 

È possibile configurare le impostazioni di batch usando il portale di Azure, PowerShell, l'interfaccia della riga di comando o il modello di Gestione risorse. 

### <a name="azure-portal"></a>Portale di Azure
Al momento della creazione di una sottoscrizione nell'interfaccia utente, nella pagina **Crea sottoscrizione evento** passare alla scheda **funzionalità avanzate** e impostare i valori per **numero massimo di eventi per batch** e **dimensioni batch preferite, in kilobyte**. 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Abilitare la suddivisione in batch al momento della creazione di una sottoscrizione":::

È possibile aggiornare questi valori per una sottoscrizione esistente nella scheda **funzionalità** della pagina dell' **argomento griglia di eventi** . 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="Abilita batch dopo la creazione":::

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager
È possibile impostare **maxEventsPerBatch** e **preferredBatchSizeInKilobytes** in un modello di Azure Resource Manager. Per ulteriori informazioni, vedere il [riferimento al modello eventSubscriptions di Microsoft. EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/eventsubscriptions).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile usare il comando [AZ eventgrid Event-Subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create&preserve-view=true) o [AZ eventgrid Event-Subscription Update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update&preserve-view=true) per configurare le impostazioni relative a batch usando i parametri seguenti: `--max-events-per-batch` o `--preferred-batch-size-in-kilobytes` .

### <a name="azure-powershell"></a>Azure PowerShell
È possibile usare il cmdlet [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) o [Update-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/update-azeventgridsubscription) per configurare le impostazioni relative al batch usando i parametri seguenti: `-MaxEventsPerBatch` o `-PreferredBatchSizeInKiloBytes` .

> [!NOTE]
> La distribuzione di eventi a una funzione di Azure in **un altro tenant** non è supportata. 

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di gestori eventi supportati, vedere l'articolo [Gestori eventi](event-handlers.md). 
