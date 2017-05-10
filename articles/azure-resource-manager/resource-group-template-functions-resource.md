---
title: Funzioni del modello di Azure Resource Manager | Documentazione Microsoft
description: Informazioni sulle funzioni da usare in un modello di Azure Resource Manager per recuperare i valori relativi alle risorse.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 66c71906614e5d0c8e8531271444fc59a5cb779f
ms.contentlocale: it-it
ms.lasthandoff: 04/28/2017


---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Funzioni delle risorse per i modelli di Azure Resource Manager

Gestione risorse fornisce le funzioni seguenti per ottenere i valori delle risorse:

* [listKeys e list{Value}](#listkeys)
* [provider](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [sottoscrizione](#subscription)

Per ottenere valori dai parametri, dalle variabili o dalla distribuzione corrente, vedere [Funzioni dei valori della distribuzione](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="listkeys-and-listvalue"></a>listKeys e list{Value}
`listKeys(resourceName or resourceIdentifier, apiVersion)`

`list{Value}(resourceName or resourceIdentifier, apiVersion)`

Restituisce i valori per qualsiasi tipo di risorsa che supporta l'operazione di tipo elenco. L'uso più comune è rappresentato da `listKeys`. 

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| resourceName o resourceIdentifier |Sì |string |Identificatore univoco della risorsa. |
| apiVersion |Sì |string |Versione dell'API dello stato di runtime della risorsa. In genere il formato è **aaaa-mm-gg**. |

### <a name="remarks"></a>Osservazioni

Qualsiasi operazione che inizia con **list** può essere usata come funzione nel modello. Le operazioni disponibili non includono solo listKeys, ma anche operazioni come `list`, `listAdminKeys` e `listStatus`. Per determinare quali tipi di risorse dispongono di un'operazione list, usare le opzioni seguenti:

* Visualizzare le [operazioni dell'API REST](/rest/api/) di un provider di risorse e individuare le operazioni list. Gli account di archiviazione, ad esempio, dispongono dell'[operazione listKeys](/rest/api/storagerp/storageaccounts#StorageAccounts_ListKeys).
* Usare il cmdlet di PowerShell [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation). L'esempio seguente ottiene tutte le operazioni list degli account di archiviazione:

  ```powershell
  Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Usare il comando seguente dell'interfaccia della riga di comando di Azure e l'utilità JSON [jq](http://stedolan.github.io/jq/download/) per filtrare solo le operazioni list:

  ```azurecli
  azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains(\"list\"))"
  ```

Specificare la risorsa usando la [funzione resourceId](#resourceid) o il formato `{providerNamespace}/{resourceType}/{resourceName}`.

### <a name="examples"></a>esempi

L'esempio seguente mostra come restituire le chiavi primaria e secondaria da un account di archiviazione nella sezione outputs.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountId": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "storageKeysOutput": {
            "value": "[listKeys(parameters('storageAccountId'), '2016-01-01')]",
            "type" : "object"
        }
    }
}
``` 

### <a name="return-value"></a>Valore restituito

L'oggetto restituito da listKeys è nel formato seguente:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Altre funzioni list possono avere formati di restituzione diversi. Per visualizzare il formato di una funzione, includerlo nella sezione outputs, come mostrato nel modello di esempio. 

<a id="providers" />

## <a name="providers"></a>provider
`providers(providerNamespace, [resourceType])`

Restituisce informazioni su un provider di risorse e i relativi tipi di risorse supportati. Se non si specifica un tipo di risorsa, la funzione restituisce tutti i tipi supportati per il provider di risorse.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| providerNamespace |Sì |string |Spazio dei nomi del provider |
| resourceType |No |string |Il tipo di risorsa all'interno dello spazio dei nomi specificato. |

### <a name="examples"></a>esempi

L'esempio seguente mostra come usare la funzione provider:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
            "type" : "object"
        }
    }
}
```

### <a name="return-value"></a>Valore restituito

Ogni tipo supportato viene restituito nel formato seguente: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

L'ordine della matrice dei valori restituiti non è garantito.

<a id="reference" />

## <a name="reference"></a>reference
`reference(resourceName or resourceIdentifier, [apiVersion])`

Restituisce un oggetto che rappresenta lo stato di runtime di una risorsa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| resourceName o resourceIdentifier |Sì |string |Nome o identificatore univoco di una risorsa. |
| apiVersion |No |string |Versione dell'API della risorsa specificata. Includere questo parametro quando non viene effettuato il provisioning della risorsa nello stesso modello. In genere il formato è **aaaa-mm-gg**. |

### <a name="remarks"></a>Osservazioni

La funzione reference deriva il proprio valore da uno stato di runtime, quindi non può essere usata nella sezione variables. Può essere usata, invece, nella sezione outputs di un modello. 

Usando la funzione di riferimento, si dichiara implicitamente che una risorsa dipende da un'altra se il provisioning della risorsa cui si fa riferimento viene effettuato nello stesso modello. Non è necessario usare anche la proprietà dependsOn. La funzione non viene valutata fino a quando la risorsa cui si fa riferimento ha completato la distribuzione.

Per visualizzare i nomi e i valori delle proprietà per un tipo di risorsa, creare un modello che restituisca l'oggetto nella sezione outputs. Se si dispone di una risorsa esistente di quel tipo, il modello restituisce l'oggetto senza distribuire nuove risorse. 

### <a name="examples"></a>esempi

L'esempio seguente fa riferimento a un account di archiviazione non distribuito in questo modello, ma esistente nello stesso gruppo di risorse.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }
}
```

In alternativa, è possibile distribuire e far riferimento alla risorsa nello stesso modello.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      }
    }
}
``` 

In genere, la funzione di riferimento viene usata per restituire un valore specifico da un oggetto, ad esempio l'URI dell'endpoint BLOB o il nome di dominio completo.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

### <a name="return-value"></a>Valore restituito

Ogni tipo di risorsa restituisce proprietà diverse per la funzione di riferimento. La funzione non restituisce un singolo formato predefinito. Per visualizzare le proprietà per un tipo di risorsa, restituire l'oggetto nella sezione output, come illustrato nell'esempio.

<a id="resourcegroup" />

## <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

Restituisce un oggetto che rappresenta il gruppo di risorse corrente. 

### <a name="examples"></a>esempi

Il modello seguente restituisce le proprietà del gruppo di risorse.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

Un utilizzo comune della funzione resourceGroup consiste nel creare risorse nello stesso percorso del gruppo di risorse. L'esempio seguente usa il percorso del gruppo di risorse per assegnare il percorso per un sito Web.

```json
"resources": [
   {
      "apiVersion": "2014-06-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="return-value"></a>Valore restituito

L'oggetto restituito è nel formato seguente:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

<a id="resourceid" />

## <a name="resourceid"></a>resourceId
`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Restituisce l'identificatore univoco di una risorsa. Questa funzione viene usata quando il nome della risorsa è ambiguo o non è stato sottoposto a provisioning all'interno dello stesso modello. 

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Tipo | Descrizione |
|:--- |:--- |:--- |:--- |
| subscriptionId |No |Stringa (in formato GUID) |Il valore predefinito è la sottoscrizione corrente. Specificare questo valore quando si vuole recuperare una risorsa in un'altra sottoscrizione. |
| resourceGroupName |No |string |Il valore predefinito è il gruppo di risorse corrente. Specificare questo valore quando si vuole recuperare una risorsa in un altro gruppo di risorse. |
| resourceType |Sì |string |Tipo di risorsa, incluso lo spazio dei nomi del provider di risorse. |
| resourceName1 |Sì |string |Nome della risorsa. |
| resourceName2 |No |string |Segmento successivo del nome della risorsa se la risorsa è annidata. |

### <a name="examples"></a>esempi

L'esempio seguente restituisce l'ID della risorsa per un account di archiviazione nel gruppo di risorse:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceIdOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        }
    }
}
```

L'esempio seguente mostra come recuperare gli ID di risorsa di un sito Web in un gruppo di risorse diverso e un database in un gruppo di risorse diverso:

```json
[resourceId('otherResourceGroup', 'Microsoft.Web/sites', parameters('siteName'))]
[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
```

Spesso è necessario usare questa funzione quando si usa un account di archiviazione o una rete virtuale in un gruppo di risorse alternative. L'account di archiviazione o la rete virtuale possono essere usati in più gruppi di risorse, quindi non devono essere eliminati quando si elimina un singolo gruppo di risorse. L'esempio seguente mostra come usare facilmente una risorsa di un gruppo di risorse esterno:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="return-value"></a>Valore restituito

L'identificatore viene restituito nel formato seguente:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

<a id="subscription" />

## <a name="subscription"></a>sottoscrizione
`subscription()`

Restituisce i dettagli sulla sottoscrizione per la distribuzione corrente. 

### <a name="examples"></a>esempi

L'esempio seguente mostra la funzione subscription chiamata nella sezione outputs. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

### <a name="return-value"></a>Valore restituito

La funzione restituisce il formato seguente:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>Passaggi successivi
* Per una descrizione delle sezioni in un modello Azure Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per unire più modelli, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Per eseguire un'iterazione di un numero di volte specificato durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).
* Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Azure Resource Manager](resource-group-template-deploy.md).


