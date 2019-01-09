---
title: 'Avvio rapido: Creare un account di archiviazione - Archiviazione di Azure'
description: In questa guida introduttiva viene illustrato come creare rapidamente un account di archiviazione usando il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure. Un account di archiviazione di Azure offre uno spazio dei nomi univoco in Microsoft Azure per archiviare gli oggetti dati creati in Archiviazione di Azure e accedere a tali oggetti.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 09/18/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 5086c0758e7c535b65c877917dc790dafa46f763
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994722"
---
# <a name="create-a-storage-account"></a>Creare un account di archiviazione

In questa guida introduttiva viene illustrato come creare rapidamente un account di archiviazione usando il [portale di Azure](https://portal.azure.com/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

No.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Per questa guida introduttiva è richiesto il modulo Azure PowerShell Az versione 0.7 o successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione corrente. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile accedere ad Azure ed eseguire i comandi dell'interfaccia della riga di comando di Azure in uno dei due modi seguenti:

- È possibile eseguire i comandi dell'interfaccia della riga di comando nel portale di Azure, in Azure Cloud Shell 
- È possibile installare l'interfaccia della riga di comando ed eseguire i relativi comandi in locale  

### <a name="use-azure-cloud-shell"></a>Usare Azure Cloud Shell

Azure Cloud Shell è una shell Bash gratuita che è possibile eseguire direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Fare clic sul pulsante **Cloud Shell** nel menu in alto a destra nel portale di Azure:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Il pulsante avvia una shell interattiva che è possibile usare per eseguire i passaggi di questa guida introduttiva:

[![Screenshot che mostra la finestra di Cloud Shell nel portale](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installare l'interfaccia della riga di comando in locale

È anche possibile installare e usare l'interfaccia della riga di comando di Azure in locale. Questa guida introduttiva richiede l'interfaccia della riga di comando di Azure 2.0.4 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

---

## <a name="log-in-to-azure"></a>Accedere ad Azure

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

Accedere al [Portale di Azure](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate per eseguire l'autenticazione.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per avviare Azure Cloud Shell, accedere al [portale di Azure](https://portal.azure.com).

Per accedere all'installazione locale dell'interfaccia della riga di comando, eseguire il comando di accesso:

```cli
az login
```

---

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

È ora possibile creare l'account di archiviazione.

Ogni account di archiviazione deve appartenere a un gruppo di risorse di Azure. Un gruppo di risorse è un contenitore logico per raggruppare i servizi di Azure. Quando si crea un account di archiviazione, è possibile creare un nuovo gruppo di risorse o usarne uno esistente. Questa guida introduttiva illustra come creare un nuovo gruppo di risorse. 

Un account di archiviazione **per utilizzo generico v2** consente l'accesso a tutti i servizi di Archiviazione di Azure: BLOB, file, code, tabelle e dischi. La guida introduttiva crea un account di archiviazione per utilizzo generico v2, ma i passaggi per creare qualsiasi tipo di account di archiviazione sono simili.   

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Creare prima un nuovo gruppo di risorse con PowerShell usando il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup): 

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location 
```

In caso di dubbi su quale area specificare per il parametro `-Location`, è possibile recuperare un elenco di aree supportate per la sottoscrizione con il comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```powershell
Get-AzLocation | select Location 
$location = "westus"
```

Creare quindi un account di archiviazione per utilizzo generico v2 con archiviazione con ridondanza locale. Usare il comando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount): 

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 
```

Per creare un account di archiviazione per utilizzo generico v2 con l'archiviazione con ridondanza della zona (ZRS) (anteprima), l'archiviazione con ridondanza geografica o l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), sostituire il parametro **SkuName** con il valore desiderato nella tabella seguente. 

|Opzione di replica  |Parametro SkuName  |
|---------|---------|
|Archiviazione con ridondanza locale     |Standard_LRS         |
|Archiviazione con ridondanza della zona (ZRS).     |Standard_ZRS         |
|Archiviazione con ridondanza geografica (GRS)     |Standard_GRS         |
|Archiviazione con ridondanza geografica e accesso in lettura     |Standard_RAGRS         |

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Creare prima un nuovo gruppo di risorse con l'interfaccia della riga di comando di Azure usando il comando [az group create](/cli/azure/group#az_group_create). 

```azurecli-interactive
az group create \
    --name storage-quickstart-resource-group \
    --location westus
```

In caso di dubbi su quale area specificare per il parametro `--location`, è possibile recuperare un elenco di aree supportate per la sottoscrizione con il comando [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Creare quindi un account di archiviazione per utilizzo generico v2 con archiviazione con ridondanza locale. Usare il comando [az storage account create](/cli/azure/storage/account#az_storage_account_create):

```azurecli-interactive
az storage account create \
    --name storagequickstart \
    --resource-group storage-quickstart-resource-group \
    --location westus \
    --sku Standard_LRS \
    --kind StorageV2
```

Per creare un account di archiviazione per utilizzo generico v2 con l'archiviazione con ridondanza della zona (versione in anteprima), l'archiviazione con ridondanza geografica o l'archiviazione con ridondanza geografica e accesso in lettura, sostituire il parametro **sku** con il valore desiderato nella tabella seguente. 

|Opzione di replica  |Parametro sku  |
|---------|---------|
|Archiviazione con ridondanza locale     |Standard_LRS         |
|Archiviazione con ridondanza della zona (ZRS).     |Standard_ZRS         |
|Archiviazione con ridondanza geografica (GRS)     |Standard_GRS         |
|Archiviazione con ridondanza geografica e accesso in lettura     |Standard_RAGRS         |

---

Per altre informazioni sulle opzioni di replica disponibili, vedere [Opzioni di replica dell'archiviazione](storage-redundancy.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create in questa guida introduttiva, è sufficiente eliminare il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminati anche l'account di archiviazione associato e tutte le altre risorse correlate al gruppo di risorse.

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

Per rimuovere un gruppo di risorse usando il portale di Azure:

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e scegliere **Gruppi di risorse** per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse da eliminare e fare clic con il pulsante destro del mouse su **Altro** (**...**) a destra dell'elenco.
3. Scegliere **Elimina gruppo di risorse** e confermare.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Per rimuovere il gruppo di risorse e tutte le risorse correlate, incluso il nuovo account di archiviazione, usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per rimuovere il gruppo di risorse e tutte le risorse correlate, incluso il nuovo account di archiviazione, usare il comando [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name storage-quickstart-resource-group
```

---

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stato creato un account di archiviazione standard per utilizzo generico v2. Per informazioni su come caricare e scaricare BLOB nell'account di archiviazione, passare alla guida introduttiva su Archiviazione BLOB.

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Usare i BLOB con il portale di Azure](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Usare i BLOB con PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Usare i BLOB con l'interfaccia della riga di comando di Azure](../blobs/storage-quickstart-blobs-cli.md)

---
