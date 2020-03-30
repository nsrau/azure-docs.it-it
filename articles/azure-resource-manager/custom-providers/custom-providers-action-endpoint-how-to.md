---
title: Aggiunta di azioni personalizzate all'API REST di Azure
description: Informazioni su come aggiungere azioni personalizzate all'API REST di Azure.Learn how to add custom actions to the Azure REST API. Questo articolo illustra i requisiti e le procedure consigliate per gli endpoint che desiderano implementare azioni personalizzate.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650396"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Aggiunta di azioni personalizzate all'API REST di AzureAdding Custom Actions to Azure REST API

Questo articolo illustra i requisiti e le procedure consigliate per la creazione di endpoint del provider di risorse personalizzato di Azure che implementano azioni personalizzate. Se non si ha familiarità con i provider di risorse personalizzate di Azure, vedere [panoramica sui provider](overview.md)di risorse personalizzati.

## <a name="how-to-define-an-action-endpoint"></a>Come definire un endpoint azioneHow to define an Action Endpoint

Un endpoint è un URL che punta a un servizio, che implementa il contratto sottostante tra esso e Azure.An **endpoint** is a URL that points to a service, which implements the underlying contract between it and Azure. L'endpoint è definito nel provider di risorse personalizzato e può essere qualsiasi URL accessibile pubblicamente. Nell'esempio riportato `myCustomAction` di `endpointURL`seguito è riportata **un'azione** denominata implementata da .

**Risorsa di esempio:**

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

## <a name="building-an-action-endpoint"></a>Creazione di un endpoint azioneBuilding an action endpoint

Un endpoint che implementa **un'azione** deve gestire la richiesta e la risposta per la nuova API in Azure.An **endpoint** that implements an action must handle the request and response for the new API in Azure. Quando viene creato un provider di risorse personalizzato con **un'azione,** verrà generato un nuovo set di API in Azure.When a custom resource provider with an action is created, it will generate a new set of APIs in Azure. In questo caso, l'azione genererà `POST` una nuova API di azione di Azure per le chiamate:In this case, the action will generate a new Azure action API for calls:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Richiesta in ingresso API di Azure:Azure API Incoming Request:

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

Questa richiesta verrà quindi inoltrata **all'endpoint** nel formato:

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

Analogamente, la risposta **dall'endpoint** viene quindi inoltrata al cliente. La risposta dall'endpoint deve restituire:The response from the endpoint should return:

- Documento oggetto JSON valido. Tutte le matrici e le stringhe devono essere annidate sotto un oggetto superiore.
- L'intestazione `Content-Type` deve essere impostata su "application/json; charset:utf-8".

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

Risposta del provider di risorse personalizzato di Azure:Azure Custom Resource Provider Response:

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

## <a name="calling-a-custom-action"></a>Chiamata di un'azione personalizzataCalling a Custom Action

Esistono due modi principali per chiamare un'azione personalizzata da un provider di risorse personalizzato:There are two main ways of calling a custom action off of a custom resource provider:

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
action | *Sì* | Nome dell'azione definita nel **ResourceProvider**.
ids | *Sì* | ID di risorsa di **ResourceProvider**.
request-body | *No* | Corpo della richiesta che verrà inviato **all'endpoint.**

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

> [!NOTE]
> Le azioni hanno un supporto limitato nei modelli di Azure Resource Manager.Actions have limited support in Azure Resource Manager Templates. Affinché l'azione venga chiamata all'interno di [`list`](../templates/template-functions-resource.md#list) un modello, deve contenere il prefisso nel nome.

ResourceProvider di esempio con azione elenco:Sample **ResourceProvider** with List Action:

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
resourceIdentifier (identificatore delle risorse) | *Sì* | ID di risorsa di **ResourceProvider**.
apiVersion | *Sì* | Versione API del runtime delle risorse. Questo dovrebbe essere sempre "2018-09-01-preview".
functionValues | *No* | Corpo della richiesta che verrà inviato **all'endpoint.**

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica sui provider di risorse personalizzate di AzureOverview on Azure Custom Resource Providers](overview.md)
- [Guida introduttiva: Creare il provider di risorse personalizzato di Azure e distribuire risorse personalizzate](./create-custom-provider.md)
- [Esercitazione: Creare azioni e risorse personalizzate in AzureTutorial: Create custom actions and resources in Azure](./tutorial-get-started-with-custom-providers.md)
- [Procedura: Aggiunta di risorse personalizzate all'API REST di AzureHow To: Adding Custom Resources to Azure REST API](./custom-providers-resources-endpoint-how-to.md)
