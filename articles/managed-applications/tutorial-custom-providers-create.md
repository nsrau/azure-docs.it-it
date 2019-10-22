---
title: Creare e usare un provider personalizzato
description: Questa esercitazione illustra come creare e usare un provider personalizzato di Azure. Usare i provider personalizzati per cambiare i flussi di lavoro in Azure.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 6217e9007f20cb365aff0837f58f1a6074a3e6ce
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330048"
---
# <a name="create-and-use-a-custom-provider"></a>Creare e usare un provider personalizzato

Un provider personalizzato è un contratto tra Azure e un endpoint. Con i provider personalizzati, è possibile cambiare i flussi di lavoro in Azure. Questa esercitazione illustra il processo di creazione di un provider personalizzato. Se non si ha familiarità con i provider personalizzati di Azure, vedere la [panoramica dei provider di risorse personalizzati di Azure](./custom-providers-overview.md).

## <a name="create-a-custom-provider"></a>Creare un provider personalizzato

> [!NOTE]
> Questa esercitazione non mostra come creare un endpoint. Se non è disponibile un endpoint RESTFUL, seguire l'[esercitazione sulla creazione di endpoint RESTful](./tutorial-custom-providers-function-authoring.md), che rappresenta la base per l'esercitazione corrente.

Dopo aver creato un endpoint, è possibile creare un provider personalizzato per generare un contratto tra il provider e l'endpoint. Con un provider personalizzato, è possibile specificare un elenco di definizioni di endpoint:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Proprietà | Obbligatoria | DESCRIZIONE
---|---|---
**nome** | Sì | Nome della definizione dell'endpoint. Azure espone questo nome tramite l'API in /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**routingType** | No | Il tipo di contratto dell'endpoint. Se non si specifica un valore, viene usata l'impostazione predefinita "Proxy".
**endpoint** | Sì | Endpoint a cui indirizzare le richieste. Gestisce la risposta, nonché eventuali effetti collaterali della richiesta.

Il valore di **endpoint** è l'URL del trigger dell'app per le funzioni di Azure. I segnaposto `<yourapp>`, `<funcname>` e `<functionkey>` devono essere sostituiti con i valori dell'app per le funzioni creata.

## <a name="define-custom-actions-and-resources"></a>Definire azioni e risorse personalizzate

Il provider personalizzato contiene un elenco di definizioni di endpoint modellate nelle proprietà **actions** e **resourceTypes**. La proprietà **actions** corrisponde alle azioni personalizzate esposte dal provider personalizzato, mentre la proprietà **resourceTypes** rappresenta le risorse personalizzate. In questa esercitazione il provider personalizzato include una proprietà **actions** denominata `myCustomAction` e una proprietà **resourceTypes** denominata `myCustomResources`.

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

## <a name="deploy-the-custom-provider"></a>Distribuire il provider personalizzato

> [!NOTE]
> È necessario sostituire i valori di **endpoint** con l'URL del trigger dell'app per le funzioni creata nell'esercitazione precedente.

Per distribuire il provider personalizzato descritto in precedenza, è possibile usare un modello di Azure Resource Manager:

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

## <a name="use-custom-actions-and-resources"></a>Usare azioni e risorse personalizzate

Dopo aver creato un provider personalizzato, è possibile usare le nuove API di Azure. Le schede seguenti illustrano come chiamare e usare un provider personalizzato.

### <a name="custom-actions"></a>Azioni personalizzate

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

> [!NOTE]
> È necessario sostituire i segnaposto `{subscriptionId}` e `{resourceGroupName}` con la sottoscrizione e il gruppo di risorse in cui è stato distribuito il provider personalizzato.

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
*action* | Sì | Il nome dell'azione definita nel provider personalizzato
*ids* | Sì | L'ID risorsa del provider personalizzato
*request-body* | No | Il corpo della richiesta che verrà inviato all'endpoint

# <a name="templatetabtemplate"></a>[Modello](#tab/template)

No.

---

### <a name="custom-resources"></a>Risorse personalizzate

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

> [!NOTE]
> È necessario sostituire i segnaposto `{subscriptionId}` e `{resourceGroupName}` con la sottoscrizione e il gruppo di risorse in cui è stato distribuito il provider personalizzato.

#### <a name="create-a-custom-resource"></a>Creare una risorsa personalizzata

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
*is-full-object* | Sì | Indica se l'oggetto proprietà include altre opzioni, come posizione, tag, SKU o piano.
*id* | Sì | ID della risorsa personalizzata. Questo ID è un'estensione dell'ID risorsa del provider personalizzato.
*properties* | Sì | Il corpo della richiesta che verrà inviato all'endpoint.

#### <a name="delete-a-custom-resource"></a>Eliminare una risorsa personalizzata

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametro | Obbligatoria | DESCRIZIONE
---|---|---
*id* | Sì | ID della risorsa personalizzata. Questo ID è un'estensione dell'ID risorsa del provider personalizzato.

#### <a name="retrieve-a-custom-resource"></a>Recuperare una risorsa personalizzata

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametro | Obbligatoria | DESCRIZIONE
---|---|---
*id* | Sì | ID della risorsa personalizzata. Questo ID è un'estensione dell'ID risorsa del provider personalizzato.

# <a name="templatetabtemplate"></a>[Modello](#tab/template)

Esempio di modello di Resource Manager:

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
*resourceTypeName* | Sì | Il valore `name` della proprietà **resourceType** definita nel provider personalizzato.
*resourceProviderName* | Sì | Nome dell'istanza del provider personalizzato.
*customResourceName* | Sì | Nome della risorsa personalizzata.

---

> [!NOTE]
> Dopo aver completato la distribuzione e aver usato il provider personalizzato, ricordarsi di pulire tutte le risorse create, inclusa l'app per le funzioni di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni sui provider personalizzati. Per altre informazioni, vedere:

- [Procedura: Aggiunta di azioni personalizzate all'API REST di Azure](./custom-providers-action-endpoint-how-to.md)
- [Procedura: Aggiunta di risorse personalizzate all'API REST di Azure](./custom-providers-resources-endpoint-how-to.md)
