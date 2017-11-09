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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: 8b58a83ffd473500dd3f76c09e251f9208527d4f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="using-linked-templates-when-deploying-azure-resources"></a>Uso di modelli collegati nella distribuzione di risorse di Azure
Dall'interno di un modello di Azure Resource Manager è possibile collegarsi a un altro modello che consente di scomporre la distribuzione in un set di modelli di destinazione specifici. In modo analogo alla scomposizione di un'applicazione in diverse classi di codice, la scomposizione offre vantaggi in termini di testing, riuso e leggibilità.  

È possibile passare parametri da un modello principale a un modello collegato. Tali parametri possono venire associati direttamente ai parametri e alle variabili esposti dal modello chiamante. Il modello collegato può inoltre passare una variabile di output al modello di origine, consentendo un scambio bidirezionale di dati tra modelli.

## <a name="linking-to-a-template"></a>Collegamento a un modello
Per creare un collegamento tra due modelli, aggiungere una risorsa di distribuzione all'interno del modello principale che punta al modello collegato. Impostare la proprietà **templateLink** sull'URI del modello collegato. È possibile specificare i valori dei parametri per il modello collegato direttamente nel modello o in un file di parametri. Nel seguente esempio viene utilizzata la proprietà **parameters** per specificare direttamente un valore di parametro.

```json
"resources": [ 
  { 
      "apiVersion": "2017-05-10", 
      "name": "linkedTemplate", 
      "type": "Microsoft.Resources/deployments", 
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": { 
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
        } 
      } 
  } 
] 
```

Come altri tipi di risorse, è possibile impostare le dipendenze tra il modello collegato e altre risorse. Pertanto, quando le altre risorse richiedono un valore di output presente nel modello collegato, è possibile accertarsi di distribuire il modello collegato prima di queste risorse. Viceversa, quando il modello collegato dipende da altre risorse, è possibile accertarsi di distribuire le altre risorse prima di questo modello. Per recuperare un valore da un modello collegato, è possibile usare la sintassi seguente:

```json
"[reference('linkedTemplate').outputs.exampleProperty.value]"
```

Il servizio Resource Manager deve poter accedere al modello collegato. Non è possibile specificare un file locale o un file che sia disponibile unicamente nella rete locale per il modello collegato. È possibile fornire solo un valore URI che includa **http** o **https**. È possibile scegliere di inserire il modello collegato in un account di archiviazione e usare l'URI per tale elemento, come illustrato nell'esempio seguente:

```json
"templateLink": {
    "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
    "contentVersion": "1.0.0.0",
}
```

Anche se il modello collegato deve essere disponibile esternamente, non è necessario che sia pubblicamente disponibile. È possibile aggiungere il modello a un account di archiviazione privato accessibile solo al proprietario dell'account di archiviazione. Creare quindi un token di firma di accesso condiviso per consentire l'accesso durante la distribuzione. Aggiungere il token con firma di accesso condiviso all'URI del modello collegato. Per conoscere la procedura per la configurazione di un modello in un account di archiviazione e per la generazione di un token con firma di accesso condiviso, consultare [Deploy resources with Resource Manager templates and Azure PowerShell](resource-group-template-deploy.md) (Distribuire le risorse con i modelli di Resource Manager e Azure PowerShell) o [Deploy resources with Resource Manager templates and Azure CLI](resource-group-template-deploy-cli.md) (Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure). 

Nell'esempio seguente viene illustrato un modello padre che si collega a un altro modello. È possibile accedere al modello collegato con un token di firma di accesso condiviso passato come parametro.

```json
"parameters": {
    "sasToken": { "type": "securestring" }
},
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "linkedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
          "mode": "incremental",
          "templateLink": {
            "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
            "contentVersion": "1.0.0.0"
          }
        }
    }
],
```

Anche se il token viene passato come stringa sicura, l'URI del modello collegato, incluso il token di firma di accesso condiviso, è registrato nelle operazioni di distribuzione. Per limitare l'esposizione, impostare una scadenza per il token.

Resource Manager gestisce ogni modello collegato come una distribuzione distinta. Nella cronologia delle distribuzioni relativa al gruppo di risorse, sono visibili distribuzioni distinte per il modello padre i modelli annidati.

![cronologia della distribuzione](./media/resource-group-linked-templates/linked-deployment-history.png)

## <a name="linking-to-a-parameter-file"></a>Collegamento a un file di parametri
Nell'esempio successivo viene utilizzata la proprietà **parametersLink** per il collegamento a un file di parametri.

```json
"resources": [ 
  { 
     "apiVersion": "2017-05-10", 
     "name": "linkedTemplate", 
     "type": "Microsoft.Resources/deployments", 
     "properties": { 
       "mode": "incremental", 
       "templateLink": {
          "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       }, 
       "parametersLink": { 
          "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
          "contentVersion":"1.0.0.0"
       } 
     } 
  } 
] 
```

Il valore URI per il file di parametro collegato non può essere un file locale e deve includere o **http** o **https**. È anche possibile limitare l'accesso al file dei parametri solo tramite un token di firma di accesso condiviso.

## <a name="using-variables-to-link-templates"></a>Uso di variabili per collegare i modelli
Negli esempi precedenti sono illustrati i valori di URL hard-coded per i collegamenti del modello. Questo approccio potrebbe funzionare per un modello semplice ma non funziona correttamente in caso di uso di un ampio set di modelli modulari. In alternativa, è possibile creare una variabile statica contenente un URL di base per il modello principale e quindi creare dinamicamente gli URL per i modelli collegati da tale URL di base. Il vantaggio di questo approccio è rappresentato dal fatto che risulta più semplice spostare o scomporre il modello perché è sufficiente modificare la variabile statica nel modello principale. Il modello principale passa gli URI corretti a tutto il modello scomposto.

Nell'esempio seguente viene illustrato come usare un URL di base per creare due URL per i modelli collegati (**sharedTemplateUrl** e **vmTemplate**). 

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

È inoltre possibile usare [deployment ()](resource-group-template-functions-deployment.md#deployment) per ottenere l'URL di base per il modello corrente e usarlo per ottenere l'URL per altri modelli nello stesso percorso. Questo approccio è utile se la posizione del modello cambia, ad esempio a causa del controllo delle versioni, o si vuole evitare la codifica di URL nel file del modello. 

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="complete-example"></a>Esempio completo
I seguenti modelli di esempio mostrano una disposizione semplificata di modelli collegati, allo scopo di illustrare alcuni dei concetti in questo articolo. supponendo che i modelli siano stati aggiunti nello stesso contenitore in un account di archiviazione con accesso pubblico disattivato. Il modello collegato restituisce un valore al modello principale nella sezione **outputs** .

Il file **parent.json** è costituito da:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference('linkedTemplate').outputs.result.value]"
    }
  }
}
```

Il file **helloworld.json** è costituito da:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "result": {
        "value": "Hello World",
        "type" : "string"
    }
  }
}
```

Nella PowerShell, ottenere un token per il contenitore e distribuire i modelli con:

```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Nell'interfaccia della riga di comando 2.0 di Azure si ottiene un token per il contenitore e si distribuiscono i modelli con il codice seguente:

```azurecli
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

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulla definizione dell'ordine di distribuzione per le risorse, vedere [Definizione delle dipendenze nei modelli di Azure Resource Manager](resource-group-define-dependencies.md)
* Per informazioni su come definire una sola risorsa e crearne molte istanze, vedere [Creare più istanze di risorse in Azure Resource Manager](resource-group-create-multiple.md)

