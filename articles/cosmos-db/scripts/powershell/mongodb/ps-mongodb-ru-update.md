---
title: Script di Azure PowerShell - Aggiornare le unità richiesta al secondo di Azure Cosmos DB per l'API MongoDB
description: Script di Azure PowerShell - Aggiornare le unità richiesta al secondo di Azure Cosmos DB per l'API MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/18/2019
ms.author: mjbrown
ms.openlocfilehash: 517857116bf077814b936e5d7ecb54d841959657
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603879"
---
# <a name="update-rus-for-a-database-or-collection-for-azure-cosmos-db---mongodb-api"></a>Aggiornare le unità richiesta al secondo per un database o una raccolta per Azure Cosmos DB - API MongoDB

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-ru-update.ps1 "Update throughput on a database or collection for MongoDB API")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
|**Risorse di Azure**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Crea una risorsa. |
|**Gruppi di risorse di Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script PowerShell di Azure Cosmos DB sono disponibili in [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Script di PowerShell per Azure Cosmos DB).