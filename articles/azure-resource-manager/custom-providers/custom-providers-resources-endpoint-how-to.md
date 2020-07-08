---
title: Aggiunta di risorse personalizzate all'API REST di Azure
description: Informazioni su come aggiungere risorse personalizzate all'API REST di Azure. In questo articolo vengono illustrati i requisiti e le procedure consigliate per gli endpoint che desiderano implementare risorse personalizzate.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75650526"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Aggiunta di risorse personalizzate all'API REST di Azure

Questo articolo illustra i requisiti e le procedure consigliate per la creazione di endpoint del provider di risorse personalizzati di Azure che implementano risorse personalizzate. Se non si ha familiarità con i provider di risorse personalizzati di Azure, vedere [la panoramica sui provider di risorse personalizzati](overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Come definire un endpoint di risorsa

Un **endpoint** è un URL che punta a un servizio che implementa il contratto sottostante tra l'IT e Azure. L'endpoint è definito nel provider di risorse personalizzato e può essere qualsiasi URL accessibile pubblicamente. Nell'esempio seguente è presente un oggetto **ResourceType** chiamato `myCustomResource` implementato da `endpointURL` .

**ResourceProvider**di esempio:

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

## <a name="building-a-resource-endpoint"></a>Compilazione di un endpoint di risorsa

Un **endpoint** che implementa un oggetto **ResourceType** deve gestire la richiesta e la risposta per la nuova API in Azure. Quando viene creato un provider di risorse personalizzato con un oggetto **ResourceType** , viene generato un nuovo set di API in Azure. In questo caso, il **ResourceType** genererà una nuova API risorse di Azure per `PUT` , `GET` e `DELETE` per eseguire CRUD su una singola risorsa, oltre che `GET` per recuperare tutte le risorse esistenti:

Modificare una singola risorsa ( `PUT` , `GET` e `DELETE` ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Recupera tutte le risorse ( `GET` ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Per le risorse personalizzate, i provider di risorse personalizzati offrono due tipi di **routingTypes**: " `Proxy` " e " `Proxy, Cache` ".

### <a name="proxy-routing-type"></a>tipo di routing proxy

" `Proxy` " **RoutingType** proxy tutti i metodi di richiesta all' **endpoint** specificato nel provider di risorse personalizzato. Quando usare " `Proxy` ":

- Il controllo completo sulla risposta è necessario.
- Integrazione dei sistemi con le risorse esistenti.

Per ulteriori informazioni sulle `Proxy` risorse "", vedere [il riferimento al proxy di risorsa personalizzato](proxy-resource-endpoint-reference.md) .

### <a name="proxy-cache-routing-type"></a>tipo di routing della cache proxy

" `Proxy, Cache` " **RoutingType** solo proxy `PUT` e `DELETE` metodi di richiesta all' **endpoint** specificato nel provider di risorse personalizzato. Il provider di risorse personalizzato restituirà automaticamente `GET` le richieste in base al contenuto archiviato nella cache. Se una risorsa personalizzata è contrassegnata con la cache, anche il provider di risorse personalizzato aggiunge/sovrascrive i campi nella risposta per rendere le API conformi ad Azure. Quando usare " `Proxy, Cache` ":

- Creazione di un nuovo sistema privo di risorse esistenti.
- Usare l'ecosistema di Azure esistente.

Per ulteriori informazioni sulle `Proxy, Cache` risorse "", vedere [il riferimento alla cache delle risorse personalizzate](proxy-cache-resource-endpoint-reference.md) .

## <a name="creating-a-custom-resource"></a>Creazione di una risorsa personalizzata

Esistono due modi principali per creare una risorsa personalizzata da un provider di risorse personalizzato:

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
id | *Sì* | ID della risorsa personalizzata. Questo dovrebbe esistere all'esterno del **ResourceProvider**
properties | *Sì* | Il corpo della richiesta che verrà inviato all' **endpoint**.

Eliminare una risorsa personalizzata di Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametro | Obbligatoria | Descrizione
---|---|---
id | *Sì* | ID della risorsa personalizzata. Questa operazione deve essere disattivata da **ResourceProvider**.

Recuperare una risorsa personalizzata di Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametro | Obbligatoria | Descrizione
---|---|---
id | *Sì* | ID della risorsa personalizzata. Questo dovrebbe esistere all'esterno del **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

> [!NOTE]
> Per le risorse è necessario che la risposta contenga un oggetto appropriato `id` , `name` e `type` dall' **endpoint**.

Azure Resource Manager modelli richiedono che `id` , `name` e `type` siano restituiti correttamente dall'endpoint downstream. Una risposta di risorsa restituita deve essere nel formato seguente:

Risposta dell' **endpoint** di esempio:

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
resourceTypeName | *Sì* | **Nome** del **ResourceType** definito nel provider personalizzato.
resourceProviderName | *Sì* | Nome dell'istanza del provider di risorse personalizzato.
customResourceName | *Sì* | Nome della risorsa personalizzata.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica sui provider di risorse personalizzati di Azure](overview.md)
- [Guida introduttiva: creare un provider di risorse personalizzato di Azure e distribuire risorse personalizzate](./create-custom-provider.md)
- [Esercitazione: creare azioni e risorse personalizzate in Azure](./tutorial-get-started-with-custom-providers.md)
- [Procedura: aggiungere azioni personalizzate all'API REST di Azure](./custom-providers-action-endpoint-how-to.md)
- [Riferimento: riferimento al proxy di risorsa personalizzato](proxy-resource-endpoint-reference.md)
- [Riferimento: informazioni di riferimento sulla cache delle risorse personalizzate](proxy-cache-resource-endpoint-reference.md)
