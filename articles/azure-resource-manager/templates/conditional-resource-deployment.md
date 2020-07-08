---
title: Distribuzione condizionale con modelli
description: Viene descritto come distribuire in modo condizionale una risorsa in un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: effa7fe6ee1393e44a124bc087609da5d4898210
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84259321"
---
# <a name="conditional-deployment-in-arm-templates"></a>Distribuzione condizionale nei modelli ARM

In alcuni casi è necessario distribuire facoltativamente una risorsa in un modello di Azure Resource Manager (ARM). Usare l' `condition` elemento per specificare se la risorsa viene distribuita. Il valore di questo elemento restituisce true o false. Quando il valore è true, la risorsa viene creata. Quando il valore è false, la risorsa non viene creata. Il valore può essere applicato solo all'intera risorsa.

> [!NOTE]
> La distribuzione condizionale non si sovrappone alle [risorse figlio](child-resource-name-type.md). Se si desidera distribuire una risorsa e le relative risorse figlio in modo condizionale, è necessario applicare la stessa condizione a ogni tipo di risorsa.

## <a name="new-or-existing-resource"></a>Risorsa nuova o esistente

È possibile usare la distribuzione condizionale per creare una nuova risorsa o utilizzarne una esistente. L'esempio seguente illustra come usare la condizione per distribuire un nuovo account di archiviazione o usare un account di archiviazione esistente.

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

Quando il parametro **newOrExisting** è impostato su **New**, la condizione restituisce true. L'account di archiviazione viene distribuito. Tuttavia, quando **newOrExisting** è impostato su **existing**, la condizione restituisce false e l'account di archiviazione non viene distribuito.

Per un modello di esempio completo che usa l'elemento `condition`, vedere [Macchina virtuale con una Rete virtuale nuova o esistente, archiviazione e indirizzo IP pubblico](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="allow-condition"></a>Consenti condizione

È possibile passare un valore di parametro che indica se è consentita una condizione. Nell'esempio seguente viene distribuita un'installazione di SQL Server e, facoltativamente, gli indirizzi IP di Azure.

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

Per il modello completo, vedere [server logico SQL di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Funzioni di runtime

Se si usa una funzione di [riferimento](template-functions-resource.md#reference) o [elenco](template-functions-resource.md#list) con una risorsa distribuita in modo condizionale, la funzione viene valutata anche se la risorsa non viene distribuita. Viene ricevuto un errore se la funzione fa riferimento a una risorsa che non esiste.

Usare la funzione [if](template-functions-logical.md#if) per assicurarsi che la funzione venga valutata solo per le condizioni quando la risorsa viene distribuita. Vedere la [funzione if](template-functions-logical.md#if) per un modello di esempio che usa if e reference con una risorsa distribuita in modo condizionale.

Si imposta una [risorsa come dipendente](define-resource-dependency.md) da una risorsa condizionale esattamente come per qualsiasi altra risorsa. Quando una risorsa condizionale non viene distribuita, Azure Resource Manager la rimuove automaticamente dalle dipendenze richieste.

## <a name="complete-mode"></a>Modalità completa

Se si distribuisce un modello con la [modalità completa](deployment-modes.md) e una risorsa non viene distribuita perché la condizione restituisce false, il risultato dipende dalla versione dell'API REST usata per distribuire il modello. Se si usa una versione precedente alla 2019-05-10, la risorsa **non viene eliminata**. Con 2019-05-10 o versioni successive, la risorsa **viene eliminata**. Le versioni più recenti di Azure PowerShell e dell'interfaccia della riga di comando di Azure eliminano la risorsa quando condition è false.

## <a name="next-steps"></a>Passaggi successivi

* Per consigli sulla creazione di modelli, vedere procedure consigliate per il [modello ARM](template-best-practices.md).
* Per creare più istanze di una risorsa, vedere [iterazione delle risorse nei modelli ARM](copy-resources.md).
