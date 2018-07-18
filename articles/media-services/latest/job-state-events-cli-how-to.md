---
title: Instradare gli eventi di Servizi multimediali di Azure verso un endpoint Web personalizzato | Microsoft Docs
description: Usare Griglia di eventi di Azure per sottoscrivere eventi di modifica dello stato dei processi di Servizi multimediali.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: e9df0cd24ef890765b78c25a073d671889be10a7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723743"
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>Instradare gli eventi di Servizi multimediali di Azure verso un endpoint Web personalizzato tramite l'interfaccia della riga di comando

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per sottoscrivere eventi di modifica dello stato dei processi di Servizi multimediali e attivare un evento per visualizzarne il risultato. 

In genere, si inviano eventi a un endpoint che risponde all'evento, ad esempio un webhook o una funzione di Azure. Questa esercitazione illustra come creare e impostare un webhook.

Al termine della procedura descritta in questo articolo, si potrà notare che i dati dell'evento sono stati inviati a un endpoint.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](http://portal.azure.com) e avviare **CloudShell** per eseguire i comandi dell'interfaccia della riga di comando, come illustrato nei passaggi successivi.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessaria l'interfaccia della riga di comando di Azure 2.0 o versione successiva. Eseguire `az --version` per trovare la versione in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

Assicurarsi di ricordare i valori usati per il nome dell'account di Servizi multimediali, il nome di archiviazione e il nome della risorsa.

## <a name="enable-event-grid-resource-provider"></a>Abilitare il provider di risorse di Griglia di eventi

Prima di tutto è necessario verificare che nella sottoscrizione sia abilitato un provider di risorse di Griglia di eventi. 

Nel portale di **Azure** eseguire questa procedura:

1. Passare a Sottoscrizioni.
2. Selezionare la propria sottoscrizione.
3. In Impostazioni selezionare Provider di risorse.
4. Cercare "EventGrid".
5. Verificare che il servizio Griglia di eventi sia registrato. In caso contrario, fare clic sul pulsante **Registra**.  

## <a name="create-a-generic-azure-function-webhook"></a>Creare un webhook generico di Funzione di Azure 

### <a name="create-a-message-endpoint"></a>Creare un endpoint del messaggio

Prima di sottoscrivere l'articolo di Griglia di eventi, creare un endpoint in cui vengono raccolti i messaggi per la visualizzazione.

Creare una funzione attivata da un webhook generico, come descritto nell'articolo su un [webhook generico](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function). In questa esercitazione si usa il codice **C#**.

Dopo aver creato il webhook, copiare l'URL facendo clic sul collegamento *Recupera URL della funzione* nella parte superiore della finestra del portale di **Azure**. Non è necessario includere l'ultima parte dell'URL (*&clientID=default*).

![Creare un webhook](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>Convalidare il webhook

Quando si registra l'endpoint del webhook con Griglia di eventi, viene inviata una richiesta POST con un semplice codice di convalida per dimostrare la proprietà dell'endpoint. È necessario che l'app risponda rimandando il codice di convalida. Griglia di eventi non recapita gli eventi agli endpoint di webhook che non hanno superato la convalida. Per altre informazioni, vedere [Event Grid security and authentication](https://docs.microsoft.com/azure/event-grid/security-authentication) (Sicurezza e autenticazione di Griglia di eventi). Questa sezione definisce due parti che è necessario definire per superare la convalida.

#### <a name="update-the-source-code"></a>Aggiornare il codice sorgente

Dopo la creazione del webhook, il file **run.csx** viene visualizzato nel browser. Sostituire il codice predefinito con il codice seguente. 

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"Webhook was triggered!");

    string jsonContent = await req.Content.ReadAsStringAsync();
    string eventGridValidation = 
        req.Headers.FirstOrDefault( x => x.Key == "Aeg-Event-Type" ).Value?.FirstOrDefault();

    dynamic eventData = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"event: {eventData}");

    if (eventGridValidation != String.Empty)
    {
        if (eventData[0].data.validationCode !=String.Empty && eventData[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent")
        {
            return req.CreateResponse(HttpStatusCode.OK, new 
            {
                validationResponse = eventData[0].data.validationCode
            });
        }
    }
    
    log.Info(jsonContent);

    return req.CreateResponse(HttpStatusCode.OK);
}
```

#### <a name="update-test-request-body"></a>Aggiornare il corpo della richiesta di test

Nella parte destra della finestra del portale di **Azure** vengono visualizzate due schede: **Visualizza file** e **Test**. Selezionare la scheda **Test**. In **Corpo della richiesta** incollare il codice json seguente. È possibile incollarlo così com'è, senza dover modificare alcun valore.

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}
]
```

Scegliere **Salva ed esegui** nella parte superiore della finestra.

![Corpo della richiesta](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>Registrarsi per la sottoscrizione a Griglia di eventi 

Si esegue la sottoscrizione a un articolo per indicare a Griglia di eventi gli eventi di cui tenere traccia. L'esempio seguente sottoscrive l'account di Servizi multimediali creato e passa l'URL del webhook di Funzione di Azure creato come endpoint per la notifica degli eventi. 

Sostituire `<event_subscription_name>` con un nome univoco per la sottoscrizione di eventi. Per `<resource_group_name>` e `<ams_account_name>` usare i valori creati in precedenza.  Per `<endpoint_URL>` incollare l'URL dell'endpoint. Rimuovere *&clientID=default* dall'URL. Se durante la sottoscrizione si specifica un endpoint, la griglia di eventi gestisce il routing degli eventi verso tale endpoint. 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

Il valore ID della risorsa dell'account di Servizi multimediali è simile al seguente:

/subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amstestaccount

## <a name="test-the-events"></a>Testare gli eventi

Eseguire un processo di codifica, ad esempio come quello descritto nella guida introduttiva [Eseguire lo streaming di file video](stream-files-dotnet-quickstart.md).

È stato attivato l'evento e la griglia di eventi ha inviato il messaggio all'endpoint configurato al momento della sottoscrizione. Selezionare il webhook creato in precedenza. Fare clic su **Monitoraggio** e **Aggiorna**. Verranno visualizzati gli eventi di modifica dello stato del processo: "Queued", "Scheduled", "Processing", "Finished", "Error", "Canceled", "Canceling".  Per altre informazioni, vedere [Schemi di eventi di Servizi multimediali](media-services-event-schemas.md).

Ad esempio: 

```json
[{
  "topic": "/subscriptions/<subscription id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job id>",
  "eventType": "Microsoft.Media.JobStateChange",
  "eventTime": "2018-04-20T21:17:26.2534881",
  "id": "<id>",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

![Testare gli eventi](./media/job-state-events-cli-how-to/test_events.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare a usare questo account di archiviazione e questa sottoscrizione di eventi, non è necessario pulire le risorse create in questo articolo. Se non si intende continuare, usare il comando seguente per eliminare le risorse create con questo articolo.

Sostituire `<resource_group_name>` con il gruppo di risorse creato in precedenza.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Passaggi successivi

[Reazione agli eventi](reacting-to-media-services-events.md)

## <a name="see-also"></a>Vedere anche 

[Interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
