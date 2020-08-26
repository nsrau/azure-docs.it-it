---
title: Creare una VM di SQL Server con un modello di Resource Manager
description: Informazioni su come creare una macchina virtuale (VM) di SQL Server in Azure usando un modello di Azure Resource Manager.
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.openlocfilehash: 21909a9c7a1edda5059b9e83cf8dda243862b966
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660208"
---
# <a name="quickstart-create-sql-server-vm-using-an-arm-template"></a>Avvio rapido: Creare una VM di SQL Server con un modello di Resource Manager

Usare questo modello di Azure Resource Manager per distribuire una macchina virtuale di SQL Server in Azure. 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Con il modello di Resource Manager per la VM di SQL Server, sono richiesti gli elementi seguenti:

- La versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e/o di [PowerShell](/powershell/scripting/install/installing-powershell). 
- Un [gruppo di risorse](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) preconfigurato con una [rete virtuale](../../../virtual-network/quick-create-portal.md) e una [subnet](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) preparate.
- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/).

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json":::

Nel modello sono definite cinque risorse di Azure: 

- [Microsoft.Network/publicIpAddresses](/azure/templates/microsoft.network/publicipaddresses): crea un indirizzo IP pubblico. 
- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups): crea un gruppo di sicurezza di rete. 
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces): configura l'interfaccia di rete. 
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): crea una macchina virtuale in Azure. 
- [Microsoft.SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines): registra la macchina virtuale con il provider di risorse della VM di SQL. 

Altri modelli di VM di SQL Server in Azure sono disponibili nella [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine&pageNumber=1&sort=Popular).


## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea una macchina virtuale con la versione di SQL Server prevista installata e registrata con il provider di risorse della VM di SQL. 

   [![Distribuzione in Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. Selezionare o immettere i valori seguenti.

    * **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: gruppo di risorse preparato per la VM di SQL Server. 
    * **Area**: scegliere un'area,  ad esempio **Stati Uniti centrali**.
    * **Nome macchina virtuale**: immettere un nome per la macchina virtuale di SQL Server. 
    * **Dimensioni macchina virtuale**: scegliere le dimensioni appropriate della macchina virtuale dall'elenco a discesa.
    * **Rete virtuale esistente**: immettere il nome della rete virtuale preparata per la VM di SQL Server. 
    * **Existing Vnet Resource Group** (Gruppo di risorse di rete virtuale esistente): immettere il gruppo di risorse in cui è stata preparata la rete virtuale. 
    * **Existing Subnet Name** (Nome della subnet esistente): nome della subnet preparata. 
    * **Offerta immagine**: scegliere l'immagine di SQL Server e Windows Server più adatta alle esigenze aziendali. 
    * **SQL Sku** (SKU SQL): scegliere l'edizione della SKU di SQL Server più adatta alle esigenze aziendali. 
    * **Nome utente amministratore**: nome utente dell'amministratore della macchina virtuale. 
    * **Password amministratore**: password usata dall'account amministratore della macchina virtuale. 
    * **Storage Workload Type** (Tipo di carico di lavoro di archiviazione):  tipo di archiviazione per il carico di lavoro più adatto all'azienda. 
    * **Sql Data Disks Count** (Conteggio dischi dati SQL):  numero di dischi usati da SQL Server per i file di dati.  
    * **Percorso dati**:  percorso dei file di dati di SQL Server. 
    * **Sql Log Disks Count** (Conteggio dischi log SQL):  numero di dischi usati da SQL Server per i file di log. 
    * **Percorso del log**:  percorso dei file di log di SQL Server. 
    * **Località**:  località di tutte le risorse. Non modificare il valore predefinito `[resourceGroup().location]`. 

3. Selezionare **Rivedi e crea**. Al termine della distribuzione della VM di SQL Server, si riceverà una notifica.

Per distribuire il modello, si usa il portale di Azure. Oltre al portale di Azure, è anche possibile usare Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

È possibile usare l'interfaccia della riga di comando di Azure per controllare le risorse distribuite. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse con l'interfaccia della riga di comando di Azure o con Azure PowerShell:

# <a name="cli"></a>[Interfaccia della riga di comando](#tab/CLI)

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
> [ Esercitazione: Creare e distribuire il primo modello di Resource Manager](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

Per altre modalità di distribuzione di una VM di SQL Server, vedere: 
- [Portale di Azure](create-sql-vm-portal.md)
- [PowerShell](create-sql-vm-powershell.md)

Per altre informazioni, vedere [una panoramica delle VM di SQL Server in Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).
