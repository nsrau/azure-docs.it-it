---
title: Gestire endpoint e Route (API e CLI)
titleSuffix: Azure Digital Twins
description: Vedere come configurare e gestire gli endpoint e le route di eventi per i dati di Azure Digital gemelli.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7016abc9d52aa12b497d29f605fe351ee3f6a2dd
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96519114"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Gestire endpoint e route nei dispositivi gemelli digitali di Azure (API e CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Nei dispositivi gemelli digitali di Azure è possibile instradare le [notifiche degli eventi](how-to-interpret-event-data.md) ai servizi downstream o alle risorse di calcolo connesse. Questa operazione viene eseguita impostando innanzitutto gli **endpoint** che possono ricevere gli eventi. È quindi possibile creare  [**Route di eventi**](concepts-route-events.md) che specificano gli eventi generati dai dispositivi gemelli digitali di Azure che vengono recapitati a quali endpoint.

Questo articolo illustra il processo di creazione di endpoint e route con le [API route di eventi](/rest/api/digital-twins/dataplane/eventroutes), [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)e l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md).

In alternativa, è anche possibile gestire gli endpoint e le route con la [portale di Azure](https://portal.azure.com). Per una versione di questo articolo che usa invece il portale, vedere [*How-to: Manage Endpoints and routes (Portal)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Prerequisiti

* È necessario un **account Azure** (è possibile impostarne uno gratuitamente [qui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* È necessaria un' **istanza di Azure Digital Twins** nella sottoscrizione di Azure. Se non si dispone già di un'istanza, è possibile crearne una usando la procedura descritta in [*procedura: configurare un'istanza e l'autenticazione*](how-to-set-up-instance-cli.md). Per usare più avanti in questo articolo, è possibile usare i valori seguenti del programma di installazione:
    - Nome istanza
    - Gruppo di risorse
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Creare un endpoint per i dispositivi gemelli digitali di Azure

Questi sono i tipi di endpoint supportati che è possibile creare per l'istanza:
* [Griglia di eventi](../event-grid/overview.md)
* [Hub eventi](../event-hubs/event-hubs-about.md)
* [Bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md)

Per altre informazioni sui diversi tipi di endpoint, vedere [*scegliere tra i servizi di messaggistica di Azure*](../event-grid/compare-messaging-services.md).

Per collegare un endpoint ai dispositivi gemelli digitali di Azure, è necessario che l'argomento della griglia di eventi, l'hub eventi o il bus di servizio usato per l'endpoint sia già esistente. 

### <a name="create-an-event-grid-endpoint"></a>Creare un endpoint di griglia di eventi

L'esempio seguente illustra come creare un endpoint di tipo griglia di eventi usando l'interfaccia della riga di comando di Azure. È possibile usare [Azure cloud Shell](https://shell.azure.com)o [installare l'interfaccia della riga di](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)comando in locale.

Per prima cosa, creare un argomento di griglia di eventi. È possibile usare il comando seguente o visualizzare i passaggi in modo più dettagliato visitando [la sezione *creare un argomento personalizzato*](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) della Guida introduttiva *agli eventi personalizzati* di griglia di eventi.

```azurecli-interactive
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Per restituire un elenco di nomi di aree di Azure che possono essere passati ai comandi nell'interfaccia della riga di comando di Azure, eseguire questo comando:
> ```azurecli-interactive
> az account list-locations -o table
> ```

Dopo aver creato l'argomento, è possibile collegarlo ai dispositivi gemelli digitali di Azure con il [comando dell'interfaccia della riga di comando di Azure Digital gemello](how-to-use-cli.md)seguente:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

A questo punto, l'argomento di griglia di eventi è disponibile come endpoint all'interno di Azure Digital gemelli, sotto il nome specificato con l' `--endpoint-name` argomento. Questo nome viene in genere usato come destinazione di una **Route di eventi**, che verrà creata [più avanti in questo articolo](#create-an-event-route) usando l'API del servizio dispositivi digitali gemelli di Azure.

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Creare un hub eventi o un endpoint del bus di servizio

Il processo per la creazione di hub eventi o endpoint del bus di servizio è simile al processo di griglia di eventi illustrato in precedenza.

Prima di tutto, creare le risorse che verranno usate come endpoint. Ecco cosa è necessario:
* Bus di servizio: _spazio dei nomi del bus_ di servizio, _argomento del bus di servizio_, regola di _autorizzazione_
* Hub eventi: _spazio dei nomi di hub eventi_, _Hub eventi_, _regola di autorizzazione_

Quindi, usare i comandi seguenti per creare gli endpoint nei dispositivi gemelli digitali di Azure: 

* Aggiungere un endpoint dell'argomento del bus di servizio (richiede una risorsa del bus di servizio creata in precedenza)
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Aggiungere l'endpoint di hub eventi (richiede una risorsa di hub eventi creata in precedenza)
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Creazione di un endpoint con messaggi non recapitabili

Quando un endpoint non è in grado di recapitare un evento entro un determinato periodo di tempo o dopo il tentativo di recapitare l'evento un certo numero di volte, può inviare l'evento non recapitato a un account di archiviazione. Questo processo è noto come **messaggio non recapitabile**.

Per ulteriori informazioni sui messaggi non recapitabili, vedere [*concetti: route degli eventi*](concepts-route-events.md#dead-letter-events). Per istruzioni su come configurare un endpoint con messaggi non recapitabili, continuare con la parte restante di questa sezione.

#### <a name="set-up-storage-resources"></a>Configurare le risorse di archiviazione

Prima di impostare il percorso dei messaggi non recapitabili, è necessario avere un [account di archiviazione](../storage/common/storage-account-create.md?tabs=azure-portal) con un [contenitore](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) configurato nell'account Azure. 

Si fornirà l'URL per questo contenitore durante la creazione dell'endpoint in un secondo momento. La posizione dei messaggi non recapitabili verrà fornita all'endpoint come URL del contenitore con un [token](../storage/common/storage-sas-overview.md)di firma di accesso condiviso. Il token necessita `write` dell'autorizzazione per il contenitore di destinazione nell'account di archiviazione. L'URL con formato completo sarà nel formato: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>` .

Attenersi alla procedura seguente per configurare queste risorse di archiviazione nell'account di Azure, per preparare la configurazione della connessione dell'endpoint nella sezione successiva.

1. Seguire i passaggi descritti in [*creare un account di archiviazione*](../storage/common/storage-account-create.md?tabs=azure-portal) per creare un **account di archiviazione** nella sottoscrizione di Azure. Prendere nota del nome dell'account di archiviazione per usarlo in un secondo momento.
2. Seguire i passaggi descritti in [*creare un contenitore*](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) per creare un **contenitore** all'interno del nuovo account di archiviazione. Prendere nota del nome del contenitore per usarlo in un secondo momento.
3. Successivamente, creare un **token** di firma di accesso condiviso per l'account di archiviazione che l'endpoint può usare per accedervi. Per iniziare, passare all'account di archiviazione nel [portale di Azure](https://ms.portal.azure.com/#home) (è possibile trovarlo in base al nome con la barra di ricerca del portale).
4. Nella pagina account di archiviazione scegliere il collegamento _firma di accesso condiviso_ nella barra di spostamento a sinistra per avviare la configurazione del token SAS.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="Pagina account di archiviazione nell'portale di Azure" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. Nella *pagina firma di accesso condiviso*, in *Servizi consentiti* e *tipi di risorse consentiti*, selezionare tutte le impostazioni desiderate. È necessario selezionare almeno una casella in ogni categoria. In *autorizzazioni consentite* scegliere **scrittura** (è anche possibile selezionare altre autorizzazioni se lo si desidera).
1. Impostare tutti i valori desiderati per le impostazioni rimanenti.
1. Al termine, selezionare il pulsante _genera SAS e stringa di connessione_ per generare il token SAS. 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="Pagina account di archiviazione nell'portale di Azure che Mostra tutte le impostazioni selezionate per generare un token di firma di accesso condiviso ed evidenziando il pulsante &quot;genera SAS e stringa di connessione&quot;" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. Questa operazione genererà diversi valori della stringa di connessione e di firma di accesso condiviso nella parte inferiore della stessa pagina, sotto le selezioni delle impostazioni. Scorrere verso il basso per visualizzare i valori e usare l'icona *copia negli Appunti* per copiare il valore del **token SAS** . Salvarlo per usarlo in seguito.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="Copiare il token SAS da usare nel segreto dei messaggi non recapitabili." lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="configure-the-endpoint"></a>Configurare l'endpoint

Per creare un endpoint con messaggi non recapitabili abilitati, è necessario creare l'endpoint usando le API Azure Resource Manager. 

1. Per prima cosa, usare la [documentazione delle api Azure Resource Manager](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) per configurare una richiesta di creazione di un endpoint e compilare i parametri richiesti della richiesta. 

1. Successivamente, aggiungere un `deadLetterSecret` campo all'oggetto Properties nel **corpo** della richiesta. Impostare questo valore in base al modello riportato di seguito, che esegue l'artigianato di un URL in base al nome dell'account di archiviazione, al nome del contenitore e al valore del token di firma di accesso condiviso raccolti nella [sezione precedente](#set-up-storage-resources).
      
    ```json
    {
      "properties": {
        "endpointType": "EventGrid",
        "TopicEndpoint": "https://contosoGrid.westus2-1.eventgrid.azure.net/api/events",
        "accessKey1": "xxxxxxxxxxx",
        "accessKey2": "xxxxxxxxxxx",
        "deadLetterSecret":"https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>"
      }
    }
    ```
1. Inviare la richiesta per creare l'endpoint.

Per altre informazioni sulla strutturazione di questa richiesta, vedere la documentazione dell'API REST di Azure Digital gemelli: [endpoints-DigitalTwinsEndpoint CreateOrUpdate](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate).

### <a name="message-storage-schema"></a>Schema di archiviazione messaggi

Una volta configurato l'endpoint con i messaggi non recapitabili, i messaggi non recapitabili verranno archiviati nel formato seguente nell'account di archiviazione:

`{container}/{endpointName}/{year}/{month}/{day}/{hour}/{eventId}.json`

I messaggi non recapitabili corrisponderanno allo schema dell'evento originale destinato a essere recapitato all'endpoint originale.

Di seguito è riportato un esempio di messaggio non recapitabile per una notifica di creazione di un dispositivo [gemello](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications):

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<yourInstance>.api.<yourregion>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Creare una route di eventi

Per inviare effettivamente i dati dai dispositivi gemelli digitali di Azure a un endpoint, è necessario definire una **route dell'evento**. Le **API EventRoutes** di Azure Digital Twins consentono agli sviluppatori di collegare il flusso degli eventi, in tutto il sistema e ai servizi downstream. Per altre informazioni sulle route di eventi, vedere [*concetti relativi al routing di eventi gemelli digitali di Azure*](concepts-route-events.md).

Gli esempi in questa sezione usano [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).

**Prerequisito**: è necessario creare endpoint come descritto in precedenza in questo articolo prima di poter passare alla creazione di una route. È possibile procedere alla creazione di una route di eventi al termine della configurazione degli endpoint.

> [!NOTE]
> Se gli endpoint sono stati distribuiti di recente, verificare che la distribuzione sia terminata **prima** di provare a usarli per una nuova route di eventi. Se la distribuzione della route non riesce perché gli endpoint non sono pronti, attendere alcuni minuti e riprovare.
>
> Se si crea uno script per questo flusso, è possibile che si voglia tenere conto di questa situazione creando in 2-3 minuti di tempo di attesa per il completamento della distribuzione del servizio endpoint prima di passare alla configurazione del routing.

### <a name="creation-code-with-apis-and-the-c-sdk"></a>Creazione di codice con le API e C# SDK

Le route degli eventi vengono definite usando le [API del piano dati](how-to-use-apis-sdks.md#overview-data-plane-apis). 

Una definizione di route può contenere gli elementi seguenti:
* Nome della route che si vuole usare
* Nome dell'endpoint che si desidera utilizzare
* Filtro che definisce gli eventi che vengono inviati all'endpoint 

Se non è presente alcun nome di route, nessun messaggio viene instradato all'esterno dei dispositivi gemelli digitali di Azure. Se è presente un nome di route e il filtro è `true` , tutti i messaggi vengono instradati all'endpoint. Se è presente un nome di route e viene aggiunto un filtro diverso, i messaggi verranno filtrati in base al filtro.

Una route deve consentire la selezione di più notifiche e tipi di evento. 

`CreateOrReplaceEventRouteAsync` è la chiamata SDK utilizzata per aggiungere una route dell'evento. Di seguito è riportato un esempio di utilizzo:

```csharp
string eventFilter = "$eventType = 'DigitalTwinTelemetryMessages' or $eventType = 'DigitalTwinLifecycleNotification'";
var er = new DigitalTwinsEventRoute("<your-endpointName>", eventFilter);
await client.CreateOrReplaceEventRouteAsync("routeName", er);
```
    
> [!TIP]
> Tutte le funzioni SDK sono disponibili in versioni sincrone e asincrone.

### <a name="event-route-sample-code"></a>Codice di esempio della route di eventi

Il metodo di esempio seguente mostra come creare, elencare ed eliminare una route di eventi:
```csharp
private async static Task CreateEventRoute(DigitalTwinsClient client, String routeName, DigitalTwinsEventRoute er)
{
  try
  {
    Console.WriteLine("Create a route: testRoute1");
            
    // Make a filter that passes everything
    er.Filter = "true";
    await client.CreateOrReplaceEventRouteAsync(routeName, er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable<DigitalTwinsEventRoute> result = client.GetEventRoutes();
    foreach (DigitalTwinsEventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointName} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (DigitalTwinsEventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
  }
    catch (RequestFailedException e)
    {
        Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
    }
  }
```

## <a name="filter-events"></a>Filtrare gli eventi

Senza filtro, gli endpoint ricevono una serie di eventi da dispositivi gemelli digitali di Azure:
* Telemetria generata dai dispositivi [gemelli digitali](concepts-twins-graph.md) con l'API del servizio di dispositivi digitali gemelli di Azure
* Notifiche delle modifiche delle proprietà dei dispositivi gemelli, attivate per le modifiche delle proprietà per qualsiasi dispositivo gemello nell'istanza di Azure
* Eventi del ciclo di vita, generati quando vengono creati o eliminati gemelli o relazioni

È possibile limitare gli eventi inviati aggiungendo un **filtro** per un endpoint alla route dell'evento.

Per aggiungere un filtro, è possibile usare una richiesta PUT su *https://{Your-Azure-Digital-gemells-hostname}/eventRoutes/{Event-route-name}? API-Version = 2020-10-31* con il corpo seguente:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 
Ecco i filtri di route supportati. Usare i dettagli nella colonna *Filtra schema testo* per sostituire il `<filter-text>` segnaposto nel corpo della richiesta precedente.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

## <a name="manage-endpoints-and-routes-with-cli"></a>Gestire endpoint e route con l'interfaccia della riga di comando

Gli endpoint e le route possono essere gestiti anche tramite l'interfaccia della riga di comando di Azure Digital gemelli. Per altre informazioni sull'uso dell'interfaccia della riga di comando e sui comandi disponibili, vedere [*procedura: usare l'interfaccia della riga di comando di Azure Digital gemelli*](how-to-use-cli.md).

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui diversi tipi di messaggi di evento che è possibile ricevere, vedere:
* [*Procedura: interpretare i dati degli eventi*](how-to-interpret-event-data.md)
