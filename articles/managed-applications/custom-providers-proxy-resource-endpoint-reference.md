---
title: Informazioni di riferimento per il proxy di risorse personalizzate
description: Riferimento di risorsa personalizzata proxy per i provider di risorse di Azure personalizzati. Questo articolo verrà esaminati i requisiti per gli endpoint che implementa le risorse personalizzate di proxy.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: fe470165d11b24ffb1df704ecaa9804663fc9e5c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796022"
---
# <a name="custom-resource-proxy-reference"></a>Riferimento a una risorsa personalizzata Proxy

Questo articolo verrà esaminati i requisiti per gli endpoint che implementa le risorse personalizzate di proxy. Se non si ha familiarità con i provider di risorse personalizzati di Azure, vedere [la panoramica sul provider di risorse personalizzati](./custom-providers-overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Come definire un endpoint di risorse di proxy

È possibile creare una risorsa proxy specificando il **routingType** "Proxy".

Provider di risorse personalizzato di esempio:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
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

Un' **endpoint** che implementa una risorsa "Proxy" **endpoint** deve gestire la richiesta e risposta per la nuova API in Azure. In questo caso, il **resourceType** genererà una nuova risorsa di Azure API per `PUT`, `GET`, e `DELETE` per eseguire operazioni su una sola risorsa, nonché `GET` per recuperare tutte le risorse esistenti.

> [!NOTE]
> Il `id`, `name`, e `type` campi non sono necessari, ma sono necessarie per integrare la risorsa personalizzata con un ecosistema di Azure esistente.

Risorse di esempio:

``` JSON
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

Riferimento al parametro:

Proprietà | Esempio | Descrizione
---|---|---
name | '{myCustomResourceName}' | Il nome della risorsa personalizzata.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | Lo spazio dei nomi di tipo di risorsa.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | L'ID risorsa.

### <a name="create-a-custom-resource"></a>Creare una risorsa personalizzata

Richiesta in ingresso API di Azure:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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

**Endpoint** risposta:

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

Allo stesso modo la risposta il **endpoint** viene quindi inoltrata al cliente. Deve restituire la risposta dall'endpoint:

- Documento di oggetto JSON valido. Tutte le matrici e stringhe devono essere annidate in un oggetto di primo livello.
- Il `Content-Type` intestazione deve essere impostata su "application/json; charset = utf-8 ".

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

Questa richiesta verrà inoltrata quindi per il **endpoint** nel formato:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Analogamente, la risposta il **endpoint** viene quindi inoltrata al cliente. Deve restituire la risposta dall'endpoint:

- Un documento oggetto JSON valido. Tutte le matrici e stringhe devono essere annidate in un oggetto di primo livello.
- Il `Content-Type` intestazione deve essere impostata su "application/json; charset = utf-8 ".

**Endpoint** risposta:

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

Questa richiesta verrà inoltrata quindi per il **endpoint** nel formato:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Analogamente, la risposta il **endpoint** viene quindi inoltrata al cliente. Deve restituire la risposta dall'endpoint:

- Un documento oggetto JSON valido. Tutte le matrici e stringhe devono essere annidate in un oggetto di primo livello.
- Il `Content-Type` intestazione deve essere impostata su "application/json; charset = utf-8 ".
- L'elenco di risorse deve essere inserita nel primo livello `value` proprietà.

**Endpoint** risposta:

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
- [Informazioni di riferimento: Riferimento a una risorsa personalizzata della Cache](./custom-providers-proxy-cache-resource-endpoint-reference.md)
