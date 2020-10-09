---
title: Aggiunta di azioni personalizzate all'API REST di Azure
description: Informazioni su come aggiungere azioni personalizzate all'API REST di Azure. In questo articolo vengono illustrati i requisiti e le procedure consigliate per gli endpoint che desiderano implementare azioni personalizzate.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75650396"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Aggiunta di azioni personalizzate all'API REST di Azure

In questo articolo verranno illustrati i requisiti e le procedure consigliate per la creazione di endpoint del provider di risorse personalizzati di Azure che implementano azioni personalizzate. Se non si ha familiarità con i provider di risorse personalizzati di Azure, vedere [la panoramica sui provider di risorse personalizzati](overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Come definire un endpoint azione

Un **endpoint** è un URL che punta a un servizio che implementa il contratto sottostante tra l'IT e Azure. L'endpoint è definito nel provider di risorse personalizzato e può essere qualsiasi URL accessibile pubblicamente. Nell'esempio seguente è presente un' **azione** denominata `myCustomAction` implementata da `endpointURL` .

**ResourceProvider**di esempio:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
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

## <a name="building-an-action-endpoint"></a>Compilazione di un endpoint azione

Un **endpoint** che implementa un' **azione** deve gestire la richiesta e la risposta per la nuova API in Azure. Quando viene creato un provider di risorse personalizzato con un' **azione** , verrà generato un nuovo set di API in Azure. In questo caso, l'azione genererà una nuova API di azione di Azure per le `POST` chiamate:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Richiesta in ingresso API di Azure:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Questa richiesta verrà quindi trasmessa all' **endpoint** nel formato seguente:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Analogamente, la risposta dall' **endpoint** viene quindi reinviata al cliente. La risposta dall'endpoint deve restituire:

- Documento oggetto JSON valido. Tutte le matrici e le stringhe devono essere annidate in un oggetto top.
- L' `Content-Type` intestazione deve essere impostata su "Application/JSON; charset = UTF-8 ".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Risposta del provider di risorse personalizzato di Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Chiamata di un'azione personalizzata

Esistono due modi principali per chiamare un'azione personalizzata da un provider di risorse personalizzato:

- Interfaccia della riga di comando di Azure
- Modelli di Azure Resource Manager

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Parametro | Obbligatoria | Descrizione
---|---|---
action | *Sì* | Nome dell'azione definita in **ResourceProvider**.
ids | *Sì* | ID di risorsa di **ResourceProvider**.
request-body | *No* | Il corpo della richiesta che verrà inviato all' **endpoint**.

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

> [!NOTE]
> Le azioni hanno un supporto limitato nei modelli di Azure Resource Manager. Affinché l'azione venga chiamata all'interno di un modello, deve contenere il [`list`](../templates/template-functions-resource.md#list) prefisso nel nome.

Esempio di **ResourceProvider** con azione List:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Modello di Azure Resource Manager di esempio:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Parametro | Obbligatoria | Descrizione
---|---|---
resourceIdentifier | *Sì* | ID di risorsa di **ResourceProvider**.
apiVersion | *Sì* | Versione dell'API del runtime di risorse. Deve essere sempre "2018-09-01-Preview".
functionValues | *No* | Il corpo della richiesta che verrà inviato all' **endpoint**.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica sui provider di risorse personalizzati di Azure](overview.md)
- [Guida introduttiva: creare un provider di risorse personalizzato di Azure e distribuire risorse personalizzate](./create-custom-provider.md)
- [Esercitazione: creare azioni e risorse personalizzate in Azure](./tutorial-get-started-with-custom-providers.md)
- [Procedura: aggiungere risorse personalizzate all'API REST di Azure](./custom-providers-resources-endpoint-how-to.md)
