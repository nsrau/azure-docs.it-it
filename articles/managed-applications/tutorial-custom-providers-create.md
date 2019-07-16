---
title: Creare e usare un provider personalizzato di Azure
description: Questa esercitazione illustra come creare e utilizzare un provider personalizzato.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 65a8e60d8216e1da16af987c9e699e24ecaec3ec
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799130"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Creazione di un endpoint RESTful per provider personalizzati

I provider personalizzati consentono di personalizzare i flussi di lavoro in Azure. Un provider personalizzato è un contratto tra Azure e un `endpoint`. Questa esercitazione illustra il processo di creazione di un provider personalizzato. Se non si ha familiarità con i provider personalizzati di Azure, vedere la [panoramica dei provider di risorse personalizzati](./custom-providers-overview.md).

Questa esercitazione è suddivisa nei passaggi seguenti:

- Definizione di un provider personalizzato
- Definizione di azioni e risorse personalizzate
- Distribuzione del provider personalizzato

Questa esercitazione si basa sulle esercitazioni seguenti:

- [Creazione di un endpoint RESTful per provider personalizzati](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-a-custom-provider"></a>Creazione di un provider personalizzato

> [!NOTE]
> Questa esercitazione non illustra la creazione di un endpoint. Seguire l'[esercitazione sulla creazione di endpoint RESTful](./tutorial-custom-providers-function-authoring.md) se non è disponibile un endpoint RESTful.

Dopo la creazione dell'`endpoint`, è possibile creare un provider personalizzato per generare un contratto tra il provider e l'`endpoint`. Un provider personalizzato consente di specificare un elenco di definizioni di endpoint.

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Proprietà | Obbligatoria | DESCRIZIONE
---|---|---
name | *sì* | Nome della definizione dell'endpoint. Azure esporrà tale nome tramite l'API in '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *no* | Determina il tipo di contratto con l'`endpoint`. Se non viene specificato, per impostazione predefinita sarà "Proxy".
endpoint | *sì* | Endpoint a cui indirizzare le richieste. Gestirà la risposta, nonché eventuali effetti collaterali della richiesta.

In questo caso, l'`endpoint` è l'URL del trigger della funzione di Azure. `<yourapp>`, `<funcname>` e `<functionkey>` devono essere sostituiti con i valori della funzione creata.

## <a name="defining-custom-actions-and-resources"></a>Definizione di azioni e risorse personalizzate

Il provider personalizzato contiene un elenco di definizioni di endpoint modellate in `actions` e `resourceTypes`. Le `actions` sono associate alle azioni personalizzate esposte dal provider personalizzato, mentre i `resourceTypes` sono le risorse personalizzate. Per questa esercitazione verrà definito un provider personalizzato con una `action` denominata `myCustomAction` e un `resourceType` denominato `myCustomResources`.

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

Sostituire `endpoint` con l'URL del trigger della funzione creata nell'esercitazione precedente.

## <a name="deploying-the-custom-provider"></a>Distribuzione del provider personalizzato

> [!NOTE]
> L'`endpoint` deve essere sostituito dall'URL della funzione.

Per distribuire il provider personalizzato descritto in precedenza, è possibile usare un modello di Azure Resource Manager.

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="using-custom-actions-and-resources"></a>Uso di azioni e risorse personalizzate

Dopo aver creato un provider personalizzato, è possibile utilizzare le nuove API di Azure. La sezione successiva illustra come chiamare e utilizzare un provider personalizzato.

### <a name="custom-actions"></a>Azioni personalizzate

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` e `{resourceGroupName}` devono essere sostituiti con la sottoscrizione e il gruppo di risorse in cui è stato distribuito il provider personalizzato.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parametro | Obbligatoria | DESCRIZIONE
---|---|---
action | *sì* | Nome dell'azione definita nel provider personalizzato creato.
ids | *sì* | ID risorsa del provider personalizzato creato.
request-body | *no* | Corpo della richiesta che verrà inviato all'`endpoint`.

# <a name="templatetabtemplate"></a>[Modello](#tab/template)

No.

---

### <a name="custom-resources"></a>Risorse personalizzate

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` e `{resourceGroupName}` devono essere sostituiti con la sottoscrizione e il gruppo di risorse in cui è stato distribuito il provider personalizzato.

Creare una risorsa personalizzata:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

Parametro | Obbligatoria | DESCRIZIONE
---|---|---
is-full-object | *sì* | Indica che l'oggetto proprietà include altre opzioni, come location, tags, sku, e/o plan.
id | *sì* | ID della risorsa personalizzata. Deve esistere indipendentemente dal provider personalizzato creato.
properties | *sì* | Corpo della richiesta che verrà inviato all'`endpoint`.

Eliminare una risorsa personalizzata di Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametro | Obbligatoria | DESCRIZIONE
---|---|---
id | *sì* | ID della risorsa personalizzata. Deve esistere indipendentemente dal provider personalizzato creato.

Recuperare una risorsa personalizzata di Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametro | Obbligatoria | DESCRIZIONE
---|---|---
id | *sì* | ID della risorsa personalizzata. Deve esistere indipendentemente dal provider personalizzato creato.

# <a name="templatetabtemplate"></a>[Modello](#tab/template)

Modello di Azure Resource Manager di esempio:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

Parametro | Obbligatoria | DESCRIZIONE
---|---|---
resourceTypeName | *sì* | `name` del parametro *resourceType* definito nel provider personalizzato.
resourceProviderName | *sì* | Nome dell'istanza del provider personalizzato.
customResourceName | *sì* | Nome della risorsa personalizzata.

---

> [!NOTE]
> Dopo aver completato la distribuzione e aver usato il provider personalizzato, ricordarsi di pulire le eventuali risorse create, inclusa la funzione di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni sui provider personalizzati. Passare all'articolo successivo per creare un provider personalizzato.

- [Procedure: Aggiunta di azioni personalizzate all'API REST di Azure](./custom-providers-action-endpoint-how-to.md)
- [Procedure: Aggiunta di risorse personalizzate all'API REST di Azure](./custom-providers-resources-endpoint-how-to.md)
