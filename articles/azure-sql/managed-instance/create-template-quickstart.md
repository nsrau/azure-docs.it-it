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
ms.openlocfilehash: eed333b5e6a83b140df515fc02767b8a7c7a63c7
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2020
ms.locfileid: "85506640"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-arm-template"></a>Avvio rapido: Creare un'istanza gestita di SQL di Azure con un modello di Resource Manager

Questo argomento di avvio rapido illustra il processo di distribuzione di un modello di Azure Resource Manager (modello ARM) per creare un'istanza gestita di SQL di Azure e una rete virtuale. [Istanza gestita di SQL di Azure](sql-managed-instance-paas-overview.md) è un database cloud intelligente, scalabile e completamente gestito le cui funzionalità sono quasi sovrapponibili a quelle del motore di database di SQL Server.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sqlmi-new-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json" range="001-249" highlight="113,178,188,226":::

Nel modello sono definite queste risorse:

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft.Sql/managedinstances**](/azure/templates/microsoft.sql/managedinstances)

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

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

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

---

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

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare una macchina virtuale di Azure per la connessione a Istanza gestita di SQL di Azure](connect-vm-instance-configure.md)
