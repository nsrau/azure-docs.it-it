---
title: "Azure Resource Manager: Creare un'istanza gestita di SQL di Azure"
description: Informazioni su come creare un'istanza gestita di SQL di Azure usando un modello di Azure Resource Manager.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/22/2020
ms.openlocfilehash: 01c6c37d31d41f88b370face372555536724adde
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85256071"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-azure-resource-manager-template"></a>Avvio rapido: Creare un'istanza gestita di SQL di Azure con un modello di Azure Resource Manager

Questo argomento di avvio rapido illustra il processo di distribuzione di un modello di Resource Manager per creare un'istanza gestita di SQL di Azure e una rete virtuale.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

No.

## <a name="create-an-azure-sql-managed-instance"></a>Creare un'istanza gestita di SQL di Azure

[Istanza gestita di SQL di Azure](sql-managed-instance-paas-overview.md) è un database cloud intelligente, scalabile e completamente gestito le cui funzionalità sono quasi sovrapponibili a quelle del motore di database di SQL Server.

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

Nel modello sono definite queste risorse:

- [**Microsoft.Sql/managedinstances**](/azure/templates/microsoft.sql/managedinstances)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)



Per altri modelli di esempio disponibili, vedere i [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuire il modello

Selezionare **Provalo** dal blocco di codice PowerShell seguente per aprire Azure Cloud Shell.

> [!IMPORTANT]
> La distribuzione di un'istanza gestita è un'operazione di lunga durata. La distribuzione della prima istanza nella subnet richiede in genere molto più tempo rispetto alla distribuzione in una subnet contenente istanze gestite esistenti. Per informazioni sui tempi medi di provisioning, vedere [Operazioni di gestione di istanze gestite di SQL](sql-managed-instance-paas-overview.md#management-operations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

* * *

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Visitare il [portale di Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) e verificare che l'istanza gestita sia presente nel gruppo di risorse selezionato. Poiché la creazione di un'istanza gestita può richiedere tempo, può essere necessario controllare il collegamento **Distribuzioni** nella pagina **Panoramica** del gruppo di risorse.

- Per una guida di avvio rapido che illustra come connettersi a Istanza gestita di SQL tramite una macchina virtuale di Azure, vedere [Configurare una connessione tramite macchina virtuale di Azure](connect-vm-instance-configure.md).
- Per una guida di avvio rapido che illustra come connettersi a Istanza gestita di SQL tramite un computer client locale usando una connessione da punto a sito, vedere [Configurare una connessione da punto a sito](point-to-site-p2s-configure.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Per continuare con i [passaggi successivi](#next-steps), mantenere l'istanza gestita, ma eliminare sia l'istanza che le risorse correlate una volta completate le esercitazioni aggiuntive. Dopo aver eliminato un'istanza gestita, vedere [Eliminare una subnet dopo l'eliminazione di un'istanza gestita](virtual-cluster-delete.md).


Per eliminare il gruppo di risorse:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare una macchina virtuale di Azure per la connessione a Istanza gestita di SQL di Azure](connect-vm-instance-configure.md)
