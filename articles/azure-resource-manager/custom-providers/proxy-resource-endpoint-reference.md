---
title: Informazioni di riferimento per il proxy di risorse personalizzate
description: Custom resource proxy reference for Azure Custom Resource Providers. Questo articolo illustra i requisiti per gli endpoint che implementano risorse personalizzate proxy.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650461"
---
# <a name="custom-resource-proxy-reference"></a>Guida di riferimento al proxy di risorsa personalizzatoCustom Resource Proxy Reference

Questo articolo illustra i requisiti per gli endpoint che implementano risorse personalizzate proxy. Se non si ha familiarità con i provider di risorse personalizzate di Azure, vedere [panoramica sui provider](overview.md)di risorse personalizzati.

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Come definire un endpoint di risorsa proxyHow to define a proxy resource endpoint

È possibile creare una risorsa proxy specificando **routingType** su "Proxy".

Provider di risorse personalizzato di esempio:Sample custom resource provider:

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

## <a name="building-proxy-resource-endpoint"></a>Creazione di un endpoint di risorse proxyBuilding proxy resource endpoint

Un endpoint che implementa un endpoint di risorsa "Proxy" deve gestire la richiesta e la risposta per la nuova API in Azure.An **endpoint** that implements a "Proxy" resource **endpoint** must handle the request and response for the new API in Azure. In questo caso, **resourceType** genererà una `PUT`nuova `GET`API `DELETE` di risorsa di Azure per `GET` , , ed esegue CRUD su una singola risorsa, nonché per recuperare tutte le risorse esistenti.

> [!NOTE]
> I `id` `name`campi `type` , e non sono obbligatori, ma sono necessari per integrare la risorsa personalizzata con l'ecosistema di Azure esistente.

Risorsa di esempio:Sample resource:

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

Riferimento parametro:

Proprietà | Esempio | Descrizione
---|---|---
name | '{myCustomResourceName}' | Il nome della risorsa personalizzata.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | Lo spazio dei nomi del tipo di risorsa.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/'myCustomResourceName'' | L'ID della risorsa.

### <a name="create-a-custom-resource"></a>Creare una risorsa personalizzata

Richiesta in ingresso API di Azure:Azure API Incoming Request:

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

**Endpoint** Risposta:

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

Analogamente, la risposta **dall'endpoint** viene inoltrata al cliente. La risposta dall'endpoint deve restituire:The response from the endpoint should return:

- Documento oggetto JSON valido. Tutte le matrici e le stringhe devono essere annidate sotto un oggetto superiore.
- L'intestazione `Content-Type` deve essere impostata su "application/json; charset:utf-8".

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

Questa richiesta verrà quindi inoltrata **all'endpoint** nel formato:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Analogamente, la risposta **dall'endpoint** viene quindi inoltrata al cliente. La risposta dall'endpoint deve restituire:The response from the endpoint should return:

- Documento oggetto JSON valido. Tutte le matrici e le stringhe devono essere annidate sotto un oggetto superiore.
- L'intestazione `Content-Type` deve essere impostata su "application/json; charset:utf-8".

**Endpoint** Risposta:

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

Questa richiesta verrà quindi inoltrata **all'endpoint** nel formato:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Analogamente, la risposta **dall'endpoint** viene quindi inoltrata al cliente. La risposta dall'endpoint deve restituire:The response from the endpoint should return:

- Documento oggetto JSON valido. Tutte le matrici e le stringhe devono essere annidate sotto un oggetto superiore.
- L'intestazione `Content-Type` deve essere impostata su "application/json; charset:utf-8".
- L'elenco delle risorse deve essere `value` inserito nella proprietà di primo livello.

**Endpoint** Risposta:

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
- [Riferimento: Riferimento alla cache delle risorse personalizzataReference: Custom Resource Cache Reference](proxy-cache-resource-endpoint-reference.md)
