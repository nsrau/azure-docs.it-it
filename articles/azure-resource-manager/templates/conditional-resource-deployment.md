---
title: Distribuzione condizionale con i modelliConditional deployment with templates
description: Descrive come distribuire in modo condizionale una risorsa in un modello di Azure Resource Manager.Describes how to conditionally deploy a resource in an Azure Resource Manager template.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f170710118c0e3de6f3643b6216ed55b83b5c7df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153421"
---
# <a name="conditional-deployment-in-arm-templates"></a>Distribuzione condizionale nei modelli ARMConditional deployment in ARM templates

A volte è necessario distribuire facoltativamente una risorsa in un modello di Azure Resource Manager (ARM). Utilizzare `condition` l'elemento per specificare se la risorsa viene distribuita. Il valore di questo elemento restituisce true o false. Quando il valore è true, la risorsa viene creata. Quando il valore è false, la risorsa non viene creata. Il valore può essere applicato solo all'intera risorsa.

## <a name="new-or-existing-resource"></a>Risorsa nuova o esistente

È possibile usare la distribuzione condizionale per creare una nuova risorsa o usarne una esistente. L'esempio seguente mostra come usare la condizione per distribuire un nuovo account di archiviazione o usare un account di archiviazione esistente.

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

Quando il parametro **newOrExisting** è impostato su **new**, la condizione restituisce true. L'account di archiviazione viene distribuito. Tuttavia, quando **newOrExisting** è impostato su **existing**, la condizione restituisce false e l'account di archiviazione non viene distribuito.

Per un modello di esempio completo che usa l'elemento `condition`, vedere [Macchina virtuale con una Rete virtuale nuova o esistente, archiviazione e indirizzo IP pubblico](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="allow-condition"></a>Condizione di autorizzazione

È possibile passare un valore di parametro che indica se una condizione è consentita. L'esempio seguente distribuisce un server SQL e, facoltativamente, consente gli indirizzi IP di Azure.The following example deploys a SQL server and optionally allows Azure IPs.

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
  "location": "[parameters('location')]",
  "properties": {
    "administratorLogin": "[parameters('administratorLogin')]",
    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
    "version": "12.0"
  },
  "resources": [
    {
      "condition": "[parameters('allowAzureIPs')]",
      "type": "firewallRules",
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
      ],
      "properties": {
        "endIpAddress": "0.0.0.0",
        "startIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Per il modello completo, vedere Server logico SQL di [Azure.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)

## <a name="runtime-functions"></a>Funzioni di runtime

Se si usa una funzione di [riferimento](template-functions-resource.md#reference) o [di elenco](template-functions-resource.md#list) con una risorsa distribuita in modo condizionale, la funzione viene valutata anche se la risorsa non è distribuita. Se la funzione fa riferimento a una risorsa che non esiste, viene visualizzato un errore.

Utilizzare la funzione [if](template-functions-logical.md#if) per assicurarsi che la funzione venga valutata solo per le condizioni quando la risorsa viene distribuita. Vedere la [funzione if](template-functions-logical.md#if) per un modello di esempio che usa if e fare riferimento con una risorsa distribuita in modo condizionale.

Impostare una [risorsa come dipendente da](define-resource-dependency.md) una risorsa condizionale esattamente come si farebbe con qualsiasi altra risorsa. Quando una risorsa condizionale non viene distribuita, Azure Resource Manager la rimuove automaticamente dalle dipendenze richieste.

## <a name="condition-with-complete-mode"></a>Condizione con modalità completa

Se si distribuisce un modello con [la modalità completa](deployment-modes.md) e una risorsa non viene distribuita perché la condizione restituisce false, il risultato dipende dalla versione dell'API REST utilizzata per distribuire il modello. Se si utilizza una versione precedente al 2019-05-10, la risorsa **non viene eliminata.** Con 2019-05-10 o versione successiva, la risorsa **viene eliminata.** Le versioni più recenti di Azure PowerShell e dell'interfaccia della riga di comando di Azure eliminano la risorsa quando la condizione è falsa.

## <a name="next-steps"></a>Passaggi successivi

* Per suggerimenti sulla creazione di modelli, vedere Procedure consigliate per [i modelli ARM](template-best-practices.md).
* Per creare più istanze di una risorsa, vedere [Iterazione delle](copy-resources.md)risorse nei modelli ARM .