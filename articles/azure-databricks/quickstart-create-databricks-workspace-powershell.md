---
title: "Avvio rapido: Creare un'area di lavoro di Azure Databricks con PowerShell"
description: Questo argomento di avvio rapido descrive come usare PowerShell per creare un'area di lavoro di Azure Databricks.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 06/02/2020
ms.custom: mvc
ms.openlocfilehash: 445832119a113f92dc1da51eb5b5e77dff176fa3
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485689"
---
# <a name="quickstart-create-an-azure-databricks-workspace-using-powershell"></a>Avvio rapido: Creare un'area di lavoro di Azure Databricks con PowerShell

Questo argomento di avvio rapido descrive come usare PowerShell per creare un'area di lavoro di Azure Databricks. È possibile usare PowerShell per creare e gestire le risorse di Azure in modo interattivo o negli script.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Se si sceglie di usare PowerShell in locale, per questo articolo è necessario installare il modulo Az PowerShell e connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount). Per altre informazioni sull'installazione del modulo Az PowerShell, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Quando è disponibile in anteprima, il modulo Az.Databricks di PowerShell deve essere installato separatamente dal modulo Az di PowerShell usando il comando seguente: `Install-Module -Name Az.Databricks -AllowPrerelease`.
> Quando il modulo Az.Databricks di PowerShell sarà disponibile a livello generale, verrà incluso nelle versioni future del modulo Az PowerShell e sarà disponibile in modo nativo dall'interno di Azure Cloud Shell.

Se è la prima volta che si usa Azure Databricks, è necessario registrare il provider di risorse **Microsoft.Databricks**.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Databricks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se si possiedono più sottoscrizioni di Azure, scegliere quella appropriata in cui verranno fatturate le risorse. Selezionare un ID sottoscrizione specifico usando il cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) con il cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

L'esempio seguente crea un gruppo di risorse denominato **myresourcegroup** nell'area **West US 2**.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus2
```

## <a name="create-an-azure-databricks-workspace"></a>Creare un'area di lavoro di Azure Databricks

In questa sezione viene creata un'area di lavoro di Azure Databricks con PowerShell.

```azurepowershell-interactive
New-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup -Location westus2 -ManagedResourceGroupName databricks-group -Sku standard
```

Specificare i valori seguenti:

|       **Proprietà**       |                                                                                **Descrizione**                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Nome                     | Specificare un nome per l'area di lavoro di Databricks                                                                                                                                   |
| ResourceGroupName        | Specificare il nome di un gruppo di risorse esistente                                                                                                                                        |
| Location                 | Selezionare **Stati Uniti occidentali 2**. Per le altre aree disponibili, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/)                                     |
| ManagedResourceGroupName | Specificare se si vuole creare un nuovo gruppo di risorse gestite o usarne uno esistente.                                                                                        |
| Sku                      | Scegliere tra **Standard**, **Premium** e **Versione di valutazione**. Per altre informazioni su questi piani tariffari, vedere [Prezzi di Azure Databricks](https://azure.microsoft.com/pricing/details/databricks/) |

La creazione dell'area di lavoro richiede alcuni minuti, Al termine di questo processo, l'account utente viene aggiunto automaticamente come utente amministratore nell'area di lavoro.

Quando la distribuzione di un'area di lavoro non riesce, l'area di lavoro viene creata comunque anche se in uno stato di errore. Eliminare l'area di lavoro in errore e crearne una nuova per risolvere gli errori di distribuzione. Quando si elimina l'area di lavoro in errore, vengono eliminati anche il gruppo di risorse gestite e tutte le eventuali risorse distribuite correttamente.

## <a name="determine-the-provisioning-state-of-a-databricks-workspace"></a>Determinare lo stato di provisioning di un'area di lavoro di Databricks

Per determinare se il provisioning di un'area di lavoro di Databricks è stato eseguito correttamente, è possibile usare il cmdlet `Get-AzDatabricksWorkspace`.

```azurepowershell-interactive
Get-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup |
  Select-Object -Property Name, SkuName, Location, ProvisioningState
```

```Output
Name            SkuName   Location  ProvisioningState
----            -------   --------  -----------------
mydatabricksws  standard  westus2   Succeeded
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse usate in questa guida di avvio rapido non sono necessarie per un'altra guida o esercitazione, è possibile eliminarle usando il comando seguente.

> [!CAUTION]
> L'esempio seguente elimina il gruppo di risorse specificato e tutte le risorse al suo interno.
> Se nel gruppo di risorse specificato sono presenti anche risorse diverse da quelle usate in questa guida di avvio rapido, verranno eliminate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Per eliminare solo il server creato in questa guida di avvio rapido senza eliminare il gruppo di risorse, usare il cmdlet `Remove-AzDatabricksWorkspace`.

```azurepowershell-interactive
Remove-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un cluster Spark in Databricks](quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)
