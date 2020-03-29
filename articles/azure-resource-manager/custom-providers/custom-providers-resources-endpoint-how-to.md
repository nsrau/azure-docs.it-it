---
title: Aggiunta di risorse personalizzate all'API REST di Azure
description: Informazioni su come aggiungere risorse personalizzate all'API REST di Azure.Learn how to add custom resources to the Azure REST API. Questo articolo illustra i requisiti e le procedure consigliate per gli endpoint che desiderano implementare risorse personalizzate.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650526"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Aggiunta di risorse personalizzate all'API REST di AzureAdding Custom Resources to Azure REST API

Questo articolo illustra i requisiti e le procedure consigliate per la creazione di endpoint del provider di risorse personalizzato di Azure che implementa risorse personalizzate. Se non si ha familiarità con i provider di risorse personalizzate di Azure, vedere [panoramica sui provider](overview.md)di risorse personalizzati.

## <a name="how-to-define-a-resource-endpoint"></a>Come definire un endpoint di risorsaHow to define a resource endpoint

Un endpoint è un URL che punta a un servizio, che implementa il contratto sottostante tra esso e Azure.An **endpoint** is a URL that points to a service, which implements the underlying contract between it and Azure. L'endpoint è definito nel provider di risorse personalizzato e può essere qualsiasi URL accessibile pubblicamente. Nell'esempio riportato di `myCustomResource` seguito `endpointURL`è riportato **un resourceType** chiamato implemented da .

**Risorsa di esempio:**

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
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

## <a name="building-a-resource-endpoint"></a>Creazione di un endpoint di risorseBuilding a resource endpoint

Un endpoint che implementa un resourceType deve gestire la richiesta e la risposta per la nuova API in Azure.An **endpoint** that implements an **resourceType** must handle the request and response for the new API in Azure. Quando viene creato un provider di risorse personalizzato con un **resourceType,** verrà generato un nuovo set di API in Azure.When a custom resource provider with an resourceType is created, it will generate a new set of APIs in Azure. In questo caso, resourceType genererà una nuova API `DELETE` di risorsa di Azure per `GET` , , ed esegue CRUD su una singola risorsa e per recuperare tutte le risorse esistenti:In this case, the **resourceType** will generate a new Azure resource API for `PUT`, `GET`, and to perform CRUD on a single resource as well as to retrieve all existing resources:

Manipolazione di`PUT` `GET`una `DELETE`singola risorsa ( , , e ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Recupera tutte`GET`le risorse ( ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Per le risorse personalizzate, i provider di`Proxy`risorse personalizzate`Proxy, Cache`offrono due tipi di **routingTipi:**" " e " ".

### <a name="proxy-routing-type"></a>tipo di routing proxy

"`Proxy`" **routingType** invia tramite proxy tutti i metodi di richiesta all'endpoint specificato nel provider di risorse personalizzato. **endpoint** Quando usare`Proxy`" ":

- È necessario il pieno controllo sulla risposta.
- Integrazione dei sistemi con le risorse esistenti.

Per ulteriori informazioni`Proxy`sulle risorse " ", vedere il riferimento al [proxy di risorse personalizzate](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>tipo di routing della cache proxy

I`Proxy, Cache`metodi " " **routingType** solo `PUT` proxy e `DELETE` richiesta all'endpoint specificato nel provider di risorse personalizzato. **endpoint** Il provider di risorse `GET` personalizzato restituirà automaticamente le richieste in base a ciò che ha archiviato nella cache. Se una risorsa personalizzata è contrassegnata con cache, il provider di risorse personalizzato aggiungerà/sovrascriverà anche i campi nella risposta per rendere le API compatibili con Azure.If a custom resource is marked with cache, the custom resource provider will also add /overwrite fields in response to make the APIs Azure compliant. Quando usare`Proxy, Cache`" ":

- Creazione di un nuovo sistema senza risorse esistenti.
- Usare l'ecosistema di Azure esistente.

Per ulteriori informazioni`Proxy, Cache`sulle risorse " ", vedere le informazioni di riferimento sulla [cache delle risorse personalizzate](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Creazione di una risorsa personalizzataCreating a custom resource

Esistono due modi principali per creare una risorsa personalizzata da un provider di risorse personalizzato:There are two main ways of creating a custom resource off of a custom resource provider:

- Interfaccia della riga di comando di Azure
- Modelli di Azure Resource Manager

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Creare una risorsa personalizzata:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

Parametro | Obbligatoria | Descrizione
---|---|---
is-full-object | *Sì* | Indica che l'oggetto proprietà include altre opzioni, come location, tags, sku, e/o plan.
id | *Sì* | ID della risorsa personalizzata. Questo dovrebbe esistere al di fuori del **ResourceProvider**
properties | *Sì* | Corpo della richiesta che verrà inviato **all'endpoint.**

Eliminare una risorsa personalizzata di Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametro | Obbligatoria | Descrizione
---|---|---
id | *Sì* | ID della risorsa personalizzata. Questo dovrebbe esistere al di fuori di **ResourceProvider**.

Recuperare una risorsa personalizzata di Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametro | Obbligatoria | Descrizione
---|---|---
id | *Sì* | ID della risorsa personalizzata. Questo dovrebbe esistere al di fuori del **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

> [!NOTE]
> Le risorse richiedono che `id`la `name`risposta `type` contenga un oggetto , e dall'endpoint . **endpoint**

I modelli di `id`Azure `name`Resource `type` Manager richiedono tale , e vengono restituiti correttamente dall'endpoint downstream. Una risposta alla risorsa restituita deve essere nel formato seguente:A returned resource response should be in the form:

Esempio di risposta **all'endpoint:Sample endpoint** response:

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

Modello di Azure Resource Manager di esempio:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

Parametro | Obbligatoria | Descrizione
---|---|---
resourceTypeName | *Sì* | **Nome** del **resourceType** definito nel provider personalizzato.
resourceProviderName | *Sì* | Nome dell'istanza del provider di risorse personalizzato.
customResourceName | *Sì* | Nome della risorsa personalizzata.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica sui provider di risorse personalizzate di AzureOverview on Azure Custom Resource Providers](overview.md)
- [Guida introduttiva: Creare il provider di risorse personalizzato di Azure e distribuire risorse personalizzate](./create-custom-provider.md)
- [Esercitazione: Creare azioni e risorse personalizzate in AzureTutorial: Create custom actions and resources in Azure](./tutorial-get-started-with-custom-providers.md)
- [Procedura: Aggiunta di azioni personalizzate all'API REST di AzureHow To: Adding Custom Actions to Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Riferimento: Riferimento al proxy di risorsa personalizzatoReference: Custom Resource Proxy Reference](proxy-resource-endpoint-reference.md)
- [Riferimento: Riferimento alla cache delle risorse personalizzataReference: Custom Resource Cache Reference](proxy-cache-resource-endpoint-reference.md)
