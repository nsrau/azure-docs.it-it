---
title: Informazioni di riferimento per la cache di risorse personalizzate
description: Custom resource cache reference for Azure Custom Resource Providers. Questo articolo illustra i requisiti per gli endpoint che implementano le risorse personalizzate della cache.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650383"
---
# <a name="custom-resource-cache-reference"></a>Informazioni di riferimento sulla cache delle risorse personalizzateCustom Resource Cache Reference

Questo articolo illustra i requisiti per gli endpoint che implementano le risorse personalizzate della cache. Se non si ha familiarità con i provider di risorse personalizzate di Azure, vedere [panoramica sui provider](overview.md)di risorse personalizzati.

## <a name="how-to-define-a-cache-resource-endpoint"></a>Come definire un endpoint di risorse della cacheHow to define a cache resource endpoint

È possibile creare una risorsa proxy specificando **routingType** su "Proxy, Cache".

Provider di risorse personalizzato di esempio:Sample custom resource provider:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-proxy-resource-endpoint"></a>Creazione di un endpoint di risorse proxyBuilding proxy resource endpoint

Un endpoint che implementa un endpoint di risorsa "Proxy, Cache" deve gestire la richiesta e la risposta per la nuova API in Azure.An **endpoint** that implements a "Proxy, Cache" resource **endpoint** must handle the request and response for the new API in Azure. In questo caso, resourceType genererà una nuova `GET`API `DELETE` delle risorse di Azure per `GET` , , ed esegue CRUD su una singola risorsa, nonché per recuperare tutte le risorse esistenti:In this case, the **resourceType** will generate a new Azure resource API for `PUT`, , and to perform CRUD on a single resource, as well as to retrieve all existing resources:

> [!NOTE]
> L'API di Azure `PUT`genererà i `GET`metodi di richiesta , , e `DELETE`, ma l'endpoint della cache deve solo gestire **endpoint** `PUT` e `DELETE`.
> È consigliabile che `GET` **l'endpoint** implementi anche .

### <a name="create-a-custom-resource"></a>Creare una risorsa personalizzata

Richiesta in ingresso API di Azure:Azure API Incoming Request:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Questa richiesta verrà quindi inoltrata **all'endpoint** nel formato:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Analogamente, la risposta **dall'endpoint** viene quindi inoltrata al cliente. La risposta dall'endpoint deve restituire:The response from the endpoint should return:

- Documento oggetto JSON valido. Tutte le matrici e le stringhe devono essere annidate sotto un oggetto superiore.
- L'intestazione `Content-Type` deve essere impostata su "application/json; charset:utf-8".
- Il provider di risorse `name` `type`personalizzato `id` sovrascriverà i campi , e per la richiesta.
- Il provider di risorse personalizzato `properties` restituirà solo i campi sotto l'oggetto per un endpoint della cache.

**Endpoint** Risposta:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

I `name` `id`campi `type` , e verranno generati automaticamente per la risorsa personalizzata dal provider di risorse personalizzato.

Risposta del provider di risorse personalizzato di Azure:Azure Custom Resource Provider Response:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Rimuovere una risorsa personalizzata

Richiesta in ingresso API di Azure:Azure API Incoming Request:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Questa richiesta verrà quindi inoltrata **all'endpoint** nel formato:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Analogamente, la risposta **dall'endpoint** viene quindi inoltrata al cliente. La risposta dall'endpoint deve restituire:The response from the endpoint should return:

- Documento oggetto JSON valido. Tutte le matrici e le stringhe devono essere annidate sotto un oggetto superiore.
- L'intestazione `Content-Type` deve essere impostata su "application/json; charset:utf-8".
- Il provider di risorse personalizzato di Azure rimuoverà l'elemento dalla cache solo se viene restituita una risposta di livello 200.The Azure Custom Resource Provider will only remove the item from its cache if a 200-level response is returned. Anche se la risorsa non esiste, **l'endpoint** deve restituire 204.

**Endpoint** Risposta:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Risposta del provider di risorse personalizzato di Azure:Azure Custom Resource Provider Response:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Recuperare una risorsa personalizzata

Richiesta in ingresso API di Azure:Azure API Incoming Request:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

La richiesta **non** verrà inoltrata **all'endpoint.**

Risposta del provider di risorse personalizzato di Azure:Azure Custom Resource Provider Response:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Enumerare tutte le risorse personalizzate

Richiesta in ingresso API di Azure:Azure API Incoming Request:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Questa richiesta **non** verrà inoltrata **all'endpoint.**

Risposta del provider di risorse personalizzato di Azure:Azure Custom Resource Provider Response:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica sui provider di risorse personalizzate di AzureOverview on Azure Custom Resource Providers](overview.md)
- [Guida introduttiva: Creare il provider di risorse personalizzato di Azure e distribuire risorse personalizzate](./create-custom-provider.md)
- [Esercitazione: Creare azioni e risorse personalizzate in AzureTutorial: Create custom actions and resources in Azure](./tutorial-get-started-with-custom-providers.md)
- [Procedura: Aggiunta di azioni personalizzate all'API REST di AzureHow To: Adding Custom Actions to Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Riferimento: Riferimento al proxy di risorsa personalizzatoReference: Custom Resource Proxy Reference](proxy-resource-endpoint-reference.md)
