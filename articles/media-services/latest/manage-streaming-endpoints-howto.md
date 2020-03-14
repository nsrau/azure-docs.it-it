---
title: Gestire gli endpoint di streaming con servizi multimediali di Azure V3
description: Questo articolo illustra come gestire gli endpoint di streaming con servizi multimediali di Azure V3.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2020
ms.author: juliako
ms.openlocfilehash: 5dd3cc1efd25f7ec09f897c67bebebedb84d9570
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370546"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Gestire gli endpoint di streaming con servizi multimediali V3

Quando l'account di Servizi multimediali viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione [dinamica dei pacchetti](dynamic-packaging-overview.md) e della [crittografia dinamica](content-protection-overview.md), l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato in **esecuzione** .

Questo articolo illustra come avviare l'endpoint di streaming.
 
> [!NOTE]
> Il costo verrà addebitato solo quando StreamingEndpoint è in stato di esecuzione.
    
## <a name="prerequisites"></a>Prerequisiti

Recensione 

* [Concetti relativi ai Servizi multimediali](concepts-overview.md)
* [Concetto di endpoint di streaming](streaming-endpoint-concept.md)
* [Creazione dinamica dei pacchetti](dynamic-packaging-overview.md)

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Passare all'account di servizi multimediali di Azure.
1. A sinistra selezionare endpoint di **streaming**.
1. Selezionare l'endpoint di streaming che si desidera avviare, quindi fare clic su **Avvia**.

## <a name="use-the-java-sdk"></a>Usare Java SDK

```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Vedere l' [esempio di codice Java](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128)completo.

## <a name="use-the-net-sdk"></a>Usare .NET SDK

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Vedere l' [esempio di codice .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112)completo.

## <a name="use-cli"></a>Usare l'interfaccia della riga di comando

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Per altre informazioni, vedere [AZ AMS streaming-endpoint Start](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start).

## <a name="use-rest"></a>Usare REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Per altre informazioni, vedere: 

* La documentazione di riferimento per l' [avvio di StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) .
* L'avvio di un endpoint di streaming è un'operazione asincrona. 

    Per informazioni dettagliate su come monitorare le operazioni con esecuzione prolungata, vedere [operazioni con esecuzione prolungata](media-services-apis-overview.md)
* Questa [raccolta di post](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) contiene esempi di più operazioni REST, tra cui l'avvio di un endpoint di streaming.

## <a name="next-steps"></a>Passaggi successivi

* [Specifica OpenAPI di Media Services V3 (spavalderia)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Operazioni dell'endpoint di streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints)
