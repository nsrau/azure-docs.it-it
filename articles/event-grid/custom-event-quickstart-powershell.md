---
title: Eventi personalizzati per Griglia di eventi di Azure con PowerShell | Microsoft Docs
description: Usare Griglia di eventi di Azure e PowerShell per pubblicare un argomento e sottoscrivere l'evento.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 03/20/2018
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: 183bafad9b38ccb0e7eaae222c5569e45b15ac21
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="create-and-route-custom-events-with-azure-powershell-and-event-grid"></a>Creare e instradare eventi personalizzati con Azure PowerShell e Griglia di eventi

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. In questo articolo viene usato Azure PowerShell per creare un argomento personalizzato, sottoscrivere l'argomento e attivare l'evento per visualizzare il risultato. In genere, si inviano eventi a un endpoint che risponde all'evento, ad esempio un webhook o una funzione di Azure. Per maggiore semplicità, tuttavia, in questo articolo gli eventi vengono inviati a un URL che si limita a raccoglie i messaggi. È possibile creare questo URL mediante uno strumento di terze parti da [Hookbin](https://hookbin.com/).

>[!NOTE]
>**Hookbin** non è destinato all'utilizzo con velocità effettiva elevata. Questo strumento viene usato esclusivamente per scopi dimostrativi. Se si esegue il push di più di un evento alla volta, è possibile che non vengano visualizzati tutti gli eventi nello strumento.

Al termine, i dati dell'evento vengono inviati a un endpoint.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Ai fini di questo articolo, è necessario eseguire la versione più recente di Azure PowerShell. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Gli argomenti della griglia di eventi sono risorse di Azure e devono essere inseriti in un gruppo di risorse di Azure. Un gruppo di risorse è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

L'esempio seguente crea un gruppo di risorse denominato *gridResourceGroup* nella località *westus2*.

```powershell
New-AzureRmResourceGroup -Name gridResourceGroup -Location westus2
```

## <a name="create-a-custom-topic"></a>Creare un argomento personalizzato

Un argomento di Griglia di eventi fornisce un endpoint definito dall'utente in cui vengono pubblicati gli eventi. L'esempio seguente crea l'argomento personalizzato nel gruppo di risorse. Sostituire `<topic_name>` con un nome univoco per l'argomento. Il nome dell'argomento deve essere univoco perché è rappresentato da una voce DNS.

```powershell
New-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Location westus2 -Name <topic_name>
```

## <a name="create-a-message-endpoint"></a>Creare un endpoint del messaggio

Prima di sottoscrivere l'argomento personalizzato, creare l'endpoint per il messaggio dell'evento. Anziché scrivere codice per rispondere all'evento, creare un endpoint che raccoglie i messaggi per poterli visualizzare. Hookbin è uno strumento di terze parti che consente di creare un endpoint e visualizza le richieste che gli vengono inviate. Passare a [Hookbin](https://hookbin.com/) e fare clic su **Create New Endpoint** (Crea nuovo endpoint).  Copiare l'URL del contenitore, necessario per sottoscrivere l'argomento.

## <a name="subscribe-to-a-topic"></a>Sottoscrivere un argomento

Si sottoscrive un argomento per indicare alla griglia di eventi gli eventi di cui si vuole tenere traccia. L'esempio seguente sottoscrive l'argomento personalizzato creato e passa l'URL da Hookbin come endpoint per la notifica dell'evento. Sostituire `<event_subscription_name>` con un nome univoco per la sottoscrizione e `<endpoint_URL>` con il valore preso dalla sezione precedente. Se durante la sottoscrizione si specifica un endpoint, la griglia di eventi gestisce il routing degli eventi verso tale endpoint. Per `<topic_name>`, usare il valore creato in precedenza.

```powershell
New-AzureRmEventGridSubscription -EventSubscriptionName <event_subscription_name> -Endpoint <endpoint_URL> -ResourceGroupName gridResourceGroup -TopicName <topic_name>
```

## <a name="send-an-event-to-your-topic"></a>Inviare un evento all'argomento

Attivare un evento per vedere come la Griglia di eventi distribuisce il messaggio nell'endpoint. Ottenere prima di tutto l'URL e la chiave per l'argomento. Usare ancora una volta il nome dell'argomento per `<topic_name>`.

```powershell
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName gridResourceGroup -Name <topic-name>
```

Per semplificare questo articolo, configurare dati di esempio dell'evento da inviare all'argomento personalizzato. In genere, i dati dell'evento vengono inviati da un'applicazione o un servizio di Azure. L'esempio seguente usa HashTable per creare i dati dell'evento `htbody` e quindi li converte in un oggetto `$body` del payload JSON ben formato:

```powershell
$eventID = Get-Random 99999

#Date format should be SortableDateTimePattern (ISO 8601)
$eventDate = Get-Date -Format s

#Construct body using Hashtable
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/motorcycles"
    eventTime= $eventDate   
    data= @{
        make="Ducati"
        model="Monster"
    }
    dataVersion="1.0"
}

#Use ConvertTo-Json to convert event body from Hashtable to JSON Object
#Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
$body = "["+(ConvertTo-Json $htbody)+"]"
```

Visualizzando `$body`, è possibile visualizzare l'evento completo. L'elemento `data` del JSON è il payload dell'evento. Questo campo accetta qualsiasi JSON ben formato. È anche possibile usare il campo oggetto per il filtro e il routing avanzato.

Inviare ora un evento all'argomento.

```powershell
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

È stato attivato l'evento e Griglia di eventi ha inviato il messaggio all'endpoint configurato al momento della sottoscrizione. Passare all'URL dell'endpoint creato in precedenza. In alternativa, fare clic su Aggiorna nel browser aperto. Verrà visualizzato l'evento appena inviato.

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2018-01-25T15:58:13",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare a usare questo evento, non è necessario pulire le risorse create con questo articolo. In caso contrario, usare il comando seguente per eliminare le risorse create con questo articolo.

```powershell
Remove-AzureRmResourceGroup -Name gridResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare argomenti e sottoscrizioni di eventi, è possibile approfondire le operazioni possibili con la griglia di eventi:

- [Informazioni sulla griglia di eventi](overview.md)
- [Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato (anteprima)](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorare le modifiche alla macchina virtuale con la griglia di eventi di Azure e le app per la logica](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md)
