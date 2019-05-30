---
title: Collegare modelli per la distribuzione di Azure | Microsoft Docs
description: Descrive come usare i modelli collegati in un modello di Azure Resource Manager per creare una soluzione basata su un modello modulare. Mostra come passare i valori dei parametri, specificare un file di parametri e gli URL creati in modo dinamico.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2019
ms.author: tomfitz
ms.openlocfilehash: 95044373800441bdcc04bdb84e8485dce29f11e7
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357412"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Uso di modelli collegati e annidati nella distribuzione di risorse di Azure

Per distribuire la soluzione, è possibile usare un modello singolo o un modello principale con molti modelli correlati. Il modello correlato può essere un file separato collegato dal modello principale o un modello che viene annidato all'interno del modello principale.

Per le piccole e medie soluzioni, un modello singolo è più facile da comprendere e gestire. È possibile vedere tutti i valori e le risorse in un unico file. Per gli scenari avanzati, i modelli collegati consentono di suddividere la soluzione in componenti di destinazione, oltre che di riutilizzare i modelli.

Quando si usano modelli collegati, si crea un modello principale che riceve i valori dei parametri durante la distribuzione. Il modello principale contiene tutti i modelli collegati e passa i valori a tali modelli in base alle esigenze.

Per un'esercitazione, vedere [Esercitazione: Creare modelli collegati di Azure Resource Manager](./resource-manager-tutorial-create-linked-templates.md).

> [!NOTE]
> Per modelli collegati o annidati, è possibile usare solo la modalità di distribuzione [Incrementale](deployment-modes.md).
>

## <a name="link-or-nest-a-template"></a>Collegare o annidare un modello

Per stabilire un collegamento a un altro modello, aggiungere una risorsa **deployments** al modello principale.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
        "mode": "Incremental",
        <nested-template-or-external-template>
    }
  }
]
```

Le proprietà fornite per la risorsa di distribuzione variano in base al fatto che si stia stabilendo un collegamento a un modello esterno o annidando un modello inline nel modello principale.

### <a name="nested-template"></a>Modello annidato

Per annidare il modello all'interno del modello principale, usare la proprietà **template** e specificare la sintassi del modello.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "nestedTemplate",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[variables('storageName')]",
            "location": "West US",
            "kind": "StorageV2",
            "sku": {
                "name": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> Per i modelli annidati non è possibile usare i parametri o le variabili definiti all'interno del modello annidato. È possibile usare i parametri e variabili dal modello principale. Nell'esempio precedente `[variables('storageName')]` recupera un valore dal modello principale e non dal modello annidato. Questa restrizione non è valida per i modelli esterni.
>
> Per due risorse definiti all'interno di un modello annidato e una risorsa dipende l'altro, il valore della dipendenza è semplicemente il nome della risorsa dipendente:
> ```json
> "dependsOn": [
>   "[variables('storageAccountName')]"
> ],
> ```
>
> Non è possibile usare la funzione `reference` nella sezione outputs di un modello annidato. Per restituire i valori per una risorsa distribuita in un modello annidato, convertire il modello annidato in un modello collegato.

Per il modello annidato è necessario specificare le [stesse proprietà](resource-group-authoring-templates.md) del modello standard.

### <a name="external-template-and-external-parameters"></a>Modello esterno e parametri esterni

Per collegare un modello esterno e un file di parametri, usare **templateLink** e **parametersLink**. Quando si stabilisce un collegamento a un modello, il servizio Resource Manager deve potervi accedere. Non è possibile specificare un file locale o un file disponibile solo nella rete locale. È possibile fornire solo un valore URI che includa **http** o **https**. È possibile inserire il modello collegato in un account di archiviazione e usare l'URI per tale elemento.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
    "mode": "Incremental",
    "templateLink": {
        "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion":"1.0.0.0"
    },
    "parametersLink": {
        "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion":"1.0.0.0"
    }
    }
  }
]
```

Non è necessario specificare la proprietà `contentVersion` per il modello o i parametri. Se non si specifica un valore per la versione del contenuto, viene distribuita la versione corrente del modello. Se si specifica un valore per la versione del contenuto, questa deve corrispondere alla versione del modello collegato. In caso contrario, la distribuzione ha esito negativo con un errore.

### <a name="external-template-and-inline-parameters"></a>Modello esterno e parametri inline

In alternativa, è possibile fornire il parametro inline. Non è possibile usare i parametri inline e un collegamento a un file di parametri. La distribuzione ha esito negativo con un errore quando vengono specificati sia `parametersLink` che `parameters`.

Per passare un valore dal modello principale al modello collegato, usare **parameters**.

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2018-05-01",
     "name": "linkedTemplate",
     "properties": {
       "mode": "Incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parameters": {
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
        }
     }
  }
]
```

## <a name="using-copy"></a>Tramite copia

Per creare più istanze di una risorsa con un modello annidato, aggiungere l'elemento di copia a livello dei **Resources/Deployments** risorsa.

Il modello di esempio seguente viene illustrato come usare copy con un modello annidato.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy":{
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "kind": "StorageV2",
            "sku": {
              "name": "Standard_LRS"
            }
            // no, copy doesn't work here
            //"copy":{
            //  "name": "storagecopy",
            //  "count": 2
            //}
          }
        ]
      }
    }
  }
]
```

## <a name="using-variables-to-link-templates"></a>Uso di variabili per collegare i modelli

Negli esempi precedenti sono illustrati i valori di URL hard-coded per i collegamenti del modello. Questo approccio può funzionare per un modello semplice, ma non funziona correttamente se si usa un set di grandi dimensioni di modelli modulari. In alternativa, è possibile creare una variabile statica contenente un URL di base per il modello principale e quindi creare dinamicamente gli URL per i modelli collegati da tale URL di base. Il vantaggio di questo approccio è rappresentato dal fatto che risulta più semplice spostare o scomporre il modello perché è sufficiente modificare la variabile statica nel modello principale. Il modello principale passa gli URI corretti a tutto il modello scomposto.

Nell'esempio seguente viene illustrato come usare un URL di base per creare due URL per i modelli collegati (**sharedTemplateUrl** e **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

È inoltre possibile usare [deployment ()](resource-group-template-functions-deployment.md#deployment) per ottenere l'URL di base per il modello corrente e usarlo per ottenere l'URL per altri modelli nello stesso percorso. Questo approccio è utile se la posizione del modello cambia o si vuole evitare la codifica di URL nel file del modello. La proprietà templateLink viene restituita solo durante il collegamento a un modello remoto con un URL. Se si usa un modello locale, tale proprietà non è disponibile.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Ottenere valori dal modello collegato

Per ottenere un valore di output da un modello collegato, recuperare il valore della proprietà con una sintassi analoga a: `"[reference('deploymentName').outputs.propertyName.value]"`.

Quando si ottiene una proprietà di output da un modello collegato, il nome della proprietà non può includere un trattino.

Gli esempi seguenti illustrano come fare riferimento a un modello collegato e recuperare un valore di output. Il modello collegato restituisce un messaggio semplice.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "greetingMessage": {
            "value": "Hello World",
            "type" : "string"
        }
    }
}
```

Il modello principale distribuisce il modello collegato e ottiene il valore restituito. Si noti che fa riferimento alla risorsa di distribuzione in base al nome e usa il nome della proprietà restituito dal modello collegato.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
                    "contentVersion": "1.0.0.0"
                }
            }
        }
    ],
    "outputs": {
        "messageFromLinkedTemplate": {
            "type": "string",
            "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
        }
    }
}
```

Come altri tipi di risorse, è possibile impostare le dipendenze tra il modello collegato e altre risorse. Pertanto, quando le altre risorse richiedono un valore di output presente nel modello collegato, accertarsi di distribuire il modello collegato prima di queste risorse. Viceversa, quando il modello collegato dipende da altre risorse, accertarsi di distribuire le altre risorse prima di questo modello.

L'esempio seguente mostra un modello che distribuisce un indirizzo IP pubblico e restituisce l'ID di risorsa:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
            "location": "eastus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "resourceID": {
            "type": "string",
            "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
        }
    }
}
```

Per usare l'indirizzo IP pubblico del modello precedente, quando si distribuisce un servizio di bilanciamento del carico, stabilire un collegamento al modello e aggiungere una dipendenza nella risorsa di distribuzione. L'indirizzo IP pubblico nel servizio di bilanciamento del carico è impostato sul valore di output del modello collegato.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_name": {
            "defaultValue": "mylb",
            "type": "string"
        },
        "publicIPAddresses_name": {
            "defaultValue": "myip",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2018-11-01",
            "name": "[parameters('loadBalancers_name')]",
            "location": "eastus",
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundNatRules": [],
                "inboundNatPools": []
            },
            "dependsOn": [
                "linkedTemplate"
            ]
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters":{
                    "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
                }
            }
        }
    ]
}
```

## <a name="linked-and-nested-templates-in-deployment-history"></a>Modelli collegati e annidati nella cronologia di distribuzione

Resource Manager elabora ogni modello come distribuzione distinta nella cronologia di distribuzione. Un modello principale con tre modelli collegati o annidati viene pertanto visualizzato nella cronologia di distribuzione come segue:

![Cronologia di distribuzione](./media/resource-group-linked-templates/deployment-history.png)

È possibile usare queste voci distinte nella cronologia per recuperare i valori di output dopo la distribuzione. Il modello seguente crea un indirizzo IP pubblico e restituisce l'indirizzo IP:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
            "location": "southcentralus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4,
                "dnsSettings": {
                    "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "returnedIPAddress": {
            "type": "string",
            "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
        }
    }
}
```

Il modello seguente stabilisce un collegamento al modello precedente. Crea tre indirizzi IP pubblici.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "copy": {
                "count": 3,
                "name": "ip-loop"
            },
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            }
        }
    ]
}
```

Dopo la distribuzione, è possibile recuperare i valori di output con lo script di PowerShell seguente:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

In alternativa, lo script dell'interfaccia della riga di comando di Azure in una shell Bash:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Protezione di un modello esterno

Anche se il modello collegato deve essere disponibile esternamente, non è necessario che sia pubblicamente disponibile. È possibile aggiungere il modello a un account di archiviazione privato accessibile solo al proprietario dell'account di archiviazione. Creare quindi un token di firma di accesso condiviso per consentire l'accesso durante la distribuzione. Aggiungere il token con firma di accesso condiviso all'URI del modello collegato. Anche se il token viene passato come stringa sicura, l'URI del modello collegato, incluso il token di firma di accesso condiviso, è registrato nelle operazioni di distribuzione. Per limitare l'esposizione, impostare una scadenza per il token.

È anche possibile limitare l'accesso al file dei parametri solo tramite un token di firma di accesso condiviso.

L'esempio seguente mostra come passare un token di firma di accesso condiviso quando si stabilisce un collegamento a un modello:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

In PowerShell ottenere un token per il contenitore e distribuire i modelli con i comandi seguenti. Si noti che il parametro **containerSasToken** è definito nel modello. Non è un parametro del comando **New-AzResourceGroupDeployment**.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Per l'interfaccia della riga di comando di Azure in una shell Bash, ottenere un token per il contenitore e distribuire i modelli con il codice seguente:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Modelli di esempio

Gli esempi seguenti mostrano gli usi più frequenti dei modelli collegati.

|Modello principale  |Modello collegato |Descrizione  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[Modello collegato](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Restituisce una stringa dal modello collegato. |
|[Load Balancer con indirizzo IP pubblico](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[Modello collegato](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Restituisce l'indirizzo IP pubblico dal modello collegato e imposta tale valore nel servizio di bilanciamento del carico. |
|[Indirizzi IP multipli](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [Modello collegato](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Crea diversi indirizzi IP pubblici nel modello collegato.  |

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire un'esercitazione, vedere [Esercitazione: Creare modelli collegati di Azure Resource Manager](./resource-manager-tutorial-create-linked-templates.md).
* Per informazioni sulla definizione dell'ordine di distribuzione per le risorse, vedere [Definire l'ordine per la distribuzione delle risorse nei modelli di Azure Resource Manager](resource-group-define-dependencies.md).
* Per informazioni su come definire una sola risorsa e crearne molte istanze, vedere [Distribuire più istanze di una risorsa o di una proprietà nei modelli di Azure Resource Manager](resource-group-create-multiple.md).
* Per conoscere la procedura per la configurazione di un modello in un account di archiviazione e per la generazione di un token con firma di accesso condiviso, consultare [Deploy resources with Resource Manager templates and Azure PowerShell](resource-group-template-deploy.md) (Distribuire le risorse con i modelli di Resource Manager e Azure PowerShell) o [Deploy resources with Resource Manager templates and Azure CLI](resource-group-template-deploy-cli.md) (Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure).
