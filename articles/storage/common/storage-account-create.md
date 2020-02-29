---
title: Creare un account di archiviazione
titleSuffix: Azure Storage
description: Informazioni su come creare un account di archiviazione usando il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure. Un account di archiviazione di Azure offre uno spazio dei nomi univoco in Microsoft Azure per archiviare e accedere ai dati.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/07/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c2d1e8b4975be0657983192df00cc434da00a6f7
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197726"
---
# <a name="create-an-azure-storage-account"></a>Creare un account di Archiviazione di Azure

Un account di archiviazione di Azure contiene tutti gli oggetti dati di Archiviazione di Azure: BLOB, file, code, tabelle e dischi. L'account di archiviazione fornisce uno spazio dei nomi univoco per i dati di archiviazione di Azure accessibili da qualsiasi parte del mondo tramite HTTP o HTTPS. I dati nell'account di archiviazione di Azure sono durevoli e a disponibilità elevata, protetti e altamente scalabili.

In questo articolo sulle procedure viene illustrato come creare un account di archiviazione usando il [portale di Azure](https://portal.azure.com/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), l' [interfaccia](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)della riga di comando di Azure o un [modello di Azure Resource Manager](../../azure-resource-manager/management/overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

# <a name="portal"></a>[Portale](#tab/azure-portal)

No.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per creare un account di archiviazione di Azure con PowerShell, verificare di aver installato Azure PowerShell modulo AZ versione 0,7 o successiva. Per ulteriori informazioni, vedere [Introduzione al Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

Per trovare la versione corrente, eseguire il comando seguente:

```powershell
Get-InstalledModule -Name "Az"
```

Per installare o aggiornare Azure PowerShell, vedere [install Azure PowerShell Module](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile accedere ad Azure ed eseguire i comandi dell'interfaccia della riga di comando di Azure in uno dei due modi seguenti:

- È possibile eseguire i comandi dell'interfaccia della riga di comando dall'interno del portale di Azure, Azure Cloud Shell.
- È possibile installare l'interfaccia della riga di comando ed eseguire i comandi CLI localmente.

### <a name="use-azure-cloud-shell"></a>Usare Azure Cloud Shell

Azure Cloud Shell è una shell Bash gratuita che è possibile eseguire direttamente nel portale di Azure. L'interfaccia della riga di comando di Azure è preinstallata e configurata per l'uso con l'account. Fare clic sul pulsante **cloud Shell** nel menu nella sezione in alto a destra del portale di Azure:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Il pulsante avvia una shell interattiva che è possibile usare per eseguire la procedura descritta in questo articolo:

[![Screenshot che mostra la finestra di Cloud Shell nel portale](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installare l'interfaccia della riga di comando in locale

È anche possibile installare e usare l'interfaccia della riga di comando di Azure in locale. Questo articolo illustra le procedure necessarie per eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

# <a name="template"></a>[Modello](#tab/template)

No.

---

## <a name="sign-in-to-azure"></a>Accedere ad Azure

# <a name="portal"></a>[Portale](#tab/azure-portal)

Accedere al [portale di Azure](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate per eseguire l'autenticazione.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per avviare Azure Cloud Shell, accedere al [portale di Azure](https://portal.azure.com).

Per accedere all'installazione locale dell'interfaccia della riga di comando, eseguire il comando [AZ login](/cli/azure/reference-index#az-login) :

```azurecli-interactive
az login
```

# <a name="template"></a>[Modello](#tab/template)

N/D

---

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

A questo punto è possibile creare un account di archiviazione.

Ogni account di archiviazione deve appartenere a un gruppo di risorse di Azure. Un gruppo di risorse è un contenitore logico per raggruppare i servizi di Azure. Quando si crea un account di archiviazione, è possibile creare un nuovo gruppo di risorse o usarne uno esistente. Questo articolo illustra come creare un nuovo gruppo di risorse.

Un account di archiviazione **per utilizzo generico v2** consente l'accesso a tutti i servizi di Archiviazione di Azure: BLOB, file, code, tabelle e dischi. I passaggi descritti di seguito consentono di creare un account di archiviazione per utilizzo generico V2, ma i passaggi per creare qualsiasi tipo di account di archiviazione sono simili.

# <a name="portal"></a>[Portale](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare prima un nuovo gruppo di risorse con PowerShell usando il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

In caso di dubbi su quale area specificare per il parametro `-Location`, è possibile recuperare un elenco di aree supportate per la sottoscrizione con il comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```powershell
Get-AzLocation | select Location
```

Creare quindi un account di archiviazione per utilizzo generico V2 con archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) usando il comando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) . Tenere presente che il nome dell'account di archiviazione deve essere univoco in Azure, quindi sostituire il valore del segnaposto tra parentesi quadre con il proprio valore univoco:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Se si prevede di utilizzare [Azure Data Lake storage](https://azure.microsoft.com/services/storage/data-lake-storage/), includere `-EnableHierarchicalNamespace $True` in questo elenco di parametri.

Per creare un account di archiviazione per utilizzo generico V2 con un'opzione di replica diversa, sostituire il valore desiderato nella tabella seguente per il parametro **SkuName** .

|Opzione di replica  |Parametro SkuName  |
|---------|---------|
|Archiviazione con ridondanza locale (LRS)     |Standard_LRS         |
|Archiviazione con ridondanza della zona (ZRS).     |Standard_ZRS         |
|Archiviazione con ridondanza geografica (GRS)     |Standard_GRS         |
|Archiviazione con ridondanza geografica e accesso in lettura     |Standard_RAGRS         |
|Archiviazione con ridondanza della zona geografica (GZRS) (anteprima)    |Standard_GZRS         |
|Archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS) (anteprima)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Creare prima un nuovo gruppo di risorse con l'interfaccia della riga di comando di Azure usando il comando [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

In caso di dubbi su quale area specificare per il parametro `--location`, è possibile recuperare un elenco di aree supportate per la sottoscrizione con il comando [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Creare quindi un account di archiviazione per utilizzo generico V2 con l'archiviazione con ridondanza geografica e accesso in lettura usando il comando [AZ storage account create](/cli/azure/storage/account#az_storage_account_create) . Tenere presente che il nome dell'account di archiviazione deve essere univoco in Azure, quindi sostituire il valore del segnaposto tra parentesi quadre con il proprio valore univoco:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> Se si prevede di utilizzare [Azure Data Lake storage](https://azure.microsoft.com/services/storage/data-lake-storage/), includere `--enable-hierarchical-namespace true` in questo elenco di parametri. 

Per creare un account di archiviazione per utilizzo generico V2 con un'opzione di replica diversa, sostituire il valore desiderato nella tabella seguente per il parametro **SKU** .

|Opzione di replica  |Parametro sku  |
|---------|---------|
|Archiviazione con ridondanza locale (LRS)     |Standard_LRS         |
|Archiviazione con ridondanza della zona (ZRS).     |Standard_ZRS         |
|Archiviazione con ridondanza geografica (GRS)     |Standard_GRS         |
|Archiviazione con ridondanza geografica e accesso in lettura     |Standard_RAGRS         |
|Archiviazione con ridondanza della zona geografica (GZRS) (anteprima)    |Standard_GZRS         |
|Archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS) (anteprima)    |Standard_RAGZRS         |

# <a name="template"></a>[Modello](#tab/template)

È possibile usare Azure Powershell o l'interfaccia della riga di comando di Azure per distribuire un modello di Resource Manager per creare un account di archiviazione. Il modello usato in questo articolo sulle procedure è da [Azure Resource Manager modelli di avvio rapido](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Per eseguire gli script, selezionare **Prova** per aprire Azure Cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse nella shell e quindi scegliere **Incolla**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Questo modello viene utilizzato solo come esempio. Sono presenti molte impostazioni dell'account di archiviazione che non sono configurate come parte di questo modello. Se ad esempio si desidera utilizzare [Azure Data Lake storage](https://azure.microsoft.com/services/storage/data-lake-storage/), modificare il modello impostando la proprietà `isHnsEnabledad` dell'oggetto `StorageAccountPropertiesCreateParameters` su `true`. 

Per informazioni su come modificare il modello o crearne di nuovi, vedere:

- [Documentazione di Azure Resource Manager](/azure/azure-resource-manager/).
- [Storage account template reference](/azure/templates/microsoft.storage/allversions)(Riferimento sul modello di account di archiviazione).
- [Additional storage account template samples](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)(Altri esempi di modelli di account di archiviazione).

---

Per altre informazioni sulle opzioni di replica disponibili, vedere [Opzioni di replica dell'archiviazione](storage-redundancy.md).

## <a name="delete-a-storage-account"></a>Eliminare un account di archiviazione

L'eliminazione di un account di archiviazione consente di eliminare l'intero account, inclusi tutti i dati nell'account, e non può essere annullato.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Passare all'account di archiviazione nel [portale di Azure](https://portal.azure.com).
1. Fare clic su **Elimina**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eliminare l'account di archiviazione, usare il comando [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eliminare l'account di archiviazione, usare il comando [AZ storage account Delete](/cli/azure/storage/account#az-storage-account-delete) :

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Modello](#tab/template)

Per eliminare l'account di archiviazione, usare Azure PowerShell o l'interfaccia della riga di comando di Azure.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

In alternativa, è possibile eliminare il gruppo di risorse, che elimina l'account di archiviazione e tutte le altre risorse nel gruppo di risorse. Per altre informazioni sull'eliminazione di un gruppo di risorse, vedere eliminare il gruppo di risorse [e le risorse](../../azure-resource-manager/management/delete-resource-group.md).

> [!WARNING]
> Non è possibile ripristinare un account di archiviazione eliminato, né recuperare gli elementi che conteneva prima dell'eliminazione. Assicurarsi di eseguire il backup di tutti gli elementi da salvare prima di eliminare l'account. Lo stesso vale per tutte le risorse nell'account: dopo aver eliminato un BLOB, una tabella, una coda o un file, non è più possibile recuperarlo.
>
> Se si prova a eliminare un account di archiviazione associato a una macchina virtuale di Azure, potrebbe essere visualizzato un errore che informa che l'account di archiviazione è ancora in uso. Per informazioni sulla risoluzione di questo errore, vedere [risolvere gli errori quando si eliminano gli account di archiviazione](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sulle procedure è stato creato un account di archiviazione standard per utilizzo generico V2. Per informazioni su come caricare e scaricare i BLOB da e verso l'account di archiviazione, continuare con una delle guide introduttive di archiviazione BLOB.

# <a name="portal"></a>[Portale](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Usare i BLOB con il portale di Azure](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Usare i BLOB con PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Usare i BLOB con l'interfaccia della riga di comando di Azure](../blobs/storage-quickstart-blobs-cli.md)

# <a name="template"></a>[Modello](#tab/template)

> [!div class="nextstepaction"]
> [Usare i BLOB con il portale di Azure](../blobs/storage-quickstart-blobs-portal.md)

---
