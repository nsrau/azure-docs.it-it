---
title: Route di eventi
titleSuffix: Azure Digital Twins
description: Informazioni su come instradare gli eventi nei dispositivi gemelli digitali di Azure e ad altri servizi di Azure.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d41518b1fc0d8cdda3ded1e8036bd29e24e2b34a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541357"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Indirizzare gli eventi all'interno e all'esterno dei dispositivi gemelli digitali di Azure

I dispositivi gemelli digitali di Azure usano le **route degli eventi** per inviare dati a utenti esterni al servizio. 

Durante la fase di anteprima, esistono due casi principali per l'invio di dati dei dispositivi gemelli digitali di Azure:
* Invio di dati da un gemello nel grafico dei gemelli digitali di Azure a un altro. Ad esempio, quando una proprietà di un dispositivo gemello digitale cambia, è possibile che si voglia inviare una notifica e aggiornare un altro gemello digitale di conseguenza.
* Invio di dati a downstream Data Services per l'archiviazione o l'elaborazione aggiuntiva (noti anche come *dati in uscita*). Ad esempio,
  - Un ospedale potrebbe voler inviare i dati degli eventi di Azure Digital Twins a [Time Series Insights (TSI)](../time-series-insights/time-series-insights-update-overview.md)per registrare i dati delle serie temporali degli eventi correlati a mani per l'analisi in blocco.
  - Un'azienda che usa già [mappe di Azure](../azure-maps/about-azure-maps.md) potrebbe voler usare i dispositivi gemelli digitali di Azure per migliorare la propria soluzione. È possibile abilitare rapidamente una mappa di Azure dopo aver configurato i dispositivi gemelli digitali di Azure, importare le entità mappa di Azure in dispositivi gemelli digitali di Azure come [gemelli digitali](concepts-twins-graph.md) nel grafico gemello o eseguire query potenti sfruttando le mappe di Azure e i dati di Azure Digital gemelli insieme.

Le route degli eventi vengono usate per entrambi questi scenari.

## <a name="about-event-routes"></a>Informazioni sulle route degli eventi

Una route di eventi consente di inviare i dati degli eventi dai dispositivi gemelli digitali nei dispositivi gemelli digitali di Azure agli endpoint definiti in modo personalizzato nelle sottoscrizioni. Per gli endpoint sono attualmente supportati tre servizi di Azure: [Hub eventi](../event-hubs/event-hubs-about.md), [griglia di eventi](../event-grid/overview.md)e bus di [servizio](../service-bus-messaging/service-bus-messaging-overview.md). Ognuno di questi servizi di Azure può essere connesso ad altri servizi e funge da intermediario, inviando i dati a destinazioni finali, ad esempio TSI o mappe di Azure, per qualsiasi tipo di elaborazione necessario.

Il diagramma seguente illustra il flusso dei dati degli eventi tramite una soluzione più ampia con un aspetto di gemelli digitale di Azure:

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Dispositivi gemelli di Azure che indirizzano i dati attraverso gli endpoint a diversi servizi downstream" border="false":::

Le destinazioni downstream tipiche per le route di eventi sono risorse come le soluzioni TSI, Azure Maps, di archiviazione e di analisi.

### <a name="event-routes-for-internal-digital-twin-events"></a>Route di eventi per eventi gemelli digitali interni

Durante la versione di anteprima corrente, le route degli eventi vengono usate anche per gestire gli eventi all'interno del grafo gemello e inviare i dati dal dispositivo gemello digitale al dispositivo gemello. Questa operazione viene eseguita connettendo le route degli eventi tramite griglia di eventi alle risorse di calcolo, ad esempio [funzioni di Azure](../azure-functions/functions-overview.md). Queste funzioni definiscono quindi il modo in cui i gemelli dovrebbero ricevere e rispondere agli eventi. 

Quando una risorsa di calcolo vuole modificare il grafico a due gemelli in base a un evento ricevuto tramite la route dell'evento, può essere utile conoscere il gemello che desidera modificare in anticipo. 

In alternativa, il messaggio di evento contiene anche l'ID del dispositivo gemello di origine che ha inviato il messaggio, in modo che la risorsa di calcolo possa usare query o attraversare le relazioni per trovare un dispositivo gemello di destinazione per l'operazione desiderata. 

Anche la risorsa di calcolo deve stabilire le autorizzazioni di sicurezza e accesso in modo indipendente.

Per esaminare il processo di configurazione di una funzione di Azure per elaborare eventi gemelli digitali, vedere [*procedura: configurare una funzione di Azure per l'elaborazione dei dati*](how-to-create-azure-function.md).

## <a name="create-an-endpoint"></a>Creare un endpoint

Per definire una route di eventi, gli sviluppatori devono innanzitutto definire gli endpoint. Un **endpoint** è una destinazione esterna ai dispositivi gemelli digitali di Azure che supporta una connessione di route. Le destinazioni supportate nella versione di anteprima corrente sono:
* Argomenti personalizzati di griglia di eventi
* Hub eventi
* Bus di servizio

Per creare un endpoint, è possibile usare le [**API del piano di controllo**](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins)dei dispositivi gemelli di Azure, i comandi dell' [**interfaccia**](how-to-manage-routes-apis-cli.md#manage-endpoints-and-routes-with-cli)della riga di comando o la [**portale di Azure**](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins). 

Quando si definisce un endpoint, è necessario fornire:
* Nome dell'endpoint
* Tipo di endpoint (griglia di eventi, Hub eventi o bus di servizio)
* Stringa di connessione primaria e stringa di connessione secondaria da autenticare 
* Percorso dell'argomento dell'endpoint, ad esempio *your-topic.westus2.eventgrid.Azure.NET*

Le API dell'endpoint disponibili nel piano di controllo sono:
* Creare un endpoint
* Ottenere l'elenco degli endpoint
* Ottenere l'endpoint in base al nome
* Elimina endpoint per nome

## <a name="create-an-event-route"></a>Creare una route di eventi
 
Per creare una route di eventi, è possibile usare le API del [**piano dati**](how-to-manage-routes-apis-cli.md#create-an-event-route)di Azure Digital gemelli, i [**comandi dell'interfaccia**](how-to-manage-routes-apis-cli.md#manage-endpoints-and-routes-with-cli)della riga di comando o la [**portale di Azure**](how-to-manage-routes-portal.md#create-an-event-route). 

Di seguito è riportato un esempio di creazione di una route di evento all'interno di un'applicazione client tramite la chiamata a `CreateEventRoute` [.NET (C#) SDK](how-to-use-apis-sdks.md) : 

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

1. `EventRoute`Viene innanzitutto creato un oggetto e il costruttore accetta il nome di un endpoint. Questo `endpointName` campo identifica un endpoint, ad esempio un hub eventi, una griglia di eventi o un bus di servizio. Questi endpoint devono essere creati nella sottoscrizione e collegati ai dispositivi gemelli digitali di Azure usando le API del piano di controllo prima di effettuare questa chiamata di registrazione.

2. L'oggetto route dell'evento dispone anche di un campo [**Filter**](./how-to-manage-routes-apis-cli.md#filter-events) , che può essere usato per limitare i tipi di eventi che seguono questa route. Un filtro di `true` Abilita la route senza filtri aggiuntivi (un filtro `false` Disabilita la route). 

3. Questo oggetto route dell'evento viene quindi passato a `CreateEventRoute` , insieme a un nome per la route.

> [!TIP]
> Tutte le funzioni SDK sono disponibili in versioni sincrone e asincrone.

È anche possibile creare route usando l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md).

### <a name="types-of-event-messages"></a>Tipi di messaggi di evento

Tipi diversi di eventi nell'hub Internet e i gemelli digitali di Azure producono tipi diversi di messaggi di notifica, come descritto di seguito.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Passaggi successivi

Vedere come configurare e gestire una route di eventi:
* [*Procedura: gestire endpoint e Route*](how-to-manage-routes-apis-cli.md)

In alternativa, vedere come usare funzioni di Azure per indirizzare gli eventi nei dispositivi gemelli digitali di Azure:
* [*Procedura: configurare una funzione di Azure per l'elaborazione dei dati*](how-to-create-azure-function.md)