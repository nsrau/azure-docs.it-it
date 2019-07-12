---
title: Informazioni di riferimento per la cache di risorse personalizzate
description: Riferimento di risorsa personalizzata della cache per i provider di risorse di Azure personalizzati. Questo articolo verrà esaminati i requisiti per gli endpoint che implementa le risorse personalizzate di cache.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 2970e69e825dacb6f548b3e66a830f221ece0b1c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795235"
---
# <a name="custom-resource-cache-reference"></a>Riferimento a una risorsa personalizzata della Cache

Questo articolo verrà esaminati i requisiti per gli endpoint che implementa le risorse personalizzate di cache. Se non si ha familiarità con i provider di risorse personalizzati di Azure, vedere [la panoramica sul provider di risorse personalizzati](./custom-providers-overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Come definire un endpoint di risorse della cache

È possibile creare una risorsa proxy specificando il **routingType** a "Proxy", della Cache.

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

## <a name="building-proxy-resource-endpoint"></a>Endpoint di risorse di proxy predefiniti

Un' **endpoint** che implementa una risorsa "Proxy, Cache" **endpoint** deve gestire la richiesta e risposta per la nuova API in Azure. In questo caso, il **resourceType** genererà una nuova risorsa di Azure API per `PUT`, `GET`, e `DELETE` per eseguire operazioni su una sola risorsa, nonché `GET` per recuperare tutte le risorse esistenti:

> [!NOTE]
> L'API di Azure genererà i metodi di richiesta `PUT`, `GET`, e `DELETE`, ma la cache **endpoint** deve solo gestire `PUT` e `DELETE`.
> È consigliabile che il **endpoint** implementa anche `GET`.

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

Questa richiesta verrà inoltrata quindi per il **endpoint** nel formato:

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

Analogamente, la risposta il **endpoint** viene quindi inoltrata al cliente. Deve restituire la risposta dall'endpoint:

- Un documento oggetto JSON valido. Tutte le matrici e stringhe devono essere annidate in un oggetto di primo livello.
- Il `Content-Type` intestazione deve essere impostata su "application/json; charset = utf-8 ".
- Il provider di risorse personalizzato sovrascriverà il `name`, `type`, e `id` campi per la richiesta.
- Il provider di risorse personalizzato restituirà solo i campi sotto il `properties` oggetto per un endpoint della cache.

**Endpoint** risposta:

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

Il `name`, `id`, e `type` campi verranno automaticamente generati per la risorsa personalizzata dal provider di risorse personalizzati.

Risposta di Provider di risorse personalizzati di Azure:

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

Questa richiesta verrà inoltrata quindi per il **endpoint** nel formato:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Analogamente, la risposta il **endpoint** viene quindi inoltrata al cliente. Deve restituire la risposta dall'endpoint:

- Un documento oggetto JSON valido. Tutte le matrici e stringhe devono essere annidate in un oggetto di primo livello.
- Il `Content-Type` intestazione deve essere impostata su "application/json; charset = utf-8 ".
- Il Provider di risorse personalizzato di Azure rimuoverà solo l'elemento dalla cache se viene restituita una risposta a livello di 200. Anche se la risorsa non esiste, il **endpoint** deve restituire 204.

**Endpoint** risposta:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Risposta di Provider di risorse personalizzati di Azure:

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

La richiesta verrà **non** inoltrare le **endpoint**.

Risposta di Provider di risorse personalizzati di Azure:

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

Richiesta in ingresso API di Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Questa richiesta verrà **non** inoltrare il **endpoint**.

Risposta di Provider di risorse personalizzati di Azure:

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

- [Panoramica sui provider di risorse personalizzati di Azure](./custom-providers-overview.md)
- [Avvio rapido: Creare il Provider di risorse personalizzati di Azure e distribuire le risorse personalizzate](./create-custom-provider.md)
- [Esercitazione: Creare azioni personalizzate e le risorse in Azure](./tutorial-custom-providers-101.md)
- [Procedure: Aggiunta di azioni personalizzate all'API REST di Azure](./custom-providers-action-endpoint-how-to.md)
- [Informazioni di riferimento: Riferimento a una risorsa personalizzata Proxy](./custom-providers-proxy-resource-endpoint-reference.md)
