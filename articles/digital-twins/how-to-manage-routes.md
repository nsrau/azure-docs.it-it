---
title: Gestire endpoint e route
titleSuffix: Azure Digital Twins
description: Vedere come configurare e gestire gli endpoint e le route di eventi per i dati di Azure Digital gemelli.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bc22cf5a21709ccacafe068a60541cc9990d1131
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132263"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Gestire endpoint e route nei dispositivi gemelli digitali di Azure

Nei dispositivi gemelli digitali di Azure è possibile instradare le [notifiche degli eventi](how-to-interpret-event-data.md) ai servizi downstream o connettersi alle risorse di calcolo. Questa operazione viene eseguita impostando prima gli **endpoint** che possono ricevere gli eventi, seguiti dalle [**route degli eventi**](concepts-route-events.md) che specificano gli eventi generati dai gemelli digitali di Azure che vengono recapitati a quali endpoint.

I tipi di endpoint supportati sono:
* [Hub eventi](../event-hubs/event-hubs-about.md)
* [Griglia di eventi](../event-grid/overview.md)
* [Bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md)

Per altre informazioni sui diversi endpoint, vedere [*scegliere tra i servizi di messaggistica di Azure*](https://docs.microsoft.com/azure/event-grid/compare-messaging-services).

Gli endpoint e le route vengono gestiti con le [**API EventRoutes**](how-to-use-apis-sdks.md), [.NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)o l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md). Possono anche essere gestiti tramite il [portale di Azure](https://portal.azure.com).

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Creare un endpoint per i dispositivi gemelli digitali di Azure

Per collegare un endpoint ai dispositivi gemelli digitali di Azure, l'hub eventi, l'argomento della griglia di eventi o il bus di servizio usato per l'endpoint deve esistere già. 

L'esempio seguente illustra come creare un argomento di griglia di eventi usando l'interfaccia della riga di comando di Azure:

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Per restituire un elenco di nomi di aree di Azure che possono essere passati ai comandi nell'interfaccia della riga di comando di Azure, eseguire questo comando:
> ```azurecli
> az account list-locations -o table
> ```

Dopo aver creato l'argomento, è possibile collegarlo a dispositivi digitali gemelli di Azure con il comando seguente:

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

In questo modo, l'argomento della griglia di eventi è disponibile all'interno dei dispositivi gemelli digitali di Azure, con il nome specificato con l' `--endpoint-name` argomento. Questo nome viene in genere usato come destinazione di una **Route di eventi**, che verrà creata nella sezione successiva con l'API del servizio dispositivi digitali gemelli di Azure.

Sono presenti comandi equivalenti per gli endpoint di hub eventi e del bus di servizio:

* Aggiungere un endpoint dell'argomento del bus di servizio (richiede una risorsa del bus di servizio creata in precedenza)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Aggiungere un endpoint dell'hub eventi (richiede una risorsa di hub eventi creata in precedenza)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="manage-event-routes-with-apis"></a>Gestire le route degli eventi con le API

Per inviare effettivamente i dati dai dispositivi gemelli digitali di Azure a un endpoint, è necessario definire una route dell'evento. Le **API EventRoutes** di Azure Digital Twins consentono agli sviluppatori di collegare il flusso degli eventi, in tutto il sistema e ai servizi downstream. Per altre informazioni sulle route di eventi, vedere [*concetti relativi al routing di eventi gemelli digitali di Azure*](concepts-route-events.md).

È possibile procedere alla creazione di una route di eventi al termine della configurazione degli endpoint.

>[!NOTE]
>Se gli endpoint sono stati distribuiti di recente, verificare che la distribuzione sia terminata **prima** di provare a usarli per una nuova route di eventi. Se la distribuzione della route non riesce perché gli endpoint non sono pronti, attendere alcuni minuti e riprovare.
>
> Se si crea uno script per questo flusso, è possibile che si voglia tenere conto di questa situazione creando in 2-3 minuti di tempo di attesa per il completamento della distribuzione del servizio endpoint prima di passare alla configurazione del routing.

Gli esempi in questo articolo usano C# SDK.

Le route degli eventi vengono definite usando le API del piano dati. Una definizione di route può contenere gli elementi seguenti:
* ID della route che si vuole usare
* Nome dell'endpoint che si desidera utilizzare
* Filtro che definisce gli eventi che vengono inviati all'endpoint 

Se non è presente alcun ID di route, nessun messaggio viene instradato all'esterno dei dispositivi gemelli digitali di Azure. Se è presente un ID route e il filtro è `true` , tutti i messaggi vengono instradati all'endpoint. Se è presente un ID di route e viene aggiunto un filtro diverso, i messaggi verranno filtrati in base al filtro.

Una route deve consentire la selezione di più notifiche e tipi di evento. 

`CreateEventRoute`è la chiamata SDK utilizzata per aggiungere una route dell'evento. Di seguito è riportato un esempio di utilizzo:

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> Tutte le funzioni SDK sono disponibili in versioni sincrone e asincrone.

### <a name="event-route-sample-code"></a>Codice di esempio della route di eventi

Nell'esempio di codice seguente viene illustrato come creare, elencare ed eliminare una route di eventi:
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

### <a name="filter-events"></a>Filtrare gli eventi

Senza filtro, gli endpoint ricevono una serie di eventi da dispositivi gemelli digitali di Azure:
* Telemetria generata dai dispositivi [gemelli digitali](concepts-twins-graph.md) con l'API del servizio di dispositivi digitali gemelli di Azure
* Notifiche delle modifiche delle proprietà dei dispositivi gemelli, attivate per le modifiche delle proprietà per qualsiasi dispositivo gemello nell'istanza di Azure
* Eventi del ciclo di vita, generati quando vengono creati o eliminati gemelli o relazioni
* Eventi di modifica del modello, generati quando i [modelli](concepts-models.md) configurati in un'istanza di Azure Digital Twins vengono aggiunti o eliminati

È possibile limitare gli eventi inviati aggiungendo un filtro a un endpoint.

Per aggiungere un filtro, è possibile usare una richiesta PUT per *https://{YourHost}/EventRoutes/myNewRoute? API-Version = 2020-05 -31-Preview* con il corpo seguente:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Ecco i filtri di route supportati.

| Nome filtro | Descrizione | Schema filtro | Valori supportati | 
| --- | --- | --- | --- |
| Tipo | [Tipo di eventi](./concepts-route-events.md#types-of-event-messages) che passano attraverso l'istanza di dispositivi gemelli digitali | `"filter" : "type = '<eventType>'"` | `Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Source (Sorgente) | Nome dell'istanza di Azure Digital Twins | `"filter" : "source = '<hostname>'"`|  **Per le notifiche**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Per la telemetria**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Oggetto | Descrizione dell'evento nel contesto dell'origine evento precedente | `"filter": " subject = '<subject>'"` | **Per le notifiche**: l'oggetto è`<twinid>` <br> o un formato URI per gli oggetti, che sono identificati in modo univoco da più parti o ID:<br>`<twinid>/relationships/<relationshipid>`<br> **Per la telemetria**: l'oggetto è il percorso del componente (se i dati di telemetria vengono emessi da un componente gemello), ad esempio `comp1.comp2` . Se i dati di telemetria non vengono emessi da un componente, il relativo campo soggetto è vuoto. |
| Schema dei dati | ID modello DTDL | `"filter": "dataschema = 'dtmi:example:com:floor4;2'"` | **Per la telemetria**: lo schema di dati è l'ID modello del dispositivo gemello o il componente che genera i dati di telemetria <br>**Per le notifiche**: schema dati non supportato|
| Tipo di contenuto | Tipo di contenuto del valore dati | `"filter": "datacontenttype = '<contentType>'"` | `application/json` |
| Versione spec | Versione dello schema di eventi in uso | `"filter": "specversion = '<version>'"` | Deve essere `1.0`. Indica la versione dello schema CloudEvents 1,0 |
| True/false | Consente la creazione di una route senza filtro o la disabilitazione di una route | `"filter" : "<true/false>"` | `true`= Route abilitata senza filtro <br> `false`= la route è disabilitata |
<!--
| ID | *Not implemented for public preview* | "filter": "id = '…'" | |
| Schema | *Not implemented for public preview*  | "filter": "dataschema = '…'" | |
-->

È anche possibile combinare i filtri usando le operazioni seguenti:

| Nome filtro | Schema filtro | Esempio | 
| --- | --- | --- |
| E/O | `"filter": "<filter1> AND <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'"` |
| E/O | `"filter": "<filter1> OR <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'"` |
| Operazioni annidate | `"filter": "(<Comparison1>) AND (<Comparison2>)"` | `"filter": "(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')"` |

> [!NOTE]
> Durante l'anteprima, è supportata solo l'uguaglianza di stringhe (=,! =).

Quando si implementa o si aggiorna un filtro, la modifica potrebbe richiedere alcuni minuti per essere riflessa nella pipeline di dati.

## <a name="manage-endpoints-and-routes-with-cli"></a>Gestire endpoint e route con l'interfaccia della riga di comando

Gli endpoint e le route possono essere gestiti anche tramite l'interfaccia della riga di comando di Azure Digital gemelli. È possibile trovare i comandi in [*procedura: usare l'interfaccia della riga di comando di Azure Digital gemelli*](how-to-use-cli.md).

## <a name="monitor-event-routes"></a>Monitorare le route degli eventi

Le metriche di routing, ad esempio conteggio, latenza e percentuale di errori, possono essere visualizzate nel [portale di Azure](https://portal.azure.com/). 

Dalla Home page del portale, cercare l'istanza di Azure Digital gemelli per recuperare i dettagli. Selezionare l'opzione **metrica** dal menu dell'istanza di Azure Digital gemelli per visualizzare la pagina *metrica* .

:::image type="content" source="media/how-to-manage-routes/metrics.png" alt-text="Pagina metrica di un'istanza di dispositivi gemelli digitali di Azure nel portale di Azure":::

Da qui è possibile visualizzare le metriche per l'istanza e creare visualizzazioni personalizzate.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui diversi tipi di messaggi di evento che è possibile ricevere, vedere:
* [*Procedura: interpretare i dati degli eventi*](how-to-interpret-event-data.md)
