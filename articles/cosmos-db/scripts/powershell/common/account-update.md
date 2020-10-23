---
title: Script di PowerShell per aggiornare il livello di coerenza predefinito in un account Azure Cosmos
description: Esempio di script di Azure PowerShell - Aggiornare il livello di coerenza predefinito in un account Azure Cosmos DB con PowerShell
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/21/2020
ms.author: mjbrown
ms.openlocfilehash: 7a13bbc3f3d9758accdf10daa21f45df1b973775
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282770"
---
# <a name="update-the-regions-on-an-azure-cosmos-db-account-using-powershell"></a>Aggiornare le aree in un account Azure Cosmos DB con PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script di esempio

> [!NOTE]
> Non è possibile modificare le aree e cambiare altre proprietà dell'account Cosmos nella stessa operazione. Queste modifiche devono essere eseguite con due operazioni separate.
> [!NOTE]
> Questo esempio mostra l'utilizzo di un account per l'API SQL. Per usare questo esempio per altre API, copiare le proprietà correlate e applicarle allo script specifico dell'API.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update.ps1 "Update an Azure Cosmos DB account")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Elenca gli account Cosmos DB o ottiene un account Cosmos DB specificato. |
| [Update-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Aggiornare un account Cosmos DB. |
|**Gruppi di risorse di Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).
