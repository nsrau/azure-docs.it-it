---
title: Creare un'istanza del Servizio Migrazione del database (modello di Azure Resource Manager)
description: Informazioni su come creare un'istanza del Servizio Migrazione del database usando un modello di Azure Resource Manager.
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: dms
ms.openlocfilehash: 9e0d537a948e960c90737cc3f367940a0ffca74c
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85852479"
---
# <a name="create-instance-of-azure-database-migration-service-azure-resource-manager-template"></a>Creare un'istanza del Servizio Migrazione del database di Azure (modello di Azure Resource Manager)

Usare questo modello di Azure Resource Manager per distribuire un'istanza del Servizio Migrazione del database di Azure. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prerequisiti

Il modello di Azure Resource Manager del Servizio Migrazione del database di Azure richiede gli elementi seguenti: 

- La versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) e/o di [PowerShell](/powershell/scripting/install/installing-powershell?view=powershell-7). 
- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-azure-database-migration-simple-deploy/).

:::code language="json" source="~/quickstart-templates/101-azure-database-migration-simple-deploy/azuredeploy.json" highlight="33-75":::

Nel modello sono definite tre risorse di Azure: 

- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks): crea la rete virtuale. 
- [Microsoft.Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets): crea la subnet. 
- [Microsoft.DataMigration/services](/azure/templates/microsoft.datamigration/services): distribuisce un'istanza del Servizio Migrazione del database di Azure. 

Altri modelli per il Servizio Migrazione del database di Azure sono disponibili nella [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datamigration).


## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un'istanza del Servizio Migrazione del database di Azure. 

   [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

2. Selezionare o immettere i valori seguenti.

    * **Sottoscrizione** Selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente dall'elenco a discesa oppure selezionare **Crea nuovo** per creare un nuovo gruppo di risorse. 
    * **Area**: area in cui verranno distribuite le risorse.
    * **Nome servizio**: nome del nuovo servizio di migrazione.
    * **Località**: località del gruppo di risorse, accettare il valore predefinito `[resourceGroup().location]`.
    * **Nome rete virtuale**: nome della nuova rete virtuale.
    * **Nome subnet**: nome della nuova subnet associata alla rete virtuale.



3. Selezionare **Rivedi e crea**. Al termine della corretta distribuzione dell'istanza del Servizio Migrazione del database di Azure, si riceverà una notifica. 


Per distribuire il modello, si usa il portale di Azure. Oltre al portale di Azure, è anche possibile usare Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

È possibile usare l'interfaccia della riga di comando di Azure per controllare le risorse distribuite. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse con l'interfaccia della riga di comando di Azure o con Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Passaggi successivi

Per un'esercitazione dettagliata che illustra il processo di creazione di un modello, vedere:

> [!div class="nextstepaction"]
> [ Esercitazione: Creare e distribuire il primo modello di Azure Resource Manager](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

Per altri modi di distribuire il Servizio Migrazione del database di Azure, vedere: 
- [Azure portal](quickstart-create-data-migration-service-portal.md)

Per altre informazioni, vedere una [panoramica del Servizio Migrazione del database di Azure](dms-overview.md)