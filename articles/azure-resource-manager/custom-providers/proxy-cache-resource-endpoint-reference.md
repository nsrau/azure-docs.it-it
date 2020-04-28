---
title: Informazioni di riferimento per la cache di risorse personalizzate
description: Informazioni di riferimento sulla cache di risorse personalizzate per i provider di risorse personalizzati di Azure. In questo articolo verranno esaminati i requisiti per gli endpoint che implementano risorse personalizzate della cache.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75650383"
---
# <a name="custom-resource-cache-reference"></a>Riferimento alla cache delle risorse personalizzate

In questo articolo verranno esaminati i requisiti per gli endpoint che implementano risorse personalizzate della cache. Se non si ha familiarità con i provider di risorse personalizzati di Azure, vedere [la panoramica sui provider di risorse personalizzati](overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Come definire un endpoint di risorsa della cache

È possibile creare una risorsa proxy specificando il valore di **routingType** su "proxy, cache".

Provider di risorse personalizzato di esempio:

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

## <a name="building-proxy-resource-endpoint"></a>Compilazione dell'endpoint della risorsa proxy

Un **endpoint** che implementa un **endpoint** della risorsa "proxy, cache" deve gestire la richiesta e la risposta per la nuova API in Azure. In questo caso, il **ResourceType** genererà una nuova API risorse di Azure `PUT`per `GET`, e `DELETE` per eseguire CRUD su una singola risorsa, oltre che `GET` per recuperare tutte le risorse esistenti:

> [!NOTE]
> L'API di Azure genererà i metodi `PUT`di `GET`richiesta, `DELETE`e, ma l' **endpoint** della cache deve solo `PUT` gestire `DELETE`e.
> Si consiglia inoltre di **endpoint** implementare `GET`l'endpoint.

### <a name="create-a-custom-resource"></a>Creare una risorsa personalizzata

Richiesta in ingresso API di Azure:

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

Questa richiesta verrà quindi trasmessa all' **endpoint** nel formato seguente:

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

Analogamente, la risposta dall' **endpoint** viene quindi reinviata al cliente. La risposta dall'endpoint deve restituire:

- Documento oggetto JSON valido. Tutte le matrici e le stringhe devono essere annidate in un oggetto top.
- L' `Content-Type` intestazione deve essere impostata su "Application/JSON; charset = UTF-8 ".
- Il provider di risorse personalizzato `name`sovrascriverà `type`i campi `id` , e per la richiesta.
- Il provider di risorse personalizzato restituirà solo i `properties` campi nell'oggetto per un endpoint della cache.

**Endpoint** di Risposta

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

I `name`campi `id`, e `type` verranno generati automaticamente per la risorsa personalizzata dal provider di risorse personalizzato.

Risposta del provider di risorse personalizzato di Azure:

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

Richiesta in ingresso API di Azure:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Questa richiesta verrà quindi trasmessa all' **endpoint** nel formato seguente:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Analogamente, la risposta dall' **endpoint** viene quindi reinviata al cliente. La risposta dall'endpoint deve restituire:

- Documento oggetto JSON valido. Tutte le matrici e le stringhe devono essere annidate in un oggetto top.
- L' `Content-Type` intestazione deve essere impostata su "Application/JSON; charset = UTF-8 ".
- Il provider di risorse personalizzato di Azure rimuoverà l'elemento dalla relativa cache solo se viene restituita una risposta di 200 a livello. Anche se la risorsa non esiste, l' **endpoint** deve restituire 204.

**Endpoint** di Risposta

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Risposta del provider di risorse personalizzato di Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Recuperare una risorsa personalizzata

Richiesta in ingresso API di Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

La richiesta **non** verrà trasmessa all' **endpoint**.

Risposta del provider di risorse personalizzato di Azure:

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

### <a name="enumerate-all-custom-resources"></a>Enumera tutte le risorse personalizzate

Richiesta in ingresso API di Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Questa richiesta **non** verrà trasmessa all' **endpoint**.

Risposta del provider di risorse personalizzato di Azure:

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

- [Panoramica sui provider di risorse personalizzati di Azure](overview.md)
- [Guida introduttiva: creare un provider di risorse personalizzato di Azure e distribuire risorse personalizzate](./create-custom-provider.md)
- [Esercitazione: creare azioni e risorse personalizzate in Azure](./tutorial-get-started-with-custom-providers.md)
- [Procedura: aggiungere azioni personalizzate all'API REST di Azure](./custom-providers-action-endpoint-how-to.md)
- [Riferimento: riferimento al proxy di risorsa personalizzato](proxy-resource-endpoint-reference.md)
