---
title: Aggiunta di risorse personalizzate all'API REST di Azure
description: Informazioni su come aggiungere risorse personalizzate per l'API REST di Azure. Questo articolo verrà illustrata i requisiti e procedure consigliate per gli endpoint che vogliono implementare risorse personalizzate.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: a3cd1fe69a0d99f9faf3a451f76a3a420d713711
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795210"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Aggiunta di risorse personalizzate all'API REST di Azure

Questo articolo verrà esaminati i requisiti e procedure consigliate per la creazione di endpoint del Provider di risorse di Azure personalizzata che implementa le risorse personalizzate. Se non si ha familiarità con i provider di risorse personalizzati di Azure, vedere [la panoramica sul provider di risorse personalizzati](./custom-providers-overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Come definire un endpoint di risorse

Un' **endpoint** è un URL che punta a un servizio che implementa il contratto sottostante tra i dati e Azure. L'endpoint è definito nel provider di risorse personalizzato e può essere qualsiasi URL pubblicamente accessibile. L'esempio seguente include un' **resourceType** chiamato `myCustomResource` implementata da `endpointURL`.

Campione **ResourceProvider**:

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

## <a name="building-a-resource-endpoint"></a>Creazione di un endpoint di risorse

Un' **endpoint** che implementa un' **resourceType** deve gestire la richiesta e risposta per la nuova API in Azure. Quando un provider di risorse personalizzato con un **resourceType** viene creato, verrà generato un nuovo set di API in Azure. In questo caso, il **resourceType** genererà una nuova risorsa di Azure API per `PUT`, `GET`, e `DELETE` per eseguire operazioni su una sola risorsa, nonché `GET` per recuperare tutte le risorse esistenti:

Modifica risorse singola (`PUT`, `GET`, e `DELETE`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Recuperare tutte le risorse (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Per le risorse personalizzate, provider di risorse personalizzati sono disponibili due tipi di **routingTypes**: "`Proxy`"e"`Proxy, Cache`".

### <a name="proxy-routing-type"></a>tipo di routing del proxy

Il "`Proxy`" **routingType** proxy tutte le richieste di metodi per il **endpoint** specificato nel provider di risorse personalizzati. Quando usare "`Proxy`":

- È necessario un controllo completo sulla risposta.
- L'integrazione di sistemi con risorse esistenti.

Per altre informazioni su "`Proxy`" le risorse, vedere [il riferimento di risorsa personalizzata proxy](./custom-providers-proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>tipo di routing di proxy della cache

Il "`Proxy, Cache`" **routingType** solo i proxy `PUT` e `DELETE` richiedere metodi per i **endpoint** specificato nel provider di risorse personalizzato. Il provider di risorse personalizzato restituirà automaticamente `GET` le richieste basate su ciò che ha memorizzato nella cache. Se una risorsa personalizzata è contrassegnata con la cache, il provider di risorse personalizzato verrà anche aggiunta / sovrascrivere i campi nella risposta per rendere conforme il API di Azure. Quando usare "`Proxy, Cache`":

- Creazione di un nuovo sistema non con le risorse esistenti.
- Funziona con l'ecosistema di Azure esistente.

Per altre informazioni su "`Proxy, Cache`" le risorse, vedere [il riferimento di risorsa personalizzata della cache](./custom-providers-proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Creazione di una risorsa personalizzata

Esistono due modi principali per la creazione di una risorsa personalizzata di fuori di un provider di risorse personalizzate:

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
is-full-object | *Sì* | Indica che l'oggetto delle proprietà include altre opzioni, ad esempio percorso, i tag, sku e/o piano.
id | *Sì* | L'ID risorsa della risorsa personalizzata. Questo deve essere disponibile all'esterno del **ResourceProvider**
properties | *Sì* | Il corpo della richiesta che verrà inviato il **endpoint**.

Eliminare una risorsa personalizzata di Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametro | Obbligatoria | DESCRIZIONE
---|---|---
id | *Sì* | L'ID risorsa della risorsa personalizzata. Questo deve essere disponibile all'esterno della **ResourceProvider**.

Recuperare una risorsa personalizzata di Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametro | Obbligatoria | Descrizione
---|---|---
id | *Sì* | L'ID risorsa della risorsa personalizzata. Questo deve essere disponibile all'esterno del **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

> [!NOTE]
> Le risorse richiedono che la risposta contiene un oggetto appropriato `id`, `name`, e `type` dal **endpoint**.

I modelli di Azure Resource Manager richiede che `id`, `name`, e `type` sono stati restituiti correttamente dall'endpoint di downstream. Una risposta per la risorsa restituita deve essere nel formato:

Campione **endpoint** risposta:

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

Esempio modello di gestione risorse di Azure:

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
resourceTypeName | *Sì* | Il **name** delle **resourceType** definito nel provider personalizzato.
resourceProviderName | *Sì* | Il nome di istanza del provider di risorse personalizzato.
customResourceName | *Sì* | Il nome di risorsa personalizzata.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica sui provider di risorse personalizzati di Azure](./custom-providers-overview.md)
- [Avvio rapido: Creare il Provider di risorse personalizzati di Azure e distribuire le risorse personalizzate](./create-custom-provider.md)
- [Esercitazione: Creare azioni personalizzate e le risorse in Azure](./tutorial-custom-providers-101.md)
- [Procedure: Aggiunta di azioni personalizzate all'API REST di Azure](./custom-providers-action-endpoint-how-to.md)
- [Informazioni di riferimento: Riferimento a una risorsa personalizzata Proxy](./custom-providers-proxy-resource-endpoint-reference.md)
- [Informazioni di riferimento: Riferimento a una risorsa personalizzata della Cache](./custom-providers-proxy-cache-resource-endpoint-reference.md)
