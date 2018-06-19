---
title: Eventi personalizzati per Griglia di eventi di Azure con il portale di Azure | Microsoft Docs
description: Usare Griglia di eventi di Azure e PowerShell per pubblicare un argomento e sottoscrivere l'evento.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 06/04/2018
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: fa6a232c657d9d96943f241eb05221e42f0f599b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763341"
---
# <a name="create-and-route-custom-events-with-the-azure-portal-and-event-grid"></a>Creare e instradare eventi personalizzati con il portale di Azure e Griglia di eventi

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. In questo articolo viene usato il portale di Azure per creare un argomento personalizzato, sottoscrivere l'argomento e attivare l'evento per visualizzare il risultato. Si invia l'evento a una funzione di Azure che registra i dati dell'evento. Al termine, i dati dell'evento vengono inviati a un endpoint e registrati.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-custom-topic"></a>Creare un argomento personalizzato

Un argomento di Griglia di eventi fornisce un endpoint definito dall'utente in cui vengono pubblicati gli eventi. 

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Per creare un argomento personalizzato, selezionare **Crea una risorsa**. 

   ![Creare una risorsa](./media/custom-event-quickstart-portal/create-resource.png)

1. Cercare *Argomento di Griglia di eventi* e selezionare tale voce nelle opzioni disponibili.

   ![Cercare l'argomento di Griglia di eventi](./media/custom-event-quickstart-portal/search-event-grid.png)

1. Selezionare **Create**.

   ![Passaggi iniziali](./media/custom-event-quickstart-portal/select-create.png)

1. Specificare un nome univoco per l'argomento personalizzato. Il nome dell'argomento deve essere univoco perché è rappresentato da una voce DNS. Non usare il nome visualizzato nell'immagine. ma crearne uno personalizzato. Selezionare una delle [aree supportate](overview.md). Specificare un nome per il gruppo di risorse. Selezionare **Create**.

   ![Creare valori dell'argomento di Griglia di eventi](./media/custom-event-quickstart-portal/create-custom-topic.png)

1. Dopo che l'argomento personalizzato è stato creato, viene visualizzata una notifica dell'operazione riuscita.

   ![Visualizzare la notifica dell'operazione riuscita](./media/custom-event-quickstart-portal/success-notification.png)

   Se la distribuzione non è riuscita, individuare la causa dell'errore. Selezionare **La distribuzione non è riuscita**.

   ![Selezionare La distribuzione non è riuscita](./media/custom-event-quickstart-portal/select-failed.png)

   Selezionare il messaggio di errore.

   ![Selezionare La distribuzione non è riuscita](./media/custom-event-quickstart-portal/failed-details.png)

   Nella figura seguente viene illustrata una distribuzione che non è riuscita perché il nome dell'argomento personalizzato è già in uso. Se viene visualizzato questo errore, riprovare la distribuzione con un nome diverso.

   ![Conflitto di nomi](./media/custom-event-quickstart-portal/name-conflict.png)

## <a name="create-an-azure-function"></a>Creare una funzione di Azure

Prima di sottoscrivere l'argomento, creare l'endpoint per il messaggio dell'evento. In questo articolo Funzioni di Azure viene usato per creare un'app per le funzioni per l'endpoint.

1. Per creare una funzione, selezionare **Crea una risorsa**.

   ![Creare una risorsa](./media/custom-event-quickstart-portal/create-resource-small.png)

1. Selezionare **Calcolo** e **App per le funzioni**.

   ![Creare una funzione](./media/custom-event-quickstart-portal/create-function.png)

1. Specificare un nome univoco per la funzione di Azure. Non usare il nome visualizzato nell'immagine. Selezionare il gruppo di risorse creato in questo articolo. Per il piano di hosting, usare **Piano a consumo**. Usare il nuovo account di archiviazione suggerito. È possibile disattivare Application Insights. Dopo avere specificato i valori, selezionare **Crea**.

   ![Specificare i valori per la funzione](./media/custom-event-quickstart-portal/provide-function-values.png)

1. Al termine della distribuzione, selezionare **Vai alla risorsa**.

   ![Vai alla risorsa](./media/custom-event-quickstart-portal/go-to-resource.png)

1. Accanto a **Funzioni** selezionare **+**.

   ![Aggiungere una funzione](./media/custom-event-quickstart-portal/add-function.png)

1. Selezionare **Funzione personalizzata** tra le opzioni disponibili.

   ![Funzione personalizzata](./media/custom-event-quickstart-portal/select-custom-function.png)

1. Scorrere verso il basso fino a individuare **Trigger griglia di eventi**. Selezionare **C#**.

   ![Selezionare il trigger di Griglia di eventi](./media/custom-event-quickstart-portal/select-event-grid-trigger.png)

1. Accettare i valori predefiniti e selezionare **Crea**.

   ![Nuova funzione](./media/custom-event-quickstart-portal/new-function.png)

La funzione è ora pronta a ricevere gli eventi.

## <a name="subscribe-to-a-topic"></a>Sottoscrivere un argomento

Si sottoscrive un argomento per indicare a Griglia di eventi gli eventi di cui si vuole tenere traccia e dove inviare tali eventi.

1. Nella funzione di Azure selezionare **Aggiungi sottoscrizione di Griglia di eventi**.

   ![Aggiungere una sottoscrizione di Griglia di eventi](./media/custom-event-quickstart-portal/add-event-grid-subscription.png)

1. Fornire i valori per la sottoscrizione. Selezionare **Argomenti di Griglia di eventi** per il tipo di argomento. Per la sottoscrizione e il gruppo di risorse, selezionare la sottoscrizione e il gruppo di risorse in cui è stato creato l'argomento personalizzato. Ad esempio, selezionare il nome dell'argomento personalizzato. L'endpoint del sottoscrittore viene prepopolato con l'URL per la funzione.

   ![Fornire i valori della sottoscrizione](./media/custom-event-quickstart-portal/provide-subscription-values.png)

1. Prima di attivare l'evento, aprire i log per la funzione in modo da visualizzare i dati dell'evento quando viene inviato. Nella parte inferiore della funzione di Azure, selezionare **Log**.

   ![Selezionare i log](./media/custom-event-quickstart-portal/select-logs.png)

A questo punto, attivare un evento per vedere come la griglia di eventi distribuisce il messaggio nell'endpoint. Per semplificare questo articolo, viene usato Cloud Shell per inviare dati di esempio dell'evento all'argomento personalizzato. In genere, i dati dell'evento vengono inviati da un'applicazione o un servizio di Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="send-an-event-to-your-topic"></a>Inviare un evento all'argomento

Usare l'interfaccia della riga di comando di Azure o PowerShell per inviare un evento di test all'argomento personalizzato.

Il primo esempio usa l'interfaccia della riga di comando di Azure. Ottiene l'URL e la chiave per l'argomento e i dati dell'evento di esempio. Usare il nome dell'argomento per `<topic_name>`. Per visualizzare l'evento completo, usare `echo "$body"`. L'elemento `data` del JSON è il payload dell'evento. Questo campo accetta qualsiasi JSON ben formato. È anche possibile usare il campo oggetto per il filtro e il routing avanzato. CURL è un'utilità che invia richieste HTTP.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Il secondo esempio usa PowerShell per eseguire passaggi simili.

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName gridResourceGroup -Name <topic-name>

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

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

È stato attivato l'evento e Griglia di eventi ha inviato il messaggio all'endpoint configurato al momento della sottoscrizione. Analizzare i log per visualizzare i dati dell'evento.

![Visualizzare i log](./media/custom-event-quickstart-portal/view-log-entry.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare a usare questo evento, non è necessario pulire le risorse create con questo articolo. In caso contrario, eliminare le risorse create in questo articolo.

Selezionare il gruppo di risorse e quindi fare clic su **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare argomenti personalizzati e sottoscrizioni di eventi, è possibile approfondire le operazioni possibili con Griglia di eventi:

- [Informazioni sulla griglia di eventi](overview.md)
- [Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato (anteprima)](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorare le modifiche alla macchina virtuale con la griglia di eventi di Azure e le app per la logica](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md)
