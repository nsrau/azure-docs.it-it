---
title: Gestire endpoint e Route (API e CLI)
titleSuffix: Azure Digital Twins
description: Vedere come configurare e gestire gli endpoint e le route di eventi per i dati di Azure Digital gemelli.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 27b745353521a44733c46170a5f5952c194c2343
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89293507"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Gestire endpoint e route nei dispositivi gemelli digitali di Azure (API e CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Nei dispositivi gemelli digitali di Azure è possibile instradare le [notifiche degli eventi](how-to-interpret-event-data.md) ai servizi downstream o alle risorse di calcolo connesse. Questa operazione viene eseguita impostando innanzitutto gli **endpoint** che possono ricevere gli eventi. È quindi possibile creare  [**Route di eventi**](concepts-route-events.md) che specificano gli eventi generati dai dispositivi gemelli digitali di Azure che vengono recapitati a quali endpoint.

Gli endpoint e le route possono essere gestiti con le [API EventRoutes](how-to-use-apis-sdks.md), [.NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)o l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md). Questo articolo illustra il processo di creazione di endpoint e route attraverso questi meccanismi.

Possono anche essere gestiti tramite il [portale di Azure](https://portal.azure.com). Per una versione di questo articolo che usa invece il portale, vedere [*How-to: Manage Endpoints and routes (Portal)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Prerequisiti

* È necessario un **account Azure** (è possibile impostarne uno gratuitamente [qui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* È necessaria un' **istanza di Azure Digital Twins** nella sottoscrizione di Azure. Se non si dispone già di un'istanza, è possibile crearne una usando la procedura descritta in [*procedura: configurare un'istanza e l'autenticazione*](how-to-set-up-instance-scripted.md). Per usare più avanti in questo articolo, è possibile usare i valori seguenti del programma di installazione:
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

L'esempio seguente illustra come creare un endpoint di tipo griglia di eventi usando l'interfaccia della riga di comando di Azure. È possibile usare [Azure cloud Shell](https://shell.azure.com)o [installare l'interfaccia della riga di](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)comando in locale.

Per prima cosa, creare un argomento di griglia di eventi. È possibile usare il comando seguente o visualizzare i passaggi in modo più dettagliato visitando [la sezione *creare un argomento personalizzato* ](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) della Guida introduttiva *agli eventi personalizzati* di griglia di eventi.

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

A questo punto, l'argomento di griglia di eventi è disponibile come endpoint all'interno di Azure Digital gemelli, sotto il nome specificato con l' `--endpoint-name` argomento. Questo nome viene in genere usato come destinazione di una **Route di eventi**, che verrà creata [più avanti in questo articolo](#event-routes-with-apis-and-the-c-sdk) usando l'API del servizio dispositivi digitali gemelli di Azure.

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Creare un hub eventi o un endpoint del bus di servizio

Il processo per la creazione di hub eventi o endpoint del bus di servizio è simile al processo di griglia di eventi illustrato in precedenza.

Prima di tutto, creare le risorse che verranno usate come endpoint. Ecco cosa è necessario:
* Bus di servizio: _spazio dei nomi del bus_di servizio, _argomento del bus di servizio_, regola di _autorizzazione_
* Hub eventi: _spazio dei nomi di hub eventi_, _Hub eventi_, _regola di autorizzazione_

Quindi, usare i comandi seguenti per creare gli endpoint nei dispositivi gemelli digitali di Azure: 

* Aggiungere un endpoint dell'argomento del bus di servizio (richiede una risorsa del bus di servizio creata in precedenza)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Aggiungere l'endpoint di hub eventi (richiede una risorsa di hub eventi creata in precedenza)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="event-routes-with-apis-and-the-c-sdk"></a>Route di eventi (con le API e C# SDK)

Per inviare effettivamente i dati dai dispositivi gemelli digitali di Azure a un endpoint, è necessario definire una **route dell'evento**. Le **API EventRoutes** di Azure Digital Twins consentono agli sviluppatori di collegare il flusso degli eventi, in tutto il sistema e ai servizi downstream. Per altre informazioni sulle route di eventi, vedere [*concetti relativi al routing di eventi gemelli digitali di Azure*](concepts-route-events.md).

Gli esempi in questa sezione usano C# SDK.

**Prerequisito**: è necessario creare endpoint come descritto in precedenza in questo articolo prima di poter passare alla creazione di una route. È possibile procedere alla creazione di una route di eventi al termine della configurazione degli endpoint.

>[!NOTE]
>Se gli endpoint sono stati distribuiti di recente, verificare che la distribuzione sia terminata **prima** di provare a usarli per una nuova route di eventi. Se la distribuzione della route non riesce perché gli endpoint non sono pronti, attendere alcuni minuti e riprovare.
>
> Se si crea uno script per questo flusso, è possibile che si voglia tenere conto di questa situazione creando in 2-3 minuti di tempo di attesa per il completamento della distribuzione del servizio endpoint prima di passare alla configurazione del routing.

### <a name="create-an-event-route"></a>Creare una route di eventi

Le route degli eventi vengono definite usando le API del piano dati. 

Una definizione di route può contenere gli elementi seguenti:
* Nome della route che si vuole usare
* Nome dell'endpoint che si desidera utilizzare
* Filtro che definisce gli eventi che vengono inviati all'endpoint 

Se non è presente alcun nome di route, nessun messaggio viene instradato all'esterno dei dispositivi gemelli digitali di Azure. Se è presente un nome di route e il filtro è `true` , tutti i messaggi vengono instradati all'endpoint. Se è presente un nome di route e viene aggiunto un filtro diverso, i messaggi verranno filtrati in base al filtro.

Una route deve consentire la selezione di più notifiche e tipi di evento. 

`CreateEventRoute` è la chiamata SDK utilizzata per aggiungere una route dell'evento. Di seguito è riportato un esempio di utilizzo:

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

È possibile limitare gli eventi inviati aggiungendo un **filtro** per un endpoint alla route dell'evento.

Per aggiungere un filtro, è possibile usare una richiesta PUT per *https://{YourHost}/EventRoutes/myNewRoute? API-Version = 2020-05 -31-Preview* con il corpo seguente:

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

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui diversi tipi di messaggi di evento che è possibile ricevere, vedere:
* [*Procedura: interpretare i dati degli eventi*](how-to-interpret-event-data.md)
