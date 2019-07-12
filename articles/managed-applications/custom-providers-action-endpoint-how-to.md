---
title: Aggiunta di azioni personalizzate all'API REST di Azure
description: Informazioni su come aggiungere azioni personalizzate per l'API REST di Azure. Questo articolo verrà illustrata i requisiti e procedure consigliate per gli endpoint che desiderano implementare le azioni personalizzate.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6fbd20c201e1b141b7276e3283599b00cdefd118
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795291"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Aggiunta di azioni personalizzate all'API REST di Azure

Questo articolo verrà esaminati i requisiti e procedure consigliate per la creazione di endpoint del Provider di risorse di Azure personalizzati che implementano le azioni personalizzate. Se non si ha familiarità con i provider di risorse personalizzati di Azure, vedere [la panoramica sul provider di risorse personalizzati](./custom-providers-overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Come definire un Endpoint di azione

Un' **endpoint** è un URL che punta a un servizio che implementa il contratto sottostante tra i dati e Azure. L'endpoint è definito nel provider di risorse personalizzato e può essere qualsiasi URL pubblicamente accessibile. L'esempio seguente include un' **azione** chiamato `myCustomAction` implementata da `endpointURL`.

Campione **ResourceProvider**:

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

## <a name="building-an-action-endpoint"></a>Creazione di un endpoint di azione

Un' **endpoint** che implementa un' **azione** deve gestire la richiesta e risposta per la nuova API in Azure. Quando un provider di risorse personalizzato con un **azione** viene creato, verrà generato un nuovo set di API in Azure. In questo caso, l'azione genererà una nuova azione Azure API per `POST` chiamate:

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

Questa richiesta verrà inoltrata quindi per il **endpoint** nel formato:

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

Analogamente, la risposta il **endpoint** viene quindi inoltrata al cliente. Deve restituire la risposta dall'endpoint:

- Un documento oggetto JSON valido. Tutte le matrici e stringhe devono essere annidate in un oggetto di primo livello.
- Il `Content-Type` intestazione deve essere impostata su "application/json; charset = utf-8 ".

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

Risposta di Provider di risorse personalizzati di Azure:

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

## <a name="calling-a-custom-action"></a>La chiamata a un'azione personalizzata

Esistono due modi principali per la chiamata a un'azione personalizzata di fuori di un provider di risorse personalizzate:

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
action | *Sì* | Il nome dell'azione definito nel **ResourceProvider**.
ID | *Sì* | L'ID della risorsa di **ResourceProvider**.
request-body | *no* | Il corpo della richiesta che verrà inviato il **endpoint**.

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

> [!NOTE]
> Le azioni hanno limitato supporto nei modelli di Resource Manager di Azure. Affinché l'azione che deve essere chiamato all'interno di un modello, deve contenere il [ `list` ](../azure-resource-manager/resource-group-template-functions-resource.md#list) prefisso nel nome.

Campione **ResourceProvider** con azione dell'elenco:

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

Esempio modello di gestione risorse di Azure:

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
resourceIdentifier | *Sì* | L'ID della risorsa di **ResourceProvider**.
apiVersion | *Sì* | La versione API del runtime di risorse. Deve essere sempre "2018-09-01-preview".
functionValues | *no* | Il corpo della richiesta che verrà inviato il **endpoint**.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica sui provider di risorse personalizzati di Azure](./custom-providers-overview.md)
- [Avvio rapido: Creare il Provider di risorse personalizzati di Azure e distribuire le risorse personalizzate](./create-custom-provider.md)
- [Esercitazione: Creare azioni personalizzate e le risorse in Azure](./tutorial-custom-providers-101.md)
- [Procedure: Aggiunta di risorse personalizzate all'API REST di Azure](./custom-providers-resources-endpoint-how-to.md)
