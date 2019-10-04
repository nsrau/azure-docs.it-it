---
title: Distribuzione condizionale con modelli di Azure Resource Manager
description: Viene descritto come distribuire in modo condizionale una risorsa in un modello di Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 88f8b6a8dcce0e498a7b81b8741072bcf4cfcad8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259509"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Distribuzione condizionale nei modelli di Gestione risorse

In alcuni casi è necessario distribuire facoltativamente una risorsa in un modello. Usare l' `condition` elemento per specificare se la risorsa viene distribuita. Il valore di questo elemento restituisce true o false. Quando il valore è true, la risorsa viene creata. Quando il valore è false, la risorsa non viene creata. Il valore può essere applicato solo all'intera risorsa.

## <a name="new-or-existing-resource"></a>Risorsa nuova o esistente

È possibile usare la distribuzione condizionale per creare una nuova risorsa o utilizzarne una esistente. L'esempio seguente illustra come usare la condizione per distribuire un nuovo account di archiviazione o usare un account di archiviazione esistente.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
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
    "name": "[parameters('serverName')]",
    "apiVersion": "2015-05-01-preview",
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
            "name": "AllowAllWindowsAzureIps",
            "apiVersion": "2015-05-01-preview",
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

Se si usa una funzione di [riferimento](resource-group-template-functions-resource.md#reference) o [elenco](resource-group-template-functions-resource.md#list) con una risorsa distribuita in modo condizionale, la funzione viene valutata anche se la risorsa non viene distribuita. Viene ricevuto un errore se la funzione fa riferimento a una risorsa che non esiste.

Usare la funzione [if](resource-group-template-functions-logical.md#if) per assicurarsi che la funzione venga valutata solo per le condizioni quando la risorsa viene distribuita. Vedere la [funzione If](resource-group-template-functions-logical.md#if) per un modello di esempio che usa if e Reference con una risorsa distribuita in modo condizionale.

## <a name="next-steps"></a>Passaggi successivi

* Per suggerimenti sulla creazione di modelli, vedere [Procedure consigliate per la creazione di modelli di Azure Resource Manager](template-best-practices.md).
* Per creare più istanze di una risorsa, vedere [Resource, Property o Variable iterazione in Azure Resource Manager Templates](resource-group-create-multiple.md).